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
