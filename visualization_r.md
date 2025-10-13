# Visualizing Quantitative Data Analysis in R - Interactive Learning Nugget

## 📊 Introduction to Data Visualization

> **Learning Objectives:**  
> By the end of this learning nugget, you will be able to:
> 
> - Understand which visualizations match which analysis methods
> - 
> - Create professional publication-ready plots in R
> - 
> - Customize visualizations for different audiences
> - 
> - Export high-quality graphics for reports and presentations
> - 
> - Choose between base R and ggplot2 for your needs
> - 
> - Apply best practices for clear scientific communication

---

## Why Visualization Matters

{{1}}
**Visualization transforms numbers into insights:**
- Makes complex patterns immediately recognizable
- Communicates findings more effectively than tables alone
- Reveals outliers, trends, and relationships at a glance
- Essential for presentations, papers, and reports
- Helps verify assumptions and identify data issues

{{2}}
> **Key Principle:** Every statistical analysis has a corresponding visualization that best communicates its results. This learning nugget shows you exactly which visualization to use for each method!

{{3}}
**The Two Main Approaches in R:**
- **Base R graphics:** Simple, fast, built-in
- **ggplot2:** Professional, customizable, publication-ready

This nugget covers both approaches so you can choose based on your needs.

---

## 🔧 Setting Up Your R Environment

{{1}}
### Step 1: Install and Load Required Packages

```r
# Install packages (only needed once)
install.packages("ggplot2")      # Professional graphics
install.packages("ggpubr")       # Statistical plots
install.packages("gridExtra")    # Multiple plots
install.packages("RColorBrewer") # Color palettes
install.packages("scales")       # Formatting scales

# Load packages (needed every session)
library(ggplot2)
library(ggpubr)
library(gridExtra)
library(RColorBrewer)
library(scales)
```

{{2}}
### Step 2: Load Example Dataset

```r
# Using built-in mtcars dataset for demonstrations
data(mtcars)
df <- mtcars

# Preview the data
head(df)
str(df)

# For your own data:
# df <- read.csv("your_data.csv")
```

{{3}}
### Step 3: Set Global Plotting Options

```r
# Set default theme for ggplot2
theme_set(theme_minimal())

# Set default colors
my_colors <- c("#3498db", "#e74c3c", "#2ecc71", "#f39c12", "#9b59b6")

# Set plot dimensions for export
plot_width <- 8
plot_height <- 6
```

---

## 📈 Visualization Type 1: Histograms

{{1}}
### When to Use Histograms

**Purpose:** Show the distribution of a single continuous variable

**Used in these analyses:**
- ✅ **Descriptive Statistics** (see Descriptive Statistics Learning Nugget)
- ✅ **Normality Checks** (see Normality Check Learning Nugget)
- ✅ **Exploratory Data Analysis**

{{2}}
### Basic Histogram (Base R)

```r
# Simple histogram
hist(df$mpg,
     main = "Distribution of Miles Per Gallon",
     xlab = "MPG",
     ylab = "Frequency",
     col = "steelblue",
     border = "white",
     breaks = 10)

# Add mean line
abline(v = mean(df$mpg), col = "red", lwd = 2, lty = 2)

# Add median line
abline(v = median(df$mpg), col = "green", lwd = 2, lty = 2)

# Add legend
legend("topright",
       legend = c("Mean", "Median"),
       col = c("red", "green"),
       lty = 2,
       lwd = 2)
```

{{3}}
### Professional Histogram (ggplot2)

```r
# Create publication-ready histogram
ggplot(df, aes(x = mpg)) +
  geom_histogram(aes(y = after_stat(density)),
                 bins = 10,
                 fill = "steelblue",
                 color = "white",
                 alpha = 0.8) +
  geom_density(color = "darkblue",
               linewidth = 1) +
  geom_vline(aes(xintercept = mean(mpg)),
             color = "red",
             linetype = "dashed",
             linewidth = 1) +
  geom_vline(aes(xintercept = median(mpg)),
             color = "green",
             linetype = "dashed",
             linewidth = 1) +
  labs(title = "Distribution of Miles Per Gallon",
       subtitle = paste("n =", nrow(df), "| Mean =", round(mean(df$mpg), 2)),
       x = "Miles Per Gallon (MPG)",
       y = "Density") +
  theme_minimal() +
  theme(plot.title = element_text(size = 14, face = "bold"),
        plot.subtitle = element_text(size = 10))
```

{{4}}
### Grouped Histograms (Comparing Groups)

```r
# Compare distributions across groups
ggplot(df, aes(x = mpg, fill = factor(cyl))) +
  geom_histogram(bins = 10,
                 alpha = 0.6,
                 position = "identity") +
  scale_fill_manual(values = c("4" = "#3498db",
                               "6" = "#e74c3c",
                               "8" = "#2ecc71"),
                    name = "Cylinders") +
  labs(title = "MPG Distribution by Number of Cylinders",
       x = "Miles Per Gallon",
       y = "Frequency") +
  theme_minimal()
```

---

## 📦 Visualization Type 2: Boxplots

{{1}}
### When to Use Boxplots

**Purpose:** Compare distributions across groups, identify outliers

**Used in these analyses:**
- ✅ **Descriptive Statistics** (see Descriptive Statistics Learning Nugget)
- ✅ **Normality Checks** (see Normality Check Learning Nugget)
- ✅ **t-tests** (see t-test Learning Nuggets)
- ✅ **ANOVA** (see ANOVA Learning Nugget)

{{2}}
### Basic Boxplot (Base R)

