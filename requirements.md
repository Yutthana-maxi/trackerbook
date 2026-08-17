# Smart Finance Tracker

## Project Overview

Smart Finance Tracker เป็นระบบจัดการการเงินส่วนบุคคล (Personal Finance Platform) ที่ช่วยให้ผู้ใช้สามารถติดตามรายรับรายจ่าย จัดการหลายบัญชีเงิน ตั้งงบประมาณ วางแผนการออม และจัดการข้อมูลทางการเงินผ่าน Dashboard พร้อมรองรับการอัปโหลดสลิปเพื่อสร้างรายการทางการเงินอัตโนมัติด้วย OCR

---

# Core Features

## Authentication

ผู้ใช้สามารถ

* สมัครสมาชิก
* ล็อกอิน
* ล็อกเอาต์
* จัดการ Session
* ยืนยันอีเมล (Future Feature)

---

# Multi Account Management

ผู้ใช้สามารถสร้างบัญชีทางการเงินได้หลายบัญชี

ตัวอย่าง

* SCB
* KBank
* เงินสด (Cash)
* TrueMoney Wallet
* Savings Account

ประเภทบัญชี

* BANK
* CASH
* EWALLET
* SAVING

Business Rules

* User 1 คนสามารถมีได้หลาย Account
* Account Balance จะไม่ถูกเก็บในฐานข้อมูล
* Balance คำนวณจาก Transaction เสมอ
* Transaction เป็น Source of Truth ของระบบ

---

# Transaction Management

ผู้ใช้สามารถ

* เพิ่มรายรับ
* เพิ่มรายจ่าย
* เพิ่มการออม
* เพิ่มรายการโอนเงิน
* แก้ไขรายการ
* ลบรายการ
* ดูประวัติย้อนหลัง

Transaction Types

* INCOME
* EXPENSE
* SAVING
* TRANSFER

Transaction Status

* PENDING
* COMPLETED
* CANCELLED

Business Rules

* Transaction ทุกรายการต้องสังกัด Account
* Transaction ทุกรายการต้องมี Owner เป็น User
* Transaction 1 รายการมี Category ได้ 1 หมวดหมู่

---

# Category Management

หมวดหมู่เริ่มต้นของระบบ

* Food
* Travel
* Car
* Home
* Internet
* Saving
* Investment
* Other

Business Rules

* Category 1 รายการสามารถถูกใช้งานได้หลาย Transaction
* Transaction 1 รายการมีได้เพียง 1 Category

---

# Budget Management

ผู้ใช้สามารถ

* กำหนดงบประมาณรายเดือน
* กำหนดงบตามหมวดหมู่
* ตรวจสอบการใช้งบประมาณ

ตัวอย่าง

Food = 5000 บาท

Travel = 3000 บาท

Car = 4000 บาท

Budget Alerts

* 80%
* 90%
* 100%

---

# Goal Saving System

ผู้ใช้สามารถ

* สร้างเป้าหมายการออม
* กำหนดจำนวนเงินเป้าหมาย
* เพิ่มเงินเข้าเป้าหมาย
* ติดตามความคืบหน้า

ตัวอย่าง

Goal: MacBook Air

Target Amount: 35,000 บาท

Current Amount: 12,000 บาท

Business Rules

* User 1 คนสามารถมีหลาย Goal
* Goal สามารถรับเงินออมได้หลายครั้ง
* ระบบคำนวณ Progress อัตโนมัติ

---

# Receipt OCR

ผู้ใช้สามารถ

* อัปโหลดสลิป
* ส่งสลิปเข้า OCR
* สร้าง Transaction อัตโนมัติ

Receipt Workflow

Upload Receipt

↓

OCR Processing

↓

Extract Transaction Data

↓

Create Transaction

↓

Update Dashboard

↓

Create Notification

Business Rules

* Receipt 1 ใบ สร้าง Transaction ได้ 1 รายการ
* Receipt เดียวกันห้ามอัปโหลดซ้ำ
* ใช้ File Hash สำหรับตรวจสอบ Duplicate
* OCR ต้องสามารถตรวจจับสถานะการประมวลผลได้

Receipt Status

* PENDING
* PROCESSING
* COMPLETED
* FAILED

---

# Notification System

ระบบแจ้งเตือนสำหรับ

* Income
* Expense
* Budget Alert
* Goal Progress
* Receipt Processed
* Upcoming Expense

Notification Status

* READ
* UNREAD

Business Rules

* Notification ทุกอันต้องมีเจ้าของ
* Notification สามารถอ้างอิงไปยัง Entity อื่นได้

ตัวอย่าง

* Transaction
* Goal
* Receipt
* Budget

---

# Scheduled Expense

รองรับรายจ่ายที่เกิดซ้ำ

ตัวอย่าง

* ค่าเช่าห้อง
* ค่าอินเทอร์เน็ต
* ค่าโทรศัพท์

การแจ้งเตือนล่วงหน้า

* 1 วัน
* 3 วัน
* 7 วัน

ก่อนถึงกำหนดชำระ

---

# Dashboard

Dashboard ต้องแสดงข้อมูลต่อไปนี้

Financial Summary

* Total Assets
* Monthly Income
* Monthly Expense
* Net Balance

Transaction Analytics

* Expense by Category
* Income by Category
* Monthly Trends

Budget Analytics

* Budget Usage
* Budget Alerts

Goal Analytics

* Goal Progress
* Remaining Amount

Receipt Analytics

* OCR Success Rate
* Processed Receipts

---

# Security Requirements

* Password Hashing
* Protected Routes
* Authentication Middleware
* Input Validation
* Duplicate Receipt Protection

---

# Performance Requirements

* Pagination สำหรับ Transaction History
* Query Optimization
* Dashboard Aggregation
* Lazy Loading

---

# Reliability Requirements

* Transaction Validation
* Duplicate Receipt Detection
* Audit-Friendly Transaction History
* Source of Truth = Transaction

---

# Database Entities

* User
* Account
* Transaction
* Category
* Budget
* Goal
* Receipt
* Notification

---

# Relationships

User 1:N Account

User 1:N Transaction

User 1:N Budget

User 1:N Goal

User 1:N Receipt

User 1:N Notification

Account 1:N Transaction

Category 1:N Transaction

Receipt 1:1 Transaction

---

# Architectural Decisions

ADR-001

Account Balance จะไม่ถูกเก็บในฐานข้อมูล

เหตุผล

* ลดปัญหาข้อมูลไม่ตรงกัน
* Transaction เป็น Source of Truth
* รองรับการแก้ไขข้อมูลย้อนหลัง
* Audit ได้ง่าย
* เหมาะกับระบบการเงิน

Balance จะถูกคำนวณจาก Transaction ทุกครั้ง
