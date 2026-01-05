# 💳 Payment Gateway – Razorpay / Stripe Style System

This project is a complete implementation of a mini payment gateway similar to Razorpay or Stripe. It supports merchant authentication, order creation, UPI & Card payments, a merchant dashboard, and a hosted checkout page.

---

## System Architecture

![Payment Gateway Architecture](./screenshots/architecture.png)

**Runtime Flow**

* Merchant / User → Dashboard (Port 3000) → API Server (Port 8000) → PostgreSQL (Port 5432)
* Customer → Checkout Page (Port 3001) → Public API Endpoints → API Server (Port 8000)

---

## Prerequisites

* Docker
* Docker Compose

---

## How to Start the Application

```bash
docker-compose up -d --build
```

Verify containers:

```bash
docker ps
```

---

## Service URLs

| Service   | URL                                            |
| --------- | ---------------------------------------------- |
| API       | [http://localhost:8000](http://localhost:8000) |
| Dashboard | [http://localhost:3000](http://localhost:3000) |
| Checkout  | [http://localhost:3001](http://localhost:3001) |
| Database  | localhost:5432                                 |

---

## Test Merchant (Auto Seeded)

| Field      | Value                                       |
| ---------- | ------------------------------------------- |
| ID         | 550e8400-e29b-41d4-a716-446655440000        |
| Email      | [test@example.com](mailto:test@example.com) |
| API Key    | key_test_abc123                             |
| API Secret | secret_test_xyz789                          |

---

## Database Schema

### merchants

| Field      | Type                |
| ---------- | ------------------- |
| id         | UUID (PK)           |
| name       | varchar(255)        |
| email      | varchar(255) UNIQUE |
| api_key    | varchar(64) UNIQUE  |
| api_secret | varchar(64)         |
| is_active  | boolean             |
| created_at | timestamp           |
| updated_at | timestamp           |

### orders

| Field       | Type                   |
| ----------- | ---------------------- |
| id          | order_xxxxxxxxxxxxxxxx |
| merchant_id | UUID (FK)              |
| amount      | integer                |
| currency    | char(3)                |
| status      | varchar(20)            |
| created_at  | timestamp              |
| updated_at  | timestamp              |

### payments

| Field             | Type                          |
| ----------------- | ----------------------------- |
| id                | pay_xxxxxxxxxxxxxxxx          |
| order_id          | FK                            |
| amount            | integer                       |
| method            | upi / card                    |
| status            | processing / success / failed |
| card_network      | varchar(20)                   |
| card_last4        | char(4)                       |
| vpa               | varchar(255)                  |
| error_code        | varchar(50)                   |
| error_description | text                          |

---

## API Endpoints Summary

| Method | Endpoint                   |
| ------ | -------------------------- |
| GET    | /health                    |
| POST   | /api/v1/orders             |
| GET    | /api/v1/orders/{id}        |
| GET    | /api/v1/orders/{id}/public |
| POST   | /api/v1/payments           |
| POST   | /api/v1/payments/public    |
| GET    | /api/v1/payments/{id}      |
| GET    | /api/v1/test/merchant      |

---

## Health Check

```bash
curl http://localhost:8000/health
```

---

## Step 1 – Create Order

```bash
curl -X POST http://localhost:8000/api/v1/orders \
-H "X-Api-Key: key_test_abc123" \
-H "X-Api-Secret: secret_test_xyz789" \
-H "Content-Type: application/json" \
-d '{"amount":50000}'
```

---

## Step 2 – Open Checkout Page

```
http://localhost:3001/checkout?order_id=ORDER_ID
```

---

## Step 3 – Make Payment

**UPI Example**

```
user@paytm
```

**Card Example**

```
4111111111111111
12/25
123
Test User
```

---

## Payment Validation

### VPA Validation

Regex: `^[a-zA-Z0-9._-]+@[a-zA-Z0-9]+$`

### Card Validation

* Luhn algorithm
* Card Networks:

  * Visa → starts with 4
  * Mastercard → 51–55
  * Amex → 34, 37
  * RuPay → 60, 65, 81–89
* Only last 4 digits are stored
* Expiry must be future date

---

## Test Mode

```
TEST_MODE=true
TEST_PAYMENT_SUCCESS=true
TEST_PROCESSING_DELAY=1000
```

Enables deterministic behavior for automated evaluation.

---

## Screenshots

All screenshots are inside `screenshots/` folder:
### architecture.png

![System Architecture](./screenshots/architecture.png)

### Health Check API
![Health API](./screenshots/health_api.png)

### Create Order API
![Create Order API](./screenshots/create-order-api.png)

### Login Page
![Login Page](./screenshots/login.png)

### Dashboard Home
![Dashboard](./screenshots/Dashboard.png)

### Transactions Page
![Transactions](./screenshots/Transactions.png)


### UPI Checkout
![Checkout UPI](./screenshots/checkout-upi.png)

### Card Checkout
![Checkout Card](./screenshots/checkout-card.png)

### Processing State
![Processing](./screenshots/processing.png)

### Payment Success
![Payment Success](./screenshots/payment-success.png)


---


## Environment File (.env.example)

```
DATABASE_URL=postgresql://gateway_user:gateway_pass@postgres:5432/payment_gateway
PORT=8000

TEST_MERCHANT_EMAIL=test@example.com
TEST_API_KEY=key_test_abc123
TEST_API_SECRET=secret_test_xyz789

UPI_SUCCESS_RATE=0.90
CARD_SUCCESS_RATE=0.95
PROCESSING_DELAY_MIN=5000
PROCESSING_DELAY_MAX=10000

TEST_MODE=false
TEST_PAYMENT_SUCCESS=true
TEST_PROCESSING_DELAY=1000
```

---

## Submission Checklist

* docker-compose up -d runs all services
* Test merchant auto-seeded
* Dashboard shows live stats
* Checkout flow functional
* UPI & Card validations working
* Payments stored in DB
* Screenshots & video included
