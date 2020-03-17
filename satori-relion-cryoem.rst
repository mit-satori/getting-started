
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
     
       bsub -q normal sleep 7200
       
  3. Use bjobs to determine the node of the job ie:
     
     .. code:: bash
     
       JOBID   USER    STAT  QUEUE      FROM_HOST   EXEC_HOST   JOB_NAME   SUBMIT_TIME
       45954   seyvos  RUN   normal     service0002 node0028.in sleep 7200 Mar 10 13:50
  
  4. Login into the node:
  
     .. code:: bash
     
       ssh -X node0028 
       
  5. Add paths to access relion libraries:
  
     .. code:: bash
     
       export PATH=/nobackup/users/ruzhu/RELION/bin:/nobackup/users/ruzhu/CTFfind4/bin:/opt/ibm/spectrum_mpi/bin:$PATH
       export LD_LIBRARY_PATH=/opt/ibm/spectrum_mpi/profilesupport/lib:/nobackup/users/ruzhu/CTFfind4/lib:$LD_LIBRARY_PATH
       
  6. Open Relion by typing: 
  
    .. code:: bash   
    
       relion



Other notes
^^^^^^^^^^^
