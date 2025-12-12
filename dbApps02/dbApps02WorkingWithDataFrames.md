---
marp: true
theme: default
class: invert
paginate: true
---

# Lesson 02: Working with DataFrames
## Preparing for SQL

## Database Applications Development
### Software Engineering | Medina County Career Center

---

## Learning Objectives

By the end of this lesson, you will be able to:

- Select specific columns from a DataFrame
- Filter DataFrame rows using boolean conditions
- Sort data by one or more columns
- Calculate basic statistics on filtered data
- Understand how DataFrame operations map to SQL queries

**Today's focus:** Learn to manipulate data in pandas, next lesson we'll do the same operations in SQL.

---

## Review: Where We Are

**Lesson 01 - You learned:**
- JupyterLab and code cells
- Variables and data types
- Loading CSV files with pandas
- Basic exploration: `.head()`, `.tail()`, `.info()`

**Today - Lesson 02:**
Learn to ask questions of your data using pandas

**Next - Lesson 03:**
Move that data into SQLite and ask the same questions using SQL

---

## Why This Lesson Matters

**Understanding the CONCEPT** of these operations in pandas makes SQL easier:

| Today (Pandas / Python)   | Next Lesson (SQL)            |
|---------------------------|------------------------------|
| `df[["name", "age"]]`     | `SELECT` statement           |
| `df[df["age"] > 30]`      | `WHERE` clause               |
| `df.sort_values("age")`   | `ORDER BY` clause            |
| `df["age"].mean()`        | `AVG()`, `COUNT()`, `SUM()`  |


Today we are going to learn what questions to ask using Python syntax... and next we'll learn how to ask them in database language.

---

## Quick Setup

Let's load our Titanic dataset:

```python
import pandas as pd

titanic = pd.read_csv('Titanic Dataset.csv')

# Quick check
print(titanic.shape)    # (rows, columns)
print(titanic.head())   # First few rows
```

Make sure you have this working before we continue!

---

## Part 1: Selecting Columns

**Goal:** Get specific columns from your data (like asking "show me just the names and ages").

### Select a Single Column

```python
# Get one column (returns a Series - like a single column)
names = titanic['name']
print(names.head())  # Gives you first five records/rows of names
```

**Database Connection:** This is like SQL syntax: `SELECT name FROM titanic`

---

## Select Multiple Columns

Use **double brackets** with a list of column names:

```python
# Select multiple columns (returns a DataFrame)
subset = titanic[['name', 'age', 'sex', 'survived']]
print(subset.head())

# Note the double brackets: [[ ]]
# Outer brackets = "select from titanic"
# Inner brackets = list of columns
```

**Database Connection:** This is like `SELECT name, age, sex FROM titanic`

---

## Column Selection Practice

```python
# Just passenger info
passenger_info = titanic[['name', 'sex', 'age']]

# Just survival data
survival_data = titanic[['name', 'survived', 'pclass']]

# You can then work with these smaller DataFrames
print(passenger_info.info())
print(survival_data.describe())
```

### Why Column Selection Matters:
- A smaller DataFrame makes all later operations (filtering, sorting, grouping) faster  
- Reduces clutter so you can focus only on the columns that matter  
---

## Part 2: Filtering Rows

**Goal:** Get specific rows that meet a condition (like "show me only passengers who survived").

### Boolean Indexing

```python
# Create a condition (True/False for each row)
survived_condition = titanic['survived'] == 1

# Use condition to filter
survivors = titanic[survived_condition]

# Or combine in one line
survivors = titanic[titanic['survived'] == 1]

print(f"Total passengers: {len(titanic)}")
print(f"Survivors: {len(survivors)}")
```

**Database Connection:** This is like `SELECT * FROM passengers WHERE survived = 1`

---

## How Boolean Filtering Works

```python
# Step 1: Create a True/False mask
mask = titanic['age'] > 30
print(mask.head())  # Shows True, False, True, False, etc.

# Step 2: Use mask to filter
older_passengers = titanic[mask]

# The DataFrame keeps only rows where mask is True
print(older_passengers[['name', 'age']].head())
```

**Think of it like:** Going through each row and asking "does this row match my condition?"

---

## Comparison Operators

You can use these operators for filtering:

