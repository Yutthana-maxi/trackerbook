# Smart Finance Tracker - Business Logic

## Overview

Smart Finance Tracker เป็นระบบจัดการการเงินส่วนบุคคลที่รองรับหลายบัญชีเงิน (Multi-Account) โดยใช้แนวคิด Transaction เป็น Source of Truth ของระบบ

ระบบถูกออกแบบให้รองรับการติดตามรายรับรายจ่าย การออม การตั้งงบประมาณ และการประมวลผลสลิปอัตโนมัติผ่าน OCR

---

# Core Business Rules

## BR-001 Transaction Is Source of Truth

Transaction เป็นแหล่งข้อมูลหลักของระบบ

ห้ามเก็บยอดคงเหลือ (Balance) ไว้ใน Account โดยตรง

ยอดเงินใน Account จะถูกคำนวณจาก Transaction ทุกครั้ง

สูตร

Balance = Income - Expense - Saving + Incoming Transfer - Outgoing Transfer

เหตุผล

* ป้องกันข้อมูลไม่ตรงกัน
* รองรับการแก้ไขย้อนหลัง
* ตรวจสอบ Audit ได้
* เหมาะกับระบบการเงิน

---

## BR-002 Multi Account Support

ผู้ใช้ 1 คนสามารถมีหลายบัญชีได้

ตัวอย่าง

* SCB
* KBank
* Cash
* TrueMoney Wallet
* Savings Account

ทุก Transaction ต้องสังกัด Account เสมอ

---

## BR-003 Transaction Ownership

Transaction ทุกรายการต้องมีเจ้าของเพียง 1 คน

User 1 คนสามารถมี Transaction ได้ไม่จำกัดจำนวน

---

## BR-004 Transaction Category

Transaction 1 รายการสามารถอยู่ได้เพียง 1 Category

ตัวอย่าง

Expense

Food

70 บาท

หากต้องการหลาย Category ในอนาคต ให้พัฒนาเป็น Split Transaction

---

## BR-005 Transaction Types

ระบบรองรับ Transaction 4 ประเภท

* INCOME
* EXPENSE
* SAVING
* TRANSFER

---

## BR-006 Transaction Status

ระบบรองรับสถานะ

* PENDING
* COMPLETED
* CANCELLED

เฉพาะ Transaction ที่เป็น COMPLETED เท่านั้นที่จะถูกนำไปคำนวณใน Dashboard และ Balance

---

# Account Logic

## BR-007 Account Balance Calculation

ระบบไม่เก็บ Balance ในตาราง Account

Balance จะถูกคำนวณจาก Transaction

ตัวอย่าง

Income = 10,000

Expense = 2,000

Saving = 1,000

Balance = 7,000

---

## BR-008 Transfer Between Accounts

Transfer เป็นการเคลื่อนย้ายเงินระหว่าง Account

ตัวอย่าง

SCB → KBank

1,000 บาท

ผลลัพธ์

SCB ลดลง 1,000

KBank เพิ่มขึ้น 1,000

Transfer ต้องมี

* Source Account
* Destination Account

---

# Goal Saving Logic

## BR-009 Goal Creation

ผู้ใช้สามารถสร้าง Goal ได้หลายรายการ

ตัวอย่าง

* MacBook Air
* Japan Trip
* Emergency Fund

---

## BR-010 Goal Contribution

Goal ไม่เก็บ Current Amount โดยตรง

Current Amount จะถูกคำนวณจาก Goal Contributions

สูตร

Current Amount = SUM(Goal Contributions)

---

## BR-011 Contribution Source Tracking

ทุก Goal Contribution ต้องรู้ว่าเงินมาจากไหน

ข้อมูลที่ต้องเก็บ

* Goal
* Account
* Transaction
* Amount

ตัวอย่าง

Goal

MacBook Air

Contribution

SCB → 3,000

KBank → 5,000

Cash → 2,000

---

## BR-012 Goal Progress

ระบบคำนวณความคืบหน้าอัตโนมัติ

Progress (%) = Current Amount / Target Amount × 100

