Creating your own Jupyter kernels for OOD portal
++++++++++++++++++++++++++++++++++++++++++++++++

The OOD portal has a set of centrally installed Jupyter kernels that
include common software. The software aims to cover most
things needed for frequently used tools. If you start using
the portal extensively, sometimes you will come across
packages that are missing and see a message like this image

.. image:: module-not-found.png
  :width: 400
  :alt: Module not found message

When this happens it is possible to create a custom kernel yourself.
This kernel will then be included in the OOD menu for your account. That kernel can
include any available software you want. You can also request that the
module be added to one of the standard environments or that a new standard environment
be added. Sometimes, however, getting a module added to the standard environment 
requires waiting or the module may conflict with something else. 

By learning how to add your own kernels you can avoid having to wait and work around
any incompatibilities. You can also gather customized, versioned sets of tools into 
a standard kernels that can then be shared within a group working on a common 
project, or that you can request be added for your group to a standard set. This can
be more efficient than having to wait for something to installed centrally every
time you find a new awesome package that all the cool people are using. 

An additional benefit of knowing how to create your own kernels is that it can help with sharing and reproducibility
of results. Stric versioning and maintaining historic kernels in perpetuity in central locations 
can easily become an impractical combinatorial challenge. Creating
precise kernels that collect specific version combinations of software in an 
immutable state in a per account location can help, although this still requires care and
diligence!

Steps to create a kernel
========================

These instructions assume you are slightly familiar with conda ( https://docs.conda.io/en/latest/index.html ), that you are
starting a brand new conda environment for this kernel. The steps involve a series of terminal commands. There is a terminal
available in the OOD portal, under the **Clusters** pull-down.

.. image:: clusters-pull-down.png	
  :width: 400
  :alt: Cluster terminal pull down

we will use the OOD terminal for executing the steps needed for a custom kernel. Alternatively, the same steps
can be carried out when logged in from an ssh session, for example via the command line
``ssh -l USER satori-login-001.mit.edu`` of through a client like PuTTY.

To create our new kernel we will  

#. **Create** a brand new, clean conda environment in a new directory
  
#. **Activate** the environment and install the packages we need
  
#. **Initialise** the interactive python settings for the new environment

Commands to type to create a new conda environment and custom kernel
--------------------------------------------------------------------

#. **Create.** These commands create a new conda environment. This example uses the account name ``cnh``. The new 
   environment is placed in a new directory under the scratch space ``/nobackup/users/cnh/projects/condas`` belonging to 
   the account ``cnh``. The new environment is named ``cnh-awesome-new-environment``. These values can be changed for 
   different accounts and ways of organizing files. The command sequence starts with ``conda deactivate`` to prevent
   inadvertantly overwriting and existing conda environment. If conda is not already activted then the message ``conda:
   command not found`` may be printed. That is OK::


        conda deactivate
        CKNAME=cnh-awesome-new-environment
        CKUSER=cnh
        CKROOT=projects/condas
        mkdir -p /nobackup/users/${CKUSER}/${CKROOT}/${CKNAME}
        cd /nobackup/users/${CKUSER}/$CKROOT}/${CKNAME}
        wget https://repo.continuum.io/miniconda/Miniconda2-4.7.12.1-Linux-ppc64le.sh
        chmod +x Miniconda2-4.7.12.1-Linux-ppc64le.sh
        ./Miniconda2-4.7.12.1-Linux-ppc64le.sh -b -p `pwd`/miniconda3
   
   
   **NOTE** - the conda commands tend to hard code full path names as part of their installation. So, once the create step
   is complete, renaming or moving ``/nobackup/users/cnh/projects/condas/cnh-awesome-new-environment``will break the setup. 
   
#. **Activate.** Once the new environment has been created in its own directory, make sure the shell is still has the 
   correct settings and then activate using the following commands::

      CKNAME=cnh-awesome-new-environment
      CKUSER=cnh
      CKROOT=projects/condas
      cd /nobackup/users/${CKUSER}/${CKROOT}/${CKNAME}
      export PATH="`pwd`/miniconda3/bin:$PATH"
      . miniconda3/etc/profile.d/conda.sh
      conda create -y --name ${CKNAME} python=3.6
      conda activate ${CKNAME}



