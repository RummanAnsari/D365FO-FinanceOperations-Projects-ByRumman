# ANSARIStudentTable – Custom Table (D365 F&O)

A well‑formatted Markdown structure for creating your custom table and using it later to generate a Data Entity.

---

## 🧩 Table Name  
**ANSARIStudentTable**

---

## 🗂 Table Description  
A simple custom table created for learning purposes.  
Includes primary key, index, and basic fields that work well for Entities.

---

## 📌 Table Structure

| Field Name      | Label            | Data Type      | Extended Data Type (EDT) | Mandatory | Description |
|-----------------|------------------|----------------|---------------------------|-----------|-------------|
| StudentId       | Student Id       | Int64          | RefRecId                  | Yes       | Primary Key |
| StudentName     | Student Name     | String         | Name                      | Yes       | Student full name |
| Age             | Age              | Int32          | Age                       | No        | Age of the student |
| Email           | Email            | String         | Email                     | No        | Email address |
| JoiningDate     | Joining Date     | Date           | TransDate                 | No        | Date of joining |
| IsActive        | Active Status    | Enum           | No EDT (Use custom enum)  | Yes       | Whether student is active |

---

## 🔑 Indexes

### **1. StudentIdx (Primary Index)**
- Field: **StudentId**
- AllowDuplicates: **No**

---

## 🧱 Example Table X++ Code (for reference)

```x++
public final class ANSARIStudentTable extends Common
{
    StudentId       StudentId;
    StudentName     StudentName;
    Age             Age;
    Email           Email;
    JoiningDate     JoiningDate;
    IsActive        IsActive;
}
```

---

## ✔ Ready to Use for Data Entity Creation

This table can now be used to create:
- **Public / Standard Data Entity**
- **Composite Entity**
- **OData Integration**
- **DMF Import/Export**

---

## 📁 File Information
- **Format:** Markdown (.md)
- **Purpose:** Documentation + teaching + reference for D365 FO custom table
