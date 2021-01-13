IBM Watson Machine Learning Community Edition (WML-CE) and Open Cognitive Environment (Open-CE)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Watson Machine Learning Community Edition is an IBM Cognitive Systems 
offering that is designed for the rapidly growing and quickly evolving 
AI category of deep learning.
WML-CE and Open-CE brings a suite of capabilities from the open source
community and combines them into a single enterprise distribution of
software that incorporates complete lifecycle management from
installation and configuration; data ingest and preparation; building,
optimizing, and training the model; to inference; testing; and moving
the model into production. WML-CE and Open-CE takes advantage of a distributed
architecture to help enable your teams to quickly iterate through the
training cycle on more data to help continuously improve the model over
time.

WML-CE and Open-CE are designed for scale, with software optimised for both
single server and cluster deep learning training. It offers many
optimizations that can ease installation and management, and can help
accelerate performance:

-  Ready-to-use deep learning frameworks (TensorFlow, PyTorch, Caffe,
   Caffe2, ONNX, and Keras).
-  Powerful and scalable machine learning libraries (Snap ML and NVIDIA
   RAPIDS).
-  Distributed as prebuilt containers, or on demand through the Conda
   provisioning process.
-  Includes dependencies and libraries.
-  Easy updates: Code updates arrive from a repository.
-  Validated deep learning platform with each release.
-  Dedicated support teams for deep learning.
-  Designed for enterprise scale with multisystem cluster performance
   and large memory support.
-  Supported on GPU accelerated IBM AC922 servers; and also supported on
   accelerated x86 architecture servers.



[1] Install Anaconda
''''''''''''''''''''

All users on Satori will have two folders:

.. code:: bash

   /home/<username>
   /nobackup/users/<username>
   
please download and install Anaconda3 in the: /nobackup/users/<your-username>/anaconda3 
This is because /nobackup disk partition has way much more space compared with /home. In addition all files in /home will be automaticlay backuped compared with /nobackup partition. Anaconda3 can be install at any time in less then 10 minutes, therefore no backup is need.

To install WMLCE you need to install on your login account `Anaconda for
POWER9/ ppc64le 
architecture <https://www.anaconda.com/distribution/#download-section>`__.
download of the Anaconda can be done using ``wget``:

.. code:: bash

   cd /nobackup/users/$(whoami)
   wget https://repo.anaconda.com/archive/Anaconda3-2019.10-Linux-ppc64le.sh
   sh Anaconda3-2019.10-Linux-ppc64le.sh -f -p /nobackup/users/$(whoami)/anaconda3
   source ~/.bashrc

By default Anaconda will be insalled in your home folder under
``anaconda3`` and all the WMLCE pachages will be install in a
sub-directory on chosen virtual name/folder (ie.
anaconda3/envs/wmlce-1.7.0)

[2] WML-CE and Open-CE: Setting up the software repository
'''''''''''''''''''''''''''''''''''''''''''''

The WML-CE and Open-CE MLDL packages are distributed as conda packages in an online
conda repository. conda must be configured to give priority to
installing packages from this channel.

Add the IBM WML-CE channel to the conda configuration by running the
following command:

.. code:: bash

   conda config --prepend channels \
   https://public.dhe.ibm.com/ibmdl/export/pub/software/server/ibm-ai/conda/

Add, the MIT Open-CE channel to the conda configuration by running the
following command:

.. code:: bash

   conda config --prepend channels \
   https://opence.mit.edu
   

NOTE: Moving forward with new AI frameworks and new related packages OPENCE.MIT.EDU conda channel will the prefered choice (i.e. TensorFlow 2.3.1, 2.4.0, Pytorch 1.6.0, 1.7.1 etc).


[3] WML-CE and Open-CE: Creating and activate conda environments (recommended)
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

With conda, you can create environments that have different versions of
Python or packages installed in them. Conda environments are optional
but recommended. If not used, packages are installed in the default
environment called base, which often has a higher risk of containing
conflicting packages or dependencies. Switching between environments is
called activating the environment.

The syntax to create and activate a conda environment is:

.. code:: bash

   conda create --name <environment name> python=<python version>
   conda activate <environment name>

The only valid Python versions with WML-CE are Python 3.6, 3.7 and for Open-CE are Python 3.6, 3.7, 3.8 . For
example, to create an environment named wmlce_env with Python 3.6:

.. code:: bash

   conda create --name wmlce-1.7.0 python=3.6
   conda activate wmlce-1.7.0
   
   
or

.. code:: bash

   conda create --name opence python=3.7
   conda activate opence
   
   
NOTE: As a good practice to maintain the disk space usage on Satori please run at regular intervals the following command that will remove unused packages and caches from your Anaconda profile. In addition this command shuld be use before installing a new version of WML-CE/Open-CE on another virtual environment.

.. code:: bash

   conda clean --all
   
   


[4] WML-CE: Installing all frameworks at the same time
'''''''''''''''''''''''''''''''''''''''''''''''''''''

