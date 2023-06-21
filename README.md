# Sequelize-study
Reference material for the use of Sequelize in a Express Application


# Common Sequelize Methods and Use.
Commonly used Sequelize methods along with use cases and explanations:

1. **define (name, attributes, options)**: This method is used to define a new model.

    **Sample use case:**
    ```javascript
    const User = sequelize.define('User', {
      firstName: {
        type: Sequelize.STRING,
        allowNull: false
      },
      lastName: {
        type: Sequelize.STRING
      }
    });
    ```
    **Explanation:** Here we're defining a new model named 'User' with two attributes: `firstName` and `lastName`. The `firstName` attribute is a string and cannot be null, while `lastName` is simply a string.


2. **sync({ force: true })**: This method syncs all defined models to the database by creating the appropriate tables if they do not exist, and `force: true` option will drop the table first if it already exists.

    **Sample use case:**
    ```javascript
    sequelize.sync({ force: true }).then(() => {
      console.log("Database & tables created!");
    });
    ```
    **Explanation:** Here we're syncing our defined models with the database. If the tables already exist, they will be dropped and recreated.


3. **create(values, options)**: This method builds a new model instance and saves it to the database.

    **Sample use case:**
    ```javascript
    User.create({ firstName: "John", lastName: "Doe" }).then(user => {
      console.log("New user's auto-generated ID:", user.id);
    });
    ```
    **Explanation:** We're creating a new 'User' instance with a `firstName` of "John" and a `lastName` of "Doe". The instance is then saved to the database.


4. **findAll(options)**: This method searches the database for all instances that match the options, and returns an array of instances.

    **Sample use case:**
    ```javascript
    User.findAll({
      where: {
        lastName: "Doe"
      }
    }).then(users => {
      console.log("All users:", JSON.stringify(users, null, 2));
    });
    ```
    **Explanation:** We're finding all 'User' instances where the `lastName` is "Doe". An array of matching instances is then returned.


5. **findOne(options)**: This method is similar to `findAll`, but it only returns the first instance that matches the options.

    **Sample use case:**
    ```javascript
    User.findOne({
      where: {
        firstName: "John",
        lastName: "Doe"
      }
    }).then(user => {
      console.log("User found:", user);
    });
    ```
    **Explanation:** We're finding a 'User' instance where the `firstName` is "John" and `lastName` is "Doe". If a matching instance is found, it is returned.


6. **update(values, options)**: This method updates instances in the database that match the options.

    **Sample use case:**
    ```javascript
    User.update({ lastName: "Smith" }, {
      where: {
        firstName: "John",
        lastName: "Doe"
      }
    }).then(() => {
      console.log("User updated");
    });
    ```
    **Explanation:** We're updating the `lastName` of 'User' instances where the `firstName` is "John" and `lastName` is "Doe" to "Smith".


7. **destroy(options)**: This method deletes instances from the database that match the options.

    **Sample use case:**
    ```javascript
    User.destroy({
      where: {
        lastName: "Smith"
      }


    }).then(() => {
      console.log("User deleted");
    });
    ```
    **Explanation:** We're deleting 'User' instances where the `lastName` is "Smith".

Remember that all these methods return Promises, hence the use of `.then()` to handle the result.


# Mock File structure

 Let's begin with an introduction to each technology and how they fit into the application:

- **Node.js** is a JavaScript runtime built on Chrome's V8 JavaScript engine. It is used for developing server-side and networking applications. 

- **Express.js** is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications.

- **Sequelize** is a promise-based Node.js ORM (Object-Relational Mapping) for SQL databases. It supports the dialects PostgreSQL, MySQL, SQLite, and MSSQL and features solid transaction support, relations, eager and lazy loading, and read replication.

- **MySQL** is a popular open-source relational database management system (RDBMS) that uses SQL (Structured Query Language).

- **Docker** is an open platform for developing, shipping, and running applications. Docker allows you to separate your applications from your infrastructure, so you can deliver software quickly.

In a nutshell, Node.js and Express.js handle the server-side operations, Sequelize connects your application with the MySQL database, and Docker is used to contain your application and its environment.

