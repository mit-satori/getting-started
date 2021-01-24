Using MPI and CUDA on Satori
============================

CUDA aware MPI from Slurm batch scripts is supported through MPI modules using OpenMPI. To use CUDA aware MPI codes, and libraries that 
involve MPI, may need to be recompiled with the correct OpenMPI modules. 

Getting started
^^^^^^^^^^^^^^^

The following modules are needed to CUDA aware MPI codes

.. code:: bash

  module purge all
  module add spack
  module add cuda/10.1
  module load openmpi/3.1.4-pmi-cuda-ucx


Compiling
^^^^^^^^^

Codes and libraries that make MPI calls against CUDA device memory pointers need
to compiled using the MPI compilation wrappers e.g. ``mpicc``, ``mpiCC``, ``mpicxx``, ``mpic++``,
``mpif77``, ``mpif90``, ``mpifort``. The CUDA runtime library needs to be added as a link
library, e.g. ``-lcudart``.

A typical compilation setup is

.. code:: bash

   module purge all
   module add spack
   module add cuda/10.1
   module load openmpi/3.1.4-pmi-cuda-ucx
   mpiCC MYFILE.cc -lcudart


Submiting a batch script
^^^^^^^^^^^^^^^^^^^^^^^^

Batch script header
...................

The following example SLURM batch script heading illustrates requesting 8 GPUs on 2 nodes with exclusive access. In this
example we are want to have one MPI rank for each GPU, so we set ``cpus-per-task``, ``ntasks-per-core`` and ``threads-per-core``
to ``1``.  The start of the bacth scripts selects the modules needed for OpenMPI CUDA aware MPI with SLURM integration. 

.. code:: bash

  #!/bin/bash
  #SBATCH --nodes=2
  #SBATCH --ntasks-per-node=4
  #SBATCH --gres=gpu:4
  #SBATCH --cpus-per-task=1
  #SBATCH --ntasks-per-core=1
  #SBATCH --threads-per-core=1
  #SBATCH --mem=1T
  #SBATCH --exclusive
  #SBATCH --time 00:05:00
  
  module purge all
  module add spack
  module add cuda/10.1
  module load openmpi/3.1.4-pmi-cuda-ucx

Assigning GPUs to MPI ranks
...........................

The batch script will allocated 4 GPUs on each node to the batch session. Individual MPI ranks then need to
be attached to specific GPUs to run in parallel. There are 2 ways to do this.

 #. Attach GPU to a rank using a bash script.
 
    In this approach a bash script is written that is used as a launcher for the MPI program to be run. This
    bash script modifies the environment variable ``CUDA_VISIBLE_DEVICES`` so that the MPI program will only see
    the GPU it has been allocated. An example script is shown below.
 
.. code:: bash

    #!/bin/bash
    #
    # Choose a CUDA device based on ${SLURM_LOCALID}
    #
    ngpu=`nvidia-smi -L | grep UUID | wc -l`
    mygpu=$((${SLURM_LOCALID} % ${ngpu} ))
    export CUDA_VISIBLE_DEVICES=${mygpu}
    exec $*
  

 #. Attach a GPU to a rack using CUDA library runtime code.


