# Mysql 

To make copy of DB:

`mysqldump -h jsubmit.jlab.org -u robertej -p CLAS12OCR | mysql -h jsubmit.jlab.org -u robertej -p clas12Backup`

to run with files:

mysql --defaults-extra-file=msql_conn.txt -N -s --execute="SELECT scard from CLAS12OCR.submissions where user_submission_id=221;" | grep gcard


# SQL Database Schema

[Schema PDF File](CLAS12_Simulations_DB.pdf)
[db diagram link](https://dbdiagram.io/d/5c9b829bf7c5bb70c72f6c34)



# mysql backup:

two steps because we need to enter PW twice

mysqldump -h jsubmit.jlab.org -u ungaro -p CLAS12OCR > d.back

cat d.back | mysql -h jsubmit.jlab.org -u ungaro -p clas12Backup

note: when running python utils/create_database.py the credentials in msqlrw.txt should be the ones of the user that created the DB



# mysql changes:

add column:

alter table CLAS12OCR.submissions add column run_job_text VARCHAR(15) AFTER clas12_condor_text;



# mysql priority:

mysql -u clas12jserver -h jsubmit.jlab.org -p

 use CLAS12OCR;
 select * from users;
 update users set priority='1' where user='ungaro';


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


