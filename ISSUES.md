# My Web Restoration Journey
# Kita Parnell, Fall 2025
## Issues Encountered

While trying to install **Omeka**, I accidentally overwrote **WordPress** and lost access to my site.  
After recovering WordPress, I realized I had downloaded the wrong version of Omeka.

---

## From My Journal

> So… somewhere I messed up. My WordPress page is gone. I get an error page.  
> When I go to my web address: `http://35.193.105.7/`, I get an index page.  
> I did so much work. I had WordPress installed and ready to be used, and now it’s gone.  
> All I have are zips and error pages.

---

## My Original Workflow

I started by moving the Omeka zip files to the web root and unzipping:

```bash
sudo unzip omeka.zip
Mistake 1 – Downloaded Omeka S Instead of Omeka Classic
Then I moved the contents to the web root:

bash
Copy code
sudo mv omeka/* /var/www/html/
sudo rm -rf omeka
Mistake 2 – Permissions Adjustments Gone Wrong
I followed advice from Google to set permissions:

bash
Copy code
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
Everything seemed fine, so I went on to create a MySQL database for Omeka:

sql
Copy code
CREATE DATABASE omeka DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
CREATE USER 'omekauser'@'localhost' IDENTIFIED BY 'omekapassword';
GRANT ALL PRIVILEGES ON omeka.* TO 'omekauser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
After restarting Apache, I tried opening Omeka in Chrome — nothing.
The omeka.zip file wasn’t found. I located omeka-s.zip and tried unzipping it again, but errors appeared.
That’s when I realized I had the wrong version of Omeka.

Then I ran:

bash
Copy code
sudo rm omeka.zip
ls -l
Output:

css
Copy code
-rw-r--r-- 1 root www-data 0 Oct 7 11:39 omeka.zip
My zip file had 0 bytes — it was empty.

Mistake 3 – Clearing My Web Root
I followed another online suggestion to start fresh:

bash
Copy code
sudo rm -rf /var/www/html/*
This deleted everything, including WordPress.
Downloaded Omeka S again. Still no luck.
At this point, I had no working WordPress or Omeka.

Troubleshooting WordPress
I decided to reinstall WordPress.

Checked requirements — all good. Installed additional PHP modules (already present). Restarted Apache and MySQL successfully.

Downloaded WordPress:

bash
Copy code
sudo wget https://wordpress.org/latest.zip
sudo unzip latest.zip
Created a database and user:

sql
Copy code
CREATE DATABASE wordpress;
CREATE USER 'wordpress'@'localhost' IDENTIFIED BY 'XXXXXXXX';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress'@'localhost';
FLUSH PRIVILEGES;
I edited the wp-config.php file with my database details:

bash
Copy code
sudo nano wp-config.php
When I opened my site at http://35.193.105.7, I got the setup page — success!

Then I moved the directory:

bash
Copy code
sudo mv /var/www/html/wordpress /var/www/html/library
But I still got an error. I realized my database name and username weren’t consistent — some were capitalized differently (“Kita” vs “kita”). After correcting these in Nano, the site finally loaded again. 🎉

Reinstalling Omeka (Correctly This Time)
Time for a fresh start with Omeka Classic.

Updated Apache and verified dependencies:

bash
Copy code
sudo apt update && sudo apt upgrade -y
sudo apt install imagemagick -y
sudo systemctl restart apache2
Created a new database:

sql
Copy code
CREATE DATABASE omekadb;
CREATE USER 'omekauser'@'localhost' IDENTIFIED BY 'R3b0ct78#N2p';
GRANT ALL PRIVILEGES ON omekadb.* TO 'omekauser'@'localhost';
FLUSH PRIVILEGES;
Downloaded the correct Omeka version:

bash
Copy code
cd /var/www/html
sudo wget https://github.com/omeka/Omeka/releases/download/v3.1.2/omeka-3.1.2.zip
sudo unzip omeka-3.1.2.zip
sudo mv omeka-3.1.2 omeka
Configured Omeka’s database file:

bash
Copy code
cd /var/www/html/omeka
sudo nano db.ini
Inside:

ini
Copy code
user     = "omekauser"
password = "R3b0ct78#N2p"
host     = "localhost"
dbname   = "omekadb"
prefix   = "omeka_"
charset  = "utf8"
Set ownership and permissions:

bash
Copy code
sudo chown -R www-data:www-data /var/www/html/omeka
sudo chmod -R 755 /var/www/html/omeka
Then opened in browser:

http://35.193.105.7/omeka/

http://35.193.105.7/omeka/install/

It worked! Installation complete.

After logging into the admin side, I changed the site theme and created a test collection.

Issues Resolved
Accidentally overwrote WordPress — recovered by reinstalling and fixing directory names
Wrong Omeka version (Omeka S) — replaced with Omeka Classic
Empty zip file — confirmed proper download via wget
Database inconsistencies — corrected capitalization and database references
Permissions — verified correct ownership for Apache

Lessons Learned
Always confirm which version of software you’re downloading (Omeka Classic ≠ Omeka S).

Avoid clearing /var/www/html without backups.

Keep naming consistent — even capitalization matters.

Test in small steps before major deletions or overwrites.

Documentation saves you — this journal made it possible to retrace every fix.

“I breathed!” — the exact moment when everything finally worked again.
