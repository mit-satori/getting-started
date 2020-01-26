Creating your own Jupyter kernels for OOD portal
++++++++++++++++++++++++++++++++++++++++++++++++

The OOD portal has a set of centrally installed Jupyter kernels that
include common software. The software is supposed to include most
things needed for frequently used tools. Sometimes you will come across
packages that are missing and see a message like this image

.. image:: module-not-found.png
  :width: 400
  :alt: Module not found message

When this happens it is possible to create a custom kernel yourself
that will be included in the OOD menu for your account. That kernel can
include any available software you want. You can also request that the
module be added to a standard environment or that a new standard environment
be added, however, sometimes that requires waiting or the module may conflict 
with something else. 

By learning how to add your own kernels you can avoid having to wait, work around
any incompatabilities. You can also gather customized, versioned sets of tools into 
a standard kernel that can be shared within a group working on a common project, or
that you can request be added for your group to a standard set. 

Steps to create kernel
======================

These instructions assume you are slightly familiar with conda ( https://docs.conda.io/en/latest/index.html ) and that you are
starting a brand new conda environment for the kernel. 

