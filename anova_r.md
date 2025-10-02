# ANOVA in R - Interactive Learning Nugget

## 📊 Introduction to ANOVA

> **Learning Objectives:**  
> By the end of this learning nugget, you will be able to:
> - Understand when to use ANOVA instead of t-tests
> - Distinguish between one-way ANOVA, ANCOVA, and MANOVA
> - Conduct and interpret one-way ANOVA in R
> - Check assumptions for ANOVA
> - Perform post-hoc tests to identify specific group differences
> - Report ANOVA results in APA style
> - Calculate and interpret effect sizes

---

## What is ANOVA?

{{1}}
**ANOVA (Analysis of Variance)** is a statistical method used to compare means across **three or more groups** simultaneously. While a t-test compares exactly two groups, ANOVA extends this comparison to multiple groups.

{{2}}
**Research Question Example:**
*"Do students taught with three different teaching methods (traditional, online, hybrid) have different exam scores?"*

{{3}}
> **Important:** ANOVA tells you **IF** groups differ, but not **WHICH** groups differ. Post-hoc tests identify specific differences.

---

## When to Use ANOVA

{{1}}
### Use ANOVA when:

- ✅ You have **one continuous dependent variable** (e.g., test scores, reaction time, blood pressure)
- ✅ You have **one categorical independent variable** with **three or more groups** (e.g., treatment A, B, C, D)
- ✅ You want to test if there are **significant differences** between group means
- ✅ Your data meets ANOVA assumptions (covered later)

{{2}}
### Do NOT use ANOVA when:

- ❌ You only have **two groups** → Use **t-test** instead
- ❌ You have **multiple dependent variables** → Use **MANOVA** instead
- ❌ You want to control for **covariates** → Use **ANCOVA** instead
- ❌ You have **multiple independent variables** → Use **factorial ANOVA** instead
- ❌ Your data is **severely non-normal** → Use **Kruskal-Wallis test** (non-parametric alternative)

---

## Types of ANOVA

{{1}}
### One-Way ANOVA

The simplest form with **one independent variable** (factor) with multiple levels.

**Example:** Comparing exam scores across three teaching methods (Traditional, Online, Hybrid).

**Null Hypothesis (H₀):** μ₁ = μ₂ = μ₃ (all group means are equal)  
**Alternative Hypothesis (H₁):** At least one group mean differs

---

{{2}}
### ANCOVA (Analysis of Covariance)

ANOVA with one or more **continuous covariates** to control for confounding variables.

**When to use:**
- You want to control for continuous covariates (e.g., prior knowledge, age)
- Increases statistical power by reducing error variance
- Adjusts group means to account for baseline differences

**Example:** Comparing teaching methods while controlling for students' pre-test scores.

---

{{3}}
### MANOVA (Multivariate Analysis of Variance)

ANOVA with **multiple dependent variables** tested simultaneously.

**When to use:**
- You have multiple related continuous dependent variables
- You want to control for Type I error across multiple tests
- The dependent variables are correlated

**Example:** Comparing teaching methods on both math scores AND reading comprehension.

---

{{4}}
> **Note:** ANCOVA and MANOVA are advanced techniques beyond the scope of this learning nugget. This guide focuses on **one-way ANOVA**.

---

{{5}}
### Quick Comparison Table

| Situation | Number of Groups | Test to Use |
|:----------|:-----------------|:------------|
| Compare two groups | 2 | Independent t-test |
| Compare three or more groups | 3+ | One-way ANOVA |
| Multiple groups + control for covariate | 3+ | ANCOVA |
| Multiple dependent variables | 3+ | MANOVA |
| Non-normal data, three or more groups | 3+ | Kruskal-Wallis test |

---

## ANOVA Assumptions

{{1}}
Before conducting ANOVA, your data must meet these assumptions:

{{2}}
| Assumption | Description | How to Check |
|:-----------|:------------|:-------------|
| **1. Independence** | Observations are independent of each other | Study design consideration |
| **2. Normality** | Data in each group is normally distributed | See **"Normality Check"** learning nugget |
| **3. Homogeneity of variance** | Equal variances across all groups | Levene's Test |
| **4. Measurement level** | Dependent variable is continuous/interval | Data inspection |

