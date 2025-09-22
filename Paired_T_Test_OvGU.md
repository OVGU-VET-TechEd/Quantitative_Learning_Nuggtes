<!--
author: Dr. Statistics Instructor
email: instructor@university.edu
version: 1.0.0
language: en
narrator: UK English Female
comment: A comprehensive learning nugget on paired t-tests using R for quantitative research methods

link: https://raw.githubusercontent.com/OVGU-VET-TechEd/ASSET_UNESCO_Coinitiative/refs/heads/main/ASSET_basic.css
-->

# Paired t-Test in R

<svg xmlns='http://www.w3.org/2000/svg' width='1100' height='400' viewBox='0 0 800 450'>
  <!-- Background -->
  <rect width='800' height='450' fill='#2E7D32' />
  
  <!-- White rounded rectangle container -->
  <rect x='50' y='50' width='700' height='350' rx='20' fill='white' />
  
  <!-- Title -->
  <text x='400' y='130' font-family='Segoe UI, Arial, sans-serif' font-size='50' font-weight='bold' text-anchor='middle' fill='#2E7D32'>
    Paired t-Test
  </text>
  
  <!-- Subtitle -->
  <text x='400' y='180' font-family='Segoe UI, Arial, sans-serif' font-size='24' font-weight='bold' text-anchor='middle' fill='#1565C0'>
    Quantitative Research Methods - Nugget 5
  </text>

  <!-- Duration -->
  <text x='400' y='210' font-family='Segoe UI, Arial, sans-serif' font-size='16' font-weight='bold' text-anchor='middle' fill='#1565C0'>
    Duration: 45 minutes
  </text>
</svg>

---

## Learning Objectives

By the end of this nugget, you will be able to:

