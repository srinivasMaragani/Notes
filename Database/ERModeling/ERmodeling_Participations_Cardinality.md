**Entity-Relationship (ER) Model - Detailed Notes**

***

### 1. Introduction

The **Entity-Relationship (ER) Model** is a conceptual framework used for database design. It visually represents the structure of a database by defining entities (real-world objects), their attributes (properties), and relationships among them.

The **ER Diagram (ERD)** is the graphical representation of this model, aiding in the logical and visual design of databases before actual implementation.

***

### 2. ER Model in Database Design Process

**Steps to design a database using the ER Model:**

1.  **Requirement Analysis:** Collect data and functional requirements from stakeholders.
    
2.  **Conceptual Design:** Create the ER diagram representing the data model.
    
3.  **Logical and Physical Design:** Convert ERD into relational schemas and define indexes, storage, etc.
    

***

### 3. Components of ER Model

#### A. Entity

An **entity** is any real-world object, person, place, or concept about which data is stored.

**Examples:**

-   Person: Doctor, Student
    
-   Concept: Reservation, Membership
    
-   Thing: Book, Machine
    

#### B. Entity Set

An **entity set** is a collection of similar types of entities. For example, all students in a college form the "Student" entity set.

***

### 4. Types of Entities

#### A. Strong Entity

-   Has a **primary key** that uniquely identifies each instance.
    
-   Independent of other entities.
    
-   Represented by a **rectangle** in ERD.
    

**Example:** Employee with EmployeeID as a primary key.

#### B. Weak Entity

-   Cannot be identified uniquely without relying on a related strong entity.
    
-   No primary key on its own; relies on a **foreign key**.
    
-   Represented by a **double rectangle**.
    

**Example:** Dependent of an Employee. DependentID alone isn't unique without EmployeeID.

***

### 5. Attributes

An **attribute** describes the property of an entity.

-   Represented by an **ellipse**.
    

#### Types of Attributes:

1.  **Key Attribute:**
    
    -   Uniquely identifies an entity (e.g., RollNumber).
        
    -   Underlined in ERD.
        
2.  **Composite Attribute:**
    
    -   Comprised of multiple sub-attributes (e.g., Address = Street + City + State).
        
3.  **Multivalued Attribute:**
    
    -   Can have multiple values (e.g., PhoneNumbers).
        
    -   Represented by a **double ellipse**.
        
4.  **Derived Attribute:**
    
    -   Can be computed from other attributes (e.g., Age from DateOfBirth).
        
    -   Represented by a **dashed ellipse**.
        

***

### 6. Relationships

A **relationship** represents how two or more entities are related.

-   Represented by a **diamond** in ERD.
    

**Example:** A Student _enrolls_ in a Course.

#### Relationship Set

A **relationship set** is a collection of similar relationships.

***

### 7. Degree of Relationship

Indicates the number of entity sets involved in a relationship.

1.  **Unary (Recursive):**
    
    -   Single entity involved.
        
    -   Example: Employee supervises another Employee.
        
2.  **Binary:**
    
    -   Two entity sets involved.
        
    -   Example: Student registers for Course.
        
3.  **Ternary:**
    
    -   Three entities involved.
        
    -   Example: Doctor prescribes Medicine to Patient.
        
4.  **N-ary:**
    
    -   More than three entity sets.
        
    -   Rare in practice; usually broken into binary.
        

***

### 8. Cardinality Constraints

Describes how many instances of one entity relate to instances of another.

1.  **One-to-One (1:1):**
    
    -   One entity is related to exactly one entity of another set.
        
    -   Example: Person has one Passport.
        
2.  **One-to-Many (1:N):**
    
    -   One entity relates to many entities of another set.
        
    -   Example: Department has many Employees.
        
3.  **Many-to-One (N:1):**
    
    -   Many entities relate to one entity.
        
    -   Example: Many Employees work in one Department.
        
4.  **Many-to-Many (M:N):**
    
    -   Many entities relate to many entities.
        
    -   Example: Students enrolled in many Courses.
        

***

### 9. Participation Constraints

Defines whether all or only some entities participate in a relationship.

1.  **Total Participation:**
    
    -   All entities in the set must participate.
        
    -   Represented with **double lines**.
        
    -   Example: Every Student must enroll in at least one Course.
        
2.  **Partial Participation:**
    
    -   Some entities may not participate.
        
    -   Represented with **single lines**.
        
    -   Example: Some Professors may not guide any Project.
        

***

### 10. How to Draw an ER Diagram

1.  **Identify Entities:** Represent them using rectangles.
    
2.  **Identify Relationships:** Use diamonds and connect to entities.
    
3.  **Add Attributes:** Draw ellipses connected to respective entities.
    
4.  **Mark Key Attributes:** Underline unique identifiers.
    
5.  **Normalize:** Avoid redundant entities or relationships.
    
6.  **Review:** Ensure clarity and completeness.
    

***