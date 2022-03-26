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

nginx not active because apache2 is using port 80

stop apache2 and start nginx
```bash
    sudo systemctl stop apache2
    sudo systemctl start nginx
```
![ssh_gitbash](http://cybronix.com.ng/devops/nginx_start.png)

