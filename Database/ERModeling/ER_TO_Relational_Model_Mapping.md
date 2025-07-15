| Level | Model | What it Describes | Who Uses It |
| --- | --- | --- | --- |
| 1. **Conceptual** | ER Model | Real-world entities and relationships | Designers, Analysts |
| 2. **Logical** | Relational Model | Tables, attributes, keys (abstract view) | Designers, Developers |
| 3. **Physical** | Database Tables (SQL) | Actual SQL schema and storage details | DBAs, Developers |

-   ER model is for **conceptual design**,
    
-   Relational model is for **logical design**,
    
-   Physical tables in SQL are the **implementation**.

### 🔸 **Case 1: 1:1 with Total Participation (One side)**

-   **Example**: Person ↔ Passport (Passport fully participates)
    
-   ✅ Merge into **one table**
    
-   **Key**: `Per-Id` (not null)
    
-   **Optional**: `Pass-No` can be NULL (partial participation from Person)
    
-   ✅ Merge if **at least one side** has **total participation**
    

***

### 🔸 **Case 2: 1:1 with Partial Participation (Both sides)**

-   **Example**: Male ↔ Female (Marriage)
    
-   ❌ Cannot merge into one table
    
-   🔸 Use **two tables**:
    
    -   One with `M-Id` → `F-Id` (nullable)
        
    -   One with `F-Id`
        
-   ⚠️ No guaranteed non-null key to merge
    

***

### 🔸 **Case 3: n:1 (Many-to-One)**

-   **Example**: Student ↔ Elective_Course
    
-   ✅ Merge `Student` + relationship (Enrolls) into one table:
    
    -   `S-Id` → `E-Id` (foreign key)
        
-   🔸 Keep `Elective_Course` as a separate table
    
-   **Key**: `S-Id` (not repeating in Enrolls)
    

***

### 🔸 **Case 4: m:n (Many-to-Many)**

-   **Example**: Student ↔ Compulsory_Courses
    
-   ❌ Cannot merge
    
-   🔸 Use **three tables**:
    
    -   `Student` (S-Id)
        
    -   `Compulsory_Courses` (C-Id)
        
    -   `Enrolls`: with composite key `(S-Id, C-Id)`
        

***

### 🔸 **Case 5: Weak Entity**

-   **Example**: Employee ↔ Dependent (Dependent is weak entity)
    
-   ✅ Use **two tables**:
    
    -   `Employee` (E-Id)
        
    -   `Dependent` with composite key `(E-Id, D-Name)`
        
-   ⚠️ Weak entity gets key from identifying entity (total participation)
    

***

### 📌 **Quick Rules Summary**

| Relationship Type | Participation | No. of Tables | Merge Possible? |
| --- | --- | --- | --- |
| 1:1 | One side total | 1   | ✅ Yes |
| 1:1 | Both partial | 2   | ❌ No |
| n:1 | Any | 2   | ✅ Yes (merge into many-side) |
| m:n | Always many-to-many | 3   | ❌ No |
| Weak Entity | Always total (dependent) | 2   | ✅ Yes (merge relation + weak) |