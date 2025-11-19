# Checking for Normal Distribution in R - Interactive Learning Nugget

## 📊 Introduction to Normality Testing

> **Learning Objectives:**  
> By the end of this learning nugget, you will be able to:
> - Understand why normality testing matters in research
> - Visually assess normality using histograms and boxplots
> - Conduct and interpret statistical normality tests
> - Choose the appropriate normality test for your sample size
> - Make informed decisions about parametric vs. non-parametric tests
> - Report normality assessment results appropriately

---

## Why Check for Normal Distribution?

{{1}}
Many statistical tests assume that data follows a normal distribution (bell curve). These are called **parametric tests**:
- t-tests
- ANOVA
- Pearson correlation
- Linear regression

{{2}}
**If your data is NOT normally distributed:**
- Parametric test results may be invalid
- You should use non-parametric alternatives
- Or transform your data to achieve normality

{{3}}
> **Important:** With large sample sizes (n > 30), parametric tests are robust to violations of normality due to the Central Limit Theorem. However, it's still best practice to check!

---

## 🔧 Setting Up Your R Environment


### Step 1: Install and Load Required Packages

```r
# Install packages (only needed once)
install.packages("ggplot2")      # For visualization
install.packages("moments")      # For skewness and kurtosis
install.packages("nortest")      # For Lilliefors test

# Load packages (needed every session)
library(ggplot2)
library(moments)
library(nortest)
```


### Step 2: Load Your Data

```r
# Option 1: Using built-in dataset (for practice)
data(mtcars)
df <- mtcars

# Option 2: Load your own CSV file
# df <- read.csv("your_data.csv")

# View the structure
str(df)
head(df)
```


### Step 3: Select Variable to Test

```r
# Extract the variable you want to test
test_variable <- df$mpg

# Check for missing values
sum(is.na(test_variable))

# Remove missing values if present
test_variable <- na.omit(test_variable)

# Check sample size
n <- length(test_variable)
print(paste("Sample size:", n))
```

---

## 👁️ Step 1: Visual Assessment of Normality


### Why Start with Visualization?

Visual inspection is the **first and most important step**:
- Statistical tests can be overly sensitive with large samples
- Plots reveal the actual distribution pattern
- Easy to communicate findings
- Provides intuitive understanding


> **Best Practice:** Always visualize your data before running statistical tests!

---

### 1.1 Histogram with Normal Curve

{{1}}
A histogram shows the frequency distribution of your data and is the most common tool for assessing normality.

```r
# Create histogram with normal curve overlay
hist(test_variable,
     main = "Histogram with Normal Curve",
     xlab = "Value",
     ylab = "Frequency",
     col = "lightblue",
     border = "black",
     probability = TRUE)  # Use density instead of frequency

# Add normal curve
curve(dnorm(x, mean = mean(test_variable), sd = sd(test_variable)),
      col = "red",
      lwd = 2,
      add = TRUE)

# Add mean and median lines
abline(v = mean(test_variable), col = "blue", lwd = 2, lty = 2)
abline(v = median(test_variable), col = "green", lwd = 2, lty = 2)

# Add legend
legend("topright",
       legend = c("Normal Curve", "Mean", "Median"),
       col = c("red", "blue", "green"),
       lty = c(1, 2, 2),
       lwd = 2)
```

{{2}}
**What to look for in a histogram:**
- ✅ **Bell-shaped curve:** Data clusters around center, tapers at ends
- ✅ **Mean ≈ Median:** Lines should overlap or be very close
- ✅ **Symmetry:** Both sides of center look similar
- ❌ **Skewness:** One tail longer than the other
- ❌ **Multiple peaks:** Suggests multiple subgroups (bimodal/multimodal)
- ❌ **Flat distribution:** Data spread evenly across range
- ❌ **Heavy tails:** More extreme values than expected


### Enhanced Histogram with ggplot2

```r
# More professional histogram
ggplot(data.frame(x = test_variable), aes(x = x)) +
  geom_histogram(aes(y = after_stat(density)), 
                 bins = 10,
                 fill = "steelblue", 
                 color = "black", 
                 alpha = 0.7) +
  stat_function(fun = dnorm,
                args = list(mean = mean(test_variable), 
                           sd = sd(test_variable)),
                color = "red",
                linewidth = 1.2) +
  geom_vline(aes(xintercept = mean(x)), 
             color = "blue", 
             linetype = "dashed",
             linewidth = 1) +
  geom_vline(aes(xintercept = median(x)), 
             color = "green", 
             linetype = "dashed",
             linewidth = 1) +
  labs(title = "Distribution with Normal Curve Overlay",
       subtitle = paste("n =", n),
       x = "Value",
       y = "Density") +
  theme_minimal()
```


