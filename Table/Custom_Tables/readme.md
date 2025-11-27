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

### ANSARIStudentTable - Field Definition

| Field Name         | Label           | Type        | EDT / Extended Data Type | Mandatory | Description                        |
|--------------------|-----------------|-------------|---------------------------|-----------|------------------------------------|
| **StudentId**      | Student ID      | String (20) | Name / String20          | Yes       | Primary key of student             |
| **StudentName**    | Student Name    | String (60) | Name / String60          | Yes       | Full name of student               |
| **Age**            | Age             | Integer     | ---                      | No        | Age of student                     |
| **Email**          | Email Address   | String (80) | Email                    | No        | Student Email                      |
| **Phone**          | Phone Number    | String (20) | Phone                    | No        | Contact number                     |
| **IsActive**       | Active          | NoYes Enum  | NoYesId                  | Yes       | Whether student is active          |
| **EnrollmentDate** | Enrollment Date | Date        | TransDate                | No        | Date when student joined           |
| **CourseCode**     | Course Code     | String (20) | String20                 | No        | Course Code of the selected course |

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
