Copying larger files and large file sets
----------------------------------------

 
cp command is a single threaded process and it does not take advantage of the distributed file systems like GPFS.  If you have a lot of files to copy, or you have very large files, you may want to use  one of the large data set copy fasciliities supporte4d by Satori. The two methods are:

* msrsync (https://github.com/jbd/msrsync) whichis a python wrapper around rsync. 
* Aspera (https://www.ibm.com/products/aspera) which is a high speed data transfer service offered by IBM
 
Using mrsync
------------
https://github.com/jbd/msrsync
 
To install

.. code:: bash

  wget https://raw.githubusercontent.com/jbd/msrsync/master/msrsync && chmod +x msrsync

To use 

.. code:: bash

  $ msrsync --help
    usage: msrsync [options] [--rsync "rsync-options-string"] SRCDIR [SRCDIR2...] DESTDIR
  or: msrsync --selftest
  msrsync options:
    -p, --processes ...   number of rsync processes to use [1]
    -f, --files ...       limit buckets to <files> files number [1000]
    -s, --size ...        limit partitions to BYTES size (1024 suffixes: K, M, G, T, P, E, Z, Y) [1G]
    -b, --buckets ...     where to put the buckets files (default: auto temporary directory)
    -k, --keep            do not remove buckets directory at the end
    -j, --show            show bucket directory
    -P, --progress        show progress
    --stats               show additional stats
    -d, --dry-run         do not run rsync processes
    -v, --version         print version
   rsync options:
    -r, --rsync ...       MUST be last option. rsync options as a quoted string ["-aS --numeric-ids"]. The "--from0 --files-from=... --quiet --verbose --stats --log-file=..." options will ALWAYS be added, no
                            matter what. Be aware that this will affect all rsync *from/filter files if you want to use them. See rsync(1) manpage for details.
   self-test options:
    -t, --selftest        run the integrated unit and functional tests
    -e, --bench           run benchmarks
    -g, --benchshm        run benchmarks in /dev/shm or the directory in $SHM environment variable                        
 
Example:  

.. code:: bash

  [root@service0001 kvtran-test]# ./msrsync -p 24 --stats --rsync "-a --inplace" /data/ImageNet/ILSVRC2012 ILSVRC2012-service0001
  Status: SUCCESS
  Working directory: /nobackup/projects/kvtran-test
  Command line: ./msrsync -p 24 --stats --rsync -a --inplace /data/ImageNet/ILSVRC2012 ILSVRC2012-service0001
  Total size: 143.1 G
  Total entries: 1331169
  Buckets number: 1332
  Mean entries per bucket: 999
  Mean size per bucket: 110.0 M
  Entries per second: 3136
  Speed: 345.2 M/s
  Rsync workers: 24
  Total rsync's processes (1332) cumulative runtime: 10116.8s
  Crawl time: 212.5s (50.1% of total runtime)
  Total time: 424.5s
 
Note:
--inplace: rsync writes updated data directly to a file, instead of making a copy and moving it into place.

We used 24 workers in this example because I found out that gave me the best time when I tested with the GPFS filesystem in POK environment.
 
The copy time is 424.5s using msrsync versus 27m34.417s with cp command.
 
For remove the directory,weI used rsync and it took 8 minutes to remove a directory with 1000 subdirectories and  total of 1281167 files on POK env.
 
We created an empty directory and used it with rsync --delete as the example below:

.. code:: bash

   -bash-4.2$ time rsync -a --delete empty-dir/ train2-24/
   real    8m25.144s
   user    0m2.756s
   sys     0m35.559s
 


Using Aspera for remote file transfer to Satori cluster
-------------------------------------------------------

Note: Aspera server is enabled on either satori-login-001.mit.edu or satori-login-002.mit.edu login nodes.

1. Install Aspera client or connect on your local machine

   * For Mac, Windows or Linux-x86, please download and install Aspera connect from https://downloads.asperasoft.com/en/downloads/8?list  
      To install on Mac or Windows, please following the software installation step to complete the installation. For Linux as non-root user, untar the gz file and run the install script file. After the installation is complete, it will be located at your home directory .aspera/connect

   * For PowerPC (ppc64le) system, please download and install Aspera desktop client from https://downloads.asperasoft.com/en/downloadsArchive/2 
      To install desktop client on Power system after download deb or rpm file:
   
   * on an Ubuntu system
      
.. code:: bash
      
      sudo apt-get install ibm-aspera-desktopclient-3.9.6.176567-linux-ppc64le.deb
      
      * RHEL|CentOS
      
.. code:: bash
      
      sudo yum install ibm-aspera-desktopclient-3.9.6.176567-linux-ppc64le.rpm

2. Transfer files using Aspera connect or client
Note: <asperauser> is your userid on Satori

   * on Windows
   
.. code:: bash 
   
      ascp.exe –P 22 –mode=send|recv  \path\to\file.txt  <asperauser>@satori-login-002.mit.edu:/path/to/file.txt

   * on Mac or Linux

.. code:: bash 
   
      ascp –P 22 –mode=send|recv  /path/to/file.txt  <asperauser>@satori-login-002.mit.edu:/path/to/file.txt
   
      The path to ascp command is located at $HOME/.aspera/connect/bin 
      
      Here's an estimator for Aspera transfer times https://www.ibm.com/aspera/file-transfer-calculator/

For technical support, Support <satori-support@techsquare.com>
