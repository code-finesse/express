# 33.0-express-intro

## Middleware

a function that transforms the request and/or the response object

it runs between the handling of the request and response

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3b9240c9-22ef-4b37-94b2-2b300f096e5f/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3b9240c9-22ef-4b37-94b2-2b300f096e5f/Untitled.png)

Middleware can be used for a lot of things, including

working with form data, authentication, logging, error handling

1. set up new route in index.js

    ```jsx
    app.get('/', (req, res) => {
      res.render('welcome')
    })
    ```

2. create new view with form layout

    ```jsx
    <!-- views/welcome.hbs -->
    <h1>Hello World</h1>
    {{!-- action is the route --}}
    {{!-- method post is to send data to the server --}}
    <form action="/" method="post">
      <label for="name">Please enter your name</label>
      {{!-- whatever is in name will be the object property of body --}}
      <input id="name" type="text" name="name">
      <input type="submit">
    </form>
    ```

3. create a post route with the same signature, but now it has a post method

    ```jsx
    app.post('/', (req, res) => {
      res.send('Waddup Was Hannenin')
    })
    ```

    now we use middleware to capture the user input

4. put middleware into index.js (below requirements and above controllers)

    ```jsx
    // add `express.json` middleware which will parse JSON requests into
    // JS objects before they reach the route files.
    // The method `.use` sets up middleware for the Express application
    app.use(express.json())
    // this parses requests that may use a different content type
    app.use(express.urlencoded({ extended: true }))
    ```

5. put something in the post method so it knows what to do after form submit

    ```jsx
    app.post('/', (req, res) => {
      console.log(req.body.name)
      res.send('What\'s good ' + req.body.name)
    })
    ```

## Express & Mongoose

### Intro to MVC

stands for model, view, controller

one of the most common application architectures

MVC is powerful because of Separation of Concerns

For any given feature of an application, we'll have multiple things we need to do to build that feature: 

persist the data for that feature

present the data for that feature

write some business logic to control how the feature works.

- more info

    Each of these can be considered a separate concern: presentation, persistence, business logic. Separating these makes them easier to build, write, maintain or change. For example, if we want to change how we're presenting some data, we can do so by just changing the presentation part of our app without affecting the persistence or business logic.

    In terms of MVC, we can roughly correlate:

    - **Model** to data
    - **View** to user interface
    - **Controller** to the business logic inside the callback for each of our routes

    # 

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8a31b501-64dd-4136-8c2d-225ba46be282/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8a31b501-64dd-4136-8c2d-225ba46be282/Untitled.png)

- Web Application Structures

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/433c977f-81d6-4841-bd33-1481aa53e43d/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/433c977f-81d6-4841-bd33-1481aa53e43d/Untitled.png)

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4299630a-7ae3-429c-9df7-f8b252559ecd/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4299630a-7ae3-429c-9df7-f8b252559ecd/Untitled.png)

## Express & MongoDB

### Setup

1. Create a directory for our project in our `sandbox` with `mkdir express-mvc`.
2. Change into the directory with `cd express-mvc`.
3. Run `npm init -y`.
4. Install express, handlebars, mongoose, and nodemon with `npm i express mongoose hbs nodemon`.
5. Create the git ignore files in the project root with  `echo nodemodules > .gitignore`
6. Create the base files in the project root with `touch index.js`
7. Scaffold out the folder structure for our application:

    in terminal: ***mkdir db models views controllers***

### Mongoose

Connect to Mongoose