```r
# Single variable boxplot
boxplot(df$mpg,
        main = "Miles Per Gallon Distribution",
        ylab = "MPG",
        col = "lightblue",
        border = "darkblue",
        horizontal = FALSE)

# Add mean point
points(1, mean(df$mpg), col = "red", pch = 19, cex = 1.5)
```

{{3}}
### Grouped Boxplot for t-test or ANOVA

```r
# Compare groups (Base R)
boxplot(mpg ~ cyl,
        data = df,
        main = "MPG by Number of Cylinders",
        xlab = "Cylinders",
        ylab = "Miles Per Gallon",
        col = c("lightblue", "lightgreen", "lightcoral"),
        border = "black")

# Add group means
means <- tapply(df$mpg, df$cyl, mean)
points(1:length(means), means, col = "red", pch = 19, cex = 1.5)
```

{{4}}
### Professional Boxplot (ggplot2)

```r
# Publication-ready boxplot
ggplot(df, aes(x = factor(cyl), y = mpg, fill = factor(cyl))) +
  geom_boxplot(alpha = 0.7,
               outlier.color = "red",
               outlier.size = 3) +
  stat_summary(fun = mean,
               geom = "point",
               shape = 23,
               size = 3,
               fill = "white",
               color = "black") +
  scale_fill_manual(values = c("4" = "#3498db",
                               "6" = "#e74c3c",
                               "8" = "#2ecc71"),
                    name = "Cylinders") +
  labs(title = "Miles Per Gallon by Cylinder Count",
       subtitle = "Diamond = Mean | Line = Median | Red dots = Outliers",
       x = "Number of Cylinders",
       y = "Miles Per Gallon (MPG)") +
  theme_minimal() +
  theme(legend.position = "none")
```

---

## 📊 Visualization Type 3: Bar Charts

{{1}}
### When to Use Bar Charts

**Purpose:** Display means, counts, or proportions with error bars

**Used in these analyses:**
- ✅ **Descriptive Statistics** (see Descriptive Statistics Learning Nugget)
- ✅ **t-tests** (see t-test Learning Nuggets)
- ✅ **ANOVA** (see ANOVA Learning Nugget)
- ✅ **Frequency Tables** (see Frequency and Cross Tables Learning Nugget)

{{2}}
### Bar Chart with Error Bars (Base R)

```r
# Calculate means and standard errors
library(dplyr)

summary_stats <- df %>%
  group_by(cyl) %>%
  summarise(mean_mpg = mean(mpg),
            se_mpg = sd(mpg) / sqrt(n()))

# Create bar chart
barplot(summary_stats$mean_mpg,
        names.arg = summary_stats$cyl,
        main = "Mean MPG by Cylinder Count",
        xlab = "Number of Cylinders",
        ylab = "Mean MPG",
        col = "steelblue",
        ylim = c(0, max(summary_stats$mean_mpg) + 5))

# Add error bars
arrows(x0 = barplot(summary_stats$mean_mpg, plot = FALSE),
       y0 = summary_stats$mean_mpg - summary_stats$se_mpg,
       y1 = summary_stats$mean_mpg + summary_stats$se_mpg,
       angle = 90,
       code = 3,
       length = 0.1)
```

{{3}}
### Professional Bar Chart (ggplot2)

```r
# Create summary statistics
summary_stats <- df %>%
  group_by(cyl) %>%
  summarise(mean_mpg = mean(mpg),
            se_mpg = sd(mpg) / sqrt(n()),
            sd_mpg = sd(mpg))

# Bar chart with error bars
ggplot(summary_stats, aes(x = factor(cyl), y = mean_mpg, fill = factor(cyl))) +
  geom_bar(stat = "identity",
           alpha = 0.8,
           color = "black") +
  geom_errorbar(aes(ymin = mean_mpg - se_mpg,
                    ymax = mean_mpg + se_mpg),
                width = 0.2,
                linewidth = 1) +
  scale_fill_manual(values = c("4" = "#3498db",
                               "6" = "#e74c3c",
                               "8" = "#2ecc71"),
                    name = "Cylinders") +
  labs(title = "Mean MPG by Number of Cylinders",
       subtitle = "Error bars represent ± 1 SE",
       x = "Number of Cylinders",
       y = "Mean Miles Per Gallon") +
  theme_minimal() +
  theme(legend.position = "none")
```

---

## 🔵 Visualization Type 4: Scatter Plots

{{1}}
### When to Use Scatter Plots

**Purpose:** Show relationships between two continuous variables

**Used in these analyses:**
- ✅ **Correlations** (see Correlation Learning Nugget)
- ✅ **Regression** (see Regression Learning Nugget)

{{2}}
### Basic Scatter Plot (Base R)

```r
# Simple scatter plot
plot(df$wt, df$mpg,
     main = "Relationship Between Weight and MPG",
     xlab = "Weight (1000 lbs)",
     ylab = "Miles Per Gallon",
     pch = 19,
     col = "steelblue")

# Add regression line
abline(lm(mpg ~ wt, data = df), col = "red", lwd = 2)

# Add correlation coefficient
cor_value <- cor(df$wt, df$mpg)
text(x = max(df$wt) * 0.9,
     y = max(df$mpg) * 0.9,
     labels = paste("r =", round(cor_value, 3)),
     col = "red",
     cex = 1.2)
```

{{3}}
### Professional Scatter Plot (ggplot2)

