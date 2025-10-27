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



