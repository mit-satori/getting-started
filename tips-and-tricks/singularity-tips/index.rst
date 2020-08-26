How do I build a Singularity image from scratch?
================================================

To build an image for Singularity from scratch you generally need to have privileged access to a Power 9 CPU system. This
is a little awkward since nobody has Power 9 CPU laptops or desktops. Fortunately Docker have recently created a way to do this using
a Power 9 emulator. The steps to get this set up on a laptop are shown below.

#. Launch or log in to an Ubuntu 18.04 Linux machine

   if you don't have an Ubuntu machine you can create a virtual machine as follows
     
   #. install Vagrant ( https://www.vagrantup.com/downloads ) and Virtual Box ( https://www.virtualbox.org/wiki/Downloads ) and then
      launch a Vagrant virtual machine running Ubuntu. Vagrant and Virtual Box should work on most Mac, Windows or Linux hosts::
   
         vagrant up generic/ubuntu1804
         vagrant ssh

#.

#.
