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


# Exit codes:

202 for cvmfs fail load setup
203 generator fail
204 gemc fail
205 evio2hipo fail
206 (if present) bg-merger fail
207 coatjava fail
208 hipo-utils (dst creation) fail
210 background merging file does not exist
211 "ls" check fail
212 bgMerginFilename failure
213 df fail


To check exit codes:

condor_history gemc -af:jclusterId ExitStatus ExitCode Cmd -backwards | more



To find out where job is held:
condor_q 3623085 -constraint 'jobstatus == 5' -af MATCH_EXP_JOBGLIDEIN_ResourceName | sort | uniq -c
condor_q gemc -constraint 'jobstatus == 5' -af MATCH_EXP_JOBGLIDEIN_ResourceName | sort | uniq -c




# Retry automatically

on_exit_remove = (ExitBySignal == False) && (ExitCode == 0)
on_exit_hold = (ExitBySignal == True) || (ExitCode != 0)
periodic_release =  (NumJobStarts < 3) && ((CurrentTime - EnteredCurrentStatus) > (60*60))



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



# Singularity Image

* cvmfs path: ```/cvmfs/singularity.opensciencegrid.org/jeffersonlab/clas12software:production```

To run the singularity image:
```
singularity shell --home ${PWD}:/srv --pwd /srv --bind /cvmfs --contain --ipc --pid /cvmfs/singularity.opensciencegrid.org/jeffersonlab/clas12software:production
singularity shell --home ${PWD}:/srv --pwd /srv --bind /cvmfs --contain --ipc --pid /cvmfs/singularity.opensciencegrid.org/jeffersonlab/clas12software:devel
```



# Open Science Grid
[HomePage](https://support.opensciencegrid.org/support/home) [Grid Accounting](https://gracc.opensciencegrid.org/dashboard/db/gracc-home) 

Apparently submission from scosg16 do not automatically use JLAB VO but OSG VO whcih will go to the opportunistic pool. 
Only when we go to Glasgow we use the JLAB VO?


Help: help@opensciencegrid.org

https://opensciencegrid.org/contact

OSG Contacts: https://topology.opensciencegrid.org/contacts

# Scripts to be installed in utils and ran as cronjob (on scosg16.jlab.org):


#~ungaro:  volatileQuery.sh
~ungaro:  osgQuery.sh
~gemc:    gemcSubmitCron.sh priorityCron.sh





# To delete empty directories older than N days:

find /volatile/clas12/osg -mindepth 1 -mtime +N -type d -empty -delete
find /volatile/clas12/osg -mindepth 1 -type d -name log -exec rm -rv {} < why not?


# To delete anything other than hipo files

find /volatile/clas12/osg -type f -mtime +10 -not -name '*.hipo' -not -name nodeScript.sh -delete


# How to make SubMit.py run from anywhere on a machine:


1. `export PATH=$PATH:/path/to/clas-12-simulations-repo/client/src`
2. `export PYTHONPATH=/path/to/clas-12-simulations-repo` #this allows all SubMit.py to import all needed modules


---------------------

To change file format (ff)
----------------------------

If files are edited on a windows machine, the ff is ```\r\n```. To change it to the unix ff ```\n``` one can use vi:

```:set ff=unix```




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
- ability to remove lund from output
- disable back button otherwise job is submitted twice.




## Medium term todos:

- function to return approx location of submission using ip address and display on minimap
- options should be separated and different for client and server, not common in utils?
- show or make available options of the software versions inside the container
- error message if writing scard was not successful
- error if pool node doesn't make sense - in that case do not mark as submitted
- change   scripts_baseDir  = "/home/gemc/software/Submit/" to relative path 
- remove "#" from the scard in mysql (note: this may break things?)
- add a test for each single generators
- test release cycle and perfect it
- ability to add suffix to output dir
- change testRelease to the Submit line has the debug more (for example to use CLAS12OCR)
- priority page
- priority form


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
- when "submit" is pressed, add directory output message like: "Output will be at /lustre/expphy/volatile/clas12/osg/<username> at the bottom of the table." 
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




## Messages for maintenance

Dear all,

The CLAS12 OSG portal will undergo maintenance today starting at 3pm to upgrade the singularity container to include:

Clas12tags 4.3.2
Coatjava 6.5.3

All jobs submitted before 3pm will still run on OSG normally but it won’t be possible to submit new ones.
The maintenance will last a few hours.

Regards,
Mauri

Dear all,


The CLAS12 OSG portal is back online. The new container has:

Clas12tags 4.3.2
Coatjava 6.5.3

Regards,
The Clas12 OSG Team


# Getting Farm Statistics from Existing Options

* JLab Batch Farm: /site/scicomp/auger-slurm/bin/slurmHosts
* OSG: condor_status -pool flock.opensciencegrid.org at scosg16
* MIT: condor_status -pool t3serv007.mit.edu:11000?sock=collector






# from Mats: suggested flags for condor

Ok, we have applied a fix for this on our end as well. You can make sure you get it by adding this to your job requirements:

OSG_GLIDEIN_VERSION >= 546

While no longer fully necessary, I would still suggest you do the same "cleaning" in your container before setting up new modules. Here are the environment variables I'm unsetting:

unset ENABLE_LMOD
unset _LMFILES_
unset LMOD_ANCIENT_TIME
unset LMOD_arch
unset LMOD_CMD
unset LMOD_COLORIZE
unset LMOD_DEFAULT_MODULEPATH
unset LMOD_DIR
unset LMOD_FULL_SETTARG_SUPPORT
unset LMOD_PACKAGE_PATH
unset LMOD_PKG
unset LMOD_PREPEND_BLOCK
unset LMOD_SETTARG_CMD
unset LMOD_SETTARG_FULL_SUPPORT
unset LMOD_sys
unset LMOD_SYSTEM_DEFAULT_MODULES
unset LMOD_VERSION
unset LOADEDMODULES
unset MODULEPATH
unset MODULEPATH_ROOT
unset MODULESHOME

