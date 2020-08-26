How do I build a Singularity image from scratch?
================================================

To build an image for Singularity from scratch you generally need to have privileged access to a Power 9 CPU system. This
is a little awkward since nobody has Power 9 CPU laptops or desktops. Docker ( https://docs.docker.com/get-docker/ ) has recently added 
features that provide a way to do this, using
the ppc64le Power 9 emulation that is part of the QEMU (https://www.qemu.org) project. Using this feature is described below.

Set up to run Docker in ppc64le mode
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

One time steps to get this set up on a laptop, desktop or virtual 
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
          
#. Install QEMU emulator binfmt information for ppc64le architecture::
    
     docker run --rm --privileged aptman/qus -s -- -p 

#.

Note - the steps above involve installing Docker on an Ubuntu virtual of physical machine. In principle a native install of Docker could work. This does not seem to work reliably in practice on OSX. 