```r
# Calculate correlation
cor_result <- cor.test(df$wt, df$mpg)
cor_text <- paste0("r = ", round(cor_result$estimate, 3),
                  "\np ", ifelse(cor_result$p.value < 0.001,
                               "< .001",
                               paste("=", round(cor_result$p.value, 3))))

# Create scatter plot with regression line
ggplot(df, aes(x = wt, y = mpg)) +
  geom_point(size = 3,
             alpha = 0.7,
             color = "steelblue") +
  geom_smooth(method = "lm",
              se = TRUE,
              color = "red",
              fill = "pink",
              alpha = 0.2) +
  annotate("text",
           x = max(df$wt) * 0.85,
           y = max(df$mpg) * 0.9,
           label = cor_text,
           size = 4.5,
           color = "red") +
  labs(title = "Relationship Between Weight and Fuel Efficiency",
       subtitle = "Linear regression with 95% confidence interval",
       x = "Weight (1000 lbs)",
       y = "Miles Per Gallon (MPG)") +
  theme_minimal()
```

{{4}}
### Grouped Scatter Plot (By Category)

```r
# Scatter plot with groups
ggplot(df, aes(x = wt, y = mpg, color = factor(cyl), shape = factor(cyl))) +
  geom_point(size = 3, alpha = 0.7) +
  geom_smooth(method = "lm", se = FALSE, linewidth = 1) +
  scale_color_manual(values = c("4" = "#3498db",
                                "6" = "#e74c3c",
                                "8" = "#2ecc71"),
                     name = "Cylinders") +
  scale_shape_manual(values = c(16, 17, 18),
                     name = "Cylinders") +
  labs(title = "Weight vs MPG by Cylinder Count",
       x = "Weight (1000 lbs)",
       y = "Miles Per Gallon") +
  theme_minimal()
```

---

## 📉 Visualization Type 5: Line Graphs

{{1}}
### When to Use Line Graphs

**Purpose:** Show trends over time or ordered categories

**Used in these analyses:**
- ✅ **Regression** (showing predicted values)
- ✅ **Descriptive Statistics** (trends over time)
- ✅ **Paired t-tests** (showing before-after changes)

{{2}}
### Line Graph for Paired Data (Base R)

```r
# Example: Before-After comparison
set.seed(123)
subjects <- 1:10
before <- rnorm(10, mean = 100, sd = 15)
after <- before + rnorm(10, mean = 5, sd = 8)

# Plot both lines
plot(subjects, before,
     type = "b",
     pch = 19,
     col = "red",
     ylim = c(min(c(before, after)) - 5, max(c(before, after)) + 5),
     main = "Before-After Comparison",
     xlab = "Subject",
     ylab = "Score")

lines(subjects, after, type = "b", pch = 19, col = "blue")

# Add connecting lines
for (i in 1:length(subjects)) {
  segments(subjects[i], before[i], subjects[i], after[i],
           col = "gray", lty = 2)
}

legend("topleft",
       legend = c("Before", "After"),
       col = c("red", "blue"),
       pch = 19,
       lty = 1)
```

{{3}}
### Professional Line Graph (ggplot2)

```r
# Prepare paired data
paired_data <- data.frame(
  subject = rep(subjects, 2),
  time = factor(rep(c("Before", "After"), each = 10),
                levels = c("Before", "After")),
  score = c(before, after)
)

# Create line graph
ggplot(paired_data, aes(x = time, y = score, group = subject)) +
  geom_line(color = "gray70", alpha = 0.6) +
  geom_point(aes(color = time), size = 3, alpha = 0.8) +
  stat_summary(aes(group = 1),
               fun = mean,
               geom = "line",
               color = "black",
               linewidth = 2) +
  stat_summary(aes(group = 1),
               fun = mean,
               geom = "point",
               color = "black",
               size = 5,
               shape = 18) +
  scale_color_manual(values = c("Before" = "#e74c3c", "After" = "#3498db"),
                     name = "Time Point") +
  labs(title = "Individual Changes: Before vs After Treatment",
       subtitle = "Gray lines = individual subjects | Bold line = group mean",
       x = "Time Point",
       y = "Score") +
  theme_minimal()
```

---

## 📋 Visualization Type 6: Frequency Tables and Bar Charts

{{1}}
### When to Use Frequency Visualizations

**Purpose:** Display counts and proportions of categorical data

**Used in these analyses:**
- ✅ **Frequency Tables** (see Frequency and Cross Tables Learning Nugget)
- ✅ **Cross Tables / Chi-Square Tests**
- ✅ **Descriptive Statistics for categorical variables**

{{2}}
### Simple Frequency Bar Chart (Base R)

```r
# Create frequency table
freq_table <- table(df$cyl)

# Basic bar chart
barplot(freq_table,
        main = "Frequency of Cylinder Counts",
        xlab = "Number of Cylinders",
        ylab = "Frequency",
        col = "steelblue",
        border = "white")

# Add counts on top of bars
text(x = barplot(freq_table, plot = FALSE),
     y = freq_table,
     labels = freq_table,
     pos = 3)
```

{{3}}
### Professional Frequency Chart (ggplot2)

```r
# Calculate frequencies and percentages
freq_data <- as.data.frame(table(df$cyl))
names(freq_data) <- c("cylinders", "count")
freq_data$percentage <- round(freq_data$count / sum(freq_data$count) * 100, 1)

# Create bar chart with percentages
ggplot(freq_data, aes(x = cylinders, y = count, fill = cylinders)) +
  geom_bar(stat = "identity", alpha = 0.8, color = "black") +
  geom_text(aes(label = paste0(count, "\n(", percentage, "%)")),
            vjust = -0.5,
            size = 4) +
  scale_fill_manual(values = c("4" = "#3498db",
                               "6" = "#e74c3c",
                               "8" = "#2ecc71")) +
  labs(title = "Distribution of Cylinder Counts",
       subtitle = "Counts and percentages displayed",
       x = "Number of Cylinders",
       y = "Frequency") +
  theme_minimal() +
  theme(legend.position = "none") +
  ylim(0, max(freq_data$count) * 1.15)
```

