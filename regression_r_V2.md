# Multiple Linear Regression in R - Interactive Learning Nugget

## 📊 Introduction to Regression Analysis

> **Learning Objectives:**  
> By the end of this learning nugget, you will be able to:
> - Understand different types of regression and when to use them
> - Perform multiple linear regression in R
> - Check and interpret regression assumptions
> - Evaluate model quality and fit
> - Interpret regression coefficients and statistical significance
> - Report regression results in APA format
> - Create effective visualizations for regression models

---

## What is Regression Analysis?

{{1}}
Regression analysis examines the relationship between one **dependent variable (DV)** and one or more **independent variables (IVs)**. It helps us:

- **Predict** outcomes based on predictor variables
- **Explain** how much variance in the DV is explained by IVs
- **Identify** which predictors are statistically significant
- **Quantify** the strength and direction of relationships

{{2}}
> **Important:** Regression analysis identifies **associations**, not necessarily **causation**. Careful research design and theory are needed to make causal claims.

---

## 🔍 Types of Regression: Overview


### Simple Linear Regression

**Formula:** Y = β₀ + β₁X + ε

- **One** independent variable
- **One** continuous dependent variable
- Examines straight-line relationship

**Example:** Predicting salary based on years of experience

```r
model <- lm(salary ~ experience, data = df)
```


**When to use:**
- ✅ One predictor variable
- ✅ Linear relationship between X and Y
- ✅ When you want to understand one specific relationship

---


### Multiple Linear Regression

**Formula:** Y = β₀ + β₁X₁ + β₂X₂ + ... + βₖXₖ + ε

- **Multiple** independent variables
- **One** continuous dependent variable
- Examines combined effects of several predictors

**Example:** Predicting salary based on experience, education, and age

```r
model <- lm(salary ~ experience + education + age, data = df)
```


**When to use:**
- ✅ Multiple predictor variables
- ✅ Want to control for confounding variables
- ✅ Need to identify the unique contribution of each predictor
- ✅ Real-world scenarios (most phenomena have multiple causes)

---


### Logistic Regression

**Formula:** log(p/(1-p)) = β₀ + β₁X₁ + β₂X₂ + ... + βₖXₖ

- One or multiple independent variables
- **Binary** dependent variable (0/1, Yes/No)
- Predicts probability of an event

**Example:** Predicting whether a customer will buy (yes/no) based on age and income

```r
model <- glm(purchase ~ age + income, data = df, family = "binomial")
```


**When to use:**
- ✅ DV is binary/dichotomous
- ✅ Predicting group membership or event occurrence
- ✅ Examples: disease diagnosis, customer churn, exam pass/fail

---


### Polynomial Regression

**Formula:** Y = β₀ + β₁X + β₂X² + β₃X³ + ... + ε

- Captures **non-linear** curved relationships
- Uses powers (quadratic, cubic) of predictor variables

**Example:** Relationship between age and productivity (inverted U-shape)

```r
model <- lm(productivity ~ age + I(age^2), data = df)
```


**When to use:**
- ✅ Relationship is curved, not straight
- ✅ Scatter plot shows U-shape or inverted U-shape
- ✅ Theory suggests non-linear relationship

---


### Quick Reference: Which Regression?

| Dependent Variable Type | Number of IVs | Relationship Shape | Use This Regression |
|:------------------------|:--------------|:-------------------|:-------------------|
| Continuous | One | Linear | Simple Linear |
| Continuous | Multiple | Linear | **Multiple Linear** |
| Binary (0/1) | One or more | — | Logistic |
| Continuous | One or more | Curved | Polynomial |
| Count (0,1,2,3...) | One or more | — | Poisson |
| Ordinal categories | One or more | — | Ordinal Logistic |


> **This learning nugget focuses on Multiple Linear Regression**, the most commonly used regression type in quantitative research.

---

## 🎯 Assumptions of Multiple Linear Regression

Before performing regression, your data must meet these assumptions:

### 1. **Linearity**
The relationship between each IV and the DV is linear (straight-line).

**How to check:** Scatter plots of each IV against DV


### 2. **Independence of Observations**
Observations are independent (no autocorrelation).

**How to check:** Study design, Durbin-Watson test


### 3. **Multicollinearity**
IVs are not too highly correlated with each other.

**How to check:** Variance Inflation Factor (VIF < 10)


