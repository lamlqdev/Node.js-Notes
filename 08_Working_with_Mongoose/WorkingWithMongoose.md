# 🚀 Mongoose Guide for E-commerce Project

## 📑 Table of Contents

- [🚀 Mongoose Guide for E-commerce Project](#-mongoose-guide-for-e-commerce-project)
  - [📑 Table of Contents](#-table-of-contents)
  - [🤔 What is Mongoose?](#-what-is-mongoose)
  - [🔑 Key Concepts](#-key-concepts)
    - [1. Schema 📝](#1-schema-)
    - [2. Model 🏗️](#2-model-️)
    - [3. Documents 📄](#3-documents-)
  - [💻 Common Operations in Project](#-common-operations-in-project)
    - [1. Creating Documents ➕](#1-creating-documents-)
    - [2. Reading Documents 🔍](#2-reading-documents-)
    - [3. Updating Documents ✏️](#3-updating-documents-️)
    - [4. Deleting Documents 🗑️](#4-deleting-documents-️)
  - [🔗 Relationships in Project](#-relationships-in-project)
    - [1. References (Population)](#1-references-population)
    - [2. Embedded Documents](#2-embedded-documents)
  - [⚡ Middleware in Mongoose](#-middleware-in-mongoose)
  - [✅ Best Practices Used in Project](#-best-practices-used-in-project)
  - [🛠️ Common Methods Used in Project](#️-common-methods-used-in-project)
    - [User Methods](#user-methods)
  - [🔌 Connection Setup](#-connection-setup)
  - [🎯 Benefits of Using Mongoose in This Project](#-benefits-of-using-mongoose-in-this-project)
  - [🎉 Conclusion](#-conclusion)

## 🤔 What is Mongoose?

Mongoose is an Object Data Modeling (ODM) library for MongoDB and Node.js. It manages relationships between data, provides schema validation, and is used to translate between objects in code and the representation of those objects in MongoDB.

## 🔑 Key Concepts

### 1. Schema 📝

A Schema defines the structure of documents in a MongoDB collection.

```javascript
const productSchema = new Schema({
  title: {
    type: String,
    required: true,
  },
  price: {
    type: Number,
    required: true,
  },
  description: String,
  imageUrl: String,
  userId: {
    type: Schema.Types.ObjectId,
    ref: "User",
    required: true,
  },
});
```

### 2. Model 🏗️

A Model is a wrapper around the Schema that provides an interface to the database.

```javascript
const Product = mongoose.model("Product", productSchema);
```

### 3. Documents 📄

Documents are instances of Models. They represent actual data in MongoDB.

```javascript
const product = new Product({
  title: "Book",
  price: 29.99,
  description: "A good book",
  imageUrl: "book.jpg",
  userId: user._id,
});
```

## 💻 Common Operations in Project

### 1. Creating Documents ➕

```javascript
// Adding a new product
const product = new Product({...});
await product.save();

// Alternative method
await Product.create({...});
```

### 2. Reading Documents 🔍

```javascript
// Find all products
const products = await Product.find();

// Find product by ID
const product = await Product.findById(productId);

// Find products with conditions
const userProducts = await Product.find({ userId: user._id });
```

### 3. Updating Documents ✏️

```javascript
// Update a product
await Product.findByIdAndUpdate(productId, {
  title: "Updated Title",
  price: 39.99,
});
```

### 4. Deleting Documents 🗑️

```javascript
// Delete a product
await Product.findByIdAndRemove(productId);
```

## 🔗 Relationships in Project

### 1. References (Population)

Mongoose can populate references across collections:

```javascript
// User Schema with reference to Orders
const userSchema = new Schema({
  orders: [
    {
      type: Schema.Types.ObjectId,
      ref: "Order",
    },
  ],
});

// Populating orders
const user = await User.findById(userId).populate("orders");
```

### 2. Embedded Documents

Using nested schemas for cart items:

```javascript
const userSchema = new Schema({
  cart: {
    items: [
      {
        productId: {
          type: Schema.Types.ObjectId,
          ref: "Product",
        },
        quantity: Number,
      },
    ],
  },
});
```

## ⚡ Middleware in Mongoose

Mongoose middleware (also called pre and post hooks) are functions that run before or after certain operations:

```javascript
productSchema.pre("save", function (next) {
  // Do something before saving
  next();
});
```

## ✅ Best Practices Used in Project

1. **Validation**

```javascript
const productSchema = new Schema({
  title: {
    type: String,
    required: [true, "Title is required"],
    minlength: 3,
  },
});
```

2. **Error Handling**

```javascript
try {
  await product.save();
} catch (err) {
  console.log("Error saving product:", err);
}
```

3. **Indexes**

```javascript
productSchema.index({ title: 1 });
```

## 🛠️ Common Methods Used in Project

### User Methods

```javascript
// Add to cart
userSchema.methods.addToCart = function (product) {
  const cartProductIndex = this.cart.items.findIndex((cp) => {
    return cp.productId.toString() === product._id.toString();
  });
  // ... rest of the method
};

// Clear cart
userSchema.methods.clearCart = function () {
  this.cart = { items: [] };
  return this.save();
};
```

## 🔌 Connection Setup

```javascript
mongoose
  .connect("mongodb://localhost/shop", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => console.log("Connected to MongoDB"))
  .catch((err) => console.log("Connection error:", err));
```

## 🎯 Benefits of Using Mongoose in This Project

1. **Schema Validation** ✔️: Ensures data consistency
2. **Type Casting** 🔄: Automatically converts types
3. **Query Building** 🏗️: Provides an elegant API for MongoDB queries
4. **Middleware** ⚡: Allows for pre and post hooks
5. **Population** 🔗: Easy handling of relationships between collections
6. **Business Logic** 💡: Can add methods to models

## 🎉 Conclusion

Mongoose simplifies MongoDB operations in our Node.js application by providing a straightforward, schema-based solution to model application data. It handles data validation, query building, and business logic hooks, making it an essential tool for our e-commerce project.
