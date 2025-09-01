[![Sorc Lab](/SorcLabLogo_White.png)](https://sorclab.com/)

# Relational Database: Data Normalization
This article will cover the main normalization "forms": 1NF, 2NF, 3NF,
3.5NF (Boyce-Codd Normal Form or "BCNF"), 4NF, 5NF, and 6NF.

Data normalization best practices are defined in tiers known as "Normal Forms". Each Normal Form (NF)
contains rules to achieve that form, and the next tier/form includes more rules, and also include the
rules of the form before it. For example, if you comply with 2NF, that means you also much comply
with 1NF, etc.

Most practical examples of a real world database only cover up to 3NF, and the rest are more theoretical, or apply to a
"data warehouse", which will be covered here. For most cases, you can stop after 3NF to get a good
idea of how your data relations should work.

The following are examples of what each form requires and how they can be violated.


## 1NF: First Normal Form
Rule: 
- No repeating groups (no arrays, lists, or multiple values in a single cell).
- Every column must hold atomic (indivisible) values.
- Each row is unique.

❌ Violation of 1NF:
```
Table: Orders
+------------+-------------------+-----------------------+
| OrderID    | CustomerName      | Products              |
+------------+-------------------+-----------------------+
| 101        | Alice             | Apple, Banana, Orange |
| 102        | Bob               | Milk, Bread           |
+------------+-------------------+-----------------------+
```
Problem: The `Products` column stores a list of items in a single field.

✅ Correct 1NF:
```
Table: Orders
+------------+-------------------+-----------+
| OrderID    | CustomerName      | Product   |
+------------+-------------------+-----------+
| 101        | Alice             | Apple     |
| 101        | Alice             | Banana    |
| 101        | Alice             | Orange    |
| 102        | Bob               | Milk      |
| 102        | Bob               | Bread     |
+------------+-------------------+-----------+
```
Fix: Each row has **one product per record** (atomic values).


## 2NF: Second Normal Form
Rule: 
- Must already satisfy 1NF.
- No **partial dependency**: non-key attributes cannot depend on just part of a **composite primary key**.
(If the primary key is a single column, you’re already in 2NF.)

❌ Violation of 2NF:
```
Table: OrderDetails
Composite Key = (OrderID, ProductID)

+------------+-----------+-------------+--------------+
| OrderID    | ProductID | ProductName | CustomerName |
+------------+-----------+-------------+--------------+
| 101        | P01       | Apple       | Alice        |
| 101        | P02       | Banana      | Alice        |
| 102        | P03       | Milk        | Bob          |
+------------+-----------+-------------+--------------+
```

Problem:
- `ProductName` depends only on `ProductID` (not the whole key).
- `CustomerName` depends only on `OrderID` (not the whole key).
- Both are **partial dependencies** → breaks 2NF.

✅ Correct 2NF: \
Split into separate tables:
```
Orders
+------------+--------------+
| OrderID    | CustomerName |
+------------+--------------+
| 101        | Alice        |
| 102        | Bob          |
+------------+--------------+

Products
+-----------+-------------+
| ProductID | ProductName |
+-----------+-------------+
| P01       | Apple       |
| P02       | Banana      |
| P03       | Milk        |
+-----------+-------------+

OrderDetails
+------------+-----------+
| OrderID    | ProductID |
+------------+-----------+
| 101        | P01       |
| 101        | P02       |
| 102        | P03       |
+------------+-----------+
```
Fix: Each non-key attribute depends on the **whole key, not part of it**.


## 3NF: Third Normal Form
Rule:
- Must already satisfy 2NF.
- No **transitive dependency**: non-key attributes must depend only on the key, not on another
non-key attribute.

❌ Violation of 3NF:
```
Table: Customers
Primary Key = CustomerID

+------------+--------------+-------------+----------------+
| CustomerID | CustomerName | ZipCode     | City           |
+------------+--------------+-------------+----------------+
| C01        | Alice        | 12345       | New York       |
| C02        | Bob          | 54321       | Los Angeles    |
+------------+--------------+-------------+----------------+
```
Problem:
- `City` depends on `ZipCode`, not directly on `CustomerID`.
- This is a **transitive dependency**.

✅ Correct 3NF:
```
Customers
+------------+--------------+-------------+
| CustomerID | CustomerName | ZipCode     |
+------------+--------------+-------------+
| C01        | Alice        | 12345       |
| C02        | Bob          | 54321       |
+------------+--------------+-------------+

ZipCodes
+-------------+----------------+
| ZipCode     | City           |
+-------------+----------------+
| 12345       | New York       |
| 54321       | Los Angeles    |
+-------------+----------------+
```
Fix: Now `City` depends on `ZipCode`, and `ZipCode` is properly factored into its own table.

## Summary: 1NF, 2NF, 3NF
- **1NF**: No lists inside cells.
- **2NF**: If the primary key has multiple columns, every non-key column must depend on *all* of
them.
- **3NF**: No column should depend on another non-key column.


## What is a Dependency?
If you are not familiar with the terms "dependency" or "transitive dependency", this section might
help.

Using a previous example with `ZipCode` inside `Customers` table:
```
Table: Customers
Primary Key = CustomerID

+------------+--------------+-------------+----------------+
| CustomerID | CustomerName | ZipCode     | City           |
+------------+--------------+-------------+----------------+
| C01        | Alice        | 12345       | New York       |
| C02        | Bob          | 54321       | Los Angeles    |
+------------+--------------+-------------+----------------+
```

When we say **"X depends on Y"**, it means:
- If I know the value of **Y**, I can determine the value of **X**.
- Formally: **Y** → **X** (Y functionally determines X).

### Example: City depends on ZipCode
- ZipCode `12345` → "New York"
- ZipCode `54321` → "Los Angeles"

So whenever you know a `ZipCode`, you can uniquely determine the `City`.
That's why we say:
```
ZipCode → City
```
This is a **transitive dependency** if `ZipCode` is not a primary key in the `Customers` table but
is instead just another attribute.

### Why it matters
If you store City inside a Customers table:
- **Update anomaly**: If the USPS renames “Los Angeles” to “LA” and you have 10,000 customers in
that zip code, you must update 10,000 rows. If you miss one, your data is inconsistent.
- **Insert anomaly**: If you want to add a new zip code before you have a customer there, you
can’t — you have to wait for a customer row.
- **Delete anomaly**: If you delete the last customer in a zip code, you lose the fact that that
zip code existed at all.

By splitting `ZipCode` → `City` into a separate table, those anomalies disappear.  
The Answer: `ZipCodes` table:
```
ZipCodes
+-------------+----------------+
| ZipCode     | City           |
+-------------+----------------+
| 12345       | New York       |
| 54321       | Los Angeles    |
+-------------+----------------+
```

### Another Way to Look at It
A `City` depends on a `ZipCode`, not the other way around. This is because a `ZipCode` uniquely
identifies a `City`. If we tried to use the `City` as a key we would quickly realize that a `City`
can have multiple `ZipCode` associated with it. Thus, a `City` cannot uniquely identify a `ZipCode`:
```
Los Angeles → 90001, 90002, 90003, ...
```

Instead, have `City` *depend* on `ZipCode` to uniquely identify a `City`:
- If I know the value of `ZipCode`, I can determine the value of `City`.
- `ZipCode` → `City` (ZipCode **functionally determines** City).

## 3.5NF: Boyce-Codd Normal Form (BCNF)
- Sometimes treated as "3.5NF".
- Slightly stricter than 3NF: for every non-trivial functional dependency X → Y, X must be a superkey.
- Handles some edge cases that 3NF allows.

**Rule**: For every non-trivial functional dependency X → Y, X must be a superkey.
**Why it exists**: Sometimes 3NF still allows anomalies.

❌ **BCNF Violation**
```
Table: Courses
+-----------+----------+----------+
| CourseID  | Teacher  | Room     |
+-----------+----------+----------+
| MATH101   | Alice    | R1       |
| CS102     | Bob      | R2       |
| ENG201    | Alice    | R1       |
+-----------+----------+----------+
```
Dependencies:
- `CourseID → Teacher, Room` ✅ (CourseID is PK)
- But also: `Teacher → Room` (each teacher has a fixed room) ❌

Here, `Teacher` is not a key, but it determines `Room`.

✅ BCNF Fix \
Split into two tables:
```
Courses(CourseID, Teacher)
TeacherRooms(Teacher, Room)
```


## 4NF: Fourth Normal Form
Rule: No **multi-valued dependencies** (MVDs).

Example violation:
```
StudentCourseClub
+----------+---------+---------+
| Student  | Course  | Club    |
+----------+---------+---------+
| Alice    | Math    | Drama   |
| Alice    | Math    | Chess   |
| Alice    | History | Drama   |
| Alice    | History | Chess   |
+----------+---------+---------+
```
Alice independently takes multiple courses and joins multiple clubs. \
This creates a **Cartesian explosion**.

Fix (4NF): Split into two tables:
```
StudentCourse(Student, Course)
StudentClub(Student, Club)
```

**Rule**: No multi-valued dependencies (MVDs).
- A multi-valued dependency happens when two attributes are **independent lists** associated with
the same key.

❌ **4NF Violation**
```
Table: StudentInterests
+---------+----------+----------+
| Student | Sport    | Hobby    |
+---------+----------+----------+
| Alice   | Soccer   | Chess    |
| Alice   | Soccer   | Painting |
| Alice   | Tennis   | Chess    |
| Alice   | Tennis   | Painting |
+---------+----------+----------+
```
- Alice plays multiple sports and has multiple hobbies.
- Sports and Hobbies are independent attributes, but they cause a Cartesian explosion.

✅ **4NF Fix**
Split into two tables:
```
StudentSports(Student, Sport)
StudentHobbies(Student, Hobby)
```


## 5NF: Fifth Normal Form, aka Project-Join Normal Form (PJ/NF)
- Rule: No **join dependencies** (table should not be reconstructable only through multiple joins
that introduce redundancy).
- Example: Usually arises in complex many-to-many relationships, like product suppliers and
component assemblies.
- Pretty rare in practice unless you’re modeling something highly combinatorial.

**Rule**: No join dependencies — a table should not be reconstructable only by joining multiple
tables in ways that cause redundancy.

- Typically arises in **complex many-to-many relationships**.

❌ **5NF Violation**

Imagine we track which **Suppliers** can provide which **Products** for which **Projects**:
```
SupplierProductProject
+----------+---------+---------+
| Supplier | Product | Project |
+----------+---------+---------+
| S1       | P1      | J1      |
| S1       | P2      | J1      |
| S1       | P1      | J2      |
| S1       | P2      | J2      |
+----------+---------+---------+
```

Here:
- Supplier S1 supplies P1 and P2.
- Both P1 and P2 are needed in both J1 and J2.
- But the table redundantly stores every combination.

✅ **5NF Fix**

Split into three separate relationships:
```
SupplierProduct(Supplier, Product)
ProductProject(Product, Project)
SupplierProject(Supplier, Project)
```
Now we can reconstruct the full relation by joins, without redundancy.


## 6NF: Sixth Normal Form
- Rule: Every join dependency is trivial.
- Tables are decomposed into the **finest possible granularity** (often just two columns).
- Example: Time-variant databases (data warehouse, temporal DBs).
- Used when you need extreme precision for versioning or temporal data, e.g. regulatory compliance.
- Almost never used in ordinary OLTP systems.

**Rule**: Every join dependency is trivial; tables are decomposed to the smallest possible
granularity (often 2 columns).
- Common in **temporal databases** (time-varying facts).

❌ **6NF Violation**
```
Table: EmployeeSalary
+---------+-----------+-------------+------------------+
| EmpID   | Salary    | Dept        | ValidFrom        |
+---------+-----------+-------------+------------------+
| E1      | 60000     | IT          | 2023-01-01       |
| E1      | 65000     | IT          | 2024-01-01       |
| E1      | 65000     | HR          | 2024-01-01       |
+---------+-----------+-------------+------------------+
```
- Salary and Department are independent facts, but tied together with time.
- If salary changes but department doesn’t, we still repeat data.

✅ **6NF Fix**

Break into separate tables by fact type:
```
EmployeeDept(EmpID, Dept, ValidFrom)
EmployeeSalary(EmpID, Salary, ValidFrom)
```
Now each table tracks one independent time-varying attribute.


## Summary: 3.5NF/BCNF, 4NF, 5NF, 6NF
- **BCNF**: Teacher → Room (non-key determines non-key).
- **4NF**: Student → Sport & Hobby (independent multi-valued facts in one table).
- **5NF**: Supplier-Product-Project triple redundancy.
- **6NF**: Time-varying attributes jammed into one table.


- Most real-world DBs aim for **3NF/BCNF**.
- **4NF/5NF** only matter in niche cases (multi-valued lists, complex many-to-many).
- **6NF** is almost never used outside temporal data warehouses or compliance systems.


## How necessary is it to check all the boxes?
- For business apps, websites, games, etc. → 3NF/BCNF is almost always enough.
- Going further can make queries slower and schema harder to understand.
- In practice, DBAs often denormalize intentionally for performance (esp. in OLAP / reporting).
- Think of normalization as a toolbox: use the level that balances data integrity with performance.