### 4. **Homoscedasticity**
Variance of residuals is constant across all levels of IVs.

**How to check:** Residuals vs Fitted plot, Breusch-Pagan test


### 5. **Normality of Residuals**
Residuals (prediction errors) are normally distributed.

**How to check:** Q-Q plot, Shapiro-Wilk test  
**See:** "Normality Check" learning nugget


### 6. **No Influential Outliers**
Extreme values don't disproportionately affect the model.

**How to check:** Cook's Distance, leverage plots


> **Note:** We will check all these assumptions step-by-step in the analysis workflow below.

---

## 🔧 Setting Up Your R Environment


### Step 1: Create Your Project Structure

```r
# Create a new R Project
# File → New Project → New Directory → New Project
# Name it: 2025_YourSurname_Quant_Methodology

# Create a script for regression analysis
# File → New File → R Script
# Save it as: regression_analysis_yoursurname.R
```


### Step 2: Install and Load Required Packages

```r
# Install packages (only needed once)
install.packages("car")         # For VIF and assumption tests
install.packages("lmtest")      # For Breusch-Pagan test
install.packages("ggplot2")     # For visualizations
install.packages("GGally")      # For pairs plots
install.packages("broom")       # For tidy model output

# Load packages (needed every session)
library(car)
library(lmtest)
library(ggplot2)
library(GGally)
library(broom)
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

# Check structure
str(df)

# Check for missing values
sum(is.na(df))
```

---

## 📈 Example Research Question

{{1}}
**Research Question:**  
*"Can we predict a car's fuel efficiency (mpg) based on its weight (wt), horsepower (hp), and number of cylinders (cyl)?"*

- **Dependent Variable (DV):** `mpg` (miles per gallon) - continuous
- **Independent Variables (IVs):** 
  - `wt` (weight in 1000 lbs) - continuous
  - `hp` (horsepower) - continuous
  - `cyl` (number of cylinders) - continuous (can be treated as continuous or categorical)

{{2}}
**Hypotheses:**
- H₁: Vehicle weight negatively predicts fuel efficiency
- H₂: Horsepower negatively predicts fuel efficiency
- H₃: Number of cylinders negatively predicts fuel efficiency

---

## 📊 Step 1: Exploratory Data Analysis


### Descriptive Statistics

```r
# Descriptive statistics for all variables
summary(df[, c("mpg", "wt", "hp", "cyl")])

# More detailed descriptives
library(psych)
describe(df[, c("mpg", "wt", "hp", "cyl")])
```


> **See the "Descriptive Statistics" learning nugget for detailed guidance on describing your variables.**


### Visualize Relationships

```r
# Scatter plot matrix
pairs(~ mpg + wt + hp + cyl, data = df,
      main = "Scatter Plot Matrix")

# Enhanced version with ggpairs
library(GGally)
ggpairs(df[, c("mpg", "wt", "hp", "cyl")])
```


### Check Linearity Visually

```r
# Individual scatter plots
par(mfrow = c(2, 2))  # 2x2 grid

plot(df$wt, df$mpg, main = "MPG vs Weight",
     xlab = "Weight (1000 lbs)", ylab = "MPG", pch = 19, col = "blue")
abline(lm(mpg ~ wt, data = df), col = "red", lwd = 2)

plot(df$hp, df$mpg, main = "MPG vs Horsepower",
     xlab = "Horsepower", ylab = "MPG", pch = 19, col = "blue")
abline(lm(mpg ~ hp, data = df), col = "red", lwd = 2)

plot(df$cyl, df$mpg, main = "MPG vs Cylinders",
     xlab = "Cylinders", ylab = "MPG", pch = 19, col = "blue")
abline(lm(mpg ~ cyl, data = df), col = "red", lwd = 2)

par(mfrow = c(1, 1))  # Reset
```


**What to look for:**
- Do the relationships appear linear (straight-line)?
- Are there obvious outliers?
- Is there a clear direction (positive/negative)?

---

## 🔨 Step 2: Build the Regression Model


### Fit the Multiple Linear Regression Model

```r
# Build the model
model <- lm(mpg ~ wt + hp + cyl, data = df)

# View model summary
summary(model)
```


### Understanding the Output

The `summary()` function provides:

1. **Call:** The model formula
2. **Residuals:** Summary statistics of prediction errors
3. **Coefficients table:** 
   - Estimate: β coefficients
   - Std. Error: Standard error of coefficients
   - t value: Test statistic
   - Pr(>|t|): p-value (significance)
