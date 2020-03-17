
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
       
  7. Use GUI to configure your job.
  
  8. Use "Print" command to print job commands to terminal. 
  
  9. Adjust the relion submission script to incorporate the commands specific to your job. Note: If you have a job that requires a lot of memory, you should alter the ``--n`` and ``--j`` values. Save submission script. Make sure paths for ``--o`` already exist. 
  
  10. Execute job
  
      .. code:: bash
      
        bsub< run
      
  11. Check job is running with bjobs. Submission script generates a log file. 



Other notes
^^^^^^^^^^^
