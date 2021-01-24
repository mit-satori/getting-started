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

1. Attach GPU to a rank using a bash script.
 
   In this approach a bash script is written that is used as a launcher for the MPI program to be run. This
   bash script modifies the environment variable ``CUDA_VISIBLE_DEVICES`` so that the MPI program will only see
   the GPU it has been allocated. An example script is shown below:
 
.. code:: bash

    #!/bin/bash
    #
    # Choose a CUDA device based on ${SLURM_LOCALID}
    #
    ngpu=`nvidia-smi -L | grep UUID | wc -l`
    mygpu=$((${SLURM_LOCALID} % ${ngpu} ))
    export CUDA_VISIBLE_DEVICES=${mygpu}
    exec $*
  

2. Attach a GPU to a rank using CUDA library runtime code.

   In this approach the MPI program source must be modified to include GPU device selection code
   before ``MPI_Init()`` is invoked. An example code fragment for GPU device selection (based on the 
   environment variable SLURM_LOCALID) is shown below:
   
.. code:: C

    #include <mpi.h>
    #include <stdio.h>
    #include <cuda_runtime.h>

    int main(int argc, char** argv, char *envp[]) {

    char * localRankStr = NULL;
    int localrank = 0, devCount = 0, mydev;
    // We extract the local rank initialization using an environment variable
    if ((localRankStr = getenv("SLURM_LOCALID")) != NULL) {
      localrank = atoi(localRankStr);
    }
    cudaGetDeviceCount(&devCount);
    mydev=localrank % devCount;
    cudaSetDevice(mydev);
          :
          :
    MPI_Init(NULL, NULL);
          :
          :

Running the MPI program within the batch script
...............................................

To run the MPI program the SLURM command ``srun`` is used (and not ``mpirun`` or ``mpiexec``). The ``srun`` command
works like the MPI run or exec commands but it creates the environment variables needed to select which rank 
works with which GPU prior to any calls to MIT_Init(). An example of using srun with a launch script is shown
below.

.. code:: bash

   srun ./launch.sh ./a.out
   
  
A complete example SLURM batch script
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The script below shows a full example of the steps for CUDA and MPI using GPUs under SLURM. The example
shows both the bash script launcher and the CUDA runtime calls. Only one of these is needed in practice, both
are shown to illustrate the approaches. 

.. code:: bash
    #!/bin/bash
    #
    # Example SLURM batch script to run example CUDA aware MPI program with one rank on 
    # each GPU, using two nodes with 4 GPUs on each node.
    #
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

    cat > launch.sh <<'EOFA'
    #!/bin/bash

    # Choose a CUDA device number ($mygpu) based on ${SLURM_LOCALID}, cycling through
    # the available GPU devices ($ngpu) on the node.
    ngpu=`nvidia-smi -L | grep UUID | wc -l`
    mygpu=$((${SLURM_LOCALID} % ${ngpu} ))
    export CUDA_VISIBLE_DEVICES=${mygpu}

    # Run MPI program with any arguments
    exec $*
    EOFA

    cat > x.cc <<'EOFA'
    #include <mpi.h>
    #include <stdio.h>
    #include <cuda_runtime.h>

    int main(int argc, char** argv, char *envp[]) {

      char * localRankStr = NULL;
      int localrank = 0, devCount = 0, mydev;

      // We extract the local rank initialization using an environment variable
      if ((localRankStr = getenv("SLURM_LOCALID")) != NULL) {
        localrank = atoi(localRankStr);
      }
      cudaGetDeviceCount(&devCount);
      mydev=localrank % devCount;
      cudaSetDevice(mydev);

      MPI_Init(NULL, NULL);
      int world_size;
      MPI_Comm_size(MPI_COMM_WORLD, &world_size);
      int world_rank;
      MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);
      char processor_name[MPI_MAX_PROCESSOR_NAME];
      int name_len;
      MPI_Get_processor_name(processor_name, &name_len);

      // Let check which CUDA device we have
      char pciBusId[13];
      cudaDeviceGetPCIBusId ( pciBusId, 13, mydev );
      printf("MPI rank %d of %d on host %s is using GPU with PCI id %s.\n",world_rank,world_size,processor_name,pciBusId);

      MPI_Finalize();
    }
    EOFA

    mpic++ x.cc -lcudart

    srun ./launch.sh ./a.out

