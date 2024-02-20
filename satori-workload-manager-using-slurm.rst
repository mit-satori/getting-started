

   Satori

Running your AI training jobs on Satori using Slurm
---------------------------------------------------

Computational work on Satori is quickly being migrated to use the Slurm  workload manager. A typical job consists of several
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
-  Modify provided SLURM job template for the batch script or write a new
   one
-  Submit the batch script to the Workload Manager
-  Monitor the job’s progress before and during execution

A Note on Exclusivity
^^^^^^^^^^^^^^^^^^^^^

To make best use of Satori's GPU resource  default job submissions are not
exclusive. That means that unless you ask otherwise, the GPUs on the node(s)
you are assigned may already be in use by another user. That means if you
request a node  with 2GPU's  the 2 other GPUs on that node may be engaged by
another job. This allows us to more efficently allocate all of the GPU
resources. This may require some additional checking to make sure you can
uniquely use  all of the GPU's on a machine. If you're in doubt, you can request
the node to be 'exclusive' . See below on how to request exclusive access  in
an interactive and batch situation. 

Interactive Jobs
^^^^^^^^^^^^^^^^

Most users will find batch jobs to be the easiest way to interact with
the system, since they permit you to hand off a job to the scheduler and
then work on other tasks; however, it is sometimes preferable to run
interactively on the system. This is especially true when developing,
modifying, or debugging code.

Since all compute resources are managed/scheduled by SLURM, it is not
possible to simply log into the system and begin running a parallel code
interactively. You must request the appropriate resources from the
system and, if necessary, wait until they are available. This is done
with an “interactive batch” job. Interactive batch jobs are submitted
via the command line, which supports the same options that are passed
via **#SBATCH** parameters in a batch script. The final options on the command
line are what makes the job “interactive batch”: -Is followed by a shell
name. For example, to request an interactive batch job (with bash as the
shell) equivalent to the sample batch script above, you would use the
command:

.. code:: bash

   srun --gres=gpu:4 -N 1 --mem=1T --time 1:00:00 -I --pty /bin/bash


This will request an AC922 node with 4x GPUs from the Satori (normal
queue) for 1 hour.

If you need to make sure no one else can allocate the unused GPU's on the machine you can use

.. code:: bash
  
   srun --gres=gpu:4 -N 1 --exclusive --mem=1T --time 1:00:00 -I --pty /bin/bash
   
this will request exclusive use of an interactive node with 4GPU's 

**Note:** if your Interactive job will not launch it may mean that there are no interactive notes available… Try removing the *-I* flag which and your job will run when a node becomes available rather than failing immediately.    

.. code:: bash

   srun --gres=gpu:4 -N 1 --mem=1T --time 1:00:00  --pty /bin/bash

You may also want to lower the memory requested in the *--mem=** flag as that may alow you to get interactive time on a shared node.   Eg

.. code:: bash

   srun --gres=gpu:4 -N 1 --mem=250G  --time 1:00:00  --pty /bin/bash


Batch Scripts
^^^^^^^^^^^^^

The most common way to interact with the batch system is via batch jobs.
A batch job is simply a shell script with added directives to request
various resources from or provide certain information to the batch
scheduling system. Aside from the lines containing SLURM options, the
batch script is simply the series commands needed to set up and run your
AI job.
 
To submit a batch script, use the bsub command:

.. code:: bash
  
   sbatch myjob.slurm

As an example, consider the following batch script for 4x V100 GPUs
(single AC922 node):

