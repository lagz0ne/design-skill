# Design Catalog: Artisan E-commerce Platform

## Overview

An e-commerce platform connecting artisans with customers for selling handmade products. The platform provides a trust layer through escrow-based payments, ensuring safe transactions between makers and buyers while giving artisans full control over their inventory and fulfillment.

## Requirements

See [requirements.md](./requirements.md) for detailed requirements, actors, constraints, and success criteria.

**Key highlights:**
- Target scale: 1K-10K users, 100-1000 products
- Platform escrow payment model
- Seller-managed fulfillment
- Phased rollout: 6-12 months

## Big Picture EventStorming

The following diagram shows the high-level business process with key events, commands, and actors:

```mermaid
flowchart LR
    %% EventStorming Color Conventions
    classDef event fill:#ff9800,stroke:#e65100,color:#000
    classDef command fill:#2196f3,stroke:#0d47a1,color:#fff
    classDef actor fill:#ffeb3b,stroke:#f57f17,color:#000
    classDef system fill:#9c27b0,stroke:#4a148c,color:#fff
    classDef aggregate fill:#4caf50,stroke:#1b5e20,color:#fff
    classDef hotspot fill:#f44336,stroke:#b71c1c,color:#fff

    %% Actors
    Customer[Customer]:::actor
    Seller[Artisan/Seller]:::actor
    Admin[Platform Admin]:::actor

    %% Seller Journey - Product Management
    CmdListProduct[List Product]:::command
    EvtProductListed[Product Listed]:::event
    AggProduct[Product]:::aggregate

    CmdUpdateInventory[Update Inventory]:::command
    EvtInventoryUpdated[Inventory Updated]:::event

    %% Customer Journey - Discovery & Purchase
    CmdBrowseProducts[Browse Products]:::command
    EvtProductViewed[Product Viewed]:::event

    CmdAddToCart[Add to Cart]:::command
    EvtItemAddedToCart[Item Added to Cart]:::event
    AggCart[Shopping Cart]:::aggregate

    CmdCheckout[Checkout]:::command
    EvtOrderPlaced[Order Placed]:::event
    AggOrder[Order]:::aggregate

    %% Payment Flow
    EvtPaymentInitiated[Payment Initiated]:::event
    SysPaymentGateway[Payment Gateway]:::system
    EvtPaymentProcessed[Payment Processed]:::event
    EvtFundsEscrowed[Funds Escrowed]:::event
    AggEscrow[Escrow Account]:::aggregate

    %% Fulfillment Flow
    EvtSellerNotified[Seller Notified]:::event
    CmdShipOrder[Ship Order]:::command
    EvtOrderShipped[Order Shipped]:::event
    SysShippingCarrier[Shipping Carrier API]:::system
    EvtTrackingNumberAdded[Tracking Number Added]:::event

    SysEmailService[Email Service]:::system

    %% Delivery & Payment Release
    EvtOrderDelivered[Order Delivered]:::event
    CmdConfirmDelivery[Confirm Delivery]:::command
    EvtDeliveryConfirmed[Delivery Confirmed]:::event
    EvtFundsReleased[Funds Released to Seller]:::event

    %% Analytics
    SysAnalytics[Analytics System]:::system

    %% Seller Dashboard
    CmdViewSales[View Sales Dashboard]:::command
    EvtSalesDashboardViewed[Sales Data Retrieved]:::event

    %% Hotspots
    Hot1[? Escrow release timing rules]:::hotspot
    Hot2[? Dispute resolution process]:::hotspot
    Hot3[? Return/refund policy]:::hotspot
    Hot4[? Product categorization]:::hotspot
    Hot5[? Auto-delivery confirmation]:::hotspot

    %% Flow - Seller lists product
    Seller --> CmdListProduct
    CmdListProduct --> EvtProductListed
    EvtProductListed --> AggProduct
    EvtProductListed -.question.- Hot4

    Seller --> CmdUpdateInventory
    CmdUpdateInventory --> EvtInventoryUpdated
    EvtInventoryUpdated --> AggProduct

    %% Flow - Customer browses and purchases
    Customer --> CmdBrowseProducts
    CmdBrowseProducts --> EvtProductViewed
    EvtProductViewed --> AggProduct
    EvtProductViewed --> SysAnalytics

    Customer --> CmdAddToCart
    CmdAddToCart --> EvtItemAddedToCart
    EvtItemAddedToCart --> AggCart

    Customer --> CmdCheckout
    CmdCheckout --> EvtOrderPlaced
    EvtOrderPlaced --> AggOrder

    %% Flow - Payment processing
    EvtOrderPlaced --> EvtPaymentInitiated
    EvtPaymentInitiated --> SysPaymentGateway
    SysPaymentGateway --> EvtPaymentProcessed
    EvtPaymentProcessed --> EvtFundsEscrowed
    EvtFundsEscrowed --> AggEscrow
    EvtFundsEscrowed -.question.- Hot1

    %% Flow - Fulfillment
    EvtPaymentProcessed --> EvtSellerNotified
    EvtSellerNotified --> SysEmailService

    Seller --> CmdShipOrder
    CmdShipOrder --> EvtOrderShipped
    EvtOrderShipped --> AggOrder
    EvtOrderShipped --> SysShippingCarrier
    SysShippingCarrier --> EvtTrackingNumberAdded
    EvtTrackingNumberAdded --> AggOrder
    EvtTrackingNumberAdded --> SysEmailService

    %% Flow - Delivery and payment release
    SysShippingCarrier --> EvtOrderDelivered
    EvtOrderDelivered -.question.- Hot5

    Customer --> CmdConfirmDelivery
    CmdConfirmDelivery --> EvtDeliveryConfirmed
    EvtDeliveryConfirmed --> EvtFundsReleased
    EvtDeliveryConfirmed -.question.- Hot3
    EvtFundsReleased --> AggEscrow
    EvtFundsReleased --> SysPaymentGateway

    EvtDeliveryConfirmed -.question.- Hot2

    %% Flow - Seller dashboard
    Seller --> CmdViewSales
    CmdViewSales --> EvtSalesDashboardViewed
    EvtSalesDashboardViewed --> AggOrder
    EvtSalesDashboardViewed --> AggProduct
```

