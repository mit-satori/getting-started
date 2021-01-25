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
     
#. once the node is available, set up the environment under your account. eg::

     conda activate wmlce-1.6.2
     
#. create a sub directory for the run e.g.::

     mkdir powertest; cd powertest

#. load the power profiling tools::

     module load perftools
     git clone https://gist.github.com/bff35521a2fa0c499578c98751be1b3c.git
     cp bff35521a2fa0c499578c98751be1b3c/launch.sh .
     cp bff35521a2fa0c499578c98751be1b3c/main_script_commands .
     cp bff35521a2fa0c499578c98751be1b3c/reset.sh .
     cp bff35521a2fa0c499578c98751be1b3c/setup.sh .
     cp bff35521a2fa0c499578c98751be1b3c/read_inst_power_cons.sh .
     rm -fr bff35521a2fa0c499578c98751be1b3c
     chmod +x *.sh

#. Once the environment is all set, beginin running the power monitoring script ::
  
      mpirun --tag-output ./setup.sh
      
#. If things work as expected this should create a file that logs power use 
   you should be able to see the power  readings using the command ``cat`` like this::
    
     cat energy-consumption.out.<###> 
     
   where <###> is your job number. You should see:: 


         1580143466:     Instantaneous power reading:                   497 Watts
         1580143470:     Instantaneous power reading:                   555 Watts
         1580143473:     Instantaneous power reading:                   557 Watts
         1580143476:     Instantaneous power reading:                   559 Watts
         1580143480:     Instantaneous power reading:                   469 Watts

#. now we can start an application. First lets use the example from ``https://github.com/johncohn/pytorchstyletransfer_satori.git`` as follows::
 
        git clone https://github.com/johncohn/pytorchstyletransfer_satori.git
        cd pytorchstyletransfer_satori
        conda install nbconvert
        jupyter nbconvert --to script TorchTransfer.ipynb
        python TorchTransfer.py
        cd ..
        
    **NOTE** the ``conda install nbconvert`` above is only needed once. 
    
#. While the ``python TorchTransfer.py`` step is executing you can open another terminal  using https://satori-portal.mit.edu  and look at the output. To do that cd to the dirctory you started the  job in (e.g.  powertest/pytorchstyletransfer_satori) and type::
     
     tail -f energy-consumption.out.<###> 
     where <###> is your job number.
      
#. we can now try and do the same with a four gpu application::
  
        git clone https://github.com/tensorflow/benchmarks
        cd benchmarks
        git checkout 1ef603fd7e568ff75127ec07f160808fcc59911c
        cd ..
        conda install gxx_linux-ppc64le=7.3.0 cffi cudatoolkit-dev
        HOROVOD_CUDA_HOME=$CONDA_PREFIX HOROVOD_GPU_ALLREDUCE=DDL pip install horovod --no-cache-dir
        ddlrun -v ./launch.sh python /nobackup/users/florin/hpms/tf_cnn_benchmarks/tf_cnn_benchmarks.py --model resnet50 --batch_size 128 --variable_update=horovod --num_batches=1000 --use_fp16 
        
    in this case the application is a Tensor Flow benchmark, but any application can be used. **NOTE** as before we
    only need to add the ``conda install`` steps once. There is also another wrinkle in this example. The Horovod modules
    need to be built for a specific configuraiton. We pass the environment variables to ``pip install`` to
    select the configuraiton. We also need to use the pip ( ) installation tool for Horovod, not conda. There 
    is no clear reason why Horovod could not be in Conda, but sometimes computing is like that! 
    
    Again, while the application is running we can check out the power log and we can make a plot from
    the log too. Unfortunately this example doesn't quite work yet! 
    
  
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
      


* **Congratulations if yuu got this far**. You have earned a cookie and are now in a good state to try measuring and
  optimizing resource use in your favorite project! **GOOD LUCK**.

Code and input data repositories for this example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

see text above


Useful references
~~~~~~~~~~~~~~~~~


