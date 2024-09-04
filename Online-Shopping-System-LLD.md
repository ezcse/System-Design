# Online Shopping System

The Online Shopping System is a conceptual design for an e-commerce platform that enables users to browse and search for products, manage a shopping cart, and complete purchases through an intuitive interface. This system covers the essential functionalities required for an online shopping experience, including user registration, product management, order processing, and more.

## System Description

### Features

- **User Registration and Authentication**: Users can create accounts, log in, and manage their profiles.
- **Product Management**: Products can be added, categorized, and searched by users.
- **Shopping Cart**: Users can add, remove, and modify items in their shopping cart.
- **Order Processing**: Users can check out their shopping cart, generating orders.
- **Order Tracking**: Users can track their orders from creation to delivery.
- **Notifications**: Users are notified about changes in order status, shipping updates, etc.

### High-Level Workflow

1. **User Interaction**: Users interact with the system by registering, logging in, and browsing products.
2. **Product Search and Selection**: Users search for products by name or category and add desired items to their shopping cart.
3. **Cart Management**: Users view, modify, or remove items in their cart before proceeding to checkout.
4. **Order Placement and Tracking**: Users complete purchases by checking out, and the system generates orders for fulfillment. Users can track the status of their orders.

## Low-Level Design (LLD)

The Low-Level Design (LLD) provides a detailed view of the system's individual components, how they interact, and the specific responsibilities of each class. It serves as the blueprint for developers to understand and implement the system.

### Class Diagram

```plaintext
 +-----------------+        +-----------------+        +-------------------+
 |     User        |        |    Product      |        |    Order           |
 +-----------------+        +-----------------+        +-------------------+
 | - userId        |        | - id            |        | - date             |
 | - userName      |        | - name          |        | - time             |
 | - userEmail     |        | - description   |        | - mProductId       |
 | - address       |        | - productCategory|       +-------------------+
 | - fullName      |        +-----------------+        | + Order(string id) |
 | - isLoggedIn    |        | + getId()       |        +-------------------+
 | + loginUser()   |        | + getName()     |
 +-----------------+        | + getDescription|
                            | + getCategory() |
                            +-----------------+

 +--------------------------+        +---------------------------+
 |     ProductList          |        |         Cart               |
 +--------------------------+        +---------------------------+
 | - allProducts            |        | - userCart                |
 | - catFilter              |        +---------------------------+
 | + createProduct()        |        | + addToCart(string, Product*) |
 | + getAllProductsInCategory()|     | + removeFromCart(string, Product*)|
 | + searchProductByName()  |        | + checkoutCart(string, OrderList*)|
 +--------------------------+        +---------------------------+

 +----------------------------+       +----------------------------+
 |        OrderList            |       |      ShoppingSystem        |
 +----------------------------+       +----------------------------+
 | - userOrders               |       | - productList               |
 | + purchaseCartItems()      |       | - cart                      |
 | + purchase()               |       | - orderList                 |
 +----------------------------+       | - users                     |
                                       | + registerUser()            |
                                       | + loginUser()               |
                                       | + addProduct()              |
                                       | + searchProductByName()     |
                                       | + addProductToCart()        |
                                       | + viewCart()                |
                                       | + checkout()                |
                                       +----------------------------+
```

### 1. **User Management**

#### `User` Class
- **Purpose**: Manages user details and authentication.
- **Attributes**:
  - `userId`: A unique identifier for the user.
  - `userName`: The name of the user.
  - `userEmail`: The user's email address.
  - `address`: A list of addresses associated with the user.
  - `fullName`: The full name of the user.
  - `isLoggedIn`: A flag indicating whether the user is logged in.
- **Methods**:
  - `getUserId()`, `getUserEmail()`, `getAddress()`, `isLoggedIn()`: Accessor methods.
  - `addAddress()`: Adds a new address to the user's address list.
  - `loginUser()`: Logs in the user.

### 2. **Product Management**

#### `Product` Class
- **Purpose**: Represents a product in the system.
- **Attributes**:
  - `id`: A unique identifier for the product.
  - `name`: The name of the product.
  - `description`: A description of the product.
  - `productCategory`: The category to which the product belongs (e.g., Electronics, Home).
- **Methods**:
  - `getId()`, `getName()`, `getDescription()`, `getCategory()`: Accessor methods.

#### `ProductList` Class
- **Purpose**: Manages the collection of products available in the system.
- **Attributes**:
  - `allProducts`: A list of all products in the system.
  - `catFilter`: A map that categorizes products for easy filtering.
- **Methods**:
  - `createProduct()`: Adds a new product to the list.
  - `getAllProductsInCategory()`: Retrieves products by category.
  - `searchProductByName()`: Searches for products by name.

### 3. **Shopping Cart**

#### `Cart` Class
- **Purpose**: Manages the products that users wish to purchase.
- **Attributes**:
  - `userCart`: A map linking users to their respective shopping carts.
