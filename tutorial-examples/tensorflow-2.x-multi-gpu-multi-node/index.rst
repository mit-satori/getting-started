
Multi Node Multi GPU TensorFlow 2.0 Distributed Training Example
----------------------------------------------------------------

Romeo Kienzler ( 1 s t n a m e . l a s t n a m e  a t  c h . i b m . c o m )

Jerome Nilmeier ( l a s t n a m e  a t  us . i b m . c o m )

Description
+++++++++++
Ported the TensorFlow example to run on Satori


Commands to run this example
++++++++++++++++++++++++++++

#. Login to Satori Login Node

#. wget https://raw.githubusercontent.com/mit-satori/getting-started/master/tutorial-examples/tensorflow-2.x-multi-gpu-multi-node/multi_worker_with_keras_runner.py

#. chmod 755 multi_worker_with_keras_numpyArrays.py

#. wget https://raw.githubusercontent.com/mit-satori/getting-started/master/tutorial-examples/tensorflow-2.x-multi-gpu-multi-node/multi_worker_with_keras_runner.py

#. chmod 755 multi_worker_with_keras_runner.py

#. Install TensorFlow 2.0 as described here => IMPORTANT <= this might change, please ask John or Chris => https://mit-satori.github.io/satori-ai-frameworks.html#the-wml-ce-early-access-channel-is-available-at-https-public-dhe-ibm-com-ibmdl-export-pub-software-server-ibm-ai-conda-early-access => it is important that you name your environment wmlce-ea

#. bsub -W 3:00 -q normalx -x  -n 8  -gpu "num=4" -R "span[ptile=4]" -I "while (true) do ls > /dev/null; done" (replace 2586 with a number smaller equals than 256 :)

#. login to a new shell

#. nodes=`bjobs |grep 4*node |awk -F"\*" '{print $2}' |awk -F"." '{print $1}'`

#. echo $nodes |python multi_worker_with_keras_runner.py


Wait until training starts, please run different new terminals on your worker nodes to observe what's happening

watch -n 0.1 nvidia-smi

What's going on here?
+++++++++++++++++++++
All scrips running on all nodes start a Service component which communicates with the other scripts in the background for parameter averaging.


Code and input data repositories for this example
+++++++++++++++++++++++++++++++++++++++++++++++++

Code:
https://raw.githubusercontent.com/IBM/coursera/master/coursera_ai/week4/multi_worker_with_keras.py

Useful references
+++++++++++++++++
https://lambdalabs.com/blog/tensorflow-2-0-tutorial-05-distributed-training-multi-node/
https://www.tensorflow.org/api_docs/python/tf/distribute/experimental/MultiWorkerMirroredStrategy




