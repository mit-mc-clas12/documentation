# Submitting a job

The script  `Submit_Job.py [options] [scardfile]` is used to submit jobs to various offsite farms to run the CLAS12 simulations. Run `Submit_Job.py -h` to see options that can be passed. 
Important options:


* `-d 1` will turn on some debugging messages, `-d 2` will turn on all debugging messages. 
* `-s` will submit the job(s)  
* `-t` is useful for testing on local machines that do not run HTCondor or Slurm; it simulates a job submission
* `-b #` can be used to specify a specific batch number, e.g. `-b 2` will submit scripts from batch #2
* `-w` will write submission scripts to disk
* `-l` routes to a local SQLite Database, otherwise it will default to look for the jsubmit MySQL database
*  `scardfile` : location of the steering card


# Steering Card




# Workflow Test


Create a dir and clone the repoos:
* mkdir clas12-test      
* cd clas12-test   
* git clone https://github.com/mit-mc-clas12/client  
* git clone https://github.com/mit-mc-clas12/server                
* git clone https://github.com/mit-mc-clas12/utils       

Create the DB, client submit one job and server reads the unsubmitted jobs, then submit it:

* python utils/create_database.py -d 1      
* python client/src/SubMit.py -d 1                                                                 
* python server/src/Submit_batch.py -s -d 1  



# SQL Database Schema

[Schema PDF File](CLAS12_Simulations_DB.pdf)
[db diagram link](https://dbdiagram.io/d/5c9b829bf7c5bb70c72f6c34)


# Singularity Image

* cvmfs path: ```/cvmfs/singularity.opensciencegrid.org/jeffersonlab/clas12simulations:production```

To run the singularity image:
```
singularity shell --home ${PWD}:/srv --pwd /srv --bind /cvmfs --contain --ipc --pid /cvmfs/singularity.opensciencegrid.org/jeffersonlab/clas12simulations:production
```

# Open Science Grid
[HomePage](https://support.opensciencegrid.org/support/home) [Grid Accounting](https://gracc.opensciencegrid.org/dashboard/db/gracc-home) 

# HT Condor

[Homepage](https://research.cs.wisc.edu/htcondor/index.html) [Manual](http://research.cs.wisc.edu/htcondor/manual/v8.8/contentsname.html) [HowTos](https://htcondor-wiki.cs.wisc.edu/index.cgi/wiki?p=HowToAdminRecipes) [Description and Examples](http://vivaldi.ll.iac.es/sieinvens/siepedia/pmwiki.php?n=HOWTOs.CondorSubmitFile)

Useful commands:
---------------------

To submit a job:

* condor_submit file.condor

Query jobs:

* condor_q
* condor_q -goodput 
* condor_q -better-analyze JOBID
* condor_q  -hold

User History:

* condor_history mauri
* condor_history -long mauri

Useful Commands:
* condor_rm ID

# How to make SubMit.py run from anywhere on a machine:


1. `export PATH=$PATH:/path/to/clas-12-simulations-repo/client/src`
2. `export PYTHONPATH=/path/to/clas-12-simulations-repo` #this allows all SubMit.py to import all needed modules


---------------------

To change file format (ff)
----------------------------

If files are edited on a windows machine, the ff is ```\r\n```. To change it to the unix ff ```\n``` one can use vi:

```:set ff=unix```



