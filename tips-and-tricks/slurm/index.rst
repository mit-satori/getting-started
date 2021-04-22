How do I see my jobs priority ranking?
======================================

Jobs are scheduled by Slurm using a priority ordering that takes into account
available resources, resources that an account has used to date relative to other
accounts, requested resources. 

You can query the priority ranking of pending jobs using the command

sprio -l