{{4}}
### Grouped Bar Chart for Cross Tables

```r
# Create cross-table
cross_table <- as.data.frame(table(df$cyl, df$am))
names(cross_table) <- c("cylinders", "transmission", "count")
cross_table$transmission <- factor(cross_table$transmission,
                                   labels = c("Automatic", "Manual"))

# Grouped bar chart
ggplot(cross_table, aes(x = cylinders, y = count, fill = transmission)) +
  geom_bar(stat = "identity",
           position = position_dodge(width = 0.9),
           alpha = 0.8,
           color = "black") +
  geom_text(aes(label = count),
            position = position_dodge(width = 0.9),
            vjust = -0.5,
            size = 3.5) +
  scale_fill_manual(values = c("Automatic" = "#3498db",
                               "Manual" = "#e74c3c"),
                    name = "Transmission") +
  labs(title = "Cross-Tabulation: Cylinders by Transmission Type",
       x = "Number of Cylinders",
       y = "Frequency") +
  theme_minimal()
```

---

## 🎨 Visualization Type 7: Heatmaps for Correlation Matrices

{{1}}
### When to Use Heatmaps

**Purpose:** Visualize multiple correlations simultaneously

**Used in these analyses:**
- ✅ **Correlation Analysis** (see Correlation Learning Nugget)
- ✅ **Exploring relationships between many variables**

{{2}}
### Basic Correlation Heatmap

```r
# Calculate correlation matrix
cor_matrix <- cor(df[, c("mpg", "cyl", "disp", "hp", "wt")])

# Install and load required package
# install.packages("corrplot")
library(corrplot)

# Create heatmap
corrplot(cor_matrix,
         method = "color",
         type = "upper",
         addCoef.col = "black",
         tl.col = "black",
         tl.srt = 45,
         title = "Correlation Matrix Heatmap",
         mar = c(0, 0, 2, 0))
```

{{3}}
### Professional Heatmap (ggplot2)

```r
# Prepare correlation matrix for ggplot
library(reshape2)
cor_melted <- melt(cor_matrix)

# Create heatmap
ggplot(cor_melted, aes(x = Var1, y = Var2, fill = value)) +
  geom_tile(color = "white") +
  geom_text(aes(label = round(value, 2)),
            color = "black",
            size = 4) +
  scale_fill_gradient2(low = "#e74c3c",
                       mid = "white",
                       high = "#3498db",
                       midpoint = 0,
                       limit = c(-1, 1),
                       name = "Correlation") +
  labs(title = "Correlation Matrix with Significance Levels",
       subtitle = "* p < .05 | ** p < .01 | *** p < .001",
       x = "",
       y = "") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

---

## 📊 Visualization Type 8: Q-Q Plots (Normality Assessment)

{{1}}
### When to Use Q-Q Plots

**Purpose:** Visually assess if data follows a normal distribution

**Used in these analyses:**
- ✅ **Normality Checks** (see Normality Check Learning Nugget)
- ✅ **Before conducting t-tests, ANOVA, or regression**

{{2}}
### Basic Q-Q Plot (Base R)

```r
# Simple Q-Q plot
qqnorm(df$mpg,
       main = "Q-Q Plot: Miles Per Gallon",
       pch = 19,
       col = "steelblue")
qqline(df$mpg,
       col = "red",
       lwd = 2)

# Add text interpretation
if (shapiro.test(df$mpg)$p.value > 0.05) {
  mtext("Points follow line → Normal distribution ✓",
        side = 3,
        line = 0.5,
        col = "darkgreen")
} else {
  mtext("Points deviate from line → Non-normal distribution ✗",
        side = 3,
        line = 0.5,
        col = "darkred")
}
```

{{3}}
### Professional Q-Q Plot (ggplot2)

```r
# Create Q-Q plot with ggplot2
ggplot(df, aes(sample = mpg)) +
  stat_qq(color = "steelblue", size = 3, alpha = 0.7) +
  stat_qq_line(color = "red", linewidth = 1) +
  labs(title = "Q-Q Plot: Assessing Normality of MPG",
       subtitle = "Points should fall along the red line for normal distribution",
       x = "Theoretical Quantiles",
       y = "Sample Quantiles") +
  theme_minimal()
```

---

## 🎯 Visualization Type 9: Violin Plots

{{1}}
### When to Use Violin Plots

**Purpose:** Combine boxplot information with distribution shape

**Used in these analyses:**
- ✅ **Descriptive Statistics**
- ✅ **t-tests** (see t-test Learning Nuggets)
- ✅ **ANOVA** (see ANOVA Learning Nugget)
- ✅ **Alternative to boxplots when distribution shape is important**

{{2}}
### Basic Violin Plot (ggplot2)

```r
# Simple violin plot
ggplot(df, aes(x = factor(cyl), y = mpg, fill = factor(cyl))) +
  geom_violin(alpha = 0.7, trim = FALSE) +
  scale_fill_manual(values = c("4" = "#3498db",
                               "6" = "#e74c3c",
                               "8" = "#2ecc71"),
                    name = "Cylinders") +
  labs(title = "Distribution of MPG by Cylinder Count",
       subtitle = "Violin plot shows distribution shape",
       x = "Number of Cylinders",
       y = "Miles Per Gallon") +
  theme_minimal() +
  theme(legend.position = "none")
