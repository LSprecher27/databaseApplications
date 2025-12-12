# Track B Extension Task: Titanic Survivor Research Project

## Database Applications Development - Lesson 04

---

## Overview

You've learned how to query databases to find specific information. Now you'll use those skills for a real historical investigation: finding and researching an actual Titanic passenger who shares characteristics with you or someone in your family.

**This task combines:**
- SQL querying skills
- Online research methods
- Information literacy (evaluating sources)
- Writing and reflection

---

## Part 1: Find Your Passenger Match

### Step 1: Identify Your Search Criteria

Choose **yourself** or **someone close to you** (parent, sibling, grandparent, etc.) to base your search on.

**Identify at least 3 characteristics from the Titanic dataset:**

- **Age** (approximate is fine - use a range like 25-35)
- **Sex** (male/female)
- **Passenger Class** (1st, 2nd, or 3rd)
  - Think about socioeconomic status - what class would your person likely travel in?
- **Other factors to consider:**
  - Traveling alone or with family? (SibSp, Parch columns)
  - Fare paid (higher or lower)

**Example:**
> I'm searching for passengers similar to my dad:
> - Male
> - Age 45-55
> - Would likely travel 2nd class
> - Traveling with family

---

### Step 2: Write Your SQL Query

**Requirements:**
- Use WHERE clause with multiple conditions (AND/OR)
- Filter for **survivors only** (Survived = 1)
- Use ORDER BY to sort results
- Use LIMIT to get a manageable number of results (5-15 passengers)

**Example Query Structure:**
```sql
SELECT Name, Age, Sex, Pclass, Survived, SibSp, Parch, Fare
FROM passengers
WHERE [your conditions here]
  AND Survived = 1
ORDER BY [choose relevant column]
LIMIT 10;
```

**In your notebook, include:**
1. A markdown cell explaining WHO you're searching for and WHY you chose those criteria
2. Your SQL query in a code cell
3. The results displayed

---

### Step 3: Choose Your Passenger

From your query results, **select ONE passenger** to research in depth.

**Selection tips:**
- Choose the closest match to your criteria
- Or choose someone with an interesting name that fits your base criteria
- Make sure you have enough information to research (a full name is VERY helpful!)

**In your notebook:**
- State which passenger you chose
- Explain why this person is your best match
- List their key details from the database

---

## Part 2: Research Your Passenger

### Research Requirements

**You must find information about your passenger from at least TWO legitimate sources.**

**✅ Good Sources:**
- Encyclopedia Titanica (most comprehensive Titanic database)
- Historical archives and museums
- Academic articles or books
- Newspaper archives
- Genealogy websites with primary documents
- Official Titanic survivor testimonies

**❌ NOT Allowed:**
- AI tools (ChatGPT, Claude, etc.)
- Wikipedia as a primary source (you can use it to find other sources)
- Social media posts without verification
- Websites without clear authorship or sources

---

### What to Research

Try to find out:
- **Biography:** Where were they from? What was their life like before Titanic?
- **Why were they on Titanic?** Emigrating? Visiting? Returning home?
- **Survival story:** How did they survive? Where did they go after?
- **Later life:** What happened to them after the disaster?
- **Family:** Did they travel with family? Did family survive?
- **Interesting details:** Any unique or compelling parts of their story?

**Note:** Not all passengers have extensive records. Do your best with what you can find!

---

## Part 3: Write Your Report

### Writing Requirements

Write **at least 3 paragraphs** (5-8 sentences each) in a markdown cell in your notebook.

**Paragraph 1: Introduction & Database Connection**
- Introduce your passenger (name, basic details from database)
- Explain why you searched for this type of passenger
- State what you hoped to discover

**Paragraph 2: Historical Research Findings**
- Share what you learned from your sources
- Include specific details about their life and survival
- Use quotes or specific facts from your sources
- Cite your sources properly (see format below)

