Web Portal Login
================

SSH Login
=========

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
