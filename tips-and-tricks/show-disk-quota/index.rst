How can I see disk usage?
=========================

The somewhat obscure commands

.. code:: bash

   /usr/lpp/mmfs/bin/mmlsquota --block-size 1G -u cnh nbusers
   /usr/lpp/mmfs/bin/mmlsquota --block-size 1G -u cnh home
   
show the current disk space and file count use for a particular account (in this case account ``cnh``) on the
scratch file system ( ``nbusers`` ) and on the backed up file system ( ``home`` ). The
account limits for space and file count are also shown. 
Quotas on ``nbusers`` are set relatively low by default. Account holders with a legitimate
need can request increases to their ``nbusers`` quotas by emailing satori-support@techsquare.com.


Where should I put world or project shared datasets?
====================================================

Project directories with group ownership will be coming soon. 