4. **Residual standard error:** Model's prediction error
5. **Multiple R-squared:** Proportion of variance explained
6. **Adjusted R-squared:** R² adjusted for number of predictors
7. **F-statistic:** Overall model significance


### Extract Key Statistics

```r
# Model coefficients
coef(model)

# Confidence intervals for coefficients
confint(model)

# Model fit statistics
summary(model)$r.squared       # R²
summary(model)$adj.r.squared   # Adjusted R²
summary(model)$sigma           # Residual standard error

# ANOVA table
anova(model)
```


### Create a Tidy Output Table

```r
# Using broom package
library(broom)

# Tidy coefficients
tidy(model)

# Model fit statistics
glance(model)

# Observation-level statistics
augment(model)
```

---

## ✅ Step 3: Check Regression Assumptions


### Assumption 1: Linearity

**Method 1: Residual vs Fitted Plot**

```r
# Base R diagnostic plots
plot(model, which = 1)
```

**Interpretation:**
- ✅ Good: Random scatter around horizontal line at 0
- ❌ Problem: Curved pattern suggests non-linearity


**Method 2: Component-Residual Plots**

```r
# Component + residual plots
library(car)
crPlots(model)
```

**Interpretation:**
- ✅ Good: Blue line follows pink dashed line closely
- ❌ Problem: Blue line curves away from pink line

---


### Assumption 2: Independence of Observations

```r
# Durbin-Watson test for autocorrelation
library(car)
durbinWatsonTest(model)
```

**Interpretation:**
- DW statistic should be close to 2 (range: 0-4)
- p-value > 0.05: No significant autocorrelation ✅
- p-value < 0.05: Autocorrelation present ❌

**Note:** This is mainly relevant for time-series data. For cross-sectional data, independence usually comes from proper research design.

---


### Assumption 3: No Multicollinearity

```r
# Variance Inflation Factor (VIF)
library(car)
vif(model)

# Tolerance (1/VIF)
1 / vif(model)
```

**Interpretation:**
- ✅ VIF < 5: No problematic multicollinearity
- ⚠️ VIF 5-10: Moderate multicollinearity
- ❌ VIF > 10: Severe multicollinearity (remove or combine variables)

**Tolerance:**
- ✅ Tolerance > 0.2: Acceptable
- ❌ Tolerance < 0.1: Problematic


**Additional Check: Correlation Matrix**

```r
# Correlation matrix of IVs
cor(df[, c("wt", "hp", "cyl")])

# Visual correlation matrix
library(corrplot)
corrplot(cor(df[, c("wt", "hp", "cyl")]), method = "number")
```

**Interpretation:**
- ✅ Correlations < 0.8: Generally acceptable
- ❌ Correlations > 0.9: Very high, consider removing one variable

---


### Assumption 4: Homoscedasticity

**Method 1: Scale-Location Plot**

```r
# Visual check
plot(model, which = 3)
```

**Interpretation:**
- ✅ Good: Horizontal line with random scatter
- ❌ Problem: Funnel shape or pattern


**Method 2: Breusch-Pagan Test**

```r
# Statistical test
library(lmtest)
bptest(model)
```

**Interpretation:**
- p-value > 0.05: Homoscedasticity assumption met ✅
- p-value < 0.05: Heteroscedasticity present ❌


**If heteroscedasticity is present:**
- Transform the dependent variable (log, square root)
- Use robust standard errors
- Try weighted least squares regression

---


### Assumption 5: Normality of Residuals

```r
# Q-Q plot
plot(model, which = 2)

# Histogram of residuals
hist(residuals(model), breaks = 20, 
     main = "Histogram of Residuals",
     xlab = "Residuals", col = "lightblue")

# Shapiro-Wilk test
shapiro.test(residuals(model))
```

**Interpretation:**
- **Q-Q Plot:** Points should follow diagonal line ✅
- **Histogram:** Should be approximately bell-shaped ✅
- **Shapiro-Wilk:** p > 0.05 indicates normality ✅


> **See the "Normality Check" learning nugget for detailed guidance on assessing normality.**

---


### Assumption 6: No Influential Outliers

**Method 1: Cook's Distance**

