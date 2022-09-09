Satori Basics
=============

What is Satori?
^^^^^^^^^^^^^^^

Satori is a GPU dense, high-performance Power 9 system developed as a collaboration between MIT and IBM.
It has 64 1TB memory Power 9 nodes. Each node hosts four NVidia V100 32GB memory GPU cards. Within a 
node GPUs are linked by an NVLink2 network that supports nearly 200GB/s bi-directional transfer
between GPUs. A 100Gb/s Infiniband network with microsecond user space latency connects the cluster 
nodes togehter. 

How can I get an account?
^^^^^^^^^^^^^^^^^^^^^^^^^

Access requires active MIT Kerberos credentials (available to all students, employees and sponsored guest accounts https://ist.mit.edu/guest-accounts ). To have an account created on Satori please visit the Satori portal (https://satori-portal.mit.edu/) and log in using MIT Kerberos credentials.

New user accounts are granted storage in two locations with the following storage (block) and file (inode) quotas:

* /home/<user> | 20GB & 500,000 files
* /nobackup/users/<user> | 500GB & 1,000,000 files 

Getting help?
^^^^^^^^^^^^^

If you are stuck and can't find an answer in the web pages please feel free to
email our support address satori-support@techsquare.com with questions and requests. 