```

{{3}}
### Violin Plot with Boxplot Overlay

```r
# Combine violin and boxplot
ggplot(df, aes(x = factor(cyl), y = mpg, fill = factor(cyl))) +
  geom_violin(alpha = 0.5, trim = FALSE) +
  geom_boxplot(width = 0.2, alpha = 0.8, outlier.shape = NA) +
  stat_summary(fun = mean,
               geom = "point",
               shape = 23,
               size = 3,
               fill = "white",
               color = "black") +
  scale_fill_manual(values = c("4" = "#3498db",
                               "6" = "#e74c3c",
                               "8" = "#2ecc71"),
                    name = "Cylinders") +
  labs(title = "MPG Distribution by Cylinder Count",
       subtitle = "Violin = distribution | Box = quartiles | Diamond = mean",
       x = "Number of Cylinders",
       y = "Miles Per Gallon") +
  theme_minimal() +
  theme(legend.position = "none")
```

---

## 📈 Visualization Type 10: Regression Diagnostic Plots

{{1}}
### When to Use Diagnostic Plots

**Purpose:** Check regression assumptions and model fit

**Used in these analyses:**
- ✅ **Regression Analysis** (see Regression Learning Nugget)
- ✅ **Verifying linear model assumptions**

{{2}}
### Standard Regression Diagnostics (Base R)

```r
# Fit regression model
model <- lm(mpg ~ wt + hp, data = df)

# Create all four diagnostic plots
par(mfrow = c(2, 2))
plot(model)
par(mfrow = c(1, 1))
```

{{3}}
### Individual Diagnostic Plots with Explanations

```r
# 1. Residuals vs Fitted (Linearity check)
par(mfrow = c(2, 2))

plot(model, which = 1,
     main = "1. Residuals vs Fitted\nCheck: Linearity",
     col = "steelblue",
     pch = 19)

# 2. Q-Q Plot (Normality of residuals)
plot(model, which = 2,
     main = "2. Q-Q Plot\nCheck: Normal residuals",
     col = "steelblue",
     pch = 19)

# 3. Scale-Location (Homoscedasticity)
plot(model, which = 3,
     main = "3. Scale-Location\nCheck: Equal variance",
     col = "steelblue",
     pch = 19)

# 4. Residuals vs Leverage (Influential points)
plot(model, which = 5,
     main = "4. Residuals vs Leverage\nCheck: Influential cases",
     col = "steelblue",
     pch = 19)

par(mfrow = c(1, 1))
```

{{4}}
### Professional Diagnostic Plots (ggplot2)

```r
# Install and load required package
# install.packages("ggfortify")
library(ggfortify)

# Create all diagnostic plots
autoplot(model,
         which = 1:4,
         colour = "steelblue",
         smooth.colour = "red",
         smooth.linetype = "dashed",
         ad.colour = "blue",
         label.size = 3,
         label.n = 3) +
  theme_minimal()
```

---

## 🎯 Quick Reference: Analysis Method → Visualization

---

## 🎯 Quick Reference: Analysis Method → Visualization

{{1}}
### Complete Mapping Guide

| Analysis Method | Primary Visualization | Secondary Visualization | Learning Nugget |
|:----------------|:---------------------|:----------------------|:----------------|
| **Descriptive Statistics** | Histogram, Boxplot | Bar chart (means), Violin plot | Descriptive Statistics Nugget |
| **Normality Check** | Histogram, Q-Q Plot | Boxplot | Normality Check Nugget |
| **Paired t-test** | Line graph (before-after), Boxplot | Violin plot | Paired t-test Nugget |
| **Unpaired t-test** | Boxplot, Bar chart (means) | Violin plot | Unpaired t-test Nugget |
| **ANOVA** | Boxplot, Bar chart (means) | Violin plot | ANOVA Nugget |
| **Correlation** | Scatter plot, Correlation heatmap | Scatter plot matrix | Correlation Nugget |
| **Regression** | Scatter plot with line, Diagnostic plots | Residual plots | Regression Nugget |
| **Frequency Tables** | Bar chart | Pie chart (avoid!) | Frequency Tables Nugget |
| **Cross Tables** | Grouped bar chart, Stacked bar chart | Mosaic plot | Cross Tables Nugget |

---

## 🎨 Customization Tips for Publication-Quality Figures

{{1}}
### Color Palettes

```r
# Professional color schemes
# Colorblind-friendly palette
cb_palette <- c("#E69F00", "#56B4E9", "#009E73", "#F0E442",
                "#0072B2", "#D55E00", "#CC79A7", "#999999")

# Use in ggplot
ggplot(df, aes(x = factor(cyl), y = mpg, fill = factor(cyl))) +
  geom_boxplot() +
  scale_fill_manual(values = cb_palette[1:3])

# Grayscale for publications
gray_palette <- c("gray90", "gray60", "gray30")

# Using RColorBrewer
library(RColorBrewer)
display.brewer.all(colorblindFriendly = TRUE)

# Apply brewer palette
ggplot(df, aes(x = factor(cyl), y = mpg, fill = factor(cyl))) +
  geom_boxplot() +
  scale_fill_brewer(palette = "Set2")
```

{{2}}
### Font and Text Customization

```r
# Set global text size
theme_set(theme_minimal(base_size = 14))

# Custom font sizes for specific elements
ggplot(df, aes(x = wt, y = mpg)) +
  geom_point() +
  labs(title = "Title in Large Font",
       subtitle = "Subtitle in medium font",
       x = "X-axis Label",
       y = "Y-axis Label") +
  theme_minimal() +
  theme(
    plot.title = element_text(size = 18, face = "bold"),
    plot.subtitle = element_text(size = 12),
    axis.title = element_text(size = 14, face = "bold"),
    axis.text = element_text(size = 12)
  )
