
# This documentation is a guide to installation and configuration of a LEMP stack running on an Ubuntu machine
## Steps required
# Ubuntu Machine
The machine which the  whole stack is hoing to be hostenstance , i will be using t2 micro instance powered by the Ubuntu 24.lt
image
![LEMP AWS](https://github.com/GrailRoyal/LEMP_Stack/blob/images/LEMP%20AWS.png)

```bash
ssh -i key.pem ubuntu@mypublicip
```
i ssh into the Linux machine
![LEMP SSH](https://github.com/GrailRoyal/LEMP_Stack/blob/images/LEMP%20SSH.png)

## Install Ngnix
```bash
sudo apt update
```
This command update the Package repository for ubuntu to ensure its up to date
```basg
sudo apt install nginx
```
Installs the Nginx server
```bash
sudo systemctl status nginx
```
This checks the status of the nginx servr to know if its active or not
Testing Nginx
http://mypublicip
![LEMP NGINX](https://github.com/GrailRoyal/LEMP_Stack/blob/images/LEMP%20NGINX.png)

## Install mySQL and security configuring
```bash
sudo apt insatll mysql-server
```
Install the databasse server o the machine  we are downloading sql here
```bash
sudo 
```
started up mysql to check if its properly installed
```mqsql
ALTER USER'root'@'localhost' IDENTIFIED WITH mysql_native_password BY'PassWord.1';

```

Security of mysql is inportant , which makes this line of code important . it selects the authentication method for the root user to sign in into my sql
i made use of "mysql_native_password" which is an older protocol in place of caching_sha2_authentication
because mysqlnd which is the mysql php library that allows mysql work with php  doesnt work properly or doesnt work at all with caching_sha2_authentication
```mqsql
sudo mysql_secure_installation
```
to further secure the database as well as acess to the database

```mysql
sudo mysql -p
 exit
```
Tested to see the security measures taken have been implemented and logged out of mysql
## Install PHP ,other models and configuration 
```bash
sudo apt install php-fpm php-mysql
```

install php , this is diffrent compared to apache which requires just installing the php , with nginx its diffrent i install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing and php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. The core PHP packages will automatically be installed as dependencies 
```bash
sudo mkdir /var/www/projectLEMP
sudo chown -R $USER:$USER /var/www/projectLEMP
sudo nano /etc/nginx/sites-available/projectLEMP
```
```bash
#/etc/nginx/sites-available/projectLEMP
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
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```

create a directory
change directory ownership and the files in the directory wiich is enable by the recursive in the command
create a config file inside the sites-available sub directory inside the nginx directory 
input the confguration file and save

```bash
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```
link the new configured directory to the site-enabled directory


```bash
sudo nginx -t
```
 check if it works well

```bash
sudo unlink /etc/nginx/sites-enabled/default
```
unlink the default so has not to allow the default override the newly enabled when it is requested

```bash
sudo systemctl reload nginx
```
reload the nginx server so all the new changes can stats to function

## Testing
```bash
sudo echo 'Hello LEMP from hostname' $(TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```
creates a file to be dispplayed for the test  for php

http://mypublicip

```bash
nano /var/www/projectLEMP/info.php

<?php
phpinfo();
```
![LEMP PHP Info](https://github.com/GrailRoyal/LEMP_Stack/blob/images/LEMPPHP%20INFO.png)

create a new file on project diretory to display php info page 

## mySQL table creation
```sql
mysql -u example_user -p

SHOW DATABASES;
CREATE TABLE example_database.todo_list (
item_id INT AUTO_INCREMENT,
content VARCHAR(255),
 PRIMARY KEY(item_id)
INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
```
This section creates tables insidemyql database and put values into the table meants to be diplayed

```sql
SELECT * FROM example_database.todo_list;
```
displays the the newtable and its content on the terminal
![LEMP Term DB](https://github.com/GrailRoyal/LEMP_Stack/blob/images/LEMPTERMDB.png)

```bash
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
create a new php file which is mean to serve the content of the databae through php if the file is properly written or configured it will be able to connect and display the content of mysql from
## Testing
http://publicip/todo_list.php
![LEMP DB PHP](https://github.com/GrailRoyal/LEMP_Stack/blob/images/LEMP%20DBPHP.png)


Thank ou for following!!!
