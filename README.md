
# 🏥 HealthcareDB: SQL-Based Analytics and Insights

> A structured relational database system to manage, analyze, and derive insights from healthcare data using MySQL.

---

## 📌 Project Overview

**HealthcareDB** is a SQL project designed to simulate a real-world hospital management and analytics system. It leverages normalized tables and advanced SQL queries to extract actionable insights on hospital performance, patient demographics, treatment costs, and more.

---

## 🎯 Objectives

- Design a normalized relational database to store healthcare records efficiently.
- Ensure referential integrity between patients, hospitals, admissions, and treatments.
- Use SQL queries to analyze:
  - Hospital performance metrics.
  - Treatment frequency and cost.
  - Patient demographics and admission patterns.
- Support decision-making through views, aggregation functions, subqueries, and window functions.

---

## 🛠️ Database Schema

The project creates and uses a database named `HealthcareDB` with the following tables:

### 1. `Patients`
Stores demographic data.
```sql
PatientID (PK), FullName, Age, Gender, Address
```

### 2. `Hospitals`
Stores hospital details.
```sql
HospitalID (PK), HospitalName, Location, Capacity
```

### 3. `Admissions`
Records patient admissions.
```sql
AdmissionID (PK), PatientID (FK), HospitalID (FK), AdmissionDate, DischargeDate, ReasonForAdmission
```

### 4. `Treatments`
Details of treatments per admission.
```sql
TreatmentID (PK), AdmissionID (FK), ProcedureName, Cost, Outcome
```

---

## 📊 Analytical SQL Queries

### 1. **Patient Demographics**
```sql
SELECT Gender, COUNT(*) AS Total_Patients, ROUND(AVG(Age), 2) AS Avg_Age
FROM Patients
GROUP BY Gender;
```

### 2. **Top Hospitals by Admissions**
```sql
SELECT HospitalName, COUNT(PatientID) AS Total_Patients
FROM Hospitals JOIN Admissions USING (HospitalID)
GROUP BY HospitalName;
```

### 3. **Treatment Cost per Hospital**
```sql
SELECT h.HospitalName, SUM(t.Cost) AS Total_Treatment_Cost
FROM Hospitals h
JOIN Admissions a ON h.HospitalID = a.HospitalID
JOIN Treatments t ON a.AdmissionID = t.AdmissionID
GROUP BY h.HospitalName;
```

### 4. **Average Length of Stay**
```sql
SELECT HospitalName, ROUND(AVG(DATEDIFF(DischargeDate, AdmissionDate)),1) AS Avg_Stay
FROM Hospitals JOIN Admissions USING (HospitalID)
GROUP BY HospitalName;
```

### 5. **Patients Staying > 7 Days**
```sql
SELECT FullName, DATEDIFF(DischargeDate, AdmissionDate) AS Stay_Duration
FROM Patients JOIN Admissions USING (PatientID)
WHERE DATEDIFF(DischargeDate, AdmissionDate) > 7;
```

### 6. **High-Frequency Treatments (>5 times)**
```sql
SELECT HospitalName, ProcedureName, COUNT(*) AS Frequency
FROM Treatments
JOIN Admissions USING (AdmissionID)
JOIN Hospitals USING (HospitalID)
GROUP BY HospitalName, ProcedureName
HAVING COUNT(*) > 5;
```

### 7. **Patients Admitted for 'Surgery' or 'Therapy'**
```sql
SELECT FullName, AdmissionDate, DischargeDate, ReasonForAdmission
FROM Patients JOIN Admissions USING (PatientID)
WHERE ReasonForAdmission IN ('Surgery', 'Therapy');
```

### 8. **Hospital with Highest Average Treatment Cost**
```sql
SELECT HospitalName, ROUND(AVG(Cost), 1) AS Avg_Treatment_Cost
FROM Hospitals
JOIN Admissions USING (HospitalID)
JOIN Treatments USING (AdmissionID)
GROUP BY HospitalName
ORDER BY Avg_Treatment_Cost DESC
LIMIT 1;
```

---

## 👁️‍🗨️ Views and Window Functions

### 📌 View: `Hospital_Performance`
```sql
CREATE VIEW Hospital_Performance AS
SELECT 
    h.HospitalName,
    COUNT(a.AdmissionID) AS Total_Admissions,
    ROUND(AVG(DATEDIFF(a.DischargeDate, a.AdmissionDate)), 2) AS Avg_Stay,
    SUM(t.Cost) AS Total_Revenue
FROM Admissions a
JOIN Treatments t ON a.AdmissionID = t.AdmissionID
JOIN Hospitals h ON h.HospitalID = a.HospitalID
WHERE a.DischargeDate IS NOT NULL
GROUP BY h.HospitalName;
```

### 📌 Rank Hospitals by Revenue
```sql
SELECT 
    HospitalName,
    SUM(Cost) AS Total_Revenue,
    RANK() OVER (ORDER BY SUM(Cost) DESC) AS Revenue_Rank
FROM Hospitals
JOIN Admissions USING (HospitalID)
JOIN Treatments USING (AdmissionID)
GROUP BY HospitalName;
```

### 📌 Rank Treatments by Frequency
```sql
SELECT 
    ProcedureName,
    COUNT(*) AS Frequency,
    DENSE_RANK() OVER (ORDER BY COUNT(*) DESC) AS Frequency_Rank
FROM Treatments
GROUP BY ProcedureName;
```

---

## 📈 Sample Insights

- Gender-wise patient distribution with average ages.
- Revenue leaders among hospitals.
- High-frequency procedures across facilities.
- Insights on prolonged hospital stays and average durations.

---

## 👨‍💻 Developed By

**Tejas Phalke**  
Final Year B.E. Student  
SQL Analytics & IoT-Based Solutions Enthusiast

---

## ✅ Usage

1. Run the script in `HealthcareDB Data.sql` to create and populate the database.
2. Execute the queries above using any MySQL client.
3. Modify filters or GROUP BY clauses to tailor your analysis.

---

## 📁 Files Included

- `HealthcareDB Data.sql` – Full SQL script to build and populate the database.
- `SQL_PROJECT_PPT.pdf` – Presentation summarizing objectives and key query insights.

---

## 📬 Feedback & Suggestions

Open to feedback, pull requests, or collaborations on extending this project with:
- Stored procedures
- Triggers
- More complex reporting (e.g., monthly trends, bed utilization)
