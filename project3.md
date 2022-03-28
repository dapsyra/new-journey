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


# Routes

## There are three actions that our To-Do application needs to be able to do:

1. Create a new task

1. Display list of all tasks

1. Delete a completed task

## create a folder routes and decend into it

```bash
    mkdir routes
    cd routes
```

## create a file api.js
```bash
    touch api.js
```
### Edit api.js file
```bash
    vim api.js
```

 ### Paste code:
    
```bash
const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;
```

## Install mongoose to make working with mongodb easier
```bash
    npm install mongoose 
    mkdir models
    cd models
```
## Create a file and name it todo.js Inside the models folder

```bash
    touch todo.js
```
## Edit file and paste code
```bash
     nano todo.js
```
## Paste code
```bash
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//create schema for todo
const TodoSchema = new Schema({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})

//create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;
```
## Now update the routes from the file api.js in ‘routes’ directory to make use of the new model.  In Routes directory, open api.js with vim api.js, delete the code inside with :%d command and paste there code below into it then save and exit

```bash
    vim api.js
```
## Paste code

```bash
const express = require ('express');
const router = express.Router();
const Todo = require('/home/ubuntu/Todo/routes/models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});

router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});

router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})

module.exports = router;
```

# MongoDB Database

## Signup for mLab
```bash
    https://www.mongodb.com/atlas-signup-from-mlab
```

## Create a file in your Todo directory and name it .env
```bash
    touch .env && vi .env
```

## Add the connection string to access the database in it, just as below: (Ensure to update <username>, <password>, <network-address> and <database> according to your setup)

```bash
    DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
```
## update the index.js to reflect the use of .env so that Node.js can connect to the database

```bash
    cd /home/ubuntu/Todo
    vi index.js
```

## Replace content of file with the code below
```bash
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

//connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
.then(() => console.log(`Database connected successfully`))
.catch(err => console.log(err));

//since mongoose promise is depreciated, we overide it with node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use(bodyParser.json());

app.use('/api', routes);

app.use((err, req, res, next) => {
console.log(err);
next();
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```
## Start the server
```bash
    node index.js
```
![start1](http://cybronix.com.ng/devops/start1.png)

# Testing Backend Code without Frontend using RESTful API








