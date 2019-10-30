# MySQL_Server
## Installation
To install MySQL, run the following command from a terminal prompt:

    sudo apt install mysql-server
Once the installation is complete, the MySQL server should be started automatically. You can run the following command from a terminal prompt to check whether the MySQL server is running:

    sudo netstat -tap | grep mysql
When you run this command, you should see the following line or something similar:

tcp        0      0 localhost:mysql         *:*                LISTEN      2556/mysqld
If the server is not running correctly, you can type the following command to start it:

sudo systemctl restart mysql.service
## Configuration
  You can edit the /etc/mysql/my.cnf file to configure the basic settings -- log file, port number, etc. For example, to configure MySQL to listen for connections from network hosts, change the bind-address directive to the server's IP address:

  bind-address            = 192.168.0.5
  Replace 192.168.0.5 with the appropriate address.

  After making a change to /etc/mysql/my.cnf the MySQL daemon will need to be restarted:

  sudo systemctl restart mysql.service
## Advanced configuration
Creating a tuned my.cnf file
There are a number of parameters that can be adjusted within MySQL's configuration file that will allow you to improve the performance of the server over time. For initial set-up you may find Percona's my.cnf generating tool useful. This tool will help generate a my.cnf file that will be much more optimised for your specific server capabilities and your requirements.

Do not replace your existing my.cnf file with Percona's one if you have already loaded data into the database. Some of the changes that will be in the file will be incompatible as they alter how data is stored on the hard disk and you'll be unable to start MySQL. If you do wish to use it and you have existing data, you will need to carry out a mysqldump and reload:

mysqldump --all-databases --routines -u root -p > ~/fulldump.sql
This will then prompt you for the root password before creating a copy of the data. It is advisable to make sure there are no other users or processes using the database whilst this takes place. Depending on how much data you've got in your database, this may take a while. You won't see anything on the screen during this process.
Once the dump has been completed, shut down MySQL:

    sudo systemctl stop mysql.service
    Now backup the original my.cnf file and replace with the new one:
    sudo cp /etc/mysql/my.cnf /etc/mysql/my.cnf.backup
    sudo cp /path/to/new/my.cnf /etc/mysql/my.cnf
    Then delete and re-initialise the database space and make sure ownership is correct before restarting MySQL:
    sudo rm -rf /var/lib/mysql/*
    sudo mysql_install_db
    sudo chown -R mysql: /var/lib/mysql
    sudo systemctl start mysql.service
    Finally all that's left is to re-import your data. To give us an idea of how far the import process has got you may find the 'Pipe Viewer' utility, pv, useful. The following shows how to install and use pv for this case, but if you'd rather not use it just replace pv with cat in the following command. Ignore any ETA times produced by pv, they're based on the average time taken to handle each row of the file, but the speed of inserting can vary wildly from row to row with mysqldumps:
    sudo apt install pv
    pv ~/fulldump.sql | mysql  
  
