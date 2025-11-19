# Frequency and Cross Tables in R - Interactive Learning Nugget

## 📊 Introduction to Frequency and Cross Tables

> **Learning Objectives:**  
> By the end of this learning nugget, you will be able to:
> - Create frequency tables for single variables
> - Construct cross tables (contingency tables) for two or more variables
> - Calculate relative frequencies and percentages
> - Choose appropriate table types for your data
> - Interpret relationships between categorical variables
> - Export and format tables for reporting

---

## What are Frequency and Cross Tables?

{{1}}
**Frequency Tables** (also called frequency distributions) show how often each value or category appears in your dataset. They answer: *"How many observations fall into each category?"*

{{2}}
**Cross Tables** (also called contingency tables or crosstabs) display the relationship between two or more categorical variables. They answer: *"How are categories of one variable distributed across categories of another?"*

{{3}}
> **Important:** Before working with frequency and cross tables, you should:
> - Have completed the **"Descriptive Statistics"** learning nugget
> - Understand your variable types (nominal, ordinal)
> - Check data quality and missing values

---

## 🔧 Setting Up Your R Environment


### Step 1: Load Required Packages

```r
# Install packages (only needed once)
install.packages("gmodels")
install.packages("descr")
install.packages("janitor")

# Load packages (needed every session)
library(gmodels)   # For CrossTable function
library(descr)     # For crosstab function
library(janitor)   # For tabyl function
```


### Step 2: Load Your Data

```r
# Option 1: Using built-in dataset (for practice)
data(mtcars)
df <- mtcars

# Convert numeric to factors for categorical analysis
df$cyl <- factor(df$cyl, levels = c(4, 6, 8),
                 labels = c("4 cyl", "6 cyl", "8 cyl"))
df$am <- factor(df$am, levels = c(0, 1),
                labels = c("Automatic", "Manual"))
df$vs <- factor(df$vs, levels = c(0, 1),
                labels = c("V-shaped", "Straight"))

# Option 2: Load your own CSV file
# df <- read.csv("your_data.csv")

# Check structure
str(df)
```

---

## 📈 Frequency Tables for Single Variables


### Absolute Frequencies

Absolute frequency shows the count of observations in each category.

```r
# Simple frequency table
table(df$cyl)

# Store in object
freq_cyl <- table(df$cyl)
freq_cyl

# More detailed output
summary(df$cyl)
```

**Example Output:**
```
4 cyl  6 cyl  8 cyl 
   11      7     14
```


**When to use absolute frequencies:**
- ✅ When you need exact counts
- ✅ Nominal or ordinal categorical data
- ✅ To see sample sizes per category
- ✅ Before calculating proportions or percentages
- ✅ Quality control and data validation


### Relative Frequencies (Proportions)

Relative frequency shows the proportion of observations in each category (0 to 1).

```r
# Calculate relative frequencies
prop.table(table(df$cyl))

# With more decimal places
round(prop.table(table(df$cyl)), 3)

# Store in object
rel_freq_cyl <- prop.table(table(df$cyl))
rel_freq_cyl
```

**Example Output:**
```
  4 cyl   6 cyl   8 cyl 
  0.344   0.219   0.438
```


**When to use relative frequencies:**
- ✅ Comparing distributions across different sample sizes
- ✅ When proportions are more meaningful than counts
- ✅ Statistical calculations (probabilities)
- ✅ Academic reporting (often preferred)


### Percentages

Percentages show the proportion multiplied by 100.

```r
# Calculate percentages
prop.table(table(df$cyl)) * 100

# Rounded to 2 decimal places
round(prop.table(table(df$cyl)) * 100, 2)

# Store in object
perc_cyl <- round(prop.table(table(df$cyl)) * 100, 2)
perc_cyl
```

**Example Output:**
```
4 cyl  6 cyl  8 cyl 
34.38  21.88  43.75
```


**When to use percentages:**
- ✅ General audience or non-technical reports
- ✅ Easier interpretation than proportions
- ✅ Visual presentations (charts, infographics)
- ✅ Business reports and dashboards


### Comprehensive Frequency Table

Combine all information in one table:

```r
# Create comprehensive frequency table
freq_table <- data.frame(
  Category = names(table(df$cyl)),
  Frequency = as.vector(table(df$cyl)),
  Relative = round(as.vector(prop.table(table(df$cyl))), 3),
  Percentage = round(as.vector(prop.table(table(df$cyl))) * 100, 2),
  Cumulative_Freq = cumsum(as.vector(table(df$cyl))),
  Cumulative_Perc = round(cumsum(as.vector(prop.table(table(df$cyl)))) * 100, 2)
)

freq_table
```

