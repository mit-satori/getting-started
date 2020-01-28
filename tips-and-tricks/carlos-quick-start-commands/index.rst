
Go to some /nobackup/user/UUUU directory::

   wget https://repo.anaconda.com/archive/Anaconda3-2019.10-Linux-ppc64le.sh
   sh Anaconda3-2019.10-Linux-ppc64le.sh -f -p /nobackup/users/$(whoami)/anaconda3
   source ~/.bashrc
   conda config --prepend channels https://public.dhe.ibm.com/ibmdl/export/pub/software/server/ibm-ai/conda/
   conda create --name wmlce-1.6.2 python=3.6
   conda activate wmlce-1.6.2
   conda install powerai
   conda install -c conda-forge jupyterlab


Once the environment is setup.  You can run LSF interactive with::

 
    bsub -W 3:00 -q normal -gpu “num=4” -R “select[type=any]” -Ip bash
    conda activate wmlce-1.6.2
