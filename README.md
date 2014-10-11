###Roll-your-own Drupal setup on Ubuntu 14.04
=============
*Note* As of 10/10/14 .. this document has not been completed or formatted. If you need it asap, I have a completed google doc. Find me on freenode #drupal-florida

Complete Ubuntu 14.04 local setup guide for Drupal 7 &amp; 8. Includes lamp, git, composer, drush, and rvm  

**LOCAL set up only!**  Ubuntu 14.04 / LAMP / Drupal sites setup

1. Create a bootable install disk or usb drive, follow directions and install Ubuntu  
2. It is very important to write down or remember your username and pw. You will use these on a regular basis  
3. Open a terminal. Click on the top left icon and type *terminal*.  
4. Run updates (don’t bother with the software updater) This takes a while.  

`sudo apt-get update`  

5. If you want to use a text editor, it is called gedit. Open it the same way as the terminal. Once it is in the sidebar launcher, you can left click and lock.  
6. You can also remove most of the other annoying icons from the launcher so they are not in your way.  

**Give your user permissions!**  

1. `sudo adduser yourusername sudo`   
2. `sudo adduser yourusername www-data`  

* Note: If you are having permission issues, you can also do the following. I do not believe this method is recommended  
1. `sudo visudo`  
2. Using the arrow keys scroll down until you see # User privilege specification  
3. Place cursor under: root ALL=(ALL:ALL) ALL  
4. Add: yourusername ALL=(ALL:ALL) ALL   
5. esc  
6. :wq  