| Operator | Meaning | Example |
|----------|---------|---------|
| `==` | Equal to | `titanic['sex'] == 'female'` |
| `!=` | Not equal to | `titanic['pclass'] != 3` |
| `>` | Greater than | `titanic['age'] > 30` |
| `<` | Less than | `titanic['fare'] < 10` |
| `>=` | Greater or equal | `titanic['age'] >= 18` |
| `<=` | Less or equal | `titanic['age'] <= 12` |

---

## Filtering Examples

```python
# First class passengers
first_class = titanic[titanic['pclass'] == 1]

# Female passengers
females = titanic[titanic['sex'] == 'female']

# Children (age 12 or under)
children = titanic[titanic['age'] <= 12]

# Passengers who paid more than $50
expensive_tickets = titanic[titanic['fare'] > 50]

# For each filter, you can check how many rows matched
print(f"First class passengers: {len(first_class)}")
print(f"Female passengers: {len(females)}")
```


---

# BREAK TIME

---

## Multiple Conditions

Use `&` (AND) and `|` (OR) to combine conditions. **Must use parentheses!**

```python
# AND: Both conditions must be true
female_survivors = titanic[
    (titanic['sex'] == 'female') & 
    (titanic['survived'] == 1)
]
```
---

# OR: At least one condition must be true

```python
upper_class = titanic[
    (titanic['pclass'] == 1) | 
    (titanic['pclass'] == 2)
]
```

---

# Three conditions

```python
young_first_survivors = titanic[
    (titanic['age'] < 18) & 
    (titanic['pclass'] == 1) & 
    (titanic['survived'] == 1)
]
```

---

## Important: Parentheses Required!

```python
# WRONG - Will cause error
result = titanic[titanic['age'] > 30 & titanic['sex'] == 'female']

# CORRECT - Parentheses around EACH condition
result = titanic[(titanic['age'] > 30) & (titanic['sex'] == 'female')]
```

**Remember:**
- `&` means AND (both must be true)
- `|` means OR (at least one must be true)
- Each condition needs `( )` around it

**Database Connection:** This is like `WHERE age > 30 AND sex = 'female'`

---

## Part 3: Sorting Data

Sort data by one or more columns using `.sort_values()`.

```python
# Sort by age (youngest first)
sorted_by_age = titanic.sort_values('age')
print(sorted_by_age[['name', 'age']].head())

# Sort by age (oldest first)
sorted_by_age_desc = titanic.sort_values('age', ascending=False)
print(sorted_by_age_desc[['name', 'age']].head())

# Sort by multiple columns
sorted_multiple = titanic.sort_values(['pclass', 'fare'], 
                                       ascending=[True, False])
# First by class (low to high), then by fare (high to low)
```

**Database Connection:** This is like `ORDER BY age` or `ORDER BY age DESC`

---

## Sorting Examples

```python
# Who were the youngest passengers?
youngest = titanic.sort_values('age')[['name', 'age']].head(10)

# Who paid the highest fares?
highest_fares = titanic.sort_values('fare', ascending=False)[['name', 'fare']].head(10)

# Organize by class, then by fare within each class
organized = titanic.sort_values(['pclass', 'fare'], 
                                 ascending=[True, False])
print(organized[['name', 'pclass', 'fare']].head(20))
```

---

## Part 4: Calculating Statistics

Perform calculations on columns or filtered data.

```python
# Basic statistics on a column
print("Average age:", titanic['age'].mean())
print("Minimum fare:", titanic['fare'].min())
print("Maximum fare:", titanic['fare'].max())
print("Total passengers:", len(titanic))

# Count non-missing values
print("Passengers with known age:", titanic['age'].count())
```

---

## Statistics on Filtered Data

The real power: calculate statistics on subsets of your data.

```python
# Average age of survivors
survivors = titanic[titanic['survived'] == 1]
print("Average survivor age:", survivors['age'].mean())

# Average fare by class
first_class = titanic[titanic['pclass'] == 1]
print("Average first class fare:", first_class['fare'].mean())

third_class = titanic[titanic['pclass'] == 3]
print("Average third class fare:", third_class['fare'].mean())

# Survival rate (percentage)
survival_rate = titanic['survived'].mean() * 100
print(f"Survival rate: {survival_rate:.1f}%")
```

---

## Counting with value_counts()

Count how many times each value appears in a column.

```python
# How many passengers in each class?
print(titanic['pclass'].value_counts())
# Output:
# 3    709
# 1    323
# 2    277

# Gender distribution
print(titanic['sex'].value_counts())
# Survival counts
print(titanic['survived'].value_counts())
# 0 = did not survive
# 1 = survived
```

