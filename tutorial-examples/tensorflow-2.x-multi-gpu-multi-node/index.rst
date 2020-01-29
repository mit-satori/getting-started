
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

#. wget https://raw.githubusercontent.com/IBM/coursera/master/coursera_ai/week4/multi_worker_with_keras.py

#. chmod 755 multi_worker_with_keras.py

#. Install TensorFlow 2.0 as described here => IMPORTANT <= this might change, please ask John or Chris => https://mit-satori.github.io/satori-ai-frameworks.html#the-wml-ce-early-access-channel-is-available-at-https-public-dhe-ibm-com-ibmdl-export-pub-software-server-ibm-ai-conda-early-access

#. bsub -W 3:00 -q normalx -x  -n 256  -gpu "num=4" -R "span[ptile=4]" -I "while (true) do ls > /dev/null; done" (replace 256 with a number smaller than 256 :)

#. login to a new shell

#. nodes=`bjobs |grep 4*node |awk -F"\*" '{print $2}' |awk -F"." '{print $1}'`

#. echo ${TF_CONFIG='{"cluster": {"worker": ["nodes:12345", "node0053:12345"]}, "task": {"index": 0, "type": "worker"}}'}
{"cluster": {"worker": ["node:12345", "node0053:12345"]}, "task": {"index": 0, "type": "worker"}}

#. conda activate wmlce-ea

#. paste and execute the following (replace node0046 and node0053 with your nodes) into the terminal 
TF_CONFIG='{"cluster": {"worker": ["node0046:12345", "node0053:12345"]}, "task": {"index": 0, "type": "worker"}}'

#. python multi_worker_with_keras.py

#. Go back to your first terminal window where "YOUR" node0046 node resides

#. paste and execute the following (replace node0046 and node0053 with your nodes) into the terminal 
TF_CONFIG='{"cluster": {"worker": ["node0046:12345", "node0053:12345"]}, "task": {"index": 1, "type": "worker"}}'
PLEASE NOTE THAT THE INDEX HAS CHANGED

#. python multi_worker_with_keras.py

Wait until training starts on both terminal windows (it should now)

What's going on here?
+++++++++++++++++++++
Both scripts start a Service component which communicates with the other scripts in the background for parameter averaging.


Code and input data repositories for this example
+++++++++++++++++++++++++++++++++++++++++++++++++

Code:
https://raw.githubusercontent.com/IBM/coursera/master/coursera_ai/week4/multi_worker_with_keras.py

Useful references
+++++++++++++++++
https://lambdalabs.com/blog/tensorflow-2-0-tutorial-05-distributed-training-multi-node/
https://www.tensorflow.org/api_docs/python/tf/distribute/experimental/MultiWorkerMirroredStrategy




