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


# Mysql 

To make copy of DB:

`mysqldump -h jsubmit.jlab.org -u robertej -p CLAS12OCR | mysql -h jsubmit.jlab.org -u robertej -p clas12Backup`





# Steering Card

Steering card a.k.a. scard contains configuration for running simulation.
There can be scards with different types.
e.g., (type1)[submissionTypes/type1.txt] and (type2)[submissionTypes/type2.txt]


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

Apparently submission from scosg16 do not automatically use JLAB VO but OSG VO whcih will go to the opportunistic pool. 
Only when we go to Glasgow we use the JLAB VO?



# mysql backup:

two steps because we need to enter PW twice

mysqldump -h jsubmit.jlab.org -u ungaro -p CLAS12OCR > d.back

cat d.back | mysql -h jsubmit.jlab.org -u ungaro -p clas12Backup

note: when running python utils/create_database.py the credentials in msqlrw.txt should be the ones of the user that created the DB



add column:

alter table CLAS12OCR.submissions add column run_job_text VARCHAR(15) AFTER clas12_condor_text;




# Scripts to be installed in utils and ran as cronjob (on scosg16.jlab.org):


#~ungaro:  volatileQuery.sh, osgQuery.sh
~ungaro:  osgQuery.sh
~gemc:    gemcSubmitCron.sh priorityCron.sh



# Release Cycle:


- edit indexMaintanance.php with new message, push it to repo 
- run script utils/testRelease to: 
	- clone repos  inside a "test" directories on /group/clas/www/gemc/html and /group/clas12/SubMit/
	- copy indexMaintanance.php to index.php
	- copy stats files into test dir
	- copy permissions files into test dir

- If mysql table are changed:

	- alter table CLAS12OCR.submissions auto_increment = 220;
	- copy users tables into new one 

- change osgQuery or use the test version to pick up the correct directory. Change to: use osgQuery test, or give optional arguments?

7. update permission so gemc can write : stats directories? 
8. update    gemcSubmitCron.sh (or create gemcSubmitCronTest.sh)  	cd /group/clas12/SubMit/test/SubMit/server - or give optional arguments?

- after tests: undo changes to index.php and run updateSubmit.sh. this is the  "official release"
- after user tests: tag the files, and use intallRelease to install it (this will delete everything, make sure stats_results is kept) < don't do this yet






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


* Condor Hold:
condor_hold -constraint 'ClusterId == N && ProcId >= 5000'
You can also "test" the constraint with condor_q first. condor_q and 
condor_hold takes the same -const argument.


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



# October 2019 Notes and TODOs


## Short term todos:

1. remove path and extension from gcard entry in scard. Currently the valid gcards needs argument as well

		gcards: /jlab/clas12Tags/gcards/clas12-default.gcard        
		becomes: 
		gcards: clas12-default        

- web interface gcard name should be username_type#.scard (after submission it can be moved to username_#farmSubmissionID_type#.scard)
- support lund gzipped files (easy, can add a script to unzip anything that has .gz)
- ability to cancel job 
- ability to remove job_out
- ability to add suffix to output dir


## Medium term todos:

- function to return approx location of submission using ip address and display on minimap
- options should be separated and different for client and server, not common in utils?
- show or make available options of the software versions inside the container
- error message if writing scard was not successful
- error if pool node doesn't make sense - in that case do not mark as submitted
- change   scripts_baseDir  = "/group/clas12/SubMit" to relative path 
- remove "#" from the scard in mysql (note: this may break things?)
- add a test for each single generators




## Long term todos:

1. unit tests 
2. python packages installation rather than importing dirs and files within the same project
3. configurations file (currently gcard available files in utils) should be shared between client and web interface
3. send email with farm submission ID, out location. 
4. monitor job statistics (memory, completion, resource usage)


## Completed
- standardize generator options documentation
- log off users after certain amount of time
- the farmSubmissionID and gcard ID should match but sometimes they don't. This should be fixed (or prevented). To temp fix this, change the farmSubmissionID from workbench.
- mechanism to pass the configuration (rga-fall18, etc) to the runscript generators. This is needed to run reconstruction (Mauri)
- fix allowed container gcards, currently only 1 in fs.py (default)  
- replaced the main page sidebar menu
- uniforming style for all php and html 
- collect css style in common between varrious php files  (Sangbaek)
- when "submit" is pressed, add directory output message like: "Output and logs will be at /lustre/expphy/volatile/clas12/osg/<username> at the bottom of the table." 
- limit use to web submission, not using the scripts. Remove any doc references. 
- client: get username from command line if specified
- web submission work w/o generator arguments
- add farm statistic scripts to repo  (Sangbaek)
- type 2 working.
- add handling scard entries:
	generated:          # yes/no choice
	gemc evio:          # yes/no choice
	gemc decoded   # yes/no choice
	reconstructed     # yes/no choice
	dst: yes               # yes/no choice
- "total number of events" validated be a number, and formatted 
- client submit agnostic to scard content, just scard type (David)
- Catch IP address 
- add entry or table to store either ip address or location (or both)
- remove need to write scripts (-w option is currently necessary)
- remove domain_name, submissions and total events from user table
- remove run_job_text from submissions table
- Have Submit returns the farmSubmissionID so it can be picked up by the web interface, display feedback message



# Supporting Packages:

- there is no need to convert hipo files to root to do analysis in c++/root;
- supporting multiple formats for the same files is a waste of resources we cannot afford;
- for simulation, we encourage the physics WG to organize production campaigns to produce 
   significant statistics that could be shared among analyzers and for that we recommend saving hipo DSTs;
- conversion to other formats is possible even if not encouraged but cannot be supported with centralized resources;
- in case collaborators feels the conversion to other formats is necessary for their analysis, we strongly recommend 
   the groups interested in similar file formats to discuss and agree on the tool to use for the conversion  
   and the structure of the converted files to avoid further duplication.
