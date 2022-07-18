# Mysql 

To make copy of DB:

`mysqldump -h jsubmit.jlab.org -u robertej -p CLAS12OCR | mysql -h jsubmit.jlab.org -u robertej -p clas12Backup`

to run with files:

mysql --defaults-extra-file=msql_conn.txt -N -s --execute="SELECT scard from CLAS12OCR.submissions where user_submission_id=221;" | grep gcard


# SQL Database Schema

[Schema PDF File](CLAS12_Simulations_DB.pdf)
[db diagram link](https://dbdiagram.io/d/5c9b829bf7c5bb70c72f6c34)


# mysql debugging:

### read:

mysql --defaults-extra-file=msql_conn.txt
use CLAS12OCR;
use CLAS12TEST;

describe submissions;
select user_id, user, client_time, user_submission_id, run_status from submissions ;
select scard from submissions where user_submission_id=28;
select runscript_text from submissions where user_submission_id=28;

### write 
### (from cue machines only):

mysql -u clas12jserver -h jsubmit.jlab.org -p
use CLAS12OCR;
use CLAS12TEST;

- delete entry
 delete from submissions where user_submission_id=970;
  
 - change status of submission
 update submissions set run_status='Not Submitted' where user_submission_id='3218';

 
 ## usual test flow
 
 # mysql permissions:

JLAB:
There is concern about giving thosle privileges to users since it allows users to create account and grant access to non-jlab people.. and, if used in particular ways, can actually give people the ability to execute code on the server. 
So, normally we try to avoid granting those privileges and just manage account creation for you. 

show users:

select user, host from mysql.db where db='CLAS12OCR';

show grants:

show grants for 'clas12jserver'@'%.jlab.org';

GRANT ALL ON *.* TO 'clas12jserver'@'%.jlab.org';

current users that access the db: 
 
 show processlist;



# mysql backup:

two steps because we need to enter PW twice

mysqldump -h jsubmit.jlab.org -u ungaro -p CLAS12OCR > d.back

cat d.back | mysql -h jsubmit.jlab.org -u ungaro -p clas12Backup

note: when running python3 utils/create_database.py the credentials in msqlrw.txt should be the ones of the user that created the DB



# table changes:

add column:

alter table CLAS12OCR.submissions add column run_job_text VARCHAR(15) AFTER clas12_condor_text;


# mysql priority:

mysql -u clas12jserver -h jsubmit.jlab.org -p

 use CLAS12OCR;
 select * from users;
 update users set priority='1' where user='ungaro';


# Mysql on Mac

Need to test pymysql

