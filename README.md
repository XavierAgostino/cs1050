# K-Anonymity Analysis: Privacy Protection for Educational Data

CS1050 Project analyzing privacy protection techniques on HarvardX MOOC data.

## Overview

This project evaluates four k-anonymity techniques (record suppression, column suppression, generalization, and combined approach) on a dataset of 199,999 student records from HarvardX online courses. The analysis demonstrates privacy-utility trade-offs in educational data anonymization.

## Key Findings

- **Initial k-anonymity**: 1 (high re-identification risk)
- **Target achieved**: 5-anonymity using combined approach
- **Data retained**: 41% of original records
- **Best method**: Generalization + record suppression

## Project Structure

```
cs1050/
├── notebooks/
│   └── k_anonymity_analysis.ipynb      # Main analysis notebook
├── reports/
│   └── K_Anonymity_Analysis_Report.md  # Full report with findings
├── data/
│   └── reduced_qi_filled.csv           # HarvardX student dataset (199,999 records)
└── README.md
```

## Dataset

The dataset contains anonymized student records from HarvardX MOOCs with 13 quasi-identifiers:
- Demographics: city, postal code, level of education, year of birth, gender
- Behavioral: forum posts, votes, endorsements, threads, comments, pinned items, events

## Requirements

```bash
pip install pandas numpy matplotlib seaborn jupyter
```

## Usage

```bash
# Run the analysis
jupyter notebook notebooks/k_anonymity_analysis.ipynb

# View the full report
open reports/K_Anonymity_Analysis_Report.md
```

## Methods Evaluated

1. **Record Suppression**: Remove records with small equivalence classes (25% retention)
2. **Column Suppression**: Remove quasi-identifier columns (100% privacy, 0% utility)
3. **Generalization**: Group values into broader categories (insufficient alone)
4. **Combined Approach**: Generalization + record suppression (41% retention, 5-anonymity ✓)

## Results

| Technique | K-Anonymity | Records Retained | Data Utility |
|-----------|-------------|------------------|--------------|
| Original | 1 | 100% | High |
| Record Suppression | 5 | 25% | Low |
| Column Suppression | 199,999 | 100% | None |
| Generalization | 1 | 100% | Medium |
| **Combined** | **5** | **41%** | **Medium** |

## Key Functions

- `calculate_k_anonymity()`: Computes k-anonymity level and equivalence class statistics
- `record_suppression()`: Implements record removal strategy
- `find_minimal_column_suppression()`: Greedy column selection algorithm
- `generalize_data()`: Applies generalization transformations
- Visualization functions for equivalence class distributions

## Files

- **`notebooks/k_anonymity_analysis.ipynb`** - Complete analysis pipeline with visualizations
- **`reports/K_Anonymity_Analysis_Report.md`** - Comprehensive report with methodology, findings, and recommendations
- **`data/reduced_qi_filled.csv`** - HarvardX MOOC student dataset with 13 quasi-identifiers

## Conclusion

This analysis demonstrates that achieving meaningful privacy protection in high-dimensional datasets requires hybrid approaches. The combined generalization and record suppression method achieved 5-anonymity while retaining 41% of records, representing the best balance between privacy and data utility.

## Author

Xavier Agostino  
CS1050  
October 2025

