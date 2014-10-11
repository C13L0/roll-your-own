###Roll-your-own Drupal setup on Ubuntu 14.04
=============

Complete Ubuntu 14.04 local setup guide for Drupal 7 &amp; 8. Includes lamp, git, composer, drush, and rvm  

LOCAL set up only!  Ubuntu 14.04 / LAMP / Drupal sites setup

Create a bootable install disk or usb drive, follow directions and install Ubuntu
It is very important to write down or remember your username and pw. You will use these on a regular basis
Open a terminal. Click on the top left icon and type terminal.
Run updates (don’t bother with the software updater) This takes a while.

```sudo apt-get update```

If you want to use a text editor, it is called gedit. Open it the same way as the terminal. Once it is in the sidebar launcher, you can left click and lock. 
You can also remove most of the other annoying icons from the launcher so they are not in your way.

Give your user permissions!  
```sudo adduser yourusername sudo```  
```sudo adduser yourusername www-data```  

Note: If you are having permission issues, you can also do the following. I do not believe this method is recommended  
```sudo visudo``` 
Using the arrow keys scroll down until you see # User privilege specification  
Place cursor under: root ALL=(ALL:ALL) ALL  
Add: yourusername ALL=(ALL:ALL) ALL  
esc  
:wq  

Create Lamp Stack http://www.krizna.com/ubuntu/install-lamp-server-ubuntu-14-04/

```sudo apt-get install apache2```  
```sudo nano /etc/apache2/apache2.conf```  
Use the arrow key to scroll down to the end of the file and type in:  
ServerName localhost
ctrl o (saves)
enter 
ctrl x (exit)
You can check to make sure that it saved with 
cat /etc/apache2/apache2.conf
Restart apache 
sudo service apache2 restart
sudo chown yourusername:www-data /var/www/html -R
You can check to make sure it works properly by opening the browser and typing /var/www/html 

Install mysql server

sudo apt-get install mysql-server
It will ask you to create a password. Generally for localhost it is root/root. 
Check the service status with 
sudo /etc/init.d/mysql status
If your prompt is mysql> after checking the status, you can escape with:  
exit

Install php
sudo apt-get install php5 php5-mysql
Create a php file. 
sudo nano /var/www/html/phpinfo.php
Add the following code 

<?php
phpinfo();
?>

ctrl o (saves)
enter 
ctrl x (exit)
restart apache2: 
sudo service apache2 restart
Open browser and navigate to: localhost/phpinfo.php

Install phpmyadmin https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-phpmyadmin-on-ubuntu-14-04

