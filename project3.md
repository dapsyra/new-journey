# Implement a web solution based on MERN stack in AWS Cloud

## Update and upgarde Ubuntu

```bash
    sudo apt update
    sudo apt upgrade
```

## Fetch and install NodeSourse Node.js 12.x repository 

```bash
    curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
```

## Install Node.js and verify
```bash
    sudo apt-get install -y nodejs
    node -v
    npm -v
```
![nodejs_install](http://cybronix.com.ng/devops/nodejs1.png)

## Create a directory for the Todo project
```bash
    mkdir Todo
```
## Verify existence of Todo directory
```bash
    ls -lih
```
![todo1](http://cybronix.com.ng/devops/todo1.png)

```bash
    cd Todo
```
## Initialise project to create a new package.json file.  Follow the prompt
```bash
    npm init
```


## Install ExpressJS and confirm

```bash
    npm install express
    ls
```
![todo2](http://cybronix.com.ng/devops/todo2.png)

## Install dotenv

```bash
    npm install dotenv
```
## Edit index.js and add codes
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

## Test server
```bash
    node index.js
```
![server-test](http://cybronix.com.ng/devops/server-test.png)

### Open port TCP/5000 in AWS instance to allow access to MERN server

![port5000](http://cybronix.com.ng/devops/port5000.png)

## Launch web browser to test
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

## In this project, we will use [Postman](https://www.getpostman.com/) to test our API.
### Click [Install Postman](https://www.getpostman.com/downloads/) to download and install postman on your machine.

### You should test all the API endpoints and make sure they are working. For the endpoints that require body, you should send JSON back with the necessary fields since it’s what we setup in our code.

### Now open your Postman, create a POST request to the API http://PublicIP-or-PublicDNS:5000/api/todos. This request sends a new task to our To-Do list so the application could store it in the database.

### Note: make sure your set header key Content-Type as application/json

![postman1](http://cybronix.com.ng/devops/postman1.png)

![postman2](http://cybronix.com.ng/devops/postman2.png)

### Create a GET request to your API on http://PublicIP-or-PublicDNS:5000/api/todos. This request retrieves all existing records from our To-do application (backend requests these records from the database and sends it us back as a response to GET request).

![postman3](http://cybronix.com.ng/devops/postman3.png)


### Create a DELETE request to your API on http://PublicIP-or-PublicDNS:5000/api/todos/task-id. This request delete the task from our To-do application

![postman4](http://cybronix.com.ng/devops/postman4.png)

# FRONTEND CREATION

## Since we are done with the functionality we want from our backend and API, it is time to create a user interface for a Web client (browser) to interact with the application via API. To start out with the frontend of the To-do app, we will use the create-react-app command to scaffold our app.

## NOTE:  create-react-app requires node 14 or higher.  Upgrade nodejs to version 14

```bash
    curl -fsSL https://deb.nodesource.com/setup_14.x | sudo -E bash -
    sudo apt-get install -y nodejs
```
## now install create-react-app
```bash
    cd ~/Todo
    npx create-react-app client
```
## Running a React App
### install dependencies
```bash
    npm install concurrently --save-dev && npm install nodemon --save-dev
```

## Edit package.json in the Todo folder and replace highlighted part with the code below

```bash
    "scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
```
![replace1](http://cybronix.com.ng/devops/replace1.png)

### Configure Proxy in package.json by adding the key value pair in the package.json file "proxy": "http://localhost:5000".
```bash
        cd client
        nano package.json
```
### add
```bash
    "proxy": "http://localhost:5000"
```
## Goto Todo directory and simply do:
```bash
    cd ~/Todo
    npm run dev
```
### Your app should open and start running on localhost:5000

![success1](http://cybronix.com.ng/devops/success1.png)
### Important note: In order to be able to access the application from the Internet you have to open TCP port 5000 on EC2

## Creating your React Components
### One of the advantages of react is that it makes use of components, which are reusable and also makes code modular. For our Todo app, there will be two stateful components and one stateless component.
### From your Todo directory run:
```bash
    cd client
    mkdir src
```
### move to the src directory
```bash
    cd src
```
### Inside your src folder create another folder called components

```bash
    mkdir components
```
### Move into the components directory with

```bash
    cd components
```
### Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js.
```bash
    touch Input.js ListTodo.js Todo.js
```
### Open Input.js file
```bash
    vi Input.js
```
### To make use of Axios, which is a Promise based HTTP client for the browser and node.js, you need to cd into your client from your terminal and run yarn add axios or npm install axios.

### Move to the src folder
```bash
    cd ..
```
### Move to clients folder
```bash
    cd ..
```
### Install Axios
```bash
    npm install axios
```
### Go to ‘components’ directory
```bash
    cd src/components
```
### Edit ListTodo.js and paste following code

```bash
    nano ListTodo.js
```
### paste code

```bash
import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {

return (
<ul>
{
todos &&
todos.length > 0 ?
(
todos.map(todo => {
return (
<li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
)
})
)
:
(
<li>No todo(s) left</li>
)
}
</ul>
)
}

export default ListTodo
```
### Edit Todo.js and paste following code
```bash
    nano Todo.js
```
### paste code
```bash
import React, {Component} from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {

state = {
todos: []
}

componentDidMount(){
this.getTodos();
}

getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}

deleteTodo = (id) => {

    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))

}

render() {
let { todos } = this.state;

    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )

}
}

export default Todo;
```

### We need to make little adjustment to our react code. Delete the logo and adjust our App.js to look like this.

### Move to the src folder
```bash
    cd ..
```
### Make sure that you are in the src folder and run
```bash
    nano App.js
```
### Copy and paste the code below into it
```bash
    import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;
```
### In the src directory open the App.css
```bash
    vi App.css
```
### paste the following code into App.css:
```bash
    .App {
text-align: center;
font-size: calc(10px + 2vmin);
width: 60%;
margin-left: auto;
margin-right: auto;
}

input {
height: 40px;
width: 50%;
border: none;
border-bottom: 2px #101113 solid;
background: none;
font-size: 1.5rem;
color: #787a80;
}

input:focus {
outline: none;
}

button {
width: 25%;
height: 45px;
border: none;
margin-left: 10px;
font-size: 25px;
background: #101113;
border-radius: 5px;
color: #787a80;
cursor: pointer;
}

button:focus {
outline: none;
}

ul {
list-style: none;
text-align: left;
padding: 15px;
background: #171a1f;
border-radius: 5px;
}

li {
padding: 15px;
font-size: 1.5rem;
margin-bottom: 15px;
background: #282c34;
border-radius: 5px;
overflow-wrap: break-word;
cursor: pointer;
}

@media only screen and (min-width: 300px) {
.App {
width: 80%;
}

input {
width: 100%
}

button {
width: 100%;
margin-top: 15px;
margin-left: 0;
}
}

@media only screen and (min-width: 640px) {
.App {
width: 60%;
}

input {
width: 50%;
}

button {
width: 30%;
margin-left: 10px;
margin-top: 0;
}
}
```
### In the src directory open the index.css
```bash
    vim index.css
```
### paste code
```bash
body {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}

code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
monospace;
}
```
### Go to the Todo directory
```bash
    cd ../..
```
### Launch dev from Todo directory run:
```bash
    npm run dev
```
