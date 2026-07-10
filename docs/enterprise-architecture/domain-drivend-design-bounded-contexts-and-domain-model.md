# Domain-Driven Design (DDD) – Bounded Contexts & Domain Model

## Global Retail Enterprise (USA, Canada & Australia)

---

# Document Information

| Item               | Details                                       |
| ------------------ | --------------------------------------------- |
| Document           | Domain-Driven Design (DDD) – Bounded Contexts |
| Organization       | Global Retail Enterprise                      |
| Industry           | Retail & E-Commerce                           |
| Methodology        | Domain-Driven Design (DDD)                    |
| Architecture Style | Microservices + Event-Driven Architecture     |
| Cloud Platform     | Microsoft Azure                               |
| Regions            | USA, Canada, Australia                        |
| Version            | 1.0                                           |

---

# 1. Domain Overview

The enterprise is decomposed into independent **bounded contexts**, each aligned with a business capability. Every bounded context owns its business logic, APIs, database, events, and deployment lifecycle. Communication between contexts occurs primarily through asynchronous domain events and well-defined APIs.

## Core Domains

| Domain           | Classification |
| ---------------- | -------------- |
| Ecommerce        | Core           |
| Customer         | Core           |
| Loyalty          | Supporting     |
| Inventory        | Core           |
| Warehouse        | Core           |
| Supply Chain     | Core           |
| Vendor           | Supporting     |
| Pricing          | Core           |
| Promotions       | Supporting     |
| Order Management | Core           |
| Payments         | Core           |
| Shipping         | Supporting     |
| Notifications    | Generic        |
| Fraud            | Supporting     |
| Analytics        | Generic        |

---

# 2. Domain Relationship Overview

```text
Customer
    │
    ▼
Ecommerce ─────► Cart/Checkout ─────► Order Management
    │                                    │
    │                                    ├────► Payments
    │                                    ├────► Inventory
    │                                    ├────► Shipping
    │                                    ├────► Notifications
    │                                    └────► Loyalty
    │
    ├────► Pricing
    ├────► Promotions
    └────► Product Catalog

Inventory ◄──── Warehouse ◄──── Supply Chain ◄──── Vendor

Analytics subscribes to events from every domain.

Fraud monitors Customer, Payments, Orders, and Shipping.
```

---

# 3. Ecommerce Domain

## Responsibilities

* Product browsing
* Search and navigation
* Shopping experience
* Shopping cart
* Checkout orchestration
* Customer session management

### Aggregates

* Shopping Cart
* Checkout Session
* Wishlist

### Entities

* Cart
* Cart Item
* Wishlist
* Checkout Session
* Saved Cart

### Domain Events

* CartCreated
* ItemAddedToCart
* ItemRemovedFromCart
* CheckoutStarted
* CheckoutCompleted
* WishlistUpdated

### APIs

* Cart API
* Checkout API
* Wishlist API
* Search API

### Dependencies

* Customer
* Product Catalog
* Pricing
* Promotions
* Inventory
* Order Management

### Ownership Boundary

Owns customer shopping sessions, cart lifecycle, and checkout initiation. Does **not** own product master data, pricing rules, inventory quantities, or order fulfillment.

---

# 4. Customer Domain

## Responsibilities

* Customer lifecycle
* Identity
* Profile management
* Addresses
* Preferences
* Authentication
* Consent management

### Aggregates

* Customer
* Customer Profile
* Address Book

### Entities

* Customer
* Address
* Contact
* Preference
* Communication Consent

### Domain Events

* CustomerRegistered
* CustomerUpdated
* AddressAdded
* CustomerDeleted
* ConsentUpdated

### APIs

* Customer API
* Profile API
* Identity API

### Dependencies

* Notifications
* Loyalty

### Ownership Boundary

Owns customer master data and identity. Other domains consume customer information through APIs or published events.

---

# 5. Loyalty Domain

## Responsibilities

* Loyalty enrollment
* Points accrual
* Rewards
* Membership tiers
* Benefits

### Aggregates

* Loyalty Account
* Reward Wallet

### Entities

* Member
* Reward
* Point Transaction
* Tier

### Domain Events

* LoyaltyMemberCreated
* PointsEarned
* PointsRedeemed
* TierUpgraded

### APIs

* Loyalty API
* Rewards API

### Dependencies

* Customer
* Order Management

### Ownership Boundary

