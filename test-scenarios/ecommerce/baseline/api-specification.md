# API Specification

## Authentication

All authenticated endpoints require a JWT token in the Authorization header:
```
Authorization: Bearer <token>
```

## User Service APIs

### Register User
```
POST /api/v1/auth/register

Request:
{
  "email": "user@example.com",
  "password": "securePassword123",
  "firstName": "John",
  "lastName": "Doe",
  "role": "CUSTOMER" // or "SELLER"
}

Response: 201 Created
{
  "id": "uuid",
  "email": "user@example.com",
  "firstName": "John",
  "lastName": "Doe",
  "role": "CUSTOMER",
  "createdAt": "2024-01-15T10:30:00Z"
}
```

### Login
```
POST /api/v1/auth/login

Request:
{
  "email": "user@example.com",
  "password": "securePassword123"
}

Response: 200 OK
{
  "token": "jwt_token_string",
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "role": "CUSTOMER"
  }
}
```

### Get User Profile
```
GET /api/v1/users/{userId}

Headers:
  Authorization: Bearer <token>

Response: 200 OK
{
  "id": "uuid",
  "email": "user@example.com",
  "firstName": "John",
  "lastName": "Doe",
  "role": "CUSTOMER",
  "addresses": [
    {
      "id": "uuid",
      "addressLine1": "123 Main St",
      "city": "New York",
      "state": "NY",
      "postalCode": "10001",
      "country": "USA",
      "isDefault": true
    }
  ]
}
```

### Update User Profile
```
PUT /api/v1/users/{userId}

Headers:
  Authorization: Bearer <token>

Request:
{
  "firstName": "John",
  "lastName": "Smith"
}

Response: 200 OK
{
  "id": "uuid",
  "email": "user@example.com",
  "firstName": "John",
  "lastName": "Smith",
  "updatedAt": "2024-01-15T11:00:00Z"
}
```

## Product Service APIs

### Get Products (Browse/Search)
```
GET /api/v1/products?search={query}&category={categoryId}&minPrice={min}&maxPrice={max}&page={page}&limit={limit}&sort={field}&order={asc|desc}

Query Parameters:
  - search: string (optional) - Search term
  - category: UUID (optional) - Filter by category
  - minPrice: decimal (optional)
  - maxPrice: decimal (optional)
  - page: integer (default: 1)
  - limit: integer (default: 20, max: 100)
  - sort: string (default: "createdAt") - Sort field
  - order: string (default: "desc") - Sort order

Response: 200 OK
{
  "products": [
    {
      "id": "uuid",
      "name": "Handmade Ceramic Mug",
      "description": "Beautiful handcrafted ceramic mug",
      "price": 29.99,
      "stockQuantity": 15,
      "images": ["url1", "url2"],
      "category": {
        "id": "uuid",
        "name": "Ceramics"
      },
      "seller": {
        "id": "uuid",
        "shopName": "Artisan Pottery",
        "rating": 4.8
      },
      "rating": 4.5,
      "reviewCount": 23
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8
  }
}
```

### Get Product Details
```
GET /api/v1/products/{productId}

Response: 200 OK
{
  "id": "uuid",
  "name": "Handmade Ceramic Mug",
  "description": "Beautiful handcrafted ceramic mug with unique glaze pattern",
  "price": 29.99,
  "stockQuantity": 15,
  "images": ["url1", "url2", "url3"],
  "category": {
    "id": "uuid",
    "name": "Ceramics",
    "parent": "Home & Kitchen"
  },
  "seller": {
    "id": "uuid",
    "shopName": "Artisan Pottery",
    "description": "Making pottery since 2010",
    "rating": 4.8,
    "verified": true
  },
  "rating": 4.5,
  "reviewCount": 23,
  "reviews": [
    {
      "id": "uuid",
      "rating": 5,
      "comment": "Love this mug!",
      "userName": "Jane D.",
      "createdAt": "2024-01-10T08:30:00Z"
    }
  ],
  "status": "ACTIVE",
  "createdAt": "2023-12-01T10:00:00Z"
}
```