[View source](./big-picture.mmd)

## Process Models

Detailed process EventStorming for critical workflows:

### Checkout & Payment Process

Customer checkout flow with cart validation, payment authorization, and escrow creation:

```mermaid
flowchart LR
    %% EventStorming Process Level - Checkout & Payment with Escrow
    classDef event fill:#ff9800,stroke:#e65100,color:#000
    classDef command fill:#2196f3,stroke:#0d47a1,color:#fff
    classDef actor fill:#ffeb3b,stroke:#f57f17,color:#000
    classDef policy fill:#ba68c8,stroke:#6a1b9a,color:#fff
    classDef aggregate fill:#4caf50,stroke:#1b5e20,color:#fff
    classDef system fill:#9c27b0,stroke:#4a148c,color:#fff
    classDef hotspot fill:#f44336,stroke:#b71c1c,color:#fff

    %% Actors & Systems
    Customer[Customer]:::actor
    SysPayment[Payment Gateway]:::system
    SysEmail[Email Service]:::system

    %% Commands
    CmdCheckout[Initiate Checkout]:::command
    CmdSubmitPayment[Submit Payment Info]:::command

    %% Events
    EvtCheckoutStarted[Checkout Started]:::event
    EvtCartValidated[Cart Validated]:::event
    EvtShippingInfoProvided[Shipping Info Provided]:::event
    EvtPaymentInfoProvided[Payment Info Provided]:::event
    EvtOrderCreated[Order Created]:::event
    EvtPaymentAuthorized[Payment Authorized]:::event
    EvtFundsEscrowed[Funds Moved to Escrow]:::event
    EvtSellerNotified[Seller Notified]:::event
    EvtCustomerNotified[Customer Notified]:::event

    %% Aggregates
    AggCart[Shopping Cart]:::aggregate
    AggOrder[Order]:::aggregate
    AggEscrow[Escrow Account]:::aggregate

    %% Policies
    PolicyCartValid{Cart Items<br/>Available?}:::policy
    PolicyPaymentValid{Payment<br/>Authorized?}:::policy

    %% Error Events
    EvtCheckoutFailed[Checkout Failed]:::event
    EvtPaymentFailed[Payment Failed]:::event

    %% Hotspots
    Hot1[? Partial inventory scenarios]:::hotspot
    Hot2[? Payment retry logic]:::hotspot
    Hot3[? Escrow hold duration]:::hotspot

    %% Flow
    Customer --> CmdCheckout
    CmdCheckout --> EvtCheckoutStarted
    EvtCheckoutStarted --> AggCart

    EvtCheckoutStarted --> PolicyCartValid
    PolicyCartValid -->|inventory available| EvtCartValidated
    PolicyCartValid -->|items unavailable| EvtCheckoutFailed
    EvtCartValidated -.question.- Hot1

    EvtCartValidated --> EvtShippingInfoProvided
    EvtShippingInfoProvided -.data.- Note1[shipping_address,<br/>contact_info set]

    Customer --> CmdSubmitPayment
    CmdSubmitPayment --> EvtPaymentInfoProvided
    EvtPaymentInfoProvided --> SysPayment

    SysPayment --> PolicyPaymentValid
    PolicyPaymentValid -->|authorized| EvtPaymentAuthorized
    PolicyPaymentValid -->|declined| EvtPaymentFailed
    EvtPaymentFailed -.question.- Hot2

    EvtPaymentAuthorized --> EvtOrderCreated
    EvtOrderCreated --> AggOrder
    EvtOrderCreated -.data.- Note2[order_id,<br/>total_amount,<br/>status=PENDING]

    EvtOrderCreated --> EvtFundsEscrowed
    EvtFundsEscrowed --> AggEscrow
    EvtFundsEscrowed -.data.- Note3[escrow_amount,<br/>hold_start_time,<br/>seller_id]
    EvtFundsEscrowed -.question.- Hot3

    EvtFundsEscrowed --> EvtSellerNotified
    EvtSellerNotified --> SysEmail

    EvtFundsEscrowed --> EvtCustomerNotified
    EvtCustomerNotified --> SysEmail
```