**Example Output:**
```
  Category  Frequency  Relative  Percentage  Cumulative_Freq  Cumulative_Perc
1   4 cyl         11     0.344       34.38               11            34.38
2   6 cyl          7     0.219       21.88               18            56.25
3   8 cyl         14     0.438       43.75               32           100.00
```


**Interpreting Comprehensive Tables:**
- **Frequency:** Raw counts
- **Relative:** Proportion (sum = 1.0)
- **Percentage:** Proportion × 100 (sum = 100%)
- **Cumulative Frequency:** Running total of counts
- **Cumulative Percentage:** Running total of percentages (useful for ordinal data)

---

## 🔀 Cross Tables (Contingency Tables)


### Basic Cross Table (2×2 and Beyond)

Cross tables show the relationship between two categorical variables.

```r
# Simple cross table: cylinders by transmission
table(df$cyl, df$am)

# With row and column names
crosstab <- table(df$cyl, df$am)
crosstab

# Add margins (row and column totals)
addmargins(crosstab)
```

**Example Output (without margins):**
```
        Automatic  Manual
4 cyl           3       8
6 cyl           4       3
8 cyl          12       2
```

**Example Output (with margins):**
```
        Automatic  Manual  Sum
4 cyl           3       8   11
6 cyl           4       3    7
8 cyl          12       2   14
Sum            19      13   32
```


**When to use cross tables:**
- ✅ Examining relationship between two categorical variables
- ✅ Before conducting Chi-square test
- ✅ Comparing group distributions
- ✅ Survey data analysis
- ✅ Exploring associations between factors


### Cross Table with Row Percentages

Show how categories of the **column variable** are distributed within each **row**.

```r
# Row percentages
prop.table(table(df$cyl, df$am), margin = 1) * 100

# Rounded
round(prop.table(table(df$cyl, df$am), margin = 1) * 100, 2)

# Example interpretation:
# "Of cars with 4 cylinders, X% are manual and Y% are automatic"
```

**Example Output:**
```
        Automatic  Manual
4 cyl       27.27   72.73     (each row sums to 100%)
6 cyl       57.14   42.86
8 cyl       85.71   14.29
```

**Reading this table:**
- Of 4-cylinder cars, 27.27% are automatic and 72.73% are manual
- Of 6-cylinder cars, 57.14% are automatic and 42.86% are manual
- Of 8-cylinder cars, 85.71% are automatic and 14.29% are manual


**When to use row percentages:**
- ✅ When the **row variable** is your grouping variable (independent variable)
- ✅ To compare how the column variable is distributed across row categories
- ✅ Example: "How does transmission type vary by cylinder count?"
- ✅ Reading: "Of [row category], X% are [column category]"


### Cross Table with Column Percentages

Show how categories of the **row variable** are distributed within each **column**.

```r
# Column percentages
prop.table(table(df$cyl, df$am), margin = 2) * 100

# Rounded
round(prop.table(table(df$cyl, df$am), margin = 2) * 100, 2)

# Example interpretation:
# "Of manual cars, X% have 4 cylinders, Y% have 6 cylinders, Z% have 8 cylinders"
```

**Example Output:**
```
        Automatic  Manual
4 cyl       15.79   61.54     (each column sums to 100%)
6 cyl       21.05   23.08
8 cyl       63.16   15.38
```

**Reading this table:**
- Of automatic cars, 15.79% have 4 cylinders, 21.05% have 6 cylinders, 63.16% have 8 cylinders
- Of manual cars, 61.54% have 4 cylinders, 23.08% have 6 cylinders, 15.38% have 8 cylinders


**When to use column percentages:**
- ✅ When the **column variable** is your grouping variable (independent variable)
- ✅ To compare how the row variable is distributed across column categories
- ✅ Example: "How does cylinder count vary by transmission type?"
- ✅ Reading: "Of [column category], X% are [row category]"


### Cross Table with Total Percentages

Show each cell as a percentage of the **grand total**.

```r
# Total percentages (of all observations)
prop.table(table(df$cyl, df$am)) * 100

# Rounded
round(prop.table(table(df$cyl, df$am)) * 100, 2)

# Example interpretation:
# "X% of all cars are 4-cylinder manuals"
```

**Example Output:**
```
        Automatic  Manual
4 cyl        9.38   25.00     (entire table sums to 100%)
6 cyl       12.50    9.38
8 cyl       37.50    6.25
```

**Reading this table:**
- 9.38% of all cars are 4-cylinder automatic
- 25.00% of all cars are 4-cylinder manual
- 37.50% of all cars are 8-cylinder automatic
- Most common combination: 8-cylinder automatic (37.50%)


**When to use total percentages:**
- ✅ To see the overall distribution across both variables
- ✅ When both variables are equally important
- ✅ To identify the most common combinations
- ✅ Marketing segmentation analysis

---

## 🎯 Decision Guide: Which Percentage to Use?


