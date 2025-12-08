# Descriptive Statistics in R - Interactive Learning Nugget

## 📊 Introduction to Descriptive Statistics

> **Learning Objectives:**  

> By the end of this learning nugget, you will be able to:

> - Calculate measures of central tendency (mean, median, mode)
> - Compute measures of dispersion (variance, standard deviation, range)
> - Create frequency tables and distributions
> - Visualize data using histograms, boxplots, and density plots
> - Interpret descriptive statistics in research context
> - Choose appropriate descriptive measures for your data

---

## What is Descriptive Statistics?

{{0}}
Descriptive statistics summarize and organize characteristics of a dataset. They help us understand:

- **Central Tendency**: Where is the center of our data?
- **Dispersion**: How spread out is our data?
- **Distribution**: What shape does our data take?
- **Frequency**: How often do values occur?

{{1}}
> **Important:** Descriptive statistics describe your sample data, but do not allow you to make conclusions beyond the data you have analyzed.


--{{0}}--
!?[Video: Descriptive Statistics in R (Noor)](https://github.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggtes/blob/495d327ef2044dc95c6461b5135812c498c69527/descriptive_stats_r_Media/Noor_descriptive_stats_r_1.mp4?raw=true)

---

## 🔧 Setting Up Your R Environment


### Step 1: Create Your Project Structure

```r
# Create a new R Project
# File → New Project → New Directory → New Project
# Name it: 2025_YourSurname_Quant_Methodology

# Create a script for descriptive statistics
# File → New File → R Script
# Save it as: descriptive_stats_yoursurname.R
```


### Step 2: Install and Load Required Packages

```r
# Install packages (only needed once)
install.packages("psych")
install.packages("DescTools")
install.packages("moments")
install.packages("ggplot2")

# Load packages (needed every session)
library(psych)      # For descriptive statistics
library(DescTools)  # For additional descriptive tools
library(moments)    # For skewness and kurtosis
library(ggplot2)    # For visualization
```


### Step 3: Load Your Data

```r
# Option 1: Using built-in dataset (for practice)
data(mtcars)
df <- mtcars

# Option 2: Load your own CSV file
# df <- read.csv("your_data.csv")

# View the first few rows
head(df)

# Check structure of data
str(df)
```

---

## 📈 Measures of Central Tendency


### Mean (Arithmetic Average)

The mean is the sum of all values divided by the number of observations.

```r
# Calculate mean
mean_mpg <- mean(df$mpg)
mean_mpg

# Mean with missing values removed
mean_mpg_clean <- mean(df$mpg, na.rm = TRUE)
```


**When to use the mean:**
- ✅ Data is approximately normally distributed
- ✅ No extreme outliers present
- ✅ Interval or ratio scale data
- ✅ When you need to perform further calculations (e.g., standard deviation)
- ❌ **Avoid when:** Data is heavily skewed or contains outliers (the mean will be pulled toward extremes)


### Median (Middle Value)

The median is the middle value when data is ordered.

```r
# Calculate median
median_mpg <- median(df$mpg)
median_mpg

# Median is robust to outliers
median(c(1, 2, 3, 4, 100))  # = 3
mean(c(1, 2, 3, 4, 100))    # = 22
```


**When to use the median:**
- ✅ Data has outliers or extreme values
- ✅ Data is skewed (not symmetric)
- ✅ Ordinal scale data
- ✅ When you want a typical value that isn't affected by extremes
- ✅ Income, house prices, or other data with high-end outliers
- ❌ **Avoid when:** You need a measure that uses all data points for further analysis


### Mode (Most Frequent Value)

R doesn't have a built-in mode function, but we can create one.

```r
# Function to calculate mode
get_mode <- function(x) {
  unique_x <- unique(x)
  unique_x[which.max(tabulate(match(x, unique_x)))]
}

# Calculate mode
mode_cyl <- get_mode(df$cyl)
mode_cyl

# Alternative using table
table(df$cyl)  # Shows frequency of each value
```


**When to use the mode:**
- ✅ Nominal (categorical) data
- ✅ When you want to know the most common category
- ✅ Discrete data with repeated values
- ✅ Examples: most common shirt size, most frequent response
- ❌ **Avoid when:** Continuous data with few repeated values (may not be meaningful)

---

## 📏 Measures of Dispersion


### Range

The difference between maximum and minimum values.

```r
# Calculate range
range_mpg <- range(df$mpg)
range_mpg

# Range span
range_span <- diff(range(df$mpg))
range_span

# Or simply
max(df$mpg) - min(df$mpg)
```


**When to use the range:**
- ✅ Quick overview of data spread
- ✅ When you need to know the extremes
- ✅ Simple quality control checks
- ❌ **Avoid when:** Outliers are present (highly sensitive to extreme values)
- ❌ **Avoid when:** You need a measure that considers all data points


### Variance

Average squared deviation from the mean.

```r
# Calculate variance
var_mpg <- var(df$mpg)
var_mpg

# Manual calculation
mean_val <- mean(df$mpg)
variance_manual <- sum((df$mpg - mean_val)^2) / (length(df$mpg) - 1)
```


**When to use variance:**
- ✅ Theoretical statistical calculations
- ✅ When performing further statistical tests (ANOVA, regression)
- ✅ Comparing variability across groups (mathematically)
- ❌ **Avoid when:** You need interpretability in original units (use SD instead)
- ❌ **Avoid when:** Presenting results to non-technical audiences


### Standard Deviation

Square root of variance (in original units).

```r
# Calculate standard deviation
sd_mpg <- sd(df$mpg)
sd_mpg

# Relationship to variance
sqrt(var(df$mpg))  # Same as sd()
```


**Interpreting Standard Deviation:**
- Small SD = Data points close to mean
- Large SD = Data points spread out
- SD = 0 = All values identical


**When to use standard deviation:**
- ✅ Data is approximately normally distributed
- ✅ When reporting with the mean
- ✅ Comparing variability across different datasets
- ✅ Most common measure for reporting spread in research
- ✅ Interpretable in the original units of measurement
- ❌ **Avoid when:** Data has severe outliers (consider IQR instead)
- ❌ **Avoid when:** Data is not interval/ratio scale


### Interquartile Range (IQR)

The range of the middle 50% of data.

```r
# Calculate IQR
iqr_mpg <- IQR(df$mpg)
iqr_mpg

# Quartiles
quantile(df$mpg)

# Custom quantiles
quantile(df$mpg, probs = c(0.25, 0.50, 0.75))
```


**When to use IQR:**
- ✅ Data contains outliers
- ✅ Data is skewed
- ✅ Robust measure needed (not affected by extremes)
- ✅ When reporting with the median
- ✅ Ordinal data
- ❌ **Avoid when:** You need a measure using all data points

---

## 🎯 Decision Guide: Which Measure to Use?


### Quick Reference Table

| Data Characteristics | Central Tendency | Dispersion | Reason |
|:--------------------|:-----------------|:-----------|:-------|
| **Normal distribution, no outliers** | Mean | Standard Deviation | Uses all data, mathematically optimal |
| **Skewed distribution** | Median | IQR | Not affected by skewness |
| **Contains outliers** | Median | IQR | Robust to extreme values |
| **Ordinal data** | Median | IQR | Appropriate for ranked data |
| **Nominal/Categorical** | Mode | — | Only meaningful measure |
| **Small sample with extremes** | Median | IQR | Less influenced by extreme values |
| **For further analysis (regression, ANOVA)** | Mean | Variance/SD | Required for parametric tests |


### Decision Tree

```ascii
                    What type of data?
                           |
          _________________|_________________
         |                                   |
    Categorical                         Numerical
         |                                   |
      MODE                          Distribution shape?
                                            |
                          __________________|__________________
                         |                                     |
                  Symmetric/Normal                     Skewed/Outliers
                         |                                     |
                    MEAN + SD                           MEDIAN + IQR
```

---

## 📊 Complete Descriptive Summary


### Using Base R

```r
# Basic summary
summary(df$mpg)

# Summary for entire dataset
summary(df)
```


### Using psych Package

```r
# Comprehensive descriptive statistics
describe(df$mpg)

# For multiple variables
describe(df[, c("mpg", "hp", "wt")])

# For entire dataset
describe(df)
```


### Using DescTools Package

```r
# Detailed description
Desc(df$mpg)

# With plot
Desc(df$mpg, plotit = TRUE)
```


### Creating a Custom Summary Table

```r
# Function for complete summary
custom_summary <- function(x) {
  data.frame(
    N = length(x),
    Missing = sum(is.na(x)),
    Mean = mean(x, na.rm = TRUE),
    Median = median(x, na.rm = TRUE),
    SD = sd(x, na.rm = TRUE),
    Min = min(x, na.rm = TRUE),
    Max = max(x, na.rm = TRUE),
    Range = max(x, na.rm = TRUE) - min(x, na.rm = TRUE),
    Q1 = quantile(x, 0.25, na.rm = TRUE),
    Q3 = quantile(x, 0.75, na.rm = TRUE),
    IQR = IQR(x, na.rm = TRUE),
    Skewness = skewness(x, na.rm = TRUE),
    Kurtosis = kurtosis(x, na.rm = TRUE)
  )
}

# Apply to variable
custom_summary(df$mpg)
```

---

## 📑 Frequency Tables


### Simple Frequency Table

```r
# Frequency table
freq_table <- table(df$cyl)
freq_table

# With proportions
prop.table(freq_table)

# With percentages
prop.table(freq_table) * 100
```


**When to use frequency tables:**
- ✅ Categorical or discrete data
- ✅ To see distribution of responses
- ✅ To identify most/least common categories
- ✅ Survey data with fixed response options
- ✅ Count data (number of events)


### Enhanced Frequency Table

```r
# Create comprehensive frequency table
freq_enhanced <- data.frame(
  Frequency = as.vector(table(df$cyl)),
  Relative_Freq = as.vector(prop.table(table(df$cyl))),
  Percentage = as.vector(prop.table(table(df$cyl)) * 100),
  Cumulative = cumsum(as.vector(table(df$cyl)))
)
rownames(freq_enhanced) <- names(table(df$cyl))
freq_enhanced
```


### Frequency Table for Continuous Variables

```r
# Create bins for continuous variable
mpg_bins <- cut(df$mpg, 
                breaks = c(10, 15, 20, 25, 30, 35),
                labels = c("10-15", "15-20", "20-25", "25-30", "30-35"))

# Frequency table
table(mpg_bins)

# With proportions
prop.table(table(mpg_bins))
```

---

## 📉 Data Visualization


### Histogram

```r
# Basic histogram
hist(df$mpg,
     main = "Distribution of MPG",
     xlab = "Miles per Gallon",
     ylab = "Frequency",
     col = "lightblue",
     border = "black")

# Add mean line
abline(v = mean(df$mpg), col = "red", lwd = 2, lty = 2)

# Add median line
abline(v = median(df$mpg), col = "blue", lwd = 2, lty = 2)

# Add legend
legend("topright", 
       legend = c("Mean", "Median"),
       col = c("red", "blue"),
       lty = 2, lwd = 2)
```


**When to use histograms:**
- ✅ Continuous or discrete numerical data
- ✅ To visualize distribution shape
- ✅ To identify modality (unimodal, bimodal)
- ✅ To detect outliers or gaps
- ✅ To assess normality


### Boxplot

```r
# Basic boxplot
boxplot(df$mpg,
        main = "Boxplot of MPG",
        ylab = "Miles per Gallon",
        col = "lightgreen",
        border = "darkgreen")

# Horizontal boxplot
boxplot(df$mpg, horizontal = TRUE,
        main = "Boxplot of MPG (Horizontal)",
        xlab = "Miles per Gallon")

# Multiple boxplots
boxplot(mpg ~ cyl, data = df,
        main = "MPG by Number of Cylinders",
        xlab = "Number of Cylinders",
        ylab = "Miles per Gallon",
        col = c("red", "blue", "green"))
```


**Interpreting Boxplots:**
- Box shows IQR (25th to 75th percentile)
- Line in box is median
- Whiskers extend to 1.5 × IQR
- Points beyond whiskers are potential outliers


**When to use boxplots:**
- ✅ To identify outliers
- ✅ To compare distributions across groups
- ✅ To show median and spread simultaneously
- ✅ When you have skewed data
- ✅ To quickly assess symmetry


### Density Plot

```r
# Basic density plot
plot(density(df$mpg),
     main = "Density Plot of MPG",
     xlab = "Miles per Gallon",
     ylab = "Density",
     col = "blue",
     lwd = 2)

# Add rug plot (actual data points)
rug(df$mpg, col = "red")
```


**When to use density plots:**
- ✅ Smooth representation of distribution
- ✅ To overlay multiple distributions
- ✅ When histogram bins might be misleading
- ✅ To show the probability distribution


### Using ggplot2 (Advanced)

```r
# Histogram with ggplot2
ggplot(df, aes(x = mpg)) +
  geom_histogram(binwidth = 2, fill = "steelblue", color = "black") +
  geom_vline(aes(xintercept = mean(mpg)), 
             color = "red", linetype = "dashed", size = 1) +
  labs(title = "Distribution of MPG",
       x = "Miles per Gallon",
       y = "Frequency") +
  theme_minimal()

# Boxplot with ggplot2
ggplot(df, aes(x = factor(cyl), y = mpg, fill = factor(cyl))) +
  geom_boxplot() +
  labs(title = "MPG by Number of Cylinders",
       x = "Number of Cylinders",
       y = "Miles per Gallon") +
  theme_minimal() +
  theme(legend.position = "none")
```

---

## 🔍 Distribution Shape


### Skewness

Measure of asymmetry in distribution.

```r
# Calculate skewness
library(moments)
skewness(df$mpg)
```


**Interpreting Skewness:**
- **Skewness ≈ 0:** Symmetric distribution (normal-like)
- **Skewness > 0:** Right-skewed (positive skew, tail on right)
  - Mean > Median
  - Most values cluster on left
- **Skewness < 0:** Left-skewed (negative skew, tail on left)
  - Mean < Median
  - Most values cluster on right
- **|Skewness| > 1:** Highly skewed


**When to report skewness:**
- ✅ To assess normality assumption
- ✅ To justify use of median over mean
- ✅ When distribution shape is important
- ✅ Before parametric statistical tests


### Kurtosis

Measure of "tailedness" of distribution.

```r
# Calculate kurtosis
kurtosis(df$mpg)
```


**Interpreting Kurtosis:**
- **Kurtosis ≈ 3:** Normal distribution (mesokurtic)
- **Kurtosis > 3:** Heavy tails, sharp peak (leptokurtic)
  - More outliers than normal
- **Kurtosis < 3:** Light tails, flat peak (platykurtic)
  - Fewer outliers than normal


**When to report kurtosis:**
- ✅ To assess normality
- ✅ When outliers are a concern
- ✅ In quality control contexts
- ✅ Advanced statistical modeling


### Visual Assessment of Normality

```r
# Q-Q Plot
qqnorm(df$mpg, main = "Q-Q Plot of MPG")
qqline(df$mpg, col = "red", lwd = 2)

# If points follow the line, distribution is approximately normal
```

---

## 📝 Reporting Descriptive Statistics


### Creating a Results Table

```r
# For a single variable
results <- data.frame(
  Variable = "MPG",
  N = length(df$mpg),
  M = round(mean(df$mpg), 2),
  SD = round(sd(df$mpg), 2),
  Median = round(median(df$mpg), 2),
  Min = round(min(df$mpg), 2),
  Max = round(max(df$mpg), 2)
)
results

# For multiple variables
variables <- c("mpg", "hp", "wt")
results_multiple <- data.frame(
  Variable = variables,
  N = sapply(df[variables], length),
  M = round(sapply(df[variables], mean), 2),
  SD = round(sapply(df[variables], sd), 2),
  Median = round(sapply(df[variables], median), 2),
  Min = round(sapply(df[variables], min), 2),
  Max = round(sapply(df[variables], max), 2)
)
results_multiple
```


### Reporting Guidelines

**For normally distributed data:**
> "Miles per gallon ranged from 10.40 to 33.90 (*M* = 20.09, *SD* = 6.03, *N* = 32)."

**For skewed data:**
> "Miles per gallon ranged from 10.40 to 33.90 (*Mdn* = 19.20, *IQR* = 7.38, *N* = 32)."

**For categorical data:**
> "Of the 32 cars, 14 (43.75%) had 8 cylinders, 11 (34.38%) had 4 cylinders, and 7 (21.88%) had 6 cylinders."


### Exporting Results

```r
# Save as CSV
write.csv(results_multiple, "descriptive_results_yoursurname.csv", 
          row.names = FALSE)

# Save plot as PNG
png("histogram_yoursurname.png", width = 800, height = 600)
hist(df$mpg, main = "Distribution of MPG", 
     xlab = "Miles per Gallon", col = "lightblue")
dev.off()
```

---

## 🎯 Knowledge Check Quiz


### Question 1: Choosing Central Tendency

A dataset of household incomes shows the following: Mean = €75,000, Median = €45,000. What does this tell you?

    [( )] The data is normally distributed
    [( )] There are no outliers in the data
    [(X)] The data is right-skewed with high-income outliers
    [( )] The median is incorrect and should be recalculated
    [[?]]**Explanation:** When the mean is much higher than the median, it indicates right-skewed data with high values pulling the mean upward. In this case, a few very high incomes are pulling the mean above the median.

---


### Question 2: Interpreting Standard Deviation

Dataset A: Mean = 50, SD = 2  
Dataset B: Mean = 50, SD = 15

Which statement is correct?

    [( )] Dataset A has more variability
    [(X)] Dataset B has more variability
    [( )] Both datasets have the same spread
    [( )] Cannot determine without seeing the data
    [[?]]**Explanation:** A larger standard deviation (SD = 15) indicates greater variability. Dataset B's values are more spread out from the mean compared to Dataset A.

---


### Question 3: Appropriate Measure Selection

You're analyzing survey responses about satisfaction levels (1=Very Unsatisfied to 5=Very Satisfied). Which measures should you report?

    [( )] Mean and Standard Deviation
    [(X)] Median and IQR
    [( )] Mode only
    [( )] Range only
    [[?]]**Explanation:** Satisfaction scales are ordinal data. While some researchers use mean for Likert scales, the median and IQR are more appropriate as they don't assume equal intervals between response categories.

---


### Question 4: Interpreting IQR

A boxplot shows IQR = 10. What does this mean?

    [( )] The range of all data is 10
    [(X)] The middle 50% of data spans 10 units
    [( )] There are 10 outliers in the data
    [( )] The standard deviation is 10
    [[?]]**Explanation:** The IQR (Interquartile Range) represents the range between the 25th and 75th percentiles, capturing the middle 50% of the data.

---


### Question 5: Distribution Shape

A variable has skewness = -1.5. Which statement is true?

    [( )] The distribution is symmetric
    [( )] The mean is greater than the median
    [(X)] The distribution has a long tail on the left
    [( )] There are no outliers
    [[?]]**Explanation:** Negative skewness indicates left-skewness with a tail extending to the left. The mean will be pulled below the median by the low values in the left tail.

---


### Question 6: When to Use Mode

For which variable is the mode the MOST appropriate measure of central tendency?

    [( )] Age of participants (in years)
    [( )] Test scores (0-100)
    [(X)] Favorite color
    [( )] Height (in cm)
    [[?]]**Explanation:** The mode is most appropriate for nominal (categorical) data like favorite color. For the other variables, mean or median would be more informative.

---


### Question 7: Outlier Impact

A dataset has one extreme outlier. Which measures will be LEAST affected?

    [( )] Mean and Standard Deviation
    [(X)] Median and IQR
    [( )] Mean and Range
    [( )] All measures are equally affected
    [[?]]**Explanation:** The median and IQR are robust statistics that are not heavily influenced by outliers, unlike the mean, standard deviation, and range.

---

### Question 8: Interpreting Frequency Tables

A frequency table shows: Category A = 45%, Category B = 35%, Category C = 20%. What is the mode?

    [(X)] Category A
    [( )] Category B
    [( )] Category C
    [( )] 45%
    [[?]]**Explanation:** The mode is the most frequently occurring value or category. Category A appears most often (45% of cases), making it the mode.

---


### Question 9: Normality Assessment

Which combination suggests data is approximately normally distributed?

    [( )] Skewness = 2.5, Kurtosis = 8
    [(X)] Skewness = 0.1, Kurtosis = 3.2
    [( )] Mean much larger than median
    [( )] Large IQR with many outliers
    [[?]]**Explanation:** Normal distributions have skewness near 0 (symmetric) and kurtosis near 3. Values of 0.1 and 3.2 are very close to these benchmarks.

---


### Question 10: Reporting Statistics

You analyzed exam scores that are normally distributed. What should you report?

    [( )] Median and IQR only
    [(X)] Mean and Standard Deviation
    [( )] Mode and Range
    [( )] Only the Mean
    [[?]]**Explanation:** For normally distributed data without outliers, report the mean (central tendency) paired with standard deviation (dispersion). This uses all data points and is the standard for parametric data.

---

## ✅ Best Practices Checklist

{{1}}
**Before Analysis:**
- [ ] Understand your data type (nominal, ordinal, interval, ratio)
- [ ] Check for missing values
- [ ] Identify your research question
- [ ] Know your audience (technical vs. general)

{{2}}
**During Analysis:**
- [ ] Visualize data first (histogram, boxplot)
- [ ] Check for outliers
- [ ] Assess distribution shape
- [ ] Choose appropriate measures based on data characteristics
- [ ] Calculate multiple measures when uncertain
- [ ] Document your code with comments

{{3}}
**For Reporting:**
- [ ] Report N (sample size) always
- [ ] Pair central tendency with dispersion (Mean+SD or Median+IQR)
- [ ] Use consistent decimal places (usually 2)
- [ ] Include units of measurement
- [ ] Provide context for interpretation
- [ ] Include visualizations
- [ ] Follow APA format for statistics

---

## 📚 Key Takeaways


### Choosing Measures: The Golden Rules

1. **For symmetric, normal data:** Mean ± SD
2. **For skewed data or outliers:** Median ± IQR
3. **For categorical data:** Mode and frequencies
4. **Always visualize first:** Let the data guide your choice
5. **Report context:** Sample size, units, and interpretation


### Common Mistakes to Avoid

- ❌ Using mean with heavily skewed data
- ❌ Forgetting to remove missing values (na.rm = TRUE)
- ❌ Not checking data types before analysis
- ❌ Reporting too many decimal places
- ❌ Creating plots without clear labels
- ❌ Interpreting statistics without context
- ❌ Using SD when data has severe outliers
- ❌ Reporting only central tendency without dispersion


### Remember

> **The "best" descriptive statistic depends entirely on your data characteristics and research goals. There is no one-size-fits-all answer!**

---

## 🔗 Additional Resources


- **R Documentation:** `?mean`, `?sd`, `?summary`
- **psych package:** `?describe`
- **ggplot2 documentation:** https://ggplot2.tidyverse.org/
- **Quick-R Tutorial:** https://www.statmethods.net/stats/descriptives.html
- **When to use which test:** https://statsandr.com/blog/

---

## 💬 Questions?

Contact the teaching team:
- **Hannes Tegelbeckers:** hannes.tegelbeckers@ovgu.de
- **Office Hours:** https://cloud.ovgu.de/call/ciz2te64

**Remember:** Good descriptive statistics are the foundation of all quantitative research!