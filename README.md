# Express

- Middleware

    Stack overflow answer to `what is middleware?:`Here is the explanation that should clear doubts on `express.json()` and `express.urlencoded()` and the use of body-parser. It took me some time to figure this out.

    1. What is Middleware? It is those methods/functions/operations that are called BETWEEN processing the Request and sending the Response in your application method.
    2. When talking about `express.json()` and `express.urlencoded()` think specifically about POST requests (i.e. the .post request object) and PUT Requests (i.e. the .put request object)
    3. You DO NOT NEED `express.json()` and `express.urlencoded()` for GET Requests or DELETE Requests.
    4. You NEED `express.json()` and `express.urlencoded()` for POST and PUT requests, because in both these requests you are **sending data** (in the form of some data object) to the server and you are asking the server to accept or store that data (object), which is enclosed in the body (i.e. `req.body`) of that (POST or PUT) Request
    5. Express provides you with middleware to deal with the (incoming) data (object) in the body of the request.
    6. a. `express.json()` is a method inbuilt in express to recognize the incoming Request Object as a **JSON Object**. This method is called as a middleware in your application using the code: `app.use(express.json());`
    7. b. `express.urlencoded()` is a method inbuilt in express to recognize the incoming Request Object as **strings or arrays**. This method is called as a middleware in your application using the code: `app.use(express.urlencoded());`

RESTful Routes is a method plus a path

REST can be translated in to RESTful Routes (routes that follow REST):

