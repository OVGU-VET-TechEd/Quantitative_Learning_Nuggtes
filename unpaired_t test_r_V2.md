# Unpaired t-Test in R - Interactive Learning Nugget

## 🔬 Introduction to the Unpaired t-Test

> **Learning Objectives:**  
> By the end of this learning nugget, you will be able to:
> - Understand when to use an unpaired t-test
> - Check prerequisites for conducting a t-test
> - Perform an unpaired t-test in R
> - Interpret t-test results correctly
> - Report t-test findings in APA format
> - Handle violations of test assumptions

---

## What is an Unpaired t-Test?

{{1}}
The **unpaired t-test** (also called independent samples t-test or two-sample t-test) compares the means of two independent groups to determine whether there is statistical evidence that the population means are significantly different.

{{2}}
**Example Research Questions:**
- Do male and female students differ in exam scores?
- Is there a difference in blood pressure between treatment and control groups?
- Do customers from two regions differ in their satisfaction ratings?

{{3}}
> **Key Concept:** The unpaired t-test asks: "Is the difference between group means large enough that it's unlikely to have occurred by chance?"

---

## When to Use the Unpaired t-Test


### ✅ Use the Unpaired t-Test When:

- **Two independent groups:** Participants belong to one group OR the other (not both)
- **One continuous dependent variable:** Your outcome is measured on interval or ratio scale
- **Between-subjects design:** Different participants in each group
- **Comparing means:** You want to know if average values differ


### Prerequisites (Assumptions):

1. **Independence of observations:** Data points are not related between groups
2. **Normal distribution:** Data in each group should be approximately normally distributed
   - *See learning nugget: "Normality Check"*
   - Less critical with larger samples (n > 30 per group) due to Central Limit Theorem
3. **Homogeneity of variance:** Both groups should have similar variances (spread)
   - Levene's test checks this assumption
   - If violated, use Welch's t-test (default in R)


### ❌ Do NOT Use the Unpaired t-Test When:

- More than two groups → Use ANOVA instead
- Same participants measured twice → Use paired t-test
- Data is ordinal or categorical → Use non-parametric tests (Mann-Whitney U)
- Severe violations of assumptions → Use Mann-Whitney U test

---

## 🔧 Setting Up Your R Environment


### Step 1: Load Required Packages

```r
# Load packages
library(car)        # For Levene's test
library(ggplot2)    # For visualization
library(psych)      # For descriptive statistics

# If not installed, install first:
# install.packages("car")
# install.packages("ggplot2")
# install.packages("psych")
```


### Step 2: Load Your Data

```r
# Option 1: Using built-in dataset (for practice)
# We'll use the mtcars dataset
data(mtcars)
df <- mtcars

# View the data structure
str(df)
head(df)

# Option 2: Load your own CSV file
# df <- read.csv("your_data.csv")
```


### Step 3: Prepare Your Variables

```r
# We'll compare mpg (continuous) between transmission types (am: 0=automatic, 1=manual)

# Check the grouping variable
table(df$am)

# Create a factor with meaningful labels
df$transmission <- factor(df$am, 
                         levels = c(0, 1),
                         labels = c("Automatic", "Manual"))

# Verify
table(df$transmission)
```

---

## 📊 Step 1: Descriptive Statistics and Visualization


### Calculate Descriptive Statistics by Group

> **Important:** Always explore your data descriptively before conducting hypothesis tests. This helps you understand group differences and identify potential issues.
>
> **For detailed guidance on calculating and interpreting descriptive statistics, see learning nugget: "Descriptive Statistics"**

```r
# Brief example - see Descriptive Statistics learning nugget for details
describeBy(df$mpg, group = df$transmission)
```


### Visualize Your Data

```r
# Boxplot - excellent for comparing distributions
boxplot(mpg ~ transmission, 
        data = df,
        main = "MPG by Transmission Type",
        xlab = "Transmission Type",
        ylab = "Miles per Gallon (MPG)",
        col = c("lightblue", "lightgreen"),
        border = "darkblue")

# Add individual data points
stripchart(mpg ~ transmission, 
          data = df,
          vertical = TRUE,
          method = "jitter",
          add = TRUE,
          pch = 19,
          col = "red")
```


**Using ggplot2 (Advanced):**

