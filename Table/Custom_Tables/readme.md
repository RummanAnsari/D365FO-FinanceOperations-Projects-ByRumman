# ANSARIStudentTable -- Custom Table Design (D365 F&O)

A simple and practical table structure that can later be used to
generate a Data Entity.

## 📌 Table Name: `ANSARIStudentTable`

## 📘 Table Structure

  -------------------------------------------------------------------------------------
  Field Name       Label        Type      EDT / Extended   Mandatory   Description
                                          Data Type                    
  ---------------- ------------ --------- ---------------- ----------- ----------------
  StudentId        Student ID   String    Name / String20  Yes         Primary key of
                                (20)                                   student

  StudentName      Student Name String    Name / String60  Yes         Full name of
                                (60)                                   student

  Age              Age          Integer   ANSARIAge oe Empty No          Age of student

  Email            Email        String    Email            No          Student email
                   Address      (80)                                   

  Phone            Phone Number String    Phone            No          Contact number
                                (20)                                   

  IsActive         Active       NoYes     NoYesId          Yes         Whether student
                                Enum                                   is active

  EnrollmentDate   Enrollment   Date      TransDate        No          Date student
                   Date                                                joined

  CourseCode       Course Code  String    String20         No          Course code
                                (20)                                   
  -------------------------------------------------------------------------------------

## 🔧 Technical Notes

-   Primary Index: `StudentId`
-   Create RecId index: Yes
-   Table Type: Standard
-   Cache Lookup: Found
-   CreatedDateTime, ModifiedDateTime: Yes

## 🛠 Required Custom EDT (for Age field)

-   EDT Name: `ANSARIAge`
-   Label: Age
-   Base Type: Integer