{{3}}
### Robustness to Violations

| Condition | ANOVA Robust? | Recommendation |
|:----------|:--------------|:---------------|
| Equal/similar sample sizes + large n (>30) | ✅ Yes | Proceed with standard ANOVA |
| Mild normality violations + balanced design | ✅ Yes | Proceed with standard ANOVA |
| Unequal variances + equal sample sizes | ⚠️ Maybe | Check Levene's test; consider Welch ANOVA |
| Unequal variances + unequal sample sizes | ❌ No | Use Welch's ANOVA |
| Severe non-normality (heavy skew/outliers) | ❌ No | Use Kruskal-Wallis test |

---

## 🔧 Setting Up Your R Environment

{{1}}
### Step 1: Install and Load Required Packages

```r
# Install packages (only needed once)
install.packages("car")         # For Levene's test
install.packages("ggplot2")     # For visualization
install.packages("dplyr")       # For data manipulation
install.packages("effectsize")  # For effect size calculations
install.packages("rstatix")     # For pipe-friendly statistics

# Load packages (needed every session)
library(car)
library(ggplot2)
library(dplyr)
library(effectsize)
library(rstatix)
```

{{2}}
### Step 2: Prepare Your Data

```r
# Example dataset: Student exam scores by teaching method
data <- data.frame(
  score = c(85, 88, 82, 90, 87,    # Traditional (n=5)
            78, 80, 75, 82, 79,    # Online (n=5)
            92, 95, 90, 93, 91),   # Hybrid (n=5)
  method = c(rep("Traditional", 5),
             rep("Online", 5),
             rep("Hybrid", 5))
)

# CRITICAL: Convert grouping variable to factor
data$method <- factor(data$method)

# View the data structure
str(data)
head(data, 10)

# Check for missing values
sum(is.na(data$score))
sum(is.na(data$method))

# Summary of data
summary(data)
```

{{3}}
**Important Notes:**
- ⚠️ Your **grouping variable MUST be a factor** in R
- ⚠️ Each observation should be in a separate row (long format)
- ⚠️ Remove or handle missing values before analysis
- ⚠️ Check that you have at least 2 observations per group

---

## 📊 Step 1: Descriptive Statistics by Group

{{1}}
### Calculate Summary Statistics

Always start by examining your data descriptively for each group.

```r
# Calculate descriptive statistics by group
descriptive_stats <- data %>%
  group_by(method) %>%
  summarise(
    n = n(),
    mean = mean(score, na.rm = TRUE),
    sd = sd(score, na.rm = TRUE),
    median = median(score, na.rm = TRUE),
    min = min(score, na.rm = TRUE),
    max = max(score, na.rm = TRUE),
    se = sd / sqrt(n)  # Standard error
  )

print(descriptive_stats)
```

{{2}}
**Example Output:**
```
# A tibble: 3 × 8
  method          n  mean    sd median   min   max    se
  <fct>       <int> <dbl> <dbl>  <dbl> <dbl> <dbl> <dbl>
1 Hybrid          5  92.2  2.05     92    90    95 0.917
2 Online          5  78.8  2.86     79    75    82 1.28 
3 Traditional     5  86.4  3.21     87    82    90 1.44 
```

{{3}}
**What to look for:**
- Are sample sizes balanced across groups?
- Are means noticeably different?
- Are standard deviations similar? (important for homogeneity assumption)
- Any extreme min/max values suggesting outliers?

{{4}}
> For a detailed understanding of descriptive statistics, see the **"Descriptive Statistics"** learning nugget.

---

## 📈 Step 2: Visualize Your Data

{{1}}
### Why Visualize First?

Visual inspection is crucial:
- Reveals distribution patterns within each group
- Identifies outliers
- Shows if groups appear to differ
- Helps assess assumptions visually
- Communicates findings effectively

{{2}}
> **Best Practice:** Always visualize your data before running ANOVA!

---

### Boxplot by Group

{{1}}
Boxplots are ideal for comparing distributions across groups.

