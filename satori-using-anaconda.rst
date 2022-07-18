Using Anaconda on Satori
========================

[1] Using Anaconda
''''''''''''''''''

Satori uses Environment Modules to allow users to have access to different software. These modulefiles are built for Satori's architecture and can be loaded/unloaded from one's environment on the fly. Anaconda can be accessed by searching for and loading the appropriate module.

To display a list of modulefiles matching the name 'anaconda':

.. code:: bash

  module avail anaconda
  
Loading one of the anaconda modules:

.. code:: bash

  module load anaconda3/2020.02-2ks5tch


[2] Creating and activating conda environments
''''''''''''''''''''''''''''''''''''''''''''''

Once an anaconda module is loaded, you can create environments that have different versions of Python or packages installed in them. Conda environments are optional but recommended. If not used, packages are installed in the default environment called base, which often has a higher risk of containing conflicting packages or dependencies. Switching between environments is called activating the environment.

The syntax for creating and activating an environment is:

.. code:: bash

   conda create --name <environment name> python=<python version>
   source activate <environment name>


To create an anaconda environment in a specfic location, use the `--prefix` flag during creation with the absolute path and name of the environment:

.. code:: bash
   
   conda create --prefix=<path/to/env>
   
Example:

.. code:: bash

   conda create --prefix=/nobackup/users/ageod/.conda/envs/test-env01
   source activate /nobackup/users/ageod/.conda/envs/test-env01
   
NOTE: As a good practice to maintain the disk space usage on Satori please run at regular intervals the following command that will remove unused packages and caches from your Anaconda profile. In addition this command shuld be use before installing a new version of WML-CE/Open-CE on another virtual environment.

.. code:: bash

   conda clean --all
   
   
[3] Setting up conda channels
'''''''''''''''''''''''''''''

When using conda to search for packages, one or more "channels", or repositories, may be queried. By default, conda will search it's default packages channel for any results that match the package name searched for matching the systems architecture (ppc64le). If no packages are available either under the queried name, or for host system's architecture, no results will be displayed. Note that not all packages existing for x86 can also be found for ppc64le.

To add the IBM WML-CE channel to the conda configuration:

.. code:: bash

  conda config --prepend channels \
  https://public.dhe.ibm.com/ibmdl/export/pub/software/server/ibm-ai/conda/

To add the MIT Open-CE channel to the conda configuration:

.. code:: bash

  conda config --prepend channels \
  https://opence.mit.edu

There are a number of other conda channels that exist which can be added to the conda config in the same manner. By adding the two channels above, your conda searches will be expanded beyond the default repository. See the section entitled `IBM Watson Machine Learning Community Edition (WML-CE) and Open Cognitive Environment (Open-CE) <https://mit-satori.github.io/satori-ai-frameworks.html#ibm-watson-machine-learning-community-edition-wml-ce-and-open-cognitive-environment-open-ce>`_ for more information about these two channels.


[4] Searching for and installing conda packages
'''''''''''''''''''''''''''''''''''''''''''''''

The basic syntax for searching for a package through conda is as follow:

.. code:: bash

  conda search <package-name>
  
This command will search the base anaconda channel along with any channels currently added to your conda configuration. You can search a specific channel by explicitly including it in the search:

.. code:: bash

  conda search -c <channel> <package>
  
Example:

.. code:: bash

  conda search -c conda-forge pytorch
 
Results from conda searches typically include several available versions for the package queried. If no particular version of a package is specified, conda will install [the most appropriate version based on the version of python installed locally]. Conda search results may look similar to this:

.. code:: bash

  (test2022-14) [ageod@satori-login-001 envs]$ conda search pytorch
  Loading channels: done
  # Name                       Version           Build  Channel             
  pytorch                        1.7.1      hca541ab_1  None                
  pytorch                        1.9.0 cuda10.2_py37_1  None                
  pytorch                        1.9.0 cuda10.2_py38_1  None                
  pytorch                        1.9.0 cuda10.2_py39_1  None                
  pytorch                       1.10.2 cpu_py310hef0c51e_0  pkgs/main           
  pytorch                       1.10.2 cpu_py37h6f0ae12_0  pkgs/main           
  pytorch                       1.10.2 cpu_py38h6f0ae12_0  pkgs/main           
  pytorch                       1.10.2 cpu_py39h6f0ae12_0  pkgs/main
  
The output lists the name of the package relevant to the query, the version available, its build, and what channel it exists on. There are several versions of pytorch in the example that show they were built with cuda support, the specific version of cuda used, and the version of python used. There are also versions of pytorch built to utilize CPU's rather than GPU's. 

The "pkgs/main" channel refers to the default anaconda repository, whereas the results with the channels listed as "None" are coming from the MIT OpenCE channel.

To install a package, you must first need to have an environment activated. Again, packages are installed within conda environments. You can specify versions of packages through levels of specificity:

.. code:: bash

  conda install <package>
  
or

.. code:: bash

  conda install <package>=<version>
  conda install <package>=<version>=<build>
  
Example:

.. code:: bash

  conda install pytorch
  conda install pytorch=1.9.0
  conda install pytorch=1.9.0=cuda10.2_py39_1
  

[5] Listing the contents of your conda environment
''''''''''''''''''''''''''''''''''''''''''''''''''

Viewing the packages installed in your environment is easy. Use the following command:

.. code:: bash

  conda list
  
You can be more granular and search for a specific package in your environment by adding the packages name after the above command.


[6] Leaving your conda environment
''''''''''''''''''''''''''''''''''

When you are done working in your environment, or want to switch to a different environment, you can deactivate the current environment with:

.. code:: bash

  conda deactivate
  
Environments retain their state when inactive - as in, the packages installed within them. Simply activate the envionment again to use the installed software.