```

{{3}}
### Aspect Ratio and Size

```r
# Set figure dimensions
plot <- ggplot(df, aes(x = wt, y = mpg)) +
  geom_point() +
  theme_minimal()

# Save with specific dimensions
ggsave("figure.png",
       plot = plot,
       width = 8,    # inches
       height = 6,   # inches
       dpi = 300)    # resolution

# Common sizes:
# - Single column (journal): width = 3.5 inches
# - Double column (journal): width = 7 inches
# - Presentation slide: width = 10 inches
# - Poster: width = 12+ inches
```

{{4}}
### Exporting for Different Purposes

```r
# For presentations (PowerPoint)
ggsave("presentation_figure.png",
       width = 10,
       height = 7.5,
       dpi = 150,
       bg = "white")

# For publication (journal submission)
ggsave("publication_figure.pdf",
       width = 7,
       height = 5,
       dpi = 300)

# For thesis/report
ggsave("thesis_figure.tiff",
       width = 6,
       height = 4.5,
       dpi = 600,
       compression = "lzw")

# For web/online
ggsave("web_figure.png",
       width = 8,
       height = 6,
       dpi = 96)
```

{{5}}
### Adding Statistical Annotations

```r
# Install ggpubr for statistical annotations
library(ggpubr)

# Add p-values to plots
ggplot(df, aes(x = factor(cyl), y = mpg, fill = factor(cyl))) +
  geom_boxplot(alpha = 0.7) +
  stat_compare_means(method = "anova",
                     label.y = max(df$mpg) + 2) +
  stat_compare_means(comparisons = list(c("4", "6"),
                                       c("6", "8"),
                                       c("4", "8")),
                     method = "t.test",
                     label = "p.signif") +
  scale_fill_manual(values = c("#3498db", "#e74c3c", "#2ecc71")) +
  labs(title = "MPG Comparison with Statistical Tests",
       x = "Cylinders",
       y = "Miles Per Gallon") +
  theme_minimal() +
  theme(legend.position = "none")
```

---

## ✅ Best Practices Checklist

{{1}}
**Before Creating Visualizations:**
- [ ] Understand your data type (continuous, categorical, paired)
- [ ] Know which analysis you're conducting
- [ ] Decide on your audience (scientific paper, presentation, report)
- [ ] Check for missing values and outliers

{{2}}
**During Visualization Creation:**
- [ ] Choose the appropriate plot type for your analysis
- [ ] Use clear, descriptive labels for axes and titles
- [ ] Include sample size information when relevant
- [ ] Use colorblind-friendly palettes
- [ ] Ensure adequate font sizes (minimum 10pt for publication)
- [ ] Add error bars or confidence intervals where appropriate
- [ ] Include legends when comparing groups

{{3}}
**For Statistical Visualizations:**
- [ ] Always show both raw data and summary statistics when possible
- [ ] Include confidence intervals or standard errors
- [ ] Report p-values or significance levels when relevant
- [ ] Show sample sizes for each group
- [ ] Indicate which statistical test was used

{{4}}
**For Publication and Presentation:**
- [ ] Export at appropriate resolution (300 dpi for print, 150 dpi for screen)
- [ ] Use consistent color schemes across figures
- [ ] Ensure figures are readable in grayscale (if required)
- [ ] Save in multiple formats (PNG, PDF, TIFF)
- [ ] Check file size (compress if needed for online submission)
- [ ] Include figure captions with complete information

{{5}}
**Common Mistakes to Avoid:**
- ✗ Using pie charts (use bar charts instead)
- ✗ 3D plots (they distort perception)
- ✗ Too many colors or patterns
- ✗ Truncated y-axes (can mislead)
- ✗ Missing error bars on bar charts
- ✗ Unclear or missing labels
- ✗ Too much information in one plot
- ✗ Low resolution exports
- ✗ Non-colorblind-friendly colors

---

## 📝 Reporting Visualizations in Research

{{1}}
### Writing Figure Captions

**Good figure captions should include:**
1. **What** is being shown
2. **Sample size** (n)
3. **What symbols/colors represent**
4. **What error bars represent** (SE, SD, or CI)
5. **Statistical test results** (if applicable)

{{2}}
### Example Figure Captions

**Example 1: Boxplot for t-test**
> "**Figure 1.** Comparison of miles per gallon (MPG) between automatic (n = 19) and manual (n = 13) transmission vehicles. Box represents interquartile range (IQR), horizontal line shows median, diamond indicates mean, and whiskers extend to 1.5 × IQR. Individual data points shown as dots. Independent samples t-test revealed significant difference, t(30) = -3.77, p < .001."

**Example 2: Scatter plot for correlation**
> "**Figure 2.** Scatter plot showing relationship between vehicle weight (1000 lbs) and fuel efficiency (MPG) for n = 32 vehicles. Red line represents linear regression with 95% confidence interval (shaded area). Pearson correlation: r = -.87, p < .001, indicating strong negative relationship."

**Example 3: Bar chart for ANOVA**
> "**Figure 3.** Mean miles per gallon (±SE) for vehicles with 4, 6, and 8 cylinders (n = 11, 7, and 14, respectively). One-way ANOVA indicated significant differences between groups, F(2, 29) = 39.7, p < .001. Post-hoc Tukey HSD tests showed all pairwise comparisons were significant (p < .05)."

{{3}}
### In-Text Reporting

**When describing visualizations in text:**

> "Figure 1 presents the distribution of MPG values across transmission types. Visual inspection revealed approximately normal distributions in both groups, with manual transmissions showing higher mean MPG (M = 24.4, SD = 6.2) compared to automatic transmissions (M = 17.1, SD = 3.8)."

> "As shown in Figure 2, a strong negative correlation was observed between vehicle weight and fuel efficiency (r = -.87, p < .001), with heavier vehicles consistently achieving lower MPG ratings."

---

## 💡 Pro Tips and Tricks

{{1}}
### Tip 1: Use Consistent Styling Across Figures

```r
# Create a custom theme for all your plots
my_theme <- theme_minimal() +
  theme(
    plot.title = element_text(size = 14, face = "bold"),
    plot.subtitle = element_text(size = 11),
    axis.title = element_text(size = 12, face = "bold"),
    axis.text = element_text(size = 10),
    legend.title = element_text(size = 11, face = "bold"),
    legend.text = element_text(size = 10),
    panel.grid.minor = element_blank()
  )

