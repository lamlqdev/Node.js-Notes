# Working with NoSQL & Using MongoDB 🚀

## Table of Contents

- [Working with NoSQL \& Using MongoDB 🚀](#working-with-nosql--using-mongodb-)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
    - [What is MongoDB? 🤔](#what-is-mongodb-)
    - [Key Features ⭐](#key-features-)
  - [Getting Started](#getting-started)
    - [1. Setting up MongoDB Atlas 🌐](#1-setting-up-mongodb-atlas-)
    - [2. Basic Connection 🔌](#2-basic-connection-)
  - [Basic Operations](#basic-operations)
    - [1. Create (Insert) 📝](#1-create-insert-)
    - [2. Read (Query) ����](#2-read-query-)
    - [3. Update ✏️](#3-update-️)
    - [4. Delete 🗑️](#4-delete-️)
  - [Data Relationships](#data-relationships)
    - [1. Embedded Documents (One-to-One) 📎](#1-embedded-documents-one-to-one-)
    - [2. References (One-to-Many) 🔗](#2-references-one-to-many-)
  - [MVC Implementation with MongoDB 🏗️](#mvc-implementation-with-mongodb-️)
    - [Project Structure](#project-structure)
    - [1. Database Configuration (util/database.js)](#1-database-configuration-utildatabasejs)
    - [2. Model Example (models/product.js)](#2-model-example-modelsproductjs)
    - [3. Controller Example (controllers/admin.js)](#3-controller-example-controllersadminjs)
    - [4. Routes Setup (routes/admin.js)](#4-routes-setup-routesadminjs)
    - [5. Express App Setup (app.js)](#5-express-app-setup-appjs)
    - [Key Implementation Notes:](#key-implementation-notes)
  - [Additional Resources 📚](#additional-resources-)

## Introduction

### What is MongoDB? 🤔

- **Definition**: A popular NoSQL database that stores data in JSON-like documents
- **When to Use**: Perfect for:
  - Applications with rapidly changing data
  - Projects requiring fast scalability
  - Applications with JSON-like data structures

### Key Features ⭐

- Flexible Schema
- Horizontal Scalability
- High Performance
- Document-Oriented Storage

## Getting Started

### 1. Setting up MongoDB Atlas 🌐

1. **Create MongoDB Atlas Account**:

   - Visit [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
   - Sign up for a free account
   - Create a new project

2. **Create a Cluster**:

   - Click "Build a Database"
   - Choose "FREE" shared cluster
   - Select cloud provider & region
   - Choose "M0 Sandbox" (Free tier)
   - Name your cluster

3. **Configure Access**:

   - Create Database User:

     - Go to Security → Database Access
     - Add new user with password
     - Select "Read and write to any database"

   - Set Network Access:
     - Go to Security → Network Access
     - Add IP Address
     - Use "Allow Access from Anywhere" (0.0.0.0/0) for development

4. **Get Connection String**:
   - Click "Connect" on your cluster
   - Choose "Connect your application"
   - Copy the connection string
   - Replace `<password>` with your database user password
   - Replace `<dbname>` with your database name

### 2. Basic Connection 🔌

```javascript
// .env file
MONGODB_URI=mongodb+srv://<username>:<password>@cluster0.mongodb.net/<database>

// connection.js
require('dotenv').config();
const { MongoClient } = require('mongodb');

const connect = async () => {
  try {
    const client = await MongoClient.connect(process.env.MONGODB_URI);
    console.log('Connected to MongoDB!');
    return client.db();
  } catch (error) {
    console.error('Connection failed:', error);
    throw error;
  }
};
```

## Basic Operations

### 1. Create (Insert) 📝

```javascript
// Insert one document
await collection.insertOne({
  name: "Product 1",
  price: 29.99,
  inStock: true,
});

// Insert multiple documents
await collection.insertMany([
  { name: "Product 2", price: 19.99 },
  { name: "Product 3", price: 39.99 },
]);
```

### 2. Read (Query) ����

```javascript
// Find all documents
const allProducts = await collection.find().toArray();

// Find with filters
const affordableProducts = await collection
  .find({
    price: { $lt: 30 },
  })
  .toArray();

// Find one document
const product = await collection.findOne({ name: "Product 1" });
```

### 3. Update ✏️

```javascript
// Update one document
await collection.updateOne({ name: "Product 1" }, { $set: { price: 24.99 } });

// Update many documents
await collection.updateMany(
  { inStock: false },
  { $set: { status: "discontinued" } }
);
```

### 4. Delete 🗑️

```javascript
// Delete one document
await collection.deleteOne({ name: "Product 1" });

// Delete many documents
await collection.deleteMany({ status: "discontinued" });
```

## Data Relationships

### 1. Embedded Documents (One-to-One) 📎

```javascript
{
  user: {
    name: "John",
    address: {
      street: "123 Main St",
      city: "Boston",
      country: "USA"
    }
  }
}
```

### 2. References (One-to-Many) 🔗

```javascript
// User document
{
  _id: ObjectId("user123"),
  name: "John"
}

// Orders document
{
  _id: ObjectId("order789"),
  userId: ObjectId("user123"),
  items: [...]
}
```

## MVC Implementation with MongoDB 🏗️

### Project Structure

```
src/
├── models/
│   ├── user.js
│   └── product.js
├── controllers/
│   ├── admin.js
│   ├── shop.js
│   └── error.js
├── routes/
│   ├── admin.js
│   └── shop.js
├── util/
│   ├── database.js
│   └── path.js
├── public/
│   ├── css/
│   └── js/
├── views/
│   ├── admin/
│   ├── includes/
│   └── shop/
└── app.js
```

### 1. Database Configuration (util/database.js)

```javascript
const mongodb = require("mongodb");
const MongoClient = mongodb.MongoClient;

let _db;

const mongoConnect = (callback) => {
  MongoClient.connect(
    "mongodb+srv://<username>:<password>@cluster0.mongodb.net/<dbname>?retryWrites=true"
  )
    .then((client) => {
      console.log("Connected!");
      _db = client.db();
      callback();
    })
    .catch((err) => {
      console.log(err);
      throw err;
    });
};

const getDb = () => {
  if (_db) {
    return _db;
  }
  throw "No database found!";
};

exports.mongoConnect = mongoConnect;
exports.getDb = getDb;
```

### 2. Model Example (models/product.js)

```javascript
const mongodb = require("mongodb");
const getDb = require("../util/database").getDb;

class Product {
  constructor(title, price, description, imageUrl, id, userId) {
    this.title = title;
    this.price = price;
    this.description = description;
    this.imageUrl = imageUrl;
    this._id = id ? new mongodb.ObjectId(id) : null;
    this.userId = userId;
  }

  save() {
    const db = getDb();
    let dbOp;
    if (this._id) {
      // Update the product
      dbOp = db
        .collection("products")
        .updateOne({ _id: this._id }, { $set: this });
    } else {
      dbOp = db.collection("products").insertOne(this);
    }
    return dbOp;
  }

  static fetchAll() {
    const db = getDb();
    return db.collection("products").find().toArray();
  }

  static findById(prodId) {
    const db = getDb();
    return db
      .collection("products")
      .find({ _id: new mongodb.ObjectId(prodId) })
      .next();
  }

  static deleteById(prodId) {
    const db = getDb();
    return db
      .collection("products")
      .deleteOne({ _id: new mongodb.ObjectId(prodId) });
  }
}
```

### 3. Controller Example (controllers/admin.js)

```javascript
const Product = require("../models/product");

exports.getAddProduct = (req, res, next) => {
  res.render("admin/edit-product", {
    pageTitle: "Add Product",
    path: "/admin/add-product",
    editing: false,
  });
};

exports.postAddProduct = (req, res, next) => {
  const title = req.body.title;
  const imageUrl = req.body.imageUrl;
  const price = req.body.price;
  const description = req.body.description;

  const product = new Product(
    title,
    price,
    description,
    imageUrl,
    null,
    req.user._id
  );

  product
    .save()
    .then((result) => {
      console.log("Created Product");
      res.redirect("/admin/products");
    })
    .catch((err) => console.log(err));
};

exports.getProducts = (req, res, next) => {
  Product.fetchAll()
    .then((products) => {
      res.render("admin/products", {
        prods: products,
        pageTitle: "Admin Products",
        path: "/admin/products",
      });
    })
    .catch((err) => console.log(err));
};
```

### 4. Routes Setup (routes/admin.js)

```javascript
const express = require("express");
const adminController = require("../controllers/admin");
const router = express.Router();

router.get("/add-product", adminController.getAddProduct);
router.get("/products", adminController.getProducts);
router.post("/add-product", adminController.postAddProduct);
router.get("/edit-product/:productId", adminController.getEditProduct);
router.post("/edit-product", adminController.postEditProduct);
router.post("/delete-product", adminController.postDeleteProduct);

module.exports = router;
```

### 5. Express App Setup (app.js)

```javascript
const path = require("path");
const express = require("express");
const bodyParser = require("body-parser");

const errorController = require("./controllers/error");
const mongoConnect = require("./util/database").mongoConnect;
const User = require("./models/user");

const app = express();

app.set("view engine", "ejs");
app.set("views", "views");

const adminRoutes = require("./routes/admin");
const shopRoutes = require("./routes/shop");

app.use(bodyParser.urlencoded({ extended: false }));
app.use(express.static(path.join(__dirname, "public")));

app.use((req, res, next) => {
  User.findById("5baa2528563f16379fc8a610")
    .then((user) => {
      req.user = new User(user.name, user.email, user.cart, user._id);
      next();
    })
    .catch((err) => console.log(err));
});

app.use("/admin", adminRoutes);
app.use(shopRoutes);

app.use(errorController.get404);

mongoConnect(() => {
  app.listen(3000);
});
```

### Key Implementation Notes:

1. **Database Connection**:

   - Singleton pattern used for database connection
   - Connection established once when app starts
   - Connection instance shared across application

2. **Models**:

   - Classes used for model definition
   - Static methods for database operations
   - MongoDB ObjectId handling for documents

3. **Controllers**:

   - Separation of business logic
   - Async/await or promises for database operations
   - Error handling implementation

4. **Routes**:

   - Modular routing with Express Router
   - Separate route files for different features
   - Clean URL structure

5. **Views**:
   - EJS templating engine
   - Reusable includes for common elements
   - Dynamic data rendering

## Additional Resources 📚

- [Official MongoDB Documentation](https://docs.mongodb.com)
- [MongoDB University](https://university.mongodb.com) - Free courses
- [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) - Cloud hosting
