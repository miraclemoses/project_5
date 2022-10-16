Client-Server Architecture with MySQL
At its simplest, the client-server architecture is about dividing up application processing into two logically distinct pieces.
A client-server DBMS Architecture is split into two components. The server component typically resides on the same physical computer as the database files and is responsible for all interactions with the database. The second component is the client. The client sends all database requests to the server which in turn processes the request and returns the results of the request back to the client.
A simple diagram of Web Client-Server architecture is presented below:


If we extend this concept further and add a Database Server to our architecture, we can get this picture:

Web Server has a role of a “Client” that connects and reads/writes to/from a Database (DB) Server (MySQL, MongoDB, Oracle, SQL Server or any other), and the communication between them happens over a Local Network (it can also be Internet connection, but it is a common practice to place Web Server and DB Server close to each other in local network).

This project demonstrates how to implement a Client Server Architecture using MySQL Database Management System (DBMS).

Prerequisites
Two Ubuntu 18.04 LTS Servers (this project is executed on a Microsoft Azure Ubuntu VMs). Create and configure two Linux-based virtual servers:
Server A name – ‘mysql-server’
Server B name – ‘mysql-client’
Putty (for Windows OS)
.ppk private key file to connect to the Ubuntu Server (The .ppk key is gotten from the conversion of the .pem file created when the Ubuntu VM was created. The .pem file is converted to .ppk using Puttygen).

Step 1: Install MySQL in the two servers
MySQL is to be installed in both servers. Run the following commands in both servers:
Update a list of packages in package manager
$ sudo apt update
Use ‘apt’ to acquire and install MySQL
$ sudo apt install mysql-server
When prompted, confirm installation by typing Y, and then ENTER.
You can confirm the MySQL installation
$ mysql -V 


Step 2: Configure the MySQL installation in ‘mysql-server’ Server
To configure the MySQL installation in ‘mysql-server’, it’s recommended to run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to the database system. Start the interactive script by running:
$ sudo mysql_secure_installation
This will ask if you want to configure the VALIDATE PASSWORD PLUGIN. Answer ‘y’ for yes.

The configuration will ask to select a level of password validation. Note that if ‘2’ is selected, there will be errors when attempting to set any password which does not contain numbers, upper and lowercase letters, and special characters, or which is based on common dictionary words.

The configuration will next ask to set and confirm a password for the MySQL root user. This is not to be confused with the system root. The database root user is an administrative user with full privileges over the database system. 
If password validation is enabled, the password strength for the root password just entered will be shown and the server will ask to continue with that password. If satisfied with the current password, enter Y for “yes” at the prompt. For the rest of the questions, press ‘Y’ and hit the ‘ENTER’ key at each prompt. This will remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes made:

When finished, test the MySQL console login by typing:
$ sudo mysql
This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command.  The output looks like this:

To exit the MySQL console, type:
mysql> exit

Step 3: Create a MySQL database in ‘mysql-server’ Server
Create a database named example_database and a user named ernesto (these names can be replaced with values of choice).
First, connect to the MySQL console using the root account:
$ sudo mysql
To create a new database, run the following command from your MySQL console:
mysql> CREATE DATABASE `example_database`;
Create a new user and grant the account full privileges on the database just created.
The following command creates a new user named ernesto, using mysql_native_password as default authentication method. We’re defining this user’s password as ‘Password11#’. The password chosen should be in line with the level of password validation policy chosen when the VALIDATE PASSWORD PLUGIN was configured.
mysql>  CREATE USER 'ernesto'@'%' IDENTIFIED WITH mysql_native_password BY 'Password11#';
Give this user permission over the example_database database:
mysql> GRANT ALL ON example_database.* TO 'ernesto'@'%';
This will give the ernesto user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server.
Exit the MySQL shell with:
mysql> exit


Test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:
$ mysql -u ernesto -p
Notice the -p flag in this command, which will prompt for the password used when creating the ernesto user. After logging in to the MySQL console, confirm access to the example_database database:
mysql> SHOW DATABASES;
This will give the following output:

Exit the MySQL shell with:
mysql> exit

Step 4: Connect to the database from ‘mysql-client’ Server
MySQL server uses TCP port 3306 by default. Add an inbound security rule to the Network Security Group of the ‘mysql-server’ server to open inbound connection through port 3306. For extra security, do not allow all IP addresses to reach the ‘mysql-server’ server. Allow access only to the IP address of the ‘mysql-client’ server.



Next, configure the MySQL server to allow connections from remote hosts (in this case, the ‘mysql-client’ server). Edit the MySQL server configuration file by using a text editor.
$ sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
Replace ‘127.0.0.1’ to ‘0.0.0.0’ on the bind-address line

Restart the MySQL server
$ sudo systemctl restart mysql
Confirm the status of the MySQL server
$sudo systemctl status mysql


From ‘mysql-client’ server connect remotely to ‘mysql-server’ Database Engine using the MySQL utility to perform this action.
$ mysql -u ernesto -h <IP_address_of_mysql_server> -p
Where:
-u ernesto  : ‘ernesto’ is MySQL username
-h <IP_address_of_mysql_server> : This is the ‘mysql-server’ IP address or hostname (FQDN)
-p : Prompt for password

Check that you have successfully connected to a remote MySQL server and can perform SQL queries:
mysql> SHOW DATABASES;


Conclusion
There are a couple of key advantages to the client-server architecture DBMS. Firstly, there is no need for the client to be running on the same computer system as the server. Instead, requests can be sent by the client over a network or internet connections to the server on a remote host. The fact that the server resides on a remote computer is invisible to the client user. This makes the database available to greater numbers of users. In large scale enterprise environments this also allows high levels of fault tolerance and load balancing to be implemented.
Secondly, separating the client from the server allows a wider range of client types to be used to access the database. Valid clients can be the MySQL tools, applications written in other programming languages such as C, C++ or Java, or web based applications developed using languages such as PHP or JSP).

Credits
https://darey.io
MySQL Database Architecture - Techotopia