All the MLDL frameworks except RAPIDS packages can be installed at the
same time by using the powerai meta-package. All the RAPIDS packages can
be installed using the powerai-rapids meta-package.

.. code:: bash

   conda install powerai

Additionaly pachages can be installed with the conda environment
activated, runining the following command:

.. code:: bash

   conda install <package name>

For example:

.. code:: bash

   conda install powerai-rapids
   conda install dali
   conda install apex

NOTE: During the conda install, the packages are downloaded from the
internet and after downloading, the license agreement is presented. Read
the license agreement and accept the terms and conditions to complete
the install. If you decline the license agreement the packages are not
installed. After you finish reading the license agreement, future
installations can be automated to silently accept the license agreement
by running the following command before running the conda install
command:

.. code:: bash

   export IBM_POWERAI_LICENSE_ACCEPT=yes

The license accept has to be done only once on a per user basis.

[5] WML-CE: Testing ML/DL frameworks (Pytorch, TensorFlow etc) installation
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

.. code:: bash

   conda activate wmlce-1.7.0
   python

a. PYTORCH

.. code:: bash

   import torch
   import torch.nn as nn
   import torch.nn.functional as F
   import torch.optim as optim
   torch.manual_seed(1)
   lin = nn.Linear(5, 3)  # maps from R^5 to R^3, parameters A, b
   # data is 2x5.  A maps from 5 to 3... can we map "data" under A?
   data = torch.randn(2, 5)
   print(lin(data))  # yes

b. TensorFlow

.. code:: bash

   import tensorflow as tf
   from __future__ import print_function
   # bellow two rows are for TF1.x compatibility mode in TF2.x - don't use them with TF1.x
   import tensorflow.compat.v1 as tf
   tf.disable_v2_behavior()
   # Create a Constant op
   # The op is added as a node to the default graph.
   #
   # The value returned by the constructor represents the output
   # of the Constant op.
   hello = tf.constant('Hello, TensorFlow!')
   # Start tf session
   sess = tf.Session()
   # Run the op
   print(sess.run(hello))

c. Caffe 
Test with LSF workload manager; this will run remote in one of the Satori compute nodes available

.. code:: bash


   cd ~/
   conda install keras
   wget https://raw.githubusercontent.com/mit-satori/getting-started/master/lsf-templates/template-caffetest-singlenode.lsf
   bsub < template-caffetest-singlenode.lsf
   bjobs
   bjobs
   bpeek
   bpeek
   bpeek
   bjobs


The template-caffe-test-singlenode.lsf consist in the following LSF file: 

.. code:: bash

   #BSUB -L /bin/bash
   #BSUB -J "caffe-test"
   #BSUB -o "caffe-test_o.%J"
   #BSUB -e "caffe-test_e.%J"
   #BSUB -n 4
   #BSUB -R "span[ptile=4]"
   #BSUB -gpu "num=4"
   #BSUB -q "normal"
   #BSUB -x

   HOME2=/nobackup/users/$(whoami)
   PYTHON_VIRTUAL_ENVIRONMENT=wmlce-1.7.0
   CONDA_ROOT=$HOME2/anaconda3
   source ${CONDA_ROOT}/etc/profile.d/conda.sh
   conda activate $PYTHON_VIRTUAL_ENVIRONMENT

   caffe-test


You can try even your custom ML/DL code; in case you have missing
libraries don’t forget to install them with:

.. code:: bash

   conda instal <package name>
   pip install <package name>

If you don’t have any more errors you are ready to submit jobs on the
compute nodes :)