[View source](./processes/process-checkout-payment.mmd)

### Order Fulfillment Process

Seller shipping workflow, delivery tracking, confirmation, and payment release:

```mermaid
flowchart LR
    %% EventStorming Process Level - Order Fulfillment & Delivery
    classDef event fill:#ff9800,stroke:#e65100,color:#000
    classDef command fill:#2196f3,stroke:#0d47a1,color:#fff
    classDef actor fill:#ffeb3b,stroke:#f57f17,color:#000
    classDef policy fill:#ba68c8,stroke:#6a1b9a,color:#fff
    classDef aggregate fill:#4caf50,stroke:#1b5e20,color:#fff
    classDef system fill:#9c27b0,stroke:#4a148c,color:#fff
    classDef hotspot fill:#f44336,stroke:#b71c1c,color:#fff

    %% Actors & Systems
    Seller[Artisan/Seller]:::actor
    Customer[Customer]:::actor
    SysShipping[Shipping Carrier API]:::system
    SysEmail[Email Service]:::system
    SysPayment[Payment Gateway]:::system

    %% Commands
    CmdShipOrder[Mark as Shipped]:::command
    CmdConfirmDelivery[Confirm Delivery]:::command
    CmdDisputeOrder[Raise Dispute]:::command

    %% Events
    EvtOrderReceived[Order Received by Seller]:::event
    EvtOrderPrepared[Order Prepared]:::event
    EvtOrderShipped[Order Shipped]:::event
    EvtTrackingAdded[Tracking Number Added]:::event
    EvtCustomerNotified[Customer Notified]:::event
    EvtInTransit[Package In Transit]:::event
    EvtDeliveryAttempted[Delivery Attempted]:::event
    EvtOrderDelivered[Order Delivered]:::event
    EvtDeliveryConfirmed[Delivery Confirmed]:::event
    EvtFundsReleased[Funds Released to Seller]:::event
    EvtDisputeRaised[Dispute Raised]:::event

    %% Aggregates
    AggOrder[Order]:::aggregate
    AggEscrow[Escrow Account]:::aggregate

    %% Policies
    PolicyAutoConfirm{Auto-confirm<br/>after N days?}:::policy
    PolicyDeliveryProof{Delivery<br/>Confirmed?}:::policy

    %% Hotspots
    Hot1[? Auto-confirm duration]:::hotspot
    Hot2[? Dispute resolution workflow]:::hotspot
    Hot3[? Partial delivery handling]:::hotspot

    %% Flow - Seller prepares and ships
    EvtOrderReceived --> EvtOrderPrepared
    EvtOrderPrepared -.data.- Note1[status=PREPARING]

    Seller --> CmdShipOrder
    CmdShipOrder --> EvtOrderShipped
    EvtOrderShipped --> AggOrder
    EvtOrderShipped -.data.- Note2[status=SHIPPED,<br/>shipped_at timestamp]

    EvtOrderShipped --> SysShipping
    SysShipping --> EvtTrackingAdded
    EvtTrackingAdded --> AggOrder
    EvtTrackingAdded -.data.- Note3[tracking_number,<br/>carrier_name]

    EvtTrackingAdded --> EvtCustomerNotified
    EvtCustomerNotified --> SysEmail

    %% Flow - Delivery tracking
    SysShipping --> EvtInTransit
    EvtInTransit --> AggOrder

    SysShipping --> EvtDeliveryAttempted
    EvtDeliveryAttempted --> AggOrder

    SysShipping --> EvtOrderDelivered
    EvtOrderDelivered --> AggOrder
    EvtOrderDelivered -.data.- Note4[status=DELIVERED,<br/>delivered_at timestamp]

    %% Flow - Confirmation and payment release
    EvtOrderDelivered --> PolicyAutoConfirm
    PolicyAutoConfirm -->|auto after X days| EvtDeliveryConfirmed
    PolicyAutoConfirm -.question.- Hot1

    Customer --> CmdConfirmDelivery
    CmdConfirmDelivery --> PolicyDeliveryProof
    PolicyDeliveryProof -->|confirmed| EvtDeliveryConfirmed

    EvtDeliveryConfirmed --> AggOrder
    EvtDeliveryConfirmed -.data.- Note5[status=COMPLETED,<br/>confirmed_at]

    EvtDeliveryConfirmed --> EvtFundsReleased
    EvtFundsReleased --> AggEscrow
    EvtFundsReleased --> SysPayment
    EvtFundsReleased -.data.- Note6[escrow released,<br/>payout to seller]

    %% Flow - Dispute handling
    Customer --> CmdDisputeOrder
    CmdDisputeOrder --> EvtDisputeRaised
    EvtDisputeRaised --> AggOrder
    EvtDisputeRaised -.question.- Hot2
    EvtDisputeRaised -.question.- Hot3
```

