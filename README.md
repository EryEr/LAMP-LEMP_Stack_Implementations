# LAMP-LEMP_Stack_Implementations
**LAMP(Linux, Apache, MySQL,PHP)**<br>


An AWS EC2(Elastic Compute Cloud) instance(there are enough examples on youtube to set an instance.)   <br>
Save this instance's private key(LAMP.pem) in a local folder. Hit the below code in the terminal in this local folder.<br><br>
`ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>`<br><br>
`ssh -i "LAMP.pem" ubuntu@ec2-13-40-193-105.eu-west-2.compute.amazonaws.com`<br><br>
This will allow us to connect to Ubuntu from our local folder terminal in this AWS instance.<br><br>

We may use `sudo chmod 0400 LAMP.pem`  to avoid Bad permission errors. <br><br>

Update a list of packages in the package manager. <br>
`sudo apt update` <br><br>
This updates all applications. <br>
apt--> (advance package manager) Debian types like Ubuntu, Linux mint... <br>
yum-->(yellow-dog updater, modified) RPM-based Linux like Red Hat and CentOS, <br>
akp-->Alpine Linux using this)<br><br>
Run apache2 package installation<br>
`sudo apt install apache2`<br><br>
To verify a healthy running apache:<br>
`sudo systemctl status apache2` A green colour is a sign of a good run.<br><br>
<img width="725" alt="Screenshot 2023-08-01 at 00 55 48" src="https://github.com/EryEr/LAMP-LEMP_Stack_Implementations/assets/138815393/adf1cda2-828d-428a-91f3-f244409f59bf"><br><br>

To be able to see the content of our server in our local Ubuntu shell:<br>
`curl http://localhost:80`<br><br>
<img width="922" alt="Screenshot 2023-08-01 at 00 57 13" src="https://github.com/EryEr/LAMP-LEMP_Stack_Implementations/assets/138815393/2499abf6-dacd-47ec-ab14-3545c681e660"><br>

To be able to see the content in a browser:<br>
`http://<Public-IP-Address>:80`<br><br>



Linux(Comming with AWS instance type) and Apache are done, and now we need to set MySQL <br>
`sudo apt install mysql-server` `Y`, and then `Enter` then `sudo mysql`<br><br>

The content below is verification of mysql installation.<br>
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.22-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.<br><br>

Setting a password for the root user.<br>
`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`<br><br>

For PHP<br>
`sudo apt install php libapache2-mod-php php-mysql` then `php -v` to verify the version.<br><br>
<img width="646" alt="Screenshot 2023-08-01 at 00 58 56" src="https://github.com/EryEr/LAMP-LEMP_Stack_Implementations/assets/138815393/01a9f381-f1d8-44ad-8187-70ca6d85ed8a"><br>

<img width="1125" alt="Screenshot 2023-08-01 at 01 00 26" src="https://github.com/EryEr/LAMP-LEMP_Stack_Implementations/assets/138815393/783e51b4-a817-4aae-a60e-6b0976cdb490"><br>


`sudo mkdir /var/www/projectlamp`<br>
`sudo chown -R $USER:$USER /var/www/projectlamp`<br>
`sudo vim /etc/apache2/sites-available/projectlamp.conf`<br>
`<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>`<br><br>



`sudo ls /etc/apache2/sites-available`<br>
You will see something like this<br>
`000-default.conf`  `default-ssl.conf`  `projectlamp.conf`<br><br>


`sudo a2ensite projectlamp`<br><br>

Create an index file into the prejectlamp then copy this<br>

`sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html`<br><br>

<img width="899" alt="Screenshot 2023-07-20 at 19 34 34" src="https://github.com/EryEr/LAMP-LEMP_Stack_Implementations/assets/138815393/7a4cda8f-93e5-4dc3-ab96-5fa492dbf5dc"><br>

**Enable PHP on the website**<br><br>
To be able to change the index file and put index.php to the prior one, we need to run the commands below.<br>
`sudo vim /etc/apache2/mods-enabled/dir.conf`<br>
`<IfModule mod_dir.c>`<br>
        `#Change this:`<br>
        `#DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm`<br>
        `#To this:`<br>
        `DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm`<br>
`</IfModule>`<br>
`sudo systemctl reload apache2`<br><br>
After setting these commands now, we can create our  php index file, and we can create simple content.<br>

`vim /var/www/projectlamp/index.php`<br>
`<?php`<br>
`phpinfo();`<br>
This phpinfo will provide a simple PHP information page on our website.<br>

<br><br><br><br>
**LEMP (Linux, Nginx, MySQL, PHP)**<br><br>

The difference in between LAMP and LEMP is Nginx. To set that running below command. <br>

`sudo apt update`<br>
`sudo apt install nginx`<br>
To verify it `sudo systemctl status nginx`<br><br><br>


`sudo mkdir /var/www/projectLEMP`<br>
`sudo chown -R $USER:$USER /var/www/projectLEMP`<br>
`sudo nano /etc/nginx/sites-available/projectLEMP`<br>

`#/etc/nginx/sites-available/projectLEMP`<br>
```
server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}

sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/

$ sudo nginx -t
```
Last command to check the syntax issue and print below response<br>
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok<br>
nginx: configuration file /etc/nginx/nginx.conf test is successful<br>
```
sudo unlink /etc/nginx/sites-enabled/default
sudo systemctl reload nginx
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```
<br><br><br><br>


```
sudo mysql
CREATE DATABASE `example_database`;
CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
GRANT ALL ON example_database.* TO 'example_user'@'%';
exit


mysql -u example_user -p
SHOW DATABASES;
CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));
INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
SELECT * FROM example_database.todo_list;
 exit



nano /var/www/projectLEMP/todo_list.php
<?php
$user = "example_user";
$password = "PassWord.1";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```
<br>

`dpkg -l | grep php`<br>

`sudo systemctl start php8.1-fpm`<br>
`sudo systemctl enablephp8.1-fpm`<br>

To do: different tables could create on different pages

`http://<Public_domain_or_IP>/todo_list.php`






