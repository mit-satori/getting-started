System software queries
***********************

What Linux distribution version am I running?
=============================================

The following commands can be used: 

.. code:: bash

   cat /etc/redhat-release
   
or

.. code:: bash

  lsb_release -a

What Linux kernel level am I running?
=====================================

The following command works: 

.. code:: bash

   uname -r
   
What software levels are installed on the system?
=================================================

The Module utility is used to install packages on the system. The following will give you a list of available software:

.. code:: bash

   module spider
   
If you want to see information about your conda environment, you can issue:
 
.. code:: bash

  conda info
   
and to see the packages installed, issue:
 
.. code:: bash

  conda list
  
  
System hardware queries
***********************
   
What is my CPU configuration?
=============================

The lscpu command will give you information such as
architecture, model name, sockets, cores per socket, threads per core, min/max CPU clockspeed, caches sizes:

.. code:: bash

   lscpu
      
WHow much RAM is there on my nodes?
===================================

The amount of RAM that is expected to be available on a system is summarized elsewhere. The command to interrogate a node for the total available memory is

.. code:: bash

  grep MemTotal /proc/meminfo

Note that for Satori system, this will include the available GPU RAM as well.

What SMT mode are my nodes in?
==============================

The command to interrogate a node for the SMT mode is

.. code:: bash

  ppc64_cpu --smt

What CPU governor is in effect on my nodes?
===========================================

The command to establish the governor in effect for a node is

.. code:: bash

  frequency-info | grep -A 2 "current policy"


What are the logical IDs and UUIDs for the GPUs on my nodes?
============================================================

There should be four operational GPUs on every node. The command to obtain a brief pertinent summary for a node is

.. code:: bash

  nvidia-smi -L

A less terse summary may be obtained from

.. code:: bash

  nvidia-smi

Detailed information may be obtained from

.. code:: bash

  nvidia-smi -q

What is the IBM model of my system?
===================================

It can also be obtained from the command

.. code:: bash

  grep "model" /proc/cpuinfo

Which logical CPUs belong to which socket?
==========================================

Each integrated circuit die on the system is a NUMA node in the output of the command

.. code:: bash

  numactl --hardware

or

.. code:: bash

  numactl -H

which will list the available logical CPUs and the NUMA nodes to which they belong. 


