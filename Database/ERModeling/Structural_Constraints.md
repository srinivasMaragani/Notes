**Structural Constraints in ER Model  -**

***

### 1. What are Structural Constraints?

Structural constraints are rules that govern how entities participate in relationships. They ensure consistency in data and model real-world requirements. Structural constraints = **Cardinality Ratio** + **Participation Constraint**.

***

### 2. Cardinality Ratio

Defines **how many instances** of one entity are associated with instances of another entity.

#### Types:

-   **1:1 (One-to-One)**: Each entity instance maps to one instance of another.
    
-   **1:N (One-to-Many)**: One entity instance maps to many instances.
    
-   **N:1 (Many-to-One)**: Many instances relate to one instance.
    
-   **M:N (Many-to-Many)**: Many instances relate to many instances.
    

**Example:**

-   A department has many employees: `Department 1:N Employee`
    
-   A person has one passport: `Person 1:1 Passport`
    

***

### 3. Participation Constraint

Defines **whether all or only some** entity instances participate in a relationship.

#### Types:

-   **Total Participation**: All entity instances **must** participate. → Drawn with **double lines**.
    
-   **Partial Participation**: Some instances **may not** participate. → Drawn with **single lines**.
    

**Example:**

-   Every employee **must** work in a department → Total
    
-   Some courses **may not** have students → Partial
    

***

### 4. Min-Max Notation

A precise way to represent both cardinality and participation using a pair `(m, n)`:

-   **m** = Minimum number of participations
    
-   **n** = Maximum number of participations
    

#### Interpretation:

| Notation | Meaning |
| --- | --- |
| `(0,1)` | Optional participation, at most once |
| `(1,1)` | Must participate exactly once (Total + 1:1) |
| `(0,N)` | Optional, can participate multiple times |
| `(1,N)` | Mandatory, must participate at least once |

**Examples:**

-   `(0,1)`: A book may or may not be borrowed by a member
    
-   `(1,N)`: An employee must work for at least one department
    

***

### 5. Real-World Scenarios

#### A. Library System

-   `Book (0, N)` ↔ `Member (0, N)` via `BORROWS`
    
-   Partial participation: Some books may never be borrowed
    

#### B. Company System

-   `Employee (1,1)` ↔ `Department (0, N)` via `WORKS_FOR`
    
-   Total participation on Employee side
    

***

### ✅ Summary Table

| Concept | Description |
| --- | --- |
| **Cardinality** | Number of times an entity participates |
| **Participation** | Whether participation is optional or required |
| **Min-Max Notation** | Exact min and max participation limits `(m, n)` |
| **Total Participation** | All instances must participate |
| **Partial Participation** | Participation is optional |

***

