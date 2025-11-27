<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>ANSARIStudentTable</title>
  <style>
    body {
      font-family: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      padding: 24px;
      background: #f7f9fb;
      color: #111827;
    }
    .card {
      background: #fff;
      border-radius: 10px;
      box-shadow: 0 6px 18px rgba(15,23,42,0.06);
      padding: 18px;
      max-width: 1100px;
      margin: 0 auto;
    }
    h1 { font-size: 20px; margin: 0 0 12px; }
    .table-wrap { overflow-x: auto; }
    table {
      width: 100%;
      border-collapse: collapse;
      min-width: 780px;
      font-size: 14px;
    }
    thead th {
      text-align: left;
      background: #0f172a;
      color: #ffffff;
      padding: 10px 12px;
      font-weight: 600;
      font-size: 13px;
      letter-spacing: 0.2px;
    }
    tbody td {
      padding: 10px 12px;
      border-bottom: 1px solid #e6eef8;
      vertical-align: top;
    }
    tbody tr:nth-child(odd) td { background: #ffffff; }
    tbody tr:nth-child(even) td { background: #fbfdff; }
    .muted { color: #6b7280; font-size: 13px; }
    @media (max-width: 700px) {
      thead th, tbody td { padding: 8px; font-size: 13px; }
    }
  </style>
</head>
<body>
  <div class="card">
    <h1>ANSARIStudentTable — Custom Table Structure (D365 F&O)</h1>
    <p class="muted">Copy this table into your documentation or web page. Use the fields when creating the table in AOT.</p>

    <div class="table-wrap">
      <table>
        <thead>
          <tr>
            <th>Field Name</th>
            <th>Label</th>
            <th>Type</th>
            <th>EDT / Extended Data Type</th>
            <th>Mandatory</th>
            <th>Description</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td><strong>StudentId</strong></td>
            <td>Student ID</td>
            <td>String (20)</td>
            <td>Name / String20</td>
            <td>Yes</td>
            <td>Primary key of student</td>
          </tr>
          <tr>
            <td><strong>StudentName</strong></td>
            <td>Student Name</td>
            <td>String (60)</td>
            <td>Name / String60</td>
            <td>Yes</td>
            <td>Full name of student</td>
          </tr>
          <tr>
            <td><strong>Age</strong></td>
            <td>Age</td>
            <td>Integer</td>
            <td>ANSARIAge</td>
            <td>No</td>
            <td>Age of student</td>
          </tr>
          <tr>
            <td><strong>Email</strong></td>
            <td>Email Address</td>
            <td>String (80)</td>
            <td>Email</td>
            <td>No</td>
            <td>Student email</td>
          </tr>
          <tr>
            <td><strong>Phone</strong></td>
            <td>Phone Number</td>
            <td>String (20)</td>
            <td>Phone</td>
            <td>No</td>
            <td>Contact number</td>
          </tr>
          <tr>
            <td><strong>IsActive</strong></td>
            <td>Active</td>
            <td>NoYes Enum</td>
            <td>NoYesId</td>
            <td>Yes</td>
            <td>Whether student is active</td>
          </tr>
          <tr>
            <td><strong>EnrollmentDate</strong></td>
            <td>Enrollment Date</td>
            <td>Date</td>
            <td>TransDate</td>
            <td>No</td>
            <td>Date student joined</td>
          </tr>
          <tr>
            <td><strong>CourseCode</strong></td>
            <td>Course Code</td>
            <td>String (20)</td>
            <td>String20</td>
            <td>No</td>
            <td>Course code</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</body>
</html>
