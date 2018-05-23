# Linux Server Configuration
This project aims at installing a Linux server on a virtual machine &amp; host the Item Catalog Application on the VM.
The website is deployed at http://13.58.96.32

#### Tasks to be performed:
  - Launch the Ubuntu Linux Server instance on Amazon Lightsail.
  - Follow the instructions provided on Amazon Lightsail to SSH into your server.
  - Change the SSH port from 22 to 2200.
  - Create a new user account named `grader` and give sudo access to the grader,           thereby creating a SSH key pair.
  - Configure the local timezone to UTC.
  - Install and configure Apache to serve a Python Application i.e Item Catalog            Application.
  - Install and configure PostgreSQL.
  - Create a new DB user named `Catalog` and install `git`.
  - Clone and setup the `Item Catalog` project from github such that it functions          correctly on visiting the server's IP address. 

#### Launching the Virtual Machine:
Download the private key using the instructions given below(private key not specified here):
- Move the private key file into the folder ~/.ssh (~ is the environment's home directory). For instance, if the private key file was downloaded to the Downloads folder, execute the following command on your terminal:
`mv ~/Downlaods/LightsailDefaultPrivateKey.pem ~/.ssh/`
- Open your terminal and type `chmod 600 ~/.ssh/LightsailDefaultPrivateKey.pem`
- Execute the command: 
`ssh -i ~/.ssh/LightsailDefaultPrivateKey.pem ubuntu@13.58.96.32`

#### Create a new user named 'grader':
In order to create a new user follow the instructions below:
```sh 
sudo adduser grader
nano /etc/sudoers
touch /etc/sudoers.d/grader
```
- In order to give sudo access to the grader: `nano /etc/sudoers.d/grader`
and then add the following line within the file:
`grader ALL=(ALL:ALL) ALL`
- Save & quit
#### Set ssh login using keys:
- On the local machine, generate the key using `ssh-keygen`.
- Save the private key in ~/.ssh/linuxCourse on the local machine.
- Deploy the public key on the development environment using the following steps:
    ```sh
    $ su grader
    $ mkdir .ssh    
    $ touch .ssh/authorized_keys
    $ nano .ssh/authorized_keys
    ```
- Copy the public key generated on your local machine to the 'authorized_keys' file and save.
- Give necessary permissions using the following commands:
    ```sh
    $ chmod 700 .ssh
    $ chmod 644 .ssh/authorized_keys
    ```
- Reload th server using `service ssh restart`
- Finally, you can now login as the 'grader' by using the command 
 `ssh -i [privateKeyFileName] grader@13.58.96.32` 
#### Update all the currently installed packages:
All the installed packages can be updated using the following command:
```sh
sudo apt-get update
sudo apt-get upgrade
```
#### Change the port from 22 to 2200:
- Execute `sudo nano /etc/ssh/sshd_config and then change the Port 22 to 2200. Save the file and quit.
- Reload SSH using `sudo service ssh restart`
#### Configure the UFW(Uncomplicated Firewall):
The UFW should be configured in order to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123) which can be achieved using the following commands:
```sh
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
sudo ufw enable
```
#### Configure the local timezone to UTC:
- The local timezone can be configured by executing `sudo dpkg-reconfigure tzdata`
#### Install & configure Apache to serve a Python mod_wsgi application:
- Install Apache: `sudo apt-get install apache2`
- Install Python mod_wsgi: 
`sudo apt-get install python-setuptools libapache2-mod-wsgi`
- Restart Apache: `sudo service apache2 restart`
#### Install & configure PostgreSQL:
- Install PostgreSQL: `sudo apt-get install postgresql`
- Check if no remote connections are allowed:
`sudo nano /etc/postgresql/9.3/main/pg_hba.conf`
- Login as user 'postgres': `sudo su - postgres`
- Enter PostgreSQL shell: `psql`
- Create a new database named 'catalog' and create a new user named 'catalog' in the PSQL shell:
`CREATE DATABASE catalog;`
`CREATE USER catalog;`
- Set a password for the user 'catalog'
 `ALTER ROLE catalog WITH PASSWORD 'password'`
- Give the user 'catalog' permission to access the DB 'catalog':
`GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog`
- Quit PostgreSQL: `\q`
- Exit from PostgreSQL: `exit`
#### Install git, clone and setup the Item Catalog Application:
- Install git using:`sudo apt-get install git`
- Clone Item Catalog Application into your home directory using the following commands:
    - `cd ~`
    - `git clone https://github.com/Veena0803/Item-Catalog-Application.git`
- Execute `cd /var/www` to change the directory to /var/www
- Create the Application directory `sudo mkdir CuisineApp`
- Execute `cd CuisineApp` to change directory to the app root.
- Copy all the project specific files into the above directory.
- Create a CuisineApp.wsgi file with the following contents:
    ```sh
    #! /usr/bin/python
    import sys
    import logging
    logging.basicConfig(stream = sys.stderr)
    
    sys.path.insert(0, "/var/wwww/CuisineApp")
    
    from finalProject import app as application
    application.secret_key = 'secret'
    ```
- Install pip `sudo apt-get install python-pip`
- Install psycopg2: `sudo pip install psycopg2`
- Create database schema: `sudo python database_setup.py`
- Migrate DB from SQLite to PostgreSQL as follows:
`engine = create_engine('postgresql://catalog:password@localhost/catalog')`

 #### Configure & Enable a new virtual host using WSGI:
- Change directory by executing `cd /etc/apache2/sites-enabled/`
- Update the file 000-default.conf to add the CuisineApp application by inserting the following line into the config:
`WSGIScriptAlias / /var/www/CuisineApp/CuisineApp.wsgi` 
before the </VirtualHost> tag.
#### Restart Apache:
Restart Apache: `sudo service apache2 restart`
### References:
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
https://stackoverflow.com/questions/37954008/proper-way-to-install-pip-on-ubuntu
https://discussions.udacity.com/

