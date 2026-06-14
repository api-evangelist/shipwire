# Shipwire GraphQL Schema

## Overview

This document describes a conceptual GraphQL schema for the Shipwire 3PL fulfillment platform. Shipwire exposes nine distinct REST APIs covering order management, stock tracking, receiving, returns, rate shopping, product catalogs, purchase orders, webhooks, and address validation. This GraphQL schema unifies those capabilities into a single, type-safe query and mutation surface.

## Provider

- **Name**: Shipwire
- **Website**: https://www.shipwire.com/
- **Developer Portal**: https://www.shipwire.com/developers/
- **Base URL**: https://api.shipwire.com/
- **Sandbox**: https://api.beta.shipwire.com/

## Schema Source

Conceptual schema derived from Shipwire REST API documentation covering:

- Order API — outbound B2C order creation, updates, cancellation, and tracking
- Stock API — real-time inventory levels across fulfillment centers
- Receiving API — inbound ASN management and warehouse receiving operations
- Returns API — return label generation, postage, and return tracking
- Rate API — real-time shipping quotes across carriers and service levels
- Product API — product catalog, marketing inserts, and product kits
- Purchase Order API — B2B purchase order creation and fulfillment
- Webhooks API — event subscriptions for fulfillment lifecycle notifications
- Address Validation API — shipping address verification before order submission

## Top-Level Queries

| Query | Description |
|---|---|
| `order(id: ID!)` | Fetch a single order by ID |
| `orders(filter: OrderFilter, page: Int, limit: Int)` | List orders with optional filtering |
| `product(id: ID!)` | Fetch a product by ID |
| `products(filter: ProductFilter, page: Int, limit: Int)` | List products in the catalog |
| `inventory(sku: String!)` | Fetch inventory levels for a SKU |
| `inventoryList(filter: InventoryFilter, page: Int, limit: Int)` | List inventory across fulfillment centers |
| `warehouse(id: ID!)` | Fetch a warehouse by ID |
| `warehouses` | List all available warehouses |
| `shipment(id: ID!)` | Fetch a shipment by ID |
| `rate(input: RateInput!)` | Get shipping rate quotes |
| `returnOrder(id: ID!)` | Fetch a return order by ID |
| `returnOrders(filter: ReturnFilter, page: Int, limit: Int)` | List return orders |
| `receivingOrder(id: ID!)` | Fetch an inbound receiving order by ID |
| `receivingOrders(filter: ReceivingFilter, page: Int, limit: Int)` | List receiving orders |
| `webhook(id: ID!)` | Fetch a webhook subscription by ID |
| `webhooks` | List all webhook subscriptions |
| `carrier(id: ID!)` | Fetch a carrier by ID |
| `carriers` | List all supported carriers |
| `stock(warehouseId: ID!, sku: String!)` | Check stock at a specific warehouse |
| `validateAddress(input: AddressInput!)` | Validate a shipping address |

## Top-Level Mutations

| Mutation | Description |
|---|---|
| `createOrder(input: CreateOrderInput!)` | Submit a new outbound order |
| `updateOrder(id: ID!, input: UpdateOrderInput!)` | Modify an existing order |
| `cancelOrder(id: ID!)` | Cancel an order |
| `holdOrder(id: ID!, reason: HoldReason!)` | Place an order on hold |
| `releaseHold(id: ID!)` | Release an order hold |
| `createProduct(input: CreateProductInput!)` | Add a product to the catalog |
| `updateProduct(id: ID!, input: UpdateProductInput!)` | Update product details |
| `createReceivingOrder(input: CreateReceivingInput!)` | Submit an inbound ASN |
| `updateReceivingOrder(id: ID!, input: UpdateReceivingInput!)` | Update a receiving order |
| `createReturnOrder(input: CreateReturnInput!)` | Initiate a customer return |
| `cancelReturnOrder(id: ID!)` | Cancel a return order |
| `createWebhook(input: CreateWebhookInput!)` | Subscribe to a webhook event |
| `deleteWebhook(id: ID!)` | Remove a webhook subscription |
| `createAPIKey(input: CreateAPIKeyInput!)` | Generate a new API key |
| `revokeAPIKey(id: ID!)` | Revoke an existing API key |

