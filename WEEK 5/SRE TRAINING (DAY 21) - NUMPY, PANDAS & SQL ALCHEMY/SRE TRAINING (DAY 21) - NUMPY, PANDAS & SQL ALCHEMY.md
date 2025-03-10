# SRE TRAINING (DAY 21) - NUMPY, PANDAS & SQLALCHEMY

## 1. Introduction to NumPy
NumPy is a powerful numerical computing library in Python that provides support for large, multi-dimensional arrays and matrices, along with a collection of mathematical functions to operate on these arrays.

## 2. Creating NumPy Arrays
```python
import numpy as np

# Creating a 1D array
arr1 = np.array([1, 2, 3, 4, 5])
print("1D Array:", arr1)

# Creating a 2D array
arr2 = np.array([[1, 2, 3], [4, 5, 6]])
print("2D Array:\n", arr2)
```

## 3. Array Operations
```python
# Basic arithmetic operations
arr = np.array([10, 20, 30, 40])
print("Addition:", arr + 5)
print("Multiplication:", arr * 2)
```

## 4. Indexing and Slicing
```python
arr = np.array([10, 20, 30, 40, 50])
print("Element at index 2:", arr[2])
print("Sliced array:", arr[1:4])
```

## 5. Reshaping Arrays
```python
arr = np.arange(1, 10)
reshaped_arr = arr.reshape(3, 3)
print("Reshaped 3x3 Array:\n", reshaped_arr)
```

## 6. Statistical Functions
```python
arr = np.array([10, 20, 30, 40, 50])
print("Mean:", np.mean(arr))
print("Standard Deviation:", np.std(arr))
print("Sum:", np.sum(arr))
```

## 7. Working with Excel Files using Pandas
Pandas provides an easy interface to read and modify Excel files.

### 7.1 Reading an Excel File
```python
import pandas as pd

# Reading an Excel file
excel_data = pd.read_excel("data.xlsx")
print("Excel Data:\n", excel_data.head())
```

### 7.2 Editing and Saving an Excel File
```python
# Modifying a column
excel_data["NewColumn"] = excel_data["ExistingColumn"] * 2

# Saving the modified file
excel_data.to_excel("modified_data.xlsx", index=False)
print("Excel file updated and saved!")
```
# Setting Up SQLite with SQLAlchemy in WSL & Windows

## 1️⃣ Creating a Database Directory
To keep things organized, create a dedicated directory for your database in WSL:
```bash
mkdir -p ~/databases
```
This ensures the `databases` folder exists in your home directory.

## 2️⃣ Moving the SQLite Database File
If you already have a database file (`data.db`), move it to this directory:
```bash
mv /home/your_user/basic-module/data.db ~/databases/
```

## 3️⃣ Verifying the Database File
Check if the database file exists and has the correct permissions:
```bash
ls -l ~/databases/data.db
```
If you face permission issues, run:
```bash
chmod 777 ~/databases/data.db
```

## 4️⃣ Navigating to the Project Directory
Change to the directory where your Python script is stored:
```bash
cd ~/alchemy_practice2
```

## 5️⃣ Installing Required Packages
Ensure Python and SQLAlchemy are installed:
```bash
sudo apt update
sudo apt install python3-pip
pip3 install sqlalchemy
```

## 6️⃣ Writing the Python Script
Create a Python script (e.g., `main.py`) to connect SQLAlchemy with SQLite:
```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

# Define database path
db_path = "sqlite:////home/rhear/databases/mydatabase.db"
engine = create_engine(db_path, echo=True)

SessionLocal = sessionmaker(bind=engine)
Base = declarative_base()

class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True)

Base.metadata.create_all(engine)
```
### 📝 Explanation of the Script
- **Database Path:** The database is stored at `/home/rhear/databases/mydatabase.db`.
- **Engine Creation:** `create_engine()` initializes a connection to the SQLite database.
- **Session:** `SessionLocal = sessionmaker(bind=engine)` creates a session for interacting with the database.
- **Base Class:** `Base = declarative_base()` is the base class for defining table structures.
- **User Table:**
  - `id`: Primary key (Integer, unique for each user).
  - `name`: String field with an index for optimized queries.
