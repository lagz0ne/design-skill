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

1. **[Checkout & Payment Process](./processes/process-checkout-payment.mmd)** - Customer checkout flow with cart validation, payment authorization, and escrow creation
2. **[Order Fulfillment Process](./processes/process-order-fulfillment.mmd)** - Seller shipping workflow, delivery tracking, confirmation, and payment release
3. **[Product Listing Process](./processes/process-product-listing.mmd)** - Artisan product creation, publishing, inventory management, and customer browsing

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

- **[Order Lifecycle](./data/state-order.mmd)** - Order states from PENDING through COMPLETED/CANCELLED, including payment, shipping, delivery confirmation, and escrow release
- **[Product Lifecycle](./data/state-product.mmd)** - Product states from DRAFT through PUBLISHED/ARCHIVED, including inventory management and visibility

## Critical Flows

Sequence diagrams for important interaction flows:

- **[Checkout & Escrow Flow](./flows/sequence-checkout-escrow.mmd)** - Complete checkout process with cart validation, payment authorization, escrow creation, and notifications
- **[Fulfillment & Release Flow](./flows/sequence-fulfillment-release.mmd)** - Order fulfillment from shipping through delivery confirmation and escrow fund release
- **[Product Browse Flow](./flows/sequence-product-browse.mmd)** - Customer product discovery, search, view, and add-to-cart interaction

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
