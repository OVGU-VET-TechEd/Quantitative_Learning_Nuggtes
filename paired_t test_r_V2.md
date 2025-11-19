# Paired t-Test in R - Interactive Learning Nugget

## 📊 Introduction to Paired t-Test

> **Learning Objectives:**  
> By the end of this learning nugget, you will be able to:
> - Understand when to use a paired t-test
> - Check assumptions for paired t-tests
> - Perform paired t-tests in R
> - Interpret and report results correctly
> - Calculate and interpret effect sizes
> - Visualize paired data appropriately

---

## What is a Paired t-Test?

{{1}}
The **paired t-test** (also called dependent samples t-test or repeated measures t-test) compares the means of two related groups to determine whether there is a statistically significant difference between them.

{{2}}
**Examples of paired data:**
- **Before-After measurements:** Testing participants before and after an intervention
- **Matched pairs:** Twins, siblings, or matched case-control studies
- **Repeated measures:** Same participants tested under two different conditions
- **Pre-test/Post-test designs:** Educational interventions, training programs

{{3}}
> **Key concept:** The same subjects are measured twice, or subjects are matched in meaningful pairs. This creates dependency between the measurements.

---

## 🎯 When to Use a Paired t-Test


### Required Conditions

Use a paired t-test when ALL of the following conditions are met:

✅ **1. Dependent samples:** The two measurements are related (same subjects or matched pairs)

✅ **2. Continuous data:** The dependent variable is measured on an interval or ratio scale

✅ **3. One independent variable:** With exactly two related measurements/time points

✅ **4. Difference scores are normally distributed:** The differences between paired observations should be approximately normally distributed (check with normality tests - see "Normality Check" learning nugget)

✅ **5. No extreme outliers:** In the difference scores


### When NOT to Use

❌ **Independent samples:** Use independent samples t-test instead

❌ **More than two time points:** Use repeated measures ANOVA instead

❌ **Non-normal differences with small sample:** Use Wilcoxon signed-rank test instead

❌ **Categorical outcome:** Use McNemar's test instead

---

## 🔧 Setting Up Your Analysis


### Step 1: Prepare Your R Environment

```r
# Load required packages
library(psych)      # For descriptive statistics
library(ggplot2)    # For visualization
library(effsize)    # For effect size calculation

# If not installed yet:
# install.packages("psych")
# install.packages("ggplot2")
# install.packages("effsize")
```


### Step 2: Load and Inspect Your Data

```r
# Example: Weight loss study (before and after diet)
# Create example data
weight_data <- data.frame(
  ID = 1:20,
  before = c(85, 90, 78, 92, 88, 95, 76, 84, 91, 87,
             83, 89, 94, 82, 86, 93, 81, 88, 90, 85),
  after = c(82, 87, 76, 89, 85, 92, 74, 81, 88, 84,
            80, 86, 91, 79, 83, 90, 78, 85, 87, 82)
)

# Or load your own data
# weight_data <- read.csv("your_data.csv")

# Inspect the data
head(weight_data)
str(weight_data)

# Check for missing values
sum(is.na(weight_data$before))
sum(is.na(weight_data$after))
```


> **Important:** Your data should have one row per subject/pair, with separate columns for the two measurements.

---

## 📈 Step 1: Calculate Descriptive Statistics

{{1}}
```r
# Descriptive statistics for both measurements
describe(weight_data$before)
describe(weight_data$after)
```

{{2}}
> **Note:** For a detailed guide on calculating and interpreting descriptive statistics, see the **"Descriptive Statistics"** learning nugget.

---

## 🔍 Step 2: Calculate Difference Scores


### Create Difference Variable

{{1}}

```r
# Calculate difference scores (before - after)
# Positive values = reduction (weight loss)
weight_data$difference <- weight_data$before - weight_data$after

# View the differences
head(weight_data)

# Descriptive statistics for differences
describe(weight_data$difference)

# Mean difference
mean_diff <- mean(weight_data$difference)
sd_diff <- sd(weight_data$difference)

cat("Mean difference:", round(mean_diff, 2), "\n")
cat("SD of differences:", round(sd_diff, 2), "\n")
```

{{2}}
> **Important:** The direction of subtraction matters for interpretation! Here, positive differences indicate weight loss (before > after).

---

## ✅ Step 3: Check Assumptions


### Assumption 1: Check for Outliers in Differences


```r
# Boxplot of difference scores
boxplot(weight_data$difference,
        main = "Boxplot of Weight Differences",
        ylab = "Difference (kg)",
        col = "lightblue")

# Identify potential outliers
outliers <- boxplot.stats(weight_data$difference)$out
if(length(outliers) > 0) {
  cat("Potential outliers detected:", outliers, "\n")
} else {
  cat("No outliers detected.\n")
}
```