```r
# Cook's Distance plot
plot(model, which = 4)

# Identify influential cases
influential <- which(cooks.distance(model) > 4/(nrow(df) - length(coef(model))))
influential

# View influential cases
df[influential, ]
```

**Interpretation:**
- Cook's Distance > 4/(n-k-1) suggests influential case
- Values > 1 are definitely problematic


**Method 2: Residuals vs Leverage Plot**

```r
# Residuals vs Leverage
plot(model, which = 5)
```

**Interpretation:**
- Points outside dashed Cook's distance lines are influential
- Points with high leverage and large residuals are concerning


**Method 3: Identify Outliers**

```r
# Standardized residuals
std_resid <- rstandard(model)

# Cases with |standardized residual| > 3
outliers <- which(abs(std_resid) > 3)
outliers

# View outlier cases
df[outliers, ]
```


**What to do with outliers:**
1. Check for data entry errors
2. Determine if they are valid observations
3. Report results with and without outliers
4. Consider robust regression methods if many outliers exist

---


### All Diagnostic Plots at Once

```r
# 4 key diagnostic plots in one view
par(mfrow = c(2, 2))
plot(model)
par(mfrow = c(1, 1))
```

---

## 📊 Step 4: Interpret the Results


### Model Summary Interpretation

```r
summary(model)
```

**Key components to report:**


### 1. Overall Model Fit

**R² (Coefficient of Determination):**
- Proportion of variance in DV explained by all IVs together
- Range: 0 to 1 (0% to 100%)
- Example: R² = 0.83 means IVs explain 83% of variance in mpg

**Adjusted R²:**
- R² adjusted for number of predictors (penalizes adding useless IVs)
- Always use Adjusted R² when comparing models with different numbers of IVs
- Should be similar to R² (large difference suggests overfitting)


**F-statistic:**
- Tests if the overall model is significant
- H₀: All β coefficients = 0 (model explains nothing)
- p-value < 0.05: Model is statistically significant ✅

**Example interpretation:**
> "The overall regression model was statistically significant, F(3, 28) = 42.45, p < .001, with an adjusted R² of .81, indicating that weight, horsepower, and cylinders together explained 81% of the variance in fuel efficiency."

---


### 2. Individual Coefficient Interpretation

Each predictor has:
- **Estimate (β):** Change in DV for 1-unit increase in IV (holding other IVs constant)
- **Std. Error:** Precision of the estimate
- **t value:** Test statistic (Estimate / Std. Error)
- **p-value:** Statistical significance


**Interpreting β coefficients:**

```r
# Example output:
#              Estimate Std. Error t value Pr(>|t|)    
# (Intercept)  38.7518     1.7869  21.69  < 2e-16 ***
# wt           -3.1670     0.7406  -4.28  0.00020 ***
# hp           -0.0318     0.0090  -3.52  0.00145 ** 
# cyl          -0.9416     0.5509  -1.71  0.09855 .
```

**Example interpretations:**

**Weight (wt):** β = -3.17, p < .001
> "For each additional 1,000 lbs of weight, fuel efficiency decreases by 3.17 mpg, holding horsepower and cylinders constant."

**Horsepower (hp):** β = -0.03, p = .001
> "For each additional unit of horsepower, fuel efficiency decreases by 0.03 mpg, holding weight and cylinders constant."

**Cylinders (cyl):** β = -0.94, p = .099
> "Number of cylinders was not a statistically significant predictor of fuel efficiency when controlling for weight and horsepower, β = -0.94, p = .099."


### 3. Statistical Significance

**Significance levels:**
- p < .001: *** (highly significant)
- p < .01: ** (very significant)
- p < .05: * (significant)
- p ≥ .05: not significant (ns)

**Important:** Statistical significance depends on:
- Effect size (β coefficient)
- Sample size (larger n = easier to find significance)
- Variability in data

---


### 4. Standardized Coefficients (Beta Weights)

To compare relative importance of IVs (which IV matters most?), use standardized coefficients:

```r
# Calculate standardized coefficients
library(lm.beta)
install.packages("lm.beta")  # If not installed
library(lm.beta)
lm.beta(model)

# Alternative: manual calculation
scale_model <- lm(scale(mpg) ~ scale(wt) + scale(hp) + scale(cyl), 
                  data = df)
summary(scale_model)
```

**Interpretation:**
- Standardized β represents SD change in DV per 1 SD change in IV
- Allows comparison of variables on different scales
- Larger |β| = stronger effect


