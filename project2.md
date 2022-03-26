Launch Git Bash
Connect to AWS instance
```bash
    ssh -i devops.pem ubuntu@ec2-3-144-137-190.us-east-2.compute.amazonaws.com
 ```

![ssh_gitbash](http://cybronix.com.ng/devops/ssh_with_gitbash.png)

Install Nginx

```bash
    sudo apt update
    sudo apt install nginx
```
verify nginx
```bash
    sudo systemctl status nginx
    sudo systemctl start nginx
```
![ssh_gitbash](http://cybronix.com.ng/devops/nginx_dead.png)

nginx not active because apache2 is bind to port 80

stop apache2 and start nginx
```bash
    sudo systemctl stop apache2
    sudo systemctl start nginx
    sudo systemctl status nginx
```
![ssh_gitbash](http://cybronix.com.ng/devops/nginx_start.png)

Open http port in AWS firewall


Install and secure mysql server

```bash
  sudo apt install mysql-server

  sudo mysql_secure_installation

  sudo mysql
```

![working_sql](http://cybronix.com.ng/devops/working_sql.png)


Install PHP

```bash
    sudo apt install php-fpm php-mysql
```

![php_nginx](http://cybronix.com.ng/devops/php_nginx.png)


Configure Nginx to Use PHP Processor

create working directory and assign permissions

```bash
    sudo mkdir /var/www/projectLEMP
    sudo chown -R $USER:$USER /var/www/projectLEMP
```

create a new configuration file in Nginx’s sites-available directory

```bash
    sudo nano /etc/nginx/sites-available/projectLEMP
```

paste bare-bone configuration and save file

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
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```

Activate configuration by linking to the config file from Nginx’s sites-enabled directory:
```bash
    sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```

Test config for error
```bash
    sudo nginx -t
```
![test_nginx](http://cybronix.com.ng/devops/nginx_test.png)



Disable default Nginx host and relaunch Nginx
```bash
    sudo unlink /etc/nginx/sites-enabled/default
    sudo systemctl reload nginx
```

Nginx is now active.  Create an index file to test it
```bash
    sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```

Launch site in web browser to test

![test_lemp](http://cybronix.com.ng/devops/test_lemp.png)


Test PHP with Nginx

create a test PHP file in working directory

```bash
    sudo nano /var/www/projectLEMP/info.php
```

add content to file and save
```bash
    <?php
    phpinfo();
```

launch web browser to test
```bash
    http://`server_domain_or_IP`/info.php
```
![test_nginx_php](http://cybronix.com.ng/devops/nginx_php_working.png)

Remove info.php file after testing for security purpose

```bash
     sudo rm /var/www/projectLEMP/info.php
```


Retrieving data from MySQL database with PHP

connect to the MySQL console
```bash
    sudo mysql
```
create a new DB
```bash
    CREATE DATABASE `project2_db`;
```
create a new user (replace password as appropriate)
```bash
    CREATE USER 'project2'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
```

Grant all permissions over project2_db to project2 and exit mysql
```bash
    GRANT ALL ON project2_db.* TO 'project2'@'%';
    exit
```

Login to mysql using project2 credentials
```bash
    mysql -u project2 -p
    SHOW DATABASES;
```
![test_mysql](http://cybronix.com.ng/devops/test_mysql.png)



create a test table named todo_list from mysql console

```bash
    CREATE TABLE project2_db.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));

    INSERT INTO project2_db.todo_list (content) VALUES ("My first important item");
    INSERT INTO project2_db.todo_list (content) VALUES ("My second important item");
    INSERT INTO project2_db.todo_list (content) VALUES ("My third important item");
    INSERT INTO project2_db.todo_list (content) VALUES ("My other important items");
```
confirm that the data was successfully saved to table and exit
```bash
    SELECT * FROM project2_db.todo_list;
    exit
```
![test_mysql_2](http://cybronix.com.ng/devops/test_mysql_2.png)



Create a PHP script that will connect to MySQL and query for your content

```bash
    nano /var/www/projectLEMP/todo_list.php
```

Add content to file and save
```bash
    <?php
    $user = "project2";
    $password = "password";
    $database = "project2_db";
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
Launch web browser to test
```bash
    http://ec2-3-144-137-190.us-east-2.compute.amazonaws.com/todo_list.php
```

![todo](http://cybronix.com.ng/devops/todo.png)