**Adjusting the number of bins:**
Different bin widths can reveal different patterns in your data.

```r
# Try different bin numbers
par(mfrow = c(2, 2))

for (bins in c(5, 10, 15, 20)) {
  hist(test_variable,
       main = paste("Bins =", bins),
       xlab = "Value",
       col = "lightblue",
       breaks = bins,
       probability = TRUE)
  curve(dnorm(x, mean = mean(test_variable), sd = sd(test_variable)),
        col = "red", lwd = 2, add = TRUE)
}

par(mfrow = c(1, 1))
```

---

### 1.2 Boxplot

{{1}}
Boxplots help identify outliers and assess symmetry.

```r
# Basic boxplot
boxplot(test_variable,
        main = "Boxplot for Outlier Detection",
        ylab = "Value",
        col = "lightgreen",
        border = "darkgreen",
        horizontal = FALSE)

# Add mean point
points(1, mean(test_variable), col = "red", pch = 19, cex = 1.5)

# Add legend
legend("topright",
       legend = c("Median (line)", "Mean (point)"),
       col = c("black", "red"),
       pch = c(NA, 19),
       lty = c(1, NA))
```

{{2}}
**Understanding the boxplot components:**
- **Box:** Contains middle 50% of data (IQR)
- **Line in box:** Median value
- **Whiskers:** Extend to 1.5 × IQR from box edges
- **Points beyond whiskers:** Potential outliers

{{3}}
**What to look for in a boxplot:**
- ✅ **Median in center of box:** Indicates symmetry
- ✅ **Similar whisker lengths:** Balanced distribution
- ✅ **Few or no outliers:** Points beyond whiskers
- ❌ **Median off-center:** Suggests skewness
- ❌ **Unequal whisker lengths:** Asymmetric distribution
- ❌ **Many outliers:** May indicate non-normality


### Enhanced Boxplot with ggplot2

```r
# Boxplot with ggplot2
ggplot(data.frame(x = 1, y = test_variable), aes(x = factor(x), y = y)) +
  geom_boxplot(fill = "steelblue", alpha = 0.7, outlier.color = "red", 
               outlier.size = 3) +
  stat_summary(fun = mean, geom = "point", 
               shape = 23, size = 3, fill = "red") +
  labs(title = "Boxplot with Mean and Outliers",
       subtitle = paste("n =", n, "| Red points = outliers | Diamond = mean"),
       x = "",
       y = "Value") +
  theme_minimal() +
  theme(axis.text.x = element_blank(),
        axis.ticks.x = element_blank())
```


### Horizontal vs. Vertical Boxplots

```r
# Compare orientations
par(mfrow = c(1, 2))

# Vertical
boxplot(test_variable,
        main = "Vertical Boxplot",
        ylab = "Value",
        col = "lightgreen")

# Horizontal
boxplot(test_variable,
        main = "Horizontal Boxplot",
        xlab = "Value",
        col = "lightblue",
        horizontal = TRUE)

par(mfrow = c(1, 1))
```

---

## 📏 Step 2: Descriptive Statistics for Normality


### Calculate Key Indicators

```r
# Basic statistics
mean_val <- mean(test_variable)
median_val <- median(test_variable)
sd_val <- sd(test_variable)

# Skewness and Kurtosis
skew_val <- skewness(test_variable)
kurt_val <- kurtosis(test_variable)

# Create summary
normality_descriptives <- data.frame(
  N = length(test_variable),
  Mean = round(mean_val, 3),
  Median = round(median_val, 3),
  SD = round(sd_val, 3),
  Skewness = round(skew_val, 3),
  Kurtosis = round(kurt_val, 3)
)

print(normality_descriptives)
```


### Interpreting Skewness

**Skewness measures asymmetry:**
- **-0.5 to +0.5:** Approximately symmetric ✅
- **-1 to -0.5 or +0.5 to +1:** Moderately skewed ⚠️
- **< -1 or > +1:** Highly skewed ❌

