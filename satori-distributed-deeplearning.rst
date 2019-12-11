Distributed Deep Learning
-------------------------

The following tools are libraries, which provide the communication
functions necessary to perform distributed training. Primarily allReduce
and broadcast functions.

-  IBM Spectrum MPI: Classic tool for distributed computing. Still
   commonly used for distributed deep learning.
-  NVIDIA NCCL: Nvidia’s gpu-to-gpu communication library. Since NCCL2,
   between-node communication is supported.
-  IBM DDL: Provides a topology-aware all-Reduce. Capable of optimally
   dividing communication across hierarchies of fabrics. Utilizes
   different communication protocols at different hierarchies. When
   WMLCE is installed all related frameworks are comming with IBM DDL
   support, you don’t have to compile additional software packages, only
   to modify your training scripts to make use of the need distributed
   deep learning APIs.

Integrations into deep learning frameworks to enable distributed
training is using common communication libraries such as:

-  TensorFlow Distribution Strategies. Native Tensorflow distribution
   methods.
-  IBM DDL. Provides integrations into common frameworks, including a
   Tensorflow operator that integrates IBM DDL with Tensorflow and
   similar for Pytorch.
-  Horovod [Sergeev et al. 2018]. Provides integration libraries into
   common frameworks which enable distributed training with common
   communication libraries, including. IBM DDL can be used as backend
   for Horovod implementation.

IBM DDL - Documentation and Tutorial:

1. IBM `DDL integration with
   TensorFlow/Keras <https://www.ibm.com/support/knowledgecenter/SS5SF7_1.6.2/navigation/wmlce_ddltf_tutorial.html>`__
2. IBM `DDL integration with
   Pytorch <https://www.ibm.com/support/knowledgecenter/SS5SF7_1.6.2/navigation/wmlce_ddlpytorch_tutorial.html>`__
3. IBM `DDL integration with
   Horovod <https://developer.ibm.com/linuxonpower/2018/08/24/distributed-deep-learning-horovod-powerai-ddl/>`__
4. IBM `DDL
   APIs <https://www.ibm.com/support/knowledgecenter/SS5SF7_1.6.2/navigation/wmlce_ddlapi.html>`__
   for a better integration

Examples:

-  `Keras/TensorFlow <https://github.com/IBM/powerai/tree/master/examples/tensorflow_large_model_support/v2>`__
-  Pytorch


How to get Horovod with DDL? follow bellow instructions (optional 0 - 2 if you have already install WMLCE):

0. Add ppc64le conda channel for WMLCE

.. code:: bash
   conda config --prepend channels \
   https://public.dhe.ibm.com/ibmdl/export/pub/software/server/ibm-ai/conda/
   
   
1. Create Conda Virtual Environment

.. code:: bash
   conda create --name horovod python=3.6
   
   
2. Install WMLCE (TF, Pytorch, DDL etc)

.. code:: bash
   conda install powerai
   
   
3. Install the packages to build Horovod

.. code:: bash
   conda install gxx_linux-ppc64le=7.3.0 cffi cudatoolkit-dev
   
   
4. Install Horovod with DDL backend

.. code:: bash
   HOROVOD_CUDA_HOME=$CONDA_PREFIX HOROVOD_GPU_ALLREDUCE=DDL pip install horovod --no-cache-dir


Original IBM DDL paper, can be found at this URL:
https://arxiv.org/pdf/1708.02188.pdf