# Apply to all plots
ggplot(df, aes(x = wt, y = mpg)) +
  geom_point() +
  my_theme
```

{{2}}
### Tip 2: Create a Color Palette for Your Project

```r
# Define project colors
project_colors <- list(
  primary = "#3498db",
  secondary = "#e74c3c",
  tertiary = "#2ecc71",
  quaternary = "#f39c12",
  gray = "#7f8c8d"
)

# Use consistently
ggplot(df, aes(x = factor(cyl), y = mpg, fill = factor(cyl))) +
  geom_boxplot() +
  scale_fill_manual(values = c("4" = project_colors$primary,
                               "6" = project_colors$secondary,
                               "8" = project_colors$tertiary))
```

{{3}}
### Tip 3: Save Your Plot Specifications

```r
# Save plot dimensions and settings
save_plot <- function(plot, filename, type = "publication") {
  if (type == "publication") {
    ggsave(filename, plot = plot,
           width = 7, height = 5, dpi = 300)
  } else if (type == "presentation") {
    ggsave(filename, plot = plot,
           width = 10, height = 7.5, dpi = 150)
  } else if (type == "poster") {
    ggsave(filename, plot = plot,
           width = 12, height = 9, dpi = 300)
  }
}

# Use the function
my_plot <- ggplot(df, aes(x = wt, y = mpg)) + geom_point()
save_plot(my_plot, "figure1.png", type = "publication")
```

{{4}}
### Tip 4: Quick Data Exploration Function

```r
# Create a function for quick visual exploration
quick_explore <- function(data, variable, group = NULL) {
  
  if (is.null(group)) {
    # Single variable exploration
    p1 <- ggplot(data, aes(x = .data[[variable]])) +
      geom_histogram(bins = 10, fill = "steelblue", alpha = 0.7) +
      labs(title = paste("Distribution of", variable)) +
      theme_minimal()
    
    p2 <- ggplot(data, aes(sample = .data[[variable]])) +
      stat_qq() + stat_qq_line() +
      labs(title = "Q-Q Plot") +
      theme_minimal()
    
    p3 <- ggplot(data, aes(y = .data[[variable]])) +
      geom_boxplot(fill = "steelblue", alpha = 0.7) +
      labs(title = "Boxplot") +
      theme_minimal()
    
    library(patchwork)
    return((p1 | p2) / p3)
    
  } else {
    # Grouped exploration
    p1 <- ggplot(data, aes(x = .data[[group]], y = .data[[variable]], 
                          fill = .data[[group]])) +
      geom_boxplot(alpha = 0.7) +
      labs(title = paste(variable, "by", group)) +
      theme_minimal() +
      theme(legend.position = "none")
    
    p2 <- ggplot(data, aes(x = .data[[variable]], fill = .data[[group]])) +
      geom_histogram(bins = 10, alpha = 0.6, position = "identity") +
      labs(title = "Distributions by Group") +
      theme_minimal()
    
    return(p1 / p2)
  }
}

# Use the function
quick_explore(df, "mpg")
quick_explore(df, "mpg", group = "cyl")
```

{{5}}
### Tip 5: Automate Figure Numbering

```r
# Create a figure counter
fig_num <- 1

add_figure_label <- function(plot, caption) {
  labeled_plot <- plot +
    labs(caption = paste0("Figure ", fig_num, ". ", caption))
  
  fig_num <<- fig_num + 1
  return(labeled_plot)
}

# Use it
p1 <- ggplot(df, aes(x = mpg)) + geom_histogram()
p1 <- add_figure_label(p1, "Distribution of MPG values")