```r
# Boxplot with individual points
ggplot(df, aes(x = transmission, y = mpg, fill = transmission)) +
  geom_boxplot(alpha = 0.7) +
  geom_jitter(width = 0.2, alpha = 0.5, size = 2) +
  labs(title = "MPG by Transmission Type",
       x = "Transmission Type",
       y = "Miles per Gallon") +
  theme_minimal() +
  theme(legend.position = "none")

# Violin plot (shows distribution shape)
ggplot(df, aes(x = transmission, y = mpg, fill = transmission)) +
  geom_violin(alpha = 0.7) +
  geom_boxplot(width = 0.2, alpha = 0.5) +
  labs(title = "MPG Distribution by Transmission Type",
       x = "Transmission Type",
       y = "Miles per Gallon") +
  theme_minimal() +
  theme(legend.position = "none")
```

---

## ✓ Step 2: Check Assumptions


### Assumption 1: Normality Check

> **For detailed guidance on checking normality, see learning nugget: "Normality Check"**

```r
# Brief example - see Normality Check learning nugget for details
by(df$mpg, df$transmission, shapiro.test)
```


### Assumption 2: Homogeneity of Variance (Levene's Test)

```r
# Levene's test using car package
leveneTest(mpg ~ transmission, data = df)
```

**Interpreting Levene's Test:**
- **H₀:** Variances are equal (homogeneity)
- **H₁:** Variances are different (heterogeneity)
- **p > 0.05:** Variances are equal → Use Student's t-test
- **p ≤ 0.05:** Variances are unequal → Use Welch's t-test


**Visual check: Compare spreads**

```r
# Compare standard deviations
by(df$mpg, df$transmission, sd)

# Rule of thumb: If largest SD / smallest SD < 2, 
# variances are likely similar enough
```

---

## 🎯 Step 3: Conduct the t-Test


### Standard Unpaired t-Test (Student's t-test)