**Paragraph 3: Additional Findings**
- More details from your research
- Interesting or surprising discoveries
- Connections you found between database data and historical records

**Optional Paragraph 4: Reflection** (or answer reflection questions separately)
- What stood out to you about this person's story?
- How did this research change your understanding of the Titanic disaster?
- What was challenging about finding information?

---

### Citation Format

**Use this format for your sources:**

**For websites:**
> Author Last Name, First Name (if available). "Article Title." *Website Name*, Publication Date (if available). URL. Accessed: Date.

**Example:**
> Bennett, Tom. "Margaret Brown (Molly Brown)." *Encyclopedia Titanica*, 1996. https://www.encyclopedia-titanica.org/titanic-survivor/margaret-brown.html. Accessed: December 10, 2024.

**For sources without clear author:**
> "Article Title." *Website Name*, Date. URL. Accessed: Date.

**Example:**
> "Titanic Survivors: Charles Lightoller." *Titanic Facts*, 2023. https://titanicfacts.net/charles-lightoller/. Accessed: December 10, 2024.

---

## Part 4: Reflection Questions

Answer these questions in your notebook (at least 2-3 sentences each):

1. **Database Skills:** How did using SQL help you narrow down passengers to find your match? What query challenges did you face?

2. **Research Process:** What was the most difficult part of researching your passenger? What made certain sources more reliable than others?

3. **Historical Connection:** What surprised you most about your passenger's story? How did learning about a real person change your perspective on the Titanic disaster?

4. **Data vs. Stories:** The database shows numbers and categories, but your research revealed a real person's story. How did these two types of information work together to give you a fuller picture?

5. **Information Literacy:** In an age of AI and quick answers, why is it important to find and evaluate original sources yourself?

---

## Submission Checklist

Before submitting, make sure your notebook includes:

- [ ] Markdown cell explaining WHO you're searching for and your criteria
- [ ] SQL query that filters for survivors matching your criteria
- [ ] Query results displayed
- [ ] Statement of which passenger you chose and why
- [ ] At least 3 paragraphs written about your passenger
- [ ] At least 2 legitimate sources cited properly
- [ ] Answers to reflection questions (at least 3 of the 5)
- [ ] Professional formatting with clear headers and organization

---

## Tips for Success

**SQL Querying:**
- Start broad, then narrow down if you get too many results
- Remember to include `Survived = 1`
- Use LIMIT to keep results manageable
- Test your query multiple times with different criteria

**Research Tips:**
- Start with Encyclopedia Titanica - it's the most comprehensive
- Use the passenger's full name from your query results
- Read multiple sources to verify information
- Take notes on what you find before writing
- Save your source URLs as you go!

**Writing Tips:**
- Write in your own words - no copying and pasting
- Use specific details and facts from your sources
- Make connections between the database data and what you learned
- Be honest about what you couldn't find
- Edit and proofread before submitting

---

## Example Query to Get You Started

```python
# Example: Finding survivors similar to my 16-year-old sister
query = """
SELECT Name, Age, Sex, Pclass, Survived, SibSp, Parch, Fare
FROM passengers
WHERE Age BETWEEN 14 AND 18
  AND Sex = 'female'
  AND Survived = 1
ORDER BY Age
LIMIT 10;
"""

results = pd.read_sql(query, conn)
display(results)
```

---

## Grading Criteria

This extension task will be evaluated on:

- **SQL Skills (30%):** Query correctly filters for survivors matching your criteria
- **Research Quality (30%):** Found legitimate sources with specific, relevant information
- **Writing (25%):** Clear, organized paragraphs with proper citations
- **Reflection (15%):** Thoughtful answers showing critical thinking

---

## Final Thoughts

This task connects you to real history through data and research. Every entry in the Titanic database represents a real person with a real story. By combining SQL skills with historical research, you're learning to use technology to understand human experiences.

Take your time, be thorough, and enjoy discovering your passenger's story!
