## IBM Large Model Support (LMS)

Allow seamlessly moves layers of a model between the GPU and CPU to overcome GPU memory limits allows training of:
- Deeper models
- Higher resolution data
- Larger batch sizes
Satori nodes have a fast NVLink 2.0 connection between the CPU and GPU, which allows for data swapping with minimal overhead compared with traditional x86 GPU accelerated systems where PCIe Gen3 is available for connection between the CPU and GPU.

```bash
# Example for enable TFLMSv2 in TensorFlow
# ----------------------------------------
# Import the TF LMS module
from tensorflow_large_model_support import LMS
# Instantiate the LMS object, with maximum swapping parameters 
# If you wanted to make use of the auto-tuning feature simply 
# initialise the LMS object without any arguments 
# e.g. lms_hook = LMS()
lms_hook = LMS(swapout_threshold=1,
               swapin_ahead=0,
               swapin_groupby=0,
               sync_mode=0)
# Make LMS aware of the train_batch_size parameter
lms_hook.batch_size = FLAGS.train_batch_size
# Include the lms_hook object in the estimator hooks list
estimator.train(input_fn=train_input_fn,
                max_steps=num_train_steps,
                hooks=[lms_hook])
```                

NOTE: TFLMSv2 introduces four hyper-parameters to work with. Typically you don't need to worry about them, as LMS introduces an auto-tuning feature which automatically evaluates your computational graph and sets appropriate values for these hyper-parameters, based upon estimated memory consumption throughout training. However manual tuning allows for closer control- squeezing out maximum performance. The four hyper-parameters introduced are:
- swapout_threshold: The number of tensors to hold within GPU memory before pushing them to system memory.
- swapin_ahead: The larger swapin_ahead is, the earlier a tensor is swapped in to the GPU memory from the host memory.
- swapin_groupby: Multiple swap-in operations of the same tensor will be grouped or fused into one swap-in operation for better performance if they are close to each other (the distance between them is within swapin_groupby).
- sync_mode: Whether to do synchronisation between data transfer and kernel computation or not.

Documentation and Tutorial:
- [LMS in TensorFlow](https://www.ibm.com/support/knowledgecenter/SS5SF7_1.6.2/navigation/wmlce_getstarted_tflmsv2.html)
- [LMS in Pytorch](https://www.ibm.com/support/knowledgecenter/SS5SF7_1.6.2/navigation/wmlce_getstarted_pytorch.html#wmlce_getstarted_pytorch__lms_section)

Examples:
- [Simple Keras/TensorFlow with syntetic data](https://github.com/IBM/powerai/tree/master/examples/tensorflow_large_model_support/v2) for high resolution images
- [3D U-Net with Keras/TensorFlow for Medical Images](https://github.com/smatzek/3DUnetCNN)
- [ResNet with LMS in Pytorch](https://github.com/mtbrandy/pytorch/wiki/Large-Model-Support#example) 
