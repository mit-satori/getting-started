Satori Login
============

You can connect to Satori through 

* a web portal, or 

* plain ssh from a terminal or a terminal client like PuTTY (https://www.putty.org). 

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

the web portal also has sub-menus for

#. a terminal on the front-end node (**Clusters** menu)

#. a running job monitoring window (**Jobs** menu). This is also has a batch job preparation form, but
   it is malfunction at the moment! 
   
#. a file browsing and transfer client (**Files** menu)

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


As general rules:

-  satori-login-001.mit.edu - should be used for submiting training jobs
   and related activities
-  satori-login-002.mit.edu - should be used for transfering large
   files/datasets and compiling software requiring nvcc, gcc, XL
   compiler etc
-  if one login-node will not be available try the second one
-  don’t run large computations on the login nodes


In case you want to work with Jupyter Notebooks but cannot use the https://satori-portal.mit.edu portal, you will 
need to forward Jupyter HTTP ports to your own workstation via SSHv2 tunneling.
This can be done like this: (Note FQDN == Fully Qualified Domain Name like nodeXXXX  for the node you are running on (E.g. node0014)

.. code:: bash 

   ssh -L 10001:<compute_node_FQDN>:10001 your_username@satori-login-001.mit.edu
   jupyter notebook --ip=<compute_node_FQDN> --port=10001
   
if you do this, remember to activate whatever conda enviroment you wish to use before starting ``jupyter`` e.g

.. code:: bash 

   
   conda activate wmlce-1.6.2
   