### Create Product (Seller Only)
```
POST /api/v1/products

Headers:
  Authorization: Bearer <token>

Request:
{
  "name": "Handmade Ceramic Mug",
  "description": "Beautiful handcrafted ceramic mug",
  "price": 29.99,
  "stockQuantity": 15,
  "categoryId": "uuid",
  "images": ["url1", "url2"],
  "status": "ACTIVE"
}

Response: 201 Created
{
  "id": "uuid",
  "name": "Handmade Ceramic Mug",
  "description": "Beautiful handcrafted ceramic mug",
  "price": 29.99,
  "stockQuantity": 15,
  "categoryId": "uuid",
  "sellerId": "uuid",
  "images": ["url1", "url2"],
  "status": "ACTIVE",
  "createdAt": "2024-01-15T10:00:00Z"
}
```

### Update Product (Seller Only)
```
PUT /api/v1/products/{productId}

Headers:
  Authorization: Bearer <token>

Request:
{
  "price": 34.99,
  "stockQuantity": 20,
  "status": "ACTIVE"
}

Response: 200 OK
{
  "id": "uuid",
  "name": "Handmade Ceramic Mug",
  "price": 34.99,
  "stockQuantity": 20,
  "status": "ACTIVE",
  "updatedAt": "2024-01-15T11:00:00Z"
}
```

### Delete Product (Seller Only)
```
DELETE /api/v1/products/{productId}

Headers:
  Authorization: Bearer <token>

Response: 204 No Content
```

### Get Categories
```
GET /api/v1/categories

Response: 200 OK
{
  "categories": [
    {
      "id": "uuid",
      "name": "Home & Kitchen",
      "description": "Handmade items for home",
      "children": [
        {
          "id": "uuid",
          "name": "Ceramics",
          "description": "Pottery and ceramic items"
        }
      ]
    }
  ]
}
```

## Cart Service APIs

### Get Cart
```
GET /api/v1/cart

Headers:
  Authorization: Bearer <token>

Response: 200 OK
{
  "id": "uuid",
  "items": [
    {
      "id": "uuid",
      "product": {
        "id": "uuid",
        "name": "Handmade Ceramic Mug",
        "price": 29.99,
        "images": ["url1"],
        "stockQuantity": 15
      },
      "quantity": 2,
      "priceSnapshot": 29.99,
      "subtotal": 59.98
    }
  ],
  "subtotal": 59.98,
  "tax": 4.80,
  "total": 64.78
}
```

### Add Item to Cart
```
POST /api/v1/cart/items

Headers:
  Authorization: Bearer <token>

Request:
{
  "productId": "uuid",
  "quantity": 2
}

Response: 201 Created
{
  "id": "uuid",
  "cartId": "uuid",
  "productId": "uuid",
  "quantity": 2,
  "priceSnapshot": 29.99,
  "addedAt": "2024-01-15T10:30:00Z"
}
```

### Update Cart Item
```
PUT /api/v1/cart/items/{itemId}

Headers:
  Authorization: Bearer <token>

Request:
{
  "quantity": 3
}

Response: 200 OK
{
  "id": "uuid",
  "quantity": 3,
  "subtotal": 89.97
}
```

### Remove Item from Cart
```
DELETE /api/v1/cart/items/{itemId}

Headers:
  Authorization: Bearer <token>

Response: 204 No Content
```

### Clear Cart
```
DELETE /api/v1/cart

Headers:
  Authorization: Bearer <token>

Response: 204 No Content
```

## Order Service APIs

