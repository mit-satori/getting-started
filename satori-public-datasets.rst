Satori Public Datasets
============


Satori use a dedicated partition of most used DataSets for machine learnig and deep learning. Thouse datasets are stored in /data partition of the file system
As of today only ImageNet (ILSVRC2012) is published.

.. code:: bash

   ls /data
   ls /data/ImageNet
   
   
ImageNet dataset is stored in two forms:
- original files, not processed as .tar files in the /data/ImageNet/archive folder
- processed dataset, in the /data/ImageNet/ILSVRC2012. In this folder you will find the images for training (train folder) and validation (val folder)

NOTE: Over next weeks HDF5 and TFrecords formats for ImageNet will be added.
      If you want other public datasets with large general usage and interest please get in contact with the support team.