### Assumption 2: Check Normality of Differences

```r
# Shapiro-Wilk test for normality
shapiro_test <- shapiro.test(weight_data$difference)
shapiro_test

# Q-Q Plot for visual assessment
qqnorm(weight_data$difference, main = "Q-Q Plot of Differences")
qqline(weight_data$difference, col = "red", lwd = 2)
```


> **Note:** For detailed explanations of normality tests and interpretation, see the **"Normality Check"** learning nugget.

---

## 📊 Step 4: Perform the Paired t-Test


### Basic Paired t-Test

```r
# Perform paired t-test
t_test_result <- t.test(weight_data$before, 
                        weight_data$after, 
                        paired = TRUE,
                        alternative = "two.sided")

# Display results
t_test_result
```


### Understanding the Output

The t-test output provides several key pieces of information:

**t-statistic:** Indicates how many standard errors the mean difference is away from zero. Larger absolute values indicate stronger evidence against the null hypothesis.

**degrees of freedom (df):** For paired t-tests, df = n - 1 (where n is the number of pairs). This determines the shape of the t-distribution used for calculating the p-value.

**p-value:** The probability of observing your results (or more extreme) if the null hypothesis (no difference) were true. If p < 0.05, we reject the null hypothesis and conclude there is a significant difference.

**mean of the differences:** The average change between the two measurements. This tells you the direction and magnitude of the effect.

**95% confidence interval:** The range within which we are 95% confident the true population mean difference lies. If this interval does not include zero, the difference is significant at the 0.05 level.

```r
# Extract key values
t_statistic <- t_test_result$statistic
df <- t_test_result$parameter
p_value <- t_test_result$p.value
mean_difference <- t_test_result$estimate
ci_lower <- t_test_result$conf.int[1]
ci_upper <- t_test_result$conf.int[2]

# Print formatted results
cat("=== Paired t-Test Results ===\n")
cat("t-statistic:", round(t_statistic, 3), "\n")
cat("Degrees of freedom:", df, "\n")
cat("p-value:", round(p_value, 4), "\n")
cat("Mean difference:", round(mean_difference, 2), "\n")
cat("95% CI: [", round(ci_lower, 2), ",", round(ci_upper, 2), "]\n")
```


### One-Sided Tests (Optional)

```r
# If you have a directional hypothesis:

# Test if "before" is GREATER than "after" (weight loss)
t_test_greater <- t.test(weight_data$before, 
                         weight_data$after, 
                         paired = TRUE,
                         alternative = "greater")
t_test_greater

# Test if "before" is LESS than "after" (weight gain)
t_test_less <- t.test(weight_data$before, 
                      weight_data$after, 
                      paired = TRUE,
                      alternative = "less")
```


> **Important:** Only use one-sided tests if you have a clear a priori hypothesis about the direction of the effect!

---

## 📏 Step 5: Calculate Effect Size


### Cohen's d for Paired Samples

```r
# Calculate Cohen's d for paired samples
library(effsize)

cohen_d <- cohen.d(weight_data$before, 
                   weight_data$after, 
                   paired = TRUE)
cohen_d

# Extract effect size value
d_value <- cohen_d$estimate
cat("Cohen's d:", round(d_value, 3), "\n")
```

### Interpreting Effect Size (Cohen's d)

Cohen's d expresses the mean difference in standard deviation units, indicating the practical significance of the effect.

| Cohen's d | Effect Size | Interpretation |
|:----------|:------------|:---------------|
| 0.0 - 0.2 | Negligible | Very small, may not be meaningful |
| 0.2 - 0.5 | Small | Noticeable but subtle effect |
| 0.5 - 0.8 | Medium | Moderate, clearly visible effect |
| 0.8 - 1.3 | Large | Strong, substantial effect |
| > 1.3 | Very Large | Extremely strong effect |

> **Important:** A large effect size doesn't guarantee practical importance, and a small effect can be meaningful depending on the context (e.g., medical interventions).


### Alternative: Calculate d Manually

```r
# Manual calculation of Cohen's d for paired samples
# d = mean difference / SD of differences
d_manual <- mean(weight_data$difference) / sd(weight_data$difference)
cat("Cohen's d (manual):", round(d_manual, 3), "\n")
```

---

## 🎨 Step 6: Visualize the Results


### Boxplot Comparison

Boxplots provide a quick visual summary showing medians, quartiles, and potential outliers for both time points.