### Create Order (Checkout)
```
POST /api/v1/orders

Headers:
  Authorization: Bearer <token>

Request:
{
  "shippingAddressId": "uuid",
  "paymentMethod": "CREDIT_CARD",
  "paymentDetails": {
    "cardNumber": "4111111111111111",
    "expiryMonth": "12",
    "expiryYear": "2025",
    "cvv": "123",
    "cardHolderName": "John Doe"
  }
}

Response: 201 Created
{
  "id": "uuid",
  "orderNumber": "ORD-2024-00123",
  "userId": "uuid",
  "items": [
    {
      "id": "uuid",
      "product": {
        "id": "uuid",
        "name": "Handmade Ceramic Mug"
      },
      "quantity": 2,
      "price": 29.99,
      "subtotal": 59.98
    }
  ],
  "subtotal": 59.98,
  "tax": 4.80,
  "shippingCost": 5.00,
  "total": 69.78,
  "status": "PENDING",
  "shippingAddress": {
    "addressLine1": "123 Main St",
    "city": "New York",
    "state": "NY",
    "postalCode": "10001"
  },
  "payment": {
    "id": "uuid",
    "status": "COMPLETED",
    "amount": 69.78
  },
  "createdAt": "2024-01-15T10:30:00Z"
}
```

### Get Orders (Customer)
```
GET /api/v1/orders?status={status}&page={page}&limit={limit}

Headers:
  Authorization: Bearer <token>

Query Parameters:
  - status: string (optional) - Filter by status
  - page: integer (default: 1)
  - limit: integer (default: 20)

Response: 200 OK
{
  "orders": [
    {
      "id": "uuid",
      "orderNumber": "ORD-2024-00123",
      "status": "SHIPPED",
      "total": 69.78,
      "itemCount": 2,
      "createdAt": "2024-01-15T10:30:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 5,
    "totalPages": 1
  }
}
```

### Get Order Details
```
GET /api/v1/orders/{orderId}

Headers:
  Authorization: Bearer <token>

Response: 200 OK
{
  "id": "uuid",
  "orderNumber": "ORD-2024-00123",
  "status": "SHIPPED",
  "items": [
    {
      "id": "uuid",
      "product": {
        "id": "uuid",
        "name": "Handmade Ceramic Mug",
        "images": ["url1"]
      },
      "seller": {
        "id": "uuid",
        "shopName": "Artisan Pottery"
      },
      "quantity": 2,
      "price": 29.99,
      "subtotal": 59.98,
      "status": "SHIPPED"
    }
  ],
  "subtotal": 59.98,
  "tax": 4.80,
  "shippingCost": 5.00,
  "total": 69.78,
  "shippingAddress": {
    "addressLine1": "123 Main St",
    "city": "New York",
    "state": "NY",
    "postalCode": "10001"
  },
  "payment": {
    "id": "uuid",
    "status": "COMPLETED",
    "amount": 69.78,
    "processedAt": "2024-01-15T10:30:30Z"
  },
  "tracking": {
    "carrier": "FedEx",
    "trackingNumber": "123456789",
    "status": "IN_TRANSIT",
    "estimatedDelivery": "2024-01-20"
  },
  "createdAt": "2024-01-15T10:30:00Z",
  "updatedAt": "2024-01-16T09:00:00Z"
}
```

### Get Seller Orders
```
GET /api/v1/sellers/{sellerId}/orders?status={status}&page={page}&limit={limit}

Headers:
  Authorization: Bearer <token>

Response: 200 OK
{
  "orders": [
    {
      "orderId": "uuid",
      "orderNumber": "ORD-2024-00123",
      "orderItemId": "uuid",
      "product": {
        "id": "uuid",
        "name": "Handmade Ceramic Mug"
      },
      "quantity": 2,
      "subtotal": 59.98,
      "status": "PENDING",
      "customer": {
        "name": "John Doe",
        "shippingAddress": {
          "city": "New York",
          "state": "NY"
        }
      },
      "createdAt": "2024-01-15T10:30:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 15,
    "totalPages": 1
  }
}
```