**Example:**
> "Weight had the strongest effect on fuel efficiency (β = -.67), followed by horsepower (β = -.32), while cylinders did not have a significant effect."

---


### 5. Confidence Intervals

```r
# 95% confidence intervals for coefficients
confint(model, level = 0.95)
```

**Interpretation:**
- We are 95% confident the true β falls within this interval
- If interval includes 0, the effect is not significant at α = .05
- Wider intervals = less precise estimates

**Example:**
> "Weight had a significant negative effect on fuel efficiency, β = -3.17, 95% CI [-4.68, -1.66], indicating that for each 1,000 lbs increase in weight, mpg decreases between 1.66 and 4.68 miles per gallon."

---

## 📋 Step 5: Report Your Results


### APA Style Reporting Template

**Descriptive Statistics Table**

Create a correlation and descriptives table:

```r
# Correlation matrix with means and SDs
library(psych)
describe(df[, c("mpg", "wt", "hp", "cyl")])
cor(df[, c("mpg", "wt", "hp", "cyl")])
```


**Results Text (APA Format):**

> **Method:**  
> A multiple linear regression was conducted to predict fuel efficiency (mpg) from vehicle weight (wt), horsepower (hp), and number of cylinders (cyl). Prior to analysis, assumptions of linearity, independence, normality, and homoscedasticity were examined and met. Variance Inflation Factors (VIF < 5) indicated no problematic multicollinearity.

> **Results:**  
> The overall regression model was statistically significant, F(3, 28) = 42.45, p < .001, and explained 81% of the variance in fuel efficiency (Adjusted R² = .81). Vehicle weight was a significant negative predictor, β = -3.17, t(28) = -4.28, p < .001, 95% CI [-4.68, -1.66], indicating that each 1,000 lb increase in weight was associated with a 3.17 mpg decrease in fuel efficiency, holding other variables constant. Horsepower also significantly predicted lower fuel efficiency, β = -0.03, t(28) = -3.52, p = .001, 95% CI [-0.05, -0.01]. However, number of cylinders did not significantly predict fuel efficiency when controlling for weight and horsepower, β = -0.94, t(28) = -1.71, p = .099.


### Create a Results Table

```r
# Regression coefficients table
library(broom)
results_table <- tidy(model, conf.int = TRUE)
results_table

# Round to 2 decimal places
results_table[, c(2:7)] <- round(results_table[, c(2:7)], 2)
results_table

# Export table
write.csv(results_table, "regression_results_yoursurname.csv", 
          row.names = FALSE)
```


**Example Table:**

| Predictor | B | SE | β | t | p | 95% CI |
|:----------|:--|:---|:--|:--|:--|:-------|
| (Intercept) | 38.75 | 1.79 | — | 21.69 | <.001 | [35.10, 42.41] |
| Weight | -3.17 | 0.74 | -.67 | -4.28 | <.001 | [-4.68, -1.66] |
| Horsepower | -0.03 | 0.01 | -.32 | -3.52 | .001 | [-0.05, -0.01] |
| Cylinders | -0.94 | 0.55 | -.21 | -1.71 | .099 | [-2.07, 0.18] |

*Note.* N = 32. R² = .83, Adjusted R² = .81, F(3, 28) = 42.45, p < .001.

---

## 📊 Step 6: Visualize Your Results


### 1. Scatter Plot with Regression Line

For simple relationships (DV vs one IV):

```r
# MPG vs Weight with regression line
ggplot(df, aes(x = wt, y = mpg)) +
  geom_point(size = 3, alpha = 0.6, color = "steelblue") +
  geom_smooth(method = "lm", se = TRUE, color = "red", fill = "pink") +
  labs(title = "Relationship between Weight and Fuel Efficiency",
       x = "Weight (1000 lbs)",
       y = "Miles per Gallon (MPG)") +
  theme_minimal() +
  theme(plot.title = element_text(hjust = 0.5, face = "bold"))
```


### 2. Predicted vs Actual Values

```r
# Add predictions to dataframe
df$predicted <- predict(model)
df$residuals <- residuals(model)

# Plot predicted vs actual
ggplot(df, aes(x = mpg, y = predicted)) +
  geom_point(size = 3, alpha = 0.6, color = "steelblue") +
  geom_abline(intercept = 0, slope = 1, color = "red", linetype = "dashed") +
  labs(title = "Actual vs Predicted MPG",
       x = "Actual MPG",
       y = "Predicted MPG") +
  theme_minimal() +
  theme(plot.title = element_text(hjust = 0.5, face = "bold"))
```

