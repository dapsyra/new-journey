# new-journey
My DevOps Training Projects

Project 1

Create a new AWS account for the course

![new_AWS_account](http://cybronix.com.ng/devops/aws-account.jpg)

Install Apache 2
```bash
  sudo apt install apache2
```

Open http and https ports in AWS firewall


Install and secure mysql server
```bash
  sudo apt install mysql-server

  sudo mysql_secure_installation

  sudo mysql
```

![working_sql](http://cybronix.com.ng/devops/working_sql.png)


Install PHP and related packages

```bash
  sudo apt install php libapache2-mod-php php-mysql
```

![php_confirmed](http://cybronix.com.ng/devops/php_confirmed.png)



Create the directory to host training site and assign permissions

```bash
  sudo mkdir /var/www/training

  sudo chown -R $USER:$USER /var/www/training
```

Create virtual host config file for training and add content with nano

```bash
  sudo nano /etc/apache2/sites-available/training.conf
```
<VirtualHost *:80>
    ServerName training
    ServerAlias www.training
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/training
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