### Quick Reference

| Research Question | Use This | Margin | Example |
|:------------------|:---------|:-------|:--------|
| "How does Y vary by X?" | Row % | margin = 1 | "How does transmission vary by cylinder count?" |
| "How does X vary by Y?" | Column % | margin = 2 | "How does cylinder count vary by transmission?" |
| "What's the overall distribution?" | Total % | No margin | "What % of all cars are 4-cyl manual?" |
| "Which combination is most common?" | Total % | No margin | "What's the most common cyl-transmission combo?" |


### Critical Rule

> **The percentages should add up to 100% in the direction you're analyzing:**
> - Row percentages: Each **row** sums to 100%
> - Column percentages: Each **column** sums to 100%
> - Total percentages: The **entire table** sums to 100%


### Visual Decision Tree

```ascii
        What is your independent variable?
                    |
        ____________|____________
       |                         |
   Row Variable            Column Variable
       |                         |
   Use ROW %               Use COLUMN %
   (margin = 1)            (margin = 2)
       
   
   No clear independent variable?
            |
        Use TOTAL %
```

---

## 📝 Interpreting Cross Tables


### What to Look For

When analyzing cross tables, examine:

1. **Marginal distributions:** Row and column totals
2. **Cell frequencies:** Are any cells very small (n < 5)?
3. **Patterns:** Do percentages differ across categories?
4. **Expected vs. observed:** Do distributions look independent?


### Example Interpretation

```r
# Create example table
CrossTable(df$cyl, df$am,
           prop.r = TRUE,
           prop.c = FALSE,
           prop.t = FALSE,
           prop.chisq = FALSE,
           chisq = FALSE)
```

**Example Output (simplified):**
```
   Cell Contents
|-------------------------|
|                       N |
|           N / Row Total |
|-------------------------|

             | df$am 
      df$cyl | Automatic |    Manual | Row Total | 
-------------|-----------|-----------|-----------|
      4 cyl  |         3 |         8 |        11 | 
             |     0.273 |     0.727 |           | 
-------------|-----------|-----------|-----------|
      6 cyl  |         4 |         3 |         7 | 
             |     0.571 |     0.429 |           | 
-------------|-----------|-----------|-----------|
      8 cyl  |        12 |         2 |        14 | 
             |     0.857 |     0.143 |           | 
-------------|-----------|-----------|-----------|
Column Total |        19 |        13 |        32 | 
-------------|-----------|-----------|-----------|
```

**Sample interpretation:**
> "A cross tabulation of cylinder count and transmission type shows that 4-cylinder cars are predominantly manual (72.7%, n = 8 of 11), while 8-cylinder cars are predominantly automatic (85.7%, n = 12 of 14). Six-cylinder cars show a more balanced distribution (57.1% automatic, n = 4; 42.9% manual, n = 3)."


### Red Flags in Cross Tables

- ⚠️ **Small cell counts:** Cells with n < 5 (problematic for Chi-square test)
- ⚠️ **Empty cells:** Categories with zero observations
- ⚠️ **Unbalanced design:** Very different group sizes
- ⚠️ **Too many categories:** Makes interpretation difficult

---

## 💾 Exporting Tables


### Save as CSV

```r
# Save frequency table
write.csv(freq_table, "frequency_table_yoursurname.csv", 
          row.names = FALSE)

# Save cross table
crosstab_df <- as.data.frame.matrix(crosstab)
write.csv(crosstab_df, "crosstab_yoursurname.csv")
```


### Create Publication-Ready Table

```r
# Using janitor for clean output
library(janitor)

final_table <- df %>%
  tabyl(cyl, am) %>%
  adorn_totals(c("row", "col")) %>%
  adorn_percentages("row") %>%
  adorn_pct_formatting(digits = 1) %>%
  adorn_ns(position = "front") %>%
  adorn_title("combined")

# Save
write.csv(final_table, "publication_table_yoursurname.csv")
```

---

## 📊 Visualization of Frequency Tables


### Bar Chart for Single Variable

```r
# Simple bar chart
barplot(table(df$cyl),
        main = "Distribution of Cylinder Counts",
        xlab = "Number of Cylinders",
        ylab = "Frequency",
        col = "lightblue",
        border = "black")

# Horizontal bar chart
barplot(table(df$cyl), horiz = TRUE,
        main = "Distribution of Cylinder Counts",
        xlab = "Frequency",
        ylab = "Number of Cylinders",
        col = "lightgreen")
```


### Grouped Bar Chart for Cross Tables