**Interpretation:**
- Points close to diagonal line = good predictions
- Points far from line = poor predictions (large residuals)


### 3. Coefficient Plot with Confidence Intervals

```r
# Coefficient plot (excluding intercept)
library(broom)
coef_data <- tidy(model, conf.int = TRUE)
coef_data <- coef_data[-1, ]  # Remove intercept

ggplot(coef_data, aes(x = term, y = estimate)) +
  geom_point(size = 4, color = "steelblue") +
  geom_errorbar(aes(ymin = conf.low, ymax = conf.high), 
                width = 0.2, color = "steelblue") +
  geom_hline(yintercept = 0, linetype = "dashed", color = "red") +
  coord_flip() +
  labs(title = "Regression Coefficients with 95% CI",
       x = "Predictor",
       y = "Coefficient Estimate") +
  theme_minimal() +
  theme(plot.title = element_text(hjust = 0.5, face = "bold"))
```


### 4. Residual Plots (Combined)

```r
# Enhanced diagnostic plots
par(mfrow = c(2, 2))
plot(model)
par(mfrow = c(1, 1))

# Or save as high-quality image
png("regression_diagnostics_yoursurname.png", 
    width = 800, height = 800)
par(mfrow = c(2, 2))
plot(model)
par(mfrow = c(1, 1))
dev.off()
```

---

## 🎯 Model Comparison and Selection


### Comparing Nested Models

If you want to test whether adding variables improves the model:

```r
# Model 1: Only weight
model1 <- lm(mpg ~ wt, data = df)

# Model 2: Weight + horsepower
model2 <- lm(mpg ~ wt + hp, data = df)

# Model 3: Weight + horsepower + cylinders (full model)
model3 <- lm(mpg ~ wt + hp + cyl, data = df)

# Compare models
anova(model1, model2, model3)

# AIC comparison (lower is better)
AIC(model1, model2, model3)

# BIC comparison (lower is better)
BIC(model1, model2, model3)
```


**Interpretation:**
- **ANOVA test:** p < .05 means the more complex model is significantly better
- **AIC/BIC:** Lower values indicate better model fit (penalized for complexity)
- Choose the simplest model that explains the data well (parsimony principle)


### Model Comparison Table

```r
# Create comparison table
library(broom)
comparison <- data.frame(
  Model = c("Model 1", "Model 2", "Model 3"),
  IVs = c("wt", "wt + hp", "wt + hp + cyl"),
  R2 = c(summary(model1)$r.squared,
         summary(model2)$r.squared,
         summary(model3)$r.squared),
  Adj_R2 = c(summary(model1)$adj.r.squared,
             summary(model2)$adj.r.squared,
             summary(model3)$adj.r.squared),
  AIC = c(AIC(model1), AIC(model2), AIC(model3)),
  BIC = c(BIC(model1), BIC(model2), BIC(model3))
)
comparison
```

---

## ⚠️ Common Issues and Solutions


### Issue 1: Non-linearity

**Symptoms:** Curved pattern in residual plot

**Solutions:**
```r
# Transform DV (log, square root)
model_log <- lm(log(mpg) ~ wt + hp + cyl, data = df)

# Add polynomial terms
model_poly <- lm(mpg ~ wt + I(wt^2) + hp + cyl, data = df)

# Try non-linear regression
```

---


### Issue 2: Multicollinearity (High VIF)

**Symptoms:** VIF > 10, highly correlated IVs

**Solutions:**
```r
# Check correlations
cor(df[, c("wt", "hp", "cyl")])

# Option 1: Remove one of the correlated variables
model_reduced <- lm(mpg ~ wt + hp, data = df)  # Remove cyl

# Option 2: Create composite variable (combine correlated IVs)
df$engine_power <- scale(df$hp) + scale(df$cyl)
model_composite <- lm(mpg ~ wt + engine_power, data = df)

# Option 3: Use Principal Component Analysis (PCA)
# Advanced technique - combine correlated variables into components
```

---


### Issue 3: Heteroscedasticity

**Symptoms:** Funnel shape in residual plot, significant Breusch-Pagan test

