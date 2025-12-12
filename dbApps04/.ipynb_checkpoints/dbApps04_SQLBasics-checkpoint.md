---
marp: true
theme: default
class: invert
paginate: true
---

# Lesson 04: SQL Basics
## SELECT, WHERE, ORDER BY

## Database Applications Development
### Software Engineering | Medina County Career Center

---

## Learning Objectives

By the end of this lesson, you will be able to:

- Write SELECT statements to retrieve specific columns
- Use WHERE clause to filter data
- Apply ORDER BY to sort results
- Use LIMIT to control result size
- Combine multiple SQL clauses in one query
- Translate pandas operations to SQL equivalents

**You already know these concepts from Lesson 02 and 03!** Today you will continue exploring the SQL syntax.

---

## Where We Are

**Lesson 02:** You learned data manipulation in pandas
- Select columns, filter rows, sort data

**Lesson 03:** You created your first database
- Converted CSV → SQLite, wrote basic `SELECT *` queries

**Today - Lesson 04:**
- Learn SQL syntax for everything you did in pandas
- SELECT specific columns, WHERE filtering, ORDER BY sorting

**Next - Lesson 05:**
- Database design and structure

---

## The Pandas → SQL Bridge

**Remember this from Lesson 02?**

```python
# Pandas: Get names and ages of passengers over 30
result = titanic[titanic['Age'] > 30][['Name', 'Age']]
```

**Today you'll learn the SQL equivalent:**

```sql
-- SQL: Same operation
SELECT Name, Age 
FROM passengers 
WHERE Age > 30;
```

**Same concept, different syntax!**

---

## SQL Query Structure

**Basic anatomy of a SQL query:**

```sql
SELECT column1, column2     -- What columns to show
FROM table_name             -- Which table to use
WHERE condition             -- Filter rows (optional)
ORDER BY column             -- Sort results (optional)
LIMIT number;               -- Limit rows returned (optional)
```

**Order matters!** SQL expects clauses in this order:
1. SELECT
2. FROM  
3. WHERE
4. ORDER BY
5. LIMIT

---

## Part 1: SELECT - Choosing Columns

**Get all columns:**
```sql
SELECT * FROM passengers;
```

**Get specific columns:**
```sql
SELECT Name, Age, Sex FROM passengers;
```

**Pandas equivalent:**
```python
titanic[['Name', 'Age', 'Sex']]
```

**Key points:**
- `*` means "all columns"
- List specific columns with commas
- No quotes needed around column names in SQL

---

## SELECT Examples

```sql
-- Just names
SELECT Name FROM passengers;

-- Name and age
SELECT Name, Age FROM passengers;

-- Multiple columns
SELECT PassengerId, Name, Age, Sex, Survived 
FROM passengers;
```

**Question:** How would you select just Fare and Pclass?

---

## Part 2: WHERE - Filtering Rows

**Basic WHERE clause:**
```sql
SELECT * FROM passengers
WHERE Age > 30;
```

**Pandas equivalent:**
```python
titanic[titanic['Age'] > 30]
```

**Key points:**
- WHERE filters which rows to show
- Comes AFTER FROM
- Uses comparison operators: `=`, `>`, `<`, `>=`, `<=`, `!=`

---

## WHERE with Different Conditions

**Numeric comparisons:**
```sql
WHERE Age > 30
WHERE Fare <= 10
WHERE Pclass = 1
```

**Text comparisons (need single quotes!):**
```sql
WHERE Sex = 'male'
WHERE Name = 'Smith, Mr. James'
```

**Common mistake:** Forgetting quotes around text!
- ✅ `WHERE Sex = 'male'`
- ❌ `WHERE Sex = male`

---

## Multiple Conditions: AND

**Both conditions must be true:**

```sql
SELECT Name, Age, Sex
FROM passengers
WHERE Age > 30 AND Sex = 'female';
```

