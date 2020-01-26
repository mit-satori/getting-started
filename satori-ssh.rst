Satori Login
============

You can connect to Satori through a web portal that uses the open source Open On Demand ( https://osc.github.io/ood-documentation/master/architecture.html ) software, or through plain ssh from a terminal or a terminal client like PuTTY (https://www.putty.org). 

Web Portal Login
^^^^^^^^^^^^^^^^

To use the web portal

#. Navigate to https://satori-portal.mit.edu in your favorite web browser.

#. Select the Globus Auth button.

#. When redirected to globus, authenticate via Massachusetts Institute of Technology.

#. When successfully logged in, you will be redirected back to Satori Portal! 


you can then launch Jupyter notebook environments running on GPUs 


#. Select "Jupyter Notebook" from the "Interactive Apps" dropdown in the top navbar. You can also select "My Interactive Sessions".

#. At the prompt to launch a Jupyter notebook, select the "Anaconda 2019.10 PowerAI [Experimental]" option in the "Choose Anaconda Environment" section. 

#. Enter (in hours) how long you would like the app to run for. 

#. Click launch, wait for app to begin running on a compute node and finally, select "Connect to Jupyter"!

SSH Login
^^^^^^^^^

.. figure:: http://www.sldcassam.in/images/login.png
   :alt: Satori Login


-  Login to Satori cluster is done via SSHv2 as follow:

.. code:: bash

   ssh your_username@satori-login-001.mit.edu

.. code:: bash

   ssh your_username@satori-login-002.mit.edu

-  To copy files from your workstation to Satori you can use SCP,
   `WinSCP <https://winscp.net/eng/download.php>`__,
   `Cyberduck <https://cyberduck.io/download/>`__ etc

.. code:: bash

   scp <local-file.py> your_username@satori-login-001.mit.edu:

-  For example:

.. code:: bash

   scp ResNet50-Keras.py florin@satori-login-002.mit.edu:
   ssh florin@satori-login-002.mit.edu
   conda activate wmlce-1.6.2
   python ResNet50-Keras.py

As general rules:

-  satori-login-001.mit.edu - shuld be used for submiting training jobs
   and related activities
-  satori-login-002.mit.edu - shuld be used for transfering large
   files/datasets and compiling software requireing nvcc, gcc, XL
   compiler etc
-  if one login-node will not be available try the second one
-  don’t run trainings on login nodes

In case you want to work with Jupiter Notebooks, you will need to
forward Jupiter HTTP ports to your own workstation via SSHv2 tunneling.
This can be done like this: (Note FQDN == Fully Qualified Domain Name like nodeXXXX  for the node you are running on (E.g. node0014)

.. code:: bash 

   ssh -L 10001:<compute_node_FQDN>:10001 your_username@satori-login-001.mit.edu
   conda activate wmlce-1.6.2
   jupyter notebook --ip=<compute_node_FQDN> --port=10001
