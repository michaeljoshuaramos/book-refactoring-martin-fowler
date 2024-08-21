## Table of Contents

1. [Extract Function](#extract--function)

## Extract Function

**Before:**

```javascript
function printOwing(invoice) {
  printBanner();

  let outstanding = calculateOutstanding();

  // print details
  console.log(`name: ${invoice.customer}`);
  console.log(`amount: ${outstanding}`);
}
```

**After:**

```javascript
function printOwing(invoice) {
  printBanner();

  let outstanding = calculateOutstanding();
  printDetails(outstanding);

  function printDetails(outstanding) {
    console.log(`name: ${invoice.customer}`);
    console.log(`amount: ${outstanding}`);
  }
}
```

### **Motivation:**

- The purpose is to clarify what the code is supposed to do (the intent) and hide the details of how it does it (the implementation).

### **Mechanics:**

### **Example:**

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
