.. figure:: images/lsf.png
   :alt: Satori

   Satori

Running your AI training jobs on Satori
---------------------------------------

Computational work on Satori is performed within jobs managed by a
workload manager (IBM LSF). A typical job consists of several
components:

-  A submission script
-  An executable file (python sript or C/C++ script)
-  Training data needed by the ML/DL script
-  Output files created by the training/inference job

There are two types for jobs:

-  interactive / online
-  batch

In general, the process for running a batch job is to:

-  Prepare executables and input files
-  Modify provided LSF job template for the batch script or write a new
   one
-  Submit the batch script to the Workload Manager
-  Monitor the job’s progress before and during execution

Interactive Jobs
^^^^^^^^^^^^^^^^

Most users will find batch jobs to be the easiest way to interact with
the system, since they permit you to hand off a job to the scheduler and
then work on other tasks; however, it is sometimes preferable to run
interactively on the system. This is especially true when developing,
modifying, or debugging code.

Since all compute resources are managed/scheduled by LSF, it is not
possible to simply log into the system and begin running a parallel code
interactively. You must request the appropriate resources from the
system and, if necessary, wait until they are available. This is done
with an “interactive batch” job. Interactive batch jobs are submitted
via the command line, which supports the same options that are passed
via #BSUB parameters in a batch script. The final options on the command
line are what makes the job “interactive batch”: -Is followed by a shell
name. For example, to request an interactive batch job (with bash as the
shell) equivalent to the sample batch script above, you would use the
command:

.. code:: bash

   bsub -W 3:00 -q interactive -gpu "num=4" -R "select[type==any]" -Ip bash

This will request an AC922 node with 4x GPUs from the Satori (normal
queue) for 3 hours.

Batch Scripts
^^^^^^^^^^^^^

The most common way to interact with the batch system is via batch jobs.
A batch job is simply a shell script with added directives to request
various resources from or provide certain information to the batch
scheduling system. Aside from the lines containing LSF options, the
batch script is simply the series commands needed to set up and run your
AI job.

To submit a batch script, use the bsub command:

.. code:: bash

   bsub < myjob.lsf

As an example, consider the following batch script for 4x V100 GPUs
(single AC922 node):

.. code:: bash

   #BSUB -L /bin/bash
   #BSUB -J "keras-job-name"
   #BSUB -o "keras-job-name_o.%J"
   #BSUB -e "keras-job-name_e.%J"
   #BSUB -n 4
   #BSUB -R "span[ptile=4]"
   #BSUB -gpu "num=4"
   #BSUB -q "normal"
   #BSUB -x

   HOME2=/nobackup/users/<your_user_name>
   PYTHON_VIRTUAL_ENVIRONMENT=wmlce-1.6.2
   CONDA_ROOT=$HOME2/anaconda3
   source ${CONDA_ROOT}/etc/profile.d/conda.sh
   conda activate $PYTHON_VIRTUAL_ENVIRONMENT

   cd $HOME2/projects
   python Keras-ResNet50-training.py --batch=64

In the above template you can change:

-  line 2-4: with your desire job name, but remember to keep \_o for the
   job output file and \_e for the file with the related job errors
-  line 5: “-n 4” here you can consider the no of GPUs you need,
   multiple of 4 (ie. - n 4, -n 8, -n 16, ….)
-  line 11: add your MIT assigned username folder name from the
   /nobackup/users/
-  line 12: change to your conda virtual environment defined at
   installation of WMLCE
-  line 17-18: change as need for what you will want to run and from
   where

For your convienenice additional LSF batch job templates have been
created to cover distributed deep learning trainings across Satori
cluster:

-  `Pytorch with IBM Distributed Deep Learning Library
   (DDL) <https://github.com/mit-satori/getting-started/blob/master/lsf-templates/template-pytorch-multinode.lsf>`__
-  `TensorFlow with IBM Distributed Deep Learning Library
   (DDL) <https://github.com/mit-satori/getting-started/blob/master/lsf-templates/template-tf-multinode.lsf>`__
-  `Pytorch with Horovod + IBM Distributed Deep Learning Library (DDL)
   backend <https://github.com/mit-satori/getting-started/blob/master/lsf-templates/template-pytorch-horovod-multinode.lsf>`__
-  `TensorFlow with Horovod + IBM Distributed Deep Learning Library
   (DDL)
   backend <https://github.com/mit-satori/getting-started/blob/master/lsf-templates/template-tf-horovod-multinode.lsf>`__

Job States
~~~~~~~~~~

A job will progress through a number of states through its lifetime. The
states you’re most likely to see are:

-  PEND: Job is pending
-  RUN: Job is running
-  DONE: Job completed normally (with an exit code of 0)
-  EXIT: Job completed abnormally
-  PSUSP: Job was suspended (either by the user or an administrator)
   while pending
-  USUSP: Job was suspended (either by the user or an administrator)
   after starting
-  SSUSP: Job was suspended by the system after starting

Monitoring Jobs
~~~~~~~~~~~~~~~

LSF provides several utilities with which you can monitor jobs. These
include monitoring the queue, getting details about a particular job,
viewing STDOUT/STDERR of running jobs, and more.

The most straightforward monitoring is with the bjobs command. This
command will show the current queue, including both pending and running
jobs. Running bjobs -l will provide much more detail about a job (or
group of jobs). For detailed output of a single job, specify the job id
after the -l. For example, for detailed output of job 12345, you can run
bjobs -l 12345 . Other options to bjobs are shown below. In general, if
the command is specified with -u all it will show information for all
users/all jobs. Without that option, it only shows your jobs. Note that
this is not an exhaustive list. See man bjobs for more information.

-  bjobs Show your current jobs in the queue
-  bjobs -u all Show currently queued jobs for all users
-  bjobs -P ABC123 Shows currently-queued jobs for project ABC123
-  bjobs -UF Don’t format output (might be useful if you’re using the
   output in a script)
-  bjobs -a Show jobs in all states, including recently finished jobs
-  bjobs -l Show long/detailed output
-  bjobs -l 12345 Show long/detailed output for jobs 12345
-  bjobs -d Show details for recently completed jobs
-  bjobs -s Show suspended jobs, including the reason(s) they’re
   suspended
-  bjobs -r Show running jobs
-  bjobs -p Show pending jobs
-  bjobs -w Use “wide” formatting for output

If you want to check the STDOUT/STDERR of a currently running job, you
can do so with the bpeek command. The command supports several options:

-  bpeek -J jobname Show STDOUT/STDERR for the job you’ve most recently
   submitted with the name jobname
-  bpeek 12345 Show STDOUT/STDERR for job 12345
-  bpeek -f … Used with other options. Makes bpeek use tail -f and exit
   once the job completes.

Scheduling Policy
~~~~~~~~~~~~~~~~~

In a simple batch queue system, jobs run in a first-in, first-out (FIFO)
order. This often does not make effective use of the system. A large job
may be next in line to run. If the system is using a strict FIFO queue,
many processors sit idle while the large job waits to run. Backfilling
would allow smaller, shorter jobs to use those otherwise idle resources,
and with the proper algorithm, the start time of the large job would not
be delayed. While this does make more effective use of the system, it
indirectly encourages the submission of smaller jobs.

Batch Queue Policy
~~~~~~~~~~~~~~~~~~

The batch queue is the default queue for production work on Satori. It
enforces the following policies: Limit of (4) eligible-to-run jobs per
user. Jobs in excess of the per user limit above will be placed into a
held state, but will change to eligible-to-run at the appropriate time.
Users may have only (100) jobs queued at any state at any time.
Additional jobs will be rejected at submit time.