- **Table Creation:** `Base.metadata.create_all(engine)` ensures the `users` table is created in the database.

## 7️⃣ Running the Python Script
Execute the script to create the database and table:
```bash
python3 main.py
```

## 8️⃣ Accessing SQLite Database in WSL
Open SQLite CLI and check the tables:
```bash
sqlite3 ~/databases/mydatabase.db
.tables
```
To view user records:
```sql
SELECT * FROM users;
```

## 9️⃣ Accessing SQLite Database from Windows
1. **Using Windows Command Prompt**
   ```sh
   sqlite3 C:\\Users\\rhear\\alchemy_practice2\\mydatabase.db
   ```
   Snapshot : ![Output](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%205/SRE%20TRAINING%20(DAY%2021)%20-%20NUMPY%2C%20PANDAS%20%26%20SQL%20ALCHEMY/Images/cmd_db.png)
   
3. **Using DB Browser for SQLite**
   - Download [DB Browser](https://sqlitebrowser.org/dl/)
   - Accessed at `C:\Users\rhear\alchemy_practice2\mydatabase.db`
   Snapshot : ![Output](https://github.com/rhearobinson19/Mthree_Daily_Documentation/blob/main/WEEK%205/SRE%20TRAINING%20(DAY%2021)%20-%20NUMPY%2C%20PANDAS%20%26%20SQL%20ALCHEMY/Images/db_wsl.png)

## 🔟 Exiting SQLite CLI
To exit SQLite:
```sql
.exit
```

## ✅ Summary
- Created a **database directory** (`~/databases/`)
- Installed **SQLAlchemy**
- Wrote a Python script to **create a table**
- **Ran the script** and inserted data
- Accessed SQLite from both **WSL & Windows**
- Explained how the script works


# Pandas Setup and Execution Guide

## Prerequisites

- Python installed (check using `python3 --version`)
- Virtual Environment support (`venv`)
- VS Code, Jupyter Notebook, or Terminal access

## 1️⃣ Setting Up a Virtual Environment

Run the following commands:

```bash
# Navigate to your project directory
cd /mnt/c/Users/rhear/pandas

# Create a virtual environment
python3 -m venv venv

# Activate the virtual environment
source venv/bin/activate
```


## 2️⃣ Installing Pandas

```bash
pip install pandas
```

Check if Pandas is installed correctly:

```bash
python -c "import pandas as pd; print(pd.__version__)"
```

## 3️⃣ Running the Pandas Code

Create a `script.py` file and add the following code:

```python
import pandas as pd

# Create an initial DataFrame
df_csv = pd.DataFrame({'Name': ['Alice', 'Bob'], 'Age': [25, 30], 'City': ['NY', 'LA']})

# Duplicate the DataFrame by concatenating it with itself
df_csv = pd.concat([df_csv, df_csv])
print(df_csv)

# Determine the next available index
next_idx = len(df_csv)
print(next_idx)

# Add a new row with custom values
df_csv.loc[next_idx] = ['Ashfdkjshn', 20, 'New York']
print(df_csv)

# Save the final DataFrame to a CSV file
df_csv.to_csv('data_new.csv', index=False)
```

### **Explanation of the Script:**

1. **Create an initial DataFrame**
   - A DataFrame `df_csv` is created with three columns: `Name`, `Age`, and `City`.
   - Initial data contains two rows: `Alice` and `Bob`.

2. **Duplicate the DataFrame**
   - The DataFrame is concatenated with itself, effectively doubling the number of rows.

3. **Determine the Next Index**
   - `next_idx` is calculated as the length of `df_csv`.
   - This determines the position where the new row will be added.

4. **Add a New Row**
   - A new row with custom values (`Rhea`, `23`, `New York`) is added at the calculated index.

5. **Save the DataFrame to a CSV File**
   - The modified DataFrame is saved as `data_new.csv` without an index column.

## 4️⃣ Executing the Script

Run the script inside the virtual environment:

```bash
python script.py
```

### **Output:**

```
    Name  Age City
0  Alice   25   NY
1    Bob   30   LA
0  Alice   25   NY
1    Bob   30   LA
4  Rhea    23   New York
```

A new row with `Rhea` is added, and the data is saved as `data_new.csv`.
