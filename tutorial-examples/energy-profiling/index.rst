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

The example can be run interactively or in batch mode. Here we shown interactive execution on 
one node and using four GPUs.

Running on a single node
@@@@@@@@@@@@@@@@@@@@@@@@

The following commands can be used

#. request a whole node from the scheduler::

     bsub -n 4 -R "span[ptile=4]" -gpu "num=4" -Is bash
     
#. once the node is available, set up the environment under your account. The example here shows
   using account ``cnh`` with a suitable conda environment installed 
   in ``/nobackup/users/cnh/projects/condas/cnh-awesome-new-environment``. Different accounts are free to use
   different paths and naming rules::
      
     EPUNAME=cnh
     EPCROOT=/nobackup/users/${EPUNAME}/projects/condas/cnh-awesome-new-environment
     module load perftools
     mkdir -p /nobackup/users/${EPUNAME}/four-way-energy
     cd /nobackup/users/${EPUNAME}/four-way-energy
     . ${EPCROOT}/miniconda3/etc/profile.d/conda.sh
     conda activate cnh-awesome-new-environment
     export EGO_TOP=/opt/ibm/spectrumcomputing
     git clone https://gist.github.com/bff35521a2fa0c499578c98751be1b3c.git
     cp bff35521a2fa0c499578c98751be1b3c/launch.sh .
     cp bff35521a2fa0c499578c98751be1b3c/main_script_commands .
     cp bff35521a2fa0c499578c98751be1b3c/reset.sh .
     cp bff35521a2fa0c499578c98751be1b3c/setup.sh .
     cp bff35521a2fa0c499578c98751be1b3c/read_inst_power_cons.sh .
     rm -fr bff35521a2fa0c499578c98751be1b3c
     chmod +x *.sh
     
#. Once the environment is all set

 #. set the power monitoring script running::
  
      mpirun --tag-output ./setup.sh
      
      
    if things work as expected this should create a file that logs power use from a background 
    script ``read_inst_power_cons.sh`` and you should be able to see the power 
    readings using the command ``cat`` like this::
    
       
        cnh-awesome-new-environment) [cnh@node0047 four-way-energy]$ cat energy-consumption.out.7877_0
         1580143466:     Instantaneous power reading:                   497 Watts
         1580143470:     Instantaneous power reading:                   555 Watts
         1580143473:     Instantaneous power reading:                   557 Watts
         1580143476:     Instantaneous power reading:                   559 Watts
         1580143480:     Instantaneous power reading:                   469 Watts


 #. now we can start an application. First lets use the example from ``https://github.com/johncohn/pytorchstyletransfer_satori.git`` as follows::
 
        git clone https://github.com/johncohn/pytorchstyletransfer_satori.git
        cd pytorchstyletransfer_satori.git
        conda install nbconvert
        jupyter nbconvert --to script TorchTransfer.ipynb
        python TorchTransfer.py
        
    **NOTE** the ``conda install nbconvert`` above is only needed once and may not be needed at all. It is here because
    I found that ``cnh-awesome-new-environment`` was missing the ``nbconvert`` command.
    
    While the ``python TorchTransfer.py`` step is executing you can open another terminal (for example using
    https://satori-portal.mit.edu ) and look at the ``read_inst_power_cons.sh`` output e.g.::
    
        cd /nobackup/users/cnh/four-way-energy/
        cat energy-consumption.out.7877_0
        
     **NOTE** the directory path ``/nobackup/users/cnh/four-way-energy/`` will be different for your tests 
     and so will the job number ``7877``. The output should show an increase in energy use once the GPU compute
     intensive part of the calculation starts e.g ::
     
         1580144216:     Instantaneous power reading:                   484 Watts
         1580144220:     Instantaneous power reading:                   497 Watts
         1580144223:     Instantaneous power reading:                   577 Watts
         1580144227:     Instantaneous power reading:                   686 Watts
         1580144230:     Instantaneous power reading:                   743 Watts

      
 #. we can now do the same with a four gpu applicsation::
  
        ddlrun -v ./launch.sh python $HOME2/hpms/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model resnet50 --batch_size 128 --variable_update=horovod --num_batches=1000 --use_fp16 
        
    in this case the application is a Tensor Flow benchmark, but any application can be used. 
    
  
 
 #. once the application is finished then finish power logging using the command::
 
     mpirun --tag-output ./reset.sh 
     
* The power history (recorded as snapshots every 3 seconds) will be written to a file with name 
  begining ``energy-consumption.out.`` followed by the LSF job id. This file can be listed to the
  screen e.g.::
  
     cat energy-consumption.out.7843_0
     
* **Example all in one batch script**. To see an example all in one script of the above steps for measuring power and 
  capturing energy use use the following commands::

      git clone https://gist.github.com/bff35521a2fa0c499578c98751be1b3c.git
      cp bff35521a2fa0c499578c98751be1b3c/batch-job-example.lsf .
      



Code and input data repositories for this example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~




Useful references
~~~~~~~~~~~~~~~~~