As for the file structure in a typical Node.js + Express.js + Sequelize + MySQL application, it might look something like this:

```
/myapp
  /node_modules
  /docker
    Dockerfile
  /src
    /config
      config.json
    /models
      index.js
    /migrations
    /seeders
    /routes
      index.js
    /controllers
    /middleware
    server.js
  .sequelizerc
  package.json
  docker-compose.yml
```







Firstly, we should set up a basic Express.js application with Sequelize. To do this, we need to install some packages:

```bash
npm install express sequelize mysql2
```

**Express** is the web framework, **Sequelize** is the ORM we will use to interact with our MySQL database, and **mysql2** is a MySQL driver for Node.js.

For the purpose of this guide, let's imagine we're building a simple blog platform where users can create and read posts.

**Step 1: Set Up Express.js Server**

We'll start by setting up a basic Express.js server in `server.js`:

```javascript
const express = require('express');
const app = express();
const port = process.env.PORT || 3000;

app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});
```

Here, `require('express')` imports the Express module. `express()` initializes a new Express application. We then set the application to listen on a specified port, logging a message to the console to confirm that it's running.

**Step 2: Set Up Sequelize**

Let's initialize Sequelize. This creates the necessary configuration files and directories. Run this command in your terminal:

```bash
npx sequelize-cli init
```

The `npx sequelize-cli init` command initializes Sequelize and creates the following directories and files:

- `config/config.json`: This file contains configuration for your environments.
- `models/index.js`: This file connects Sequelize with your database.
- `migrations`: This directory contains all your migration files.
- `seeders`: This directory contains all your seeder files.

**Step 3: Configure Sequelize**

Next, you'll need to tell Sequelize how to connect to your database. Open up `config/config.json` and replace its contents with your database credentials:

```json
{
  "development": {
    "username": "root",
    "password": null,
    "database": "blog_dev",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  ...
}
```

The `username`, `password`, `database`, and `host` should match your MySQL setup.

**Step 4: Define Models**

Let's define a model for our blog posts. In Sequelize, models represent database tables and can be used to perform database operations. Create a file called `post.js` in the `models` folder:

```javascript
module.exports = (sequelize, DataTypes) => {
  const Post = sequelize.define('Post', {
    title: DataTypes.STRING,
    content: DataTypes.TEXT
  });

  return Post;
};
```

In this file, we're defining a `Post` model with `title` and `content` fields. The `define` method creates a model in Sequelize.

Next, we need to run the migrations. Sequelize provides a simple command to do that:

```bash
npx sequelize-cli db:migrate
```

**Step 5: Set Up Routes and Controllers**

Routes define the endpoints of your application, and controllers handle the business logic for these endpoints. Create a file called `posts.js` in the `routes` directory:

```javascript
const express = require('express');
const router = express.Router();
const { Post } = require('../models');

router.get('/', async (req, res) => {
  const posts = await Post.findAll();
  res.json(posts);
});

router.post('/', async (req, res) => {
  const newPost = new Post(req.body);
  const savedPost = await newPost.save();
  res.json(savedPost);
});

module.exports = router;
```

Here, we've defined two routes: a GET route for retrieving all posts and a POST route for creating a new post.

This is a brief start to an application using Node.js, Express.js, and Sequelize. 

# CRUD Principals and implementation.

 CRUD stands for Create, Read, Update, and Delete, representing the four basic operations you can perform on any data storage.

In an Express.js app with Sequelize, we can define CRUD operations using different HTTP methods such as GET, POST, PUT/PATCH, and DELETE. Let's discuss each one of them in the context of our blog platform:

1. **CREATE (POST)**: This typically involves using the HTTP POST method to send data from the client to the server. In Sequelize, we can use the `create` method:

    ```javascript
    router.post('/', async (req, res) => {
        const post = await Post.create(req.body);
        res.json(post);
    });
    ```
    In this case, `router.post` creates a route that listens for POST requests. `Post.create(req.body)` creates a new Post in the database using the data in `req.body`. This data is sent from the client in the body of the POST request.

