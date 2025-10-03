# K-Anonymity Analysis Report: Privacy Protection Techniques for HarvardX MOOC Data

**Author:** Xavier Agostino  
**Course:** CS1050  
**Date:** October 2, 2025  
**Code Repository:** [GitHub - cs1050/k_anonymity_analysis](https://github.com/xavieragostino/cs1050)

---

## Executive Summary

This report analyzes k-anonymity techniques applied to a HarvardX online courses dataset containing 199,999 student records with 13 quasi-identifier attributes. I evaluated four anonymization approaches: record suppression, column suppression, generalization, and a combined methodology. The dataset exhibits extremely high uniqueness (initial k=1), requiring substantial modification to achieve 5-anonymity. The combined approach proved most effective, retaining 41% of records while achieving the target privacy threshold.

---

## 1. Introduction

### 1.1 Background
K-anonymity is a privacy protection model that ensures each individual in a dataset is indistinguishable from at least k-1 other individuals based on quasi-identifying attributes. Implementing k-anonymity protects sensitive information while maintaining data utility for research and analysis.

### 1.2 Objectives
This analysis aims to:
1. Assess the current k-anonymity level of the HarvardX online courses dataset
2. Evaluate multiple anonymization techniques to achieve 5-anonymity
3. Compare trade-offs between privacy protection and data utility
4. Identify optimal strategies for real-world privacy implementation

### 1.3 Dataset Overview
The dataset analyzed consists of:
- **Total Records:** 199,999 student entries
- **Quasi-Identifiers (13 total):** `cc_by_ip`, `city`, `postalCode`, `LoE` (Level of Education), `YoB` (Year of Birth), `gender`, `nforum_posts`, `nforum_votes`, `nforum_endorsed`, `nforum_threads`, `nforum_comments`, `nforum_pinned`, `nforum_events`
- **Initial K-Anonymity Level:** 1 (high re-identification risk)

---

## 2. Methodology

### 2.1 K-Anonymity Calculation
K-anonymity was calculated by grouping records based on their quasi-identifier combinations and identifying the minimum group size. The implementation utilized pandas groupby operations to create equivalence classes and analyze their size distributions. Statistical measures including mean, median, and percentile distributions of equivalence class sizes were computed to understand data uniqueness patterns.

### 2.2 Anonymization Techniques Evaluated

#### 2.2.1 Record Suppression
**Approach:** Remove records belonging to equivalence classes smaller than the target k-value (k=5).

**Implementation:**
```python
def record_suppression(df, quasi_identifiers, target_k=5):
    grouped = df.groupby(quasi_identifiers).size()
    df_with_size = df.merge(grouped, on=quasi_identifiers)
    return df_with_size[df_with_size['class_size'] >= target_k]
```

#### 2.2.2 Column Suppression
**Approach:** Remove quasi-identifier columns using a greedy algorithm that iteratively eliminates the column providing maximum k-anonymity improvement.

**Implementation:**
- Iterative greedy selection process
- At each step, test removal of each remaining column
- Select column whose removal maximizes k-anonymity
- Continue until target k is achieved or all columns exhausted

#### 2.2.3 Generalization
**Approach:** Reduce data specificity by grouping values into broader categories while preserving semantic meaning.

**Generalization Strategy:**
- **Year of Birth:** Converted to 5-year age ranges (e.g., 1990-1994)
- **Postal Code:** Truncated to first 3 digits (geographic region)
- **City:** Completely removed due to high cardinality
- **Forum Metrics:** Binned into ranges (0, 1-5, 6-20, 21-50, 51+)
- **Level of Education:** Maintained (already categorical)
- **Gender:** Maintained (limited cardinality)

#### 2.2.4 Combined Approach
**Approach:** Apply generalization techniques followed by record suppression to achieve target k-anonymity.

---

## 3. Results and Findings

### 3.1 Comparative Analysis

| Technique | K-Anonymity Achieved | Records Retained | Data Utility | Privacy Level |
|-----------|---------------------|------------------|--------------|---------------|
| **Original Dataset** | 1 | 199,999 (100%) | High | None |
| **Record Suppression** | 5 ✓ | 49,713 (25%) | Low | High |
| **Column Suppression** | 5 ✓ | 199,999 (0 QIs) | None | Maximum |
| **Generalization** | 1 ✗ | 199,999 (100%) | Medium | None |
| **Combined** | 5 ✓ | 81,663 (41%) | Medium | High |

### 3.2 Detailed Findings by Technique

#### 3.2.1 Record Suppression Results
- **Records Removed:** 150,286 (75.14%)
- **Records Retained:** 49,713 (24.86%)
- **K-Anonymity Achieved:** 5
- **Equivalence Classes:** 3,334 classes with mean size of 14.91

**Key Observation:** While this approach successfully achieved 5-anonymity, the cost was prohibitive. Eliminating three-quarters of the dataset significantly reduces statistical power and representativeness.

#### 3.2.2 Column Suppression Results
- **Columns Removed:** All 13 quasi-identifiers
- **K-Anonymity Achieved:** 199,999 (perfect anonymity)

**Key Observation:** This result reveals a critical insight: the dataset exhibits such high dimensional uniqueness that even single quasi-identifier columns create unique combinations. This approach, while technically achieving k-anonymity, renders the dataset meaningless for analysis as all distinguishing features are removed.

#### 3.2.3 Generalization Results
- **K-Anonymity Achieved:** 1 (failed to reach target)
- **Records Retained:** 199,999 (100%)

**Key Observation:** Despite applying substantial generalization across multiple attributes, the combination of 13 quasi-identifiers still produced unique records. This demonstrates that generalization alone is insufficient for highly dimensional datasets.

#### 3.2.4 Combined Approach Results 
- **Records Removed:** 118,336 (59.17%)
- **Records Retained:** 81,663 (40.83%)
- **K-Anonymity Achieved:** 5
- **Trade-off Balance:** Optimal

**Key Observation:** This approach represents the best compromise between privacy and utility. It retains 64% more data than record suppression alone while maintaining the target privacy level.

---

## 4. Patterns Discovered

### 4.1 Dataset Uniqueness Characteristics

**Pattern 1: Extreme Individual Uniqueness**
The dataset exhibited remarkably high uniqueness across its quasi-identifiers. With an initial k-anonymity of 1, every record was effectively unique when considering all 13 attributes. This suggests highly diverse student populations with varied behavioral patterns.

**Pattern 2: Forum Activity as Distinguishing Feature**
Forum engagement metrics (posts, votes, endorsements, threads, comments, pinned, events) contributed significantly to uniqueness. Even when demographic attributes were generalized, forum activity patterns remained highly individualistic, preventing achievement of target k-anonymity.

**Pattern 3: Geographic Granularity Impact**
The combination of city and postal code created fine-grained geographic identification. Even after generalizing postal codes to 3 digits, the combination with other attributes maintained high uniqueness.

**Pattern 4: Curse of Dimensionality**
The 13-dimensional quasi-identifier space created an enormous combination space. Even modest cardinality in each dimension (e.g., 5 age ranges × 2 genders × 4 education levels × 5^7 forum metric bins) produces millions of potential combinations, making collision unlikely in a 200K record dataset.

### 4.2 Equivalence Class Distribution Patterns

After successful anonymization (combined approach), equivalence class sizes followed a right-skewed distribution:
- **Minimum class size:** 5 (by design)
- **Median class size:** 7
- **Mean class size:** 14.91
- **Maximum class size:** 2,939 (some common demographic/behavioral profiles)

This distribution reveals that while most students have unique behavioral patterns, certain common profiles (likely entry-level students with minimal forum engagement) create large equivalence classes.

---

## 5. Implications and Recommendations

### 5.1 Privacy-Utility Trade-off
This analysis demonstrates the tension between data privacy and utility. For highly dimensional datasets with diverse populations, achieving meaningful privacy protection requires sacrificing substantial data quantity or granularity. Organizations must carefully consider this trade-off based on:
- Legal and ethical privacy requirements
- Research questions requiring specific granularity
- Acceptable loss of statistical power

### 5.2 Practical Recommendations

**For Similar Datasets:**
1. **Hybrid Approaches Are Essential:** Single-technique solutions proved inadequate. Real-world privacy protection requires combining multiple methods.
2. **Early Privacy Design:** Design data collection with anonymization in mind. Consider collecting pre-generalized data (age ranges instead of birthdates).
3. **Selective Attribute Collection:** Question whether all quasi-identifiers are necessary. Each additional dimension exponentially increases uniqueness.
4. **Dynamic K-Values:** Consider context-specific k-values. More sensitive analyses may require k≥10 or higher.

**For Educational Institutions:**
1. **Forum Metrics Aggregation:** Consider collecting forum engagement in pre-binned ranges rather than exact counts
2. **Geographic Generalization:** Collect regional identifiers rather than precise locations from the start
3. **Temporal Generalization:** Use academic year cohorts rather than specific birth years

### 5.3 Limitations of This Study

1. **Greedy Algorithm:** The column suppression approach used a greedy algorithm, which may not find the global optimum combination of columns to remove.
2. **Fixed Generalization Schema:** The generalization strategy was predetermined rather than optimized for this specific dataset.
3. **No Utility Metrics:** While I preserved data quantity, I did not measure analytical utility loss through task-specific metrics.
4. **Single K-Value:** I targeted only k=5; higher k-values may be more appropriate for sensitive educational data.

### 5.4 Future Research Directions

1. **Optimal Generalization:** Develop algorithms to automatically determine optimal generalization hierarchies for given datasets
2. **Utility Preservation:** Incorporate utility metrics (e.g., classification accuracy, correlation preservation) into anonymization optimization
3. **Alternative Privacy Models:** Explore differential privacy, l-diversity, and t-closeness as alternatives or supplements to k-anonymity
4. **Synthetic Data Generation:** Investigate generating synthetic datasets that maintain statistical properties while ensuring privacy

---

## 6. Conclusion

This analysis of k-anonymity techniques on a HarvardX online courses dataset reveals the complexity of privacy protection in high-dimensional data environments. The initial k-anonymity of 1 indicated severe re-identification risk, requiring substantial intervention to achieve the modest target of 5-anonymity.

Among the four approaches evaluated, the **combined methodology** proved most viable, achieving 5-anonymity while retaining 41% of records. This represents a 64% improvement over pure record suppression in terms of data retention. However, discarding 59% of records to achieve even modest privacy protection highlights the challenges facing privacy-preserving data analysis.

The patterns discovered (particularly the extreme uniqueness across 13 dimensions and the distinguishing power of behavioral forum metrics) underscore the "curse of dimensionality" in privacy protection. As datasets grow richer with more attributes capturing nuanced individual behaviors, traditional anonymization techniques face increasing challenges.

For educational institutions and researchers working with student data, this analysis provides clear guidance: **privacy protection requires careful upfront planning, hybrid technical approaches, and explicit acknowledgment of utility trade-offs.** Simple data anonymization through removal of direct identifiers (names, IDs) is insufficient. Modern privacy protection demands sophisticated techniques and often substantial data sacrifice.

Ultimately, this study reinforces that **privacy is not free.** It comes at the cost of data granularity, completeness, or both. Responsible data stewardship requires accepting these costs as the price of ethical research and respecting the individuals whose information we seek to analyze.

---

## Appendix: Code Repository

**GitHub Repository:** https://github.com/xavieragostino/cs1050  
**Jupyter Notebook:** `notebooks/k_anonymity_analysis.ipynb`  
**Dataset:** `data/reduced_qi_filled.csv`  

### Key Functions Implemented:
- `calculate_k_anonymity()`: Computes k-anonymity level and equivalence class statistics
- `record_suppression()`: Implements record removal strategy
- `find_minimal_column_suppression()`: Greedy column selection algorithm
- `generalize_data()`: Applies generalization transformations
- Comprehensive visualization functions for equivalence class distributions

The complete analysis pipeline, including data loading, preprocessing, anonymization implementations, and results visualization, is available in the repository for reproducibility and further exploration.

---


