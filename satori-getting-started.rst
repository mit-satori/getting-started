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

.. code:: bash

  module avail # Shows the available modules
  module load MODULENAME # Load the module MODULENAME
  module list # List the modules that you have loaded
  module rm MODULENAME # Unload the module MODULENAME
  module unload MODULENAME # Unload the module MODULENAME
  module switch MODULE1 MODULE2 # Unload MODULE1 and load MODULE2

2. **Conda**: Conda is the package manager for the system. The thing to know when you create your conda environment is that you want to make sure you are getting your packages from the right channel. We have in depth instructions on how to properly set up a conda environment on the `WMLCE <https://mit-satori.github.io/satori-ai-frameworks.html>`__ page. Steps 1-3 will show you how to create and properly set up a conda environment, Step 4 will set up the machine learning frameworks.

Transferring Files
^^^^^^^^^^^^^^^^^^
There are multiple ways to transfer files:

* At the command line using scp or rsync

* On the Satori Portal using the File explorer

* Using other tools like `WinSCP <https://winscp.net/eng/download.php>`__, `Cyberduck <https://cyberduck.io/download/>`__, etc

Using scp or rysnc
------------------
Both scp and rsync are command line tools that you can use to transfer files to and from remote systems
like Satori. They both work like cp, but you have to specify the hostname of the remote system. You'll 
need to know the path to the file or directory that you want to transfer and the path to the location where
you want to transfer the file or directory. To transfer a file from your computer to Satori, for example:

.. code:: bash

   scp <local-file.py> your_username@satori-login-001.mit.edu:/path/to/location/

To transfer a file from Satori you'd do a similar thing, but swap the arguments. For example, to transfer
to the current directory:

.. code:: bash

   scp your_username@satori-login-001.mit.edu:/path/to/file/<remote-file.py> .

To transfer a directory use the -r (recursive) flag as you would with cp and other Linux commands.

The rysnc command works similarly, but has more options. For example, you can run it with the -u (update)
option, which will only transfer files that have not already been transferred.

Satori Portal File Explorer
---------------------------

Using the File Explorer on the Satori Portal you can navigate, view, and transfer files. To get to the File
Explorer, log into the Satori Portal and click Files -> Home Directory. From here you can click use the
Upload and Download buttons to move your files. You can also drag and drop files from your computer into
the File Explorer window to transfer them to the system.

Types of Jobs
^^^^^^^^^^^^^
There a couple of different ways you can run on Satori. We've already mentioned that you can start up
Jupyter Lab from the Satori Portal and run Jupyter Notebooks there. You can also open a terminal in Jupyter
and run at the command line there. This is a great place to start if you are more familiar with Jupyter.
Outisde of Jupyter, there are two other main ways to run your code, the first are **interactive jobs**, the second
are **batch jobs**.

Running Interactive Jobs
------------------------
In an interactive job you are running at the command line on a compute node. You can start up a Julia or Python
shell and run interactively there, or you can run scripts or programs at the command line. Interactive sessions
won't end until you exit them or you come to the end of your time limit. Interactive jobs are very good for
testing, debugging, figuring out your workflow, or interactive data exploration. When you first port your code
to Satori, it can be helpful to first run it in an interactive session, so you can iteratively make edits and
rerun your code, without waiting for new resources each time. However, once you have a script that you are fairly
confident will run without need for intervention, it's time to move on to a batch job.

Visit the `Interactive Jobs <https://mit-satori.github.io/satori-workload-manager-using-lsf.html#interactive-jobs>`__
section to see how to start an interactive session using the exisitng LSF workload manager.

**Note** Satori is in the process of being migrated to the SLURM Workload manager. Visit the `Interactive Jobs <https://mit-satori.github.io/satori-workload-manager-using-slurm.html#interactive-jobs>`__
section to see how to start an interactive session using the new  SLURM workload manager. 
**Note:** More recent CUDA Drivers are only available on Slurm.


Running Batch Jobs
------------------
Batch jobs are the most common type of job on HPC clusters. You have a script or executable you'd like to run and
you submit that to the scheduler using a submission or batch script. From there, the scheduler will decide when
and where to run your script, and when your script comes to completion or runs into an error the job will end.

Visit the `Batch Jobs <https://mit-satori.github.io/satori-workload-manager.html#batch-scripts>`__
section to see how to submit a batch job using the existing LSF workload manager , as well as some sample submission scripts and other information on monitoring batch jobs.

**Note** Satori is in the process of being migrated to the SLURM Workload manager. Visit the `Batch Jobs <https://mit-satori.github.io/satori-workload-manager-using-slurm.html#batch-scripts>`__
section to see how to submit a batch job using the new SLURM workload manager , as well as some sample submission scripts and other information on monitoring batch jobs.


One of the most common questions on batch jobs is how to know what is going on with them, how does the user
know if there is an error or monitor the output of the job. In your submission script you specify where you
want the errors and output for your job. Print statements in your code can be very useful for debugging or
for quickly checking the progress of a job. One handy command for watching these files is "tail" with the "-f"
option. This command will print the last few lines of a file, and with "-f" will wait and print new lines as
they are written to the file. For example:

.. code:: bash

   tail -f my_job.log-12345

Will print the output of the log file my_job.log-12345, where 12345 is the Job ID for this particular job. To
exit the "following" mode, press Ctrl+C.

Project Groups
^^^^^^^^^^^^^^

Project groups can be created for sets of accounts that wish to share storage or make use of a priority partition. Groups can be created in MIT WebMoira. Once a group is created please email
the system administration team to request the group be activated on Satori. The WebMoira interface can then be used to manage group membership.

Creating project groups
-----------------------
To create a project group access the MIT WebMoira interface here

`https://groups.mit.edu/webmoira/ <https://groups.mit.edu/webmoira/>`__

and select "Create a new list". Follow the steps on the web pages, selecting the "Moira" 
group, "Active", "Moira Group", "NFS Group" and "Public Options". In the group page make sure the group settings
appear as

.. image:: MoiraSettings.png
   :width: 200

Once the group is created notify the systems administration team that you wish the group to be activated on Satori. The WebMoira interface can
then be used to manage group membership. 