[View source](./processes/process-order-fulfillment.mmd)

### Product Listing Process

Artisan product creation, publishing, inventory management, and customer browsing:

```mermaid
flowchart LR
    %% EventStorming Process Level - Product Listing & Management
    classDef event fill:#ff9800,stroke:#e65100,color:#000
    classDef command fill:#2196f3,stroke:#0d47a1,color:#fff
    classDef actor fill:#ffeb3b,stroke:#f57f17,color:#000
    classDef policy fill:#ba68c8,stroke:#6a1b9a,color:#fff
    classDef aggregate fill:#4caf50,stroke:#1b5e20,color:#fff
    classDef hotspot fill:#f44336,stroke:#b71c1c,color:#fff

    %% Actors
    Seller[Artisan/Seller]:::actor
    Customer[Customer]:::actor

    %% Commands
    CmdCreateProduct[Create Product]:::command
    CmdUploadImages[Upload Images]:::command
    CmdPublishProduct[Publish Product]:::command
    CmdUpdateInventory[Update Inventory]:::command
    CmdUpdatePrice[Update Price]:::command
    CmdBrowse[Browse Products]:::command

    %% Events
    EvtProductDrafted[Product Drafted]:::event
    EvtImagesUploaded[Images Uploaded]:::event
    EvtProductValidated[Product Validated]:::event
    EvtProductPublished[Product Published]:::event
    EvtInventoryUpdated[Inventory Updated]:::event
    EvtPriceUpdated[Price Updated]:::event
    EvtProductViewed[Product Viewed]:::event
    EvtProductSearched[Products Searched]:::event
    EvtOutOfStock[Product Out of Stock]:::event

    %% Aggregates
    AggProduct[Product]:::aggregate
    AggInventory[Inventory]:::aggregate

    %% Policies
    PolicyValidProduct{Product Info<br/>Complete?}:::policy
    PolicyInventoryCheck{Inventory<br/>Available?}:::policy

    %% Hotspots
    Hot1[? Product categorization taxonomy]:::hotspot
    Hot2[? Image quality requirements]:::hotspot
    Hot3[? Inventory reservation timing]:::hotspot
    Hot4[? Search/filter capabilities]:::hotspot

    %% Flow - Create and publish product
    Seller --> CmdCreateProduct
    CmdCreateProduct --> EvtProductDrafted
    EvtProductDrafted --> AggProduct
    EvtProductDrafted -.data.- Note1[title, description,<br/>category, price,<br/>status=DRAFT]
    EvtProductDrafted -.question.- Hot1

    Seller --> CmdUploadImages
    CmdUploadImages --> EvtImagesUploaded
    EvtImagesUploaded --> AggProduct
    EvtImagesUploaded -.data.- Note2[image_urls[],<br/>primary_image]
    EvtImagesUploaded -.question.- Hot2

    Seller --> CmdPublishProduct
    CmdPublishProduct --> PolicyValidProduct
    PolicyValidProduct -->|complete| EvtProductValidated
    PolicyValidProduct -->|incomplete| EvtProductDrafted

    EvtProductValidated --> EvtProductPublished
    EvtProductPublished --> AggProduct
    EvtProductPublished -.data.- Note3[status=PUBLISHED,<br/>published_at]

    %% Flow - Inventory management
    Seller --> CmdUpdateInventory
    CmdUpdateInventory --> EvtInventoryUpdated
    EvtInventoryUpdated --> AggInventory
    EvtInventoryUpdated -.data.- Note4[quantity_available,<br/>updated_at]

    EvtInventoryUpdated --> PolicyInventoryCheck
    PolicyInventoryCheck -->|quantity = 0| EvtOutOfStock
    EvtOutOfStock --> AggProduct
    EvtOutOfStock -.data.- Note5[status=OUT_OF_STOCK]
    EvtInventoryUpdated -.question.- Hot3

    %% Flow - Price updates
    Seller --> CmdUpdatePrice
    CmdUpdatePrice --> EvtPriceUpdated
    EvtPriceUpdated --> AggProduct
    EvtPriceUpdated -.data.- Note6[price, price_updated_at]

    %% Flow - Customer browsing
    Customer --> CmdBrowse
    CmdBrowse --> EvtProductSearched
    EvtProductSearched -.question.- Hot4

    EvtProductSearched --> EvtProductViewed
    EvtProductViewed --> AggProduct
```

