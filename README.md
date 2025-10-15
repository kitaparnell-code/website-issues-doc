## Author
**Kita Parnell**  
Virtual Machine Website Project  
Fall 2025  
# website-issues-doc
ILS Building Documentation with Issues
# Website Development Issues and Resolutions (VM Project)

## Overview
While developing websites on my virtual machine (VM), I encountered several major issues — from accidentally overwriting my WordPress installation to downloading the wrong version of Omeka.  
This document records my workflow, mistakes, troubleshooting steps, and final resolutions for reference and learning.

---

## Issues Summary
- While trying to install Omeka, I overwrote WordPress and lost access.  
- After recovering WordPress, I downloaded the wrong version of Omeka (Omeka S instead of Omeka Classic).

---

## From My Journal

> **Me:** So... somewhere I messed up. My WordPress page is gone. I get an error page.  
> When I go to my web address: [http://35.193.105.7/](http://35.193.105.7/) I get an index page.  
> I did so much work. I had WordPress installed and ready to be used and now it's gone.  
> All I have are zip files and error pages.

---

## Original Workflow

I was in my shell, moving Omeka zip files to the web root and unzipping Omeka:

```bash
sudo unzip omeka.zip
Mistake 1 – Downloaded the Wrong Version
I downloaded Omeka S instead of Omeka Classic.

I then moved the contents of the Omeka folder to /var/www/html:

bash
Copy code
sudo mv omeka/* /var/www/html/
sudo rm -rf omeka
Mistake 2 – Incorrect Permissions Advice
After searching online, I found the following advice and used it to set permissions:

bash
Copy code
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
I thought everything was fine, so I continued to create a MySQL database for Omeka:

sql
Copy code
CREATE DATABASE omeka DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
CREATE USER 'omekauser'@'localhost' IDENTIFIED BY 'omekapassword';
GRANT ALL PRIVILEGES ON omeka.* TO 'omekauser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
Problems Encountered
Tried opening Omeka in Chrome and got nothing.

In my VM, omeka.zip wasn’t found, but I located omeka-s.zip instead.

When I tried sudo unzip omeka.zip, I received errors because the file didn’t exist.

I removed the zip to start over:

bash
Copy code
sudo rm omeka.zip
When I checked my files:

bash
Copy code
ls -l
It showed:

css
Copy code
-rw-r--r-- 1 root www-data 0 Oct 7 11:39 omeka.zip
My zip file had 0 data, confirming it wasn’t downloaded correctly.

Mistake 3 – Cleared the Web Root
I followed Google’s suggestion to start fresh and cleared my web root:

bash
Copy code
sudo rm -rf /var/www/html/*
Then, I downloaded Omeka S again by mistake.
At this point, I realized that both my Omeka and WordPress installations were missing.

“Where’s WordPress? Also, still no data in omeka.zip.”

I needed to figure out what happened to both my OPAC and WordPress.

Troubleshooting WordPress
Step 1 – Reinstall WordPress
Checked all requirements ✅

Verified PHP modules using apt — already installed ✅

Restarted Apache2 and MySQL — all good ✅

Downloaded the latest version of WordPress:

bash
Copy code
sudo wget https://wordpress.org/latest.zip
The download completed successfully.

Step 2 – Unzip and Setup Database
Unzipped the file (lots of inflating and extracting).

Created the database and user:

sql
Copy code
CREATE USER 'wordpress'@'localhost' IDENTIFIED BY 'XXXXXXXXX';
Received an error:

sql
Copy code
ERROR 1356 create user failed
After reviewing, I realized the database and user already existed from my earlier setup.

Step 3 – Configure wp-config.php
Exited MySQL and opened the configuration file:

bash
Copy code
sudo nano wp-config.php
Added the correct database name, username, and password.
Reopened my site at http://35.193.105.7 — success!

When visiting http://35.193.105.7/wordpress/, it redirected properly.

Then, I moved WordPress from /wordpress to /library:

bash
Copy code
sudo mv /var/www/html/wordpress /var/www/html/library
However, that caused a broken link until I corrected the references.

Checked for Errors
Attempted to fix ownership again:

bash
Copy code
sudo chown -R www-data:www-data /var/www/html/library
Still received the same error page.

Then I tried visiting:

ruby
Copy code
http://35.193.105.7/library/wp-admin/install.php
That worked — but my database name didn’t match!
In nano, the DB name was different from my MySQL database list.

Fix
I corrected inconsistencies:

Changed library back to wordpress

Changed all instances of ‘Kita’ to lowercase ‘kita’

After saving and restarting Apache, the site worked! 🎉

Reinstallation of Omeka
After recovering WordPress, I moved on to reinstall Omeka properly.

Step 1 – Update and Confirm Dependencies
bash
Copy code
sudo apt update && sudo apt upgrade -y
sudo apt install imagemagick -y
sudo systemctl restart apache2
Everything was already installed and up-to-date.

Step 2 – Create a New Database for Omeka
Logged into MySQL:

bash
Copy code
sudo mysql -u root -p
Created the database and user:

sql
Copy code
CREATE DATABASE omekadb;
CREATE USER 'omekauser'@'localhost' IDENTIFIED BY 'R3b0ct78#N2p';
GRANT ALL PRIVILEGES ON omekadb.* TO 'omekauser'@'localhost';
FLUSH PRIVILEGES;
Step 3 – Download and Unzip Omeka Classic (the Correct Version)
Changed to the web root directory:

bash
Copy code
cd /var/www/html
Downloaded the correct version of Omeka Classic:

bash
Copy code
sudo wget https://github.com/omeka/Omeka/releases/download/v3.1.2/omeka-3.1.2.zip
sudo unzip omeka-3.1.2.zip
sudo mv omeka-3.1.2 omeka
Success! ✅

Step 4 – Configure Database File
Opened the configuration file:

bash
Copy code
cd /var/www/html/omeka
sudo nano db.ini
Added the following details:

ini
Copy code
user     = "omekauser"
password = "R3b0ct78#N2p"
host     = "localhost"
dbname   = "omekadb"
prefix   = "omeka_"
charset  = "utf8"
Step 5 – Set Ownership and Permissions
bash
Copy code
sudo chown -R www-data:www-data /var/www/html/omeka
sudo chmod -R 755 /var/www/html/omeka
Restarted Apache and opened in the browser:

http://35.193.105.7/omeka/

http://35.193.105.7/omeka/install/

Entered credentials and installed successfully! 🎉

Visited:

http://35.193.105.7/omeka/collections/browse

http://35.193.105.7/omeka/admin

Omeka was working perfectly.

Issues Resolved
While installing Omeka, I overwrote WordPress and lost access. ✅ Fixed by restoring WordPress configuration and database names.

WordPress was in an unreachable subdirectory. ✅ Fixed by adjusting directory structure.

Downloaded the wrong version of Omeka (Omeka S). ✅ Deleted and replaced with Omeka Classic.

Successfully installed and configured both WordPress and Omeka Classic on my VM. 🎯

Reflection
This experience reinforced how important version control, directory management, and careful file handling are when working in a VM environment. I learned to verify file versions, maintain consistent database names, and always back up configurations before making changes.
