## Table of Contents

1. [Extract Function](#extract--function)
1. [Inline Function](#inline--function)

## Extract Function

**Before:**

```javascript
function processOrder(order) {
  // Calculate total price
  let totalPrice = 0;
  for (let item of order.items) {
    let itemTotal = item.quantity * item.price;
    if (item.category === "grocery") {
      itemTotal *= 0.9; // Apply discount for groceries
    }
    totalPrice += itemTotal;
  }

  // Update order status
  if (order.paymentStatus === "paid") {
    order.status = "processing";
  } else if (order.paymentStatus === "pending") {
    order.status = "awaiting payment";
  } else {
    order.status = "payment failed";
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
  order.status = determineOrderStatus(order.paymentStatus);
  sendOrderConfirmationEmail(
    order.customerName,
    order.customerEmail,
    totalPrice,
    order.status
  );
  return totalPrice;
}

function calculateTotalPrice(items) {
  let totalPrice = 0;
  for (let item of items) {
    let itemTotal = item.quantity * item.price;
    if (item.category === "grocery") {
      itemTotal *= 0.9; // Apply discount for groceries
    }
    totalPrice += itemTotal;
  }
  return totalPrice;
}

function determineOrderStatus(paymentStatus) {
  if (paymentStatus === "paid") {
    return "processing";
  } else if (paymentStatus === "pending") {
    return "awaiting payment";
  } else {
    return "payment failed";
  }
}

function sendOrderConfirmationEmail(
  customerName,
  customerEmail,
  totalPrice,
  status
) {
  let emailMessage = `Dear ${customerName},\n\n`;
  emailMessage += `Thank you for your order! Your total is $${totalPrice.toFixed(
    2
  )}.`;
  emailMessage += ` Your order is currently ${status}. We will notify you when it has been shipped.\n\n`;
  emailMessage += "Best regards,\nYour Store";
  sendEmail(customerEmail, "Order Confirmation", emailMessage);
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
function getDiscountedPrice(price, discount) {
  return applyDiscount(price, discount);
}

function applyDiscount(amount, discount) {
  return amount * discount;
}

const finalPrice = getDiscountedPrice(100, 0.9);
```

**After:**

```javascript
function getDiscountedPrice(price, discount) {
  return price * 0.9;
}

const finalPrice = getDiscountedPrice(100, 0.9);
```

### **Motivation:**

- The purpose is to simplify the code by removing a function whose body is just as clear as its name. This refactoring is applied when a function's abstraction no longer adds value, making the code more straightforward by inlining the function's logic directly into its callers.

**[⬆ back to top](#table-of-contents)**
