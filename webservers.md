# Webserver Tips and Tricks
## interactive installation of LAMP stack
[source](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-20-04-de)

Given **L**inux is provided pre-install from an Ubuntu server 20.04 LTS image and running

1. SSH to server guest OS `ssh user@server`

2. install **A**pache
    ```
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
```
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
```sql
CREATE DATABASE example_database;
CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'banana';
GRANT ALL ON example_database.* TO 'example_user'@'%';
exit
```
as new user: `mysql -u example_user -p`  
```sql
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

