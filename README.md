
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

To run the singularity image:
```shell
singularity shell --home ${PWD}:/srv --pwd /srv --bind /cvmfs --contain --ipc --pid /cvmfs/singularity.opensciencegrid.org/jeffersonlab/clas12simulations:production
```


# HT Condor

Useful commands:
---------------------

To submit a job:

* condor_submit file.condor

Query jobs:

* condor_q
* condor_q -goodput 
* condor_q -better-analyze JOBID

User History:

* condor_history mauri
* condor_history -long mauri