2. **READ (GET)**: This involves using the HTTP GET method to retrieve data from the server. In Sequelize, we can use the `findAll` or `findOne` method:

    ```javascript
    // get all posts
    router.get('/', async (req, res) => {
        const posts = await Post.findAll();
        res.json(posts);
    });

    // get one post
    router.get('/:id', async (req, res) => {
        const post = await Post.findOne({ where: { id: req.params.id } });
        res.json(post);
    });
    ```
    In these examples, `router.get` creates a route that listens for GET requests. `Post.findAll()` retrieves all Posts from the database, and `Post.findOne({ where: { id: req.params.id } })` retrieves a single Post where the id matches `req.params.id`. The `:id` in the path of the GET request is a route parameter, allowing the client to specify which post they want to retrieve.

3. **UPDATE (PUT/PATCH)**: This involves using the HTTP PUT or PATCH method to update data on the server. In Sequelize, we can use the `update` method:

    ```javascript
    router.put('/:id', async (req, res) => {
        await Post.update(req.body, { where: { id: req.params.id } });
        const updatedPost = await Post.findOne({ where: { id: req.params.id } });
        res.json(updatedPost);
    });
    ```
    Here, `router.put` creates a route that listens for PUT requests. `Post.update(req.body, { where: { id: req.params.id } })` updates the Post where the id matches `req.params.id` with the data in `req.body`.

4. **DELETE (DELETE)**: This involves using the HTTP DELETE method to remove data from the server. In Sequelize, we can use the `destroy` method:

    ```javascript
    router.delete('/:id', async (req, res) => {
        await Post.destroy({ where: { id: req.params.id } });
        res.json({ message: 'Post deleted' });
    });
    ```
    In this case, `router.delete` creates a route that listens for DELETE requests. `Post.destroy({ where: { id: req.params.id } })` deletes the Post where the id matches `req.params.id`.

Remember, these CRUD operations constitute the most fundamental actions we perform in the database. The methods mentioned (`create`, `findAll`, `findOne`, `update`, and `destroy`) are part of the Sequelize model API, which provides easy-to-use methods for these operations.

# Sequelize relationships and management.

How to use Sequelize to manage the relationships between different data models. In many web applications, you'll have multiple models and associations between them. For instance, in our blog application, we might have `User` and `Post` models, and each user can have many posts.

To create the `User` model, you can run:

```bash
npx sequelize-cli model:generate --name User --attributes firstName:string,lastName:string,email:string
```

This creates a `User` model with `firstName`, `lastName`, and `email` attributes, all of type string. Sequelize creates a new model file for `User` in the `models` directory.

Next, let's define an association between our `User` and `Post` models. We can say that a `User` has many `Post`s, and a `Post` belongs to a `User`. This creates a one-to-many relationship between users and posts. You can define this in the model files as follows:

```javascript
// in user.js
module.exports = (sequelize, DataTypes) => {
  const User = sequelize.define('User', {
    firstName: DataTypes.STRING,
    lastName: DataTypes.STRING,
    email: DataTypes.STRING
  });

  User.associate = function(models) {
    User.hasMany(models.Post);
  };

  return User;
};

// in post.js
module.exports = (sequelize, DataTypes) => {
  const Post = sequelize.define('Post', {
    title: DataTypes.STRING,
    content: DataTypes.TEXT
  });

  Post.associate = function(models) {
    Post.belongsTo(models.User);
  };

  return Post;
};
```

In Sequelize, `hasMany` and `belongsTo` are association methods that set up a one-to-many relationship between `User` and `Post`.

Once the relationship is established, you can perform operations on associated models. For example, to create a new post for a specific user:

```javascript
const user = await User.findOne({ where: { email: 'user@example.com' } });
const newPost = await user.createPost({
  title: 'New Post',
  content: 'This is the content of the new post.'
});
```

The `createPost` method is automatically added to a `User` instance by Sequelize as a result of the `User.hasMany(Post)` association. It creates a `Post` instance that is associated with the `User`.

To get all posts for a user:

```javascript
const user = await User.findOne({ where: { email: 'user@example.com' } });
const posts = await user.getPosts();
```

The `getPosts` method is another automatically added method that retrieves all `Post` instances associated with the `User`.

