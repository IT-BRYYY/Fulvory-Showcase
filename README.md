# Fulvory

**B2B ordering, inventory, and fulfillment platform**

Fulvory gives a wholesale supplier one workspace for its catalog, stock, orders, and delivery records. Customer businesses place orders and track their progress. Supplier staff prepare orders and record dispatch, while administrators manage products and team access.

**Java 21 · Spring Boot · Spring Security · PostgreSQL · React · Docker · GitHub Actions**

[Watch the Fulvory walkthrough on YouTube](YOUTUBE_VIDEO_URL)

## See Fulvory in action

The screenshots below show the application running locally with sample data.

### 1. Customer catalog

Business customers browse products, check prices and availability, and select goods for their next order.

![Customer catalog showing products, prices, selling units, and stock availability](screenshots/catalog.png)

### 2. Supplier order queue

Supplier staff can search by order number or business name, filter by status, and see how many orders need preparation or are in transit.

![Supplier workspace showing confirmed, packing, and delivered orders](screenshots/supplier-orders.png)

### 3. Order details and delivery

A single order shows the saved items and prices, fulfillment timeline, recorded dispatch details, and status history through receipt confirmation.

![Delivered order showing its items, total, fulfillment progress, and recorded delivery details](screenshots/order-details.png)

### 4. Inventory workspace

Staff see what is physically on hand, what is reserved for open orders, and what is still available to sell. Stock history records changes and the people who made them.

![Inventory table showing on-hand, reserved, and available quantities](screenshots/inventory.png)

## How an order moves through Fulvory

1. The customer reviews a quote calculated by the backend and confirms the order. Fulvory saves the item and price details and reserves stock.
2. Staff begin packing and record the rider or courier arrangement. They mark the order **Shipped** after handover. Fulvory deducts the quantities from both on-hand and reserved stock.
3. The customer confirms receipt, or authorized staff records confirmation. The order moves to **Delivered**, and its history remains visible. Cancellation before shipping releases the reservation.

Fulvory is designed for **one supplier and one warehouse serving multiple customer businesses**. Staff and administrators share the supplier's workspace. Customers can access orders for their own business. Manual delivery records arrangements made outside Fulvory, and a separate courier simulator demonstrates booking failures, timeouts, and retries.

## Backend engineering

| Challenge | How Fulvory handles it |
|---|---|
| Two buyers want the last units | PostgreSQL row locks and a transaction coordinate stock checks, order creation, and reservation. |
| Checkout is retried after an interruption | A business-scoped idempotency key allows the same submission to return the original order without another reservation. |
| A product price changes during checkout | The server checks the quote again before confirmation. Existing orders keep their original item and price snapshots. |
| Staff work from an outdated screen | Version checks reject stale edits to stock counts and other business records. Eligible state transitions control when stock is released or deducted. |
| Access changes while someone is signed in | Spring Security enforces roles and customer ownership. Password or access changes invalidate earlier sessions on subsequent authenticated requests. |
| An account email cannot be sent immediately | A database outbox retains queued messages for delivery by a worker with bounded retries. Verification and recovery links are single-use and expire. |

The backend is a **modular monolith**. HTTP controllers delegate to services that enforce business rules and own transactions. Data access uses Spring Data JPA and targeted JDBC queries, with Flyway managing the PostgreSQL schema. React and Vite power the frontend. Docker Compose supports local services, and Mailpit captures email during development.

## Automated checks

The project recorded **140 passing backend unit and integration tests** and **7 passing courier simulator tests** during the business pilot. The v1.4.1 frontend update passed **38 frontend tests** and a production build. The GitHub Actions workflow runs the backend, frontend, and simulator checks and includes Docker builds and HTTP smoke tests.

## Showcase repository structure

| Path | Content |
|---|---|
| `README.md` | Project overview, demo link, and technical notes |
| `screenshots/catalog.png` | Customer catalog |
| `screenshots/supplier-orders.png` | Supplier order queue |
| `screenshots/order-details.png` | Delivered order and dispatch record |
| `screenshots/inventory.png` | Inventory workspace |

The application source is maintained in a separate private repository. A code review or technical walkthrough is available on request.

## Project purpose

Fulvory is a personal portfolio project for practicing Java backend development through a complete business workflow. The focus is on designing REST APIs, keeping data consistent across requests, enforcing access rules, and testing behavior when requests fail or are retried.

## Developer

**Bryan** · Aspiring Java Backend Developer · [@IT-BRYYY](https://github.com/IT-BRYYY)
