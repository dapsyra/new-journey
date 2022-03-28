Implement a web solution based on MERN stack in AWS Cloud

Update and upgarde Ubuntu

```bash
    sudo apt update
    sudo apt upgrade
```

Fetch and install NodeSourse Node.js 12.x repository 

```bash
    curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
```

Install Node.js and verify
```bash
    sudo apt-get install -y nodejs
    node -v
    npm -v
```
![nodejs_install](http://cybronix.com.ng/devops/nodejs1.png)

Create a directory for the Todo project
```bash
    mkdir Todo
```
Verify existence of Todo directory
```bash
    ls -lih
```
![todo1](http://cybronix.com.ng/devops/todo1.png)

```bash
    cd Todo
```
Initialise project to create a new package.json file.  Follow the prompt
```bash
    npm init
```


Install ExpressJS and confirm

```bash
    npm install express
    ls
```
![todo2](http://cybronix.com.ng/devops/todo2.png)

Install dotenv

```bash
    npm install dotenv
```
Edit index.js and add codes
```bash
    nano index.js
```
Copy and paste code

```bash
const express = require('express');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use((req, res, next) => {
res.send('Welcome to Express');
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```
![codes1](http://cybronix.com.ng/devops/codes1.png)

Test server
```bash
    node index.js
```
![server-test](http://cybronix.com.ng/devops/server-test.png)

Open port TCP/5000 in AWS instance to allow access to MERN server

![port5000](http://cybronix.com.ng/devops/port5000.png)

Launch web browser to test
```bash
    http://<PublicIP-or-PublicDNS>:5000
```
![test3](http://cybronix.com.ng/devops/test3.png)











MongoDB Database

Signup for mLab
```bash
    https://www.mongodb.com/atlas-signup-from-mlab
```








