Starting up on Satori
=====================

Getting Your Account
^^^^^^^^^^^^^^^^^^^^
Satori is available to all MIT students and staff with an active Kerberos ID. The first step to using Satori
is to activate your account. To do that, go to the portal page (https://satori-portal.mit.edu/). Click the 
"Authenticate with Globus" button, and you'll be asked to select an organization. Select
"Massachusetts Institute of Technology" and click "Continue". You'll be taken to your MIT Kerberos login
page, where you should log in. Follow the prompts (you'll be asked if you want to associate with an existing
Globus login, if you don't use Globus you can ignore this and press left button) and follow the instructions
on the screen. Your account should be activated at this point.

Shared HPC Clusters
^^^^^^^^^^^^^^^^^^^
Satori is an HPC-style shared cluster. You are sharing this resources with a number of other researchers,
staff, and students so it is important that you read this page and use the system as intended.

Being a cluster, there are several machines connected together with a network. We refer to these as **nodes**.
Most nodes in the cluster are referred to as **compute nodes**, this is where the computation is done on the
system (where you will run your code). When you log into the system at the command line you are on a special
purpose node called the **login node**, or the **head node**. The login node, as its name suggests, is where
you log in and is for editing code and files, installing packages and software, downloading data, and starting
jobs to run your code on one of the compute nodes.

Each job is started using a piece of software called the scheduler, which is a resource manager for the system.
You let it know what resources you need and what you want to run, and the scheduler will find those resources and
start your job on them. When your job completes those resources are relinquished. The scheduler is what ensures
that no two jobs are using the same resources, so it is very important not to run anything unless it is submitted
properly through the scheduler. You should not run anything on the login/head node.

Logging in to Satori
^^^^^^^^^^^^^^^^^^^^
There are two ways to log into and use Satori. The first is through the portal, where you can view the files
in your home directory, start a shell, and use Jupyter Lab. The second is through ssh. Take a look at the
`Satori Login <https://mit-satori.github.io/satori-ssh.html>`__ page for more information.

The Satori Portal
^^^^^^^^^^^^^^^^^
Once you've logged into the portal, you'll see a few tabs at the top of the page. You can:

* **View the Files in your Home Directory:** Click on Files -> Home Directory. From here you can download and upload files. Also can also drag and drop files from your computer into this window to transfer them to the system.

* **View your Active Jobs:** Click on Jobs -> Active Jobs. Here you can see a list of all of your currently running jobs. You can click on the arrow to the left of each line to see more information about the job.

* **Start a Shell:** Click on Clusters -> Satori Shell Access. This will open a shell in a new browser tab that will be on the login node. From here you can submit a batch job or start an interactive session to run your code.

* **Start a Jupyter Notebook:** Click on Interactive Apps -> Jupyter Notebook. From here you can create a new Jupyter Lab instance. The `Satori Login <https://mit-satori.github.io/satori-ssh.html>`__ page describes which  options you should choose when starting Jupyter Lab.

If you haven't used an HPC system before, you may want to start by using Jupyter Lab in the portal. If you select
the options described on the `Satori Login <https://mit-satori.github.io/satori-ssh.html>`__ page your environment
should be set up to run many of the popular machine learning frameworks in the Jupyter Notebooks.

Setting up Your Environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^
There are two ways we manage software and environemnts in Satori. The first is conda, which is the general package
manager for the second. The second are environment modules.

1. **Environment Modules**: Environment modules are pre-packaged environments for software, tools, and libraries. It allows you to load and unload these as you go. It's always good to check to see if a module exists for a tool before installing it yourself. To see the full set of modules available, first load the spack module (`module load spack`), then run the `module avail` command. A few common module commands are:

* `module avail`: Shows the available modules

* `module load MODULENAME`: Load the module MODULENAME

* `module list`: List the modules that you have loaded

* `module rm MODULENAME` or `module unload MODULENAME`: Unload the module MODULENAME

* `module switch MODULE1 MODULE2`: Unload MODULE1 and load MODULE2

2. **Conda**: Conda is the package manager for the system. The thing to know when you create your conda
environment is that you want to make sure you are getting your packages from the right channel. We have
in depth instructions on how to properly set up a conda environment on the `WMLCE <https://mit-satori.github.io/satori-ai-frameworks.html>`__
page. Steps 1-3 will show you how to create and properly set up a conda environment

Transferring Files
^^^^^^^^^^^^^^^^^^




