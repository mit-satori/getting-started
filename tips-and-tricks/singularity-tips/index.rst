How do I build a Singularity image from scratch?
================================================

To build an image for Singularity from scratch you generally need to have privileged access to a Power 9 CPU system. This
is a little awkward since nobody has Power 9 CPU laptops or desktops. Docker ( https://docs.docker.com/get-docker/ ) has recently added 
features that provide a way to do this, using
the ppc64le Power 9 emulation that is part of the QEMU (https://www.qemu.org) project. Using this feature is described below.

Set up to run Docker in ppc64le mode on an x86 machine
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The one time steps to get this set up on a laptop, desktop or virtual 
machine are described below. 

#. Launch or log in to an Ubuntu 18.04 Linux machine.

   If you don't have an Ubuntu machine you can create a virtual machine as follows
     
   #. install Vagrant ( https://www.vagrantup.com/downloads ) 
   #. install Virtual Box ( https://www.virtualbox.org/wiki/Downloads )
   #. launch a Vagrant virtual machine running Ubuntu::
   
         vagrant up generic/ubuntu1804
         vagrant ssh
         
   Vagrant and Virtual Box should work on most Mac, Windows or Linux hosts.

#. Install Docker on whatever Ubuntu system you selected, if it is not already installed. This requires privileged access. Steps for installing Docker are:: 

      apt-get remove docker docker-engine docker.io containerd runc
      apt-get update
      apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
      apt-key fingerprint 0EBFCD88
      add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
      apt-get update
      apt-get install docker-ce docker-ce-cli containerd.io

#. Check docker works::

      docker run hello-world
      
#. Set docker to allow experimental features
   
   #. Set Docker client to allow experimental features::
   
          export DOCKER_CLI_EXPERIMENTAL=enabled
      
   #. Set Docker server to allow experimental features by editing line::
      
          ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
      
      in file ``/lib/systemd/system/docker.service`` to read::
   
          ExecStart=/usr/bin/dockerd --experimental -H fd:// --containerd=/run/containerd/containerd.sock
      
      and restarting system daemons::
      
          systemctl daemon-reload
          systemctl restart docker
          
#. Install needed QEMU emulator binfmt information for ppc64le architecture ( see https://github.com/dbhi/qus for explanations ). Note - the default QEMU install does not seem to work reliably, but the install via https://github.com/dbhi/qus does seem robust::
    
     docker run --rm --privileged aptman/qus -s -- -p 

#. Check that we can work with ppc64le executables in an image e.g::

     docker run --rm -i -t --platform ppc64le ppc64le/ubuntu /bin/bash
     apt-get update
     apt-get install -y file
     file /bin/ls
     
   should produce output indicating a PowerPC executable::
     
     
     /bin/ls: ELF 64-bit LSB shared object, 64-bit PowerPC or cisco 7500, version 1 (SYSV), dynamically linked, interpreter /lib64/ld64.so.2, BuildID[sha1]=da9b7dc089cbaee41f4d1af3da65a40d29b25773, for GNU/Linux 3.10.0, stripped


  Note - the steps above involve installing Docker on an Ubuntu virtual or physical machine. In principle a native install of Docker could work. In practice, this does not seem to work reliably on OSX. 
  
  
Run Docker in ppc64le mode on an x86 machine to generate an image for Satori
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Once you have a machine with Docker fully configured for ppc64le it can be used to generate and push an image to Docker hub. Steps for this are
shown below.

#. Generate an image from a Dockerfile, using for example::

    curl https://raw.githubusercontent.com/christophernhill/ibmcloudhacks/master/ppc64le-docker-testing/jp-Dockerfile > Dockerfile
    docker build --platform ppc64le --tag jp-p9-sing-test:1.0 .
    
#. Tag image for pushing to your docker account, for example::

     docker tag jp-p9-sing-test:1.0 christophernhill/jp-p9-sing-test:1.0
     
#. Push image to you docker account::

     docker push christophernhill/jp-p9-sing-test:1.0
    
Import new Docker hub image into Singularity on Satori
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Once your Docker image is ready it can now be imported into Singulariy on Satori. To do this
in a dedicated interactive session (useful for large images), first start lauch an interactive 
session::

    srun -N 1 -n 40 --mem 1T -t 12:00:00 --pty /bin/bash
    
Once the interactive session starts use the following commands::
    
    
    module load singularity
    cd /nobackup/users/cnh/
    singularity build foo.sif docker://christophernhill/jp-p9-sing-test:1.0


and used (finally!)::

    singularity shell foo.sif
    
Using Singularity instead of Docker
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 
Singularity is a little different from Docker in that it restricts access more and shares more environment with the
host system. In particular there is no root access in a running Singularity image and privileged access directories
are not writable. This means any operations that requires root privileges must be taken as part of the image build.
Singularity also mounts the home directory for an account on the host system into the container by default. This
can be useful, but also is not an isolated environment so care must be taken when running ensembles of containers. 

A typical Singularity command for executing an image with some isolation on Satori is::
   
       singularity exec --nv -c -e -B /nobackup/users/cnh:/work/nb foo2.sif  /bin/bash

which launches a container running a shell within an instnace of the image ``foo2.sif``.
 
- The ``--nv`` option ensures the container can access the host GPU cards. 
- The ``-B /nobackup/users/cnh:/work/nb`` option maps the Satori scratch file space of the account to a container directory.
- The ``-c`` and ``-e`` options create a container that does not mount the host home directory for the account and does not copy
  host account environment variables. 
  
Together this set of options starts a container that can access the Satori GPUs and shared file system, but that is otherwise isolated
from the standard Satori software and can have a fully custom set of tools. 
