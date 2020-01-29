What Linux distribution version am I running?
=============================================

The following commands can be used: 

.. code:: bash

   cat /etc/redhat-release
   
or

.. code:: bash

  lsb_release -a

What Linux kernel level am I running?
=====================================

The following command works: 

.. code:: bash

   uname -r
   
What software levels are installed on the system?
=================================================

The Module utility is used to install packages on the system. The following will give you a list of available software:

.. code:: bash

   module spider
   
If you want to see information about your conda environment, you can issue:
 
.. code:: bash

  conda info
   
and to see the packages installed, issue:
 
.. code:: bash

  conda list
   
 