[View source](./processes/process-product-listing.mmd)

## Data Model

Entity-relationship diagram showing core data entities and their relationships:

```mermaid
erDiagram
    User ||--o{ Product : "seller creates"
    User ||--o{ Order : "customer places"
    User ||--|| ShoppingCart : "has"
    User ||--o{ EscrowAccount : "seller receives from"

    Product ||--o{ CartItem : "appears in"
    Product ||--o{ OrderItem : "ordered as"
    Product ||--|| Inventory : "has"

    ShoppingCart ||--o{ CartItem : "contains"

    Order ||--o{ OrderItem : "contains"
    Order ||--|| Payment : "paid via"
    Order ||--|| Shipment : "fulfilled as"
    Order ||--|| EscrowAccount : "held in"

    Payment ||--|| EscrowAccount : "creates"

    Shipment ||--o{ TrackingEvent : "tracked by"

    User {
        uuid id PK
        string email "unique"
        string name
        string role "CUSTOMER, SELLER, ADMIN"
        string phone
        timestamp created_at
        timestamp last_login
    }

    Product {
        uuid id PK
        uuid seller_id FK "references User"
        string title
        text description
        decimal price
        string category
        string[] image_urls
        string primary_image
        string status "DRAFT, PUBLISHED, OUT_OF_STOCK, ARCHIVED"
        timestamp published_at
        timestamp created_at
        timestamp updated_at
    }

    Inventory {
        uuid id PK
        uuid product_id FK "references Product, unique"
        int quantity_available
        int quantity_reserved
        timestamp updated_at
    }

    ShoppingCart {
        uuid id PK
        uuid user_id FK "references User, unique"
        timestamp created_at
        timestamp updated_at
    }

    CartItem {
        uuid id PK
        uuid cart_id FK "references ShoppingCart"
        uuid product_id FK "references Product"
        int quantity
        decimal price_snapshot
        timestamp added_at
    }

    Order {
        uuid id PK
        uuid customer_id FK "references User"
        uuid seller_id FK "references User"
        string order_number "unique"
        decimal total_amount
        string status "PENDING, PAID, SHIPPED, DELIVERED, COMPLETED, CANCELLED, DISPUTED"
        text shipping_address
        string contact_info
        timestamp created_at
        timestamp updated_at
    }

    OrderItem {
        uuid id PK
        uuid order_id FK "references Order"
        uuid product_id FK "references Product"
        int quantity
        decimal price_snapshot
        string product_title
    }

    Payment {
        uuid id PK
        uuid order_id FK "references Order, unique"
        decimal amount
        string payment_method
        string payment_gateway_id "external reference"
        string status "PENDING, AUTHORIZED, CAPTURED, FAILED, REFUNDED"
        timestamp authorized_at
        timestamp captured_at
    }

    EscrowAccount {
        uuid id PK
        uuid order_id FK "references Order, unique"
        uuid seller_id FK "references User"
        decimal amount
        string status "HELD, RELEASED, REFUNDED"
        timestamp hold_start_time
        timestamp release_time
        string release_reason
    }

    Shipment {
        uuid id PK
        uuid order_id FK "references Order, unique"
        string tracking_number
        string carrier_name
        string status "PREPARING, SHIPPED, IN_TRANSIT, OUT_FOR_DELIVERY, DELIVERED, FAILED"
        timestamp shipped_at
        timestamp delivered_at
    }

    TrackingEvent {
        uuid id PK
        uuid shipment_id FK "references Shipment"
        string event_type "STATUS_UPDATE, LOCATION_UPDATE"
        string status
        string location
        text description
        timestamp occurred_at
    }
```

