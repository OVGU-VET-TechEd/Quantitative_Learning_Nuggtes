<!--
author: {{AUTHOR_NAME}}
email: {{AUTHOR_EMAIL}}
version: {{VERSION_NUMBER}}
language: {{LANGUAGE_CODE}}
narrator: {{NARRATOR_VOICE}}
comment: {{COURSE_DESCRIPTION}}

link: https://raw.githubusercontent.com/OVGU-VET-TechEd/ASSET_UNESCO_Coinitiative/refs/heads/main/ASSET_basic.css
-->

# {{NUGGET_TITLE}}

<svg xmlns='http://www.w3.org/2000/svg' width='1100' height='400' viewBox='0 0 800 450'>
  <!-- Background -->
  <rect width='800' height='450' fill='{{PRIMARY_COLOR}}' />
  
  <!-- White rounded rectangle container -->
  <rect x='50' y='50' width='700' height='350' rx='20' fill='white' />
  
  <!-- Title -->
  <text x='400' y='130' font-family='Segoe UI, Arial, sans-serif' font-size='50' font-weight='bold' text-anchor='middle' fill='{{PRIMARY_COLOR}}'>
    {{STATISTICAL_TOPIC}}
  </text>
  
  <!-- Subtitle -->
  <text x='400' y='180' font-family='Segoe UI, Arial, sans-serif' font-size='24' font-weight='bold' text-anchor='middle' fill='{{SECONDARY_COLOR}}'>
    Quantitative Research Methods - Nugget {{NUGGET_NUMBER}}
  </text>

  <!-- Duration -->
  <text x='400' y='210' font-family='Segoe UI, Arial, sans-serif' font-size='16' font-weight='bold' text-anchor='middle' fill='{{SECONDARY_COLOR}}'>
    Duration: {{NUGGET_DURATION}}
  </text>
</svg>

---

## Learning Objectives

By the end of this nugget, you will be able to:

{{#LEARNING_OBJECTIVES}}
{{OBJECTIVE_NUMBER}}. **{{ACTION_VERB}}** {{STATISTICAL_OUTCOME}}
{{/LEARNING_OBJECTIVES}}

---

## Statistical Concept

> **{{CONCEPT_NAME}}**

{{CONCEPT_DEFINITION}}

**When to use:** {{USAGE_CONTEXT}}

**Key assumptions:** {{KEY_ASSUMPTIONS}}

---

## Hands-On R Practice

> **Activity: {{ACTIVITY_TITLE}}**

**Dataset:** `{{DATASET_NAME}}.csv` from [GitHub Repository](https://github.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggtes/tree/main/data)

**Variables:**
- {{VARIABLE_1}}: {{VARIABLE_1_DESCRIPTION}}
- {{VARIABLE_2}}: {{VARIABLE_2_DESCRIPTION}}

### Step 1: Load Data

```r
# Load required libraries
{{REQUIRED_PACKAGES}}

# Load dataset
data <- read.csv("https://github.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggtes/raw/main/data/{{DATASET_NAME}}.csv")

# Explore data structure
{{EXPLORATION_CODE}}
```

### Step 2: Data Analysis

```r
# {{ANALYSIS_DESCRIPTION}}
{{R_CODE_BLOCK}}
```

**Expected Output:**
{{EXPECTED_OUTPUT_DESCRIPTION}}

### Step 3: Interpretation

{{INTERPRETATION_INSTRUCTIONS}}

---

## Knowledge Check

> **Question {{QUESTION_NUMBER}}: {{QUESTION_TOPIC}}**

{{QUESTION_TEXT}}

    [( )] {{OPTION_1}}
    [(X)] {{OPTION_2}}
    [( )] {{OPTION_3}}
    [( )] {{OPTION_4}}
    [[?]] Select the correct answer
    <script>
      if (@input === {{CORRECT_OPTION}}) {
        send.lia("✅ **Correct!** {{FEEDBACK_CORRECT}}");
      } else {
        send.lia("❌ **Try again.** {{FEEDBACK_INCORRECT}}");
      }
    </script>

---

## Practice Challenge

**Task:** {{CHALLENGE_DESCRIPTION}}

**Instructions:**
1. {{INSTRUCTION_1}}
2. {{INSTRUCTION_2}}
3. {{INSTRUCTION_3}}

**Expected Result:** {{EXPECTED_RESULT}}

---

## Key Takeaways

- {{TAKEAWAY_1}}
- {{TAKEAWAY_2}}
- {{TAKEAWAY_3}}

---

## Resources

**Essential Reading:**
- [{{RESOURCE_1_TITLE}}]({{RESOURCE_1_URL}})
- [{{RESOURCE_2_TITLE}}]({{RESOURCE_2_URL}})

**R Documentation:**
- [{{R_FUNCTION_1}}]({{R_DOC_URL_1}})
- [{{R_FUNCTION_2}}]({{R_DOC_URL_2}})

---

## Next Steps

**Coming Up:** {{NEXT_NUGGET_TITLE}}

**Preparation:**
- Review: {{REVIEW_TOPIC}}
- Practice: {{PRACTICE_TASK}}

---

**🎉 Nugget {{NUGGET_NUMBER}} Complete!**

📧 **Questions?** Contact: {{CONTACT_EMAIL}}