**Pandas equivalent:**
```python
titanic[(titanic['Age'] > 30) & (titanic['Sex'] == 'female')]
```

**Key points:**
- Use `AND` to combine conditions
- Both must be true to include the row

---

## Multiple Conditions: OR

**At least one condition must be true:**

```sql
SELECT Name, Pclass
FROM passengers
WHERE Pclass = 1 OR Pclass = 2;
```

**Pandas equivalent:**
```python
titanic[(titanic['Pclass'] == 1) | (titanic['Pclass'] == 2)]
```

**Key points:**
- Use `OR` to combine conditions
- Only one needs to be true

---

## Combining AND/OR with Parentheses

**Use parentheses for complex logic:**

```sql
SELECT Name, Age, Sex, Survived
FROM passengers
WHERE (Sex = 'female' OR Age < 12) 
  AND Survived = 1;
```

**Translation:** Female passengers OR children (under 12) who survived.

**Best practice:** Use parentheses to make your logic clear!

---

## Part 3: ORDER BY - Sorting Results

**Sort by one column:**
```sql
SELECT Name, Age
FROM passengers
ORDER BY Age;
```

**Pandas equivalent:**
```python
titanic[['Name', 'Age']].sort_values('Age')
```

**Key points:**
- ORDER BY sorts the results
- Default is ascending (low to high)
- Comes AFTER WHERE (if you have WHERE)

---

## ORDER BY Direction

**Ascending (default - smallest first):**
```sql
ORDER BY Age ASC
-- or just
ORDER BY Age
```

**Descending (largest first):**
```sql
ORDER BY Age DESC
```

**Examples:**
```sql
-- Youngest passengers first
SELECT Name, Age FROM passengers ORDER BY Age;

-- Highest fares first  
SELECT Name, Fare FROM passengers ORDER BY Fare DESC;
```

---

## Sort by Multiple Columns

**Primary then secondary sort:**

```sql
SELECT Name, Pclass, Fare
FROM passengers
ORDER BY Pclass, Fare DESC;
```

**Translation:** Sort by class (1, 2, 3), then within each class sort by fare (highest first).

**Pandas equivalent:**
```python
titanic[['Name', 'Pclass', 'Fare']].sort_values(['Pclass', 'Fare'], 
                                                  ascending=[True, False])
```

---

## Part 4: LIMIT - Controlling Result Size

**Get first N rows:**
```sql
SELECT * FROM passengers
LIMIT 10;
```

**Pandas equivalent:**
```python
titanic.head(10)
```

**Key points:**
- LIMIT goes at the very end
- Useful for testing queries on large datasets
- Returns first N rows after all filtering and sorting

---

## LIMIT with ORDER BY

**Combine for "Top N" queries:**

```sql
-- Oldest 5 passengers
SELECT Name, Age
FROM passengers
ORDER BY Age DESC
LIMIT 5;

-- Youngest 5 passengers
SELECT Name, Age
FROM passengers
ORDER BY Age
LIMIT 5;
```

**Order matters:** ORDER BY happens first, then LIMIT picks the first N.

---

## Putting It All Together

**Complex query using all clauses:**

```sql
SELECT Name, Age, Sex, Fare
FROM passengers
WHERE Age > 18 AND Fare < 50
ORDER BY Age DESC
LIMIT 10;
```

**Translation:**
- Show Name, Age, Sex, Fare columns
- For passengers over 18 who paid less than $50
- Sort by age (oldest first)
- Show only top 10 results

---

## Comparing Pandas vs SQL

| Pandas | SQL |
|--------|-----|
| `df[['col1', 'col2']]` | `SELECT col1, col2 FROM table` |
| `df[df['Age'] > 30]` | `WHERE Age > 30` |
| `df.sort_values('Age')` | `ORDER BY Age` |
| `df.head(10)` | `LIMIT 10` |
| `(condition1) & (condition2)` | `condition1 AND condition2` |
| `(condition1) | (condition2)` | `condition1 OR condition2` |

