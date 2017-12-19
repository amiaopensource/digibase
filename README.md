**CAVEAT: This project has been abandoned.**

---

# What is digibase?
Digibase uses MySQL to create a database which tracks tapes through digitization, QC, and ingest workflows. It is designed to be used with vrecord and Media Microservices.

## Required Software, Installation, and Setup
### Hardware and Software Requirements

Just like vrecord, currently digibase requires a computer running Mac OS X and a Blackmagic Design capture card as well as an installation of the Blackmagic drivers.

### Installation

Digibase and its required software can be easily installed used the Homebrew package manager for Mac OS X. Instructions for installing Homebrew are [here](http://brew.sh).

How you install MySQL all depends on how you want to use digibase. If you are using multiple computers to digitize you may want to have a separate computer run as a database server. In which case, install MySQL on your server by running `brew install mysql`.

Digibase is currently listed a dependency of vrecord. Even if you already have an installation of vrecord you should run `brew update` in the Terminal to get the latest installation formulae. Now run either `brew install vrecord` or `brew upgrade vrecord`. When digibase installs it should also install MySQL.

In order to use digibase, you will need to setup a MySQL server.  Run `mysql.server start`. This will start an instance of the MySQL server. Make sure starting the server is successful. Now run `mysql_secure_installation` to secure your installation. Follow the prompts.

Once MySQL has successfully been secured, run `mysql.server restart`. Make sure the restart is successful.

## Setting Up MySQL and Creating your Database

When running the secure installation you should have created a user of the MySQL server called "root". If you are not already signed in to the MySQL server you can sign in using the following command:
```
$ mysql -h [name of host] -u [username, which should be "root"] -p[password, do not put a space between the "-p" and your password]
```
The name of your host is "localhost" if you are connecting the local computer.

Now you need create a database that you intend to use to store information about tape digitization. Ideal database names are all lower case and use underscores for spaces (for example, "tape_digitizations"). When signed into the server run the following query `CREATE DATABASE [name of database];`. Make sure you save the name of this database for future reference.

If you want to check to make sure sure the database was created, run this query: `SHOW DATABASES;`. You should see the name of your new database in the list. You will not need to create tables within the database. Digibase/vrecord will automatically create the appropriate tables when you run it for the first time.

### Creating a New User

Now you need to create another user who is not "root" and who is allowed connect to the server from a remote computer. This should be the computer that runs vrecord to digitize tapes. Security best practice dictates that when creating this user you specify the host address that they will connect from. You will need to find the IP addresses of both the computer running the MySQL server as well as the remote computer. First, connect to the MySQL server as "root." Now run this query: `CREATE USER 'username'@'host_address' IDENTIFIED BY 'password';`. This will create a user that can connect from a specified address.

Now run `FLUSH PRIVILEGES;`. This will refresh the user tables. To check that your user has been created you can run this query: `SELECT * FROM mysql.user;`. This will show the list of all users. The user that you created should appear in the list.

If you want a user who can connect from any host (for testing purposes only, this isn't secure) you can run this query: `CREATE USER 'username'@'%' IDENTIFIED BY 'password';`.

If you need to modify a username of change a host address use this query: `RENAME USER 'username'@'host_address' TO 'new_username'@'new_host_address';`.

### Giving Permissions to the Database and Final Test

Finally, you will need to give the new user privileges to modify the database you are using. Run the following query: `GRANT ALL PRIVILEGES ON name_of_database.* to 'username'@'host_address';`. Now run `FLUSH PRIVILEGES;` again to refresh.

To check to make sure the new user has been given the permissions on the database run `SHOW GRANTS FOR 'username'@'host_address';`.

Now for the real test. Go to your remote computer and try to login as your new user by running `mysql -h [host address of MySQL server] -u [username] -p[password]`. Now run this query: `USE name_of_database;`. If you see the message `Database changed`, you're good to go. Your new user can now modify the database. Your initial setup of digibase should now be complete!

## Troubleshooting Installation

### My Computer Can't Connect to the MySQL Server

Check to make sure your host address is correct. Next try connecting to the computer running the MySQL server by pinging. Run `ping [host_address]`. Check to make sure there are no errors. You can stop pinging by typing control + c. If pinging fails, check your network settings. If pinging succeeds, check to see if the server is listening on port 3306, which is used for MySQL. Type `telnet [host address] 3306`. If the connection works you should see some garbled nonsense like this:
```
Escape character is '^]'.
J
5.7.12
      \/e>=b*?! JD{uPnOmysql_native_passwordConnection closed by foreign host.
```
Now you now the server is listening on port 3306. Otherwise you may get a `connection refused` message which means that the port is closed for some reason. You will need to open it to get MySQL to work. Try checking your firewall or network settings to make sure that port 3306 is open.

### MySQL Server Won't Start

## How to Use Digibase

### Digibase and vrecord (setuptables script)

Digibase mostly works within vrecord. Run `vrecord -e` and in the GUI select "yes" to "Digibase integration".

When digibase runs for the first time, you will be prompted for information like the host name where your SQL server is running, your username, your password, and the name of your database. This information will be saved to a configuration file (digibase.conf) in your home folder.

### Finish_qc Script

Run this script when you have completed QC of a file. The script will prompt you for an item ID and then ask you whether the item passed QC.

### Check_db Script

Use this script to search the database for various records, based on item ID or other search terms.

# Status of digibase

Digibase is currently being tested and is not production-ready. We'll create a public release when the scripts are ready for prime time.
