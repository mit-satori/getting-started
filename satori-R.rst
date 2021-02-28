R on Satori
===========

R's PowerPC support is currently limited, and there may be issues trying to install it from source. R can instead be run on Satori using either a Spack build or a container.

Getting Started with R
~~~~~~~~~~~~~~~~~~~~~~

A fast way to get R running on Satori is to load the Spack build

.. code:: console 

    module load spack-flat
    module load r
    R


Installing Packages
~~~~~~~~~~~~~~~~~~~

To avoid R packages taking up space on home, you can consider setting the R library path inside `/nobackup`, e.g.

.. code:: R

    .libPaths("/nobackup/users/{username}/packages/R")

R packages can be installed as usual:

.. code:: R
    
    install.packages("ggplot2")
    
A Simple Batch Script Example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Here is a simple example of a batch script for running a small R job.

.. code:: bash

    #!/bin/bash
    #SBATCH -J my_r_job
    #SBATCH -o my_r_job.out
    #SBATCH -e my_r_job.err
    #SBATCH --mail-user=XXXXXXX@mit.edu
    #SBATCH --mail-type=ALL
    #SBATCH --gres=gpu:1
    #SBATCH --nodes=1
    #SBATCH --mem=50G
    #SBATCH --time=2:00:00
    module add spack-flat
    module add r
    Rscript script.R

R and Python
~~~~~~~~~~~~

With the R Spack build, it is possible to interface between R and Python using `rpy2`. However, as this uses multiple environments, it may break things.

.. code:: bash

    module load spack-flat
    module load r
    conda activate {env_name}
    {env_dir}/{env_name}/bin/python3 -m pip install rpy2
    {env_dir}/{env_name}/bin/python3 -c "import rpy2"

Running R in a container
~~~~~~~~~~~~~~~~~~~~~~~~

It is also possible to run R within a container, which has the advantage of letting you work with R in a more closed system. This involves pulling the `docker://ppc64le/r-base` container, which can be done by

.. code:: console

    cd /nobackup/users/$USER/containers
    singularity pull r-base.img docker://ppc64le/r-base

For installation and other interactive tasks you can launch the container in interactive mode

.. code:: console

    cd /nobackup/users/$USER/containers
    singularity shell r-base.img
    R
    install.packages("ggplot2")
    
    
You can also run R scripts by executing the container. The following example also binds a local folder `/nobackup/users/{username}/project_folder` to the container as `/project_folder` to allow for reading and writing data to disk.

.. code:: console

    cd /nobackup/users/$USER/containers
    singularity exec --bind /nobackup/users/$USER/project_folder:/project_folder r-base.img Rscript script.R