**You already know the left side!** Today you learned the right side.

---

## Common Mistakes to Avoid

**1. Forgetting quotes around text:**
- ❌ `WHERE Sex = male`
- ✅ `WHERE Sex = 'male'`

**2. Wrong clause order:**
- ❌ `SELECT * LIMIT 10 WHERE Age > 30`
- ✅ `SELECT * FROM passengers WHERE Age > 30 LIMIT 10`

**3. Using `=` for comparison (this is correct in SQL!):**
- Python: `==` for comparison
- SQL: `=` for comparison (one equals sign)

**4. Column names are case-sensitive in some databases:**
- Be consistent: use exact column names from your table

---

## Quick Reference: Comparison Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `=` | Equal to | `WHERE Age = 25` |
| `!=` or `<>` | Not equal | `WHERE Sex != 'male'` |
| `>` | Greater than | `WHERE Fare > 50` |
| `<` | Less than | `WHERE Age < 18` |
| `>=` | Greater or equal | `WHERE Age >= 18` |
| `<=` | Less or equal | `WHERE Age <= 12` |
| `AND` | Both conditions | `WHERE Age > 18 AND Sex = 'female'` |
| `OR` | Either condition | `WHERE Pclass = 1 OR Pclass = 2` |

---

## Useful SQL Keywords (Preview)

**You'll learn these later, but here's a preview:**

- `IN` - Match any value in a list: `WHERE Pclass IN (1, 2)`
- `BETWEEN` - Range: `WHERE Age BETWEEN 20 AND 30`
- `LIKE` - Pattern matching: `WHERE Name LIKE 'Smith%'`
- `IS NULL` - Check for missing values: `WHERE Age IS NULL`

**For now:** Stick with the basics: `=`, `>`, `<`, `AND`, `OR`

---

## Today's Practice Workflow

**You'll use:**
1. **Minimal walkthrough** - See a few example queries
2. **SQL Reference Guide** - Keep it open for syntax help
3. **Task file** - Do most of your work here!

**The task file will ask you to:**
- Write queries to answer specific questions
- Recreate Lesson 02 pandas operations in SQL
- Compare pandas code to SQL code
- Solve real-world data problems

**Track A:** Foundational queries (10-15 queries)
**Track B:** Advanced queries with multiple clauses (15-20 queries)

---

## Tips for Success

**1. Start simple, build up:**
- Get SELECT working first
- Add WHERE
- Then ORDER BY
- Finally LIMIT

**2. Test as you go:**
- Run query after each clause
- Fix errors immediately
- Don't write huge queries all at once

**3. Use the reference guide:**
- It's not cheating - professionals use references!
- Focus on understanding, not memorizing

**4. Compare to pandas:**
- If you did it in pandas, you can do it in SQL
- Think: "How did I filter/sort in Lesson 02?"

---

## Let's See Some Examples

**Open:** `dbApps04_Walkthrough.ipynb`

**You'll see:**
- 5-6 example queries with explanations
- Pandas comparison for each
- How to run queries in JupyterLab

**Then:**
- Open `dbApps04_Task_TrackA.ipynb` or `dbApps04_Task_TrackB.ipynb`
- Complete the exercises
- Submit via GitHub

---

## Key Takeaways

✅ **SELECT** chooses which columns to display
✅ **WHERE** filters which rows to include
✅ **ORDER BY** sorts the results
✅ **LIMIT** controls how many rows to return
✅ **Clause order matters:** SELECT → FROM → WHERE → ORDER BY → LIMIT
✅ **Text needs quotes:** `'male'` not `male`
✅ **You already know these concepts** - just learning SQL syntax!

---

## Questions?

Before you start the walkthrough and task:

- SELECT syntax clear?
- WHERE clause vs pandas filtering?
- ORDER BY vs sort_values()?
- Any comparison operators unclear?
- Ready to write SQL queries?