**Solutions:**
```r
# Transform DV
model_log <- lm(log(mpg) ~ wt + hp + cyl, data = df)
model_sqrt <- lm(sqrt(mpg) ~ wt + hp + cyl, data = df)

# Use robust standard errors
library(sandwich)
library(lmtest)
coeftest(model, vcov = vcovHC(model, type = "HC1"))

# Weighted least squares
weights <- 1 / lm(abs(residuals(model)) ~ fitted(model))$fitted.values^2
model_wls <- lm(mpg ~ wt + hp + cyl, data = df, weights = weights)
```

---


### Issue 4: Non-normal Residuals

**Symptoms:** Q-Q plot deviates from line, significant Shapiro-Wilk test

**Solutions:**
```r
# Transform DV
model_log <- lm(log(mpg) ~ wt + hp + cyl, data = df)

# Remove outliers (if justified)
outliers <- which(abs(rstandard(model)) > 3)
df_clean <- df[-outliers, ]
model_clean <- lm(mpg ~ wt + hp + cyl, data = df_clean)

# Use robust regression (less sensitive to outliers)
library(MASS)
model_robust <- rlm(mpg ~ wt + hp + cyl, data = df)
summary(model_robust)
```

**Note:** With large samples (n > 30-40), slight deviations from normality are often not problematic due to Central Limit Theorem.

---


### Issue 5: Influential Outliers

**Symptoms:** High Cook's Distance (> 4/(n-k-1)), leverage > 2(k+1)/n

**Solutions:**
```r
# Identify influential cases
cooks_d <- cooks.distance(model)
influential <- which(cooks_d > 4/(nrow(df) - length(coef(model))))

# Examine these cases
df[influential, ]

# Options:
# 1. Check for data entry errors
# 2. Run analysis with and without outliers
model_without <- lm(mpg ~ wt + hp + cyl, data = df[-influential, ])

# Compare results
summary(model)
summary(model_without)

# 3. Report both analyses
# 4. Use robust regression methods
```

---

## 🔄 Alternative Approaches


### Stepwise Regression (Use with Caution!)

**Backward elimination:** Start with all IVs, remove least significant

```r
# Full model
full_model <- lm(mpg ~ wt + hp + cyl + disp + drat + qsec, data = df)

# Backward stepwise
step_model <- step(full_model, direction = "backward")
summary(step_model)
```


**Forward selection:** Start with no IVs, add most significant

```r
# Null model (intercept only)
null_model <- lm(mpg ~ 1, data = df)

# Forward stepwise
step_forward <- step(null_model, 
                     scope = list(lower = null_model, 
                                  upper = full_model),
                     direction = "forward")
```


⚠️ **Warning about stepwise regression:**
- Can capitalize on chance findings
- Inflates Type I error rates
- Results may not replicate
- **Better:** Use theory to select variables
- **If you must:** Always validate with new data

---

## ✅ Analysis Checklist

{{1}}
**Before Running Regression:**
- [ ] Checked data structure and variable types
- [ ] Calculated descriptive statistics
- [ ] Examined scatter plots for linearity
- [ ] Checked for missing values
- [ ] Identified potential outliers
- [ ] Reviewed correlation matrix