These are just a few examples of how Sequelize can be used to manage relationships between models. Other association types, such as many-to-many and one-to-one, can be handled in similar ways.

#Relationship associations and examples.
In Sequelize, there are several types of associations that you can use to set up relationships between models:

1. **One-to-One**: Each instance of one model is associated with one and only one instance of another model. For example, let's assume that each `User` has one `Profile`. Here's how you could set that up:

    ```javascript
    // in user.js
    User.associate = function(models) {
      User.hasOne(models.Profile);
    };

    // in profile.js
    Profile.associate = function(models) {
      Profile.belongsTo(models.User);
    };
    ```

    `User.hasOne(models.Profile)` indicates that a one-to-one association is being created between `User` and `Profile`.

    Now, if you have a `User` instance, you can create a `Profile` for it like so:

    ```javascript
    const user = await User.findOne({ where: { email: 'user@example.com' } });
    const profile = await user.createProfile({ bio: 'Hello, world!' });
    ```

    `user.createProfile` is a method that Sequelize gives us to create a `Profile` associated with `user`.

2. **One-to-Many**: Each instance of one model is associated with multiple instances of another model. We've already seen an example of this with `User` and `Post`, but here's another one. Let's say each `User` can have many `Task`s:

    ```javascript
    // in user.js
    User.associate = function(models) {
      User.hasMany(models.Task);
    };

    // in task.js
    Task.associate = function(models) {
      Task.belongsTo(models.User);
    };
    ```

    Now, if you have a `User` instance, you can create a `Task` for it like so:

    ```javascript
    const user = await User.findOne({ where: { email: 'user@example.com' } });
    const task = await user.createTask({ description: 'Learn Sequelize' });
    ```

    And you can fetch all tasks for a user like this:

    ```javascript
    const tasks = await user.getTasks();
    ```

3. **Many-to-Many**: Multiple instances of one model are associated with multiple instances of another model. For example, let's assume a `Post` can have many `Tag`s, and a `Tag` can be associated with many `Post`s. Here's how you could set that up:

    ```javascript
    // in post.js
    Post.associate = function(models) {
      Post.belongsToMany(models.Tag, { through: 'PostTag' });
    };

    // in tag.js
    Tag.associate = function(models) {
      Tag.belongsToMany(models.Post, { through: 'PostTag' });
    };
    ```

    The `through` option is required for many-to-many relationships and represents the join table in the database. In this case, Sequelize will create a `PostTag` table for us.

    Now, if you have a `Post` instance and a `Tag` instance, you can associate them like so:

    ```javascript
    const post = await Post.findOne({ where: { title: 'Hello, Sequelize' } });
    const tag = await Tag.findOne({ where: { name: 'tutorial' } });
    await post.addTag(tag);
    ```

    The `addTag` method associates a `Tag` with a `Post`. Sequelize provides similar methods for all association types, such as `addTags`, `setTags`, `removeTag`, and `removeTags`.

Associations in Sequelize are powerful because they let you create complex relationships between models with relatively little code. Each association type has its use cases and can be used to model different kinds of relationships between data entities. 

# Querying Data

 Sequelize provides a powerful query interface that allows you to fetch, update, and delete data in a variety of ways.

Common querying operations in Sequelize:

1. **Fetching Data**

    - *Find All*: This retrieves all instances of a model. In the context of our blog application, you might want to retrieve all `Post` instances:

        ```javascript
        const posts = await Post.findAll();
        ```

    - *Find One*: This retrieves a single instance that matches a specific condition. For example, to find a `User` with a specific email:

        ```javascript
        const user = await User.findOne({ where: { email: 'user@example.com' } });
        ```

    - *Find by Primary Key*: This retrieves a single instance by its primary key:

        ```javascript
        const user = await User.findByPk(1);  // Finds the user with primary key (id) 1.
        ```

2. **Updating Data**: To update an instance, you first fetch it, modify its properties, and then call the `save` method:

    ```javascript
    const user = await User.findOne({ where: { email: 'user@example.com' } });
    user.email = 'new-email@example.com';
    await user.save();
    ```

    You can also update multiple instances that match a specific condition using the `update` method:

    ```javascript
    await User.update({ email: 'new-email@example.com' }, { where: { firstName: 'John' } });
    ```

    This updates the email of all users whose first name is 'John'.

