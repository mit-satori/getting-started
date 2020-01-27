Intergrated energy use profiling
++++++++++++++++++++++++++++++++

Florin Manaila  ( florin.manaila@de.ibm.com )

Description
~~~~~~~~~~~

This example shows how to gather detailed energy use information for a code. The example employs several 
commands that will interfere with other workloads and so should only be used on whole nodes under exclusive
access. The example uses a Tensorflow Convolutional Neural Network benchmark that assumes that a conda
virtual environment based on WMLCE powerai is active. 


Commands to run this example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The example can be run interactively on a single node or in batch mode on multiple nodes. 
Running interactively on multiple nodes is possible in principle but requires
loggin to each node to set up the hardware and to restore its state at the end. This is better
done from a script. 

Running on a single node
@@@@@@@@@@@@@@@@@@@@@@@@

The following commands can be used

#. request a whole node from the scheduler::

     bsub -n 4 -R "span[ptile=4]" -gpu "num=4" -Is bash
     
#. once the node is available set up the environment::
      
     EPUNAME=cnh
     EPCROOT=/nobackup/users/${EPUNAME}/projects/condas/cnh-awesome-new-environment
     module load perftools
     mkdir -p /nobackup/users/${EPUNAME}/four-way-energy
     cd /nobackup/users/${EPUNAME}/four-way-energy
     . ${EPCROOT}/miniconda3/etc/profile.d/conda.sh
     conda activate cnh-awesome-new-environment
     export EGO_TOP=/opt/ibm/spectrumcomputing
     git clone 



Code and input data repositories for this example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~




Useful references
~~~~~~~~~~~~~~~~~