.. code:: bash

   #!/bin/bash
   #SBATCH -J myjob_4GPUs
   #SBATCH -o myjob_4GPUs_%j.out
   #SBATCH -e myjob_4GPUs_%j.err
   #SBATCH --mail-user=florin@mit.edu
   #SBATCH --mail-type=ALL
   #SBATCH --gres=gpu:4
   #SBATCH --gpus-per-node=4
   #SBATCH --nodes=1
   #SBATCH --ntasks-per-node=4
   #SBATCH --mem=0
   #SBATCH --time=24:00:00
   #SBATCH --exclusive

   ## User python environment
   HOME2=/nobackup/users/$(whoami)
   PYTHON_VIRTUAL_ENVIRONMENT=wmlce-1.7.0
   CONDA_ROOT=$HOME2/anaconda3

   ## Activate WMLCE virtual environment 
   source ${CONDA_ROOT}/etc/profile.d/conda.sh
   conda activate $PYTHON_VIRTUAL_ENVIRONMENT
   ulimit -s unlimited

   ## Creating SLURM nodes list
   export NODELIST=nodelist.$
   srun -l bash -c 'hostname' |  sort -k 2 -u | awk -vORS=, '{print $2":4"}' | sed 's/,$//' > $NODELIST

   ## Number of total processes 
   echo " "
   echo " Nodelist:= " $SLURM_JOB_NODELIST
   echo " Number of nodes:= " $SLURM_JOB_NUM_NODES
   echo " GPUs per node:= " $SLURM_JOB_GPUS
   echo " Ntasks per node:= "  $SLURM_NTASKS_PER_NODE


   ####    Use MPI for communication with Horovod - this can be hard-coded during installation as well.
   export HOROVOD_GPU_ALLREDUCE=MPI
   export HOROVOD_GPU_ALLGATHER=MPI
   export HOROVOD_GPU_BROADCAST=MPI
   export NCCL_DEBUG=DEBUG

   echo " Running on multiple nodes/GPU devices"
   echo ""
   echo " Run started at:- "
   date

   ## Horovod execution
   horovodrun -np $SLURM_NTASKS -H `cat $NODELIST` python /data/ImageNet/pytorch_mnist.py 

   echo "Run completed at:- "
   date

In the above template you can change:

-  line 2-4: with your desired job name, but remember to keep \_out for the
   job output file and \_err for the file with the related job errors
-  line 7: ``--gres=gpu:4`` here you can consider the no of GPUs you need **per node** *e.g.* a value of 1 means you want only 
   1 GPU on each node, a value of 4 means you want all GPUS's on the node. 
-  line 9: ``--nodes=1`` here you put how many nodes you need. *e.g.* a value of 1 means 1 node, a value of 2 means 2 nodes,                
   etc. **Note:** the total number of GPUS is the product of the ``--gres`` and the ``--nodes`` settings. *e.g.* a value of 
   ``--gres=gpu:4`` and ``--nodes=2`` = 4 x 2 = 8 GPU's in total.
-  line 12: ``--time=24:00:00`` indicates the maximum run time you wish to allow. **Note** If this number is larger than the    
   runtime limit of the queue you are in, your job will be terminated at the queue limit. **It is good practice to make use 
   of checkpointing in order not to lose your work if your job is terminated.** 
-  line 13: ``--exclusive`` means that you want full use of the GPUS on the nodes you are reserving. Leaving this out allows 
   the GPU resources you're not using on the node to be shared. 
-  line 17: change to your conda virtual environment defined at installation of WMLCE (or other conda environment) 
-  line 49: change as need for what you will want to run and from where. **Note** while horovod isn't strictly needed for 
   single node runs, we recommend it in case you need to expand to more nodes. 
-  The environment variables below can be used to change Horovod communication from MPI to NCCL2; In case of the MPI, allgather allocates an output tensor which is proportionate to the number of processes participating in the training. If you find yourself running out of GPU memory and you can force allgather to happen on CPU by passing device_sparse='/cpu:0' to hvd.DistributedOptimizer.


.. code:: bash 

   export HOROVOD_GPU_ALLREDUCE=MPI
   export HOROVOD_GPU_ALLGATHER=MPI
   export HOROVOD_GPU_BROADCAST=MPI
   
   
.. code:: bash

   export HOROVOD_GPU_ALLREDUCE=NCCL
   export HOROVOD_GPU_BROADCAST=NCLL

