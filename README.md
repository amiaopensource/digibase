# What is digibase?
Digibase uses an existing installation of MySQL server to create a database which tracks tapes through digitization, QC, and ingest workflows. It is designed to work with vrecord and Media Microservices.  

# Required Software, Installation, and Setup
##Installation
In order to use digibase, you will need to install and setup a MySQL server. This can be accomplished on Mac OS X using the package manager Homebrew. Instructions for installing Homebrew are [here](http://brew.sh). To install MySQL using Homebrew open up a Terminal window and run `brew install mysql`.  Now run `mysql.server start` This will start an instance of the MySQL server. Make sure starting the server is successful. Now run `mysql_secure_installation` to secure your installation. Follow the prompts. Once MySQL has successfully installed and been secured, run `mysql.server restart`.

##Setting Up MySQL and Creating your Database
When running the secure installation you should have created a user of the MySQL server called "root". If you are not already signed in to the MySQL server you can sign in using the following command:
```
$ mysql -h [name of host] -u [username, which should be "root"] -p[password, do not put a space between the "-p" and your password]
```
Now you need create a database that you intend to use to store information about tape digitzations. Ideal database names are all lower case and use underscores for spaces (for example, "tape_digitizations"). When signed into the server run the following query `CREATE DATABASE [name of database];`. Make sure you save the name of this database for future reference. 

If you want to check to make sure sure the database was created, you can run the following query `SHOW DATABASES;`. You should see the name of your new database in the list. You will not need to create tables. Digibase/vrecord will automatically create the appropriate tables when you run them.  

##Creating a New User
Now you need to create another user that is not "root" who can connect to the server from a remote computer. This should be the computer that is running vrecord to do tape digigizations. Security best practice dictates that when creating this user you specificy the host address that they will connect from. You will need to find the IP addresses of both the computer running the MySQL server as well as the remote computer. First, connect to the MySQL server as "root." Now run this query: `CREATE USER 'username'@'host_address' IDENTIFIED BY 'password';`. This will create a user that can connect from a specified address. 

Now run `FLUSH PRIVILEGES;`. This will refresh the user tables. To check that your user has been created you can run this query: `SELECT User, Host, Password FROM mysql.user;`. The user you created should appear in the list.   

If you want a user who can connect from any host (for testing purposes only, this isn't secure) you can type `CREATE USER 'username'@'%' IDENTIFIED BY 'password';`.   

##Giving Permissions to the Database and Final Test
Finally, you will need to give the new user privileges to modify the database you are using. Run the following query: `GRANT ALL PRIVILEGES ON name_of_database.* to username@host_address;`. Now run `FLUSH PRIVILEGES;` again. 

To check to make sure the new user has been given the permissions on the database run `SHOW GRANTS FOR 'username'@'host_address';`. 

Now for the real test. Go to your remote computer and try to login as your new user by running `mysql -h [host address of MySQL server] -u [username] -p[password]`. Now run this query: `USE name_of_database;`. If you see the message `Database changed`, you're good to go. Your initial setup of digibase should now be complete!

#Troubleshooting Installation
##Checking IPs and Ports
You can check to see if the computer running the MySQL server is connected by pinging. You can also check to see if the server is listening on port 3306, which is used for MySQL. Type `telnet [host address] 3306`. If the connection works you should see some garbled nonsense like this: 
```
Escape character is '^]'.
J
5.7.12
      \/e>=b*?! JD{uPnOmysql_native_passwordConnection closed by foreign host.
```
Now you now the server is listening on port 3306. Otherwise you may get a `connection refused` message which means that the port is closed for some reason. You will need to open it to get MySQL to work. 

# How digibase works
When it starts up for the first time, digibase will prompt you for information like the host name where your SQL server is running, your username, your password, and the name of your database. This information will be saved to a configuration file (digibase.conf) in your home folder. 

Digibase mostly works with vrecord. You will need to say yes to "Digibase integration" within vrecord. 

# Status of digibase 
Digibase is currently being tested and is not production-ready. We'll create a public release when the scripts are ready for prime time. 