```r
# Basic boxplot
boxplot(score ~ method, 
        data = data,
        main = "Exam Scores by Teaching Method",
        xlab = "Teaching Method",
        ylab = "Exam Score",
        col = c("lightblue", "lightgreen", "lightcoral"),
        border = "black")

# Add mean points
means <- tapply(data$score, data$method, mean)
points(1:length(means), means, col = "red", pch = 19, cex = 1.5)

# Add legend
legend("topleft",
       legend = c("Median (line)", "Mean (point)"),
       col = c("black", "red"),
       pch = c(NA, 19),
       lty = c(1, NA))
```

{{2}}
**What to look for in boxplots:**
- ✅ **Similar box heights:** Suggests equal variances
- ✅ **Medians at similar positions within boxes:** Normal distributions
- ✅ **Non-overlapping boxes:** Suggests group differences
- ⚠️ **Outliers:** Points beyond whiskers
- ⚠️ **Very different box sizes:** May violate homogeneity assumption

{{3}}
### Enhanced Boxplot with ggplot2

```r
# Professional boxplot with ggplot2
ggplot(data, aes(x = method, y = score, fill = method)) +
  geom_boxplot(alpha = 0.7, outlier.color = "red", outlier.size = 3) +
  stat_summary(fun = mean, geom = "point", 
               shape = 23, size = 4, fill = "red", color = "darkred") +
  labs(title = "Exam Scores by Teaching Method",
       subtitle = "Red diamond = mean | Box line = median | Red points = outliers",
       x = "Teaching Method",
       y = "Exam Score") +
  theme_minimal() +
  theme(legend.position = "none") +
  scale_fill_brewer(palette = "Set2")
```

---

## ✅ Step 3: Check Assumptions

{{1}}
### 3.1 Check Normality

ANOVA assumes data in each group is normally distributed.

```r
# Visual check: Q-Q plots for each group
par(mfrow = c(1, 3))

for (group in levels(data$method)) {
  group_data <- data$score[data$method == group]
  qqnorm(group_data, main = paste("Q-Q Plot:", group))
  qqline(group_data, col = "red", lwd = 2)
}

par(mfrow = c(1, 1))
```

{{2}}
> **For comprehensive guidance on checking normality:** See the **"Normality Check"** learning nugget for detailed instructions on visual assessment, statistical tests, and interpretation.

---

### 3.2 Check Homogeneity of Variance (Levene's Test)

{{1}}
ANOVA assumes equal variances across groups.

```r
# Levene's test for homogeneity of variance
levene_test <- leveneTest(score ~ method, data = data)
print(levene_test)
```

{{2}}
**Example Output:**
```
Levene's Test for Homogeneity of Variance (center = median)
      Df F value Pr(>F)
group  2  0.5423 0.5964
      12               
```

{{3}}
**Interpretation:**
- **p > 0.05:** Variances are equal (assumption met) ✅
- **p < 0.05:** Variances are unequal (assumption violated) ❌
  - Solution: Use **Welch's ANOVA** instead

{{4}}
**Visual Check for Equal Variances:**

```r
# Compare standard deviations
data %>%
  group_by(method) %>%
  summarise(
    sd = sd(score),
    variance = var(score)
  )

# Rule of thumb: Largest SD should not be more than 2x smallest SD
```

{{5}}
**Rule of Thumb:**
If the largest standard deviation is more than twice the smallest, consider using Welch's ANOVA even if Levene's test is not significant.

---

## 🧪 Step 4: Conduct One-Way ANOVA

{{1}}
### Perform the ANOVA

```r
# Conduct one-way ANOVA
anova_result <- aov(score ~ method, data = data)

# Display detailed results
summary(anova_result)
```

