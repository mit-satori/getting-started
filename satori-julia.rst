Julia on Satori
===============

Julia's PowerPC support is currently limited and community based, if you run into
issues please leave a note on the #julia channel on the Satori Slack.

Getting started
^^^^^^^^^^^^^^^

.. code:: bash

  module load julia

Getting help?
^^^^^^^^^^^^^

The Julia community has several help channels available, if you have generic
Julia question the `Julia Discourse <https://discourse.julialang.org>`__  or `Julia Slack <https://slackinvite.julialang.org/>`__
are the best place to quickly get answers.

If you have Satori/PowerPC specific questions feel free to reach out to vchuravy either on the Satori Slack or mail.

Recipe for running single GPU, single threaded interactive session with CUDA aware MPI
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To run a single threaded Julia session on a GPU with MPI enanbled, first start an interactive session on a node::

   srun --gres=gpu:1 -N 1 -n 1 --mem 1T -t 12:00:00 --pty /bin/bash
   
then use the following to set environment::
   
   module purge > /dev/null 2>&1
   module add spack
   module load gcc/8.3.0 
   module load julia/1.4.1
   module load spack-admin/1.0
   module load spack-flat/0.1
   module load openmpi/3.1.4-gcc-8.3.0-cuda-pmi-ucx
   export UCX_ERROR_SIGNALS="SIGILL,SIGBUS,SIGFPE"
   export JULIA_MPI_BINARY=system
   export JULIA_CUDA_USE_BINARYBUILDER=false
   
to initialize julia packages (one time) use::
   
   cd /nobackup/users/cnh/julia_cuda
   export JULIA_DEPOT_PATH=`pwd`
   julia -e 'using Pkg; Pkg.add("MPI"); Pkg.build("MPI"); Pkg.precompile()'
   

running julia::

   julia 
   julia> using Pkg; using MPI
   julia> MPI.Init()
   
   