เมื่อ Current Amount >= Target Amount

Goal Status = COMPLETED

---

# Budget Logic

## BR-013 Budget Per Category

Budget ถูกกำหนดตาม Category

ตัวอย่าง

Food = 5,000

Travel = 3,000

Car = 4,000

---

## BR-014 Budget Monitoring

ระบบตรวจสอบ Budget แบบ Real-Time

แจ้งเตือนเมื่อถึง

* 80%
* 90%
* 100%

---

## BR-015 Budget Calculation

Budget Usage คำนวณจาก Expense Transactions เท่านั้น

Income และ Saving จะไม่ถูกนำมาคิด Budget

---

# Receipt OCR Logic

## BR-016 Receipt Upload

ผู้ใช้สามารถอัปโหลดสลิปได้หลายรายการ

---

## BR-017 Duplicate Receipt Detection

ห้ามอัปโหลดสลิปซ้ำ

ระบบสร้าง File Hash ทุกครั้ง

Workflow

Upload Receipt

↓

Generate File Hash

↓

Check Existing Hash

↓

Duplicate ?

YES → Reject

NO → Continue OCR

---

## BR-018 OCR Processing

Receipt มีสถานะ

* PENDING
* PROCESSING
* COMPLETED
* FAILED

---

## BR-019 Receipt Transaction Creation

Receipt 1 ใบ สร้าง Transaction ได้ 1 รายการ

Workflow

Receipt

↓

OCR

↓

Extract Data

↓

Create Transaction

---

## BR-020 OCR Failure Handling

หาก OCR ไม่สามารถอ่านข้อมูลได้

Receipt Status = FAILED

ผู้ใช้สามารถแก้ไขข้อมูลด้วยตนเอง

---

# Notification Logic

## BR-021 Notification Ownership

Notification ทุกอันต้องมีเจ้าของ

User 1 คนสามารถมี Notification ได้ไม่จำกัด

---

## BR-022 Notification Types

ระบบรองรับ

* INCOME
* EXPENSE
* BUDGET_ALERT
* GOAL_PROGRESS
* RECEIPT_PROCESSED
* UPCOMING_EXPENSE

---

## BR-023 Notification Status

Notification มีสถานะ

* READ
* UNREAD

---

## BR-024 Notification References

Notification สามารถอ้างอิง Entity อื่นได้

ตัวอย่าง

* Transaction
* Goal
* Receipt
* Budget

---

# Scheduled Expense Logic

## BR-025 Recurring Expenses

ระบบรองรับรายจ่ายประจำ

ตัวอย่าง

* Rent
* Internet
* Phone Bill

---

## BR-026 Upcoming Expense Reminder

ระบบแจ้งเตือนก่อนถึงกำหนด

* 7 วัน
* 3 วัน
* 1 วัน

---

# Dashboard Logic

## BR-027 Financial Summary

Dashboard ต้องแสดง

* Total Assets
* Monthly Income
* Monthly Expense
* Net Balance

---

## BR-028 Budget Summary

Dashboard ต้องแสดง

* Budget Usage
* Budget Alerts

---

## BR-029 Goal Summary

Dashboard ต้องแสดง

* Goal Progress
* Remaining Amount

---

## BR-030 Receipt Summary

Dashboard ต้องแสดง

* OCR Success Rate
* Processed Receipts

---

# Data Integrity Rules

## BR-031 User Isolation

ผู้ใช้สามารถเข้าถึงเฉพาะข้อมูลของตนเองเท่านั้น

---

## BR-032 Soft Delete Policy (Future)

ข้อมูลทางการเงินไม่ควรถูกลบจริง

แนะนำให้ใช้ Soft Delete ในอนาคต

---

## BR-033 Audit Friendly System

ทุก Transaction ต้องสามารถตรวจสอบย้อนหลังได้

ระบบต้องสามารถอธิบายได้ว่า

* เงินมาจากไหน
* เงินถูกใช้ไปที่ไหน
* เงินออมมาจากบัญชีใด
* Goal ถูกเติมเงินเมื่อใด