**Create Lamp Stack** [http://www.krizna.com/ubuntu/install-lamp-server-ubuntu-14-04/
](http://www.krizna.com/ubuntu/install-lamp-server-ubuntu-14-04/)

1. `sudo apt-get install apache2`  
2. `sudo nano /etc/apache2/apache2.conf`  
3. Use the arrow key to scroll down to the end of the file and type in:  
`ServerName localhost`  
4. ctrl o (saves)  
5. enter   
6. ctrl x (exit)  
7. You can check to make sure that it saved with   
`cat /etc/apache2/apache2.conf`  
8. Restart apache   
`sudo service apache2 restart`  
9. `sudo chown yourusername:www-data /var/www/html -R`  
10. You can check to make sure it works properly by opening the browser and typing `/var/www/html`  

**Install mysql server**  
1. `sudo apt-get install mysql-server`  
2. It will ask you to create a password. Generally for localhost it is root/root.   
3. Check the service status with   
`sudo /etc/init.d/mysql status`   
4. If your prompt is `mysql>` after checking the status, you can escape with  
`exit`  

**Install php**  
1. `sudo apt-get install php5 php5-mysql`  
2. Create a php file. 
`sudo nano /var/www/html/phpinfo.php`  
3. Add the following code 

```
<?php

phpinfo();

?>  
```

4. ctrl o (saves)  
5. enter   
6. ctrl x (exit)  
7. restart apache2:  
`sudo service apache2 restart`  
8. Open browser and navigate to: *localhost/phpinfo.php*  

**Install phpmyadmin **[https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-phpmyadmin-on-ubuntu-14-04](https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-phpmyadmin-on-ubuntu-14-04)

1. `sudo apt-get install phpmyadmin`  
2. Important! Hit the **spacebar** to choose apache 2. It will show an `*`  
3. Tab (to move to <ok>   
4. Enter  
5. Select Yes to configure with dbconfig-common  
6. Follow the rest with your pw and again root is a common password used for a local host  
7. `sudo php5enmod mcrypt`  
8. Restart apache:  
`sudo service apache2 restart`  
9. Test by navigating in the browser to: *localhost/phpmyadmin*  
10. You can follow the rest of the tutorial for additional securities if you want. I am not sure if this is necessary for local. Of course it would be for a cloud server  
11. Don’t *ever* do this on a live server but local is fine. Drupal php needs access.  
`chmod 775 /var/www/html`  

**Increase max limit php.ini**

1. `sudo nano /etc/php5/apache2/php.ini`  
2. ctrl w (search) memory_limit  
3. change: memory_limit = *512M*  
4. ctrl o (saves)  
5. enter  
6. ctrl x (exit)  

**Create SSH key** (you can replace keys with your old ones after initial set up)

1. `cd ~`  
2. `mkdir .ssh`  
3. `chmod 700 .ssh`  
4. `ssh-keygen -t rsa`  
5. enter, enter, enter (Enter a passphrase if you wish)  
6. *extra* if you want your old keys - put your old keys on a usb drive, navigate to the .ssh folder and replace them  

**Install git**

1. `cd ~`  
2. `sudo apt-get install git`  
3. `git config --global user.name yourname`  
4. `git config --global user.email youremail@domain.com`  
5. `git config --global color.ui auto`  

**Install composer Note! You must install composer before installing drush**

1. ```cd ~```

2. ```sudo apt-get install curl```

3. ```curl -sS https://getcomposer.org/installer | php```

4. ```sudo mv composer.phar /usr/local/bin/composer```

5. ```sed -i '1i export PATH="$HOME/.composer/vendor/bin:$PATH"' $HOME/.bashrc```

6. ```source $HOME/.bashrc```

**Install Drush (must install composer first) **For new drush commands http://www.drushcommands.com/

1. ```cd ~```

2. ```composer global require drush/drush:dev-master```

3. ```composer global update```

**Install IDE (sublime text 3)**

1. ```cd ~```

2. ```sudo add-apt-repository ppa:webupd8team/sublime-text-3```

3. ```sudo apt-get update```

4. ```sudo apt-get install sublime-text-installer```

5. Add in drupal specific preferences [https://drupal.org/node/1346890](https://drupal.org/node/1346890)

**Install rvm**

1. ```\curl -sSL https://get.rvm.io | bash -s stable```

2. ```source ~/.rvm/scripts/rvm```

3. Find current stable version at [www.ruby-lang.org/en/downloads/](http://www.ruby-lang.org/en/downloads/)

4. ```rvm install 2.1.2```  (install current stable version)

5. Change terminal preferences:
edit > profile preferences
Title and Command > checkmark Run command as a login shell
close terminal > reopen

6. Find the latest version www.ruby-lang.org/en/downloads/

7. ```rvm use 2.1.2```  to verify: ```ruby -v```

**Install IRC**

1. ```sudo add-apt-repository ppa:gwendal-lebihan-dev/hexchat-stable```

2. ```sudo apt-get update```

3. ```sudo apt-get install hexchat```

**—— Set up for Sites ——**

**Configure apache to preference .php files over .html files**

1. ```sudo nano /etc/apache2/mods-enabled/dir.conf```

2. Add a parameter reading "index.php" as the first item after "DirectoryIndex"
   
<IfModule mod_dir.c>

          DirectoryIndex **index.php** index.html index.cgi index.pl index.php index.xhtml index.htm

</IfModule>

3. ctrl o (save)

4. enter

5. ctrl x (exit)

6. ```sudo service apache2 restart```

**Enable global site clean url’s**

1.  ```sudo a2enmod rewrite```

2.  ```sudo service apache2 restart```

3.  ```sudo nano /etc/apache2/apache2.conf```

4. Scroll down until you find  
<Directory /var/www/>

		Options Indexes FollowSymLinks

		AllowOverride None

		Require all granted

</Directory>

**Change AllowOverride to all: ** 

<Directory /var/www/>

		Options Indexes FollowSymLinks

		**AllowOverride**** All**

		Require all granted

</Directory>

    5.   ```sudo service apache2 restart```

**Create vhost site configuration files** [http://ben-it.org/content/ubuntu-1204-apache-2-edit-default-virtualhost-enable-modrewrite-module-drupal-clean-urls](http://ben-it.org/content/ubuntu-1204-apache-2-edit-default-virtualhost-enable-modrewrite-module-drupal-clean-urls)

1. ```cd /etc/apache2/sites-available```

2. Create a new vhost config file just for drupal
```sudo nano drupal```

3. Add in the following

NameVirtualHost *:80

<VirtualHost *:80>

   DocumentRoot /var/www

   ServerName localhost

</VirtualHost>

4. ctrl o (saves)

5. enter

6. ctrl x (exit)

Symlink the drupal file in the sites-enabled directory

1. ```cd /etc/apache2/sites-enabled```

2. ```sudo ln -s ../sites-available/drupal . ```

3. ```sudo service apache2 restart```

**Make it easy to navigate to our sites folder by creating a symlink to /var/www/html**

1. ```cd ~```

2. Change "cielo" to your user. If you are unsure of the name, type pwd
```ln -s  /var/www/html /home/cielo/sites```

3. Now you can easily cd sites and you will be directly in the html folder

* Future note: You can create and name a symlink wherever you like

The first path is **what** you are linking

The second path is **where** the link will be

Example: ln -s /what/is/being/linked /where/symlink/goes/nameofsymlink

To remove a symlink:  unlink **sites** (symlink name)

**---- Create a drupal site (or two) ---- works great for drupal 8 ****too**

 **Configure Apache2 for Sites (do this with both site creation methods)**

1. ```sudo nano /etc/hosts```

2. ```Add under the last line```

# Drupal sites
127.0.0.1 example-site.dev

3. ctrl o (saves)

4. enter

5. ctrl x (exit)

* Future note: You will edit this file for every additional site so it will look like this

# Drupal sites
127.0.0.1 example-site.dev

127.0.0.1 newsite2.dev

127.0.0.1 newsite3.dev

**--- Create database and site via gui/git. Scroll down for cl/drush alternate. ---**

**Create database**

1. Open browser and navigate to localhost/phpmyadmin

2. Create new database called example_site

**Git clone site**

1. cd sites (hopefully you made a symlink. If you didn’t then cd /var/www/html)

2. git clone --branch 7.x http://git.drupal.org/project/drupal.git    (drupal 7)
git clone --branch 8.x http://git.drupal.org/project/drupal.git    (drupal 8)

3. Change the name of the cloned drupal directory to the name of the new site
mv drupal example-site.dev

4. ```cd example-site.dev/sites/default```

5. Copy default.settings.php
```cp default.settings.php settings.php```

**Complete Install**

1. Open browser and navigate to *localhost/example-site.dev*

2. Complete install, making sure to fill in database name and password

*Rinse and repeat this page for any ***_new_*** drupal sites =)*

**--- Alternate database and site creation using command line and drush ---**

**Create database**

1. mysqladmin -u root -p create **database_name**

2. Enter your MySQL root password at the prompt.

**Create database user for site via command line**

1. mysql -u root -p

2. Enter your MySQL root password at the prompt.

3. (at mysql>) CREATE USER '**name_of_new_user**'@'localhost' IDENTIFIED BY '**password_of_new_user**';

4. (at mysql>) GRANT ALL PRIVILEGES ON **database_name**.* TO '**name_of_new_user**'@'localhost'; 

5. (at mysql>) FLUSH PRIVILEGES;

6. (at mysql>) exit

**Create site via drush**** (Confirmed on D7. Still needs testing on D8)**

1. drush dl drupal-7.x (can also do drupal-8.x)

2. mv drupal-7.x-dev **site_directory_name**

3. cd **site_directory_name**

4. drush si standard --account-name=admin --account-pass=admin --db-url=mysql://**database_user_name**:**database_user_password**@localhost/**database_name**

**----- Get an old site up and running ****-----****
**

**You will need:**

1. Compressed database dump from old site in (mysql.zip or mysql.gz)

2. All of the files or ability to git clone the files of the old site 
** If you are not able to get a db dump and need to use backup and migrate module, Please follow a different set of directions, posted on page 10.

**Create database**

1. Open browser and navigate to localhost/phpmyadmin

2. Create new database for your site. example: oldsite or old_site

3. Click on the newly created database

4. Choose import, navigate to the compressed db file (mysql.gz) and click go

**Configure Apache2 for the old site **

1. sudo nano /etc/hosts

2. Add under the last line

# Drupal sites
127.0.0.1 oldsite.dev

3. ctrl o (saves)

4. enter

5. ctrl x (exit)

**Place the oldsite folder in /var/www/html** (or git clone the oldsite to this folder)

	

1. Navigate to /sites (or /var/www/html if you didn’t create a symlink)

2. Change the name of your folder to oldsite.dev (basically we are adding .dev and making sure the name of this folder matches the oldsite.dev name in the hosts file)

3. Navigate into the sites/default folder and delete any old settings.php

4. Copy default.settings.php and rename it: settings.php

**Manually link your oldsite to the new database**

1. Open settings.php in sublime or gedit

2. You can copy/paste this in settings.php around line 219. Look for $databases = array();

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

3. Change 'database' => 'DATABASENAME', to your oldsite database name

4. If your local phpmyadmin has a different pw than root/root then change that too

**Hope for the best!****  **We all know that getting an old site up and running locally can be challenging

1. Navigate to localhost/oldsite.dev

2. You will need your old username/password as it is stored in the database to log in

**Specific Install for using backup_migrate module:**

**You will need:**

1. All of the files or ability to git clone the files of the old site 

2. Backup of the oldsite using backup_migrate module

**Create an empty database**

1. Open browser and navigate to localhost/phpmyadmin

2. Create new database for your site. example: oldsite or old_site

**Configure Apache2 for the old site **

1. sudo nano /etc/hosts

2. Add under the last line

# Drupal sites
127.0.0.1 oldsite.dev

3. ctrl o (saves)

4. enter

5. ctrl x (exit)

**Place the oldsite folder in /var/www/html** (or git clone the oldsite to this folder)

	

1. Navigate to /sites (or /var/www/html if you didn’t create a symlink)

2. Change the name of your folder to oldsite.dev (basically we are adding .dev and making sure the name of this folder matches the oldsite.dev name in the hosts file)

3. Navigate into the sites/default folder and delete any old settings.php

4. Copy default.settings.php and rename it: settings.php

5. In your browser, navigate to localhost/oldsite.dev

6. Complete the install process, making sure to complete the section when it asks for the database name and password.

**Install backup_migrate module**

1. cd into the root of the oldsite.dev

2. drush dl backup_migrate

3. drush en backup_migrate

4. Log into your site, go to configuration management, and restore backup_migrate database as usual

**_Common troubleshooting stuff when working with older sites_**

1. Reset the admin username/password (assuming admin is the username)

    1. cd to the root of your oldsite.dev

    2. drush user-password admin --password=**newpassword**

2. Only the front page shows, 404 on any other nodes

    3. Make sure there is an .htaccess file in the root of the site

        1. If it is missing, simply copy/paste .htaccess into the root of your oldsite.dev from another drupal site


    4. If .htaccess is there and still having issues, check for clean urls

        2. In the browser, navigate to 
localhost/oldsite.dev/?q=admin/config/search/clean-urls

        3. There should be a checkmark and option to run clean urls

        4. For additional clean url troubleshooting: [https://www.drupal.org/node/15365](https://www.drupal.org/node/15365)

        5. In the meantime, you can still painfully navigate the site with /?q=


    5. Missing images

        6. If you git cloned, your files folder will be empty. Grab the files folder from the old site and place in sites/default/

    6. White screen of death (WOD)

        7. drush updb

**Important drush command ****change**

1. drupal 7:   drush cc all

2. drupal 8:   drush cr all










