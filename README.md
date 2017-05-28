
# Roll-Your-Own Local Drupal Development Environment On Ubuntu 16.04


![alt text](http://drupal.org/files/images/DrupalDiver.png "Florida Drupal Users Group")
**[Florida Drupal Users Group](https://groups.drupal.org/florida)**
**IRC**: Freenode.org **#drupal-florida**


Complete Ubuntu 16.04 local development environment setup guide for Drupal 8. Includes LAMP, git, Composer, Drush, and RVM. Also, a few optional applications are included. (Sublime Text 3, PhpStorm, Node.js, Gulp.js and HexChat) 

**LOCAL set up only!**  Ubuntu 16.04 / LAMP / Drupal Sites Setup

[1. Ubuntu 16.04 Install](#1-download-ubuntu)

[2. Lamp Stack Installation](#2-lamp-stack-installation)

[3. Server Applications Setup](#3-server-applications-setup)

[4. Apache Configuration](#4-apache-configuration)

[5. vhost Setup and Configuration](#5-vhost-setup-and-configuration)

[6. Hosts File Configuration](#6-hosts-file-configuration)

[7. Database Creation And Drupal Installation](#7-database-creation-and-drupal-installation)

[8. IDE Installation](#8-ide-installation)

[9. Optional Applications](#9-optional-applicationsinformation)

[10. Optional Database creation and Drupal Installation](#10-optionaldatabcreation)

---


# 1. Download Ubuntu

1. Create a bootable install disk or usb drive, follow directions and install Ubuntu. [Need help installing Ubuntu?](https://github.com/Lucius1024/Clean-Install-Ubuntu-15-Guide)
2. Open terminal: `Ctrl+Alt+T`
3. Run updates:
```bash
$ sudo apt-get update
```

## Give Your User Permissions!

1. Add your username to the sudo group:
```bash
$ sudo adduser yourusername sudo
```

2. Add your username to the www-data group:

```bash
$ sudo adduser yourusername www-data
```


# 2. Lamp Stack Installation

1. Install Apache:
```bash
$ sudo apt-get install apache2
``` 

2. Open Apache main configuration file:
```bash
$ sudo nano /etc/apache2/apache2.conf
```

3. Use the arrow key to scroll down to the end of the file and type in: `ServerName localhost`  
4. Press **CTRL**+**o** (to save)  
5. Press **Enter**   
6. Press **CTRL**+**x** (to exit)  
7. You can check to make sure that it saved with:
```bash
$ cat /etc/apache2/apache2.conf
```

8. Restart apache:
```bash
$ sudo service apache2 restart
```

9. Change the ownership of /var/www/html to your username:
```bash
$ sudo chown yourusername:www-data /var/www/html -R
```

## Install MySQL server

1. Install MySQL:
```bash
$ sudo apt-get install mysql-server mysql-client
```

2. Create a password. If this is your first time taking these steps, a sample user and password to use would be "root" (without quotes). Proceed to use this for the rest of the guide
3. Check the service status with:
```bash
$ sudo /etc/init.d/mysql status
```

## Install PHP

1. Install PHP: 
```bash
$ sudo apt-get -y install php7.0 libapache2-mod-php7.0
```  

2. Create a PHP file: 
```bash
$ sudo nano /var/www/html/phpinfo.php
```

3. Add the following code:  
```PHP
    <?php  
    
    phpinfo();
    
    ?>
```

4. Press **CTRL**+**o** (to save)  
5. Press **Enter**  
6. Press **CTRL**+**x** (to exit)  
7. Restart Apache:  
```bash
$ sudo service apache2 restart
```

8. Open browser and navigate to: `localhost/phpinfo.php` r enter the following in Terminal:
```bash
$ sudo systemctl status mysql
```

---

## Install phpMyAdmin

1. Install phpMyAdmin:
```Bash
$ sudo apt-get update    
$ sudo apt-get install phpmyadmin php-mbstring php-gettext

```

2. **Important!** Press the **spacebar** to choose Apache 2. An ***** will display.   
3. Press **Tab** (to navigate down the menu to ok)   
4. Press **Enter**  
5. Select *Yes* to configure with dbconfig-common  
6. Follow the prompts
7. Enable mcrypt and mbstrings extentsions:
```Bash
$ sudo phpenmod mcrypt
$ sudo phpenmod mbstring
```

8. Restart Apache:
```Bash
$ sudo service apache2 restart
```

9. Test by navigating in the browser to: `localhost/phpmyadmin`
10. You can follow the rest of the tutorial for additional security; not required for local development  
11. Don’t *ever* do this on a live server but local is fine. Drupal php needs access:
```Bash
$ chmod 777 /var/www/html
```

### Increase Max Limit In php.ini
1. Open the PHP configuration file:
```Bash
$ sudo nano /etc/php/7.0/apache2/php.ini
```

2. Press **CTRL**+**w** (to search) and type `memory_limit`
3. Change to: `memory_limit = 512M`
4. Press **CTRL**+**o** (to save)  
5. Press **Enter**   
6. Press **CTRL**+**x** (to exit)  


### Create SSH key

1. Change to root directory:
```Bash
$ cd ~
```

2. Create .ssh directory:
```Bash
$ mkdir .ssh
```

3. Change directory permissions:
```Bash
$ sudo chmod 700 .ssh
```

4. Create an RSA Key Pair (skip if you already have a key):
```Bash
$ ssh-keygen -t rsa
```

5. Press **Enter** three times (type a passphrase if you wish)  

Note: You will see the following messages to which you can just press enter:
```sh
Enter file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter]
```
```sh
Enter passphrase (empty for no passphrase): [Type a passphrase]
```
```sh
Enter same passphrase again: [Type passphrase again]
```

# 3. Server Applications Setup 

## Install git

1. Change to root directory:
```Bash
$ cd ~
``` 

2. Install git:
```Bash
$ sudo apt-get install git
```

3. Set your name for git identity:
```Bash
$ git config --global user.name yourname
```

4. Set your email for git identity:
```Bash
$ git config --global user.email youremail@domain.com
```

5. To add color to git use this command:
```Bash
$ git config --global color.ui auto
```

6. See all current values:
```Bash
$ git config --list
```

## Install Composer
**Note! You must install composer before installing drush**

1. Change to root directory:
```bash
$ cd ~
```

2. Install cURL:
```bash
$ sudo apt-get install curl
```

3. Download Composer:
```bash
$ curl -sS https://getcomposer.org/installer | php
```

4. Move composer to bin:
```bash
$ sudo mv composer.phar /usr/local/bin/composer
```

5. Add path to the .bashrc:
```bash
$ sed -i '1i export PATH="$HOME/.composer/vendor/bin:$PATH"' $HOME/.bashrc
```

6. Source the .bashrc file:
```bash
source $HOME/.bashrc
```

7. (Optional) Check your current version of Composer
```bash
composer --version
```

## Install Drush (must install composer first)
**For additional drush commands visit [www.drushcommands.com](http://www.drushcommands.com)**

### Installing Drush 8 (note, you may or may not need to precede each command with `sudo` based on your user permissions)
1. Change to your bin directory
```bash
$ cd /usr/local/bin
```

2. create the drush-8 directory
```bash
$ mkdir drush-8
```

3. Change to the new drush-8 directory
```bash
$ cd drush-8
```

4. Install Drush 8 in the current directory
```bash
$ composer require drush/drush:8.x 
```

5. Create a symlink to drush-8 to enable global calling of the `drush8` command
```bash
$ ln -s /usr/local/bin/drush-8/vendor/bin/drush /usr/local/bin/drush8
```

6. (Optional) Check your current version of drush8
```bash
$ drush8 --version
```

### Installing Drush 7 (note, you may or may not need to precede each command with `sudo` based on your user permissions)
1. Change to your bin directory
```bash
$ cd /usr/local/bin
```

2. create the drush-7 directory
```bash
$ mkdir drush-7
```

3. Change to the new drush-7 directory
```bash
$ cd drush-7
```

4. Install Drush 7 in the current directory
```bash
$ composer require drush/drush:7.x 
```

5. Create a symlink to drush-7 to enable global calling of the `drush7` command
```bash
$ ln -s /usr/local/bin/drush-7/vendor/bin/drush /usr/local/bin/drush7
```

6. (Optional) Check your current version of drush7
```bash
$ drush7 --version
```

### Write a script to change between Drush 7 and 8 automatically for your Drupal sites
1. Change to your bin directory and create a new drush file
```bash
$ cd /usr/local/bin
```
```bash
$ sudo nano drush
```

2. Insert this script in the newly created file
```Shell
#!/bin/sh
version=$(git config --get drush.version)
if [ "$version" = '7' ];
then
drush7 "$@"
else
drush8 "$@"
fi
```
Hit Ctrl+O to save the file
Hit Enter
Hit Ctrl+X to close the file

# 4. Apache Configuration
### Configure Apache To Preference .php Files Over .html Files
1. Open /etc/apache2/mods-enabled/dir.conf for editing:

```bash
$ sudo nano /etc/apache2/mods-enabled/dir.conf
```

2. Add a parameter reading "index.php" as the first item after "DirectoryIndex":

```PHP
    <IfModule mod_dir.c>
    
              DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
    
    </IfModule>
```
Note: index.php may be listed later in the line. Delete the second instance of it.

3. Press **CTRL**+**o** (to save)  
4. Press **Enter**   
5. Press **CTRL**+**x** (to exit)
6. Restart Apache:
    
```Bash
$ sudo service apache2 restart
```

---

### Enable Global Site Clean URL’s
1. Enable the rewrite module for Apache:

```Bash
$ sudo a2enmod rewrite
```

2. Restart Apache:
    
```Bash
$ sudo service apache2 restart
```

3. Open /etc/apache2/apache2.conf for editing:

```Bash
$ sudo nano /etc/apache2/apache2.conf
```

4. Scroll down until you find:

```Apache
    <Directory /var/www/>
    
		    Options Indexes FollowSymLinks
    
	    	AllowOverride None
        
	    	Require all granted
    
    </Directory>
```

5. Change `AllowOverride None` to `AllowOverride All`:

```Apache
    <Directory /var/www/>

	    	Options Indexes FollowSymLinks
    
		    AllowOverride All
    
		    Require all granted
    
    </Directory>
```

5. Restart Apache:

```Bash
$ sudo service apache2 restart
```

---

# 5. vhost Setup and Configuration

1. Change to the /etc/apache2/sites-available directory:

```Bash
$ cd /etc/apache2/sites-available
```

2. Create and open a new vhost config file just for drupal:
    
```Bash
$ sudo nano drupal
```

3. Add in the following

```Apache
    NameVirtualHost *:80
    
    <VirtualHost *:80>
    
       DocumentRoot /var/www
    
       ServerName localhost
    
    </VirtualHost>
```

3. Press **CTRL**+**o** (to save)  
4. Press **Enter**   
5. Press **CTRL**+**x** (to exit)

Create Symlinks for the drupal file in the sites-enabled directory

1. Change to the /etc/apache2/sites-enabled directory:
    
```Bash
$ cd /etc/apache2/sites-enabled
```

2. Create a symlink to ../sites-available/drupal in /etc/apache2/sites-enabled:

```
$ sudo ln -s ../sites-available/drupal .
```

3. Restart Apache
    
```Bash
$ sudo service apache2 restart
```

---

### Easy Navigate To Sites Directory By Creating A Symlink To /var/www/html

1. Change to root directory:

```bash
$ cd ~
```

2. Change "foo" to your user. (If you are unsure of the name, type `pwd` in the command line):
    
```Bash
$ ln -s  /var/www/html /home/foo/sites
```

Now you can easily cd sites and you will be directly in the html folder

**Future note**: You can create and name a symlink wherever you like
* The first path is **what** you are linking
* The second path is **where** the link will be

Example:
    
```Bash
$ ln -s /what/is/being/linked /where/symlink/goes/nameofsymlink
```

To remove a symlink: unlink **sites** (symlink name)

---
# 6. Hosts File Configuration
Configure Apache For Sites **(Follow this for either site creation methods.)**

1. Open /etc/hosts file for editing:

```Bash
$ sudo nano /etc/hosts
```

2. Add the follow to the last line:
    
```
# Drupal sites
127.0.0.1 newsite.dev
```

3. Press **CTRL**+**o** (to save)  
4. Press **Enter**   
5. Press **CTRL**+**x** (to exit)

**Future note**: You will edit this file for every additional site so it will look like this:

```
# Drupal sites
127.0.0.1 newsite.dev

127.0.0.1 newsite2.dev

127.0.0.1 newsite3.dev
```

---

# 7. Database Creation And Drupal Installation
### Create Database And Site Via GUI/git
#### Create Database
1. Open browser and navigate to *http://localhost/phpmyadmin*
2. Create new database called **newsite**

#### Git Clone Site
Note: you shouldn't need "sudo" for these steps. If you get permission errors, please check the permissions for /var/www/html
1. Change to your sites directory: (Hopefully you created a symlink. If you didn’t then use `$ cd /var/www/html`):

```Bash
$ cd sites
```

2. Clone Drupal 8:
- Go to https://drupal.org/project/drupal to verify the latest version.
- Click the "Version Control" tab.
- Select the correct version from the "Version to work from" drop down.
- Your git clone will look similar to the following:
```Bash
$ git clone --branch 8.3.x https://git.drupal.org/project/drupal.git
```

3. Change the name of the cloned drupal directory to the name of the new site:
    
```Bash
$ mv drupal newsite.dev
```

4. Change to newsite.dev/sites directory:
    
```
$ cd newsite.dev/sites
```
5. For Pantheon.io sites, copy example.settings.local.php to default and name it settings.local.php

```Bash
$ cp example.settings.local.php default/settings.local.php
```

6. Change to the default directory. Copy default.settings.php and rename as settings.php
    
```Bash
$ cd default
```
```Bash
$ cp default.settings.php settings.php
```

7. Create the Directory Files
    
```Bash
$ mkdir files
```

8. Change file permissions
    
```Bash
$ chmod 777 files
```
```Bash
$ chmod 777 settings.php
```
    
```Bash
$ chmod 777 settings.local.php
```

#### Complete Install
1. Open browser and navigate to *localhost/newsite.dev*
Note: If you are getting a blank white screen here (called the White Screen of Death), try using an earlier version of Drupal, such as 8.0.x
2. Complete install, making sure to fill in database name and password
3. At any time, to update your Drupal site, run the following Drush command from the root of your site directory (replace X.X.X with the desired version)
```bash
$ drush pm-update projects drupal-X.X.X
```


*Rinse and repeat this section for __new__ drupal sites you may create*



# 8. IDE Installation

### Install IDE (Sublime Text 3)
Sublime Text is a sophisticated text editor for code, html and prose. 

1. Change to root directory:

```bash
$ cd ~
```

2. Add the WebUpd8 Sublime Text 3 (beta) PPA:
    
```bash
$ sudo add-apt-repository ppa:webupd8team/sublime-text-3
```

3. Download the package lists from the repositories and "update" them to get information on the newest versions of packages and their dependencies. It will do this for all repositories and PPAs.

```bash
$ sudo apt-get update
```

4. Install Sublime Text 3:
    
```bash
$ sudo apt-get install sublime-text-installer
```

5. Add in drupal specific preferences [https://drupal.org/node/1346890](https://drupal.org/node/1346890)

---

### Configuration Information For PhpStorm Users
* **[Setting up PhpStorm for Drupal's Coding Standards (Drupal.org)](https://www.drupal.org/node/1962108)**
* **[Drupal Development using PhpStorm (PhpStorm Documentation)](https://confluence.jetbrains.com/display/PhpStorm/Drupal+Development+using+PhpStorm#DrupalDevelopmentusingPhpStorm-CoderandPHPCodeSnifferIntegration)**

---

# 9. Optional Applications/Information

### Install Terminator

1. Change to root directory:

```bash
$ cd ~
```

2. Add the Terminator Nightly Builds PPA:
    
```bash
$ sudo add-apt-repository ppa:gnome-terminator/nightly
```

3. Download the package lists from the repositories and "update" them to get information on the newest versions of packages and their dependencies. It will do this for all repositories and PPAs.

```bash
$ sudo apt-get update
```

4. Install Terminator:
    
```bash
$ sudo apt-get install terminator
```

### Alternate Database And Site Creation Using Command Line And Drush
#### Create Database
1. Create a database, Replace database_name with the name of your choice.
    
```Bash
$ mysqladmin -u root -p create database_name
```

2. Enter your MySQL root password at the prompt.

---

#### Create database user for site via command line
1. Open the mySQL client using root:
    
```Bash
$ mysql -u root -p
```

2. Enter your MySQL root password at the prompt.

3. Create a user:

```mySQL
mysql> CREATE USER 'name_of_new_user'@'localhost' IDENTIFIED BY 'password_of_new_user';
```

4. Grant *name_of_new_user* privileges to *database_name*:
    
```mySQL
mysql> GRANT ALL PRIVILEGES ON 'database_name'.* TO 'name_of_new_user'@'localhost';
```

5. Reload the grant tables:
    
```mySQL
mysql> FLUSH PRIVILEGES;
```

6. Exit mySQL
    
```mySQL
mysql> quit
```

---

### Install RVM

RVM (“Ruby Version Manager”) Required for theming with sass/compass
RVM allows you to install and manage multiple installations of Ruby on your system. It can also manage different gemsets. It is available for OS X, Linux, or other UNIX-like operating systems.

1. Download signatures:

```Bash
$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
```

2. Download RVM

```Bash
$ curl -sSL https://get.rvm.io | bash -s stable
```

3. Source ~/.rvm/scripts/rvm:

```Bash
$ source ~/.rvm/scripts/rvm
```

4. Find current stable version at [www.ruby-lang.org/en/downloads/](http://www.ruby-lang.org/en/downloads/)
5. Install current stable version of RVM:

```Bash
$ rvm install 2.2.2
```

6. Change terminal preferences by going to: Edit > Profile Preferences Title and Command
7. Check the box to **Run command as a login shell**.
8. Close terminal and reopen.
9. Find the latest version www.ruby-lang.org/en/downloads/
10. Switch to Ruby 2.2.2:

```bash
$ rvm use 2.2.2
```

11. Verify Ruby version:
    
```Bash
$ ruby -v
```

---

### Install Node.js
* **[http://www.hostingadvice.com/how-to/install-nodejs-ubuntu-14-04/#ubuntu-package-manager](http://www.hostingadvice.com/how-to/install-nodejs-ubuntu-14-04/#ubuntu-package-manager)**

Install Options

* Option 1 is the recommended method for the masses, as it should be stable and secure. 
* Options 2, 3, and 4 have the advantage of keeping your node and npm packages the most curren

#### Option 1: Install the standard Debian/Ubuntu packages for “node” and “npm”.
1. To install Node.js, open a terminal and type the following command:

```bash
$ sudo apt-get install nodejs
```

2. Then install the Node package manager called “npm”:

```bash
$ sudo apt-get install npm
```
3. Create a symbolic link for “node” as many Node.js tools use this name to execute.

```bash
$ sudo ln -s /usr/bin/nodejs /usr/bin/node
```

4. Now we should have both the node and npm commands working:

```bash
$ node -v
v0.10.25
```

```bash
   $ npm -v
   1.3.10
```

#### Option 2: Install from Debian/Ubuntu packages created by the Node.js (associated) team.
The process below is described here, too.

1. Add the Node.js maintained repositories to your Ubuntu package source list with this command:

```bash
$ curl -sL https://deb.nodesource.com/setup | sudo bash -
```

2. Then install Node.js with apt-get:

```bash
$ sudo apt-get install nodejs
```

3. Optionally we can create a symbolic link for “node” (for reasons mentioned earlier):

```bash
$ sudo ln -s /usr/bin/nodejs /usr/bin/node
```

4. Using this install option, we end up with newer versions of “nodejs” and “npm”:

```bash
$ node -v
v0.10.35
$ npm -v
1.4.28
```

#### Option 3: Install Node.js manually from standard binary packages on the official website.
Go to the official Node.js download page and download either the 32-bit or 64-bit Linux binary file, depending on your system type.

1. You can check what CPU architecture your server has with these commands:

```bash
$ getconf LONG_BIT
64
$ uname -p
x86_64
```

2. You can download this file from the browser or from the console. The latter is shown below (note: the specific Node.js version might be different for you):

```bash
$ wget http://nodejs.org/dist/v0.12.0/node-v0.12.0-linux-x64.tar.gz
```

3. From a console window, go to the directory where the Node.js binary was downloaded to, and then execute the following command to install the Node.js binary package in “/usr/local/”:

```bash
$ sudo tar -C /usr/local --strip-components 1 -xzf node-v0.10.34-linux-x86.tar.gz
```

4. You should now have both node and npm installed in “/usr/local/bin”. You can check this typing:

```bash
$ ls -l /usr/local/bin/node
$ ls -l /usr/local/bin/npm
```

#### Option 4: Install Node.js from the [Github source repository](https://github.com/joyent/node).
#### Useful Note Regarding Older "node" Packages When Updating

On Ubuntu, the “nodejs” package has a similar name to the older one named “node”. The old one is an amateur packet radio program you can more than likely remove.

If you already have “node” installed, you might want to remove it. Some Node.js tools might execute Node.js as “node” instead of “nodejs” causing conflicts.

You can look for and remove the “node” package by executing these commands in a terminal. To access a terminal, navigate the desktop menu to: **Applications -> Accessories -> Terminal**.

Run this command and if it says “install” in the right column, “node” is on your system:

```bash
$ dpkg --get-selections | grep node
ax25-node                                       install
node                                            install
```

If you found the old “node” package installed, run this command to completely remove it:

```bash
$ sudo apt-get remove --purge node
```

---

### Gulp.js Streaming Build System
* **[Gulp Documentation on GitHub](https://github.com/gulpjs/gulp/blob/master/docs/getting-started.md)**
* **[Automated Theming Workflow With Gulp](http://www.blinkreaction.com/blog/automated-theming-workflow-with-gulp)**

1. Install gulp globally:

```sh
$ npm install --global gulp
```

2. Install gulp in your project devDependencies:

```sh
$ npm install --save-dev gulp
```

3. Create a `gulpfile.js` at the root of your project:

```js
var gulp = require('gulp');

gulp.task('default', function() {
  // place code for your default task here
});
```

4. Run gulp:

```sh
$ gulp
```

The default task will run and do nothing.
To run individual tasks, use `gulp <task> <othertask>`.

#### Where do I go now?
You have an empty gulpfile and everything is installed. How do you REALLY get started? Check out the [recipes](https://github.com/gulpjs/gulp/blob/master/docs/recipes) and the [list of articles](https://github.com/gulpjs/gulp/blob/master/docs/README.md#articles) for more information.
#### .src, .watch, .dest, CLI args - How do I use these things?
For API specific documentation you can check out the [documentation for that](https://github.com/gulpjs/gulp/blob/master/docs/API.md).
#### Available Plugins
The gulp community is growing, with new plugins being added daily. See the [main website](http://gulpjs.com/plugins/) for a complete list.

---

### Install IRC (HexChat)
1. Add the HexChat PPA:
    
```Bash
$ sudo add-apt-repository ppa:gwendal-lebihan-dev/hexchat-stable
```

2. Download the package lists from the repositories and "update" them to get information on the newest versions of packages and their dependencies. It will do this for all repositories and PPAs
    
```Bash
$ sudo apt-get update
```

3. Install HexChat:
    
```Bash
$ sudo apt-get install hexchat
```

# 10 Optional Database Creation and Drupal installation

---

### Create site via drush
1. Change to your sites directory: (Hopefully you created a symlink. If you didn’t then use $ cd /var/www/html):

```Bash
$ cd sites
```

2. Download Drupal:

```Bash
$ sudo drush dl drupal --drupal-project-rename=site_directory_name
```

3. Change to the site_directory_name
    
```Bash
$ cd site_directory_name
```

4. Drupal Site Install. This will also create your settings.php file:
    
```Bash
$ drush si standard --account-name=admin --account-pass=admin --db-url=mysql://database_user_name:database_user_password@localhost/database_name
```
Note: as mentioned in previous steps, if this was your first time running through this, replace your account-name, account-pass, database_user_name, and database_user_password with the sample "root"

---

### Get an old site up and running
#### You will need:
1. Compressed database dump from old site in (mysql.zip or mysql.gz)
2. All of the files or ability to git clone the files of the old site 
** If you are not able to get a db dump and need to use backup and migrate module, Please follow a different set of directions, posted on page 10.

#### Create database
1. Open browser and navigate to localhost/phpMyAdmin
2. Create new database for your site. Example: oldsite or old_site
3. Click on the newly created database
4. Choose import, navigate to the compressed db file (mysql.gz) and click go

#### Configure Apache2 for the old site
1. Open the Hosts file:

```bash
$ sudo nano /etc/hosts
```

2. Add under the last line

```
# Drupal sites
127.0.0.1 oldsite.dev
```

3. Press **CTRL**+**o** (to save)

4. Press **Enter**

5. Press **CTRL**+**x** (to exit)

#### Place the oldsite folder in /var/www/html (or git clone the oldsite to this folder)
1. Navigate to /sites (or /var/www/html if you didn’t create a symlink)
2. Change the name of your folder to **oldsite.dev** (basically we are adding .dev and making sure the name of this folder matches the oldsite.dev name in the hosts file)
3. Navigate into the sites/default folder and delete any old settings.php
4. Copy default.settings.php and rename it: **settings.php**

#### Manually link your oldsite to the new database
1. Open the settings.php file:

```bash
$ sudo nano sites/default/settings.php
```

2. You can copy/paste this into settings.php around line 219. Look for:

```php
    $databases = array();
    
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
```  

3. Change `'database' => 'DATABASENAME',` to your oldsite database name
4. If your local phpMyAdmin has a different pw than root/root then change that too
5. Press **CTRL**+**o** (to save)
6. Press **Enter**
7. Press **CTRL**+**x** (to exit)

#### Hope for the best! We all know that getting an old site up and running locally can be challenging
1. Navigate to localhost/oldsite.dev
2. You will need your old username/password as it is stored in the database to log in

---

### Specific Install for using backup_migrate module
#### You will need:
1. All of the files or ability to git clone the files of the old site 
2. Backup of the oldsite using backup_migrate module

#### Create an empty database
1. Open browser and navigate to localhost/phpmyadmin
2. Create new database for your site. example: oldsite or old_site

#### Configure Apache2 for the old site
1. Open the Hosts file:

```bash
$ sudo nano /etc/hosts
```

2. Add under the last line

```
# Drupal sites
127.0.0.1 oldsite.dev
```

3. Press **CTRL**+**o** (to save)
4. Press **Enter**
5. Press **CTRL**+**x** (to exit)

#### Place the oldsite folder in /var/www/html (or git clone the oldsite to this folder)
1. Navigate to /sites (or /var/www/html if you didn’t create a symlink)
2. Change the name of your folder to **oldsite.dev** (basically we are adding .dev and making sure the name of this folder matches the oldsite.dev name in the hosts file)
3. Navigate into the sites/default folder and delete any old settings.php
4. Copy default.settings.php and rename it: **settings.php**
5. In your browser, navigate to *localhost/oldsite.dev*
6. Complete the install process, making sure to complete the section when it asks for the database name and password.

#### Install backup_migrate module
1. cd into the root of the oldsite.dev
2. Download the backup_migrate module:

```Bash
$ drush dl backup_migrate
```

3. Enable the backup_migrate module:
    
```Bash
$ drush en backup_migrate
```

4. Log into your site, go to configuration management, and restore backup_migrate database as usual

---

### Common troubleshooting stuff when working with older sites
1. Reset the admin username/password (assuming admin is the username)
  1. cd to the root of your oldsite.dev
  2. Set username and password: 

```bash
$ drush user-password admin --password=newpassword
```

2. Only the front page shows, 404 on any other nodes
  1. Make sure there is an .htaccess file in the root of the site
    * If it is missing, simply copy/paste .htaccess into the root of your oldsite.dev from another drupal site
  2. If .htaccess is there and still having issues, check for clean urls
        2. In the browser, navigate to *localhost/oldsite.dev/?q=admin/config/search/clean-urls*
        3. There should be a checkmark and option to run clean urls
        4. For additional clean url troubleshooting: [https://www.drupal.org/node/15365](https://www.drupal.org/node/15365)
        5. In the meantime, you can still painfully navigate the site with /?q=
  3. Missing images
    * If you git cloned, your files folder will be empty. Grab the files folder from the old site and place in sites/default/
  4. White screen of death (WOD):

```bash
$ drush updb
```

---

### Important drush command change

#### Drupal 8:

```bash
	$ drush cr all
```

---