```r
# Interpret skewness
if (abs(skew_val) < 0.5) {
  skew_interpret <- "Approximately symmetric (normal)"
} else if (abs(skew_val) < 1) {
  skew_interpret <- "Moderately skewed"
} else {
  skew_interpret <- "Highly skewed (non-normal)"
}

print(paste("Skewness:", round(skew_val, 3), "-", skew_interpret))
```


**Visual patterns:**
- **Positive skewness (> 0):** Right tail longer, Mean > Median
  - Example: Income data (few very high earners)
- **Negative skewness (< 0):** Left tail longer, Mean < Median
  - Example: Test scores (most students score high, few score very low)
- **Zero skewness:** Perfectly symmetric


### Interpreting Kurtosis

**Kurtosis measures tailedness:**
- **Kurtosis ≈ 3:** Normal distribution (mesokurtic) ✅
- **Kurtosis > 3:** Heavy tails, sharp peak (leptokurtic) ⚠️
- **Kurtosis < 3:** Light tails, flat peak (platykurtic) ⚠️

```r
# Interpret kurtosis (using excess kurtosis: kurtosis - 3)
excess_kurt <- kurt_val - 3

if (abs(excess_kurt) < 0.5) {
  kurt_interpret <- "Normal kurtosis (mesokurtic)"
} else if (excess_kurt > 0.5) {
  kurt_interpret <- "Heavy tails (leptokurtic) - more outliers than normal"
} else {
  kurt_interpret <- "Light tails (platykurtic) - fewer outliers than normal"
}

print(paste("Excess Kurtosis:", round(excess_kurt, 3), "-", kurt_interpret))
```


### Quick Reference Table

| Statistic | Normal Range | Interpretation |
|:----------|:-------------|:---------------|
| **Skewness** | -0.5 to +0.5 | Symmetric distribution |
| **Excess Kurtosis** | -0.5 to +0.5 | Normal tailedness |
| **Mean ≈ Median** | Difference < 0.5 SD | Symmetric center |

---

## 🧪 Step 3: Statistical Normality Tests


### Overview of Normality Tests

Statistical tests provide **objective numerical evidence** for normality:
- Tests produce a **p-value**
- Compare p-value to significance level (α = 0.05)
- **p > 0.05:** Do NOT reject normality ✅ (data may be normal)
- **p < 0.05:** Reject normality ❌ (data is NOT normal)


> **Critical Note:** Statistical tests should **supplement**, not replace, visual assessment. Tests can be overly sensitive with large samples!

---

### 3.1 Shapiro-Wilk Test

{{1}}
The **Shapiro-Wilk test** is the most widely used and most powerful normality test for small to medium samples.

```r
# Conduct Shapiro-Wilk test
shapiro_test <- shapiro.test(test_variable)

# Display results
print(shapiro_test)

# Extract key values
shapiro_statistic <- shapiro_test$statistic
shapiro_p <- shapiro_test$p.value

# Interpretation
cat("\n--- Shapiro-Wilk Test Interpretation ---\n")
cat("W Statistic:", round(shapiro_statistic, 4), "\n")
cat("p-value:", round(shapiro_p, 4), "\n")

if (shapiro_p > 0.05) {
  cat("Conclusion: Data is normally distributed (p > 0.05) ✅\n")
  cat("You can proceed with parametric tests.\n")
} else {
  cat("Conclusion: Data is NOT normally distributed (p < 0.05) ❌\n")
  cat("Consider non-parametric tests or data transformation.\n")
}
```

{{2}}
**When to use Shapiro-Wilk:**
- ✅ **Sample size: 3 ≤ n ≤ 5000**
- ✅ Most powerful test for small samples
- ✅ Default choice for most researchers
- ✅ Works well with continuous data
- ✅ Available in base R (no extra packages needed)
- ❌ **Do NOT use with n > 5000** (becomes too sensitive)
- ❌ Not available for very small samples (n < 3)

{{3}}
**Understanding the W Statistic:**
- W ranges from 0 to 1
- **W = 1:** Perfect normal distribution
- **W close to 1 (e.g., W > 0.95):** Suggests normality
- **W < 0.90:** Suggests deviation from normality
- The W statistic is always interpreted together with the p-value

{{4}}
**Example interpretations:**

