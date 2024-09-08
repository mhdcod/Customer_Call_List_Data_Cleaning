
# Customer Call List Data Cleaning Project

## Project Overview
This project involved cleaning a dataset of customer contact information to ensure **accuracy** and **efficiency** for the communication team. The dataset originally contained duplicate entries, inconsistent formatting, and unwanted characters. The goal was to standardize the data, remove unnecessary columns, and prepare it for customer outreach.

## Dataset
The original dataset consisted of customer information with the following columns:
- **CustomerID**: Unique identifier for each customer.
- **First_Name**: Customer's first name.
- **Last_Name**: Customer's last name (containing some typos).
- **Phone_Number**: Contact phone numbers, in inconsistent formats.
- **Address**: A single column with street, state, and zip code combined.
- **Paying Customer**: Indicator of whether the customer is a paying customer ("Yes", "No", with some inconsistencies).
- **Do_Not_Contact**: Indicator of customers who opted not to be contacted.
- **Not_Useful_Column**: An unnecessary column that was removed during the cleaning process.

## Data Cleaning Steps

### 1. **Removing Duplicates**
   - Removed any duplicate entries in the dataset to ensure unique customer records.
   ```python
   df = df.drop_duplicates()
```

### 2. **Dropping Unnecessary Columns**
   - Removed columns that were irrelevant for the communication team, such as `Not_Useful_Column`.
   ```python
   df = df.drop(columns='Not_Useful_Column')
```

### 3. **Cleaning Last Names
  - Removed unwanted characters (/ at the beginning and _ at the end) from the `Last_Name` column to fix typos.
    ```python
    df['Last_Name'] = df['Last_Name'].str.lstrip('/')
    df['Last_Name'] = df['Last_Name'].str.rstrip('_')
    ```

### 4. Standardizing Phone Numbers
  - Removed any non-alphanumeric characters from the Phone_Number column.
  - Converted phone numbers to a standard format (XXX-XXX-XXXX).
```python
  df["Phone_Number"] = df["Phone_Number"].str.replace('[^a-zA-Z0-9]','', regex=True)
  df["Phone_Number"] = df["Phone_Number"].apply(lambda x: str(x))
  df["Phone_Number"] = df["Phone_Number"].apply(lambda x: x[0:3] + '-' + x[3:6] + '-' + x[6:10])
```

### 5. Handling Missing Phone Numbers
  - Replaced any NaN or None values in the Phone_Number column with a blank value.
  - Removed rows where the Phone_Number field was blank.
    ```python
    df["Phone_Number"] = df["Phone_Number"].replace(['Na--', 'nan--'], '')
    for x in df.index:
        if df.loc[x, 'Phone_Number'] == '':
            df.drop(x, inplace=True)
    ```
   
### 6. Splitting Address Information
  - Split the Address column into three separate columns: Street_Address, State, and Zip_Code.
```python
  df[['Street_Address', 'State', 'Zip_Code']] = df['Address'].str.split(',', n=2, expand=True)
```

### 7. Standardizing the 'Paying Customer' Column
  - Converted Yes/No values to Y/N to reduce data size and ensure consistency.
```python
  df['Paying Customer'] = df['Paying Customer'].str.replace('Yes', 'Y')
  df['Paying Customer'] = df['Paying Customer'].str.replace('No', 'N')
```

### 8. Handling Missing Values
  - Replaced any remaining missing values in the dataset with blank strings.
 ```python
  df = df.fillna('')
 ```

### 9. Reorganizing Columns
  - Reordered columns for better readability and dropped the original Address column after splitting it.
```python
  df = df[['CustomerID', 'First_Name', 'Last_Name', 'Phone_Number', 'Street_Address', 'State', 'Zip_Code', 'Paying Customer', 'Do_Not_Contact']]
  df = df.drop(columns='Address')
```

### 10. Exporting the Cleaned Data
  - The cleaned dataset was saved as an Excel file for the communication team to use.
```python
  df.to_excel('cleaned_data.xlsx', index=False)
```

## Outcome

The dataset was cleaned and standardized, making it easier for the communication team to contact customers efficiently.
Duplicates, unnecessary columns, and typos were removed.
Phone numbers were standardized to a consistent format.
Address data was split into separate columns, making it easier to work with.
The cleaned dataset is now ready for use in customer outreach efforts.
