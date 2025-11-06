# User Flows

## Customer Flows

### 1. User Registration Flow
```
Start
  → Visit registration page
  → Enter email, password, name
  → Submit form
  → Validate input
  → Create user account
  → Send verification email
  → Redirect to login
End
```

### 2. Product Browsing Flow
```
Start
  → Visit homepage
  → Browse categories OR search products
  → Apply filters (price, category, rating)
  → View product list
  → Click on product
  → View product details
    - Images
    - Description
    - Price
    - Seller info
    - Reviews
  → Decision: Add to cart OR Continue browsing
End
```

### 3. Shopping Cart Flow
```
Start
  → View product details
  → Click "Add to Cart"
  → Update quantity (optional)
  → Continue shopping OR Proceed to checkout
  → View cart summary
    - List items
    - Calculate subtotal
    - Show taxes and shipping
  → Update quantities OR Remove items
  → Click "Checkout"
End (→ Checkout Flow)
```

### 4. Checkout Flow
```
Start (from cart)
  → Review cart items
  → Enter/Select shipping address
    - If no address: Enter new address
    - If existing: Select from saved addresses
  → Choose shipping method
    - Standard (5-7 days)
    - Express (2-3 days)
  → Enter payment information
    - Credit/Debit card
    - PayPal
    - Saved payment methods
  → Review order summary
    - Items
    - Shipping address
    - Payment method
    - Total cost
  → Click "Place Order"
  → Process payment
  → Create order
  → Send confirmation email
  → Display order confirmation
    - Order number
    - Estimated delivery
    - Tracking info (when available)
End
```

### 5. Order Tracking Flow
```
Start
  → Login to account
  → Navigate to "My Orders"
  → View order list (sorted by date)
  → Click on specific order
  → View order details
    - Order items
    - Order status
    - Shipping address
    - Payment info
  → View tracking information
    - Carrier
    - Tracking number
    - Current status
    - Estimated delivery
  → Optional: Contact seller
  → Optional: Leave review (after delivery)
End
```

### 6. Product Review Flow
```
Start
  → Navigate to "My Orders"
  → Find delivered order
  → Click "Write Review"
  → Rate product (1-5 stars)
  → Write review text
  → Submit review
  → Review appears on product page
End
```

## Seller Flows

### 1. Seller Registration Flow
```
Start
  → Register as user (basic flow)
  → Apply for seller account
  → Fill seller profile
    - Shop name
    - Description
    - Business details
  → Submit for verification
  → Admin reviews application
  → Approval notification
  → Access seller dashboard
End
```

### 2. Product Creation Flow
```
Start
  → Login to seller dashboard
  → Click "Add New Product"
  → Enter product details
    - Name
    - Description
    - Category
    - Price
    - Stock quantity
  → Upload product images (multiple)
  → Set product status (Active/Inactive)
  → Click "Save Product"
  → Product appears in catalog
End
```

### 3. Inventory Management Flow
```
Start
  → Login to seller dashboard
  → Navigate to "Inventory"
  → View product list
    - Product name
    - Stock quantity
    - Status
    - Sales
  → Select product to update
  → Update stock quantity
  → Update price (optional)
  → Update status (optional)
  → Save changes
  → System updates product availability
End
```

### 4. Order Fulfillment Flow
```
Start
  → Login to seller dashboard
  → Navigate to "Orders"
  → View pending orders
  → Click on order
  → View order details
    - Customer info
    - Products ordered
    - Shipping address
  → Prepare items for shipment
  → Update order status to "Processing"
  → Create shipping label
  → Ship items
  → Update status to "Shipped"
  → Enter tracking information
    - Carrier
    - Tracking number
  → System sends notification to customer
End
```

### 5. Sales Analytics Flow
```
Start
  → Login to seller dashboard
  → Navigate to "Analytics"
  → Select date range
  → View metrics
    - Total revenue
    - Number of orders
    - Top-selling products
    - Customer demographics
  → Export reports (optional)
  → Analyze trends
End
```

## Payment Processing Flow (Technical)

```
Start: User clicks "Place Order"
  ↓
1. Validate cart items
  - Check product availability
  - Verify stock quantities
  ↓
2. Calculate order total
  - Subtotal
  - Taxes
  - Shipping costs
  ↓
3. Create pending order
  - Generate order number
  - Store order details
  - Status: PENDING
  ↓
4. Process payment
  → Call payment gateway API
  → Send payment details (encrypted)
  ↓
5. Payment Gateway Response
  ├─ Success
  │   ↓
  │   Update order status: CONFIRMED
  │   Update inventory (reduce stock)
  │   Clear shopping cart
  │   Send confirmation email
  │   Display success page
  │   ↓
  │   End: Success
  │
  └─ Failed
      ↓
      Update order status: FAILED
      Display error message
      Keep items in cart
      ↓
      End: Failure
```

## Error Handling Flows

### Out of Stock
```
User adds product to cart
  → Proceeds to checkout
  → System checks stock
  → Product out of stock
  → Display error message
  → Remove item from cart
  → Allow user to continue with remaining items
```

### Payment Failure
```
User submits payment
  → Payment gateway returns error
  → Display specific error (card declined, insufficient funds)
  → Order remains in PENDING state
  → Allow user to retry with different payment method
  → After 3 failures, suggest contacting support
```

### Shipping Address Invalid
```
User enters shipping address
  → System validates address
  → Address validation fails
  → Highlight invalid fields
  → Suggest corrections
  → Allow user to re-enter or select saved address
```
