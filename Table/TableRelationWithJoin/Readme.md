Perfect 👌 — let’s create a **custom example** in **D365 F&O–style (TTD format)** using your prefix `Ansari` so you can clearly understand how **table relations** are defined in the **AOT (Application Object Tree)** and in **metadata (Table Designer)**.

---

## 🧩 Example: Relation Between Two Custom Tables

We’ll build a **one-to-many** relationship between:

* **`AnsariCustomerTable`** → Parent Table
* **`AnsariSalesOrderTable`** → Child Table

---

## **1️⃣ AnsariCustomerTable**

### 📘 Table Fields

| Field Name   | Type   | Extended Data Type | Description                 |
| ------------ | ------ | ------------------ | --------------------------- |
| CustomerId   | String | CustAccount        | Unique ID for each customer |
| CustomerName | String | Name               | Full name of customer       |
| City         | String | AddressCity        | City of customer            |

---

### 🧩 Table Properties (TTD Style)

```
Name: AnsariCustomerTable
PrimaryIndex: CustomerIdIdx
ClusteredIndex: CustomerIdIdx
TitleField1: CustomerName
Label: Customer Master Table
```

---

### 📑 Index

```
CustomerIdIdx
    Fields: CustomerId
    AllowDuplicates: No
```

---

## **2️⃣ AnsariSalesOrderTable**

### 📘 Table Fields

| Field Name   | Type   | Extended Data Type | Description                                |
| ------------ | ------ | ------------------ | ------------------------------------------ |
| SalesOrderId | String | SalesId            | Unique sales order number                  |
| CustomerId   | String | CustAccount        | Foreign key linking to AnsariCustomerTable |
| OrderDate    | Date   | TransDate          | Order creation date                        |
| TotalAmount  | Real   | AmountCur          | Order total amount                         |

---

### 🧩 Table Properties (TTD Style)

```
Name: AnsariSalesOrderTable
PrimaryIndex: SalesOrderIdIdx
ClusteredIndex: SalesOrderIdIdx
Label: Sales Order Table
```

---

### 📑 Index

```
SalesOrderIdIdx
    Fields: SalesOrderId
    AllowDuplicates: No
```

---

## **🔗 Table Relation Definition (in TTD format)**

In **AnsariSalesOrderTable → Relations node**:

```
Relation: AnsariCustomerRelation
    RelatedTable: AnsariCustomerTable
    RelationshipType: Association
    Cardinality: ExactlyOne
    RelatedField: CustomerId
    Field: CustomerId
    CreateNavigationPropertyMethods: Yes
    UseDefaultRoleNames: Yes
    Validate: Yes
    OnDelete: Restricted
```

---

### 📊 Meaning of Each Property

| Property                            | Description                                                                           |
| ----------------------------------- | ------------------------------------------------------------------------------------- |
| **RelatedTable**                    | The parent table that contains the referenced primary key.                            |
| **Field**                           | The field in the child table that acts as the foreign key.                            |
| **RelatedField**                    | The corresponding field in the parent table.                                          |
| **Cardinality**                     | Defines how many records are related. `ExactlyOne` = One-to-many relationship.        |
| **OnDelete**                        | Defines what happens when a parent record is deleted (Restricted / Cascade / Ignore). |
| **CreateNavigationPropertyMethods** | If Yes → system generates navigation methods automatically.                           |
| **Validate**                        | If Yes → system validates foreign key existence on insert/update.                     |

---

### 🧮 Example Data

**AnsariCustomerTable**

| CustomerId | CustomerName  | City   |
| ---------- | ------------- | ------ |
| C001       | Rumman Ansari | Mumbai |
| C002       | Ayesha Khan   | Delhi  |

**AnsariSalesOrderTable**

| SalesOrderId | CustomerId | OrderDate  | TotalAmount |
| ------------ | ---------- | ---------- | ----------- |
| SO001        | C001       | 2025-10-22 | 5000        |
| SO002        | C002       | 2025-10-23 | 3000        |
| SO003        | C001       | 2025-10-24 | 2000        |

---

### 🔍 Join Example in X++

```x++
AnsariCustomerTable cust;
AnsariSalesOrderTable sales;

while select sales
    exists join cust
    where cust.CustomerId == sales.CustomerId
{
    info(strFmt("Order %1 belongs to %2 from %3", 
        sales.SalesOrderId, 
        cust.CustomerName, 
        cust.City));
}
```

🧠 **Output:**

```
Order SO001 belongs to Rumman Ansari from Mumbai
Order SO002 belongs to Ayesha Khan from Delhi
Order SO003 belongs to Rumman Ansari from Mumbai
```

---

## 🧾  Insert Data into CustomerTable
```
INSERT INTO AnsariCustomerTable (CustomerId, CustomerName, City)
VALUES 
('C001', 'Rumman Ansari', 'Mumbai'),
('C002', 'Osman Ali Sk', 'Delhi'),
('C003', 'Inzamamul Hoque', 'Pune');

```

### Verify Data ✅ View Parent Table:
```
SELECT * FROM AnsariCustomerTable;
```

 # 🧾  Insert Data into AnsariSalesOrderTable
```
INSERT INTO AnsariSalesOrderTable (SalesOrderId, CustomerId, OrderDate, TotalAmount)
VALUES 
('SO001', 'C001', '2025-10-20', 5000),
('SO002', 'C002', '2025-10-21', 3000),
('SO003', 'C001', '2025-10-22', 2500);
```
###  ✅ View Child Table:
```
SELECT * FROM AnsariSalesOrderTable;
```
### Output for above data

#### Table browser: AnsariSalesOrderTable
<img width="1236" height="664" alt="image" src="https://github.com/user-attachments/assets/3719d856-c116-44ea-883e-2d33b0826f27" />

#### Table browser: AnsariCustomerTable
<img width="1236" height="664" alt="image" src="https://github.com/user-attachments/assets/a6193d4a-3eed-4db0-9fd5-7b8c575974e2" />

#### After Join Output 
<img width="1236" height="664" alt="image" src="https://github.com/user-attachments/assets/60aa46a8-d5d3-4dfe-bfde-9a3d8ca67558" />


## You can Add more data
### 🧾 Insert Data into AnsariCustomerTable

```
INSERT INTO AnsariCustomerTable (CustomerId, CustomerName, City)
VALUES 
('C004', 'Sara Sheikh', 'Hyderabad'),
('C005', 'Ahmed Raza', 'Kolkata'),
('C006', 'Mehnaz Fatima', 'Chennai'),
('C007', 'Zaid Khan', 'Lucknow'),
('C008', 'Nida Qureshi', 'Bangalore');

```
### 🧾 Insert Data into AnsariSalesOrderTable
```
INSERT INTO AnsariSalesOrderTable (SalesOrderId, CustomerId, OrderDate, TotalAmount)
VALUES 
('SO010', 'C004', '2025-10-25', 4800),
('SO011', 'C005', '2025-10-26', 7200),
('SO012', 'C006', '2025-10-27', 2500),
('SO013', 'C004', '2025-10-28', 6000),
('SO014', 'C008', '2025-10-29', 8200),
('SO015', 'C007', '2025-10-30', 3100);

```
### Output for above data

#### Table browser: AnsariSalesOrderTable

<img width="1236" height="664" alt="image" src="https://github.com/user-attachments/assets/790bc9a3-620d-48c0-9e90-cb62cfe32dce" />

#### Table browser: AnsariCustomerTable

#### After Join Output 