```r
# Example 1: Normal data
# W = 0.987, p = 0.234
# Interpretation: High W value and p > 0.05 → Data is normal

# Example 2: Non-normal data
# W = 0.856, p = 0.003
# Interpretation: Low W value and p < 0.05 → Data is NOT normal

# Example 3: Borderline case
# W = 0.945, p = 0.048
# Interpretation: p < 0.05 suggests non-normality, but check visuals!
```

---

### 3.2 Kolmogorov-Smirnov Test (Lilliefors Corrected)

{{1}}
The **Kolmogorov-Smirnov test** compares your data to a theoretical normal distribution. We use the **Lilliefors correction** because the standard KS test is not appropriate when parameters are estimated from the data.

```r
# Conduct Lilliefors test (corrected KS test)
library(nortest)
lillie_test <- lillie.test(test_variable)

# Display results
print(lillie_test)

# Interpretation
cat("\n--- Kolmogorov-Smirnov (Lilliefors) Test Interpretation ---\n")
cat("D Statistic:", round(lillie_test$statistic, 4), "\n")
cat("p-value:", round(lillie_test$p.value, 4), "\n")

if (lillie_test$p.value > 0.05) {
  cat("Conclusion: Data is normally distributed (p > 0.05) ✅\n")
  cat("You can proceed with parametric tests.\n")
} else {
  cat("Conclusion: Data is NOT normally distributed (p < 0.05) ❌\n")
  cat("Consider non-parametric tests or data transformation.\n")
}
```

{{2}}
**When to use Kolmogorov-Smirnov (Lilliefors):**
- ✅ **Sample size: n > 50**
- ✅ Good for larger samples
- ✅ More conservative than Shapiro-Wilk (less likely to reject normality)
- ✅ Lilliefors correction handles parameter estimation properly
- ✅ Good alternative when Shapiro-Wilk indicates borderline results
- ⚠️ Less powerful than Shapiro-Wilk for small samples
- ⚠️ Requires nortest package

{{3}}
**Understanding the D Statistic:**
- D measures the maximum distance between your data and the theoretical normal distribution
- **D close to 0:** Good fit to normal distribution
- **Larger D values:** Greater deviation from normality
- D ranges from 0 to 1
- The D statistic is always interpreted together with the p-value

{{4}}
**Why use Lilliefors instead of standard KS test?**

```r
# DON'T DO THIS (incorrect):
# ks.test(test_variable, "pnorm", mean = mean(test_variable), 
#         sd = sd(test_variable))
# This gives a warning and incorrect p-values!

# DO THIS (correct):
lillie.test(test_variable)
# Lilliefors correction accounts for parameter estimation
```

---

## 🎯 Decision Guide: Which Test to Use?


### Quick Decision Tree

```ascii
                Start: What is your sample size (n)?
                              |
            __________________|__________________
           |                  |                  |
         n ≤ 50          50 < n ≤ 5000        n > 5000
           |                  |                  |
    Shapiro-Wilk      Shapiro-Wilk OR       Visual assessment
    (most powerful)   Kolmogorov-Smirnov      ONLY
                      (both acceptable)    (tests too sensitive)
```


### Comprehensive Decision Table

| Sample Size | Recommended Test | Why? | Code |
|:------------|:----------------|:-----|:-----|
| **n < 50** | Shapiro-Wilk | Most powerful for small samples | `shapiro.test(x)` |
| **50 ≤ n ≤ 300** | Shapiro-Wilk | Still most powerful | `shapiro.test(x)` |
| **300 < n ≤ 5000** | Either test works | Both are reliable | `shapiro.test(x)` or `lillie.test(x)` |
| **n > 5000** | **No test!** | Tests become overly sensitive | Visual assessment only |


### The Three-Step Approach

Always follow this order:

```r
# STEP 1: VISUALIZE (ALWAYS FIRST!)
# Create histogram
hist(test_variable, 
     probability = TRUE, 
     col = "lightblue",
     main = "Step 1: Visual Check",
     xlab = "Value")
curve(dnorm(x, mean = mean(test_variable), sd = sd(test_variable)),
      add = TRUE, col = "red", lwd = 2)

# Create boxplot
boxplot(test_variable, 
        main = "Step 1: Check for Outliers",
        col = "lightgreen",
        horizontal = TRUE)

# STEP 2: DESCRIPTIVE STATISTICS
cat("\nStep 2: Descriptive Statistics\n")
cat("Skewness:", round(skewness(test_variable), 3), "\n")
cat("Kurtosis:", round(kurtosis(test_variable), 3), "\n")
cat("Mean:", round(mean(test_variable), 3), "\n")
cat("Median:", round(median(test_variable), 3), "\n")

# STEP 3: STATISTICAL TEST (if appropriate)
n <- length(test_variable)
cat("\nStep 3: Statistical Test\n")
cat("Sample size:", n, "\n\n")

if (n >= 3 && n <= 5000) {
  print(shapiro.test(test_variable))
} else if (n > 5000) {
  cat("Sample too large for statistical test.\n")
  cat("Rely on visual assessment.\n")
} else {
  cat("Sample too small for statistical test.\n")
}
```

---

## 🎯 Knowledge Check Quiz


### Question 1: Sample Size and Test Choice

You have a dataset with 45 observations. Which normality test should you use?

    [(X)] Shapiro-Wilk test
    [( )] Kolmogorov-Smirnov test
    [( )] No test needed, visual assessment only
    [( )] Both tests are equally appropriate
    [[?]]**Explanation:** For small samples (n < 50), the Shapiro-Wilk test is most powerful and is the recommended choice. The Kolmogorov-Smirnov test is better suited for larger samples (n > 50).

---


### Question 2: Interpreting the Histogram

Look at this histogram description: The distribution has a long tail extending to the right, with most values clustered on the left side. The mean is noticeably higher than the median.

Is this data normally distributed?

    [( )] Yes, it is normally distributed
    [(X)] No, it is right-skewed (positively skewed)
    [( )] No, it is left-skewed (negatively skewed)
    [( )] Cannot determine from this information
    [[?]]**Explanation:** A long tail to the right with most values on the left indicates positive (right) skewness. When mean > median, this confirms positive skewness. This is NOT a normal distribution.

---

### Question 3: Understanding the Boxplot

A boxplot shows the median line very close to the bottom of the box, with a long upper whisker and several outliers above. What does this suggest?

    [( )] The data is normally distributed
    [(X)] The data is right-skewed with high outliers
    [( )] The data is left-skewed
    [( )] The data has too few observations
    [[?]]**Explanation:** When the median is near the bottom of the box (closer to Q1 than Q3) and the upper whisker is longer, this indicates right-skewness. Outliers on the high end further confirm positive skewness and non-normality.

---


### Question 4: Shapiro-Wilk Results

You run a Shapiro-Wilk test and get: W = 0.94, p = 0.073. What is your conclusion?

    [(X)] Data is normally distributed (p > 0.05)
    [( )] Data is NOT normally distributed (p < 0.05)
    [( )] Results are inconclusive
    [( )] Need to run another test
    [[?]]**Explanation:** The p-value (0.073) is greater than 0.05, so we do NOT reject the null hypothesis of normality. The data can be considered normally distributed. The W statistic (0.94) also suggests reasonable normality.

---


### Question 5: Large Sample Consideration

You have 8,000 observations. Your Shapiro-Wilk test gives p < 0.001, but your histogram looks approximately bell-shaped with skewness = 0.3. What should you do?

    [( )] Trust the test result and use non-parametric tests
    [(X)] Rely on visual assessment and descriptive statistics; parametric tests are acceptable
    [( )] Collect more data
    [( )] Remove outliers and retest
    [[?]]**Explanation:** With very large samples (n > 5000), statistical tests become overly sensitive and may reject normality even for minor deviations. The visual assessment shows approximately normal distribution (skewness = 0.3 is acceptable), so parametric tests are appropriate. Always prioritize visual assessment with large samples!

---


### Question 6: Interpreting Skewness Value

Your descriptive statistics show: Skewness = -1.8, Kurtosis = 4.2. What does this tell you about the distribution?

    [( )] The data is normally distributed
    [(X)] The data is left-skewed with heavy tails
    [( )] The data is right-skewed with light tails
    [( )] The data is symmetric but has outliers
    [[?]]**Explanation:** Skewness of -1.8 indicates strong left (negative) skewness (|skewness| > 1). Kurtosis of 4.2 (> 3) indicates leptokurtic distribution with heavier tails than normal. This data is definitely NOT normally distributed and requires non-parametric tests.