sudo apt-get install phpmyadmin
Important! Hit the spacebar to choose apache 2. It will show an *
Tab (to move to <ok>
Enter
Select Yes to configure with dbconfig-common
Follow the rest with your pw and again root is a common password used for a local host
sudo php5enmod mcrypt
Restart apache: 
sudo service apache2 restart
Test by navigating in the browser to: localhost/phpmyadmin
You can follow the rest of the tutorial for additional securities if you want. I am not sure if this is necessary for local. Of course it would be for a cloud server

Don’t ever do this on a live server but local is fine. Drupal php needs access.
chmod 775 /var/www/html
  Increase max limit php.ini
sudo nano /etc/php5/apache2/php.ini
ctrl w (search) memory_limit
change: memory_limit = 512M
ctrl o (saves)
enter 
ctrl x (exit)

Create SSH key (you can replace keys with your old ones after initial set up)
cd ~
mkdir .ssh
chmod 700 .ssh
ssh-keygen -t rsa
enter, enter, enter (don’t put passphrase, but you already knew that)
*extra* if you want your old keys - put your old keys on a usb drive, navigate to the .ssh folder and replace them

Install git
cd ~
sudo apt-get install git
git config --global user.name your name
git config --global user.email youremail@domain.com
git config --global color.ui auto

Install composer Note! You must install composer before installing drush
cd ~
sudo apt-get install curl
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
sed -i '1i export PATH="$HOME/.composer/vendor/bin:$PATH"' $HOME/.bashrc
source $HOME/.bashrc

Install Drush (must install composer first) For new drush commands http://www.drushcommands.com/
cd ~
composer global require drush/drush:dev-master
composer global update

Install IDE (sublime text 3)
cd ~
sudo add-apt-repository ppa:webupd8team/sublime-text-3
sudo apt-get update
sudo apt-get install sublime-text-installer
Add in drupal specific preferences https://drupal.org/node/1346890
Install rvm for theming

\curl -sSL https://get.rvm.io | bash -s stable
source ~/.rvm/scripts/rvm
Find current stable version at www.ruby-lang.org/en/downloads/
rvm install 2.1.2  (install current stable version)
Change terminal preferences:
edit > profile preferences
Title and Command > checkmark Run command as a login shell
close terminal > reopen
Find the latest version www.ruby-lang.org/en/downloads/
rvm use 2.1.2  to verify: ruby -v

Install IRC

sudo add-apt-repository ppa:gwendal-lebihan-dev/hexchat-stable
sudo apt-get update
sudo apt-get install hexchat



—— Set up for Sites ——

Configure apache to preference .php files over .html files
sudo nano /etc/apache2/mods-enabled/dir.conf
Add a parameter reading "index.php" as the first item after "DirectoryIndex"
   
<IfModule mod_dir.c>

          DirectoryIndex index.php index.html index.cgi index.pl index.php index.xhtml index.htm

</IfModule>

ctrl o (save)
enter
ctrl x (exit)
sudo service apache2 restart

Enable global site clean url’s
 sudo a2enmod rewrite 
 sudo service apache2 restart
 sudo nano /etc/apache2/apache2.conf
Scroll down until you find  
<Directory /var/www/>
		Options Indexes FollowSymLinks
		AllowOverride None
		Require all granted
</Directory>
Change AllowOverride to all:  
<Directory /var/www/>
		Options Indexes FollowSymLinks
		AllowOverride All
		Require all granted
</Directory>
    5.   sudo service apache2 restart



Create vhost site configuration files http://ben-it.org/content/ubuntu-1204-apache-2-edit-default-virtualhost-enable-modrewrite-module-drupal-clean-urls

cd /etc/apache2/sites-available
Create a new vhost config file just for drupal
sudo nano drupal
Add in the following

NameVirtualHost *:80
<VirtualHost *:80>
   DocumentRoot /var/www
   ServerName localhost
</VirtualHost>

ctrl o (saves)
enter
ctrl x (exit)

Symlink the drupal file in the sites-enabled directory
cd /etc/apache2/sites-enabled 
sudo ln -s ../sites-available/drupal .
sudo service apache2 restart


Make it easy to navigate to our sites folder by creating a symlink to /var/www/html
cd ~
Change “cielo” to your user. If you are unsure of the name, type pwd
ln -s  /var/www/html /home/cielo/sites
Now you can easily cd sites and you will be directly in the html folder

Future note: You can create and name a symlink wherever you like
The first path is what you are linking
The second path is where the link will be
Example: ln -s /what/is/being/linked /where/symlink/goes/nameofsymlink

To remove a symlink:  unlink sites (symlink name)




---- Create a drupal site (or two) ---- works great for drupal 8 too

 Configure Apache2 for Sites (do this with both site creation methods)

sudo nano /etc/hosts
Add under the last line

# Drupal sites
127.0.0.1 example-site.dev

ctrl o (saves)
enter
ctrl x (exit)

Future note: You will edit this file for every additional site so it will look like this
# Drupal sites
127.0.0.1 example-site.dev
127.0.0.1 newsite2.dev
127.0.0.1 newsite3.dev

--- Create database and site via gui/git. Scroll down for cl/drush alternate. ---

Create database
Open browser and navigate to localhost/phpmyadmin
Create new database called example_site

Git clone site
cd sites (hopefully you made a symlink. If you didn’t then cd /var/www/html)
git clone --branch 7.x http://git.drupal.org/project/drupal.git    (drupal 7)
git clone --branch 8.x http://git.drupal.org/project/drupal.git    (drupal 8)
Change the name of the cloned drupal directory to the name of the new site
mv drupal example-site.dev
cd example-site.dev/sites/default
Copy default.settings.php
cp default.settings.php settings.php

Complete Install
Open browser and navigate to localhost/example-site.dev
Complete install, making sure to fill in database name and password

Rinse and repeat this page for any new drupal sites =)



--- Alternate database and site creation using command line and drush ---

Create database
mysqladmin -u root -p create database_name
Enter your MySQL root password at the prompt.

Create database user for site via command line
mysql -u root -p
Enter your MySQL root password at the prompt.
(at mysql>) CREATE USER 'name_of_new_user'@'localhost' IDENTIFIED BY 'password_of_new_user';
(at mysql>) GRANT ALL PRIVILEGES ON database_name.* TO 'name_of_new_user'@'localhost'; 
(at mysql>) FLUSH PRIVILEGES;
(at mysql>) exit

