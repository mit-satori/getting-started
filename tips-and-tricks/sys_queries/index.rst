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

Questions about my jobs
***********************

How can I establish which logical CPU IDs my process is bound to?
=================================================================

Establish your process’s ID (PID) using, e.g., the ps command,

.. code:: bash

  ps -ef | grep ${USER}

and then use the taskset command

.. code:: bash

  taskset -acp PID

against the retrieved PID.

Can I see the output of my job before it completes?
===================================================

Yes. The command

.. code:: bash

  bpeek
  
will cat the current output of your job to screen. If your job generates a lot of output, you may prefer to pipe it through other commands: e.g.,

.. code:: bash

  bpeek | tail
  bpeek | more
  bpeek | less
  bpeek | grep ...

to control the output or filter it down to what you find interesting.

I have a job waiting in the queue, and I want to modify the options I had selected
===================================================================================

You can use the bmod command to do this in many cases. The only alternative is to bkill your job and resubmit it with the options you prefer edited in.

I have submitted my job several times, but I get no output
==========================================================

There can be several reasons for this: for instance

You may have failed to submit the job properly: e.g., you forgot to use the appropriate redirection: i.e., you issued
bsub myBatchScript
instead of the correct form
bsub < myBatchScript

In your batch script or command line, you may have failed to use one of options (-e, -eo, -o, -oo) that specify the filename that is to capture the stderr and/or stdout streams from your job. In such cases, LSF attempts to mail the output to you; if mail is not set up on the system, your output will never arrive by this route.

The output is not in the directory or filesystem you expect it to be in: this can happen for several reasons; three are:
You did not specify a pathname for the output filename of your job, but you used the -cwd option of bsub to change the current working directory of your job; your output will then be in the directory that you specified with the -cwd option.
You used a filesystem that has the same pathname on several nodes—e.g., /tmp or an NVMe filesystem—that is not a shared filesystem.
You have exhausted your filespace quota.

How do I set a time limit on my job?
====================================

Use the -W [h:]m group of the bsub option: e.g., to set a (wall-clock) limit of 90 minutes, use either one of

.. code:: bash

  #BSUB -W 1:30
  #BSUB -W 90
  
Can I make a job’s startup depend on the completion of a previous one?
======================================================================

Yes. LSF provides significant functionality in this context. As a trivial example, consider the following, where the #BSUB -w "done (first_job)" in second_job ensures that it will only start after first_job is done:

.. code:: bash

  $ cat first_job.lsf
  #BSUB -J first_job
  #BSUB -e "first_job.stderr.\%J"
  #BSUB -o "first_job.stdout.\%J"
  #BSUB -q "normal"
  #BSUB -R "affinity[core]"
  #BSUB -M "40"
  #BSUB -R "rusage[mem=40]"
  #BSUB -x

  echo -n "Job first_job started: "; date
  sleep 180
  echo -n "Job first_job ended:   "; date
  $ cat second_job.lsf
  #BSUB -J second_job
  #BSUB -e "second_job.stderr.\%J"
  #BSUB -o "second_job.stdout.\%J"
  #BSUB -q "normal"
  #BSUB -R "affinity[core]"
  #BSUB -M "40"
  #BSUB -R "rusage[mem=40]"
  #BSUB -w "done (first_job)"
  #BSUB -x

  echo -n "Job second_job started: "; date
  $ bsub < first_job.lsf; bsub < second_job.lsf
  Job <34445> is submitted to queue <normal>.
  Job <34446> is submitted to queue <normal>.
 
The second job will start almost immediately after the completion on the first job. You can use the bjobs command to check the status of the jobs.

How do I select a specific set of hosts for my job?
===================================================

By default, your jobs can be scheduled to any of the hosts that are available to your queue, which may be listed with

.. code:: bash

  bqueues -l queue_name | grep HOSTS

To ensure that jobs are scheduled to some subset of these, add the bsub group -m "hostname1 hostname2 hostname3 ... hostnamen"; for instance, to choose from the three hosts node0002, node0042, node0051 in a batch script, add the #BSUB line

.. code:: bash

  #BSUB -m "node0002, node0042, node0051"

Note that the items in the double-quoted list are delimited by white space.

To select a specific node to be the first one in the allocation, follow its hostname by an exclamation mark: e.g.,

.. code:: bash

  #BSUB -m "node0002, node0042, node0051!"

will ensure that node0002 will be the first node in the allocation list.

How do I deselect specific nodes for my job?
============================================

To ensure that jobs are not scheduled to specific nodes, say node0034 and node0045, one can add a #BSUB statement such as the following:

.. code:: bash

  #BSUB -R "select[hname!=node0034&&hname!=node0045]"

My job’s runtime environment is different from what I expected
==============================================================

By default, LSF preserves the user’s submit-time environment. If this is not desirable, add the group -L shell_pathname to your bsub command or, e.g.,

.. code:: bash

  #BSUB -L /bin/bash

to your batch script so that your batch job starts in a “pristine” bash shell.

I want to know precisely what my job’s runtime environment is
=============================================================

For serial, POSIX threads, or OpenMP jobs, simply add the line


.. code:: bash

  env

to your job script after the #BSUB lines.

For jobs that employ Spectrum MPI or OpenMPI, do the above, and, in addition, modify the generic invocation

.. code:: bash

  mpirun ./my_exe my_opts < my_inp > my_out

to

.. code:: bash

mpirun helper.sh > my_out

where the content of helper.sh (which must be executable: i.e., chmod +x helper.sh) is

.. code:: bash

  #!/bin/sh
  env > env.${OMPI_COMM_WORLD_RANK}
  ./my_exe my_opts < my_inp > my_out

This will generate one file env.${OMPI_COMM_WORLD_RANK} for every extant value of ${OMPI_COMM_WORLD_RANK} in the job.