**Note** you may need to install Horovod by activating your conda environment and installing it. *e.g.*

.. code:: bash
   
   conda active wlmce-1.6.3.  <or whatever your conda environment is called>
   conda install horovod

For your convenience additional SLURM batch job templates have been created to cover distributed deep learning trainings across Satori cluster. You can find them `here <https://github.com/mit-satori/getting-started/tree/master/slurm-templates>`__




Monitoring Jobs
~~~~~~~~~~~~~~~

SLURM provides several utilities with which you can monitor jobs. These
include monitoring the queue, getting details about a particular job,
viewing STDOUT/STDERR of running jobs, and more.

The most straightforward monitoring is the command: 

.. code:: bash
  
   squeue 

This command will show the current queue, including both pending and running
jobs. 

Canceling Jobs
~~~~~~~~~~~~~~

SLURM allows you to kill jobs you've already submitted by using the command: 

.. code:: bash
  
   scancel <jobnumber> 

where <jobnumber> is the slurm job number see wiht you submit the job or found by running squeue. 


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

New users are granted access to a default batch queue. It
enforces the following policies: Limit of (1) executing job per
user with a maximum wall time of 12 hours. Jobs in excess of the per 
user limit above will be placed into a
queued state, and will change to eligible-to-run at the appropriate time.
 

Queue Policies
~~~~~~~~~~~~~~
Account holders who are comfortable with basic practices of how to use the
system productively (understanding basic Linux commands, learning interactive
and batch scheduling techniques, developing basic strategies for managing large
numbers of files etc... ) are able to access higher level queues on the system.
These can be useful for urgent time constraints such as paper deadlines and for
more involved workflows. To request acccess to the priority queue first make
sure you are comfortable with the technical and social norms of using a shared
system. Then please email orcd-help-satori@mit.edu and indicate that you
would like to access higher level queue features. 

A set higher level queues has initially been set up in two configurations, 1
Node with 4 GPUs and 2 Nodes with 8 GPU's. Job run  length will be capped at 24
hours so please use checkpointing. There will be a limit of 2 parallel jobs per
user running during peak times. If these queue settings do not meet your project
goals, please email orcd-help-satori@mit.edu with your needed requirments
and we will consider them. 

Running jobs in series
~~~~~~~~~~~~~~~~~~~~~~

Slurm provides numerous mechanisms for chaining jobs together to run unattended in sequence. A simple example of this sort
of job is shown below

.. code:: bash

   #!/bin/bash
   MYSCRIPT=/home/${USER}/foo.slurm
   MYSUBDIR=/home/${USER}
   JID=${SLURM_JOB_ID}
   ssh service0001 "cd $MYSUBDIR; pwd; sbatch --dependency=afterok:${JID} ${MYSCRIPT}"
   sleep 60

submitting this job, for example, as

.. code:: bash

   sbatch --gres=gpu:4 -N 1 --exclusive --mem=1T --time 1:00:00 foo.slurm


will create a series of jobs that runs one after another. Together with checkpointing this sort of
approach can be used to run extended workloads in an largely automated manner. The Slurm `documentation <https://slurm.schedmd.com/documentation.html>`__ describes many
features for managing sequences of jobs. Some more involved examples can be found at the `NIH Biowulf <https://hpc.nih.gov/docs/job_dependencies.html>`__ site. Fully automating workflows can be a little
fiddly and time consuming to get going, but once it is in place you no longer have to get up in the 
middle of the night to check on every computational experiment. 


Note on Pytorch 1.4
~~~~~~~~~~~~~~~~~~~
Note.. we have recently updated the CUDA drivers on the part of Satori running Slurm. You can install Pytorch 1.4 for use with Slurm using these commands

.. code:: bash

   conda config --prepend channels https://public.dhe.ibm.com/ibmdl/export/pub/software/server/ibm-ai/conda-early-access/
   conda create -n wmlce-ea python=3.7
   conda activate wmlce-ea
   conda install pytorch=1.4.0=23447.g18a1a27

