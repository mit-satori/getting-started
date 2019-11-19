## IBM Large Model Support (LMS)

Allow seamlessly moves layers of a model between the GPU and CPU to overcome GPU memory limits allows training of:
- Deeper models
- Higher resolution data
- Larger batch sizes
Satori nodes have a fast NVLink 2.0 connection between the CPU and GPU, which allows for data swapping with minimal overhead compared with traditional x86 GPU accelerated systems where PCIe Gen3 is available for connection between the CPU and GPU.


Documentation and Tutorial:
- [LMS in TensorFlow](https://www.ibm.com/support/knowledgecenter/SS5SF7_1.6.2/navigation/wmlce_getstarted_tflmsv2.html)
- [LMS in Pytorch](https://www.ibm.com/support/knowledgecenter/SS5SF7_1.6.2/navigation/wmlce_getstarted_pytorch.html#wmlce_getstarted_pytorch__lms_section)

Examples:
- [Simple Keras/TensorFlow with syntetic data](https://github.com/IBM/powerai/tree/master/examples/tensorflow_large_model_support/v2) for high resolution images
- [3D U-Net with Keras/TensorFlow for Medical Images](https://github.com/smatzek/3DUnetCNN)
- [ResNet with LMS in Pytorch](https://github.com/mtbrandy/pytorch/wiki/Large-Model-Support#example) 
