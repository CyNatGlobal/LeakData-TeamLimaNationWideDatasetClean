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
```

# How to Use the Script
## Step 1: Mount Google Drive
First, you need to mount your Google Drive to access and save files:

```python
from google.colab import drive
drive.mount('/content/drive')
```
## Step 2: Read the CSV File
Replace the file_path variable with the path to your CSV file in Google Drive:

```python
file_path = '/content/drive/your_file_path'
df = pd.read_csv(file_path)
print(df.head(20))  # Display the first 20 rows of the dataset
```

## Step 3: Translate Column Names
The script translates column names from their original language to English using the googletrans module. It stores the original column names in a 'garbage bin' for reference:

```python
translator = Translator()

def translate_column_names(df, garbage_bin):
    old_columns = df.columns.tolist()
    translated_columns = [translator.translate(col, dest='en').text for col in old_columns]
    garbage_bin['Old Column Names'] = old_columns
    df.columns = translated_columns
    return df, garbage_bin
```
## Step 4: Remove Outliers
Outliers in numerical columns are detected using the Z-score method. These outliers are stored in the 'garbage bin', and the clean data is saved back to the DataFrame:

```python
def remove_outliers_zscore(df, threshold=3):
    global garbage_bin
    for column in df.select_dtypes(include=np.number).columns:
        z_scores = np.abs((df[column] - df[column].mean()) / df[column].std())
        outliers = df[z_scores > threshold]
        if not outliers.empty:
            outliers['Outlier_Column'] = column
            garbage_bin = pd.concat([garbage_bin, outliers], ignore_index=True)
        df = df[z_scores <= threshold]
    return df, garbage_bin
```
## Step 5: Handle Invalid Values
Rows with missing values are removed from the main DataFrame and moved to the 'garbage bin' for auditing:

```python
def handle_invalid_values(df, garbage_bin):
    invalid_values = df.isnull().any(axis=1)
    garbage_bin = pd.concat([garbage_bin, df[invalid_values]], ignore_index=True)
    df = df.dropna()
    return df, garbage_bin
```

## Step 6: Drop Unnecessary Columns
The script allows you to define a list of columns to drop from the DataFrame. Dropped columns are moved to the 'garbage bin' for further inspection:

```python
columns_to_drop = ['Unnamed: 21', 'gender', 'Birthday', 'educate', 'monthly salary', 'cell phone', 'address', 'City', 'marriage', 'Province', 'BRAND', 'car model', 'color', 'car series', 'post code', 'industry']

for column in columns_to_drop:
    if column in df.columns:
        garbage_bin[column] = df[column]
        df = df.drop(column, axis=1)
    else:
        print(f"Warning: Column '{column}' not found in DataFrame.")
```

## Step 7: Save Processed Data
Once data cleaning is completed, the modified DataFrame and 'garbage bin' are saved to Google Drive:

```python
# Save cleaned DataFrame
df.to_csv('/content/drive/your_file_path', index=False)

# Save 'garbage bin' DataFrame
garbage_bin.to_csv('/content/drive/your_file_path.csv', index=False)
```

### Example File Outputs
- Cleaned Data: /FinalProduct/NationWideClean.csv
- Translated Columns: /FinalProduct/translated_data.csv
- Removed Outliers: /FinalProduct/removedoutlier_data.csv
- Garbage Bin: /Garbage/finalgarbage3.csv

### Customization
- Adjust Z-Score Threshold: Modify the Z-score threshold in remove_outliers_zscore(df, threshold=3) to fine-tune outlier detection.
- Change Columns to Drop: Edit the columns_to_drop list to specify which columns should be dropped.
- Handle Missing Data: You can modify the handle_invalid_values() function to customize how missing values are treated.

### Notes
- Ensure file paths are correctly set based on your Google Drive structure.
- This script can be customized for various datasets by modifying the column names, outlier removal thresholds, and handling rules.
- Before using the script, double-check that all necessary Python packages are installed.

This `README.md` provides detailed instructions for running and customizing the data cleaning script. It explains each function, the purpose of the 'garbage bin', and how to save processed data back to Google Drive.