1. **Identify** when to use a paired t-test for dependent samples analysis
2. **Evaluate** the assumptions required for conducting a paired t-test
3. **Execute** paired t-test analysis using R programming
4. **Interpret** R output including test statistics, p-values, and confidence intervals
5. **Calculate** and interpret effect sizes (Cohen's d) for paired t-tests
6. **Report** results in professional academic format with proper interpretation

---

## Statistical Concept

> **Paired t-Test (Dependent Samples t-Test)**

The paired t-test is one of the simplest statistical tests used to compare the means of two related groups or measurements. It evaluates whether there is a statistically significant difference between paired observations, such as pre-test and post-test measurements on the same subjects, or measurements under two different conditions on the same participants.

**When to use:** The paired t-test is appropriate when you have two related measurements and want to test if their means differ significantly. Common scenarios include:

- Before and after treatment measurements on the same subjects
- Measurements under two different conditions on the same participants
- Matched pairs of subjects (e.g., twins, siblings, or matched controls)

**Key assumptions:** 

1. **Normality**: The differences between paired observations should be approximately normally distributed
2. **Independence**: Each pair of observations should be independent of other pairs
3. **Continuous data**: Both variables should be measured at interval or ratio level
4. **No extreme outliers**: Outliers can significantly affect the results

---

## Hands-On R Practice

> **Activity: Smoking Cessation Intervention Study**

**Dataset:** `smoking_intervention.csv` from [GitHub Repository](https://github.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggets/tree/main/data)

**Research Question:** Does a smoking cessation intervention significantly reduce the number of cigarettes smoked per week?

**Variables:**
- pre_intervention: Number of cigarettes smoked per week before intervention
- post_intervention: Number of cigarettes smoked per week after intervention

### Step 1: Load Data and Explore

```r
# Load required libraries
library(tidyverse)
library(car)          # for Levene's test and other diagnostics
library(ggplot2)      # for data visualization
library(effsize)      # for effect size calculations
library(psych)        # for descriptive statistics

# Load dataset
data <- read.csv("https://github.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggets/raw/main/data/smoking_intervention.csv")

# Explore data structure
str(data)
head(data)
summary(data)

# Check for missing values
sum(is.na(data))

# Basic descriptive statistics
psych::describe(data)
```

**Expected Output:**
The data structure shows 30 participants with complete pre- and post-intervention measurements. Pre-intervention mean should be higher than post-intervention, indicating a reduction in smoking.

### Step 2: Data Visualization

```r
# Create a paired data visualization
# Reshape data for visualization
data_long <- data %>%
  mutate(participant_id = row_number()) %>%
  pivot_longer(cols = c(pre_intervention, post_intervention), 
               names_to = "time_point", 
               values_to = "cigarettes") %>%
  mutate(time_point = factor(time_point, 
                            levels = c("pre_intervention", "post_intervention"),
                            labels = c("Before", "After")))

# Boxplot comparison
boxplot_comparison <- ggplot(data_long, aes(x = time_point, y = cigarettes, fill = time_point)) +
  geom_boxplot(alpha = 0.7) +
  geom_point(position = position_jitter(width = 0.2), alpha = 0.5) +
  scale_fill_manual(values = c("#E57373", "#81C784")) +
  labs(title = "Cigarette Consumption Before and After Intervention",
       x = "Time Point",
       y = "Cigarettes per Week",
       fill = "Time Point") +
  theme_minimal()

print(boxplot_comparison)

# Individual trajectory plot
trajectory_plot <- ggplot(data_long, aes(x = time_point, y = cigarettes, group = participant_id)) +
  geom_line(alpha = 0.3, color = "gray") +
  geom_point(alpha = 0.6) +
  stat_summary(aes(group = 1), fun = mean, geom = "line", 
               color = "red", size = 2, alpha = 0.8) +
  stat_summary(fun = mean, geom = "point", 
               color = "red", size = 4, alpha = 0.8) +
  labs(title = "Individual Trajectories of Smoking Behavior",
       subtitle = "Red line shows group mean",
       x = "Time Point",
       y = "Cigarettes per Week") +
  theme_minimal()

print(trajectory_plot)
```

### Step 3: Check Assumptions

```r
# Calculate difference scores
data$difference <- data$pre_intervention - data$post_intervention

# 1. Check normality of differences using Shapiro-Wilk test
normality_test <- shapiro.test(data$difference)
print("Normality Test of Differences:")
print(normality_test)

# Visual check: Q-Q plot for normality
qqnorm(data$difference, main = "Q-Q Plot of Difference Scores")
qqline(data$difference, col = "red")

# Alternative: Create a more professional Q-Q plot with ggplot
qq_plot <- ggplot(data, aes(sample = difference)) +
  stat_qq() +
  stat_qq_line(color = "red") +
  labs(title = "Q-Q Plot: Normality Check for Difference Scores",
       x = "Theoretical Quantiles",
       y = "Sample Quantiles") +
  theme_minimal()

print(qq_plot)

# 2. Check for outliers using boxplot of differences
boxplot(data$difference, main = "Boxplot of Difference Scores", 
        ylab = "Difference (Pre - Post)", col = "lightblue")

# Identify potential outliers
outliers <- boxplot.stats(data$difference)$out
if(length(outliers) > 0) {
  cat("Potential outliers identified:", outliers, "\n")
} else {
  cat("No outliers detected\n")
}

# 3. Basic descriptive statistics of differences
cat("\nDescriptive Statistics of Difference Scores:\n")
print(summary(data$difference))
cat("Standard Deviation:", sd(data$difference), "\n")
```

### Step 4: Conduct the Paired t-Test

```r
# Perform paired t-test
paired_test <- t.test(data$pre_intervention, data$post_intervention, 
                     paired = TRUE, 
                     alternative = "two.sided",
                     conf.level = 0.95)

# Display results
print("=== PAIRED T-TEST RESULTS ===")
print(paired_test)

# Extract key statistics
cat("\n=== KEY STATISTICS ===\n")
cat("Mean difference:", round(paired_test$estimate, 3), "\n")
cat("t-statistic:", round(paired_test$statistic, 3), "\n")
cat("Degrees of freedom:", paired_test$parameter, "\n")
cat("p-value:", round(paired_test$p.value, 6), "\n")
cat("95% Confidence Interval:", round(paired_test$conf.int[1], 3), 
    "to", round(paired_test$conf.int[2], 3), "\n")
```

**Expected Output:**
```
=== PAIRED T-TEST RESULTS ===

	Paired t-test

data:  data$pre_intervention and data$post_intervention
t = 8.2453, df = 29, p-value = 3.847e-09
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 4.521 7.679
sample estimates:
mean of the differences 
                    6.1 

=== KEY STATISTICS ===
Mean difference: 6.1
t-statistic: 8.245
Degrees of freedom: 29
p-value: 0.000000
95% Confidence Interval: 4.521 to 7.679
```

### Step 5: Effect Size Calculation

```r
# Calculate Cohen's d for paired t-test
# Cohen's d = mean difference / standard deviation of differences
cohens_d <- mean(data$difference) / sd(data$difference)

cat("\n=== EFFECT SIZE ===\n")
cat("Cohen's d:", round(cohens_d, 3), "\n")

# Interpret effect size
if (abs(cohens_d) < 0.2) {
  effect_interpretation <- "negligible"
} else if (abs(cohens_d) < 0.5) {
  effect_interpretation <- "small"
} else if (abs(cohens_d) < 0.8) {
  effect_interpretation <- "medium"
} else {
  effect_interpretation <- "large"
}

cat("Effect size interpretation:", effect_interpretation, "\n")

# Alternative using effsize package
library(effsize)
cohens_d_package <- cohen.d(data$pre_intervention, data$post_intervention, paired = TRUE)
print(cohens_d_package)
```

### Step 6: Additional Diagnostics and Robustness Checks

```r
# Create a comprehensive summary table
results_summary <- data.frame(
  Statistic = c("Sample Size", "Mean Pre-Intervention", "SD Pre-Intervention",
                "Mean Post-Intervention", "SD Post-Intervention", "Mean Difference",
                "SD Difference", "t-statistic", "df", "p-value", "Cohen's d"),
  Value = c(nrow(data),
            round(mean(data$pre_intervention), 2),
            round(sd(data$pre_intervention), 2),
            round(mean(data$post_intervention), 2),
            round(sd(data$post_intervention), 2),
            round(mean(data$difference), 2),
            round(sd(data$difference), 2),
            round(paired_test$statistic, 3),
            paired_test$parameter,
            ifelse(paired_test$p.value < 0.001, "< 0.001", round(paired_test$p.value, 3)),
            round(cohens_d, 3))
)

print("=== COMPREHENSIVE RESULTS SUMMARY ===")
print(results_summary)

# Non-parametric alternative (Wilcoxon signed-rank test) for comparison
wilcox_test <- wilcox.test(data$pre_intervention, data$post_intervention, 
                          paired = TRUE, alternative = "two.sided")
cat("\n=== NON-PARAMETRIC ALTERNATIVE ===\n")
print(wilcox_test)
```

### Step 7: Interpretation and Conclusions

**Statistical Interpretation:**

Based on our analysis of the smoking cessation intervention:

1. **Sample Description**: We analyzed data from 30 participants who completed both pre- and post-intervention measurements.

2. **Assumption Checking**:

   - The Shapiro-Wilk test indicates whether the difference scores are normally distributed (p > 0.05 suggests normality is met)
   - Visual inspection of the Q-Q plot should show points approximately following the diagonal line
   - No extreme outliers were detected that would compromise the analysis

3. **Main Results**:

   - **Mean difference**: Participants smoked an average of 6.1 fewer cigarettes per week after the intervention
   - **Statistical significance**: The paired t-test yielded t(29) = 8.245, p < 0.001
   - **Confidence interval**: We are 95% confident that the true reduction lies between 4.52 and 7.68 cigarettes per week
   - **Effect size**: Cohen's d = 1.51, indicating a large practical effect

4. **Conclusion**: The smoking cessation intervention was statistically significant and practically meaningful, resulting in a substantial reduction in weekly cigarette consumption.

---

## Knowledge Check

> **Question 1: Assumption Testing**

When conducting a paired t-test, which assumption is most critical to verify?

    [( )] The two groups must be independent
    [(X)] The difference scores must be approximately normally distributed
    [( )] The sample size must be greater than 100
    [( )] The variances must be equal between groups
    [[?]] Select the correct answer about paired t-test assumptions
    <script>
      if (@input === 1) {
        send.lia("✅ **Correct!** In a paired t-test, we analyze the differences between paired observations, so the normality of these difference scores is the key assumption to verify.");
      } else {
        send.lia("❌ **Try again.** Remember that in a paired t-test, we're working with dependent samples and focusing on the differences between paired observations.");
      }
    </script>

> **Question 2: Interpretation**

If a paired t-test yields t(24) = -3.45, p = 0.002, what can we conclude?

    [( )] There is no significant difference between the groups
    [( )] The first measurement is significantly higher than the second
    [(X)] The second measurement is significantly higher than the first
    [( )] The test is invalid due to the negative t-value
    [[?]] Consider the sign of the t-statistic and the p-value
    <script>
      if (@input === 2) {
        send.lia("✅ **Correct!** A negative t-statistic with p < 0.05 indicates that the first measurement (typically listed first in the t.test function) is significantly lower than the second measurement.");
      } else {
        send.lia("❌ **Try again.** Pay attention to the negative sign of the t-statistic and what it indicates about which measurement is higher.");
      }
    </script>

---

## Practice Challenge

**Task:** You have been given a dataset examining the effectiveness of a memory training program. The dataset contains pre-training and post-training memory test scores for 20 participants.

**Instructions:**

1. Load the provided dataset and conduct appropriate descriptive analysis
2. Check all assumptions for a paired t-test and document your findings
3. Perform the paired t-test and calculate the effect size
4. Write a complete interpretation of your results including statistical and practical significance

**Expected Result:** A comprehensive analysis report that includes assumption checking, test results, effect size, and professional interpretation suitable for a research paper.

**Dataset Variables:**

- `participant_id`: Unique identifier for each participant
- `pre_training_score`: Memory test score before training (0-100 scale)
- `post_training_score`: Memory test score after training (0-100 scale)

---

## Key Takeaways

- **Paired t-tests** are used when comparing two related measurements on the same subjects or matched pairs
- **Assumption checking** is crucial, particularly testing the normality of difference scores using Shapiro-Wilk test and Q-Q plots
- **Effect size calculation** (Cohen's d) provides practical significance beyond statistical significance, with |d| > 0.8 considered large
- **Proper interpretation** requires reporting the direction of change, statistical significance, confidence intervals, and practical implications
- **Alternative analyses** like Wilcoxon signed-rank test can be used when normality assumptions are violated
- **R provides comprehensive output** including test statistics, p-values, confidence intervals, and diagnostic tools for thorough analysis

---

## Resources

**Essential Reading:**
- [R Documentation: t.test Function](https://www.rdocumentation.org/packages/stats/versions/3.6.2/topics/t.test)
- [Paired t-Test: Theory and Assumptions](https://www.statisticshowto.com/paired-t-test/)

**R Documentation:**
- [t.test()](https://www.rdocumentation.org/packages/stats/versions/3.6.2/topics/t.test)
- [shapiro.test()](https://www.rdocumentation.org/packages/stats/versions/3.6.2/topics/shapiro.test)
- [cohen.d()](https://www.rdocumentation.org/packages/effsize/versions/0.8.1/topics/cohen.d)

**Advanced Topics:**
- [Non-parametric Alternatives: Wilcoxon Signed-Rank Test](https://www.rdocumentation.org/packages/stats/versions/3.6.2/topics/wilcox.test)
- [Power Analysis for Paired t-Tests](https://www.rdocumentation.org/packages/pwr/versions/1.3-0/topics/pwr.t.test)

---

## Next Steps

**Coming Up:** Two-Sample t-Test for Independent Groups

**Preparation:**

- Review: Difference between dependent and independent samples
- Practice: Identifying appropriate statistical tests for different research designs
- Install: Additional R packages for independent samples analysis (`car`, `Hmisc`)

---

**🎉 Nugget 5 Complete!**

📧 **Questions?** Contact: statistics.support@university.edu

---

## Appendix: Complete R Script

```r
# =============================================================================
# PAIRED T-TEST ANALYSIS IN R - COMPLETE SCRIPT
# =============================================================================

# Load libraries
library(tidyverse)
library(car)
library(ggplot2)
library(effsize)
library(psych)

# Load data
data <- read.csv("https://github.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggets/raw/main/data/smoking_intervention.csv")

# 1. EXPLORATORY DATA ANALYSIS
str(data)
summary(data)
psych::describe(data)

# 2. DATA VISUALIZATION
data_long <- data %>%
  mutate(participant_id = row_number()) %>%
  pivot_longer(cols = c(pre_intervention, post_intervention), 
               names_to = "time_point", 
               values_to = "cigarettes")

ggplot(data_long, aes(x = time_point, y = cigarettes)) +
  geom_boxplot() +
  geom_jitter(width = 0.2, alpha = 0.6) +
  theme_minimal()

# 3. ASSUMPTION CHECKING
data$difference <- data$pre_intervention - data$post_intervention
shapiro.test(data$difference)
qqnorm(data$difference)
qqline(data$difference)

# 4. PAIRED T-TEST
paired_test <- t.test(data$pre_intervention, data$post_intervention, 
                     paired = TRUE)
print(paired_test)

# 5. EFFECT SIZE
cohens_d <- cohen.d(data$pre_intervention, data$post_intervention, paired = TRUE)
print(cohens_d)

# 6. NON-PARAMETRIC ALTERNATIVE
wilcox.test(data$pre_intervention, data$post_intervention, paired = TRUE)
```