p2 <- ggplot(df, aes(x = wt, y = mpg)) + geom_point()
p2 <- add_figure_label(p2, "Relationship between weight and MPG")
```

---

## 🎯 Final Checklist for Your Figures

{{1}}
### Before Submitting Your Work

**Content Completeness:**
- [ ] Figure clearly shows the intended comparison or relationship
- [ ] All axes are labeled with units (if applicable)
- [ ] Title is descriptive and informative
- [ ] Legend is included (if needed) and clearly labeled
- [ ] Sample sizes are indicated
- [ ] Statistical test results are shown (if relevant)

**Visual Quality:**
- [ ] Resolution is appropriate (300 dpi for publication)
- [ ] Colors are distinguishable and colorblind-friendly
- [ ] Text is readable (minimum 10pt font)
- [ ] Figure maintains clarity when printed in grayscale
- [ ] Background is white or transparent
- [ ] No overlapping elements

**Statistical Integrity:**
- [ ] Error bars are clearly defined (SE, SD, or CI)
- [ ] Appropriate visualization for the statistical test used
- [ ] Assumptions verified (e.g., normality for parametric tests)
- [ ] All data points visible (or distribution shown)
- [ ] No misleading scales or truncated axes

**Documentation:**
- [ ] Figure caption is complete and informative
- [ ] Figure is numbered correctly
- [ ] Referenced in the main text
- [ ] Source code is saved for reproducibility
- [ ] Raw data is available

---

## 💬 Summary and Key Takeaways

{{1}}
### The Golden Rules of Data Visualization

1. **Match visualization to analysis type**
   - Each statistical method has preferred visualizations
   - Refer to the Quick Reference table in this nugget

2. **Show your data, not just summaries**
   - Include individual points when possible
   - Use violin plots or boxplots to show distribution shape

3. **Make it accessible**
   - Use colorblind-friendly palettes
   - Ensure adequate contrast and font sizes
   - Test in grayscale

4. **Be consistent**
   - Use the same colors and styles across all figures
   - Maintain uniform formatting

5. **Prioritize clarity over complexity**
   - One clear message per figure
   - Remove unnecessary elements
   - Use whitespace effectively

{{2}}
### Quick Command Reference

```r
# ═══════════════════════════════════════════
# ESSENTIAL PLOTTING COMMANDS
# ═══════════════════════════════════════════

# Histogram
ggplot(df, aes(x = variable)) +
  geom_histogram(bins = 10)

# Boxplot
ggplot(df, aes(x = group, y = variable)) +
  geom_boxplot()

# Scatter plot
ggplot(df, aes(x = var1, y = var2)) +
  geom_point() +
  geom_smooth(method = "lm")

# Bar chart with error bars
ggplot(summary_df, aes(x = group, y = mean)) +
  geom_bar(stat = "identity") +
  geom_errorbar(aes(ymin = mean - se, ymax = mean + se))

# Q-Q plot
ggplot(df, aes(sample = variable)) +
  stat_qq() +
  stat_qq_line()

# Violin plot
ggplot(df, aes(x = group, y = variable)) +
  geom_violin() +
  geom_boxplot(width = 0.2)

# Save plot
ggsave("filename.png", width = 8, height = 6, dpi = 300)
```

{{3}}
### Remember

> **"A good visualization is worth a thousand statistical tables."**
> 
> Your figures should tell a clear story that supports your statistical findings. Always start with simple exploratory plots, verify assumptions visually, then create publication-ready figures that communicate your results effectively.

{{4}}
### Next Steps

1. **Practice** with your own datasets
2. **Refer** to specific analysis learning nuggets for procedures
3. **Experiment** with different plot types
4. **Seek feedback** on your visualizations
5. **Build** a personal library of plotting functions

---

## 📞 Getting Help

{{1}}
### When You Need Assistance

**For R code issues:**
- Check R documentation: `?function_name`
- ggplot2 reference: https://ggplot2.tidyverse.org/
- Stack Overflow: Tag your questions with [r] and [ggplot2]

**For statistical interpretation:**
- Refer to the specific analysis learning nugget
- Consult with your supervisor or teaching team
- Review relevant statistical textbooks

**For visualization design:**
- "Fundamentals of Data Visualization" by Claus O. Wilke (free online)
- "Data Visualization: A Practical Introduction" by Kieran Healy
- R Graphics Cookbook: https://r-graphics.org/

{{2}}
### Contact Information

**Teaching Team:**
- **Hannes Tegelbeckers:** hannes.tegelbeckers@ovgu.de
- **Office Hours:** https://cloud.ovgu.de/call/ciz2te64

**Remember:** Good visualizations take practice. Don't be discouraged if your first attempts aren't perfect. Keep experimenting, and always seek feedback!

---

## 🎓 Practice Exercises

Remember: Visualization is both an art and a science. Practice regularly, seek feedback, and always prioritize clarity and honesty in representing your data. Good luck with your research! 🚀(low = "#e74c3c",
                       mid = "white",
                       high = "#3498db",
                       midpoint = 0,
                       limit = c(-1, 1),
                       name = "Correlation") +
  labs(title = "Correlation Matrix Heatmap",
       subtitle = "Blue = positive | Red = negative",
       x = "",
       y = "") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

{{4}}
### Heatmap with Significance Indicators

```r
# Function to calculate correlation with p-values
cor_test_matrix <- function(mat) {
  mat <- as.matrix(mat)
  n <- ncol(mat)
  p_mat <- matrix(NA, n, n)
  diag(p_mat) <- 0
  
  for (i in 1:(n - 1)) {
    for (j in (i + 1):n) {
      test <- cor.test(mat[, i], mat[, j])
      p_mat[i, j] <- p_mat[j, i] <- test$p.value
    }
  }
  
  colnames(p_mat) <- rownames(p_mat) <- colnames(mat)
  return(p_mat)
}

# Calculate p-values
p_matrix <- cor_test_matrix(df[, c("mpg", "cyl", "disp", "hp", "wt")])
p_melted <- melt(p_matrix)
cor_melted$p_value <- p_melted$value
cor_melted$sig <- ifelse(cor_melted$p_value < 0.001, "***",
                        ifelse(cor_melted$p_value < 0.01, "**",
                              ifelse(cor_melted$p_value < 0.05, "*", "")))

# Plot with significance stars
ggplot(cor_melted, aes(x = Var1, y = Var2, fill = value)) +
  geom_tile(color = "white") +
  geom_text(aes(label = paste0(round(value, 2), "\n", sig)),
            color = "black",
            size = 3.5) +
  scale_fill_gradient2
