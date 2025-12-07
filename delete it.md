# Correlation Analysis in R - Interactive Learning Nugget

## 🔗 Introduction to Correlation Analysis

> **Learning Objectives:** 

> By the end of this learning nugget, you will be able to:

> - Understand the concept of correlation and its interpretation
> - Choose the appropriate correlation coefficient for your data
> - Perform Pearson, Spearman, and Kendall correlations in R
> - Test correlation significance
> - Visualize correlations using scatterplots and correlation matrices
> - Report correlation results in APA format
> - Interpret correlation strength and direction

---

## What is Correlation?

Correlation measures the **strength and direction of the relationship** between two variables. It helps us understand:

- **Direction**: Do variables move together (positive) or in opposite directions (negative)?
- **Strength**: How closely do the variables follow this relationship?
- **Linearity**: Is the relationship linear or monotonic?


**Key Concepts:**

- **Correlation coefficient (r)**: Ranges from -1 to +1
  - **+1**: Perfect positive correlation
  - **0**: No correlation
  - **-1**: Perfect negative correlation

- **Statistical significance (p-value)**: Indicates if the correlation is likely due to chance

- **Effect size**: The correlation coefficient itself indicates effect size

> **Important:** Correlation does NOT imply causation! A strong correlation between two variables does not mean one causes the other.