{{2}}
**Example Output:**
```
            Df Sum Sq Mean Sq F value   Pr(>F)    
method       2 677.73  338.87   39.51 5.52e-06 ***
Residuals   12 102.93    8.58                     
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

{{3}}
### Understanding the ANOVA Output

**Key Components:**

| Component | Meaning |
|:----------|:--------|
| **Df** | Degrees of freedom (between groups = k-1; within groups = N-k) |
| **Sum Sq** | Sum of squares (variability) |
| **Mean Sq** | Mean squares = Sum Sq / Df (variance estimates) |
| **F value** | Test statistic = Mean Sq(between) / Mean Sq(within) |
| **Pr(>F)** | p-value for the F-test |

{{4}}
**Interpreting the F-statistic:**
- **F-value >> 1:** Between-group variance is much larger than within-group variance
- **Larger F-values:** Stronger evidence of group differences
- **F-value ≈ 1:** Between-group and within-group variance are similar (no group effect)

{{5}}
**In our example:**
- **F(2, 12) = 39.51**
- **p < .001*** (highly significant)
- **Conclusion:** At least one teaching method produces significantly different scores

---

## 📊 Step 5: Interpret ANOVA Results

{{1}}
### The Hypothesis Test

**Null Hypothesis (H₀):** All group means are equal (μ₁ = μ₂ = μ₃)  
**Alternative Hypothesis (H₁):** At least one group mean differs from the others

{{2}}
**Decision Rule:**
- If **p < 0.05:** Reject H₀ → Significant differences exist between groups ✅
- If **p ≥ 0.05:** Fail to reject H₀ → No significant differences detected ❌

{{3}}
### Example Interpretations

**Scenario 1: Significant ANOVA**
```r
# F(2, 12) = 39.51, p < .001
```
> "A one-way ANOVA revealed a statistically significant difference in exam scores between teaching methods, *F*(2, 12) = 39.51, *p* < .001."

**Next step:** Conduct post-hoc tests to identify which groups differ.

{{4}}
**Scenario 2: Non-Significant ANOVA**
```r
# F(2, 27) = 1.83, p = .179
```
> "A one-way ANOVA found no statistically significant difference in exam scores between teaching methods, *F*(2, 27) = 1.83, *p* = .179."

**Next step:** No post-hoc tests needed. Report and interpret the non-significant finding.

{{5}}
### Extract ANOVA Components

```r
# Get detailed ANOVA table
anova_table <- summary(anova_result)
print(anova_table)

# Extract specific values
f_value <- anova_table[[1]]$`F value`[1]
p_value <- anova_table[[1]]$`Pr(>F)`[1]
df_between <- anova_table[[1]]$Df[1]
df_within <- anova_table[[1]]$Df[2]

cat("\nF-statistic:", round(f_value, 2), "\n")
cat("p-value:", format.pval(p_value, digits = 3), "\n")
cat("Degrees of freedom:", df_between, ",", df_within, "\n")
```

---

## 🔍 Step 6: Post-Hoc Tests (If ANOVA is Significant)

{{1}}
### Why Post-Hoc Tests?

ANOVA only tells you **IF** groups differ, not **WHICH** groups differ.

**Example:** If ANOVA is significant with 3 groups (A, B, C), you don't know if:
- A ≠ B, but A = C and B = C?
- A ≠ B ≠ C (all different)?
- A = B, but both differ from C?

Post-hoc tests conduct **pairwise comparisons** to identify specific differences.

{{2}}
> **Important:** Only conduct post-hoc tests if ANOVA is significant (p < .05)!

---

### 6.1 Tukey's HSD Test (Recommended)

{{1}}
**Tukey's Honestly Significant Difference** is the most commonly used post-hoc test.

**Advantages:**
- Controls Type I error rate (family-wise error)
- More powerful than Bonferroni
- Works well with equal or unequal sample sizes
- Easy to interpret

```r
# Perform Tukey's HSD test
tukey_result <- TukeyHSD(anova_result)
print(tukey_result)
```

{{2}}
**Example Output:**
```
  Tukey multiple comparisons of means
    95% family-wise confidence level

Fit: aov(formula = score ~ method, data = data)

$method
                       diff        lwr       upr     p adj
Online-Hybrid        -13.40  -18.8659  -7.93407 0.0001429
Traditional-Hybrid    -5.80  -11.2659  -0.33407 0.0381797
Traditional-Online     7.60    2.1341  13.06593 0.0090043
```

{{3}}
**Understanding Tukey Output:**

| Column | Meaning |
|:-------|:--------|
| **diff** | Mean difference between groups |
| **lwr, upr** | 95% confidence interval for the difference |
| **p adj** | Adjusted p-value (controls for multiple comparisons) |

{{4}}
**Interpreting Results:**

```r
# In our example:
# 1. Online vs Hybrid: diff = -13.40, p < .001 → Online scores LOWER
# 2. Traditional vs Hybrid: diff = -5.80, p = .038 → Traditional scores LOWER
# 3. Traditional vs Online: diff = 7.60, p = .009 → Traditional scores HIGHER

