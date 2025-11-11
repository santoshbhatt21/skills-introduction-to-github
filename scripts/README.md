# Scripts Directory

This directory contains the Baseline Pipeline workflow for data processing and analysis.

## Contents

- **Baseline_pipeline.py**: Main pipeline script implementing a five-stage workflow for data processing, quality control, preprocessing, analysis, and results export.

## Quick Start

### Prerequisites
- Python 3.7 or higher
- Required Python packages (install via: `pip install -r requirements.txt`)

### Basic Usage

1. Prepare your input data in CSV format
2. Run the pipeline:
   ```bash
   python Baseline_pipeline.py --input /path/to/data --output /path/to/results
   ```

### Example

```bash
# Process data from the data/raw directory and save results to results/baseline
python Baseline_pipeline.py --input data/raw --output results/baseline

# Run with verbose logging
python Baseline_pipeline.py --input data/raw --output results/baseline --verbose
```

## Documentation

For detailed information about the pipeline workflow, methodology, and parameters, please see:
- **[METHODS.md](../METHODS.md)**: Comprehensive documentation suitable for manuscript Methods sections

## Pipeline Stages

1. **Data Import and Validation**: Load and validate input files
2. **Quality Control**: Perform QC checks and generate QC reports
3. **Data Preprocessing**: Normalize, transform, and prepare data
4. **Baseline Analysis**: Execute statistical analyses
5. **Results Export**: Generate tables, figures, and reports

## Output Structure

```
output_directory/
├── pipeline_YYYYMMDD_HHMMSS.log    # Execution log
├── qc_report.txt                    # Quality control report
├── analysis_report.md               # Final analysis summary
├── tables/                          # Statistical tables
│   ├── descriptive_statistics.csv
│   ├── test_results.csv
│   └── effect_sizes.csv
└── figures/                         # Visualization plots
    ├── distributions.png
    ├── group_comparisons.png
    └── correlations.png
```

## Support

For questions or issues, please refer to the METHODS.md documentation or contact the research team.
