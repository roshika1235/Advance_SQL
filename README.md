
# 📦 Advanced SQL Order Processing System

## 📌 Overview

This project represents a structured **Order Processing System database** designed to handle core business operations such as:

* Customer management
* Product catalog maintenance
* Inventory tracking
* Order processing
* Payment handling

The system is built using relational database principles with proper relationships to ensure data consistency and integrity.

---

## 🗂️ Database Schema

### 1️⃣ CUSTOMERS

Stores customer details such as:

* Customer ID (Primary Key)
* Name
* Email
* Phone number

---

### 2️⃣ PRODUCTS

Maintains product-related information:

* Product ID (Primary Key)
* Product name
* Price

---

### 3️⃣ INVENTORY

Tracks stock availability for each product:

* Inventory ID (Primary Key)
* Product ID (Foreign Key)
* Quantity on hand
* Last updated date

---

### 4️⃣ ORDERS

Stores order-level information:

* Order ID (Primary Key)
* Customer ID (Foreign Key)
* Order date

---

### 5️⃣ ORDER_ITEMS

Contains details of products within each order:

* Order Item ID (Primary Key)
* Order ID (Foreign Key)
* Product ID (Foreign Key)
* Quantity

---

### 6️⃣ PAYMENTS

Records payment transactions:

* Payment ID (Primary Key)
* Order ID (Foreign Key)
* Amount
* Payment method

---

## 🔗 Relationships

* One customer can place multiple orders
* One order can contain multiple items
* Each item is linked to a product
* Each product has inventory tracking
* Each order is associated with a payment

---

## 🧪 Data Description

Each table contains **at least 10 records** to simulate realistic scenarios, including:

* Multiple customers placing different orders
* Products with varying prices
* Inventory updates over time
* Orders containing multiple products
* Payments made using different methods

---

## ⚙️ Key Operations Covered

### ✅ Basic Operations

* Viewing customer and product data
* Listing all orders

### 🔍 Intermediate Operations

* Combining data from multiple tables
* Calculating order totals
* Monitoring stock levels

### 🚀 Advanced Operations

* Identifying top customers based on spending
* Finding best-selling products
* Detecting orders without payments
* Identifying low inventory items
* Generating sales summaries

---

## 🎯 Learning Outcomes

This project helps in:

* Understanding relational database design
* Practicing complex query logic
* Managing data relationships using keys
* Applying real-world business scenarios

---

## 📌 Execution Guidelines

1. Create all tables based on the schema
2. Insert data into primary tables first
3. Then insert data into dependent tables
4. Ensure relationships are maintained correctly
5. Run queries to validate results

---

## 📎 Important Notes

* Maintain proper order while inserting data to avoid constraint errors
* Ensure that referenced records exist before inserting dependent records
* Validate data consistency across tables

---

## 👨‍💻 Author

Roshika Challa

---

## ⭐ Contribution

You can enhance this project by:

* Adding more advanced queries
* Introducing automation features
* Extending the schema

---

## 🚀 Future Enhancements

* Shipment tracking system
* Discount and offers module
* Reporting dashboards
* Integration with front-end applications

---
