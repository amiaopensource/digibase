# What is digibase?
Digibase uses an existing installation of MySQL server to create a database which tracks tapes through digitization, QC, and ingest workflows. It is designed to work with vrecord and Media Microservices.  

# Required Software/Setup 
In order to use digibase, you will need to install and setup a MySQL server. This can be accomplished on Mac OS X using the package manager Homebrew. Instructions for installing Homebrew are [here](http://brew.sh). To install MySQL using Homebrew open up a Terminal window and run `brew install mysql`. Once MySQL has successfully installed run `mysql.server start`. This will start an instance of the MySQL server. Now run `mysql_secure_installation` to secure your installation. 

In order to use digibase you should create a user of the MySQL server that is not "root" can sign in from a location other than the local machine. Creating a new user or managing databases are easiest to do with a MySQL graphical user interface (GUI) such as Sequel Pro for Mac. You can download Sequel Pro [here](http://www.sequelpro.com). A web-based option is PHPMyAdmin, which can be downloaded [here](https://www.phpmyadmin.net).

Create a database that you intend to use and save the name of this database. You can do this thorugh a GUI or through the command line. 

# How digibase works
When it starts up for the first time, digibase will prompt you for information like the host name where your SQL server is running, your username, your password, and the name of your database. This information will be saved to a configuration file.

# Status of digibase 
Digibase is currently being tested and is not production-ready. We'll create a public release when the scripts are ready for prime time. 