[View source](./data/erd.mmd)

## State Models

State charts for entities with complex lifecycles:

### Order Lifecycle

Order states from PENDING through COMPLETED/CANCELLED, including payment, shipping, delivery confirmation, and escrow release:

```mermaid
stateDiagram-v2
    [*] --> PENDING: Order Placed

    PENDING --> PAID: Payment Authorized & Escrowed
    PENDING --> CANCELLED: Payment Failed

    PAID --> SHIPPED: Seller Ships Order
    PAID --> CANCELLED: Seller Cancels

    SHIPPED --> DELIVERED: Carrier Reports Delivery
    SHIPPED --> CANCELLED: Delivery Failed

    DELIVERED --> COMPLETED: Delivery Confirmed (auto or manual)
    DELIVERED --> DISPUTED: Customer Raises Dispute

    DISPUTED --> COMPLETED: Dispute Resolved - Favor Seller
    DISPUTED --> CANCELLED: Dispute Resolved - Favor Customer (Refund)

    COMPLETED --> [*]: Funds Released to Seller
    CANCELLED --> [*]: Funds Refunded to Customer

    note right of PENDING
        Data: order_id, total_amount
        Escrow: Not yet created
    end note

    note right of PAID
        Data: payment_id, escrow_id
        Escrow: Funds held
        Trigger: Seller notification sent
    end note

    note right of SHIPPED
        Data: tracking_number, carrier
        Escrow: Still held
        Trigger: Customer notification sent
    end note

    note right of DELIVERED
        Data: delivered_at timestamp
        Escrow: Still held
        Auto-confirm: Timer starts
    end note

    note right of COMPLETED
        Data: confirmed_at timestamp
        Escrow: Released to seller
        Final state
    end note

    note right of DISPUTED
        Data: dispute_reason, dispute_date
        Escrow: Frozen until resolution
        Manual: Admin intervention
    end note

    note right of CANCELLED
        Data: cancelled_at, cancel_reason
        Escrow: Refunded to customer
        Final state
    end note
```

[View source](./data/state-order.mmd)

### Product Lifecycle

Product states from DRAFT through PUBLISHED/ARCHIVED, including inventory management and visibility:

```mermaid
stateDiagram-v2
    [*] --> DRAFT: Product Created

    DRAFT --> DRAFT: Edit Details / Upload Images
    DRAFT --> PUBLISHED: Publish (validation passed)
    DRAFT --> ARCHIVED: Delete Draft

    PUBLISHED --> OUT_OF_STOCK: Inventory = 0
    PUBLISHED --> PUBLISHED: Update Price / Images
    PUBLISHED --> ARCHIVED: Seller Archives

    OUT_OF_STOCK --> PUBLISHED: Inventory Replenished
    OUT_OF_STOCK --> ARCHIVED: Seller Archives

    ARCHIVED --> [*]

    note right of DRAFT
        Data: title, description, price, images
        Visibility: Private to seller
        Validation: Required fields checked
    end note

    note right of PUBLISHED
        Data: published_at timestamp
        Visibility: Public to all customers
        Searchable: Yes
        Purchasable: Yes (if inventory > 0)
    end note

    note right of OUT_OF_STOCK
        Data: inventory.quantity_available = 0
        Visibility: Public but not purchasable
        Display: "Out of Stock" label
    end note

    note right of ARCHIVED
        Data: archived_at timestamp
        Visibility: Hidden from public
        Final state: Can be restored by creating new
    end note
```

[View source](./data/state-product.mmd)