Controlling WML-CE release packages
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The conda installer uses a set of rules to determine which packages to
install. Channel priorities and package versions are weighted heavily,
but the installer also considers factors such as the number of packages
that would need to be installed, whether any packages would need to be
upgraded or removed, and so on.

The conda installer will sometimes come up with a surprising
installation solution. It may prefer to install: Packages from Anaconda
channels over the WML CE channel in spite of channel priorities.
Packages from an older release of WML CE in spite of newer versions
being available. You can guide the conda installer to ensure that it
chooses the desired WML CE package using the strict channel priority
option and the powerai-release meta-package.

a. Strict channel priority
                          

The strict channel priority option forces the conda installer to give
additional weight to the priority of channels defined in the
configuration. It is useful in cases where the conda installer is
preferring packages from lower-priority channels. The simplest use is
just to add –strict-channel-priority to the install command:

.. code:: bash

   conda install --strict-channel-priority tensorflow

You can check the priority of the channels in the configuration by
running the following:

.. code:: bash

   conda config --show
   ...
   channel_priority: flexible
   channels:
     - https://public.dhe.ibm.com/ibmdl/export/pub/software/server/ibm-ai/conda/
     - defaults
   ...

You could permanently change the channel priority setting to strict:

.. code:: bash

   conda config --set channel_priority strict

b. WML-CE release meta-package
                             

The powerai-release meta-package can be used to specify the WML CE
release you want to install from. It is useful when the installer
prefers packages from an earlier release, or if you intentionally want
to install packages from an older release. Examples:

.. code:: bash

   (my-wmlce-env) $ conda install pytorch powerai-release=1.7.0
   (my-wmlce-env) $ conda install pytorch powerai-release=1.6.2

The –strict-channel-priority option can be used with powerai-release for
greater control:

.. code:: bash

   conda install --strict-channel-priority pytorch powerai-release=1.7.0

Additional conda channels
^^^^^^^^^^^^^^^^^^^^^^^^^

The main WML CE conda channel is described above. That channel includes
the formal, supported WML CE releases.

Additional conda channels are available to complement the main channel.
Packages in these channels are not formally supported. Both of these
channels are optional. WML CE will install and run fine without either.
They can also be used independently of each other (Supplementary does
not need Early Access or vice versa). Use them if you want the packages
they provide and do not need formal support.

The WML CE Supplementary channel is available at: https://anaconda.org/powerai/.
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

This channel includes packages that are not part of WML CE, but which
may be useful to WML CE users. The packages are built from recipes in
the WML CE GitHub repository: https://github.com/ibm/powerai.

Problem reports and recipe contributions from the community are welcome.
More information about the Supplementary channel can be found in the
PowerAI Supplementary Channel README.

The WML-CE Early Access channel is available at: https://public.dhe.ibm.com/ibmdl/export/pub/software/server/ibm-ai/conda-early-access/.
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

This channel is updated occasionally with latest versions of various
packages included in WML CE. The purpose of the channel is to make new
versions of frameworks available in advance of formal WML CE releases.
Packages published in the Early Access channel may not exactly match a
later WML-CE release. For example, package and prerequisite versions may
differ.

Packages in the Early Access channel might depend on packages in the
main channel, so both channels might be needed in the conda config.

Example of getting EA WML-CE software: 


.. code:: bash

   conda config --prepend channels https://public.dhe.ibm.com/ibmdl/export/pub/software/server/ibm-ai/conda-early-access/   
   conda create -n wmlce-ea python=3.7
   conda activate wmlce-ea 
   conda install tensorflow


Alternative:

.. code:: bash

   conda config --prepend channels https://public.dhe.ibm.com/ibmdl/export/pub/software/server/ibm-ai/conda-early-access/   
   conda create -n wmlce-ea python=3.6
   conda activate wmlce-ea
   conda install tensorflow=2.1.0=gpu_py36_914.g4f6e601


To test your TF2 code you can use the this Deep Convolutional Generative Adversarial Network jupyter notebook from this `link <https://github.com/mit-satori/getting-started/blob/master/jupyter-notebooks/TF2.0-DCGAN-Tutorial.ipynb>`__ or go to https://github.com/tensorflow/docs/tree/master/site/en/tutorials 

**Note:** Updated CUDA drivers are only available in Slurm. To begin using Slurm please see **https://mit-satori.github.io/satori-workload-manager-using-slurm.html?highlight=slurm**