{{2}}
**After Running Regression:**
- [ ] Checked all 6 assumptions systematically
- [ ] Examined VIF for multicollinearity (< 10)
- [ ] Reviewed diagnostic plots
- [ ] Identified influential cases (Cook's D)
- [ ] Interpreted coefficients correctly
- [ ] Calculated confidence intervals
- [ ] Assessed overall model fit (R², F-test)
- [ ] Determined which predictors are significant

{{3}}
**For Reporting:**
- [ ] Reported assumption checks
- [ ] Included descriptive statistics table
- [ ] Presented regression coefficients table
- [ ] Reported R², Adjusted R², F-statistic, p-value
- [ ] Interpreted each significant predictor
- [ ] Included confidence intervals
- [ ] Created appropriate visualizations
- [ ] Wrote results in APA format
- [ ] Discussed limitations

---

## 🎯 Knowledge Check Quiz


### Question 1: Choosing the Right Regression

You want to predict whether students will pass (yes/no) based on study hours and attendance. Which regression should you use?

    [( )] Simple Linear Regression
    [( )] Multiple Linear Regression
    [(X)] Logistic Regression
    [( )] Polynomial Regression
    [[?]]**Explanation:** Because the dependent variable is binary (pass/fail), logistic regression is appropriate. Linear regression requires a continuous DV.

---

### Question 2: Interpreting Coefficients

In a regression predicting salary from years of experience, the coefficient is β = 2,500 (p < .001). What does this mean?

    [( )] Total salary increases by $2,500
    [(X)] Each additional year of experience predicts $2,500 higher salary
    [( )] Experience explains 2,500% of variance
    [( )] The intercept is $2,500
    [[?]]**Explanation:** The coefficient represents the change in the DV (salary) for each 1-unit increase in the IV (years), holding other variables constant.

---


### Question 3: Multicollinearity

Your VIF values are: IV1 = 2.3, IV2 = 15.8, IV3 = 3.1. What should you do?

    [( )] Nothing, all values are acceptable
    [(X)] Consider removing or combining IV2 with correlated variables
    [( )] Remove IV1 because it has the lowest VIF
    [( )] Transform the dependent variable
    [[?]]**Explanation:** VIF > 10 indicates problematic multicollinearity. IV2's VIF of 15.8 suggests it's highly correlated with other IVs and should be addressed.

---

### Question 4: R² Interpretation

Your model has R² = .45. What does this mean?

    [( )] The model predicts correctly 45% of the time
    [( )] There is a .45 correlation between IVs and DV
    [(X)] The IVs explain 45% of variance in the DV
    [( )] 45% of cases are outliers
    [[?]]**Explanation:** R² represents the proportion of variance in the dependent variable that is explained by all independent variables together.

---

## 🔑 Key Takeaways


### Essential Principles

1. **Always check assumptions before interpreting results**
   - Violations can lead to incorrect conclusions
   - Use visual and statistical checks

2. **Multiple regression ≠ causation**
   - Shows associations, not necessarily causal relationships
   - Need theory and proper design for causal claims

3. **More predictors ≠ better model**
   - Use Adjusted R² and AIC/BIC for comparison
   - Principle of parsimony: simplest model that fits well

4. **Context matters**
   - Always interpret coefficients with "holding other variables constant"
   - Statistical significance ≠ practical importance

5. **Report completely and transparently**
   - Include assumption checks
   - Report exact p-values (not just p < .05)
   - Provide confidence intervals
   - Discuss limitations


### Common Mistakes to Avoid

- ❌ Ignoring assumption violations
- ❌ Not checking for multicollinearity
- ❌ Interpreting coefficients without "controlling for" language
- ❌ Confusing R² with correlation
- ❌ Forgetting to report confidence intervals
- ❌ Not examining residual plots
- ❌ Using stepwise regression without theory
- ❌ Removing outliers without justification
- ❌ Claiming causation from regression alone
- ❌ Not considering interaction effects when theoretically relevant


### Final Advice

> **The quality of your regression analysis depends on:**
> 1. Theoretical foundation (why these variables?)
> 2. Data quality (measurement, missing values, outliers)
> 3. Assumption checking (don't skip this!)
> 4. Thoughtful interpretation (what does it really mean?)
> 5. Transparent reporting (show your work)

---

## 📚 Additional Resources

- **R Documentation:** `?lm`, `?summary.lm`, `?plot.lm`
- **car package:** `?vif`, `?durbinWatsonTest`
- **Regression diagnostics:** `?influence.measures`
- **Online tutorials:**
  - https://statistikguru.de/r/r-multiple-regression.html
  - https://www.statmethods.net/stats/regression.html
  - https://cran.r-project.org/doc/contrib/Faraway-PRA.pdf


### Recommended Reading

- Field, A., Miles, J., & Field, Z. (2012). *Discovering Statistics Using R*
- James, G., Witten, D., Hastie, T., & Tibshirani, R. (2013). *An Introduction to Statistical Learning with Applications in R*
- Fox, J., & Weisberg, S. (2019). *An R Companion to Applied Regression*

---

## 💬 Questions?

Contact the teaching team:
- **Hannes Tegelbeckers:** hannes.tegelbeckers@ovgu.de
- **Office Hours:** https://cloud.ovgu.de/call/ciz2te64

**Related Learning Nuggets:**
- Descriptive Statistics in R
- Normality Check in R
- Data Visualization in R

**Remember:** Regression is a powerful tool, but it's only as good as the thought and care you put into the analysis!