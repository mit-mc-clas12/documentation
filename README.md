# documentation

an independent set of documentation for MIT CLAS12 projects including MC submission


# To test the entire workflow: (accurate as of 20190509)

Log onto a computing pool (e.g. SubMIT) and move to a working directory, then do the following:

mkdir clas12-test      
cd clas12-test   
git clone https://github.com/mit-mc-clas12/database          
git clone https://github.com/mit-mc-clas12/client  
git clone https://github.com/mit-mc-clas12/server                
git clone https://github.com/mit-mc-clas12/utils       
python2 utils/create_database.py -d 1      
python2 client/src/SubMit.py -d 1                       
cd server                                          
python2 src/Submit_batch.py -s -d 1  
