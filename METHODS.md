# Methods: Baseline Pipeline Workflow

## Overview

This document describes the Baseline Pipeline workflow implemented in `scripts/Baseline_pipeline.py` for data processing and analysis. The pipeline provides a standardized, reproducible framework for handling raw data through quality control, preprocessing, and baseline statistical analysis.

## Pipeline Architecture

The Baseline Pipeline follows a modular, five-stage architecture designed to ensure data quality, reproducibility, and comprehensive documentation of all processing steps. Each stage is independently validated and logged, enabling traceback of results to original data sources.

### Computational Environment

- **Programming Language**: Python 3.7+
- **Required Libraries**: 
  - Standard library modules: `os`, `sys`, `argparse`, `logging`, `datetime`, `pathlib`
  - Additional dependencies specified in `requirements.txt`
- **Execution Mode**: Command-line interface with configurable parameters
- **Logging**: Comprehensive logging at INFO level with timestamped entries for all major operations

## Stage 1: Data Import and Validation

### Purpose
Import raw data files and perform initial validation to ensure data integrity before downstream processing.

### Input Requirements
- **Format**: Comma-separated values (.csv) files
- **Location**: User-specified input directory (command-line parameter `--input`)
- **Structure**: Each file must contain properly formatted tabular data

### Processing Steps
1. **Directory Validation**: Verify existence and accessibility of input directory
2. **File Discovery**: Identify all .csv files in the input directory
3. **File Enumeration**: Log count and names of discovered files
4. **Data Loading**: Import each file using standardized loading procedures
5. **Initial Validation**: 
   - Verify file format compliance
   - Check data integrity (checksums, completeness)
   - Detect and log missing values
   - Validate data types for each column

### Output
- Dictionary object containing all imported datasets
- Import log with file-level statistics
- Validation report identifying any data quality issues

### Quality Checks
- File format verification ensures all inputs are valid CSV files
- Data integrity checks prevent corrupted file processing
- Missing value detection provides early warning of incomplete datasets

## Stage 2: Quality Control

### Purpose
Systematically evaluate data quality and identify samples or measurements requiring exclusion or special handling.

### Processing Steps

#### 2.1 Outlier Detection
- **Method**: Statistical outlier detection using robust estimators
- **Criteria**: Values exceeding 3 standard deviations from the median
- **Action**: Flag but do not automatically remove outliers; document for review

#### 2.2 Distribution Analysis
- **Normality Assessment**: Evaluate data distributions for each variable
- **Skewness and Kurtosis**: Calculate distribution shape metrics
- **Visual Inspection**: Generate distribution plots for manual review

#### 2.3 Correlation Checks
- **Methodology**: Pairwise correlation analysis between variables
- **Purpose**: Identify multicollinearity and validate expected relationships
- **Threshold**: Flag correlations |r| > 0.9 for potential redundancy

#### 2.4 Sample Quality Metrics
- **Completeness**: Proportion of non-missing values per sample
- **Consistency**: Within-sample variance for replicate measurements
- **Technical Controls**: Validation against positive/negative controls

### Output
- Quality-controlled dataset with flagged samples/measurements
- QC report (`qc_report.txt`) containing:
  - Total samples processed
  - Samples passing QC criteria
  - Number of outliers detected
  - Distribution statistics
  - Correlation matrix
- QC summary visualizations

### Decision Criteria
Samples are excluded from downstream analysis if:
- Missing data exceeds 20% of variables
- Multiple outlier flags across independent QC metrics
- Technical control values outside acceptable ranges

## Stage 3: Data Preprocessing

### Purpose
Transform quality-controlled data into a standardized format suitable for statistical analysis.

### Processing Steps

#### 3.1 Normalization
- **Method**: Quantile normalization or median centering
- **Rationale**: Ensures comparability across samples and batches
- **Application**: Applied separately to each dataset/batch

#### 3.2 Data Transformation
- **Log Transformation**: Applied to skewed variables (skewness > 1.0)
- **Box-Cox Transformation**: For variables requiring normalization
- **Validation**: Post-transformation distribution assessment