Owns loyalty accounts, points, rewards, and membership tiers. Does not own customer profiles or order data.

---

# 6. Inventory Domain

## Responsibilities

* Inventory visibility
* Inventory reservation
* Stock allocation
* Stock adjustments
* Availability

### Aggregates

* Inventory Item
* Reservation

### Entities

* SKU Inventory
* Reservation
* Allocation
* Stock Adjustment

### Domain Events

* InventoryReserved
* InventoryReleased
* InventoryAdjusted
* InventoryAllocated
* StockOutDetected

### APIs

* Inventory API
* Availability API
* Reservation API

### Dependencies

* Warehouse
* Supply Chain
* Order Management

### Ownership Boundary

Owns inventory quantities, reservations, and allocation logic. Warehouse owns physical execution.

---

# 7. Warehouse Domain

## Responsibilities

* Receiving
* Putaway
* Picking
* Packing
* Shipping preparation
* Cycle counting

### Aggregates

* Warehouse Order
* Picking Batch

### Entities

* Warehouse
* Bin
* Picking Task
* Packing Task
* Shipment Package

### Domain Events

* GoodsReceived
* PickingCompleted
* PackingCompleted
* ShipmentPrepared

### APIs

* Warehouse API
* Picking API
* Packing API

### Dependencies

* Inventory
* Shipping
* Supply Chain

### Ownership Boundary

Owns warehouse execution and physical inventory movements inside fulfillment centers.

---

# 8. Supply Chain Domain

## Responsibilities

* Procurement
* Distribution
* Logistics planning
* Demand planning
* Replenishment

### Aggregates

* Purchase Order
* Distribution Plan

### Entities

* Supplier Order
* Distribution Center
* Transfer Order
* Delivery Schedule

### Domain Events

* PurchaseOrderCreated
* GoodsDispatched
* GoodsReceived
* ReplenishmentTriggered

### APIs

* Procurement API
* Replenishment API

### Dependencies

* Vendor
* Warehouse
* Inventory

### Ownership Boundary

Owns upstream product flow from suppliers to warehouses and stores.

---

# 9. Vendor Domain

## Responsibilities

* Vendor onboarding
* Supplier catalog
* Vendor compliance
* Contracts
* Performance management

### Aggregates

* Vendor
* Vendor Contract

### Entities

* Vendor
* Contract
* Certification
* Contact

### Domain Events

* VendorRegistered
* VendorApproved
* VendorSuspended
* VendorUpdated

### APIs

* Vendor API
* Supplier API

### Dependencies

* Supply Chain

### Ownership Boundary

Owns supplier information and commercial relationships.

---

# 10. Pricing Domain

## Responsibilities

* Base pricing
* Regional pricing
* Dynamic pricing
* Customer pricing
* Taxable price calculation

### Aggregates

* Price Book
* Pricing Rule

### Entities

* Price
* Price Rule
* Currency
* Effective Date

### Domain Events

* PriceChanged
* PriceActivated
* PriceExpired

### APIs

* Pricing API
* Price Calculation API

### Dependencies

* Promotions
* Product Catalog

### Ownership Boundary

Owns price calculation and pricing rules. Promotion discounts are managed separately.

---

# 11. Promotions Domain

## Responsibilities

* Discounts
* Coupons
* Campaigns
* Offers
* Promotional eligibility

### Aggregates

* Campaign
* Coupon

### Entities

* Promotion
* Coupon
* Discount Rule
* Campaign

### Domain Events

* PromotionActivated
* CouponRedeemed
* PromotionExpired

### APIs

* Promotion API
* Coupon API

### Dependencies

* Pricing
* Customer
* Loyalty

### Ownership Boundary

Owns promotional rules and campaign lifecycle.

---

# 12. Order Management Domain

## Responsibilities

* Order creation
* Order lifecycle
* Fulfillment orchestration
* Returns
* Cancellations

### Aggregates

* Order
* Return

### Entities

* Order
* Order Line
* Return
* Cancellation
* Fulfillment Request

### Domain Events

* OrderCreated
* OrderConfirmed
* OrderCancelled
* OrderFulfilled
* ReturnRequested
* RefundCompleted

### APIs

* Order API
* Return API

### Dependencies

* Inventory
* Payments
* Shipping
* Notifications
* Loyalty
* Fraud

### Ownership Boundary

Owns the complete order lifecycle but delegates payment, inventory, shipping, and notifications to their respective domains.

