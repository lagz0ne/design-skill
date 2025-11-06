# Test Scenario 1: E-commerce Platform

## Scenario Description

**Project:** Online marketplace for handmade goods

**User Request (Initial):**
"I want to build an e-commerce platform where artisans can sell handmade products. Customers should be able to browse, add to cart, checkout, and track orders. Sellers need a dashboard to manage inventory and view sales."

**Complexity Level:** Medium
- Multiple user types (buyers, sellers, admins)
- Core flows (browse, cart, checkout, order tracking)
- Payment integration
- Inventory management

## Expected Skill Behavior

### Phase 1: Requirements
- Should ask about key actors (buyers, sellers, admins, payment provider)
- Should ask about constraints (scale, budget, timeline, tech stack)
- Should ask about payment method preferences
- Should ask about success criteria

### Phase 2: Big Picture
- Should identify events: ProductListed, ItemAddedToCart, OrderPlaced, PaymentProcessed, OrderShipped, OrderDelivered
- Should identify commands: ListProduct, AddToCart, PlaceOrder, ProcessPayment, ShipOrder
- Should identify actors: Customer, Seller, Admin
- Should identify systems: Payment Gateway, Shipping Provider
- Should mark hotspots: Refund policy, inventory sync, payment failures

### Phase 3: Process EventStorming
- Should zoom into critical processes:
  - Order checkout process (detailed)
  - Product listing process
  - Order fulfillment process
- Should identify aggregates: Product, Cart, Order, Payment, Shipment
- Should show state transitions clearly

### Phase 4: System Design
- **ERD**: Should include entities: User, Product, Category, Cart, CartItem, Order, OrderItem, Payment, Shipment
- **State Charts**: Should create for Order lifecycle, Payment lifecycle
- **Sequence Diagrams**: Should create for checkout flow, payment processing

### Phase 5: Integration
- Should generate catalog README with navigation
- Should ask about next steps (implementation planning)

## Pressure Points to Test

1. **Token efficiency**: Will agent create verbose descriptions or use diagrams?
2. **Iterative refinement**: If we say "wait, we need seller ratings too", will agent go back and update artifacts?
3. **Completeness**: Will agent miss critical flows like refunds or inventory updates?
4. **Mermaid conventions**: Will agent follow color-coding for EventStorming?

## Expected Artifacts

```
docs/design-catalog/
  README.md
  requirements.md
  big-picture.mmd
  processes/
    process-checkout.mmd
    process-listing.mmd
    process-fulfillment.mmd
  data/
    erd.mmd
    state-order.mmd
    state-payment.mmd
  flows/
    sequence-checkout.mmd
    sequence-payment.mmd
```

## Test Questions During Execution

- Mid-Phase 2: "Actually, we also need seller ratings and reviews"
- Mid-Phase 3: "What about returns and refunds?"
- Phase 4: "The ERD looks incomplete - where's the shipping address?"
- Phase 5: "Can we go back and add admin approval for new sellers?"

## Success Criteria

- All 5 phases completed
- Catalog structure created correctly
- Mermaid diagrams follow conventions
- Handles iterative refinement gracefully
- Token-efficient (diagrams, not walls of text)