---

## 📝 Reporting Normality Assessment


### For Research Papers (APA Style)

**Example 1: Normal distribution**
> "Visual inspection of histograms and boxplots showed that the data were approximately normally distributed. Skewness (-0.21) and kurtosis (2.87) were within acceptable ranges. The Shapiro-Wilk test confirmed normality, *W* = 0.95, *p* = .127."

**Example 2: Non-normal distribution**
> "Visual inspection revealed positive skewness in the distribution (skewness = 1.87, kurtosis = 5.21). The Shapiro-Wilk test indicated significant departure from normality, *W* = 0.82, *p* < .001. Therefore, non-parametric tests were used for subsequent analyses."

**Example 3: Large sample**
> "With a sample size of *N* = 8,452, formal statistical tests of normality were not conducted as they become overly sensitive with large samples. Visual inspection of histograms and boxplots revealed approximately normal distribution with slight positive skewness (skewness = 0.67)."


### Creating a Results Table

```r
# Function to create publication-ready table
create_normality_table <- function(data, variables) {
  
  results <- data.frame(
    Variable = character(),
    N = numeric(),
    M = numeric(),
    SD = numeric(),
    Skewness = numeric(),
    Kurtosis = numeric(),
    SW_W = numeric(),
    SW_p = character(),
    Normal = character(),
    stringsAsFactors = FALSE
  )
  
  for (var in variables) {
    x <- na.omit(data[[var]])
    n <- length(x)
    
    # Calculate statistics
    m <- mean(x)
    s <- sd(x)
    skew <- skewness(x)
    kurt <- kurtosis(x)
    
    # Run Shapiro-Wilk if appropriate
    if (n >= 3 && n <= 5000) {
      sw <- shapiro.test(x)
      sw_w <- round(sw$statistic, 3)
      sw_p <- ifelse(sw$p.value < 0.001, "< .001", 
                     paste("=", round(sw$p.value, 3)))
      normal <- ifelse(sw$p.value > 0.05, "Yes", "No")
    } else {
      sw_w <- NA
      sw_p <- "N/A"
      normal <- "Visual only"
    }
    
    # Add to results
    results <- rbind(results, data.frame(
      Variable = var,
      N = n,
      M = round(m, 2),
      SD = round(s, 2),
      Skewness = round(skew, 2),
      Kurtosis = round(kurt, 2),
      SW_W = sw_w,
      SW_p = sw_p,
      Normal = normal,
      stringsAsFactors = FALSE
    ))
  }
  
  return(results)
}

# Example usage:
variables_to_test <- c("mpg", "hp", "wt")
normality_table <- create_normality_table(df, variables_to_test)
print(normality_table)

# Export to CSV
write.csv(normality_table, "normality_results_yoursurname.csv", 
          row.names = FALSE)
```


### What to Report

**Always include:**
1. **Sample size (N)** - Essential for interpretation
2. **Descriptive statistics** - Mean, SD (or Median, IQR if non-normal)
3. **Skewness and kurtosis values** - Indicates distribution shape
4. **Test statistic and p-value** - Shapiro-Wilk W and p
5. **Conclusion** - Normal vs. non-normal
6. **Action taken** - Which tests you used as a result


### Complete Example Report

> "Data were screened for normality using visual inspection (histograms and boxplots), descriptive statistics (skewness and kurtosis), and the Shapiro-Wilk test. Miles per gallon (*M* = 20.09, *SD* = 6.03, *N* = 32) showed acceptable skewness (-0.61) and kurtosis (3.92). The Shapiro-Wilk test indicated normality, *W* = 0.95, *p* = .141. Therefore, parametric tests (independent samples t-test) were deemed appropriate for the analysis."

---

## ✅ Best Practices Checklist

{{1}}
**Before Testing:**
- [ ] Check sample size (n)
- [ ] Remove or handle missing values
- [ ] Understand your variable type (continuous data)
- [ ] Know which statistical tests you plan to use

{{2}}
**During Assessment:**
- [ ] **ALWAYS start with visualization** (histogram and boxplot)
- [ ] Calculate skewness and kurtosis
- [ ] Choose appropriate statistical test based on sample size
- [ ] Consider the sensitivity of tests with large samples
- [ ] Look for patterns, not just p-values

