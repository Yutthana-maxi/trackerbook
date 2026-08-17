# Database Design

## Overview

Database ถูกออกแบบโดยใช้ PostgreSQL และ Prisma ORM

หลักการสำคัญ

* UUID Primary Key
* Transaction เป็น Source of Truth
* รองรับ Multi Account
* รองรับ OCR Receipt Processing
* รองรับ Goal Contribution Tracking
* รองรับ Future Scaling

---

# Enums

## AccountType

```text
BANK
CASH
EWALLET
SAVING
```

---

## TransactionType

```text
INCOME
EXPENSE
TRANSFER
SAVING
```

---

## TransactionStatus

```text
PENDING
COMPLETED
CANCELLED
```

---

## TransactionSource

```text
MANUAL
OCR
SYSTEM
```

---

## GoalStatus

```text
ACTIVE
COMPLETED
CANCELLED
```

---

## ReceiptStatus

```text
PENDING
PROCESSING
COMPLETED
FAILED
```

---

## NotificationType

```text
INCOME
EXPENSE
BUDGET_ALERT
GOAL_PROGRESS
RECEIPT_PROCESSED
UPCOMING_EXPENSE
```

---

# User

## users

| Field         | Type          |
| ------------- | ------------- |
| id            | UUID          |
| username      | String UNIQUE |
| email         | String UNIQUE |
| password_hash | String        |
| is_verified   | Boolean       |
| created_at    | Timestamp     |
| updated_at    | Timestamp     |

### Relationships

```text
User 1:N Account

User 1:N Transaction

User 1:N Goal

User 1:N Budget

User 1:N Receipt

User 1:N Notification
```

---

# Account

## accounts

| Field      | Type        |
| ---------- | ----------- |
| id         | UUID        |
| user_id    | UUID        |
| name       | String      |
| type       | AccountType |
| created_at | Timestamp   |
| updated_at | Timestamp   |

### Examples

```text
SCB

KBank

Cash

TrueMoney Wallet
```

### Relationships

```text
User 1:N Account

Account 1:N Transaction

Account 1:N GoalContribution
```

---

# Category

## categories

| Field      | Type      |
| ---------- | --------- |
| id         | UUID      |
| user_id    | UUID NULL |
| name       | String    |
| created_at | Timestamp |
| updated_at | Timestamp |

### Examples

```text
Food

Travel

Car

Home

Internet

Saving

Investment

Other
```

### Relationships

```text
Category 1:N Transaction

Category 1:N Budget
```

---

# Transaction

## transactions

| Field            | Type              |
| ---------------- | ----------------- |
| id               | UUID              |
| user_id          | UUID              |
| account_id       | UUID              |
| category_id      | UUID NULL         |
| receipt_id       | UUID NULL         |
| type             | TransactionType   |
| status           | TransactionStatus |
| source           | TransactionSource |
| amount           | Decimal(12,2)     |
| description      | Text              |
| transaction_date | Timestamp         |
| created_at       | Timestamp         |
| updated_at       | Timestamp         |

### Relationships

```text
User 1:N Transaction

Account 1:N Transaction

Category 1:N Transaction

Receipt 1:1 Transaction
```

---

# Receipt

## receipts

| Field            | Type               |
| ---------------- | ------------------ |
| id               | UUID               |
| user_id          | UUID               |
| file_hash        | String UNIQUE      |
| image_url        | String             |
| ocr_status       | ReceiptStatus      |
| ocr_text         | Text               |
| amount           | Decimal(12,2) NULL |
| transaction_date | Timestamp NULL     |
| created_at       | Timestamp          |

### Relationships

```text
User 1:N Receipt

Receipt 1:1 Transaction
```

---

# Goal

## goals

| Field         | Type           |
| ------------- | -------------- |
| id            | UUID           |
| user_id       | UUID           |
| title         | String         |
| target_amount | Decimal(12,2)  |
| target_date   | Timestamp NULL |
| status        | GoalStatus     |
| created_at    | Timestamp      |
| updated_at    | Timestamp      |

### Notes

Current Amount จะไม่ถูกเก็บ

คำนวณจาก Goal Contributions

### Relationships

```text
User 1:N Goal

Goal 1:N GoalContribution
```

---

# Goal Contribution

## goal_contributions

| Field          | Type          |
| -------------- | ------------- |
| id             | UUID          |
| goal_id        | UUID          |
| account_id     | UUID          |
| transaction_id | UUID          |
| amount         | Decimal(12,2) |
| note           | Text NULL     |
| created_at     | Timestamp     |

### Purpose

ใช้เก็บประวัติการออมแต่ละครั้ง

### Relationships

```text
Goal 1:N GoalContribution

Account 1:N GoalContribution

Transaction 1:1 GoalContribution
```

### Example

```text
Goal

MacBook Air

↓

SCB

+3000

↓

KBank

+5000

↓

Cash

+2000
```

---

# Budget

## budgets

| Field       | Type          |
| ----------- | ------------- |
| id          | UUID          |
| user_id     | UUID          |
| category_id | UUID          |
| amount      | Decimal(12,2) |
| month       | Integer       |
| year        | Integer       |
| created_at  | Timestamp     |
| updated_at  | Timestamp     |

### Relationships

```text
User 1:N Budget

Category 1:N Budget
```

---

# Notification

## notifications

| Field          | Type             |
| -------------- | ---------------- |
| id             | UUID             |
| user_id        | UUID             |
| type           | NotificationType |
| title          | String           |
| message        | Text             |
| is_read        | Boolean          |
| reference_type | String           |
| reference_id   | UUID             |
| created_at     | Timestamp        |

### Relationships

```text
User 1:N Notification
```

### Example

```text
type = BUDGET_ALERT

reference_type = budget

reference_id = budget_id
```

---

# ER Diagram

```text
User
 |
 ├── Account
 |
 ├── Transaction
 |
 ├── Goal
 |      |
 |      └── GoalContribution
 |
 ├── Budget
 |
 ├── Receipt
 |
 └── Notification

Account
 |
 ├── Transaction
 |
 └── GoalContribution

Category
 |
 ├── Transaction
 |
 └── Budget

Receipt
 |
 └── Transaction

Goal
 |
 └── GoalContribution

Transaction
 |
 └── GoalContribution
```

---

# Design Decisions

## DD-001

Transaction เป็น Source of Truth

ไม่เก็บ Balance ใน Account

---

## DD-002

Receipt 1 ใบ สร้าง Transaction ได้ 1 รายการ

---

## DD-003

Goal ไม่เก็บ Current Amount

คำนวณจาก Goal Contributions

---

## DD-004

รองรับ Multi Account ตั้งแต่ Version แรก

---

## DD-005

Duplicate Receipt Detection ด้วย File Hash
