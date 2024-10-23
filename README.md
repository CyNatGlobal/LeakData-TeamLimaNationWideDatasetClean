# Data Cleaning Script for CSV Files

This project provides a Python script designed to clean and process CSV files, specifically performing tasks such as translating column names, removing outliers, handling missing values, and dropping unnecessary columns. The script is tailored for Google Colab, where it accesses and saves data to Google Drive.

## Features

1. **Google Drive Integration**: The script reads and writes CSV files directly from/to Google Drive.
2. **Column Name Translation**: Translates column names to English using the `googletrans` library.
3. **Outlier Removal**: Identifies and removes outliers from numerical columns using the Z-score method.
4. **Handling Missing Data**: Removes rows with missing values and stores them in a 'garbage bin' for further inspection.
5. **Dropping Unnecessary Columns**: Moves specific columns to a 'garbage bin' and drops them from the main dataset.

## Prerequisites

To run the script, you need the following:

- **Google Colab** (or a local Python environment with access to Google Drive)
- **Python 3.x**
- Required Python Libraries:
  - `pandas`
  - `numpy`
  - `googletrans==3.1.0a0`
  - `os`
  - `csv`
  - `re`

Install the necessary packages in your Colab environment using:

```python
!pip install pandas numpy googletrans==3.1.0a0