Create site via drush (Confirmed on D7. Still needs testing on D8)
drush dl drupal-7.x (can also do drupal-8.x)
mv drupal-7.x-dev site_directory_name
cd site_directory_name
drush si standard --account-name=admin --account-pass=admin --db-url=mysql://database_user_name:database_user_password@localhost/database_name



----- Get an old site up and running -----

You will need:
Compressed database dump from old site in (mysql.zip or mysql.gz)
All of the files or ability to git clone the files of the old site 
** If you are not able to get a db dump and need to use backup and migrate module, Please follow a different set of directions, posted on page 10.

Create database
Open browser and navigate to localhost/phpmyadmin
Create new database for your site. example: oldsite or old_site
Click on the newly created database
Choose import, navigate to the compressed db file (mysql.gz) and click go

Configure Apache2 for the old site 

sudo nano /etc/hosts
Add under the last line

# Drupal sites
127.0.0.1 oldsite.dev

ctrl o (saves)
enter
ctrl x (exit)

Place the oldsite folder in /var/www/html (or git clone the oldsite to this folder)
	
Navigate to /sites (or /var/www/html if you didn’t create a symlink)
Change the name of your folder to oldsite.dev (basically we are adding .dev and making sure the name of this folder matches the oldsite.dev name in the hosts file)
Navigate into the sites/default folder and delete any old settings.php
Copy default.settings.php and rename it: settings.php



Manually link your oldsite to the new database

Open settings.php in sublime or gedit
You can copy/paste this in settings.php around line 219. Look for $databases = array();


$databases = array (
  'default' =>
  array (
    'default' =>
    array (
      'database' => 'DATABASENAME',
      'username' => 'root',
      'password' => 'root',
      'host' => 'localhost',
      'port' => '',
      'driver' => 'mysql',
      'prefix' => '',
    ),
  ),
);


Change 'database' => 'DATABASENAME', to your oldsite database name
If your local phpmyadmin has a different pw than root/root then change that too


Hope for the best!  We all know that getting an old site up and running locally can be challenging

Navigate to localhost/oldsite.dev
You will need your old username/password as it is stored in the database to log in




Specific Install for using backup_migrate module:

You will need:
All of the files or ability to git clone the files of the old site 
Backup of the oldsite using backup_migrate module

Create an empty database
Open browser and navigate to localhost/phpmyadmin
Create new database for your site. example: oldsite or old_site

Configure Apache2 for the old site 

sudo nano /etc/hosts
Add under the last line

# Drupal sites
127.0.0.1 oldsite.dev

ctrl o (saves)
enter
ctrl x (exit)

Place the oldsite folder in /var/www/html (or git clone the oldsite to this folder)
	
Navigate to /sites (or /var/www/html if you didn’t create a symlink)
Change the name of your folder to oldsite.dev (basically we are adding .dev and making sure the name of this folder matches the oldsite.dev name in the hosts file)
Navigate into the sites/default folder and delete any old settings.php
Copy default.settings.php and rename it: settings.php
In your browser, navigate to localhost/oldsite.dev
Complete the install process, making sure to complete the section when it asks for the database name and password.

Install backup_migrate module

cd into the root of the oldsite.dev
drush dl backup_migrate
drush en backup_migrate
Log into your site, go to configuration management, and restore backup_migrate database as usual

Common troubleshooting stuff when working with older sites

Reset the admin username/password (assuming admin is the username)
cd to the root of your oldsite.dev
drush user-password admin --password=newpassword

Only the front page shows, 404 on any other nodes
Make sure there is an .htaccess file in the root of the site
If it is missing, simply copy/paste .htaccess into the root of your oldsite.dev from another drupal site


If .htaccess is there and still having issues, check for clean urls
In the browser, navigate to 
localhost/oldsite.dev/?q=admin/config/search/clean-urls
There should be a checkmark and option to run clean urls
For additional clean url troubleshooting: https://www.drupal.org/node/15365
In the meantime, you can still painfully navigate the site with /?q=


Missing images
If you git cloned, your files folder will be empty. Grab the files folder from the old site and place in sites/default/

White screen of death (WOD)
drush updb




Important drush command change

drupal 7:   drush cc all
drupal 8:   drush cr all









