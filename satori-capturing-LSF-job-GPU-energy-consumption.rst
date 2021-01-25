
Capturing the GPU energy for an LSF Job
---------------------------------------

LSF doesn't currently caputure the power utlization of GPUs for jobs.  However, we can use LSF's [pre- and post-execution 
processing](https://www.ibm.com/support/knowledgecenter/SSWRJV_10.1.0/lsf_admin/chap_pre_post_exec_lsf_admin.html) at 
individual job or configuured as part of a [LSF queue](https://www.ibm.com/support/knowledgecenter/SSWRJV_10.1.0/lsf_admin/chap_pre_post_exec_lsf_admin.html) 
to run a script to capture the power utilization using the NVIDIA `nvidia-smi` utility.


This implementation works for single node batch or interactive jobs.

The [nvidia-smi utility] has several options one of which is to run a backgroup process to capture the power utilization of 
the GPUs to a file.  Issue the following command `nvidia-smi -h` to see all the options available.  We found that using 
`nvidia-smi dmon` option, keeps the LSF job alive even after the batch job completes or when the interact shell exits.  
The pre-execution processing script below first calls the `nvidia-smi dmon` command and then executes another bash script
that watches for the pid of the batch or interactive shell to exit.  The `watch.sh` is listed below the `pre-exec.sh` 
script.

`pre-exec.sh` script:

.. code:: bash
    #!/bin/bash

    su $LSFUSER -c "nvidia-smi dmon -i 0 -s p -o DT -f /nobackup/users/$LSFUSER/power.$LSB_JOBID.$LSB_JOBINDEX.txt 2>&1 >/dev/null < /dev/zero &"
    su $LSFUSER -c "/nobackup/users/$LSFUSER/watch.sh $LSB_JOBID &"



``watch.sh`` script:

.. code:: bash
    #!/bin/bash
    echo "Watch is running" >> /nobackup/users/$LSFUSER/watch.txt

    sleep 20
    jobid=$1

    echo $(bjobs -l $jobid | awk '/PIDs/ {print $NF}') >> /nobackup/users/$LSFUSER/watch.txt
    echo $(bjobs -l $jobid | awk '/PIDs/ {print $NF}' | tail -n 1) >> /nobackup/users/$LSFUSER/watch.txt

    watchpid=$(bjobs -l $jobid | awk '/PIDs/ {print $NF}' | tail -n 1)
    killpid=$(ps uxwww | grep nvidia | grep -v grep | awk '{print $2}')

    echo "jobid: $jobid, watchpid: $watchpid, killpid: $killpid" >> /nobackup/users/$LSFUSER/watch.txt

    while sleep 1; do kill -0 $watchpid || break; done

    echo "Done waiting" >> /nobackup/users/$LSFUSER/watch.txt
    kill -2 $killpid
    exit 0


