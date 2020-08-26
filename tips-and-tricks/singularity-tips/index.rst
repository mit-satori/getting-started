How do I build a Singularity image from scratch?
================================================

To build an image for Singularity from scratch you generally need to have privileged access to a Power 9 CPU system. This
is a little awkward since nobody has Power 9 CPU laptops or desktops. Docker ( https://docs.docker.com/get-docker/ ) has recently added 
features that provide a way to do this, using
the ppc64le Power 9 emulation that is part of the QEMU (https://www.qemu.org) project. Steps to get this set up on a laptop, desktop or virtual 
machine are described below.

#. Launch or log in to an Ubuntu 18.04 Linux machine.

   If you don't have an Ubuntu machine you can create a virtual machine as follows
     
   #. install Vagrant ( https://www.vagrantup.com/downloads ) 
   #. install Virtual Box ( https://www.virtualbox.org/wiki/Downloads )
   #. launch a Vagrant virtual machine running Ubuntu::
   
         vagrant up generic/ubuntu1804
         vagrant ssh
         
   Vagrant and Virtual Box should work on most Mac, Windows or Linux hosts.

#.

#.