1. create a connection.js file in the db/ directory
2. add the following code to the connection.js file

    ```jsx
    // db/connection.js
    // Require Mongoose:
    const mongoose = require('mongoose');

    // Store the URI for our database in a variable.
    // When we're working locally, we'll have a local DB,
    // but in production, we'll have to have a database
    // that's connected to the Internet.
    const mongoURI =
      process.env.NODE_ENV === 'production'
        ? process.env.DB_URL
        : 'mongodb://localhost/express-mvc';

    // Use the mongoose connect method to connect to the
    // database.  The connect method takes two arguments:
    // the address of the database and an object containing
    // any options.
    mongoose
      .connect(mongoURI, {
        useNewUrlParser: true,
        useCreateIndex: true,
        useUnifiedTopology: true,
        useFindAndModify: false,
      })
      // The connect method is asynchronous, so we can use
      // .then/.catch to run callback functions
      // when the connection is opened or errors out.
      .then((instance) =>
        console.log(`Connected to db: ${instance.connections[0].name}`)
      )
      .catch((error) => console.log('Connection failed!', error));

    // Export mongoose so we can use it elsewhere
    module.exports = mongoose;
    ```

    - more info

        **`const mongoose = require('mongoose')`** - To use Mongoose, we require its corresponding node module and save it in a variable we can reference later.

        **`mongoose.connect`** - To link Mongoose to our `express-mvc` Mongo database, we'll use the `mongoose.connect()` method and pass it the address of the database.

        **`module.exports = mongoose`** - When `connection.js` file is required in other files, it will evaluate to this *connected* version of `mongoose`.

Define a Mongoose Schema and Model

1. create a new file in models directory called todo-model.js
2. create a schema and model for our todos:

    ```jsx
    const mongoose = require('../db/connection');

    const ToDoSchema = new mongoose.Schema(
      {
        title: {
          type: String,
          required: true,
        },
        complete: {
          type: Boolean,
          default: false,
        },
      },
      { timestamps: true }
    );

    // Make sure to name the model with the singular Todo!
    // Mongoose pluralizes and lowercases the name of the model
    // to name the collection of documents in the database that
    // correspond to this model.
    const Todo = mongoose.model('Todo', ToDoSchema);

    module.exports = Todo;
    ```

    - more info

        **`mongoose.Schema( )`** - The schema method creates a blueprint for our Todo model describing tthe attributes it will have and what data types they will be.

        **`mongoose.model( )`** - We attach our schema to our model by passing in two arguments to this method: (1) the desired name of our model ("Todo") and (2) the existing schema.

        **`module.exports = Todo`** - When this file (`todo-model.js`) is required in other files, it will evaluate to the `Todo` model defined here through which we will be able to query the `todo` collection in our Mongo database.

Set  up Seed Data

1. create a new todo-seeds.json file in db
2. add data into the file
    - e.g.

        ```jsx
        [
           {
              "title":"Wash the dishes",
              "complete":false
           },
           {
              "title":"Feed the dog",
              "complete":false
           },
           {
              "title":"Take out the trash",
              "complete":false
           }
        ]
        ```

Set up Seed File

1. create a new todo-seeds.js file in db
2. add the following code

    ```jsx
    // Require the model which has a connection to the database
    const Todo = require('../models/todo-model');
    // Require a json file which contains some dummy data
    const seedData = require('./todo-seeds.json');

    ///// ASYNCHRONOUS /////

    // Remove any preexisting data
    Todo.deleteMany({})
      .then(() => {
        // Insert the dummy data and return it
        // so we can log it in the next .then
        return Todo.collection.insertMany(seedData);
      })
      // If the insert was successful, we'll see the
      // results in the terminal
      .then(console.log)
      // Log the error if the insert didn't work
      .catch(console.error)
      // Whether it was successful or not, we need to let the client know the status
      // exit the database.
      .finally(() => {
        // Close the connection to Mongo
        process.exit();
      });
    ```

    - more info

Running the Seed File

1. make sure you're in the express-mvc directory
2. in terminal: ***node ./db/todo-seeds.js***

    it should show you the results in the terminal but you can also *find* the data

    > db.todos.find()

- Remember CRUD?

    CRUD is an acronym you'll hear a lot: it captures all of the operations we can perform on data in our application. CRUD stands for:

    - **Create** - make a new instance of our data
    - **Read** - view our data
    - **Update** - edit an existing data instance
    - **Delete** - remove an existing piece of data

    As we're building out the routes for working with our data, we'll be building them to perform full CRUD. That means we'll have routes for creating, reading, updating and deleting data in our application.