Use when variances are equal (Levene's test p > 0.05).

```r
# Student's t-test with equal variances
t_test_student <- t.test(mpg ~ transmission, 
                        data = df,
                        var.equal = TRUE,      # Assume equal variances
                        alternative = "two.sided")  # Two-tailed test

# View results
t_test_student
```


### Welch's t-Test (Default in R)

Use when variances are unequal (Levene's test p ≤ 0.05). This is the **default and recommended** approach in R.

```r
# Welch's t-test (does not assume equal variances)
t_test_welch <- t.test(mpg ~ transmission, 
                      data = df,
                      var.equal = FALSE,      # Do not assume equal variances
                      alternative = "two.sided")  # Two-tailed test

# View results
t_test_welch
```


> **Best Practice:** Use Welch's t-test (`var.equal = FALSE`) as default. It performs well even when variances are equal and provides protection against variance inequality.


### One-Tailed vs. Two-Tailed Tests

```r
# Two-tailed test (default): Tests if means differ in ANY direction
t.test(mpg ~ transmission, data = df, alternative = "two.sided")

# One-tailed test: Tests if Group 1 > Group 2
t.test(mpg ~ transmission, data = df, alternative = "greater")

# One-tailed test: Tests if Group 1 < Group 2
t.test(mpg ~ transmission, data = df, alternative = "less")
```


**When to use one-tailed vs. two-tailed:**
- **Two-tailed (recommended):** You want to detect any difference between groups
- **One-tailed:** You have a strong directional hypothesis (use sparingly!)

---

## 📖 Step 4: Interpret the Results


### Understanding the Output

```r
# Example output from t-test
t_test_welch
```

**Output components:**

```
Welch Two Sample t-test

data:  mpg by transmission
t = -3.7671, df = 18.332, p-value = 0.001374
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 -11.280194  -3.209684
sample estimates:
mean in group Automatic    mean in group Manual 
               17.14737                24.39231
```


### Key Values to Report:

1. **t-statistic (t):** -3.7671
   - Measures how many standard errors the means are apart
   - Larger |t| = stronger evidence of difference

2. **Degrees of freedom (df):** 18.332
   - Used to determine critical values
   - Welch's t-test uses adjusted df (not always whole number)

3. **p-value:** 0.001374
   - Probability of observing this difference if H₀ is true
   - p < 0.05 → Statistically significant difference

4. **95% Confidence Interval:** [-11.28, -3.21]
   - Range for the true difference between means
   - Does not include 0 → Significant difference

5. **Sample estimates:** Mean Automatic = 17.15, Mean Manual = 24.39


### Statistical Decision

```r
# Set significance level
alpha <- 0.05

# Decision rule
if (t_test_welch$p.value < alpha) {
  print("Result: SIGNIFICANT")
  print("Reject H₀: The groups have different means")
} else {
  print("Result: NOT SIGNIFICANT")
  print("Fail to reject H₀: No evidence of difference")
}
```


### Effect Size: Cohen's d

Effect size tells you how **large** the difference is (not just if it exists).

```r
# Calculate Cohen's d manually
mean_auto <- mean(df$mpg[df$transmission == "Automatic"])
mean_manual <- mean(df$mpg[df$transmission == "Manual"])
sd_pooled <- sqrt((var(df$mpg[df$transmission == "Automatic"]) + 
                   var(df$mpg[df$transmission == "Manual"])) / 2)

cohens_d <- (mean_manual - mean_auto) / sd_pooled
cohens_d
```


**Interpreting Cohen's d:**
- **|d| < 0.2:** Negligible effect
- **|d| = 0.2 - 0.5:** Small effect
- **|d| = 0.5 - 0.8:** Medium effect
- **|d| > 0.8:** Large effect

---

## 📝 Step 5: Report Your Results


### APA Format Reporting

**For a significant result:**

> An unpaired t-test (Welch's) was conducted to compare miles per gallon (MPG) between automatic and manual transmissions. Manual transmissions (*M* = 24.39, *SD* = 6.17) showed significantly higher MPG than automatic transmissions (*M* = 17.15, *SD* = 3.83), *t*(18.33) = -3.77, *p* = .001, 95% CI [-11.28, -3.21], *d* = 1.48. This represents a large effect size.


**For a non-significant result:**

> An unpaired t-test (Welch's) was conducted to compare [dependent variable] between [Group 1] and [Group 2]. There was no significant difference between [Group 1] (*M* = X.XX, *SD* = X.XX) and [Group 2] (*M* = X.XX, *SD* = X.XX), *t*(df) = X.XX, *p* = .XXX, 95% CI [X.XX, X.XX].


### Creating a Results Table

```r
# Extract key statistics
results_table <- data.frame(
  Group = c("Automatic", "Manual"),
  N = c(sum(df$transmission == "Automatic"),
        sum(df$transmission == "Manual")),
  Mean = c(mean(df$mpg[df$transmission == "Automatic"]),
           mean(df$mpg[df$transmission == "Manual"])),
  SD = c(sd(df$mpg[df$transmission == "Automatic"]),
         sd(df$mpg[df$transmission == "Manual"]))
)

# Add test statistics as a caption or note
test_stats <- paste0(
  "t(", round(t_test_welch$parameter, 2), ") = ",
  round(t_test_welch$statistic, 2), ", p = ",
  round(t_test_welch$p.value, 3)
)

# Display
results_table
print(test_stats)
```


### Export Results

```r
# Save results table
write.csv(results_table, "ttest_results_yoursurname.csv", 
          row.names = FALSE)

# Save plot
png("ttest_boxplot_yoursurname.png", width = 800, height = 600)
boxplot(mpg ~ transmission, 
        data = df,
        main = "MPG by Transmission Type",
        xlab = "Transmission",
        ylab = "Miles per Gallon",
        col = c("lightblue", "lightgreen"))
dev.off()
```

---

## 🔄 What If Assumptions Are Violated?


### Violation: Non-Normal Distribution

**Options:**
1. **Transform the data** (log, square root)
2. **Use Mann-Whitney U test** (non-parametric alternative)

```r
# Mann-Whitney U test (Wilcoxon rank-sum test)
wilcox_test <- wilcox.test(mpg ~ transmission, 
                           data = df,
                           exact = FALSE)
wilcox_test
```

### Violation: Unequal Variances

**Solution:** Use Welch's t-test (already covered)

```r
# Welch's t-test handles unequal variances
t.test(mpg ~ transmission, data = df, var.equal = FALSE)
```


### Small Sample Size

**Considerations:**
- With n < 30 per group, normality assumption becomes more critical
- Check normality carefully
- Consider non-parametric alternatives
- Report results cautiously

---

## 💡 Common Mistakes to Avoid


### Mistake 1: Using Paired t-Test for Independent Groups

```r
# ❌ WRONG: Using paired t-test for independent samples
# t.test(group1, group2, paired = TRUE)  # NO!

# ✅ CORRECT: Using unpaired t-test
t.test(outcome ~ group, data = df, paired = FALSE)
```


### Mistake 2: Not Checking Assumptions

- ❌ Running t-test without checking normality and homogeneity
- ✅ Always check assumptions first (see learning nuggets "Normality Check" and "Descriptive Statistics")
- ✅ Use appropriate alternatives when assumptions are violated


### Mistake 3: Interpreting p-values as Effect Size

- ❌ "p < .001 means a huge difference"
- ✅ p-value indicates statistical significance, NOT practical importance
- ✅ Always report effect size (Cohen's d) alongside p-value

---

## 📋 Decision Flowchart


### When to Use Which Test?

```ascii
        Do you have TWO independent groups?
                    |
        ____________|____________
       |                         |
      YES                       NO
       |                         |
   Is DV continuous?        Use different test
       |                    (paired t-test,
   ____|____                ANOVA, etc.)
  |         |
 YES       NO
  |         |
  |    Use Mann-Whitney
  |    or Chi-square
  |
Is data normally distributed?
  |
  |_________
  |         |
 YES       NO (but n > 30)
  |         |
  |         Use Welch's t-test
  |         (robust to non-normality)
  |
NO (and n < 30)
  |
Consider Mann-Whitney U test

Homogeneity of variance?
  |
  |_________
  |         |
 YES       NO
  |         |
Student's  Welch's
t-test     t-test
```

---

## ✅ Checklist for Unpaired t-Test

{{1}}
**Before the Test:**
- [ ] Two independent groups identified
- [ ] Dependent variable is continuous
- [ ] Data loaded and properly structured
- [ ] Grouping variable is a factor with clear labels
- [ ] Descriptive statistics calculated for each group
- [ ] Data visualized (boxplot or violin plot)

{{2}}
**Assumption Checks:**
- [ ] Normality checked for each group (Shapiro-Wilk, Q-Q plots)
- [ ] Homogeneity of variance checked (Levene's test)
- [ ] Documented any assumption violations
- [ ] Chosen appropriate test variant (Student's vs. Welch's)

{{3}}
**Running the Test:**
- [ ] Decided on one-tailed vs. two-tailed (before looking at data!)
- [ ] Conducted t-test with correct parameters
- [ ] Calculated effect size (Cohen's d)
- [ ] Interpreted p-value correctly
- [ ] Checked confidence interval

{{4}}
**Reporting:**
- [ ] Reported descriptive statistics (M, SD, N for each group)
- [ ] Reported test statistic and degrees of freedom
- [ ] Reported p-value
- [ ] Reported confidence interval
- [ ] Reported effect size
- [ ] Stated conclusion in plain language
- [ ] Created appropriate visualizations
- [ ] Followed APA format

---

## 🔑 Key Takeaways

### Essential Concepts

1. **Purpose:** The unpaired t-test compares means of two independent groups
2. **Default choice:** Use Welch's t-test (`var.equal = FALSE`) unless you have strong reason otherwise
3. **Always check assumptions:** Normality and homogeneity of variance
4. **Report effect size:** p-value alone is not enough
5. **Visualize your data:** Plots reveal patterns statistics might miss


### Decision Rules

- **p < 0.05:** Reject H₀, groups differ significantly
- **p ≥ 0.05:** Fail to reject H₀, no evidence of difference
- **CI includes 0:** No significant difference
- **CI excludes 0:** Significant difference


### Common Pitfalls

- ❌ Confusing paired and unpaired t-tests
- ❌ Ignoring assumption violations
- ❌ Over-interpreting p-values
- ❌ Forgetting to report effect sizes
- ❌ Using one-tailed tests without justification

---

## 📚 Additional Resources


- **R Documentation:** `?t.test`, `?leveneTest`
- **Related Learning Nuggets:**
  - Descriptive Statistics in R
  - Normality Check in R
- **Statistics Guru Tutorial:** https://statistikguru.de/r/ungepaarter-t-test-r/
- **Quick-R Tutorial:** https://www.statmethods.net/stats/ttest.html

---

## 💬 Questions?

Contact the teaching team:
- **Hannes Tegelbeckers:** hannes.tegelbeckers@ovgu.de
- **Office Hours:** https://cloud.ovgu.de/call/ciz2te64

**Remember:** The t-test is one of the most fundamental tools in quantitative research. Master it well, and you'll have a solid foundation for more advanced analyses!