## Named Types (60+)

### Order Domain
- `Order` — top-level outbound order record
- `OrderDetails` — extended order metadata and configuration
- `OrderStatus` — enum of order lifecycle states
- `OrderType` — enum distinguishing B2C, B2B, and kit orders
- `OrderLineItem` — individual product line within an order
- `OrderTracking` — tracking summary attached to an order
- `ShipTo` — recipient contact and address for an order

### Address Domain
- `ShipAddress` — structured shipping address with validation fields

### Product Domain
- `Product` — product catalog entry
- `ProductDetails` — extended product attributes including packaging info
- `ProductSKU` — SKU identifier and barcode details
- `ProductDimensions` — length, width, height for a product
- `ProductWeight` — weight in multiple unit representations
- `ProductClass` — classification enum for hazmat, perishable, etc.

### Inventory Domain
- `Inventory` — aggregate inventory record for a SKU
- `InventoryDetails` — breakdown of committed, available, and in-transit stock
- `InventoryQuantity` — quantity type with unit and value
- `InventoryLocation` — per-warehouse inventory position

### Warehouse Domain
- `Warehouse` — fulfillment center record
- `WarehouseDetails` — address, capabilities, and operational details
- `WarehouseRegion` — geographic region enum for warehouse grouping
- `WarehouseCapacity` — available capacity metrics

### Shipment Domain
- `Shipment` — outbound shipment record
- `ShipmentDetails` — carrier, service level, and weight/dim details
- `ShipmentStatus` — enum of shipment lifecycle states
- `ShipmentMethod` — selected service method for a shipment
- `Package` — physical package within a shipment
- `PackageDetails` — package contents and weight
- `PackageDimensions` — outer dimensions of a shipping package

### Carrier Domain
- `Carrier` — carrier entity with supported services
- `CarrierDetails` — carrier capabilities and contact info
- `CarrierName` — enum of supported carrier names (UPS, FedEx, USPS, DHL, etc.)

### Tracking Domain
- `TrackingNumber` — tracking number record with carrier association
- `TrackingEvent` — individual scan or status update in tracking history

### Rate Domain
- `Rate` — shipping rate quote response
- `RateDetails` — cost breakdown by carrier and service level

### Return Domain
- `ReturnOrder` — customer return order record
- `ReturnDetails` — return instructions and label information
- `ReturnStatus` — enum of return order lifecycle states
- `ReturnLineItem` — individual product line in a return

### Hold Domain
- `Hold` — hold record applied to an order
- `HoldDetails` — hold context including timestamps and resolution
- `HoldReason` — enum of reasons an order may be held

### Stock Domain
- `Stock` — stock position at a specific warehouse for a SKU
- `StockDetails` — granular stock breakdown by lot and location
- `StockReceiving` — inbound stock not yet putaway

### Receiving Domain
- `ReceivingOrder` — inbound ASN / receiving order record
- `ReceivingDetails` — expected contents and schedule for a receiving order

### Auth / Platform Domain
- `APIKey` — API credential record with scopes and expiry
- `Token` — short-lived authentication token

### Webhook Domain
- `Webhook` — webhook subscription record
- `WebhookEvent` — enum of subscribable event types

## Usage Notes

- Authentication uses HTTP Basic Auth with an API key and password against `https://api.shipwire.com/api/v3/`.
- The sandbox environment at `https://api.beta.shipwire.com/` accepts test credentials.
- All monetary values are represented as `Float` with an accompanying `currency` field (ISO 4217).
- Pagination follows a `limit` / `offset` pattern consistent with the underlying REST APIs.
- Webhook event subscriptions cover order, shipment, inventory, and return lifecycle events.