- **Methods**:
  - `addToCart()`: Adds a product to the user's cart.
  - `getCartItems()`: Retrieves items in the user's cart.
  - `removeFromCart()`: Removes a product from the user's cart.
  - `checkoutCart()`: Processes the items in the cart for purchase.

### 4. **Order Management**

#### `Order` Class
- **Purpose**: Represents an individual order made by the user.
- **Attributes**:
  - `date`: The date when the order was placed.
  - `time`: The time when the order was placed.
  - `mProductId`: The ID of the product associated with the order.
- **Methods**:
  - `Order()`: Constructor to create an order with the product ID, date, and time.

#### `OrderList` Class
- **Purpose**: Manages all the orders placed by users.
- **Attributes**:
  - `userOrders`: A map that stores orders for each user.
- **Methods**:
  - `purchaseCartItems()`: Converts the items in a user's cart into orders.
  - `purchase()`: Internal method to handle the purchase logic.

### 5. **Utility**

#### `GenerateUniqueId` Class
- **Purpose**: Generates unique IDs for products and users.
- **Attributes**:
  - `static int id`: A static integer used to generate unique IDs.
- **Methods**:
  - `getUniqueId()`: Increments and returns a unique ID as a string.

### 6. **System Integration**

#### `ShoppingSystem` Class
- **Purpose**: Integrates all functionalities into a cohesive system that users interact with.
- **Attributes**:
  - `productList`: An instance of `ProductList` to manage products.
  - `cart`: An instance of `Cart` to manage shopping carts.
  - `orderList`: An instance of `OrderList` to manage orders.
  - `users`: A map of users registered in the system.
- **Methods**:
  - `registerUser()`: Registers a new user.
  - `loginUser()`: Logs in an existing user.
  - `addProduct()`: Adds a new product to the system.
  - `searchProductByName()`: Searches for a product by its name.
  - `addProductToCart()`: Adds a product to a user's cart.
  - `viewCart()`: Displays the items in a user's cart.
  - `checkout()`: Processes the items in a user's cart for order placement.


