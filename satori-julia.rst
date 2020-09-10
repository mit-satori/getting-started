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

A simple batch script example
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A simple batch script for a julia program on Satori is shown below::

   #!/bin/bash
   #SBATCH -J CLIMA_TEST_35
   #SBATCH -o CLIMA_TEST_35_%j.out
   #SBATCH -e CLIMA_TEST_35_%j.err
   #SBATCH --mail-user=XXXXX@mit.edu
   #SBATCH --mail-type=ALL
   #SBATCH --gres=gpu:1
   #SBATCH --nodes=1
   #SBATCH --mem=0
   #SBATCH --time=2:00:00
   #SBATCH --exclusive
   cd /nobackup/users/jars/projects/runscripts/
   module add spack
   module add spack-flat
   module load openmpi/3.1.4-gcc-8.3.0-cuda-pmi-ucx
   date
   /home/software/julia/1.4.1/bin/julia --project ../CLIMA file_35.jl
   date

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
   
   

Running a multi-process julia program somewhat interactively
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When debugging and testing it is sometimes to run outside of sbatch but still use multiple GPUs.
The best way to do this is through the Slurm ``salloc`` command. This command reserves resources
and then allows multiple job stages to be executed through the ``srun`` command from a shell as shown::

   salloc --gres=gpu:3 -N 1 -n 3 --mem 1T -t 12:00:00
   
now use srun to launch individual steps e.g.::

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
   cd /nobackup/users/cnh/projects/jmpi/
   export JULIA_DEPOT_PATH=`pwd`
   julia -e 'using Pkg; Pkg.add("MPI"); Pkg.build("MPI"); Pkg.precompile()'
   
::

   srun -n 3 julia foo.jl
   
..

      2: Sending   2 -> 0 = [2.0, 2.0, 2.0, 2.0]
      
      0: Sending   0 -> 1 = [0.0, 0.0, 0.0, 0.0]
      
      1: Sending   1 -> 2 = [1.0, 1.0, 1.0, 1.0]
      
      2: Received 1 -> 2 = [1.0, 1.0, 1.0, 1.0]
      
      0: Received 2 -> 0 = [2.0, 2.0, 2.0, 2.0]
      
      1: Received 0 -> 1 = [0.0, 0.0, 0.0, 0.0]

::

  cat foo.jl
  
..
  
     using MPI
     MPI.Init()

     comm = MPI.COMM_WORLD
     rank = MPI.Comm_rank(comm)
     size = MPI.Comm_size(comm)

     dst = mod(rank+1, size)
     src = mod(rank-1, size)

     N = 4

     send_mesg = Array{Float64}(undef, N)
     recv_mesg = Array{Float64}(undef, N)

     fill!(send_mesg, Float64(rank))

     rreq = MPI.Irecv!(recv_mesg, src,  src+32, comm)

     print("$rank: Sending   $rank -> $dst = $send_mesg\n")
     sreq = MPI.Isend(send_mesg, dst, rank+32, comm)

     stats = MPI.Waitall!([rreq, sreq])

     print("$rank: Received $src -> $rank = $recv_mesg\n")

     MPI.Barrier(comm)


An example of installing https://github.com/clima/climatemachine.jl on Satori
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Because Satori is a Power 9 CPU based system some Julia packages require a few extra steps to
ensure the right binaries are deployed. An example of
installing and running https://github.com/clima/climatemachine.jl in a clean enironment 
illustrates some steps that may occaisionally be needed. 


- Launch a an interactive node session:: 

     srun --gres=gpu:1 -N 1 -n 1 --mem 1T -t 12:00:00 --pty /bin/bash
     
     
- Create a fresh directory::

    mkdir /nobackup/user/cnh/projects/myclima
    git clone https://github.com/clima/climatemachine.jl
    cd /nobackup/user/cnh/projects/myclima
    
- Set up the Julia environment::

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
   export JULIA_DEPOT_PATH=`pwd`/../.julia
   \rm -fr $JULIA_DEPOT_PATH
   julia -e 'using Pkg; Pkg.add(PackageSpec(name="Conda",rev="master")); Pkg.build("Conda")'
   julia -e 'using Pkg; Pkg.add("NCDatasets"); Pkg.build("NCDatasets")'
   julia -e 'using Pkg; Pkg.add(PackageSpec(name="Dierckx",rev="master")); Pkg.build("Dierckx")'
   julia --project=@. -e 'using Pkg; Pkg.instantiate(); using ClimateMachine'
   
 In this example the master revision of the Conda and Dierckx packages are used because versions
 with full support for Power 9 were not available and so some small patches had to be introduced. 
 Releases of these packages with the patches will be available soon, but the steps shown illustrate
 some of the ways to work around these sorts of occurences generally. 