{{3}}
**For Reporting:**
- [ ] Report sample size
- [ ] Include descriptive statistics (skewness, kurtosis)
- [ ] Report test statistic and p-value
- [ ] State your conclusion clearly
- [ ] Justify choice of subsequent statistical tests
- [ ] Include visualizations in appendices

{{4}}
**Common Mistakes to Avoid:**
- ❌ Relying only on statistical tests without visualization
- ❌ Using tests with very large samples (n > 5000)
- ❌ Ignoring visual evidence when test says "normal"
- ❌ Not reporting what you did when data was non-normal
- ❌ Forgetting to check for outliers
- ❌ Using standard KS test instead of Lilliefors correction

---

## 📚 Key Takeaways


### The Four Pillars of Normality Assessment

1. **Visual Assessment** (Most Important!)
   - Histogram: Check for bell shape and symmetry
   - Boxplot: Check for outliers and balance

2. **Descriptive Statistics**
   - Skewness: Should be between -0.5 and +0.5
   - Kurtosis: Should be close to 3

3. **Statistical Tests**
   - Shapiro-Wilk: Best for n ≤ 5000
   - Kolmogorov-Smirnov: Alternative for n > 50

4. **Sample Size Consideration**
   - Small samples: Tests have low power
   - Large samples: Tests become too sensitive
   - Always consider n when interpreting results


### Decision Framework

```ascii
Is your data normally distributed?

        Check Histogram
              |
        _____|_____
       |           |
   Bell-shaped   Skewed/Flat
       |           |
  Check Boxplot   → Non-normal
       |              ↓
   Symmetric      Use non-parametric
       |
  Check Skewness
       |
   |Skew| < 1
       |
  Run Shapiro-Wilk
       |
    _____|_____
   |           |
  p > 0.05   p < 0.05
    |           |
  Normal    Non-normal
    ↓           ↓
Use parametric  Use non-parametric
```


### Remember

> **Visual assessment is MORE important than statistical tests!**
> 
> Tests provide numbers, but your eyes provide understanding. When visual assessment and statistical tests disagree, trust your visualizations—especially with large samples.


### What to Do with Non-Normal Data

**Option 1: Use Non-Parametric Tests**
- Mann-Whitney U test (instead of t-test)
- Kruskal-Wallis test (instead of ANOVA)
- Spearman correlation (instead of Pearson)

**Option 2: Transform Your Data**
- Log transformation: For right-skewed data
- Square root transformation: For moderate skewness
- Box-Cox transformation: Automated optimal transformation

**Option 3: Increase Sample Size**
- Central Limit Theorem: Larger samples are more robust
- n > 30 provides more flexibility

---

## 🔗 Additional Resources


### R Documentation
- Shapiro-Wilk test: `?shapiro.test`
- Histogram: `?hist`
- Boxplot: `?boxplot`
- Skewness and Kurtosis: `?skewness` (moments package)


### Online Resources
- **Quick-R Tutorial on Normality:** https://www.statmethods.net/stats/anova.html
- **Statistics How To - Normality Tests:** https://www.statisticshowto.com/probability-and-statistics/statistics-definitions/normality-test-statistical/
- **STHDA - Normality Test in R:** http://www.sthda.com/english/wiki/normality-test-in-r
- **DataCamp - Statistical Thinking in R:** https://www.datacamp.com/courses/statistical-thinking-in-r-part-1


### Recommended Readings
- Field, A. (2018). *Discovering Statistics Using IBM SPSS Statistics* (5th ed.). SAGE Publications.
  - Chapter on assumption testing
- Tabachnick, B. G., & Fidell, L. S. (2019). *Using Multivariate Statistics* (7th ed.). Pearson.
  - Section on screening data and assumptions


### Video Tutorials
- **StatQuest:** "Normal Distribution and Normality Tests" - https://www.youtube.com/c/joshstarmer
- **MarinStatsLectures:** "Checking Normality in R" - https://www.youtube.com/c/marinstatlectures

---

## 💬 Questions?

Contact the teaching team:
- **Hannes Tegelbeckers:** hannes.tegelbeckers@ovgu.de
- **Office Hours:** https://cloud.ovgu.de/call/ciz2te64

**Remember:** Checking normality is a crucial step in quantitative research. Take your time, use multiple methods, and always prioritize visual assessment!