## Implementation
Here's a possible implementation in C++:
```cpp
#include <iostream>
#include <string>
#include <vector>
#include <map>
#include <algorithm>
using namespace std;

// Class to generate unique IDs
class GenerateUniqueId {
private:
    static int id;

public:
    static string getUniqueId() {
        return std::to_string(++id);
    }
};

// Initialize the static member
int GenerateUniqueId::id = 0;

// Enum for product categories
enum Categories {
    Home,
    Kitchen,
    Electronics,
};

// Class representing a User
class User {
private:
    string userId;
    string userName;
    string userEmail;
    vector<string> address;
    string fullName;
    bool isLoggedIn;

public:
    User(string id, string name, string email) : userId(id), userName(name), userEmail(email), isLoggedIn(false) {}

    string getUserId() { return userId; }
    string getUserEmail() { return userEmail; }
    vector<string> getAddress() { return address; }
    bool getIsLoggedIn() { return isLoggedIn; }

    void addAddress(string newAddress) { address.push_back(newAddress); }
    void loginUser() { isLoggedIn = true; }
};

// Class representing a Product
class Product {
private:
    string id;
    string name;
    string description;
    Categories productCategory;

public:
    Product(string name, string description, Categories cat)
        : id(GenerateUniqueId::getUniqueId()), name(name), description(description), productCategory(cat) {}

    string getId() { return id; }
    string getName() { return name; }
    string getDescription() { return description; }
    Categories getCategory() { return productCategory; }
};

// Class to manage a list of products
class ProductList {
private:
    vector<Product*> allProducts;
    map<Categories, vector<Product*>> catFilter;

public:
    void createProduct(string name, string description, Categories productCat) {
        Product* newProd = new Product(name, description, productCat);
        allProducts.push_back(newProd);
        catFilter[productCat].push_back(newProd);
    }

    vector<Product*> getAllProductsInCategory(Categories productCat) {
        return catFilter[productCat];
    }

    Product* searchProductByName(string name) {
        for (auto& product : allProducts) {
            if (product->getName() == name)
                return product;
        }
        return nullptr;
    }
};

// Class representing a shopping cart
class Cart {
private:
    map<string, vector<Product*>> userCart;

public:
    void addToCart(string userId, Product* interestedProduct) {
        userCart[userId].push_back(interestedProduct);
    }

    vector<Product*> getCartItems(string userId) {
        return userCart[userId];
    }

    void removeFromCart(string userId, Product* unInterestedProduct) {
        auto& itemsInCart = userCart[userId];
        auto it = std::find_if(itemsInCart.begin(), itemsInCart.end(),
            [&unInterestedProduct](Product* product) {
                return product->getId() == unInterestedProduct->getId();
            });
        if (it != itemsInCart.end()) {
            itemsInCart.erase(it);
        }
    }

    void checkoutCart(string userId, class OrderList* oListObj);
};

// Class representing an Order
class Order {
private:
    string date;
    string time;
    string mProductId;

public:
    Order(string productId) : mProductId(productId) {
        date = "2024-08-30"; // Placeholder for date
        time = "12:00 PM";   // Placeholder for time
    }
};

// Class managing a list of orders
class OrderList {
private:
    map<string, vector<Order*>> userOrders;

    bool purchase(string userId, vector<Product*> cartItems) {
        for (auto& product : cartItems) {
            Order* newOrder = new Order(product->getId());
            userOrders[userId].push_back(newOrder);
        }
        return true;
    }

public:
    bool purchaseCartItems(string userId, vector<Product*> cartItems) {
        return purchase(userId, cartItems);
    }
};

// Definition of the Cart::checkoutCart method
void Cart::checkoutCart(string userId, OrderList* oListObj) {
    oListObj->purchaseCartItems(userId, userCart[userId]);
    userCart.erase(userId);
}

// Main ShoppingSystem class
class ShoppingSystem {
private:
    ProductList productList;
    Cart cart;
    OrderList orderList;
    vector<User*> users;

public:
    void registerUser(string name, string email) {
        string id = GenerateUniqueId::getUniqueId();
        User* newUser = new User(id, name, email);
        users.push_back(newUser);
        cout << "User registered: " << name << endl;
    }

    User* findUser(string email) {
        for (auto& user : users) {
            if (user->getUserEmail() == email) {
                return user;
            }
        }
        return nullptr;
    }

    void loginUser(string email) {
        User* user = findUser(email);
        if (user) {
            user->loginUser();
            cout << "User " << email << " logged in successfully!" << endl;
        } else {
            cout << "User not found!" << endl;
        }
    }

    void addProduct(string name, string description, Categories category) {
        productList.createProduct(name, description, category);
        cout << "Product added: " << name << endl;
    }

    void searchProductByName(string name) {
        Product* product = productList.searchProductByName(name);
        if (product) {
            cout << "Product found: " << product->getName() << ", " << product->getDescription() << endl;
        } else {
            cout << "Product not found!" << endl;
        }
    }

    void addProductToCart(string email, string productName) {
        User* user = findUser(email);
        if (user && user->getIsLoggedIn()) {
            Product* product = productList.searchProductByName(productName);
            if (product) {
                cart.addToCart(user->getUserId(), product);
                cout << "Product added to cart: " << productName << endl;
            } else {
                cout << "Product not found!" << endl;
            }
        } else {
            cout << "User not logged in!" << endl;
        }
    }

    void checkout(string email) {
        User* user = findUser(email);
        if (user && user->getIsLoggedIn()) {
            cart.checkoutCart(user->getUserId(), &orderList);
            cout << "Checkout completed!" << endl;
        } else {
            cout << "User not logged in!" << endl;
        }
    }

    void viewCart(string email) {
        User* user = findUser(email);
        if (user && user->getIsLoggedIn()) {
            auto items = cart.getCartItems(user->getUserId());
            cout << "Cart items:" << endl;
            for (auto& item : items) {
                cout << item->getName() << " - " << item->getDescription() << endl;
            }
        } else {
            cout << "User not logged in!" << endl;
        }
    }
};

// Main function to demonstrate the usage
int main() {
    ShoppingSystem system;

    // Register and log in a user
    system.registerUser("John Doe", "john.doe@example.com");
    system.loginUser("john.doe@example.com");

    // Add products to the system
    system.addProduct("Phone", "A smartphone", Electronics);
    system.addProduct("Laptop", "A gaming laptop", Electronics);

    // Search for a product
    system.searchProductByName("Phone");

    // Add a product to the cart
    system.addProductToCart("john.doe@example.com", "Phone");

    // View cart items
    system.viewCart("john.doe@example.com");

    // Checkout the cart
    system.checkout("john.doe@example.com");

    return 0;
}

```
### Example Workflow

1. **User Registration and Login**:
   - Register a user with name, email, and address.
   - Log in with the registered email.

2. **Product Management**:
   - Add new products with name, description, and category to the system.
   - Search for products by name or filter them by category.

3. **Shopping Cart Management**:
   - Add products to the shopping cart.
   - View the cart contents, modify quantities, or remove items.
   - Proceed to checkout to generate orders.

4. **Order Tracking**:
   - View the status of placed orders.

## Future Enhancements

- **Product Reviews and Ratings**: Implement functionality for users to leave reviews and rate products.
- **Payment Gateway Integration**: Add support for online payments via credit card, PayPal, etc.
- **Order Notifications**: Implement a system to notify users of order status updates, including shipment tracking.
- **Advanced Search**: Enhance the search functionality with filters like price range, brand, etc.

## Conclusion

This Online Shopping System LLD provides a comprehensive guide to understanding the internal workings of an e-commerce platform. It serves as a foundation for building more complex systems, with the potential for adding numerous features to enhance user experience.
