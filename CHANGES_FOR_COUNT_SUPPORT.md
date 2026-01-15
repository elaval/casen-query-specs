# Changes to System Prompts for Count Support

**Date:** 2026-01-15
**Version:** 0.3.0

## Summary

Updated both `interpreter.md` and `query_builder.md` to support the new `count` indicator for population totals.

---

## Changes to `interpreter.md`

### 1. **Added "Population Counts" to Available Capabilities**

**Location:** Lines 24-26

```markdown
3) Population counts (totals)
   - Number of people or households (expanded population estimates)
   - Can be used with or without conditions
```

### 2. **Updated Decision Rules**

**Location:** Lines 99, 107-112

Added:
- Count trigger words: "cuántos", "cuántas", "cantidad", "número de"
- Clear distinction between count vs proportion:
  - Count → absolute numbers (e.g., 3,500,000 people)
  - Proportion → percentages (e.g., 0.45 = 45%)

### 3. **Added Critical Rules for Count Indicator**

**Location:** Lines 141-149

New section explaining:
- Count can be used WITH or WITHOUT `binary_expression`
- WITHOUT: counts all observations (e.g., total population)
- WITH: counts observations meeting a condition (e.g., elderly people)
- Variable field is required but can be any non-missing variable

### 4. **Updated Output Schema**

**Location:** Line 170

Changed:
```json
"analysis_type": "mean | proportion | count"
```

---

## Changes to `query_builder.md`

### 1. **Updated API Rules**

**Location:** Lines 29-43

Added:
- `"count"` as valid indicator
- Clarified when `binary_expression` is optional vs required
- Added mapping rule: `analysis_type = "count"` → `indicator = "count"`

### 2. **Added Examples Section**

**Location:** Lines 46-119

Added 4 comprehensive examples:

**Example 3: Total population count**
```json
Input: {"analysis_type": "count", "variable": "edad", "unit_of_analysis": "person"}
Output: {"indicator": "count", "variable": "edad", "unit_of_analysis": "person"}
```

**Example 4: Count of elderly by decile**
```json
Input: {
  "analysis_type": "count",
  "variable": "edad",
  "binary_expression": "edad >= 60",
  "by_groups": ["dau"],
  "unit_of_analysis": "person"
}
Output: {
  "indicator": "count",
  "variable": "edad",
  "binary_expression": "edad >= 60",
  "by_groups": ["dau"],
  "unit_of_analysis": "person"
}
```

---

## Test Cases for Validation

### Test Case 1: Simple Count Question

**User Input:**
```
"¿Cuántas personas hay en Chile?"
```

**Expected Interpreter Output:**
```json
{
  "status": "ok",
  "interpretation": {
    "analysis_type": "count",
    "concept": "Total population",
    "variable": "edad",
    "variable_label": "Age (used as proxy for counting all persons)",
    "default_applied": false,
    "unit_of_analysis": "person"
  }
}
```

**Expected Query Builder Output:**
```json
{
  "indicator": "count",
  "variable": "edad",
  "unit_of_analysis": "person"
}
```

---

### Test Case 2: Conditional Count with Grouping

**User Input:**
```
"¿Cuántas personas de 60 años o más hay en cada decil de ingreso?"
```

**Expected Interpreter Output:**
```json
{
  "status": "ok",
  "interpretation": {
    "analysis_type": "count",
    "concept": "Elderly population by income decile",
    "variable": "edad",
    "variable_label": "Age",
    "default_applied": false,
    "binary_expression": "edad >= 60",
    "by_groups": ["dau"],
    "unit_of_analysis": "person"
  }
}
```

**Expected Query Builder Output:**
```json
{
  "indicator": "count",
  "variable": "edad",
  "binary_expression": "edad >= 60",
  "by_groups": ["dau"],
  "unit_of_analysis": "person"
}
```

---

### Test Case 3: Count vs Proportion Disambiguation

**User Input A (Count):**
```
"¿Cuántos hogares unipersonales hay por región?"
```

**Expected:** `analysis_type: "count"`

**User Input B (Proportion):**
```
"¿Qué porcentaje de hogares son unipersonales por región?"
```

**Expected:** `analysis_type: "proportion"` with `binary_expression: "numper == 1"`

---

### Test Case 4: Count with Filters

**User Input:**
```
"¿Cuántas personas en edad de trabajar (18-65 años) hay en zonas urbanas?"
```

**Expected Interpreter Output:**
```json
{
  "status": "ok",
  "interpretation": {
    "analysis_type": "count",
    "concept": "Working-age population in urban areas",
    "variable": "edad",
    "variable_label": "Age",
    "default_applied": false,
    "unit_of_analysis": "person",
    "filters": ["edad >= 18", "edad <= 65", "area == 1"]
  }
}
```

**Expected Query Builder Output:**
```json
{
  "indicator": "count",
  "variable": "edad",
  "unit_of_analysis": "person",
  "filters": ["edad >= 18", "edad <= 65", "area == 1"]
}
```

---

## Key Differences Between Count and Proportion

| Aspect | Count | Proportion |
|--------|-------|------------|
| **Question Type** | "How many?" / "¿Cuántos?" | "What percentage?" / "¿Qué porcentaje?" |
| **Result Type** | Absolute number (e.g., 3,500,000) | Percentage (e.g., 0.45 = 45%) |
| **binary_expression** | Optional | **Required** |
| **Use Case** | Total population, subgroup counts | Rates, shares, percentages |
| **Example** | "¿Cuántas personas tienen 60+?" | "¿Qué % de la población tiene 60+?" |

---

## Common Count Trigger Words (Spanish)

The interpreter should recognize these as count queries:

- ¿Cuántos? / ¿Cuántas?
- ¿Cantidad de...?
- ¿Número de...?
- ¿Qué cantidad...?
- Total de...

---

## Validation Checklist

- [ ] Interpreter recognizes "cuántos/cuántas" → count
- [ ] Interpreter recognizes "qué porcentaje" → proportion
- [ ] Query builder maps `analysis_type: "count"` → `indicator: "count"`
- [ ] Count queries work WITHOUT binary_expression (total counts)
- [ ] Count queries work WITH binary_expression (conditional counts)
- [ ] Count queries work with by_groups (grouped counts)
- [ ] Count queries work with filters

---

## Notes for LLM Testing

When testing with an LLM:

1. **Test the interpreter first** with natural language questions
2. **Verify the interpretation** matches expected analysis_type
3. **Test the query builder** with the interpreter's output
4. **Verify the final query** is valid API JSON

Example test flow:
```
User Question → Interpreter → Interpretation JSON → Query Builder → API Query JSON → API Response
```

---

**End of Changes Document**
