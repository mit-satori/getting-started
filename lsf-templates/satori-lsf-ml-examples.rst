Example machine learning LSF jobs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A single node, 4 GPU Keras example
++++++++++++++++++++++++++++++++++
 
    The LSF batch script here,
    https://github.com/mit-satori/getting-started/blob/master/lsf-templates/template-Keras_wLMS-singlenode.lsf , executes
    an example Keras machine learning framework code. The problem demonstrates using large memory support to leverage memory
    across multiple GPUs. 


A single node, 4 GPU Caffe example
++++++++++++++++++++++++++++++++++
  The LSF batch script here,
  https://raw.githubusercontent.com/IBM/powerai/master/examples/tensorflow_large_model_support/v2/Keras_ManyModel.py , 
  launches Caffe on a single node and with 4 GPUs allocated.
  
A multi-node, pythorch example
++++++++++++++++++++++++++++++
  The LSF batch script here, https://github.com/mit-satori/getting-started/blob/master/lsf-templates/template-pytorch-multinode.lsf , launches pytorch on multiple nodes, each with multiple GPUs. 
