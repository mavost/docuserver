# Web server tips and tricks

## interactive installation of LAMP stack

[source](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-20-04-de)

Given **L**inux is provided pre-install from an Ubuntu server 20.04 LTS image and running

1. SSH to server guest OS `ssh user@server`

2. install **A**pache

   ``` {bash}
   sudo apt update  
   sudo apt install apache2
   ```

3. install **M**ySQL  
   - `sudo apt install mysql-server`
   - `sudo mysql_secure_installation`  
     setting default PW,  
     disabling high-level security,  
     selecting Y on all further questions

4. install **P**hP  
    `sudo apt install php libapache2-mod-php php-mysql`

5. install ImageMagick  
    `sudo apt install imagemagick`  
    and test it:  
    `convert logo: logo.gif`

## setting up a virtual host server page

`sudo mkdir /var/www/your_domain`  
`sudo chown -R $USER:$USER /var/www/your_domain`  
`sudo nano /etc/apache2/sites-available/your_domain.conf`  

``` {bash}
<VirtualHost *:80>
    ServerName your_domain
    ServerAlias www.your_domain
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/your_domain
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

`sudo a2ensite your_domain`  
`sudo a2dissite 000-default`  
`sudo apache2ctl configtest`  
`sudo systemctl reload apache2`  

## connecting the database

`sudo mysql`  

``` {sql}
CREATE DATABASE example_database;
CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'banana';
GRANT ALL ON example_database.* TO 'example_user'@'%';
exit
```

as new user: `mysql -u example_user -p`  

``` {sql}
SHOW DATABASES;
CREATE TABLE example_database.todo_list (
    item_id INT AUTO_INCREMENT,
    content VARCHAR(255),
    PRIMARY KEY(item_id)
);
USE example_database;
SHOW TABLES;
INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
SELECT * FROM example_database.todo_list;
```

---

## Small guide for web-hosting using the MDwiki template

1. in a folder I started with a bunch of ".md" files and subfolders containing pictures
2. in those files I edited the Markdown format to match the syntax needed by MDwiki
   - no *xml*, *console*, *shell*, *property* tags in code blocks
   - eliminated manual arrows "-->"
   - indented code blocks may not contain manual horizontal rulers "--------"
3. copied the **mdwiki.html** file to folder and renamed it to **index.html**
4. some basic configuration for the wiki
   - created a **navigation.md** containing the links to all Markdown files
   - also an **about.md** and **index.md** for further info
   - added symbolic link **home.md** &rightarrow; **index.md**
   - **Readme.md** is a copy of **index.md**
   - setting the theme
   - either manually by adding a specific theme to **navigation.md**

   ``` {bash}
   [gimmick:theme](slate)
   ```

   or
   - by adding a theme chooser option

     ``` {bash}
     [gimmick:themechooser](Choose theme)
     ```

   - added a **config.json** file containing some basic settings
5. results and edits are pushed to [Github](https://github.com/mavost/docuserver)
6. given a web server is installed and running the folder is copied to */var/www/landing_page*
or checked out via  `git clone https://github.com/mavost/docuserver`