- What is REST?

    REST is an architectural style

    REST, or REpresentational State Transfer, standardizes the conventions we use when the combining these methods and paths to perform specific actions

    it's not a protocol like HTTP, but it is widely accepted as convention

- HTTP Methods for RESTful Services

    HTTP defines a set of request methods

    [Untitled](https://www.notion.so/5488230ff7544254828423eac453d028)

    So, wait -- there are 5 HTTP methods, but only 4 CRUD methods?

    `PUT` and `PATCH` are both used for updating. The difference is that `PUT` replaces an entire database record/document, whereas `PATCH` replaces some of the data in the record/document.

- RESTful Routes

    a route is a method plus a path...

    Method + Path = Route

    resource is the main collection and the path with no other parameters

    Each route results in an **action**. Routes that follow REST conventions are known as RESTful routes:

    [https://media.git.generalassemb.ly/user/17300/files/96870180-db9d-11ea-83ce-efe6b7517ec5](https://media.git.generalassemb.ly/user/17300/files/96870180-db9d-11ea-83ce-efe6b7517ec5)

    We'll refer back to these routes at each step as we build out our controllers. For a resource with full CRUD, the controller for that resource will likely have each of the above 7 routes.

    ** id's will return one thing (because it is an Object id)

Build a Server

1. in index.js it needs to have a few basic components

    ```jsx
    // index.js
    // Require express
    const express = require('express');
    // Use express to instantiate our app
    const app = express();

    ///// LANDMARKS ARE IMPORTANT /////

    /* START ROUTE CONTROLLERS */
    // Require our ToDo model
    const Todo = require('./models/todo-model.js');

    /* END ROUTE CONTROLLERS */

    // Create a variable for our port
    const port = process.env.PORT || 4000;

    // Run our server!
    app.listen(port, () => {
      console.log(`Express MVC app is running on port ${port}`);
    });
    ```

    - more info

        The port where we'll run our server might be different in production versus locally on our machine. Having the ability to use an environment variable to control this is a convenience that will serve us later if we choose to deploy this application.

2. scaffold out all routes in Landmarks area (refer to restful routes above)

    ```jsx
    /* START ROUTE CONTROLLERS */
    // Require our ToDo model
    const Todo = require('./models/todo-model.js');

    // Index
    app.get('/todos', (req, res) => {})

    // Show
    app.get('/todos/:id', (req, res) => {})

    // New
    app.get('/todos/new', (req, res) => {})

    // Create
    app.post('/todos', (req, res) => {})

    // Edit
    app.get('/todos/:id/edit', (req, res) => {})

    // Update
    app.put('/todos/:id', (req, res) => {})

    // Delete
    app.delete('/todos/:id', (req, res) => {})

    /* END ROUTE CONTROLLERS */
    ```

3. in index.js add a controller for the index route

    ```jsx
    // Index
    app.get('/todos', (req, res) => {
      // go find all the stuff in the curly braces in the Todo collection
      Todo.find({})
      .then((todos) => {
        // it needs to know the template and the data to use
        // if property name and value are the same it can be shortened(from todos: todos)
        res.render('todos/index', { todos })
      })
      // need to handle error because the client won't know something went wrong
      .catch(console.error)
    })
    ```

    - more info

        **`Todo.find({})`** - Retrieves all todos in the database since we are not passing in any parameters to the method.

        **`.then(function(todos){ ... })`** - `todos` represents the all the Todos pulled from the database. We can then reference this inside of `.then`.

        **`res.render('todos/index', { todos });`** - A little confusing, we're rendering our `index` view and passing in our `todos` from the database

4. create layout.hbs in views folder
5. create todos folder in views folder and create an index.hbs inside todos

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0a76cf28-bc1e-4152-9c29-b873d690725a/Screen_Shot_2020-11-06_at_1.17.44_PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0a76cf28-bc1e-4152-9c29-b873d690725a/Screen_Shot_2020-11-06_at_1.17.44_PM.png)

6.  import html boilerplate into layout.hbs

     import bootstrap into layout.hbs for added styling

    ```jsx
    <!DOCTYPE html>
    <html lang="en">

    <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <!--Adding Bootstrap so we can make it pretty-->
      <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css"
        integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous" />
      <title>Express MVC App</title>
    </head>

    <body class="container">
      {{{body}}}
    </body>

    </html>
    ```

7. define the view engine in index.js

    ```jsx
    app.set('view engine', 'hbs')
    ```

8. build out the page inside index.hbs

    ```jsx
    <h1>Todos</h1>
    <ul class='list-group'>
      {{!-- this.each is like mapping list items --}}
      {{!-- needs to be closed and uses functionality similar to React where things need to be passed down --}}
      {{!-- use this to reference the element bing iterated over --}}
      {{#each todos}}
        <li class='list-group-item d-flex justify-content-between'>
          <div>{{this.title}}</div>
          <div class="links">
            <!-- for actions later -->
          </div>
        </li>
      {{/each}}
    </ul>
    ```

9. define Show route in index.js

    ```jsx
    // Show
    app.get('/todos/:id', (req, res) => {
      // find the todo with the id 
      Todo.findById(req.params.id)
      .then((todo) => {
        // render it
        // first argument is template to use and second argument is data to display
        res.render('todos/show', todo)
      })
      .catch(console.error)
    })
    ```

10. create Show template file (inside views folder) and style 

    ```jsx
    <!-- views/todos/show.hbs -->
    <h1>{{title}}</h1>
    {{#if complete}}
    <p>Status: complete</p>
    {{else}}
    <p>Status: incomplete</p>
    {{/if}}
    {{!-- back button to send us back to todos --}}
    <a href="/todos" class="btn btn-secondary">&larr; Back</a>
    ```

11. create a button with an anchor tag that sends the client to the specific id inside index.hbs

    ```jsx
    // inside index.hbs
    <!--insert this anchor tag into the div with a class of 'links'-->
    <a href="/todos/{{this.id}}" class="btn btn-info">View</a>
    ```

12. create a controller file in controllers folder called todos.js
13. set up todos.js

    ```jsx
    const express = require('express');
    const router = express.Router();

    // make room for all the imports

    module.exports = router
    ```

14. import route controllers

    ** cut and paste ALL route controllers from index.js

    ```jsx
    const express = require('express');
    const router = express.Router();

    // Require our ToDo model
    const Todo = require('../models/todo-model.js');

    // Index
    router.get('/todos', (req, res) => {
      // go find all the stuff in the curly braces in the Todo collection
      Todo.find({})
      .then((todos) => {
        // it needs to know the template and the data to use
        // if property name and value are the same it can be shortened(from todos: todos)
        res.render('todos/index', { todos })
      })
      // need to handle error because the client won't know something went wrong
      .catch(console.error)
    })

    // Show
    app.get('/todos/:id', (req, res) => {
      // find the todo with the id 
      Todo.findById(req.params.id)
      .then((todo) => {
        // render it
        // first argument is template to use and second argument is data to display
        res.render('todos/show', todo)
      })
      .catch(console.error)
    })

    // New
    app.get('/todos/new', (req, res) => {})

    // Create
    app.post('/todos', (req, res) => {})

    // Edit
    app.get('/todos/:id/edit', (req, res) => {})

    // Update
    app.put('/todos/:id', (req, res) => {})

    // Delete
    app.delete('/todos/:id', (req, res) => {})

    module.exports = router
    ```

15. change all "apps" to 'router"

    command + d is a great shortcut

    ```jsx
    // New
    router.get('/todos/new', (req, res) => {})
    ```

16. change todo path

    ```jsx
    const Todo = require('../models/todo-model.js');
    ```

17. bring in controllers to index.js

    ```jsx
    /* START ROUTE CONTROLLERS */
    const todoController = require('./controllers/todos')
    /* END ROUTE CONTROLLERS */
    ```

18. tell app to use todoController

    ```jsx
    /* START ROUTE CONTROLLERS */
    const todoController = require('./controllers/todos')
    app.use(todoController)
    /* END ROUTE CONTROLLERS */
    ```

19. create a new To Do by setting up a new route and new handlebar layout

    create new.hbs file inside todos folder of views folder

20. set up new.hbs layout

    ```jsx
    <!--views/todos/new.hbs-->
    <h2>Add a new To Do</h2>
    <form action="/todos" method="POST">
      <div class="form-group">
        <label for="title">Title:</label>
        <input type="text" id="title" name="title" class="form-control" />
      </div>
      <input type="submit" class="btn btn-primary" />
      <a href="/todos" class="btn btn-secondary">Cancel</a>
    </form>
    ```

21. change index.js

    ```jsx
    // add below requirement and express

    // `express.json` parses application/json request data and
    //  adds it to the request object as request.body
    app.use(express.json());
    // `express.urlencoded` parses x-ww-form-urlencoded request data and
    //  adds it to the request object as request.body
    app.use(express.urlencoded({ extended: true }));
    ```

22. set up Create route

    ```jsx
    // New
    router.get('/new', (req, res) => {
      res.render('todos/new');
    });
    ```

23. make sure New route is above Show route

    ```jsx
    // New
    router.get('/new', (req, res) => {
      res.render('todos/new');
    });

    // Show
    router.get('/todos/:id', (req, res) => {
      // find the todo with the id 
      Todo.findById(req.params.id)
      .then((todo) => {
        // render it
        // first argument is template to use and second argument is data to display
        res.render('todos/show', todo)
      })
      .catch(console.error)
    })
    ```

24. update a To Do by installing method-override package 

    in terminal: npm i method-override

25. insert method override in index.js

    ```jsx
    // index.js

    // after epress requirement and above app=express
    const methodOverride = require('method-override');

    // ...

    // above view engine
    app.use(methodOverride('_method'));
    ```

26. create and alter edit form

    edit.hbs file goes in todos folder

    ```jsx
    <!--views/todos/edit.hbs-->
    <h2>Edit To Do:</h2>
    {{!-- method override will see this post method and override it be a put method --}}
    <form action="/todos/{{this.id}}/?_method=put" method="POST">
      <div class="form-group">
        <label for="title">Title:</label>
        {{!-- we are prepopulating the values  --}}
        <input type="text" id="title" name="title" class="form-control" value="{{this.title}}">
      </div>

      <div class="form-check mb-3">
        {{#if this.complete}}
        <input class="form-check-input" type="checkbox" id="complete" name="complete" checked="true" />
        {{else}}
        <input class="form-check-input" type="checkbox" id="complete" name="complete" />
        {{/if}}
        <label class="form-check-label" for="complete">Complete</label>
      </div>
      <input type="submit" value="Update To Do" class="btn btn-primary" />
      <a href="/todos" class="btn btn-secondary">Cancel</a>
    </form>
    ```

27. change the edit route

    ```jsx
    router.get('/todos/:id/edit', (req, res) => {
      Todo.findById(req.params.id)
        .then((todo) => {
          res.render('todos/edit', todo)
        })
        .catch(console.error)
    })
    ```

28. change the update path to allow for findoneandupdate method

    ```jsx
    router.put('/:id', (req, res) => {
      const id = req.params.id;
      Todo.findOneAndUpdate(
        { _id: id },
        {
          title: req.body.title,
          complete: req.body.complete === 'on',
        },
        { new: true }
      )
        .then((todo) => {
          res.render('todos/show', todo);
        })
        .catch(console.error);
    });
    ```

29. in order to delete we need a delete operation

    add button for logic in index.hbs

    ```jsx
    // We're using method-override again to tell Express we actually want to perform a DELETE action.

    // index.hbs
    <form action="/todos/{{this.id}}?_method=DELETE" method="POST" class="d-inline">
      <input type="submit" value="Delete" class="btn btn-danger" />
    </form>
    ```

30. update delete route

    ```jsx
    router.delete('/:id', (req, res) => {
      const id = req.params.id;
      Todo.findOneAndRemove({ _id: id })
        .then(() => {
          res.redirect('/todos');
        })
        .catch(console.error);
    });
    ```
