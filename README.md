# overview 
this project is part of udacity nanodegree program. i uploaded my item catalog using 
amazon's lightsail service.  

## server information
Ip address:52.29.170.160
ssh port: 2200 (changed like instructed)

## make a server
follow the below link and sign up in order to be able to make an instance

instance on AWS [click here to make your aws instance](https://lightsail.aws.amazon.com/).


# Getting started

### Connect to your Server using SSH from your pc/laptop

* Create a directory called .ssh if you don't have it. this directory will be sued to store your private key in it 
        
        mkdir -p ~/.ssh

* from your account page download the ssh key file, it will be a  `.pem`

* Move the downloaded `.pem` file to `.ssh` directory that you created. 
    
        mv key.pem ~/.ssh
    
* change the privalleges of the file 
    sudo chmod 400 ~/.ssh/key.pem    

* Connect to instance.

        ssh -i ~/.ssh/key.pem ubuntu@52.29.170.160

### Update apt-get 
* this step is essential in order for you to get certain packages.

        sudo apt-get update
        sudo apt-get upgrade

#  Create new user and name it grader
    
        sudo adduser gader

### Add grader to sudo group
* first you make a copy of your ubuntu sudo file        
        sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader 
* second run this command and change user from `ubuntu` to `grader`
    sudo nano /etc/sudoers.d/grader
* switch to grader user

        su grader

### Creating grader ssh key

* on your laptop/pc machine 

        Create ssh keys using command  ssh-keygen then name the file grader (or anything you like) 
    
* on aws instance 

    * Create a new directory /.ssh in the grader home directory (make sure that you are not in the ubuntu home directory)

            mkdir .ssh

    * Change .ssh permissions

            chmod 700 .ssh

    * Go to ssh directory

            cd .ssh

    * Create `authorized_keys` file on aws instance and copy the content of the `grader.pub` generated on your pc/laptop  into `authorized_keys` on aws instance

            nano authorized_keys

    * Change `authorized_keys` permissions

            chmod 644 authorized_keys


### changing configration file

        sudo nano /etc/ssh/sshd_config

* change port from 22 to 2200
* change PermitRootLogin to  no
* change PasswordAuthentication to  no

### Edit inistance configration

go to your aws instance on [Lightsail](https://lightsail.aws.amazon.com/) then click on networking then go to firewall and add another port
* port sepcs   
  custom port 2200/tcp  .

### Restart ssh
*  this is a vital step that if you forget you won't be able to login again
        sudo service ssh restart

### connect again using command on your pc/laptop

        ssh grader@52.29.170.160 -p 2200 -i ~/.ssh/grader

#  Configure the Firewall
* run the following commands one at a time 
        sudo ufw default deny incoming
        sudo ufw default allow outgoing
        sudo ufw allow 2200/tcp
        sudo ufw allow www
        sudo ufw allow ntp
        sudo ufw enable

* PS: to make sure that everything is configured fine run `sudo ufw status` this is an optional step
# Configration Timezone
* run the command 
        sudo dpkg-reconfigure tzdata

* choose none of the above
* then UTC

# installing the project on your aws instance

### Install Apache

        sudo apt install apache2

### Install Python mod_wsgi package

        sudo apt install libapache2-mod-wsgi
        

### Install postgesql

        sudo apt-get install postgresql
        sudo apt install libpq-dev python-dev postgresql postgresql-contrib

### Create DataBase

        sudo su postgres
        psql
        CREATE USER catalog WITH PASSWORD 'catalog';
        CREATE DATABASE  catalog; 
        GRANT ALL PRIVILEGES ON DATABASE "catalog" to catalog; 


### Clone the app

        sudo mkdir /var/www/html
        sudo git clone https://github.com/karimayman/udacity-project-two.git
        cd /var/www/html

*  connect to the database like this 

        create_engine('postgresql://catalog:catalog@localhost/catalog')

* install dependencies
     sudo apt-get install python-pip
     sudo apt-get install python-virtualenv 
     python 
     sudo apt install python-flask
     sudo apt-get install python-mysqldb
     python database_setup.py
     sudo apt-get install python-psycopg2
     python database_setup.py
     python lotsofitems.py 
     python lotsofitems.py 
     python lotsofitems.py 
     sudo apt-get install python-oauth2client
     sudo apt-get install python-requests
    



* run `database_setup.py`

* run `lotsofitems.py`



### Create wsgi file

* Go to the application directory

        cd /var/www/html

* Create `myapp.wsgi` file

     sudo nano myapp.wsgi

* Add this content

     #!/usr/bin/python   
     import sys
     sys.path.append('/var/www/html')
     from project import app as application





### Configure Apache2 file

* Edit configration file 

        sudo nano /etc/apache2/sites-available/000-default.conf

* Add this content

    <virtualHost *:80>
         # The ServerName directive sets the request scheme, hostname and port that
         # the server uses to identify itself. This is used when creating
         # redirection URLs. In the context of virtual hosts, the ServerName
         # specifies what hostname must appear in the request's Host: header to
         # match this virtual host. For the default virtual host (this file) this
         # value is not decisive as it is used as a last resort host regardless.
         # However, you must set it for any further virtual host explicitly.
         #ServerName www.example.com

         ServerAdmin webmaster@localhost
         DocumentRoot /var/www/html

         # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
         # error, crit, alert, emerg.
         # It is also possible to configure the loglevel for particular
         # modules, e.g.
         #LogLevel info ssl:warn

         ErrorLog ${APACHE_LOG_DIR}/error.log
         CustomLog ${APACHE_LOG_DIR}/access.log combined

         # For most configuration files from conf-available/, which are
         # enabled or disabled at a global level, it is possible to
         # include a line for only one particular virtual host. For example the
         # following line enables the CGI configuration for this host only
         # after it has been globally disabled with "a2disconf".
         #Include conf-available/serve-cgi-bin.conf
         WSGIScriptAlias / /var/www/html/myapp.wsgi
    </VirtualHost>



* Restart Apache2

        sudo apache2ctl restart

#  my website
[project 3 :item category ](http://52.29.170.160/)

