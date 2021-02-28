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

