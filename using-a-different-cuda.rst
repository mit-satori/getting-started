Using a Different Level of CUDA
=========================
Rationale
^^^^^^^^^
* Sometimes you are bringing something to the grid that requires an older (or newer) level of CUDA
* The Admin team has created some modules to enable this functionality, these are for CUDA 9.0, 9.2, and 10.0

Fast start
^^^^^^^^^^
* To load the alternate CUDAs load the following modules **within the context of your job**
* CUDA 9.0

.. code:: bash

  module add cuda/9.0

* CUDA 9.2

.. code:: bash

  module add cuda/9.2
* CUDA 10.0

.. code:: bash

  module add cuda/10.0

Other notes
^^^^^^^^^^
* To do this in the context of an LSF job - include the load module snippet in your .lsf script

Have fun!
