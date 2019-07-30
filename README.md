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

[Homepage](https://research.cs.wisc.edu/htcondor/index.html) [Manual](https://research.cs.wisc.edu/htcondor/manual/) [HowTos](https://htcondor-wiki.cs.wisc.edu/index.cgi/wiki?p=HowToAdminRecipes) [Description and Examples](http://vivaldi.ll.iac.es/sieinvens/siepedia/pmwiki.php?n=HOWTOs.CondorSubmitFile)

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



# MysqlDB on Mac

This package will be dropped in python 3 and higher.
pip cannot install it anymore.
Notice that on Mac:

locate mysqlclient
/usr/local/Cellar/mysql/8.0.16/lib/libmysqlclient.21.dylib
/usr/local/Cellar/mysql@5.7/5.7.25/lib/libmysqlclient.20.dylib


This worked for me on Mojave: 

https://stackoverflow.com/questions/1448429/how-to-install-mysqldb-python-data-access-library-to-mysql-on-mac-os-x


I had to make that link (in /mysql@5.7) and manually remove ssl from the compilation directly. After that sudo python setup.py install worked.