# Conclusion: Hybrid > Traditional > Online
```

{{5}}
**Visualize Tukey Results:**

```r
# Plot Tukey HSD results
plot(tukey_result, las = 1)
abline(v = 0, col = "red", lty = 2)
```

{{6}}
**Interpreting the Plot:**
- **Confidence intervals not crossing zero:** Significant difference
- **Confidence intervals crossing zero:** No significant difference
- **All intervals to the right of zero:** First group has higher mean
- **All intervals to the left of zero:** First group has lower mean

---

### 6.2 Alternative Post-Hoc Tests

{{1}}
**Other post-hoc tests available:**

- **Bonferroni correction:** More conservative, use `pairwise.t.test()` with `p.adjust.method = "bonferroni"`
- **Scheffé test:** Most conservative, good for complex comparisons
- **Holm correction:** Less conservative than Bonferroni
- **Games-Howell test:** For unequal variances (use with Welch ANOVA)

{{2}}
> **Recommendation:** Use **Tukey's HSD** for most situations. It provides good balance between Type I error control and statistical power.

---

## 📐 Step 7: Calculate Effect Size

{{1}}
### Why Report Effect Size?

**Statistical significance (p-value) tells you IF an effect exists.**  
**Effect size tells you HOW LARGE and MEANINGFUL the effect is.**

- p-values depend on sample size
- Large samples can produce significant results for trivial differences
- Effect sizes provide standardized measures of practical significance
- Essential for meta-analyses and comparing across studies

{{2}}
### Eta-Squared (η²)

**Eta-squared** represents the proportion of total variance in the dependent variable explained by the independent variable.

```r
# Calculate eta-squared
library(effectsize)
eta_result <- eta_squared(anova_result)
print(eta_result)
```

{{3}}
**Example Output:**
```
# Effect Size for ANOVA

Parameter | Eta2 |       95% CI
---------------------------------
method    | 0.87 | [0.71, 1.00]
```

{{4}}
**Interpreting Eta-Squared (η²):**

| Value | Interpretation |
|:------|:--------------|
| **0.01** | Small effect (1% of variance explained) |
| **0.06** | Medium effect (6% of variance explained) |
| **0.14** | Large effect (14% of variance explained) |

{{5}}
**In our example:**
- η² = 0.87 means **87% of variance** in exam scores is explained by teaching method
- This is an **extremely large effect** (far exceeds 0.14 threshold)
- The effect is both **statistically significant AND practically meaningful**

{{6}}
### Omega-Squared (ω²) - Less Biased

Omega-squared is a less biased estimate of effect size, especially with small samples.

```r
# Calculate omega-squared
omega_result <- omega_squared(anova_result)
print(omega_result)
```

{{7}}
**Why Omega-Squared?**
- Less biased than eta-squared (especially with small samples)
- More conservative estimate
- Better for population inference

---

## 🔄 Alternative: Welch's ANOVA (Unequal Variances)

{{1}}
### When to Use Welch's ANOVA

If Levene's test indicates **unequal variances** (p < .05), use Welch's ANOVA instead of standard ANOVA.

{{2}}
### Conduct Welch's ANOVA

```r
# Welch's ANOVA (does not assume equal variances)
welch_anova <- oneway.test(score ~ method, data = data, var.equal = FALSE)
print(welch_anova)
```

{{3}}
**Example Output:**
```
	One-way analysis of means (not assuming equal variances)

data:  score and method
F = 39.51, num df = 2.00, denom df = 7.87, p-value = 9.453e-05
```

{{4}}
### Post-Hoc Tests for Welch's ANOVA

```r
# Games-Howell post-hoc test (for unequal variances)
library(rstatix)
games_howell_result <- games_howell_test(data, score ~ method)
print(games_howell_result)

