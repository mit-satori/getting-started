Singularity for Satorians
=========================
Fast start
^^^^^^^^^^

* Pull your image

.. code:: bash

  export SINGULARITY_DOCKER_ARCHITECTURE=ppc64le
  singularity pull docker://centos:7

* Get an allocation, for example:

.. code:: bash

   srun --gres=gpu:4 -N 1 --mem=1T --time 1:00:00 -l --pty /bin/bash 
   
* Submit your payload

.. code:: bash

  singularity exec centos_7.sif ls /

Other notes
^^^^^^^^^^^
* Pull your image
* If you are using an existing image, confirm that arch is **ppc64le**   

Interactive Allocation:
^^^^^^^^^^^^^^^^^^^^^^^
* Get an allocation (for interactive allocation):
* Consider the size of your workload, do you need CPU? memory? GPU?
* These are all specified in the bsub request, here is a good [#]_ 
* For example, here is a `bsub` to request allocation for **4 GPUS**, via the **normal** queue, launching an interactive session using **bash** as terminal
 
.. code:: bash
  
    srun --gres=gpu:4 -N 1 --mem=1T --time 1:00:00 -l --pty /bin/bash 
  
* Submit your payload
* Using interactive allocation, we are allocated a node - your interactions with singularity will be generally standard , just like you would in any environments
* Remember you do not have `sudo` access on this cluster, this might impact your workflow
 
.. code:: bash

   singularity exec centos_7.sif ls /
  
Non interactive / batch mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
* Workflow is similar to interactive allocation, however we need to create an application profile prior to submitting the workload
* This mode might be better for headless / non-interactive workloads as the resources can be more easily allocated to the workload
* Create an application profile [#]_
* For example

.. code:: bash

   Begin Application
   NAME = singapp
   CONTAINER = singularity[image(centos_7.sif)]
   DESCRIPTION = Singularity User Service
   End Application
   
* Submit the workload via the application profile [#]_
* For example
 
.. code::
  
   sbatch singapp ./myjob.sh

.. [#] (https://www.ibm.com/support/knowledgecenter/SSWRJV_10.1.0/lsf_command_ref/bsub.man_top.1.html)
.. [#] (https://www.ibm.com/support/knowledgecenter/en/SSWRJV_10.1.0/lsf_container/lsf_singularity_config.html).
.. [#] (https://www.ibm.com/support/knowledgecenter/en/SSWRJV_10.1.0/lsf_container/lsf_singularity_use.html).