## Critical Flows

Sequence diagrams for important interaction flows:

### Checkout & Escrow Flow

Complete checkout process with cart validation, payment authorization, escrow creation, and notifications:

```mermaid
sequenceDiagram
    actor Customer
    participant Frontend
    participant OrderService
    participant PaymentService
    participant PaymentGateway
    participant EscrowService
    participant EmailService
    actor Seller

    Customer->>Frontend: Click Checkout
    Frontend->>OrderService: Validate Cart

    alt Cart Valid
        OrderService-->>Frontend: Cart OK
        Frontend->>Customer: Request Shipping Info
        Customer->>Frontend: Provide Address

        Frontend->>Customer: Request Payment Info
        Customer->>Frontend: Provide Card Details

        Frontend->>PaymentService: Authorize Payment
        PaymentService->>PaymentGateway: Process Authorization

        alt Payment Authorized
            PaymentGateway-->>PaymentService: Authorization Success
            PaymentService->>OrderService: Create Order
            OrderService->>OrderService: Generate Order ID

            OrderService->>EscrowService: Hold Funds
            EscrowService->>PaymentGateway: Capture Payment to Escrow
            PaymentGateway-->>EscrowService: Funds Captured

            EscrowService-->>OrderService: Escrow Created
            OrderService-->>Frontend: Order Placed Successfully

            par Notifications
                OrderService->>EmailService: Send Customer Confirmation
                EmailService->>Customer: Email: Order Confirmed
            and
                OrderService->>EmailService: Send Seller Notification
                EmailService->>Seller: Email: New Order
            end

            Frontend->>Customer: Show Order Confirmation

        else Payment Declined
            PaymentGateway-->>PaymentService: Authorization Failed
            PaymentService-->>Frontend: Payment Error
            Frontend->>Customer: Show Error + Retry Option
        end

    else Cart Invalid (items unavailable)
        OrderService-->>Frontend: Cart Validation Failed
        Frontend->>Customer: Show Out of Stock Message
    end
```

[View source](./flows/sequence-checkout-escrow.mmd)

### Fulfillment & Release Flow

Order fulfillment from shipping through delivery confirmation and escrow fund release:

```mermaid
sequenceDiagram
    actor Seller
    participant SellerDashboard
    participant OrderService
    participant ShippingService
    participant ShippingCarrier
    participant EmailService
    participant EscrowService
    participant PaymentGateway
    actor Customer

    Seller->>SellerDashboard: View Pending Orders
    SellerDashboard->>OrderService: Get Orders (status=PAID)
    OrderService-->>SellerDashboard: Return Order List

    Seller->>SellerDashboard: Mark Order as Shipped
    SellerDashboard->>Seller: Request Tracking Number
    Seller->>SellerDashboard: Provide Tracking Info

    SellerDashboard->>OrderService: Update Order (SHIPPED)
    OrderService->>ShippingService: Add Tracking Number
    ShippingService->>ShippingCarrier: Register Tracking
    ShippingCarrier-->>ShippingService: Tracking Registered

    ShippingService-->>OrderService: Tracking Added
    OrderService->>EmailService: Send Customer Notification
    EmailService->>Customer: Email: Order Shipped + Tracking

    Note over ShippingService,ShippingCarrier: Carrier processes delivery

    loop Tracking Updates
        ShippingCarrier->>ShippingService: Status Update (IN_TRANSIT, etc)
        ShippingService->>OrderService: Update Shipment Status
    end

    ShippingCarrier->>ShippingService: Status: DELIVERED
    ShippingService->>OrderService: Mark Delivered
    OrderService->>OrderService: Start Auto-Confirm Timer

    alt Manual Confirmation
        Customer->>OrderService: Confirm Delivery
        OrderService->>OrderService: Set status=COMPLETED

    else Auto-Confirmation After X Days
        Note over OrderService: Timer expires (e.g., 7 days)
        OrderService->>OrderService: Auto-set status=COMPLETED
    end

    OrderService->>EscrowService: Release Funds
    EscrowService->>PaymentGateway: Transfer to Seller Account
    PaymentGateway-->>EscrowService: Transfer Complete

    EscrowService-->>OrderService: Funds Released

    par Final Notifications
        OrderService->>EmailService: Notify Seller (Payment Released)
        EmailService->>Seller: Email: Payment Received
    and
        OrderService->>EmailService: Notify Customer (Order Complete)
        EmailService->>Customer: Email: Order Completed
    end
```

