# File Format Converter

## Project Overview

The File Format Converter project is a Python-based data engineering utility designed to read raw retail dataset files from structured directories, apply schema-based column mapping, and convert the source files into JSON format.

The project uses Python modules such as:

- os
- glob
- re
- json
- pandas

The application processes datasets dynamically using reusable functions and schema-driven transformations.

---


## S — Situation

Retail datasets were stored inside multiple dataset folders under the `retail_db` directory. Each dataset file contained raw delimited data without predefined headers.

The challenge was:

- Different datasets required different column structures.
- Column ordering had to match schema definitions.
- Manual conversion of files into JSON format was repetitive and error-prone.
- Dataset paths varied across operating systems using different path separators (`/` and `\\`).
- Large numbers of files needed batch processing.

The project required an automated and reusable file conversion pipeline.

---

## T — Task

The objective was to:

1. Read schema definitions dynamically from `schemas.json`.
2. Extract ordered column names using column positions.
3. Read raw dataset files from the `retail_db` directory.
4. Assign proper column names to datasets.
5. Convert the data into JSON format.
6. Automatically create destination directories.
7. Process multiple datasets dynamically.
8. Build reusable Python functions for scalable file processing.

---

## A — Action

### 1. Importing Required Modules

The project uses the following Python libraries:

```python
import os
import glob
import re
import json
import pandas as pd
```

Purpose of modules:

- `os` → directory and path handling
- `glob` → file pattern searching
- `re` → regular expression processing
- `json` → schema file handling
- `pandas` → data reading and transformation

---

### 2. Extracting Column Names from Schema

A reusable function was created to extract ordered column names from the schema file.

```python
def get_column_names(schemas, ds_name, sorting_key='column_position'):
    columns = schemas[ds_name]
    sorted_columns = sorted(columns, key=lambda x: x[sorting_key])
    return [col['column_name'] for col in sorted_columns]
```

Functionality:

- Reads dataset schema
- Sorts columns using `column_position`
- Returns ordered column names

---

### 3. Reading CSV/Text Files

A function was created to dynamically read dataset files.

```python
def read_csv(file, schemas):

    file_path_list = re.split(r'[\\/]', file)

    ds_name = file_path_list[-2]

    columns = get_column_names(schemas, ds_name)

    df = pd.read_csv(file, names=columns)

    return df
```

Functionality:

- Extracts dataset name from file path
- Handles both Windows and Linux path separators
- Retrieves schema-based columns
- Reads data into a Pandas DataFrame

---

### 4. Exporting Data into JSON Format

A reusable JSON export function was implemented.

```python
def to_json(df, base_dir, ds_name, file_name):

    dir_path = f"{base_dir}/{ds_name}"

    os.makedirs(dir_path, exist_ok=True)

    json_file_path = f"{dir_path}/{file_name}.json"

    df.to_json(json_file_path, orient='records', lines=True)
```

Functionality:

- Creates dataset directories automatically
- Converts DataFrame into line-delimited JSON
- Stores converted output inside `retail_db_json`

---

### 5. Dataset File Conversion Logic

A master conversion function was developed.

```python
def file_converter(src_dir, base_dir, schemas, ds_name):
    
    files = glob.glob(f'{src_dir}/{ds_name}/*')

    for file in files:
        print(f"Processing {file}")
        df = read_csv(file, schemas)
        file_name = re.split(r'[\\/]', file)[-1]
        #print(file_name)
        to_json(df, base_dir, ds_name, file_name) 
```

Functionality:

- Loads schema definitions
- Reads all files from a dataset folder
- Converts source files into DataFrames
- Exports JSON output files

---

### 6. Main Execution Logic

A loop was created to process all dataset folders dynamically.

```python
def process_files(ds_name = None):
    src_dir = 'retail_db'
    base_dir = 'retail_db_json'

    schemas = json.load(open('schemas.json'))
    src_file_names=glob.glob('retail_db/*/*')
    
    if not ds_name:
        ds_name = schemas.keys()
    for ds_name in ds_name:
        file_converter(src_dir, base_dir, schemas, ds_name) 
        print("File Successfully created.")   
```

Functionality:

- Detects all dataset folders
- Calls the conversion function for each dataset
- Automates complete batch processing

---

### 7. Error Handling and Debugging

Several issues were identified and resolved during development:

#### KeyError Issue

Problem:

```python
KeyError: 'retail_db'
```

Cause:

Incorrect path splitting logic.

Solution:

Used:

```python
re.split(r'[\\/]', file)
```

instead of simple string splitting.

---

#### PermissionError Issue

Problem:

```python
PermissionError: [Errno 13]
```

Cause:

A directory was accidentally created using the file name.

Solution:

Separated:

- directory path
- output file path

inside the `to_json()` function.

---

## R — Result

The final solution successfully achieved:

- Automated file conversion pipeline
- Schema-driven column mapping
- Cross-platform path handling
- Batch dataset processing
- JSON output generation
- Reusable and modular Python functions
- Reduced manual effort in data transformation
- Scalable architecture for future file formats

The project now converts retail datasets from raw files into structured JSON format efficiently and consistently.

---

# Project Folder Structure

```text
project/
│
├── retail_db/
│   ├── orders/
│   ├── customers/
│   ├── products/
│
├── retail_db_json/
│   ├── orders/
│   ├── customers/
│   ├── products/
│
├── schemas.json
├── File_Format_Converter.ipynb
└── README.md
```

---

# Features

- Dynamic schema-based processing
- Automatic directory creation
- Cross-platform compatibility
- Batch processing support
- Modular reusable functions
- JSON export support
- Scalable design

---

# Technologies Used

- Python
- Pandas
- JSON
- Regular Expressions
- File System Operations

---


# Conclusion

The File Format Converter project demonstrates a practical implementation of schema-driven ETL-style processing using Python.

The solution automates raw file transformation into structured JSON format while maintaining modularity, scalability, and reusable architecture principles.