---

# 13. Payments Domain

## Responsibilities

* Payment authorization
* Capture
* Refunds
* Settlement
* Payment reconciliation

### Aggregates

* Payment
* Refund

### Entities

* Payment
* Transaction
* Authorization
* Refund

### Domain Events

* PaymentAuthorized
* PaymentCaptured
* PaymentFailed
* RefundProcessed

### APIs

* Payment API
* Refund API

### Dependencies

* Fraud
* Order Management

### Ownership Boundary

Owns all payment transactions and financial processing.

---

# 14. Shipping Domain

## Responsibilities

* Carrier integration
* Shipment creation
* Tracking
* Delivery status

### Aggregates

* Shipment

### Entities

* Shipment
* Package
* Carrier
* Tracking Event

### Domain Events

* ShipmentCreated
* ShipmentDispatched
* ShipmentDelivered
* DeliveryFailed

### APIs

* Shipping API
* Tracking API

### Dependencies

* Warehouse
* Order Management

### Ownership Boundary

Owns shipment lifecycle and carrier communication.

---

# 15. Notifications Domain

## Responsibilities

* Email
* SMS
* Push notifications
* Templates
* Delivery tracking

### Aggregates

* Notification

### Entities

* Notification
* Template
* Subscription
* Delivery Status

### Domain Events

* NotificationQueued
* NotificationSent
* NotificationFailed

### APIs

* Notification API
* Template API

### Dependencies

* All business domains

### Ownership Boundary

Owns outbound customer communications and notification preferences.

---

# 16. Fraud Domain

## Responsibilities

* Fraud detection
* Risk scoring
* Transaction monitoring
* Case management

### Aggregates

* Fraud Case

### Entities

* Risk Score
* Fraud Alert
* Investigation
* Rule

### Domain Events

* FraudDetected
* PaymentFlagged
* OrderBlocked
* InvestigationCompleted

### APIs

* Fraud API
* Risk Assessment API

### Dependencies

* Customer
* Payments
* Order Management

### Ownership Boundary

Owns fraud evaluation and investigation workflows. It advises other domains but does not own customer, payment, or order data.

---

# 17. Analytics Domain

## Responsibilities

* Enterprise reporting
* Data warehousing
* KPIs
* Machine learning
* Forecasting
* Business intelligence

### Aggregates

* Analytical Dataset
* KPI

### Entities

* Dashboard
* Metric
* Forecast
* Report

### Domain Events

* KPICalculated
* ForecastGenerated
* ReportPublished

### APIs

* Reporting API
* Analytics API

### Dependencies

* Consumes events from all domains

### Ownership Boundary

Owns analytical models, reports, and derived datasets. Operational domains remain the source of truth for transactional data.

---

# 18. Shared Domain Events

Common enterprise events published across bounded contexts include:

* CustomerRegistered
* ProductCreated
* PriceChanged
* PromotionActivated
* CartCheckedOut
* OrderCreated
* OrderConfirmed
* PaymentAuthorized
* PaymentCaptured
* InventoryReserved
* InventoryReleased
* ShipmentCreated
* ShipmentDelivered
* ReturnRequested
* RefundProcessed
* LoyaltyPointsEarned
* NotificationSent
* FraudDetected

These events form the enterprise event catalog and are exchanged through the organization's event streaming platform (for example, Azure Event Hubs or Apache Kafka).

---

# 19. Domain Ownership Matrix

| Domain           | Owns Business Logic | Owns APIs | Owns Data | Publishes Events | Independent Deployment |
| ---------------- | :-----------------: | :-------: | :-------: | :--------------: | :--------------------: |
| Ecommerce        |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Customer         |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Loyalty          |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Inventory        |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Warehouse        |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Supply Chain     |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Vendor           |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Pricing          |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Promotions       |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Order Management |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Payments         |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Shipping         |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Notifications    |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Fraud            |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |
| Analytics        |          ✓          |     ✓     |     ✓     |         ✓        |            ✓           |

---

# 20. Summary

The enterprise is organized into independently deployable bounded contexts that map directly to business capabilities. Each domain owns its business rules, APIs, data, and events, enabling autonomous development teams, scalable cloud-native deployment, and resilient event-driven integration. This decomposition provides a strong foundation for microservices, TOGAF-aligned enterprise architecture, and future expansion into additional regions, channels, and business models.