```r
# Side-by-side boxplots
boxplot(weight_data$before, weight_data$after,
        names = c("Before", "After"),
        main = "Weight Before and After Intervention",
        ylab = "Weight (kg)",
        col = c("lightblue", "lightgreen"),
        border = c("darkblue", "darkgreen"))

# Add mean points
points(c(1, 2), 
       c(mean(weight_data$before), mean(weight_data$after)),
       pch = 19, col = "red", cex = 1.5)

# Add legend
legend("topright", 
       legend = c("Mean"),
       pch = 19, col = "red")
```

**What to look for:** The boxes show the spread (IQR) at each time point. If boxes don't overlap much, this suggests a meaningful difference. Red dots show means, which should shift if there's an effect.


### Histogram of Differences

A histogram of the difference scores helps visualize the distribution and check normality.

```r
# Histogram with normal curve
hist(weight_data$difference,
     main = "Distribution of Weight Differences",
     xlab = "Weight Difference (kg)",
     ylab = "Frequency",
     col = "skyblue",
     border = "black",
     breaks = 10)

# Add mean line
abline(v = mean(weight_data$difference), 
       col = "red", lwd = 2, lty = 2)

# Add legend
legend("topright",
       legend = paste("Mean =", round(mean(weight_data$difference), 2)),
       col = "red", lty = 2, lwd = 2)
```

**What to look for:** A roughly symmetric, bell-shaped distribution supports the normality assumption. The red line shows the mean difference—if it's far from zero, this indicates a substantial effect.

---

## 📝 Step 7: Report Your Results


### APA Style Reporting

**For a significant result (p < 0.05):**

> A paired samples t-test was conducted to compare weight before and after the diet intervention. There was a significant difference in weight before the intervention (*M* = 86.75, *SD* = 5.12) and after the intervention (*M* = 83.65, *SD* = 5.18); *t*(19) = 8.42, *p* < .001, *d* = 1.88. These results suggest that the diet intervention had a large effect on weight reduction. Specifically, participants lost an average of 3.10 kg (95% CI [2.32, 3.88]).


**For a non-significant result (p ≥ 0.05):**

> A paired samples t-test was conducted to compare weight before and after the intervention. There was no significant difference in weight before the intervention (*M* = 86.75, *SD* = 5.12) and after the intervention (*M* = 86.20, *SD* = 5.18); *t*(19) = 1.15, *p* = .265, *d* = 0.26. These results suggest that the intervention did not have a significant effect on weight.


### Creating a Results Table

```r
# Comprehensive results table
results_table <- data.frame(
  Measure = c("Before Intervention", "After Intervention", "Difference"),
  N = c(length(weight_data$before), 
        length(weight_data$after), 
        length(weight_data$difference)),
  M = c(mean(weight_data$before), 
        mean(weight_data$after), 
        mean(weight_data$difference)),
  SD = c(sd(weight_data$before), 
         sd(weight_data$after), 
         sd(weight_data$difference)),
  Min = c(min(weight_data$before), 
          min(weight_data$after), 
          min(weight_data$difference)),
  Max = c(max(weight_data$before), 
          max(weight_data$after), 
          max(weight_data$difference))
)

# Round numeric columns
results_table[, 3:6] <- round(results_table[, 3:6], 2)
results_table

# Add test statistics
test_stats <- data.frame(
  Statistic = c("t-value", "df", "p-value", "Cohen's d", "95% CI Lower", "95% CI Upper"),
  Value = c(round(t_statistic, 3),
            df,
            round(p_value, 4),
            round(d_value, 3),
            round(ci_lower, 2),
            round(ci_upper, 2))
)
test_stats
```


### Save Results

```r
# Save results table
write.csv(results_table, "paired_ttest_descriptives_yoursurname.csv", 
          row.names = FALSE)
write.csv(test_stats, "paired_ttest_statistics_yoursurname.csv", 
          row.names = FALSE)

# Save plot
ggsave("paired_ttest_plot_yoursurname.png", 
       width = 8, height = 6, dpi = 300)
```

---

## 🔄 Alternative: Wilcoxon Signed-Rank Test


### When to Use Wilcoxon

If the normality assumption is violated (especially with small samples), use the **Wilcoxon signed-rank test** as a non-parametric alternative.

```r
# Perform Wilcoxon signed-rank test
wilcox_test <- wilcox.test(weight_data$before, 
                           weight_data$after, 
                           paired = TRUE,
                           exact = FALSE)
wilcox_test

# Calculate effect size (r = Z / sqrt(N))
# Note: Wilcoxon test doesn't directly give Z, but we can approximate
N <- length(weight_data$difference)
W <- wilcox_test$statistic
# For reporting, use rank-biserial correlation
library(rcompanion)
# wilcoxonPairedR(x = weight_data$before, y = weight_data$after)
```


**Reporting Wilcoxon results:**