#### 3.3 Feature Scaling
- **Method**: Standardization (z-score) or Min-Max scaling
- **Purpose**: Ensure all variables contribute equally to downstream analyses
- **Application**: Applied after normalization and transformation

#### 3.4 Missing Value Imputation
- **Threshold**: Variables with <20% missing data eligible for imputation
- **Method**: 
  - Numeric variables: K-nearest neighbors (k=5)
  - Categorical variables: Mode imputation
- **Documentation**: All imputed values flagged in output

#### 3.5 Batch Effect Correction
- **Detection**: Principal component analysis to identify batch-associated variance
- **Correction**: ComBat or similar empirical Bayes methods
- **Validation**: Verify preservation of biological signal

### Output
- Preprocessed dataset ready for analysis
- Preprocessing log documenting all transformations
- Transformation parameters for reproducibility
- Before/after comparison visualizations

### Quality Assurance
- Verify normalization reduces technical variance
- Confirm transformations improve distribution normality
- Validate that batch effects are minimized without removing biological signal

## Stage 4: Baseline Analysis

### Purpose
Perform comprehensive statistical analysis to characterize baseline properties and relationships in the data.

### Statistical Methods

#### 4.1 Descriptive Statistics
For each variable, calculate:
- **Central Tendency**: Mean, median, mode
- **Dispersion**: Standard deviation, interquartile range
- **Distribution Shape**: Skewness, kurtosis
- **Range**: Minimum, maximum, percentiles (5th, 25th, 75th, 95th)

#### 4.2 Group Comparisons
- **Two-Group Comparisons**: 
  - Parametric: Student's t-test (equal/unequal variance)
  - Non-parametric: Mann-Whitney U test
  - Selection based on normality tests (Shapiro-Wilk, p>0.05)
- **Multi-Group Comparisons**: 
  - Parametric: One-way ANOVA
  - Non-parametric: Kruskal-Wallis test
  - Post-hoc: Tukey HSD or Dunn's test
- **Multiple Testing Correction**: Benjamini-Hochberg FDR control

#### 4.3 Correlation Analysis
- **Method**: Pearson (parametric) or Spearman (non-parametric) correlation
- **Significance**: Two-tailed p-values with multiple testing correction
- **Visualization**: Correlation heatmaps with hierarchical clustering

#### 4.4 Effect Size Calculations
- **Cohen's d**: For two-group comparisons
- **Eta-squared (η²)**: For ANOVA analyses
- **Interpretation**: Small (d=0.2, η²=0.01), Medium (d=0.5, η²=0.06), Large (d=0.8, η²=0.14)

### Output
- Statistical summary tables
- Test results with p-values and effect sizes
- Multiple testing corrected p-values (q-values)
- Confidence intervals (95%) for all estimates

### Reporting Standards
All statistical tests reported according to APA guidelines:
- Test statistic
- Degrees of freedom
- p-value
- Effect size with confidence interval
- Sample sizes for each group

## Stage 5: Results Export

### Purpose
Generate publication-ready outputs and comprehensive documentation of all analyses.

### Output Components

#### 5.1 Summary Statistics Tables
- **Format**: CSV files for data, formatted text for manuscripts
- **Location**: `output_dir/tables/`
- **Content**:
  - Descriptive statistics by group
  - Statistical test results
  - Effect sizes with confidence intervals
  - Sample sizes and missing data summaries

#### 5.2 Visualization Plots
- **Format**: High-resolution PNG (300 dpi) and vector PDF
- **Location**: `output_dir/figures/`
- **Types**:
  - Distribution plots (histograms, Q-Q plots)
  - Group comparison boxplots/violin plots
  - Correlation heatmaps
  - Effect size forest plots
  - QC summary plots

#### 5.3 Raw Results Files
- **Format**: Structured JSON and CSV
- **Location**: `output_dir/`
- **Content**: Complete analysis results for reproducibility

#### 5.4 Analysis Report
- **Format**: Markdown document (`analysis_report.md`)
- **Content**:
  - Pipeline execution summary
  - Sample and variable counts at each stage
  - QC metrics and exclusions
  - Key statistical findings
  - Warnings and recommendations

