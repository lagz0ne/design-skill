# Data Model

## Core Entities

### User
```
User {
  id: UUID (PK)
  email: string (unique)
  password_hash: string
  first_name: string
  last_name: string
  role: enum (CUSTOMER, SELLER, ADMIN)
  created_at: timestamp
  updated_at: timestamp
}
```

### Seller Profile
```
SellerProfile {
  id: UUID (PK)
  user_id: UUID (FK -> User)
  shop_name: string
  description: text
  logo_url: string
  verified: boolean
  rating: decimal
  created_at: timestamp
}
```

### Product
```
Product {
  id: UUID (PK)
  seller_id: UUID (FK -> SellerProfile)
  name: string
  description: text
  price: decimal
  stock_quantity: integer
  category_id: UUID (FK -> Category)
  images: string[] (array of URLs)
  status: enum (ACTIVE, INACTIVE, OUT_OF_STOCK)
  created_at: timestamp
  updated_at: timestamp
}
```

### Category
```
Category {
  id: UUID (PK)
  name: string
  parent_id: UUID (FK -> Category, nullable)
  description: text
}
```

### Cart
```
Cart {
  id: UUID (PK)
  user_id: UUID (FK -> User)
  created_at: timestamp
  updated_at: timestamp
}
```

### CartItem
```
CartItem {
  id: UUID (PK)
  cart_id: UUID (FK -> Cart)
  product_id: UUID (FK -> Product)
  quantity: integer
  price_snapshot: decimal (price at time of adding)
  added_at: timestamp
}
```

### Order
```
Order {
  id: UUID (PK)
  user_id: UUID (FK -> User)
  order_number: string (unique)
  status: enum (PENDING, CONFIRMED, PROCESSING, SHIPPED, DELIVERED, CANCELLED)
  subtotal: decimal
  tax: decimal
  shipping_cost: decimal
  total: decimal
  shipping_address_id: UUID (FK -> Address)
  payment_id: UUID (FK -> Payment)
  created_at: timestamp
  updated_at: timestamp
}
```

### OrderItem
```
OrderItem {
  id: UUID (PK)
  order_id: UUID (FK -> Order)
  product_id: UUID (FK -> Product)
  seller_id: UUID (FK -> SellerProfile)
  quantity: integer
  price: decimal (price at time of order)
  subtotal: decimal
  status: enum (PENDING, CONFIRMED, SHIPPED, DELIVERED)
}
```

### Address
```
Address {
  id: UUID (PK)
  user_id: UUID (FK -> User)
  address_line1: string
  address_line2: string (nullable)
  city: string
  state: string
  postal_code: string
  country: string
  is_default: boolean
}
```

### Payment
```
Payment {
  id: UUID (PK)
  order_id: UUID (FK -> Order)
  payment_method: enum (CREDIT_CARD, DEBIT_CARD, PAYPAL, STRIPE)
  amount: decimal
  status: enum (PENDING, COMPLETED, FAILED, REFUNDED)
  transaction_id: string (from payment gateway)
  processed_at: timestamp
}
```

### Review
```
Review {
  id: UUID (PK)
  product_id: UUID (FK -> Product)
  user_id: UUID (FK -> User)
  order_item_id: UUID (FK -> OrderItem)
  rating: integer (1-5)
  comment: text
  created_at: timestamp
}
```

### Tracking
```
Tracking {
  id: UUID (PK)
  order_id: UUID (FK -> Order)
  carrier: string
  tracking_number: string
  status: enum (IN_TRANSIT, OUT_FOR_DELIVERY, DELIVERED)
  estimated_delivery: date
  updated_at: timestamp
}
```

## Relationships

- User (1) -> (0..1) SellerProfile
- User (1) -> (0..1) Cart
- User (1) -> (many) Orders
- User (1) -> (many) Addresses
- SellerProfile (1) -> (many) Products
- Product (1) -> (many) CartItems
- Product (1) -> (many) OrderItems
- Product (1) -> (many) Reviews
- Cart (1) -> (many) CartItems
- Order (1) -> (many) OrderItems
- Order (1) -> (1) Payment
- Order (1) -> (0..1) Tracking
- Category (1) -> (many) Products
- Category (1) -> (many) Category (parent-child)

## Indexes

### Performance Indexes
- User.email (unique)
- Product.seller_id
- Product.category_id
- Product.status
- Order.user_id
- Order.order_number (unique)
- OrderItem.order_id
- OrderItem.seller_id
- CartItem.cart_id
- Review.product_id
