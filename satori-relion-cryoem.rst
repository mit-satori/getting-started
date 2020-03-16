
Relion Cryoem for Satorians
===========================
Quick start
^^^^^^^^^^^

* Transfer data

Data should be transferred to: 
/nobackup/users/``your_kerbos_id`` .
Use satori-login-002.mit.edu 

* To access the Relion GUI

  1. Login: 
  
     .. code:: bash
     
       ssh -Y username@satori-login-002.mit.edu
    
  
  2. Submit a fake job to access a node: 
  
     .. code:: bash
     
       bsub -x -q normalx -gpu "num=1" sleep 7200
    



Other notes
^^^^^^^^^^^
