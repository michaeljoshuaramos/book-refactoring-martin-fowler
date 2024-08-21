## Table of Contents

**A First Set of Refactoring**

1. [Extract Function](#extract-function)
1. [Inline Function](#inline-function)
1. [Extract Variable](#extract-variable)

**Encapsulation**

1. [Substitute Algorithm](#substitute-algorithm)

**Simplifying Conditional Logic**

1. [Decompose Conditional](#decompose-conditional)
1. [Consolidate Conditional Expression](#consolidate-conditional-expression)
1. [Replace Nested Conditional with Guard Clauses](#replace-nested-conditional-with-guard-clauses)
1. [Introduce Assertion](#introduce-assertion)

## Extract Function

**Before:**

```javascript
function processOrder(order) {
  // Calculate total price
  let totalPrice = 0;
  for (let item of order.items) {
    totalPrice += item.quantity * item.price;
  }

  // Send confirmation email
  let emailMessage = `Dear ${order.customerName},\n\n`;
  emailMessage += `Thank you for your order! Your total is $${totalPrice.toFixed(
    2
  )}.`;
  emailMessage += ` Your order is currently ${order.status}. We will notify you when it has been shipped.\n\n`;
  emailMessage += "Best regards,\nYour Store";
  sendEmail(order.customerEmail, "Order Confirmation", emailMessage);

  return totalPrice;
}
```

**After:**

```javascript
function processOrder(order) {
  const totalPrice = calculateTotalPrice(order.items);
  sendConfirmationEmail(
    order.customerName,
    order.customerEmail,
    totalPrice,
    order.status
  );
  return totalPrice;
}

function calculateTotalPrice(items) {
  return items.reduce((total, item) => total + item.quantity * item.price, 0);
}

function sendConfirmationEmail(name, email, totalPrice, status) {
  const message = `Dear ${name},\n\nThank you for your order! Your total is $${totalPrice.toFixed(
    2
  )}. Your order is currently ${status}. We will notify you when it has been shipped.\n\nBest regards,\nYour Store`;
  sendEmail(email, "Order Confirmation", message);
}
```

### **Motivation:**

- The purpose is to clarify what the code is supposed to do (the intent) and hide the details of how it does it (the implementation).

### Key Points

<a name="extract--function--call"></a><a name="1.1"></a>

- [1.1](#extract--function--call) Even if the code to extract is just a simple function call, it's worth doing if the new function name better clarifies the code's intent.

**Before:**

```javascript
function updateUserProfile(user) {
  saveUserData(user);
  sendEmail(user.email, "Your profile has been updated");
}
```

**After:**

```javascript
function updateUserProfile(user) {
  saveUserData(user);
  sendProfileUpdateNotification(user.email);
}

function sendProfileUpdateNotification(email) {
  sendEmail(email, "Your profile has been updated");
}
```

**[⬆ back to top](#table-of-contents)**

## Inline Function

**Before:**

```javascript
function calculateDiscountedPrice(price, discountRate) {
  return applyDiscount(price, discountRate);
}

function applyDiscount(price, discountRate) {
  return price - (price * discountRate) / 100;
}

const finalPrice = calculateDiscountedPrice(100, 10);
```

**After:**

```javascript
function calculateDiscountedPrice(price, discountRate) {
  return price - (price * discountRate) / 100;
}

const finalPrice = calculateDiscountedPrice(100, 10);
```

### **Motivation:**

- The purpose is to simplify the code by removing a function whose body is just as clear as its name. This applies when a function's abstraction no longer adds value, making the code more straightforward by inlining the function's logic directly into its callers.

**[⬆ back to top](#table-of-contents)**

## Extract Variable

**Before:**

```javascript
function calculateSubTotalPrice(items) { ... }

function calculateTotalPrice(order, discountRate) {
  const subTotalPrice = calculateSubTotalPrice(order.items);

  return subTotalPrice - (subTotalPrice * discountRate) / 100 > 150
    ? subTotalPrice - (subTotalPrice * discountRate) / 100
    : subTotalPrice - (subTotalPrice * discountRate) / 100 + 10;
}
```

**After:**

```javascript
function calculateSubTotalPrice(items) { ... }
function calculateDiscountedPrice(price, discountRate) { ... }

function calculateTotalPrice(order, discountRate) {
  const subTotalPrice = calculateSubTotalPrice(order.items);
  const discountedPrice = calculateDiscountedPrice(subTotalPrice, discountRate);
  const freeShippingThreshold = 150;
  const shippingCost = 10;

  return discountedPrice > freeShippingThreshold
    ? discountedPrice
    : discountedPrice + shippingCost;
}
```

### **Motivation:**

- The purpose is to improve code readability by introducing a named variable for an expression to clearly state the purpose or intent of that expression.

**[⬆ back to top](#table-of-contents)**

## Substitute Algorithm

**Before:**

```javascript
function getOnSaleProducts(products) {
  let onSaleProducts = [];
  for (let i = 0; i < products.length; i++) {
    if (products[i].onSale) {
      onSaleProducts.push(products[i]);
    }
  }
  return onSaleProducts;
}
```

**After:**

```javascript
function getOnSaleProducts(products) {
  return products.filter((product) => product.onSale);
}
```

### **Motivation:**

- The purpose is to improve code clarity and maintainability by replacing a complex or inefficient algorithm with a simpler or more efficient one.

**[⬆ back to top](#table-of-contents)**

## Decompose Conditional

**Before:**

```javascript
function getShippingCost(order) {
  if (order.isMember && order.total > 100) {
    return 0;
  } else if (order.total > 200) {
    return 0;
  } else {
    return 10;
  }
}
```

**After:**

```javascript
function getShippingCost(order) {
  if (isEligibleForFreeShipping(order)) {
    return 0;
  }
  return 10;
}

function isEligibleForFreeShipping(order) {
  return (order.isMember && order.total > 100) || order.total > 200;
}
```

### **Motivation:**

- The purpose is to improve code readability and maintainability by breaking down complex conditional statements into separate, self-explanatory methods or variables.

**[⬆ back to top](#table-of-contents)**

## Consolidate Conditional Expression

**Before:**

```javascript
function getDiscount(order) {
  if (order.customer.isMember) {
    return 10;
  }
  if (order.total > 100) {
    return 10;
  }
  if (order.promoCode === "SAVE10") {
    return 10;
  }
  return 0;
}
```

**After:**

```javascript
function isEligibleForDiscount(order) {
  return (
    order.customer.isMember || order.total > 100 || order.promoCode === "SAVE10"
  );
}

function getDiscount(order) {
  if (isEligibleForDiscount(order)) {
    return 10;
  }
  return 0;
}
```

### **Motivation:**

- The purpose is to simplify code by combining multiple conditional checks that lead to the same outcome into a single, unified condition.

**[⬆ back to top](#table-of-contents)**

## Replace Nested Conditional with Guard Clauses

**Before:**

```javascript
function processOrder(order) {
  if (order.isValid) {
    if (order.paymentStatus === "paid") {
      if (!order.isShipped) {
        shipOrder(order);
      }
    }
  }
}
```

**After:**

```javascript
function processOrder(order) {
  if (!order.isValid) return;
  if (order.paymentStatus !== "paid") return;
  if (order.isShipped) return;

  shipOrder(order);
}
```

### **Motivation:**

- The purpose is to simplify complex, deeply nested conditional logic by using guard clauses to handle special cases or exceptions upfront.

**[⬆ back to top](#table-of-contents)**

## Introduce Assertion

**Before:**

```javascript
function applyDiscount(order) {
  const discount = order.discountRate;
  const total = order.total;

  // Calculate discounted price
  order.finalPrice = total - (total * discount) / 100;
}
```

**After:**

```javascript
function applyDiscount(order) {
  console.assert(
    order.discountRate >= 0 && order.discountRate <= 100,
    "Discount rate must be between 0 and 100"
  );
  console.assert(order.total >= 0, "Total amount must be non-negative");

  const discount = order.discountRate;
  const total = order.total;

  // Calculate discounted price
  order.finalPrice = total - (total * discount) / 100;
}
```

### **Motivation:**

- The purpose is to make assumptions in the code explicit by adding assertions that check for expected conditions. This helps catch programming errors early, improves code clarity, and makes the code more robust by ensuring that certain conditions hold true during execution.

**[⬆ back to top](#table-of-contents)**