#### 5.5 Execution Log
- **Format**: Plain text log file
- **Naming**: `pipeline_YYYYMMDD_HHMMSS.log`
- **Content**: Timestamped record of all pipeline operations

### File Organization
```
output_dir/
├── pipeline_YYYYMMDD_HHMMSS.log
├── qc_report.txt
├── analysis_report.md
├── tables/
│   ├── descriptive_statistics.csv
│   ├── test_results.csv
│   └── effect_sizes.csv
└── figures/
    ├── distributions.png
    ├── group_comparisons.png
    └── correlations.png
```

## Pipeline Execution

### Command-Line Interface

#### Basic Usage
```bash
python scripts/Baseline_pipeline.py --input data/raw --output results/baseline
```

#### With Verbose Logging
```bash
python scripts/Baseline_pipeline.py -i data/raw -o results/baseline --verbose
```

#### With Configuration File
```bash
python scripts/Baseline_pipeline.py -i data/raw -o results/baseline --config config.yaml
```

### Required Parameters
- `--input` or `-i`: Path to directory containing input data files
- `--output` or `-o`: Path to directory for output results

### Optional Parameters
- `--verbose` or `-v`: Enable detailed logging output
- `--config`: Path to YAML configuration file for advanced parameters

### Exit Codes
- `0`: Pipeline completed successfully
- `1`: Pipeline encountered error (see log file for details)

## Reproducibility

### Version Control
- Pipeline version: 1.0
- All code maintained in version control system (Git)
- Specific version tag recorded in analysis reports

### Random Seeds
- All random operations use fixed seeds
- Seeds documented in configuration and logs
- Enables exact reproduction of results

### Environment Documentation
- Python version and all library versions logged
- Operating system and hardware specifications recorded
- Computational environment can be recreated using provided specifications

### Data Provenance
- All input files checksummed
- Processing steps logged with timestamps
- Complete audit trail from raw data to final results

## Quality Assurance and Validation

### Internal Validation
- Each stage includes self-checks and assertions
- Data dimensions verified between stages
- Statistical assumptions tested before analysis

### External Validation
- Pipeline tested on reference datasets with known properties
- Results validated against alternative analysis tools
- Code reviewed and tested by independent researchers

### Error Handling
- Comprehensive exception handling with informative messages
- Graceful degradation when possible
- Automatic cleanup of partial outputs on failure

## Limitations and Assumptions

### Assumptions
1. Input data are structured as rectangular tables (samples × variables)
2. Samples are independent observations
3. Missing data are missing at random (MAR)
4. Groups being compared have similar variance (homoscedasticity)

### Limitations
1. Pipeline designed for moderate-sized datasets (< 10,000 samples)
2. Assumes continuous or ordinal variables for most analyses
3. Batch effect correction requires known batch assignments
4. Statistical tests assume adequate sample sizes (typically n ≥ 30 per group)

### Recommendations
- Pilot testing recommended for novel data types
- Visual inspection of QC plots advised
- Statistical assumptions should be verified for each dataset
- Consult statistician for complex experimental designs

## References

### Statistical Methods
- Benjamini, Y., & Hochberg, Y. (1995). Controlling the false discovery rate: a practical and powerful approach to multiple testing. Journal of the Royal Statistical Society: Series B, 57(1), 289-300.
- Cohen, J. (1988). Statistical Power Analysis for the Behavioral Sciences (2nd ed.). Lawrence Erlbaum Associates.
- Johnson, W. E., Li, C., & Rabinovic, A. (2007). Adjusting batch effects in microarray expression data using empirical Bayes methods. Biostatistics, 8(1), 118-127.

### Best Practices
- Wilson, G., et al. (2017). Good enough practices in scientific computing. PLoS Computational Biology, 13(6), e1005510.
- Sandve, G. K., et al. (2013). Ten simple rules for reproducible computational research. PLoS Computational Biology, 9(10), e1003285.

## Contact and Support

For questions, issues, or contributions to the Baseline Pipeline, please contact the research team or open an issue in the project repository.

---

**Document Version**: 1.0  
**Last Updated**: 2025-11-11  
**Authors**: Research Team