--{{0}}--
!?[Video: Correlation in R (Noor)](https://github.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggtes/blob/f75bca2e53e506c8982861e4e59ba13402fac952/correlation_r_Media/Noor_correlation_r_1.mp4?raw=true)

---

## 📊 Types of Correlation Coefficients

### Pearson Correlation (r)

Measures **linear relationships** between two continuous variables.

**Formula concept:** Standardized covariance between variables


**When to use Pearson correlation:**
- ✅ Both variables are continuous (interval or ratio scale)
- ✅ Relationship between variables is linear
- ✅ Data is approximately normally distributed
- ✅ No significant outliers present
- ✅ Homoscedasticity (equal variance across range)
- ❌ **Avoid when:** Data is ordinal, heavily skewed, or has outliers


**Example use cases:**
- Relationship between height and weight
- Study time and exam scores
- Temperature and ice cream sales
- Age and reaction time

---


### Spearman Correlation (ρ or rs)

Measures **monotonic relationships** using rank-ordered data.

**How it works:** Converts data to ranks, then calculates Pearson correlation on ranks


**When to use Spearman correlation:**
- ✅ At least ordinal scale data (ranked data)
- ✅ Relationship is monotonic (not necessarily linear)
- ✅ Data contains outliers
- ✅ Data is not normally distributed
- ✅ One or both variables are ordinal
- ❌ **Avoid when:** You need to measure specifically linear relationships


**Example use cases:**
- Ranking of preferences vs. satisfaction scores
- Income level (grouped) and education level
- Any relationship with outliers where Pearson would be misleading
- Class rank and test scores


**What is a monotonic relationship?**
A relationship where variables consistently move in the same direction (or opposite directions), but not necessarily at a constant rate.

---


### Kendall's Tau (τ)

Another **rank-based correlation** coefficient, more robust with small samples.

**How it works:** Based on concordant and discordant pairs


**When to use Kendall's Tau:**
- ✅ Small sample sizes (n < 30)
- ✅ Data has many tied ranks
- ✅ More robust to outliers than Spearman
- ✅ When you want a more conservative estimate
- ❌ **Avoid when:** You have large samples (computationally intensive)


**Example use cases:**
- Studies with limited participants
- Data with many repeated values
- When Spearman and Pearson give conflicting results

---

## 🎯 Decision Guide: Which Correlation to Use?


### Quick Decision Tree

```ascii
                Are both variables continuous?
                           |
          ________________|________________
         |                                 |
        YES                                NO
         |                                 |
    Is data normally                  Use SPEARMAN
    distributed?                      or KENDALL
         |
    _____|_____
   |           |
  YES          NO
   |           |
Is relationship  Use SPEARMAN
linear?         or KENDALL
   |
 __|__
|     |
YES   NO
|     |
PEARSON  Use SPEARMAN
         or KENDALL
```


### Comparison Table

| Characteristic | Pearson | Spearman | Kendall |
|:--------------|:--------|:---------|:--------|
| **Data type** | Continuous | Ordinal or Continuous | Ordinal or Continuous |
| **Relationship** | Linear | Monotonic | Monotonic |
| **Assumptions** | Normality, no outliers | Minimal | Minimal |
| **Outlier sensitivity** | High | Low | Very Low |
| **Sample size** | Any | Any | Better for small (n<30) |
| **Interpretation** | Easiest | Moderate | Moderate |
| **Computation** | Fast | Moderate | Slow for large n |

---

## 🔧 Prerequisites and Setup


### Before You Start

Make sure you have completed:
- **Descriptive Statistics Learning Nugget**: Understanding your data
- **Normality Check Learning Nugget**: Testing assumptions for Pearson correlation


### Install and Load Required Packages

```r
# Install packages (only needed once)
install.packages("ggplot2")
install.packages("corrplot")
install.packages("psych")
install.packages("Hmisc")

# Load packages (needed every session)
library(ggplot2)   # For visualization
library(corrplot)  # For correlation matrices
library(psych)     # For correlation with significance
library(Hmisc)     # For correlation with p-values
```


### Load Your Data

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

## 📈 Performing Correlation Analysis


### Step 1: Explore Your Data

**Always start with descriptive statistics and visualization!**

```r
# Descriptive statistics for variables
summary(df$mpg)
summary(df$wt)

# Check for missing values
sum(is.na(df$mpg))
sum(is.na(df$wt))

# Basic scatterplot to visualize relationship
plot(df$wt, df$mpg,
     main = "Relationship between Weight and MPG",
     xlab = "Weight (1000 lbs)",
     ylab = "Miles per Gallon",
     pch = 19,
     col = "steelblue")
```


**What to look for in the scatterplot:**
- Overall direction (positive or negative)
- Linearity (straight line pattern)
- Outliers (points far from the pattern)
- Strength (how tightly points cluster around a line)
- Homoscedasticity (equal spread across the range)

---


### Step 2: Check Assumptions for Pearson Correlation

**For Pearson correlation, check:**

```r
# 1. Normality of each variable
# (Refer to Normality Check Learning Nugget for details)
shapiro.test(df$mpg)
shapiro.test(df$wt)

# Visual check with Q-Q plots
par(mfrow = c(1, 2))
qqnorm(df$mpg, main = "Q-Q Plot: MPG")
qqline(df$mpg, col = "red")
qqnorm(df$wt, main = "Q-Q Plot: Weight")
qqline(df$wt, col = "red")
par(mfrow = c(1, 1))

# 2. Check for outliers
boxplot(df$mpg, main = "MPG Outliers", horizontal = TRUE)
boxplot(df$wt, main = "Weight Outliers", horizontal = TRUE)

# 3. Visual check for linearity (already done in scatterplot)
```


**Decision after assumption checks:**
- ✅ **If assumptions are met:** Use Pearson correlation
- ❌ **If assumptions are violated:** Use Spearman or Kendall correlation

---


### Step 3: Calculate Correlation Coefficient

#### Pearson Correlation

```r
# Basic Pearson correlation
cor_pearson <- cor(df$mpg, df$wt, method = "pearson")
cor_pearson

# Pearson correlation with significance test
cor_test_pearson <- cor.test(df$mpg, df$wt, method = "pearson")
cor_test_pearson

# Extract specific values
r_value <- cor_test_pearson$estimate
p_value <- cor_test_pearson$p.value
ci_lower <- cor_test_pearson$conf.int[1]
ci_upper <- cor_test_pearson$conf.int[2]

# Print results
cat("Pearson's r =", round(r_value, 3), 
    "\np-value =", format.pval(p_value),
    "\n95% CI = [", round(ci_lower, 3), ",", round(ci_upper, 3), "]")
```

---


#### Spearman Correlation

```r
# Basic Spearman correlation
cor_spearman <- cor(df$mpg, df$wt, method = "spearman")
cor_spearman

# Spearman correlation with significance test
cor_test_spearman <- cor.test(df$mpg, df$wt, method = "spearman")
cor_test_spearman

# Extract specific values
rho_value <- cor_test_spearman$estimate
p_value_spearman <- cor_test_spearman$p.value

# Print results
cat("Spearman's rho =", round(rho_value, 3), 
    "\np-value =", format.pval(p_value_spearman))
```

---


#### Kendall's Tau Correlation

```r
# Basic Kendall correlation
cor_kendall <- cor(df$mpg, df$wt, method = "kendall")
cor_kendall

# Kendall correlation with significance test
cor_test_kendall <- cor.test(df$mpg, df$wt, method = "kendall")
cor_test_kendall

# Extract specific values
tau_value <- cor_test_kendall$estimate
p_value_kendall <- cor_test_kendall$p.value

# Print results
cat("Kendall's tau =", round(tau_value, 3), 
    "\np-value =", format.pval(p_value_kendall))
```

---


### Step 4: Interpret the Correlation Coefficient

**Interpreting the strength of correlation (general guidelines):**

| |r| value | Strength |
|:---------|:---------|
| 0.00 - 0.10 | Negligible |
| 0.10 - 0.30 | Weak |
| 0.30 - 0.50 | Moderate |
| 0.50 - 0.70 | Strong |
| 0.70 - 0.90 | Very Strong |
| 0.90 - 1.00 | Nearly Perfect |


**Interpreting the direction:**
- **Positive correlation (r > 0)**: As one variable increases, the other tends to increase
- **Negative correlation (r < 0)**: As one variable increases, the other tends to decrease
- **No correlation (r ≈ 0)**: No systematic relationship between variables


**Interpreting the p-value:**
- **p < 0.05**: Statistically significant (reject null hypothesis of no correlation)
- **p ≥ 0.05**: Not statistically significant (fail to reject null hypothesis)


**Important notes:**
- Statistical significance ≠ practical significance
- With large samples, even tiny correlations can be significant
- With small samples, only large correlations may reach significance
- Always report both r and p-value
- Consider the confidence interval for precision

---

## 📊 Visualizing Correlations


### Enhanced Scatterplot with Regression Line

```r
# Scatterplot with regression line
plot(df$wt, df$mpg,
     main = "Weight vs. MPG with Regression Line",
     xlab = "Weight (1000 lbs)",
     ylab = "Miles per Gallon",
     pch = 19,
     col = "steelblue",
     cex = 1.5)

# Add regression line
abline(lm(mpg ~ wt, data = df), col = "red", lwd = 2)

# Add correlation information
text(4.5, 30, 
     paste("r =", round(cor(df$mpg, df$wt), 3),
           "\np =", format.pval(cor.test(df$mpg, df$wt)$p.value)),
     pos = 4, cex = 1.2)

# Add grid
grid()
```

---


### Using ggplot2 (Advanced Visualization)

```r
# Create enhanced scatterplot with ggplot2
ggplot(df, aes(x = wt, y = mpg)) +
  geom_point(color = "steelblue", size = 3, alpha = 0.7) +
  geom_smooth(method = "lm", color = "red", se = TRUE, fill = "pink", alpha = 0.3) +
  labs(title = "Relationship between Weight and MPG",
       subtitle = paste("Pearson's r =", 
                       round(cor(df$mpg, df$wt), 3),
                       ", p < 0.001"),
       x = "Weight (1000 lbs)",
       y = "Miles per Gallon") +
  theme_minimal() +
  theme(plot.title = element_text(face = "bold", size = 14),
        plot.subtitle = element_text(size = 11))
```

---


### Correlation Matrix for Multiple Variables

When you want to examine correlations between multiple variables:

```r
# Select variables for correlation matrix
vars_to_correlate <- df[, c("mpg", "hp", "wt", "qsec")]

# Calculate correlation matrix
cor_matrix <- cor(vars_to_correlate, method = "pearson")
round(cor_matrix, 3)

# Visualize correlation matrix
corrplot(cor_matrix, 
         method = "color",
         type = "upper",
         addCoef.col = "black",
         tl.col = "black",
         tl.srt = 45,
         diag = FALSE,
         title = "Correlation Matrix",
         mar = c(0,0,2,0))
```

---


### Correlation Matrix with Significance Stars

```r
# Using psych package for detailed correlation matrix
correlation_results <- corr.test(vars_to_correlate, method = "pearson")

# View correlation coefficients
print(correlation_results$r, digits = 3)

# View p-values
print(correlation_results$p, digits = 3)

# Visualize with significance
corrplot(correlation_results$r, 
         method = "circle",
         type = "upper",
         p.mat = correlation_results$p,
         sig.level = 0.05,
         insig = "blank",
         addCoef.col = "black",
         tl.col = "black",
         tl.srt = 45,
         diag = FALSE,
         title = "Correlations (non-significant blanked)",
         mar = c(0,0,2,0))
```

---

## 📝 Reporting Correlation Results


### APA Style Reporting

**General format:**
> A [Pearson/Spearman/Kendall] correlation was conducted to examine the relationship between [Variable 1] and [Variable 2]. The analysis revealed a [significant/non-significant] [weak/moderate/strong] [positive/negative] correlation, *r*/*ρ*/*τ* = [value], *p* = [value], 95% CI [lower, upper].

---


### Example Reports

**Example 1: Significant Pearson Correlation**

```r
# Calculate correlation
result <- cor.test(df$mpg, df$wt, method = "pearson")
```

> "A Pearson correlation was conducted to examine the relationship between vehicle weight and fuel efficiency (MPG). The analysis revealed a significant strong negative correlation, *r* = -.87, *p* < .001, 95% CI [-.93, -.74]. This indicates that as vehicle weight increases, fuel efficiency decreases."

---


**Example 2: Non-significant Spearman Correlation**

```r
# Create example data with no correlation
set.seed(123)
x <- rnorm(30)
y <- rnorm(30)
result <- cor.test(x, y, method = "spearman")
```

> "A Spearman correlation was conducted due to violations of normality assumptions. The analysis revealed no significant relationship between Variable A and Variable B, *ρ* = .15, *p* = .43. This suggests these variables are not systematically related in our sample."

---


**Example 3: Kendall's Tau with Small Sample**

> "Due to the small sample size (*N* = 20) and presence of tied ranks, Kendall's tau was used. The analysis showed a significant moderate positive relationship between satisfaction ratings and loyalty scores, *τ* = .42, *p* = .03."

---


### Creating a Results Table

```r
# For multiple correlations
results_table <- data.frame(
  Variable_1 = c("MPG", "HP", "MPG"),
  Variable_2 = c("Weight", "Weight", "HP"),
  n = c(32, 32, 32),
  r = c(
    round(cor(df$mpg, df$wt), 3),
    round(cor(df$hp, df$wt), 3),
    round(cor(df$mpg, df$hp), 3)
  ),
  p = c(
    format.pval(cor.test(df$mpg, df$wt)$p.value),
    format.pval(cor.test(df$hp, df$wt)$p.value),
    format.pval(cor.test(df$mpg, df$hp)$p.value)
  ),
  CI_lower = c(
    round(cor.test(df$mpg, df$wt)$conf.int[1], 3),
    round(cor.test(df$hp, df$wt)$conf.int[1], 3),
    round(cor.test(df$mpg, df$hp)$conf.int[1], 3)
  ),
  CI_upper = c(
    round(cor.test(df$mpg, df$wt)$conf.int[2], 3),
    round(cor.test(df$hp, df$wt)$conf.int[2], 3),
    round(cor.test(df$mpg, df$hp)$conf.int[2], 3)
  )
)

# Print table
print(results_table)

# Save table
write.csv(results_table, "correlation_results_yoursurname.csv", row.names = FALSE)
```

---

## ⚠️ Common Pitfalls and Misconceptions


### 1. Correlation ≠ Causation

```r
# Example: Ice cream sales and drowning rates are correlated
# But ice cream doesn't cause drowning!
# Both are caused by a third variable: hot weather (summer)
```

**Remember:** Even a perfect correlation doesn't prove that one variable causes the other. There could be:
- A third variable causing both (confounding)
- Reverse causation
- Coincidence

---


### 2. Restriction of Range

```r
# Example with restricted range
# Full range
set.seed(42)
x_full <- seq(1, 100, length.out = 100)
y_full <- 2 * x_full + rnorm(100, 0, 10)
cor(x_full, y_full)  # Strong correlation

# Restricted range (only values 40-60)
restricted <- which(x_full >= 40 & x_full <= 60)
cor(x_full[restricted], y_full[restricted])  # Weaker correlation!
```

**Warning:** Correlations can be artificially weakened when the range of values is limited.

---


### 3. Outliers Can Mislead

```r
# Example: One outlier creates false correlation
set.seed(42)
x <- rnorm(20, mean = 50, sd = 10)
y <- rnorm(20, mean = 50, sd = 10)
cor(x, y)  # Weak correlation ≈ 0

# Add one outlier
x_with_outlier <- c(x, 100)
y_with_outlier <- c(y, 100)
cor(x_with_outlier, y_with_outlier)  # Now appears correlated!

# Solution: Use Spearman or remove outlier if justified
```

---


### 4. Non-linear Relationships

```r
# Example: Perfect quadratic relationship but r ≈ 0
x <- seq(-10, 10, length.out = 100)
y <- x^2
cor(x, y)  # Close to 0!

# Visualization shows clear relationship
plot(x, y, type = "l", main = "Perfect Non-linear Relationship, r ≈ 0")
```

**Lesson:** Always visualize your data! Correlation only measures linear (or monotonic) relationships.

---


### 5. Sample Size Matters

- **Small samples**: Correlations unstable, wide confidence intervals
- **Large samples**: Tiny correlations can be "significant" but meaningless
- **Best practice:** Report both statistical significance AND effect size (the r value itself)

---

## 🎯 Knowledge Check Quiz


### Question 1: Choosing the Right Test

You want to examine the relationship between class rank (1st, 2nd, 3rd...) and final exam scores. Which correlation should you use?

    [( )] Pearson
    [(X)] Spearman
    [( )] Kendall
    [( )] None of the above
    [[?]]**Explanation:** Class rank is ordinal data, making Spearman correlation the appropriate choice. Spearman can handle ranked/ordinal data and doesn't require normality.

---


### Question 2: Interpreting Correlation Strength and Direction

A study finds *r* = -.65, *p* = .001 between stress levels and sleep quality. What does this mean?

    [( )] Higher stress causes poor sleep
    [(X)] Higher stress is associated with lower sleep quality (strong negative correlation)
    [( )] The correlation is weak and not significant
    [( )] There is a moderate positive relationship
    [[?]]**Explanation:** The negative correlation (-.65) indicates a strong negative relationship: as stress increases, sleep quality tends to decrease. The absolute value of .65 falls in the "strong" range. However, correlation doesn't prove causation! The p-value of .001 indicates this is statistically significant.

---


### Question 3: Assumption Violations

Your data is heavily right-skewed with several outliers. Which correlation is most appropriate?

    [( )] Pearson - it's the most common
    [(X)] Spearman or Kendall
    [( )] Remove all outliers, then use Pearson
    [( )] You cannot calculate correlation with skewed data
    [[?]]**Explanation:** Spearman and Kendall correlations are rank-based and robust to outliers and skewness. They don't require normality assumptions, making them ideal for this situation.

---


### Question 4: Interpreting Significance

*r* = -.42, *p* = .18. What should you report?

    [( )] There is a significant moderate negative correlation
    [(X)] There is no significant correlation, though a moderate negative trend was observed
    [( )] The correlation is positive
    [( )] You need to use a different correlation test
    [[?]]**Explanation:** Since p = .18 > .05, the correlation is not statistically significant. We fail to reject the null hypothesis of no correlation. While r = -.42 suggests a moderate negative relationship in the sample, it's not statistically reliable. The absolute value of .42 would be considered moderate strength, but without significance, we cannot generalize beyond our sample.

---


### Question 5: Selecting Pearson Correlation

Which scenario is MOST appropriate for Pearson correlation?

    [( )] Comparing ranked preferences (1st choice, 2nd choice) with satisfaction scores
    [(X)] Examining the relationship between height (cm) and weight (kg) in normally distributed data
    [( )] Data with several extreme outliers
    [( )] Ordinal survey responses (Strongly Disagree to Strongly Agree)
    [[?]]**Explanation:** Pearson is appropriate for continuous data with linear relationships, normally distributed data, and no severe outliers. Height and weight are both continuous variables that typically show a linear relationship and meet normality assumptions. The other options involve ordinal data or outliers, which are better suited for Spearman or Kendall.

---


### Question 6: Effect Size Interpretation

Which correlation coefficient represents the strongest relationship?

    [( )] r = +.40
    [( )] r = -.35
    [(X)] r = -.82
    [( )] r = +.15
    [[?]]**Explanation:** The strength of correlation is determined by the absolute value, regardless of direction. |-.82| = .82 is the strongest (very strong correlation), while the others are moderate (.40), moderate (.35), or weak (.15). The sign (+ or -) only indicates direction of the relationship, not strength.

---

## ✅ Best Practices Checklist

{{1}}
**Before Analysis:**
- [ ] Understand your data type (continuous, ordinal, nominal)
- [ ] Check for missing values
- [ ] Review descriptive statistics for both variables
- [ ] Identify your research question clearly

{{2}}
**During Analysis:**
- [ ] Create a scatterplot to visualize the relationship
- [ ] Check assumptions if using Pearson (normality, linearity, outliers)
- [ ] Choose appropriate correlation method based on data characteristics
- [ ] Calculate both the correlation coefficient and p-value
- [ ] Consider confidence intervals for precision
- [ ] Document your code with comments

{{3}}
**For Reporting:**
- [ ] Report sample size (N)
- [ ] Report correlation coefficient (r, ρ, or τ)
- [ ] Report p-value
- [ ] Report confidence interval (for Pearson)
- [ ] Interpret strength and direction
- [ ] Include visualization
- [ ] Use APA format for statistics
- [ ] Never claim causation from correlation

---

## 📚 Key Takeaways


### Choosing Correlation Methods: The Golden Rules

1. **For continuous, normally distributed data with linear relationships:** Pearson correlation
2. **For ordinal data or non-normal distributions:** Spearman correlation
3. **For small samples with tied ranks:** Kendall's Tau
4. **Always visualize first:** Let the scatterplot guide your choice
5. **Report both effect size and significance:** r/ρ/τ value AND p-value


### Common Mistakes to Avoid

- ❌ Using Pearson when assumptions are violated
- ❌ Confusing correlation with causation
- ❌ Ignoring outliers in your data
- ❌ Reporting only p-values without effect sizes
- ❌ Forgetting to check for non-linear relationships
- ❌ Not considering sample size when interpreting significance
- ❌ Using correlation for categorical variables (use chi-square instead)
- ❌ Overlooking restricted range issues


### Step-by-Step Workflow Summary

1. **Explore**: Calculate descriptive statistics (see Descriptive Statistics Learning Nugget)
2. **Visualize**: Create scatterplot to assess relationship
3. **Check Assumptions**: Test normality if using Pearson (see Normality Check Learning Nugget)
4. **Choose Method**: Pearson, Spearman, or Kendall based on data characteristics
5. **Calculate**: Perform correlation test in R
6. **Interpret**: Evaluate strength, direction, and significance
7. **Report**: Present results in APA format with appropriate visualization


### Remember

> **Correlation measures association, not causation. A strong correlation between X and Y does not mean X causes Y, Y causes X, or that there's any causal relationship at all!**

---

## 🔗 Additional Resources


### R Documentation and Quick References

- **R Base Functions:** `?cor`, `?cor.test`
- **psych package:** `?corr.test` - https://personality-project.org/r/psych/
- **corrplot package:** https://cran.r-project.org/web/packages/corrplot/vignettes/corrplot-intro.html
- **Quick-R Correlation Tutorial:** https://www.statmethods.net/stats/correlations.html
- **R-bloggers Correlation Guide:** https://www.r-bloggers.com/2021/08/correlation-in-r/


### Scientific Literature

- Cohen, J. (1988). *Statistical Power Analysis for the Behavioral Sciences* (2nd ed.). Routledge. [Classic reference for interpreting effect sizes]
- Field, A., Miles, J., & Field, Z. (2012). *Discovering Statistics Using R*. SAGE Publications. [Comprehensive guide with chapters on correlation and regression]


### Video Tutorials (YouTube)

- **MarinStatsLectures:** "Correlation in R" - Clear explanation with practical examples
  https://www.youtube.com/watch?v=TjxC8qeo67o
- **StatQuest with Josh Starmer:** "Correlation Clearly Explained" - Intuitive conceptual explanation
  https://www.youtube.com/watch?v=xZ_z8KWkhXE


### Interactive Learning Tools

- **Seeing Theory (Brown University):** Interactive visualization of correlation
  https://seeing-theory.brown.edu/regression-analysis/index.html
- **Guess the Correlation Game:** Practice estimating correlation coefficients
  http://guessthecorrelation.com/
- **StatGuru R Tutorial (German):** Comprehensive guide with examples
  https://statistikguru.de/r/r-korrelationen.html

---

## 💬 Questions?

Contact the teaching team:
- **Hannes Tegelbeckers:** hannes.tegelbeckers@ovgu.de
- **Office Hours:** https://cloud.ovgu.de/call/ciz2te64

**Remember:** Understanding correlation is fundamental to quantitative research. Take your time to practice with different datasets and always visualize your data before running tests!