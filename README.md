
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