**Database Connection:** This is like `SELECT pclass, COUNT(*) FROM titanic GROUP BY pclass`

---
# Survival rate for each class

```python
survival_by_class = titanic.groupby('pclass')['survived'].mean() * 100
print(survival_by_class)
```

---

## Combining Operations

You can combine filtering, selecting, and sorting:

```python
# Get female survivors, show name/age/class, sort by age
result = titanic[
    (titanic['sex'] == 'female') & 
    (titanic['survived'] == 1)
][['name', 'age', 'pclass']].sort_values('age')

print(result.head(10))

# Breaking it down:
# 1. Filter: female survivors
# 2. Select: name, age, pclass columns
# 3. Sort: by age
# 4. Display: first 10 rows
```

This is powerful! You're asking complex questions of your data.

---

## Common Patterns You'll Use

```python
# Pattern 1: Filter → Count
num_first_class = len(titanic[titanic['pclass'] == 1])

# Pattern 2: Filter → Calculate statistic
avg_survivor_age = titanic[titanic['survived'] == 1]['age'].mean()

# Pattern 3: Filter → Select → Sort → Display
titanic[titanic['survived'] == 1][['name', 'age']].sort_values('age').head()

# Pattern 4: Value distribution
titanic['pclass'].value_counts()

# Pattern 5: Overall statistics
titanic['fare'].describe()
```

These patterns answer most data questions you'll have!

---

## The Pandas → SQL Connection

Everything you learned today has a SQL equivalent:

**Select columns:**
- Pandas: `titanic[['name', 'age']]`
- SQL: `SELECT name, age FROM passengers`

**Filter rows:**
- Pandas: `titanic[titanic['age'] > 30]`
- SQL: `SELECT * FROM passengers WHERE age > 30`

**Sort:**
- Pandas: `titanic.sort_values('age')`
- SQL: `SELECT * FROM passengers ORDER BY age`

**Next lesson:** We'll move this data into a database and write these as SQL queries!

---

## Best Practices

**1. Check your results**
```python
survivors = titanic[titanic['survived'] == 1]
print(f"Found {len(survivors)} survivors")
print(survivors['survived'].unique())  # Should only show [1]
```

**2. Use descriptive variable names**
```python
# Good
female_survivors = titanic[(titanic['sex'] == 'female') & (titanic['survived'] == 1)]

# Bad
df2 = titanic[(titanic['sex'] == 'female') & (titanic['survived'] == 1)]
```

---

## Common Errors to Avoid

**1. Forgetting parentheses with multiple conditions**
```python
# ❌ WRONG: Missing parentheses. Python gets confused about the order of operations.
#           The '&' must go BETWEEN two complete conditions.
titanic[titanic['age'] > 30 & titanic['sex'] == 'female']

# ✔ RIGHT: Each condition is wrapped in parentheses, then combined with '&'.
titanic[(titanic['age'] > 30) & (titanic['sex'] == 'female')]
```

---
## Common Errors to Avoid

**2. Using == for assignment instead of filtering**
```python
# ❌ WRONG: This *modifies* the whole column and sets everyone to 1
titanic['survived'] = 1

# ✔ RIGHT: This *filters* the DataFrame to survivors
survivors = titanic[titanic['survived'] == 1]

```

---

## Lesson 02 Task Overview

**What You'll Do:**

**Track A:** Practice column selection, basic filtering (single conditions), sorting, and calculating statistics. Answer analysis questions about the Titanic dataset.

**Track B:** Everything in Track A PLUS multiple condition filtering, complex operations, and deeper statistical analysis.

**Submission:**
- Complete `dbAppsTask02TrackA.ipynb` OR `dbAppsTask02TrackB.ipynb`
- Push to your `databaseApplications` repository on GitHub
- Due: Next class

---

## Key Takeaways

✅ **Select columns** to focus on specific data
✅ **Filter rows** using boolean conditions to find what you're looking for
✅ **Sort data** to organize information meaningfully
✅ **Calculate statistics** to understand patterns in your data
✅ These pandas operations directly map to SQL queries

**Next Lesson:** We'll convert this DataFrame to a SQLite database and learn to write SQL queries that do the exact same operations!

---

## Questions?

Before you start the task:

- Column selection unclear?
- Questions about filtering conditions?
- Boolean operators (`&`, `|`) confusing?
- Need help understanding the pandas → SQL connection?

**Let's make sure you're ready to work with data!**