[Untitled](https://www.notion.so/846d7a0fae7644c4bf6f58dc7bd3c7ec)

Biuilding an API in Express

- Set Up Express JS
    1. make directory and change into it
    2. npm init -y
    3. npm i express mongoose nodemon
    4. echo node_modules > .gitignore
    5. touch index.js
    6. update dev script

        ```jsx
        "scripts": {
            "dev": "nodemon index"
          },
        ```

    7. mkdir db controllers models
    8. set up index.js file

    ```jsx
    const express = require('express')
    // instantiate express
    const app = express();

    /* START CONTROLLERS HERE */

    /* END CONTROLLERS HERE */

    app.set('port', process.env.PORT || 8000);

    app.listen(app.get('port'), () => {
      console.log(`✅ PORT: ${app.get('port')} 🌟`);
    });
    ```

1. Build our Models

    inside models folder create a new bookmark.js file and set it up

    ```jsx
    // require the mongoose package from the connection pool
    const mongoose = require("../db/connection");

    // make a new schema with 2 properties, and assign it to a variable
    const BookmarkSchema = new mongoose.Schema({
      title: String,
      url: String
    });

    // instantiate the model, calling it "Bookmark" and with the schema we just made
    const Bookmark = mongoose.model("Bookmark", BookmarkSchema);

    // export the newly created model
    module.exports = Bookmark;
    ```

2. Create connection.js in db folder

    ```jsx
    // Require mongoose
    const mongoose = require('mongoose');

    // Store the URI for our database in a variable.
    // When we're working locally, we'll have a local DB,
    // but in production, we'll have to have a database
    // that's connected to the Internet.  The ternary
    // here will check if we have an environment variable
    // set to production, if not, it'll just use the
    // address of our local db.
    const mongoURI =
      process.env.NODE_ENV === 'production'
        ? process.env.DB_URL
        : 'mongodb://localhost/book-e';

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
      .catch(console.error);

    // Export mongoose so we can use it elsewhere
    module.exports = mongoose;
    ```

3. Seed the database

    create a seeds.json and seeds.js file inside the db directory

    run node so that seeds data is in database

    node ./db/seeds.js

    ```jsx
    // seeds.json

    [
      {
         "title": "Microsoft",
         "url": "https://microsoft.com"
      },
      {
         "title": "reddit",
         "url": "https://reddit.com"
      },
      {
         "title": "Google",
         "url": "https://google.com"
      },
      {
         "title": "Know your meme",
         "url": "https://knowyourmeme.com/"
      },
      {
         "title": "Hacker news"
         "url": "https://news.ycombinator.com"
      }
    ]

    // seeds.js

    const Bookmark = require('../models/bookmark')

    const seedData = require('./seeds.json')

    Bookmark.deleteMany({})
      .then(() => {
        return Bookmark.insertMany(seedData)
      })
      .then(console.log)
      .catch(console.error)
      .finally(() => {
        process.exit()
      })
    ```

4. Make a route

    create bookmarks.js file in controllers folder

    add controller to index.js file

    ```jsx
    // bookmarks.js

    const express = require('express')

    const router = express.Router()

    // need to grab the bookmark model
    const Bookmark = require('../models/bookmark')

    // Show: GET all the bookmarks
    router.get("/", (req, res, next) => {
      // 1. Get all of the bookmarks from the DB
      Bookmark.find({})
      // 2. Send them back to the client as JSON
      .then((bookmarks) => {
        res.json(bookmarks)
      })
      // 3. If there's an error pass it on!
      .catch(next)a
    });

    module.exports = router

    // index.js

    /* START CONTROLLERS HERE */

    const bookmarksController = require('./controllers/bookmarks')
    // the first parameter of app.use will tell it the base path so we don't have to type it in every single route
    app.use('/api/bookmarks', bookmarksController)

    /* END CONTROLLERS HERE */
    ```

5. Create Show Route

    create a show route in the bookmarks.js file

    ```jsx
    // Show: Get a Bookmark by ID
    router.get("/:id", (req, res, next) => {
      // 1. Find the Bookmark by its unique ID
      Bookmark.findById(req.params.id)
      // 2. Send it back to the client as JSON
        .then((bookmark) => {
          res.json(bookmark)
        })
        // 3. If there's an error pass it on!
        .catch(next)
    });

    // finds indivdual bookmark by id
    ```

6. Add Middleware

    in index.js

    ```jsx
    // Use middleware to parse the data in the HTTP request body and add
    // a property of body to the request object containing a POJO with with data.

    // json data
    app.use(express.json())
    // form data
    app.use(express.urlencoded({ extended: true }));

    // takes it out of http request and turns it into something we can work with then calls it in the request object
    ```

7. Make Create Route

    in bookmarks.js

    ```jsx
    router.post("/", (req, res, next) => {
      // 1. Use the data in the req body to create a new bookmark
      Bookmark.create(req.body)
        // 2. If the create is successful, send back the record that was inserted
        .then((bookmark) => {
          res.json(bookmark)
        })
        // 3. If there was an error, pass it on!
        .catch(next)
    });
    ```

8. Make Update Route

    in bookmarks.js

    - put vs patch

        The main difference between the PUT and PATCH method is that the PUT method uses the request URI to supply a modified version of the requested resource which replaces the original version of the resource, whereas the PATCH method supplies a set of instructions to modify the resource.

    ```jsx
    // Update
    router.patch('/:id', (req, res, next) => {
      // 1. Find a document by its id and update it
      const id = req.params.id;
      Bookmark.findOneAndUpdate(
        { _id: id },
        {
          title: req.body.title,
          url: req.body.url,
        },
        { new: true }
      )
      // 2. If it's successful, send back the new record
      .then((bookmark) => {
        res.json(bookmark);
      })
      // 3. If there is an error pass it on...
      .catch(next);
    });
    ```

9. Make Delete Route

    in bookmarks.js

    ```jsx
    // Delete
    router.delete('/:id', (req, res, next) => {
      // 1. Find a document by its id and delete it
      const id = req.params.id;
      Bookmark.findOneAndRemove({ _id: id })
      // 2. If delete is successful, send back a response
      .then((bookmark) => {
      res.json(bookmark);
      })
      // 3. If there is an arror pass it on...
      .catch(next);
    });
    ```

    ### PART 2

10. create a new user model as user.js

    in models folder

    ```jsx
    const mongoose = require("../db/connection");

    const UserSchema = new mongoose.Schema({
      email: {
        type: String,
        unique: true,
        index: true
      },
      name: String
    });

    const User = mongoose.model("User", UserSchema);

    module.exports = User;
    ```

11. CRUD  with two related models

    add relations to the bookmark model

    ```jsx
    const BookmarkSchema = new mongoose.Schema({
      title: String,
      url: String,
      owner: {
        // References use the type ObjectId
        type: mongoose.Schema.Types.ObjectId,
        // the name of the model to which they refer
        ref: 'User'
      } 
    });
    ```

12. create user data and routes and schema then run the seeds file to put info into the database

    $ node ./db/seeds.js

    - User Data

        ```jsx
        // userSeeds.json file in db folder

        [
          {
            "email": "Me",
            "name": "me@gmail.com"
          },
          {
            "email": "Myself",
            "name": "myself@aol.com" 
          },
          {
            "email": "I",
            "name": "i@apple.com" 
          }
        ]

        // seeds.js file in db folder

        const User = require('../models/user')

        const seedData = require('./userSeeds.json')

        User.deleteMany({})
          .then(() => {
            return User.insertMany(seedData)
          })
          .then(console.log)
          .catch(console.error)
          .finally(() => {
            process.exit()
          })
        ```

    - Routes

        ```jsx
        // index.js in controller section

        const usersController = require('./controllers/users')
        app.use('/api/users', usersController)

        // users.js inside controllers folder

        const express = require('express')

        const router = express.Router()

        const Users = require('../models/user')

        // user routes here

        // Index: GET all the users
        router.get("/", (req, res, next) => {
          Users.find({})
          .then((users) => {
            res.json(users)
          })
          .catch(next)
        });

        // Show: Get a User by ID
        router.get("/:id", (req, res, next) => {
          Users.findById(req.params.id)
            .then((user) => {
              res.json(user)
            })
            .catch(next)
        });

        // Create
        router.post("/", (req, res, next) => {
          Users.create(req.body)
            .then((user) => {
              res.json(user)
            })
            .catch(next)
        });

        module.exports = router
        ```

    - Schema

        ```jsx
        // in user.js inside models folder

        const mongoose = require("../db/connection");

        const UserSchema = new mongoose.Schema({
          email: {
            type: String,
            unique: true,
            index: true
          },
          name: String
        });

        const User = mongoose.model("User", UserSchema);

        module.exports = User;
        ```

13. add populate method in routes of bookmarks.js

    [Crows Foot Notation](http://www2.cs.uregina.ca/~bernatja/crowsfoot.html)

    [ERD Templates](https://moqups.com/templates/diagrams-flowcharts/erd/)

    ```jsx
    // Index v2
    router.get("/", (req, res, next) => {
      Bookmark.find({})
      // 1. Add a populate method so we don't have to make a second request
      .populate('owner')
      .then((bookmarks) => {
        res.json(bookmarks)
      })
      .catch(next)
    });

    // Show v2
    router.get("/:id", (req, res, next) => {
      Bookmark.findById(req.params.id)
      .populate('owner')
      .then((bookmark) => {
          res.json(bookmark)
        })
        .catch(next)
    });
    ```

14. Update Seeds.js

    ```jsx
    const mongoose = require('./connection');

    const Bookmark = require('../models/Bookmark');
    const User = require('../models/User');
    const bookmarkseeds = require('./seeds.json');

    Bookmark.deleteMany({})
      .then(() => User.deleteMany({}))
      .then(() => {
        // create a user
        return User.create({ email: 'fake@email.com', name: 'Fake Person' })
          .then((user) =>
          // get the newly created record with the user id
            bookmarkseeds.map((bookmark) => ({ ...bookmark, owner: user._id }))
          )
          // map over the bookmark seeds and set the owner as the user id
          .then((bookmarks) => Bookmark.insertMany(bookmarks));
      })
      .then(console.log)
      .catch(console.error)
      .finally(() => {
        process.exit();
      });
    ```

15. handle redirects

    In your index.js file, above the definitions for the API routes:

    ```jsx
    app.get("/", (req, res) => {
      res.redirect("/api/bookmarks");
    });
    ```

16. add the CORS dependency

    npm i cors

    import into index.js

    it is a way for the browser to be able to make a request from one site to another

    ```jsx
    const cors = require('cors')
    app.use(cors())
    ```

17. create an error handler

    inside index.js

    ```jsx
    // after controllers

    app.use((err, req, res, next) => {
      console.log('==========ERROR=========')
      console.log(err)
      console.log('method:', req.method)
      console.log('body:', req.body)
      console.log('========================')
      const statusCode = res.statusCode || 500;
      const message = err.message || 'Internal Server Error';
      res.status(statusCode).send(message);
    });

    // before port
    ```

    Flow of data:

    ```
    1. We start by connecting to mongo in our db/connection.js. 
    2. Next we export that connection.js and import it in our models/schema.js so our schema can be connected to mongo.
    3. Then we export the schema and import it in our controller so we can perform CRUD operations on our model.
    4. Finally we export our controller and import it into index.js so that our server can listen for any requests.
    ```

- Front End
    - app.js in react

        ```jsx
        import axios from 'axios'
        import React, { useState, useEffect } from 'react'
        import './App.css'

        function App() {
          const [bookmarks, setBookmarks] = useState([])

          const url = 'http://localhost:8000/api/bookmarks'

          useEffect(() => {
            fetch(url)
              .then(res => res.json())
              .then(res => setBookmarks(res))
              .catch(console.error)
          }, [])

          const updateTitle = (id) => {
            const data = {
              title: 'New Bookmark UPDATE from React2.5!',
              url: 'https://ga.co',
            };
            const url = 'http://localhost:8000/api/bookmarks/' + id;
            // fetch(url, {
            //   method: 'PUT',
            //   headers: {
            //     'Content-Type': 'application/json',
            //   },
            //   body: JSON.stringify(data),
            // })
            //   .then((res) => res.json())
            //   .then(console.log)
            //   .catch(console.error);
            axios({
              method: 'PUT',
              url,
              data
            })
              .then(console.log)
              .catch(console.error)
          };

          return (
            <div className="App">
              {bookmarks.map(bookmark => {
                return(
                  <div key={bookmark._id} onClick={() => updateTitle(bookmark._id)}>
                    {bookmark.title}
                  </div>
                )
              })}

            </div>
          );
        }

        export default App;
        ```

    - logic to what is happening
        1. when we made app.js in react we created a front-end, then we mapped the titles of the bookmarks on the front end web page --
        2. by using fetch to make a get request to our backend to grab all the bookmarks
        3. then we used the update title function to alter the data we clicked on --by using fetch to make a put request to our backend to update the given bookmark that we clicked on
        4. the logic is:
        5. grab this item’s id and change the following information about it with the following information being the title and url --that we hard-coded just to see how to send a put request from the frontend
        6. then when we changed the information it altered the content in the server --by using fetch to make a put request with the id we clicked on
        7. so now any of the original seeded data is hardcoded from whatever we wanted it to be in react (the title and url) in the server
        8. we used fetch originally then we leveled up and used axios which is like 10 times better
        9. so now the power in this is that we can change the back end by interacting with the front end --
        10. at first it looks like we’re just changing whats on the screen but we’re actually connected to the back end to change data from the client side

        (edited)
