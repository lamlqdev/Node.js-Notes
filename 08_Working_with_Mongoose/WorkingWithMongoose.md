
# 🚀 Working with Mongoose

## 📚 Introduction to Mongoose

Mongoose is an Object Data Modeling (ODM) library for MongoDB and Node.js. It provides a straightforward, schema-based solution to model application data.

### Key Concepts

#### 1. Schema
A Schema defines the structure of documents in a MongoDB collection:

```javascript
const userSchema = new Schema({
  name: { type: String, required: true },
  email: { type: String, required: true },
  age: Number,
  createdAt: { type: Date, default: Date.now }
});
```

#### 2. Model 
Models are constructors compiled from Schema definitions:

```javascript
const User = mongoose.model('User', userSchema);
```

#### 3. Documents
Instances of Models are documents:

```javascript
const user = new User({
  name: 'John',
  email: 'john@example.com'
});
```

## 🔍 Project Analysis

### Project Structure
The example project demonstrates a typical e-commerce application using Mongoose:

```
├── models/
│   ├── product.js
│   ├── user.js
│   └── order.js
├── controllers/
│   ├── admin.js
│   └── shop.js
└── app.js
```

### Key Implementation Details

#### 1. Database Connection
```javascript
mongoose.connect(
  'mongodb+srv://username:password@cluster.mongodb.net/shop',
  { useNewUrlParser: true }
)
```

#### 2. Schema Relationships
Products and Users are related using references:

```javascript
// Product Schema
const productSchema = new Schema({
  title: String,
  price: Number,
  userId: {
    type: Schema.Types.ObjectId,
    ref: 'User',
    required: true
  }
});
```

#### 3. Methods
Custom methods can be added to schemas:

```javascript
userSchema.methods.addToCart = function(product) {
  const cartProductIndex = this.cart.items.findIndex(cp => {
    return cp.productId.toString() === product._id.toString();
  });
  // ... cart logic
};
```

## 💡 Best Practices

### 1. Schema Design
- Always define `required` fields
- Use appropriate data types
- Add validation where needed
- Set default values when applicable

```javascript
const schema = new Schema({
  name: { 
    type: String, 
    required: true,
    trim: true 
  },
  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true
  }
});
```

### 2. Queries
- Use lean() for read-only operations
- Chain queries properly
- Handle errors appropriately

```javascript
// Good practice
const products = await Product
  .find({ price: { $gt: 100 }})
  .select('title price')
  .lean()
  .exec();

// Error handling
try {
  await product.save();
} catch (err) {
  console.error('Error saving product:', err);
}
```

### 3. Indexes
- Create indexes for frequently queried fields
- Use compound indexes for multiple field queries

```javascript
productSchema.index({ title: 1, price: -1 });
```

### 4. Population
- Use populate() wisely
- Select only needed fields

```javascript
const order = await Order
  .findById(id)
  .populate('userId', 'name email')
  .exec();
```

## 🎯 Common Operations

### Creating Documents
```javascript
const product = new Product({
  title: 'Book',
  price: 29.99
});
await product.save();
```

### Reading Documents
```javascript
// Find one
const product = await Product.findById(productId);

// Find many
const products = await Product.find({ price: { $lt: 100 }});
```

### Updating Documents
```javascript
// Update one
await Product.updateOne(
  { _id: productId },
  { $set: { price: 39.99 }}
);

// Update many
await Product.updateMany(
  { category: 'books' },
  { $inc: { price: 5 }}
);
```

### Deleting Documents
```javascript
// Delete one
await Product.deleteOne({ _id: productId });

// Delete many
await Product.deleteMany({ category: 'obsolete' });
```

## 🚫 Common Pitfalls to Avoid

1. Not handling promises properly
2. Forgetting to use `await` with async operations
3. Not validating input data
4. Not implementing proper error handling
5. Over-nesting documents instead of using references

## 🔑 Tips for Performance

1. Use projection to select only needed fields
2. Create appropriate indexes
3. Use `lean()` for read operations
4. Batch operations when possible
5. Implement pagination for large datasets

```javascript
// Example of pagination
const page = 1;
const limit = 10;
const products = await Product
  .find()
  .skip((page - 1) * limit)
  .limit(limit)
  .lean();
```

## 📝 Conclusion

Mongoose provides a powerful and intuitive way to work with MongoDB in Node.js applications. By following these best practices and understanding core concepts, you can build efficient and maintainable applications.

Remember to:
- Design schemas carefully
- Handle errors appropriately
- Follow MongoDB best practices
- Keep your code clean and organized
- Use Mongoose features wisely