> A Wilcoxon signed-rank test indicated that weight after the intervention (*Mdn* = 84.50) was significantly lower than weight before the intervention (*Mdn* = 87.00), *V* = 210, *p* < .001.

---

## ❓ Common Questions


### Q1: What if my sample size is small (n < 30)?

**Answer:** The paired t-test is still valid for small samples IF the differences are normally distributed. Always check normality with Shapiro-Wilk test and Q-Q plot. If normality is violated with small n, use Wilcoxon signed-rank test.


### Q2: Can I have missing data?

**Answer:** The paired t-test requires complete pairs. If a participant is missing one measurement, that entire pair must be excluded. R handles this automatically, but always check your sample size.

```r
# Check for complete pairs
complete_pairs <- sum(complete.cases(weight_data[, c("before", "after")]))
cat("Complete pairs:", complete_pairs, "\n")
```


### Q3: What if I have more than two time points?

**Answer:** Use repeated measures ANOVA instead. The paired t-test only compares two related measurements.


### Q4: Should I use a one-tailed or two-tailed test?

**Answer:** Use a two-tailed test (default) unless you have a strong a priori hypothesis about the direction. One-tailed tests are more powerful but should be justified before data collection.


### Q5: What's a "good" effect size?

**Answer:** This depends on your field and context. Generally:
- Clinical settings: Even small effects (*d* = 0.2) can be meaningful
- Psychological interventions: Medium to large effects (*d* ≥ 0.5) are typically expected
- Always interpret effect sizes in the context of practical significance

---

## ✅ Best Practices Checklist

{{1}}
**Before Analysis:**
- [ ] Verify that data are truly paired/dependent
- [ ] Check for missing values and handle appropriately
- [ ] Ensure data are on interval/ratio scale
- [ ] Decide on one-tailed vs. two-tailed hypothesis

{{2}}
**During Analysis:**
- [ ] Calculate and inspect difference scores
- [ ] Check for outliers in differences
- [ ] Test normality of differences (not original variables!)
- [ ] Run appropriate test (paired t-test or Wilcoxon)
- [ ] Calculate effect size
- [ ] Create meaningful visualizations

{{3}}
**For Reporting:**
- [ ] Report descriptive statistics for both time points
- [ ] Report mean difference with confidence interval
- [ ] Include test statistic, df, and p-value
- [ ] Report effect size (Cohen's *d*)
- [ ] Use APA formatting
- [ ] Include appropriate visualization
- [ ] Interpret results in context

---

## 🎯 Quick Reference


### Key R Functions

```r
# Paired t-test
t.test(x, y, paired = TRUE)

# Wilcoxon signed-rank test
wilcox.test(x, y, paired = TRUE)

# Effect size
cohen.d(x, y, paired = TRUE)

# Normality test
shapiro.test(differences)

# Descriptives
describe(data)
```


### Decision Tree

```
        Do you have paired data?
                |
        ________|________
       |                 |
      YES               NO
       |                 |
  Are differences   (Use independent
  normally distributed?  samples t-test)
       |
   ____|____
  |         |
 YES       NO
  |         |
Paired    Wilcoxon
t-test    signed-rank
```

---

## 📚 Key Takeaways


1. **Paired t-test** compares two related measurements from the same subjects or matched pairs

2. **Critical assumption:** The *differences* must be normally distributed, not the original variables

3. **Always calculate effect size** (Cohen's *d*) to assess practical significance beyond statistical significance

4. **Visualize your data** before and after the test to understand the pattern of change

5. **Use Wilcoxon signed-rank test** if normality assumption is violated, especially with small samples


> **Remember:** A statistically significant result (*p* < .05) does not automatically mean the effect is large or practically important. Always interpret results in context and report effect sizes!

---

## 🔗 Additional Resources

- **Related Learning Nuggets:** 
  - Descriptive Statistics in R
  - Normality Check in R
  
- **R Documentation:** `?t.test`, `?wilcox.test`, `?cohen.d`

- **Video Tutorials:**
  - **Paired t-Test in R (Step-by-Step):** [MarinStatsLectures](https://www.youtube.com/watch?v=HFKpdIOCDpI) (10 min)
  - **Understanding Paired t-Tests:** [StatQuest](https://www.youtube.com/watch?v=VEts0d80Dv0) (13 min)

- **Further Reading:**
  - statistikguru.de: https://statistikguru.de/r/gepaarter-t-test-r/

---

## 💬 Questions?

Contact the teaching team:
- **Hannes Tegelbeckers:** hannes.tegelbeckers@ovgu.de
- **Office Hours:** https://cloud.ovgu.de/call/ciz2te64

**Remember:** Understanding your data and checking assumptions is just as important as running the test itself!