```r
# Side-by-side bars
barplot(table(df$cyl, df$am),
        beside = TRUE,
        legend = TRUE,
        main = "Transmission Type by Cylinder Count",
        xlab = "Transmission Type",
        ylab = "Frequency",
        col = c("red", "blue", "green"),
        args.legend = list(title = "Cylinders"))

# Stacked bars
barplot(table(df$cyl, df$am),
        beside = FALSE,
        legend = TRUE,
        main = "Transmission Type by Cylinder Count (Stacked)",
        xlab = "Transmission Type",
        ylab = "Frequency",
        col = c("red", "blue", "green"))
```


### Mosaic Plot for Cross Tables

```r
# Mosaic plot shows proportional relationships
mosaicplot(table(df$cyl, df$am),
           main = "Relationship: Cylinders vs Transmission",
           xlab = "Number of Cylinders",
           ylab = "Transmission Type",
           color = TRUE)

# The size of each rectangle represents frequency
```

---

## 🎯 Common Mistakes to Avoid


### Mistake 1: Wrong Percentage Direction

```r
# WRONG: Using column % when you should use row %
# If cylinders is your grouping variable, use row %
prop.table(table(df$cyl, df$am), margin = 2)  # Wrong!

# CORRECT:
prop.table(table(df$cyl, df$am), margin = 1)  # Right!
```


### Mistake 2: Not Checking for Missing Values

```r
# WRONG: Ignoring NAs
table(df$variable_with_na)

# CORRECT: Include NAs or handle explicitly
table(df$variable_with_na, useNA = "always")  # Shows NAs
table(df$variable_with_na, useNA = "ifany")   # Shows NAs if present
```


### Mistake 3: Too Many Decimal Places

```r
# WRONG: Excessive precision
prop.table(table(df$cyl)) * 100
# Result: 34.37500 34.37500 31.25000

# CORRECT: Round appropriately
round(prop.table(table(df$cyl)) * 100, 1)
# Result: 34.4 34.4 31.2
```


### Mistake 4: Forgetting Totals

```r
# WRONG: No context for interpretation
table(df$cyl, df$am)

# CORRECT: Include margins
addmargins(table(df$cyl, df$am))
```

---

## 📋 Reporting Guidelines


### For Frequency Tables

> "Of the 32 vehicles analyzed, 11 (34.4%) had 4 cylinders, 7 (21.9%) had 6 cylinders, and 14 (43.8%) had 8 cylinders."


### For Cross Tables

> "A cross-tabulation of cylinder count and transmission type revealed that manual transmissions were more common among 4-cylinder vehicles (9 of 11, 81.8%), while automatic transmissions dominated 8-cylinder vehicles (12 of 14, 85.7%)."


### APA Style Table Example

```
Table 1
Frequency and Percentage of Vehicles by Cylinder Count

Cylinders    n     %
--------------------------
4           11   34.4
6            7   21.9
8           14   43.8
--------------------------
Total       32  100.0
```

---

## ✅ Summary Checklist

{{1}}
**Before Creating Tables:**
- [ ] Variables are correctly coded as factors
- [ ] Missing values are handled appropriately
- [ ] You understand what each variable represents
- [ ] You know which variable is independent/dependent

{{2}}
**When Creating Tables:**
- [ ] Choose correct percentage direction (row/column/total)
- [ ] Include totals (margins)
- [ ] Round to appropriate decimal places (1-2 for %)
- [ ] Label variables clearly
- [ ] Check for small cell counts (n < 5)

{{3}}
**For Reporting:**
- [ ] Include sample size (N)
- [ ] Report both counts and percentages
- [ ] Provide context for interpretation
- [ ] Use consistent formatting
- [ ] Consider visualization for clarity

---

## 🔗 Next Steps


After mastering frequency and cross tables, you should:

1. **Review related learning nuggets:**
   - "Descriptive Statistics" for numerical summaries
   - "Normality Check" before parametric tests

2. **Conduct hypothesis tests:**
   - Chi-square test for independence
   - Fisher's exact test (for small samples)
   - McNemar's test (for paired data)

3. **Explore measures of association:**
   - Cramér's V
   - Phi coefficient
   - Odds ratios

---

## 🔑 Key Takeaways


### The Golden Rules

1. **Match percentage to research question:**
   - Independent variable → determines margin
   - Row independent → use row %
   - Column independent → use column %

2. **Always include totals:**
   - Provides context
   - Allows verification
   - Aids interpretation

3. **Visualize when possible:**
   - Bar charts for single variables
   - Grouped/mosaic plots for relationships
   - Makes patterns visible


### Remember

> **The purpose of frequency and cross tables is to organize and summarize categorical data in a way that reveals patterns and relationships. Choose your table type and percentages based on your research question!**

---

## 💬 Questions?

Contact the teaching team:
- **Hannes Tegelbeckers:** hannes.tegelbeckers@ovgu.de
- **Office Hours:** https://cloud.ovgu.de/call/ciz2te64

**Remember:** Mastering frequency and cross tables is essential for analyzing categorical data in research!