[View source](./flows/sequence-fulfillment-release.mmd)

### Product Browse Flow

Customer product discovery, search, view, and add-to-cart interaction:

```mermaid
sequenceDiagram
    actor Customer
    participant Frontend
    participant SearchService
    participant ProductService
    participant InventoryService
    participant AnalyticsService

    Customer->>Frontend: Visit Homepage
    Frontend->>SearchService: Get Featured Products
    SearchService->>ProductService: Query (status=PUBLISHED, featured=true)
    ProductService->>InventoryService: Check Availability
    InventoryService-->>ProductService: Return Inventory Status
    ProductService-->>SearchService: Return Products + Stock Info
    SearchService-->>Frontend: Return Results
    Frontend->>Customer: Display Featured Products

    Customer->>Frontend: Enter Search Query
    Frontend->>SearchService: Search "handmade jewelry"

    SearchService->>SearchService: Parse Query + Apply Filters
    SearchService->>ProductService: Query Products (category, keywords)
    ProductService->>InventoryService: Batch Check Inventory
    InventoryService-->>ProductService: Return Availability for All

    ProductService-->>SearchService: Return Matching Products
    SearchService-->>Frontend: Return Search Results
    Frontend->>Customer: Display Product Grid

    Frontend->>AnalyticsService: Track Search Event
    AnalyticsService->>AnalyticsService: Log Search Query

    Customer->>Frontend: Click Product
    Frontend->>ProductService: Get Product Details (product_id)
    ProductService->>InventoryService: Get Current Stock
    InventoryService-->>ProductService: quantity_available

    ProductService-->>Frontend: Product Details + Inventory
    Frontend->>Customer: Show Product Page

    Frontend->>AnalyticsService: Track Product View
    AnalyticsService->>AnalyticsService: Log View Event

    alt Product Available
        Customer->>Frontend: Add to Cart
        Frontend->>ProductService: Reserve Inventory?
        Note over ProductService,InventoryService: Hotspot: Inventory reservation timing
        Frontend->>Customer: Added to Cart

    else Product Out of Stock
        Frontend->>Customer: Show "Out of Stock" + Notify Option
    end
```

[View source](./flows/sequence-product-browse.mmd)

## Hotspots & Open Questions

The following areas require decisions or further clarification before implementation:

### Critical Business Logic
1. **Escrow release timing rules** - How long should funds be held? Auto-release after X days or require explicit confirmation?
2. **Auto-delivery confirmation** - Should system auto-confirm delivery after shipping carrier reports "delivered" + X days?
3. **Dispute resolution process** - Workflow when customer claims non-delivery or product issues?
4. **Return/refund policy** - How to handle returns for custom/handmade goods?

### Product Management
5. **Product categorization taxonomy** - What category structure for handmade goods? (Jewelry, Art, Home Decor, etc.)
6. **Search/filter capabilities** - What search features needed? (keyword, category, price range, seller rating, etc.)
7. **Image quality requirements** - Size limits, format requirements, number of images per product?

### Technical Decisions
8. **Inventory reservation timing** - When to reserve inventory: at add-to-cart or checkout?
9. **Partial inventory scenarios** - Handle cases where cart items become partially unavailable during checkout?
10. **Payment retry logic** - Allow retry on payment failure? How many attempts?
11. **Commission/fee structure** - Does platform take a percentage? Fixed fee? How calculated?
12. **Multi-currency support** - International sales supported?

### Seller Onboarding
13. **Seller verification process** - Identity verification, business validation, approval workflow?
14. **Seller dashboard metrics** - What analytics/reports do sellers need?

## Next Steps

### Design Complete
This design catalog provides a comprehensive conceptual model of the artisan e-commerce platform. The next phase depends on your needs:

**Option 1: Implementation Planning**
- Create detailed implementation plan with tasks, file structure, and technical specifications
- Break down into sprints for phased rollout

**Option 2: Resolve Hotspots First**
- Address the 14 open questions above
- Refine business rules and policies
- Update design artifacts with decisions

**Option 3: Technical Architecture**
- Select technology stack (backend framework, database, hosting)
- Design API contracts
- Plan infrastructure and deployment

**Option 4: Prototyping**
- Build clickable prototype to validate UX flows
- Test checkout and seller dashboard concepts
- Gather user feedback

**Recommended:** Resolve critical hotspots (1-4) first, then proceed with implementation planning for Phase 1 MVP (browse, cart, checkout, basic seller dashboard).
