# Dynamic Routes in Node.js with Express

## Key Takeaways

### Dynamic Path Segments

- Dynamic path segments are defined using a colon (`:`) in the route path.
- Example route definition in the **controller**:
  ```javascript
  exports.getProduct = (req, res) => {
    const productId = req.params.productId; // Access dynamic segment
    res.send(`Product ID: ${productId}`);
  };
  ```
- Example route setup in the **routes** file:

  ```javascript
  const express = require("express");
  const productController = require("../controllers/product");

  const router = express.Router();

  router.get("/product/:productId", productController.getProduct);

  module.exports = router;
  ```

### Multiple Dynamic Segments

- More than one dynamic segment can be defined in a single route.
- Example:

  ```javascript
  exports.getUserPost = (req, res) => {
    const { userId, postId } = req.params;
    res.send(`User ID: ${userId}, Post ID: ${postId}`);
  };
  ```

  ```javascript
  router.get("/user/:userId/post/:postId", productController.getUserPost);
  ```

### Optional Query Parameters

- Query parameters are appended to the end of the URL using `?` for the first parameter and `&` for additional ones.
- Example URL: `/products?category=books&sort=asc`
- Query parameters can be extracted using `req.query`.
- Example:

  ```javascript
  exports.getProducts = (req, res) => {
    const category = req.query.category; // 'books'
    const sort = req.query.sort; // 'asc'
    res.send(`Category: ${category}, Sort: ${sort}`);
  };
  ```

  ```javascript
  router.get("/products", productController.getProducts);
  ```

### Handling Optional Parameters

- Since query parameters are optional, checks should be implemented to handle cases where they are not provided.
- Example:

  ```javascript
  exports.getProducts = (req, res) => {
    const category = req.query.category || "default"; // Fallback to 'default'
    res.send(`Category: ${category}`);
  };
  ```

## Working with Models

### Cart Model

- The cart model typically uses static methods to handle operations because individual cart instances are not frequently created.
- Example implementation in the **model**:

  ```javascript
  class Cart = {
    addToCart: (product) => {
      // Logic to add product to cart
    },
    removeFromCart: (productId) => {
      // Logic to remove product from cart
    },
  };

  module.exports = Cart;
  ```

### Interacting Between Models

- Models can interact, such as removing an item from the cart when a product is deleted.
- Example:

  ```javascript
  const Product = require("./product");
  const Cart = require("./cart");

  exports.deleteProduct = (productId) => {
    Product.delete(productId).then(() => {
      Cart.removeFromCart(productId);
    });
  };
  ```