3. **Deleting Data**: To delete an instance, you fetch it and then call the `destroy` method:

    ```javascript
    const user = await User.findOne({ where: { email: 'user@example.com' } });
    await user.destroy();
    ```

    To delete multiple instances that match a specific condition, use the `destroy` method on the model:

    ```javascript
    await User.destroy({ where: { firstName: 'John' } });
    ```

    This deletes all users whose first name is 'John'.

4. **Complex Queries**: Sequelize also supports more complex queries, including aggregation, sorting, limiting, and eager loading of associated models. Here's an example of a complex query:

    ```javascript
    const users = await User.findAll({
      where: {
        [Op.or]: [
          { firstName: 'John' },
          { lastName: 'Doe' }
        ]
      },
      order: [['createdAt', 'DESC']],
      limit: 10,
      include: [{
        model: Post,
        where: { title: { [Op.iLike]: '%hello%' } },
        required: false
      }]
    });
    ```

    This query retrieves the first 10 users whose first name is 'John' or last name is 'Doe', ordered by their creation date in descending order. For each user, it also fetches their associated posts that have a title containing 'hello' (case-insensitive).

# Hooks and implementation within a Sequelize structure.

**What are Hooks?**
Hooks, also known as lifecycle events, are functions that get executed at different stages of the Sequelize lifecycle. They are used to perform actions or side effects, such as data modification, validation, or logging, at specific stages of the lifecycle of a model instance.

Hooks can be divided into two broad categories:

1. **Query hooks:** These hooks are executed before and after a database query is executed. Examples include `beforeFind`, `afterFind`, `beforeBulkCreate`, and `afterBulkCreate`.

2. **Instance hooks:** These hooks are executed before and after changes to individual instances, such as `beforeSave`, `afterSave`, `beforeCreate`, `afterCreate`.

Generic examples and explanation of the step by step how to write the syntax for common hooks:

**Example 1: `beforeCreate` Hook**

Here, we're defining a hook that hashes a user's password before it's saved to the database. This is an instance hook because it's triggered when creating a new instance of the model:

```javascript
const bcrypt = require('bcrypt');

const User = sequelize.define('User', {
  //...
  password: Sequelize.STRING
}, {
  hooks: {
    beforeCreate: async (user) => {
      const salt = await bcrypt.genSalt();
      user.password = await bcrypt.hash(user.password, salt);
    }
  }
});
```
**Step-by-step Explanation:**
1. We first import `bcrypt`, a password-hashing library.
2. When defining our `User` model, we specify a `hooks` object within the model options (the third argument of `sequelize.define`).
3. Inside the `hooks` object, we define our `beforeCreate` hook. This function is asynchronous because it uses `bcrypt.genSalt()` and `bcrypt.hash()`, both of which return Promises.
4. The `beforeCreate` function takes the user instance being created as an argument.
5. We generate a salt using `bcrypt.genSalt()`, which is a string of random characters used to add complexity to the password hashing process.
6. We hash the user's password with the generated salt using `bcrypt.hash()`. The user's plain text password is then replaced with this hash.

**Example 2: `beforeBulkCreate` Hook**

This is an example of a query hook which is triggered when multiple instances are created in the database:

```javascript
const User = sequelize.define('User', {
  //...
}, {
  hooks: {
    beforeBulkCreate: (users) => {
      for (let i = 0; i < users.length; i++) {
        users[i].dataValues.createdAt = new Date();
      }
    }
  }
});
```

**Step-by-step Explanation:**
1. We define a `beforeBulkCreate` hook within our `User` model.
2. The `beforeBulkCreate` function takes an array of user instances being created as an argument.
3. We loop over the array of user instances.
4. For each instance, we set the `createdAt` attribute to the current date and time. The `dataValues` object contains the actual values of the instance's fields.

Sequelize automatically adds `createdAt` and `updatedAt` timestamps to our models, but we can manipulate these values using hooks if needed.





