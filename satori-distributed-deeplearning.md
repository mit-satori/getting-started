## Distributed Deep Learning

The following tools are libraries, which provide the communication functions necessary to perform distributed training. Primarily allReduce and broadcast functions.
- IBM Spectrum MPI: Classic tool for distributed computing.  Still commonly  used for distributed deep learning.
- NVIDIA NCCL: Nvidia’s gpu-to-gpu communication library. Since NCCL2, between-node communication is supported.
- IBM DDL: Provides a topology-aware allReduce. Capable of optimally dividing communication across hierarchies of fabrics.
Utilizes different communication protocols at different hierarchies. When WMLCE is installed all related frameworks are comming with IBM DDL support, you don't have to compile additional software packages, only to modify your training scripts to make use of the need distributed deep learning APIs.

Integrations into deep learning frameworks to enable distributed training is using common communication libraries such as:
- TensorFlow Distribution Strategies. Native Tensorflow distribution methods.
- IBM DDL. Provides integrations into common frameworks, including a Tensorflow operator that integrates IBM DDL with Tensorflow and similar for Pytorch.
- Horovod [Sergeev et al. 2018]. Provides integration libraries into common frameworks which enable distributed training with common communication libraries, including. IBM DDL can be used as backend for Horovod implementation.

Documentation and Tutorial:
1. IBM [DDL integration with TensorFlow/Keras](https://www.ibm.com/support/knowledgecenter/SS5SF7_1.6.1/navigation/wmlce_ddltf_tutorial.html)
2. IBM [DDL integration with Pytorch](https://www.ibm.com/support/knowledgecenter/SS5SF7_1.6.1/navigation/wmlce_ddlpytorch_tutorial.html)
3. IBM [DDL integration with Horovod](https://developer.ibm.com/linuxonpower/2018/08/24/distributed-deep-learning-horovod-powerai-ddl/)

Examples:
- [Keras/TensorFlow](https://github.com/IBM/powerai/tree/master/examples/tensorflow_large_model_support/v2)
- Pytorch

Original IBM DDL paper, can be found at this URL: https://arxiv.org/pdf/1708.02188.pdf