# Alternative: Pairwise t-tests without pooled SD
pairwise.t.test(data$score, data$method, 
                p.adjust.method = "bonferroni",
                pool.sd = FALSE)
```

---

## 📝 Reporting Your Results (APA Style)

{{1}}
### Complete Report Template

> A one-way ANOVA was conducted to compare the effect of [independent variable] on [dependent variable]. Descriptive statistics showed [brief description of means and SDs]. [Assumption checks: normality and homogeneity of variance]. There was a statistically significant difference between groups, *F*([df_between], [df_within]) = [F-value], *p* = [p-value], η² = [effect size]. Post-hoc comparisons using the Tukey HSD test indicated that [describe specific differences].

{{2}}
### What to Report

**Always include:**
1. **Type of test:** One-way ANOVA
2. **Sample sizes and descriptive statistics** for each group (M, SD)
3. **Assumption checks:** Results of Levene's test and normality assessment
4. **F-statistic** with degrees of freedom: *F*(df₁, df₂)
5. **p-value:** Exact value or *p* < .001
6. **Effect size:** η² or ω² with interpretation
7. **Post-hoc results:** Specific pairwise comparisons if significant
8. **Conclusion:** Clear statement about which groups differ

---

## 🎯 Knowledge Check Quiz

{{1}}
### Question 1: When to Use ANOVA

You want to compare the average test scores of students from 5 different schools. Which test should you use?

    [(X)] One-way ANOVA
    [( )] Independent t-test
    [( )] Paired t-test
    [( )] Chi-square test

{{2}}
**Explanation:** One-way ANOVA is used to compare means across three or more independent groups. With 5 schools, ANOVA is the appropriate test.

---

{{3}}
### Question 2: Interpreting F-statistic

You run an ANOVA and get F(3, 56) = 8.42, p = .001. What can you conclude?

    [(X)] At least one group mean differs significantly from the others
    [( )] All group means are significantly different from each other
    [( )] All group means are equal
    [( )] The first and last groups are significantly different

{{4}}
**Explanation:** A significant ANOVA (p < .05) indicates that at least one group differs, but it doesn't tell you which specific groups differ. You need post-hoc tests for that.

---

{{5}}
### Question 3: Levene's Test Results

Your Levene's test gives p = .032. What should you do?

    [( )] Proceed with standard ANOVA
    [(X)] Use Welch's ANOVA instead
    [( )] Transform your data
    [( )] Give up on the analysis

{{6}}
**Explanation:** A significant Levene's test (p < .05) indicates unequal variances. Welch's ANOVA should be used instead of standard ANOVA because it doesn't assume equal variances.

---

{{7}}
### Question 4: Post-Hoc Tests

Your ANOVA gives p = .347. Should you run post-hoc tests?

    [( )] Yes, always run post-hoc tests after ANOVA
    [(X)] No, post-hoc tests are only needed when ANOVA is significant
    [( )] Yes, but only Tukey's HSD
    [( )] It depends on the sample size

{{8}}
**Explanation:** Post-hoc tests should only be conducted when ANOVA is significant (p < .05). A non-significant ANOVA (p = .347) means there are no significant differences to explore.

---

{{9}}
### Question 5: Effect Size Interpretation

Your ANOVA shows η² = 0.18. How would you interpret this effect size?

    [( )] Small effect
    [( )] Medium effect
    [(X)] Large effect
    [( )] No effect

{{10}}
**Explanation:** η² = 0.18 exceeds the 0.14 threshold for a large effect. This means 18% of variance in the dependent variable is explained by the grouping variable, which is a substantial effect.

---

{{11}}
### Question 6: Tukey Results

In Tukey's output, Group A vs Group B has p adj = .072. What does this mean?

    [( )] Groups A and B are significantly different
    [(X)] Groups A and B are not significantly different
    [( )] More data is needed
    [( )] The test failed

{{12}}
**Explanation:** The adjusted p-value (.072) is greater than .05, so we fail to reject the null hypothesis. There is no statistically significant difference between Groups A and B.

---

## ✅ Best Practices Checklist

{{1}}
**Before Testing:**
- [ ] Check sample size (minimum 2 observations per group)
- [ ] Remove or handle missing values
- [ ] Ensure grouping variable is a factor
- [ ] Check that dependent variable is continuous

{{2}}
**During Assessment:**
- [ ] Calculate descriptive statistics by group
- [ ] Create boxplots to visualize group differences
- [ ] Check normality assumption (see "Normality Check" learning nugget)
- [ ] Conduct Levene's test for homogeneity of variance
- [ ] Choose appropriate ANOVA (standard vs. Welch)

{{3}}
**After ANOVA:**
- [ ] Only run post-hoc tests if ANOVA is significant (p < .05)
- [ ] Calculate and report effect size (η² or ω²)
- [ ] Visualize post-hoc results
- [ ] Report all results in APA style

{{4}}
**Common Mistakes to Avoid:**
- ❌ Using ANOVA with only two groups (use t-test instead)
- ❌ Forgetting to convert grouping variable to factor
- ❌ Running post-hoc tests when ANOVA is not significant
- ❌ Ignoring assumption violations
- ❌ Not reporting effect sizes
- ❌ Reporting only p-values without descriptive statistics

---

## 📚 Key Takeaways

{{1}}
### The ANOVA Process

1. **Calculate descriptive statistics** by group
2. **Visualize** data with boxplots
3. **Check assumptions** (normality and homogeneity of variance)
4. **Run ANOVA** to test for overall group differences
5. **If significant:** Conduct post-hoc tests to identify which groups differ
6. **Calculate effect size** to assess practical significance
7. **Report results** in APA style

{{2}}
### Remember

✓ ANOVA compares means across **three or more groups**  
✓ Always **visualize** before testing  
✓ Check **assumptions** (normality and homogeneity of variance)  
✓ Use **post-hoc tests** only if ANOVA is significant  
✓ Report **effect sizes** alongside statistical significance  
✓ **ANCOVA** controls for covariates; **MANOVA** handles multiple dependent variables  
✓ Use **Welch's ANOVA** when variances are unequal

{{3}}
### When Data Doesn't Meet Assumptions

**If normality is violated:**
- Use **Kruskal-Wallis test** (non-parametric alternative)
- Consider data transformation (log, square root)

**If homogeneity of variance is violated:**
- Use **Welch's ANOVA**
- Use **Games-Howell post-hoc test**

**If both assumptions are violated:**
- **Kruskal-Wallis test** is most appropriate

---

## 📚 Additional Resources

{{1}}
### R Documentation
- ANOVA: `?aov`
- Tukey's HSD: `?TukeyHSD`
- Levene's test: `?leveneTest` (car package)
- Effect sizes: `?eta_squared` (effectsize package)

{{2}}
### Online Resources
- **Statistics How To - ANOVA:** https://www.statisticshowto.com/probability-and-statistics/hypothesis-testing/anova/
- **STHDA - One-Way ANOVA in R:** http://www.sthda.com/english/wiki/one-way-anova-test-in-r
- **Quick-R ANOVA Tutorial:** https://www.statmethods.net/stats/anova.html
- **DataCamp - ANOVA in R:** https://www.datacamp.com/tutorial/anova-in-r

{{3}}
### Recommended Readings
- Field, A. (2018). *Discovering Statistics Using IBM SPSS Statistics* (5th ed.). SAGE Publications.
  - Chapter on comparing several means (ANOVA)
- Kabacoff, R. (2015). *R in Action* (2nd ed.). Manning Publications.
  - Chapter on basic statistics and ANOVA

{{4}}
### Related Learning Nuggets
- **"Descriptive Statistics"** - Understanding your data
- **"Normality Check"** - Checking ANOVA assumptions
- **"T-Tests in R"** - Comparing two groups

---

## 💬 Questions?

Contact the teaching team:
- **Hannes Tegelbeckers:** hannes.tegelbeckers@ovgu.de
- **A E M Sabbir Rifat:** a.rifat@ovgu.de
- **Masub Makhdoom:** masub.makhdoom@ovgu.de
- **Office Hours:** https://cloud.ovgu.de/call/ciz2te64

**Remember:** ANOVA is a powerful tool for comparing multiple groups. Take your time with assumptions, visualize your data, and always interpret results in context!