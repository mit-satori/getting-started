
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
  
* Example job

Below is an example of a job file that Relion should generate. The file you generate should look something
like this. 

   .. code:: bash
 
      #BSUB -L /bin/bash
      #BSUB -J "Relion"
      #BSUB -o "Relion.%J"
      #BSUB -n 40
      #BSUB -R "span[ptile=40]"
      #BSUB -gpu "num=4"
      #BSUB -q "normal"

      export PATH=/nobackup/users/ruzhu/RELION/bin:/nobackup/users/ruzhu/CTFfind4/bin:/opt/ibm/spectrum_mpi/bin:$PATH 
      export LD_LIBRARY_PATH=/nobackup/users/ruzhu/CTFfind4/lib:$LD_LIBRARY_PATH

      #source ~/anaconda3/bin/activate
      #conda activate CryoEM

      unset CUDA_VISIBLE_DEVICES
      export CUDA_VISIBLE_DEVICES="0,1,2,3"
      (time -p mpirun -bind-to none -n 9 relion_refine_mpi --j 4 --gpu --pool 50 --dont_combine_weights_via_disc --scratch_dir /dev/shm  --o Refine3D/job005 --auto_refine --split_random_halves --i squirrel2.star --ref Import/job004/postprocess_refine850_resampled_775.mrc --firstiter_cc --ini_high 40  --ctf --ctf_corrected_ref --particle_diameter 340 --flatten_solvent --zero_mask --oversampling 1 --healpix_order 2 --auto_local_healpix_order 4 --offset_range 5 --offset_step 2 --sym C1 --low_resol_join_halves 40 --norm --scale) >run_21x8.log 2>&1



Other notes
^^^^^^^^^^^
