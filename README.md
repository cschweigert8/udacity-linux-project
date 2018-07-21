# Udacity Linux Server Project

### Public IP Address
34.221.139.27

### URL
http://34.221.139.27

## Getting Started
1. On your local machine, downlowd the private key provided
2. Move the public key into your ~/.ssh folder
3. Change permissions by running `chmod 600 ~/.ssh/udacitylinux`
4. SSH into the instance by running `ssh -i ~/.ssh/udacitylinux grader@34.221.139.27 -p 2200`
5. Password for the private key is 'asylum'.

### Summary of Software Installed
* pip
* sqlalchemy
* Flask
* Apache2
* PostGreSQL
* git

### Summary of Configurations Made
#### Create new user and give sudo access
```
sudo adduser grader
sudo vi /etc/sudoers.d/grader
```
Insert text `grader ALL=(ALL:ALL) ALL`
#### Install SSH keys
1. Generate keys on local using `ssh-keygen`
2. Save private key in ~/.ssh
3. Add public key to lightsail environment
4. `su - grader` to change to the grader user
5. Create authorized_keys file and open editor - copy the public key in here and save
```
sudo mkdir ~/.ssh
sudo vi ~/.ssh/authorized_keys
``` 
6. Change permissions to directory and file
   ```
   sudo chmod 700 ~/.ssh
   sudo chmod 644 ~/.ssh/authorized_keys
   ```
7. Reload SSH `service ssh restart`
#### Update packages
```
sudo apt-get update
sudo apt-get upgrade
```
#### Change SSH port
```
sudo vi /etc/ssh/sshd_config
sudo service ssh restart
```
I also had to replace the default SSH port 22 with a custom TCP port 2200 on the lightsail admin page under the networking tab.

#### Configure UFW
```
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow www
sudo ufw allow 2200/tcp
sudo ufw allow udp
sudo ufw enable
```
#### Install Apache and configure for mod_wsgi app
```
sudo apt-get install apache2
sudo apt-get install python-setuptools libapache2-mod-wsgi
sudo service apache2 restart
```
#### Install and Configure PostgreSQL
```
sudo apt-get install postgresql
sudo su - postgres
psql
postgres=# CREATE DATABASE catalog;
postgres=# CREATE USER catalog;
postgres=# ALTER ROLE catalog WITH PASSWORD 'catalog';
postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
```
Check `/etc/postgresql/9.5/main/pg_hba.conf` to see if remote connections are allowed
```
sudo vi /etc/postgresql/9.5/main/pg_hba.conf
```
#### Install git, clone catalog project, and configure to run as wsgi application