### Update Order Status (Seller)
```
PUT /api/v1/orders/{orderId}/status

Headers:
  Authorization: Bearer <token>

Request:
{
  "status": "SHIPPED",
  "tracking": {
    "carrier": "FedEx",
    "trackingNumber": "123456789",
    "estimatedDelivery": "2024-01-20"
  }
}

Response: 200 OK
{
  "id": "uuid",
  "orderNumber": "ORD-2024-00123",
  "status": "SHIPPED",
  "tracking": {
    "carrier": "FedEx",
    "trackingNumber": "123456789",
    "status": "IN_TRANSIT",
    "estimatedDelivery": "2024-01-20"
  },
  "updatedAt": "2024-01-16T09:00:00Z"
}
```

## Payment Service APIs

### Process Payment
```
POST /api/v1/payments/process

Headers:
  Authorization: Bearer <token>

Request:
{
  "orderId": "uuid",
  "amount": 69.78,
  "paymentMethod": "CREDIT_CARD",
  "paymentDetails": {
    "cardNumber": "4111111111111111",
    "expiryMonth": "12",
    "expiryYear": "2025",
    "cvv": "123",
    "cardHolderName": "John Doe"
  }
}

Response: 200 OK
{
  "id": "uuid",
  "orderId": "uuid",
  "status": "COMPLETED",
  "amount": 69.78,
  "transactionId": "txn_abc123",
  "processedAt": "2024-01-15T10:30:30Z"
}

Error Response: 400 Bad Request
{
  "error": "PAYMENT_FAILED",
  "message": "Card declined",
  "code": "card_declined"
}
```

### Get Payment Details
```
GET /api/v1/payments/{paymentId}

Headers:
  Authorization: Bearer <token>

Response: 200 OK
{
  "id": "uuid",
  "orderId": "uuid",
  "status": "COMPLETED",
  "amount": 69.78,
  "paymentMethod": "CREDIT_CARD",
  "transactionId": "txn_abc123",
  "processedAt": "2024-01-15T10:30:30Z"
}
```

### Request Refund
```
POST /api/v1/payments/{paymentId}/refund

Headers:
  Authorization: Bearer <token>

Request:
{
  "amount": 69.78,
  "reason": "Customer requested refund"
}

Response: 200 OK
{
  "id": "uuid",
  "paymentId": "uuid",
  "status": "REFUNDED",
  "amount": 69.78,
  "refundedAt": "2024-01-17T14:00:00Z"
}
```

## Analytics Service APIs

### Get Sales Analytics (Seller)
```
GET /api/v1/analytics/sales?startDate={date}&endDate={date}

Headers:
  Authorization: Bearer <token>

Query Parameters:
  - startDate: date (ISO 8601 format)
  - endDate: date (ISO 8601 format)

Response: 200 OK
{
  "period": {
    "startDate": "2024-01-01",
    "endDate": "2024-01-31"
  },
  "metrics": {
    "totalRevenue": 5432.10,
    "totalOrders": 87,
    "averageOrderValue": 62.44,
    "totalItemsSold": 156
  },
  "topProducts": [
    {
      "productId": "uuid",
      "name": "Handmade Ceramic Mug",
      "unitsSold": 45,
      "revenue": 1349.55
    }
  ],
  "revenueByDay": [
    {
      "date": "2024-01-15",
      "revenue": 245.50,
      "orders": 4
    }
  ]
}
```

## Error Responses

### Standard Error Format
```
{
  "error": "ERROR_CODE",
  "message": "Human-readable error message",
  "details": {
    "field": "Specific field error"
  }
}
```

### Common HTTP Status Codes
- 200 OK - Success
- 201 Created - Resource created
- 204 No Content - Success with no response body
- 400 Bad Request - Invalid input
- 401 Unauthorized - Authentication required
- 403 Forbidden - Insufficient permissions
- 404 Not Found - Resource not found
- 409 Conflict - Resource conflict (e.g., duplicate email)
- 500 Internal Server Error - Server error
