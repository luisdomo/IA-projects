# Pistachio DataBase Exploration
The purpose of this project is to examine a database of pistachios utilizing techniques of visualization, normalization, and generation of synthetic data.

## Database
The database was obtained from [muratkoklu datasets](https://www.muratkoklu.com/datasets/). It has 12 morphological and 4 shape features described in the following table:

| Attribute         | Description or Equation  |
| :-                | :-                       |
| AREA (A)          | $A = \sum_{r,c} 1$ |
| PERIMETER (P)     | It is defined as the length of its edge |
| MAJOR_AXIS (L)    | The distance between the ends of the longest line that can be drawn |
| MINOR_AXIS (I)    | The longest line that can be drawn perpendicular to the main axis |
| ECCENTRICITY (Ec) | Eccentricity of the ellipse having the same moments as the region |
| EQDIASQ (Ed)      | $Ed = \sqrt{\frac{4*A}{\pi}}$ |
| SOLIDITY (S)      | $S = \frac{A}{C}$ |
| CONVEX_AREA (C)   | Number of pixels of the smallest convex polygon that can contain the pistachio area |
| EXTENT (Ex)       | $Ex = \frac{A}{A_B}$, where $A_B$ = Area of the bounding rectangle |
| ASPECT_RATIO (K)  | $K = \frac{L}{l}$ |
| ROUNDNESS (R)     | $R = \frac{4 \pi A}{P^2}$ |
| COMPACTNESS (CO)  | $CO = \frac{Ed}{L}$ |
| SHAPEFACTOR_1 (SF1) | $SF1 = \frac{L}{A} $ |
| SHAPEFACTOR_2 (SF2) | $SF2 = \frac{l}{A}$ |
| SHAPEFACTOR_3 (SF3) | $SF3 = \frac{A}{\frac{L}{2} * \frac{L}{2} * \pi }$ |
| SHAPEFACTOR_4 (SF4) |  $SF4 = \frac{A}{\frac{L}{2} * \frac{l}{2} * \pi }$ |

The morphological characteristics of pistachio is shown in the following figure.

<img src="img/Pistachio_features.png" width="700">

## Analize data

To analyze the information contained in the database, first do the following:
- Examine the number of instances and attributes
- Determine the type of data
- Check for missing data
- Evaluate the class balance
- Identify any outlier data
- Calculate the minimum, maximum, mean, and standard deviation
- Determine the distribution type

### Import the database and libraries

```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

directory = './Datasets/Pistachio_16_Features_Dataset.csv'
data = pd.read_csv(directory, delimiter=",")
```

> [!TIP]
> Store the attribute name in one list and the desition attribute in another list
```python
nom_col = list(data.columns)
categories = nom_col[-1]
nom_col = nom_col[:-1]
```


### Examine number of instances and attributes of the database
```python
num_inst = data.shape[0]
num_atri = data.shape[1]

print("Instances:", num_inst)
print("Attributes:", num_atri)
```

Instances: 2148 <br>
Attributes: 17

### Determine the type of data by attribute

```python
data.dtypes
```
AREA               int64 <br>
PERIMETER        float64 <br>
MAJOR_AXIS       float64 <br>
MINOR_AXIS       float64 <br>
ECCENTRICITY     float64 <br>
EQDIASQ          float64 <br>
SOLIDITY         float64 <br>
CONVEX_AREA        int64 <br>
EXTENT           float64 <br> 
ASPECT_RATIO     float64 <br>
ROUNDNESS        float64 <br>
COMPACTNESS      float64 <br>
SHAPEFACTOR_1    float64 <br>
SHAPEFACTOR_2    float64 <br>
SHAPEFACTOR_3    float64 <br>
SHAPEFACTOR_4    float64 <br>
Class             object <br>
dtype: object

### Check for missing data (empty spaces)

```python
data.isnull().sum()
```
AREA             0 <br>
PERIMETER        0 <br>
MAJOR_AXIS       0 <br>
MINOR_AXIS       0 <br>
ECCENTRICITY     0 <br>
EQDIASQ          0 <br>
SOLIDITY         0 <br>
CONVEX_AREA      0 <br>
EXTENT           0 <br>
ASPECT_RATIO     0 <br>
ROUNDNESS        0 <br>
COMPACTNESS      0 <br>
SHAPEFACTOR_1    0 <br>
SHAPEFACTOR_2    0 <br>
SHAPEFACTOR_3    0 <br>
SHAPEFACTOR_4    0 <br>
Class            0 <br>
dtype: int64

### Evaluate the class balance

```python
class_balance = data['Class'].value_counts()

num_kirmizi = class_balance.iloc[0]
num_siit = class_balance.iloc[1]

print('Kirmizi_Pistachio:', num_kirmizi, 'datos = {:.2f}'.format(num_kirmizi/num_inst*100),'%')
print('Siit_Pistachio:', num_siit, 'datos = {:.2f}'.format(num_siit/num_inst*100),'%')
```

Kirmizi_Pistachio: 1232 datos = 57.36 % <br>
Siit_Pistachio: 916 datos = 42.64 %

### Identify any outlier data

This method consists of calculating the first and third quartiles, calculating the inter quantile range (IQR) and the lower and upper limits. <br>

To calculate the quartiles, we use the quartile function of the pandas library, the IQR is the difference between quartile 3 (Q3) and quartile 1 (Q1): $$IQR = Q3-Q1$$ the lower limit is calculated as $$Linf = Q1 - (1.5 * IQR)$$ while the upper limit is calculated as $$Lsup = Q3 + (1.5 * IQR)$$.

```python
def outliers_list(A):
    # Create index
    index = np.arange(0,A.shape[0])
    # Create empty list to add outlier data
    errors = []
    # try/except to avoid non-numeric data types
    try:
        # Define the first quartile (25%)
        Q1 = A.quantile(0.25)
        # Define the third quartile (75%)
        Q3 = A.quantile(0.75)
        # Calculate IQR
        IQR = Q3-Q1
        # Calculate limits
        limite_inf = Q1 - (1.5*IQR)
        limite_sup = Q3 + (1.5*IQR)
        # Look for outliers
        for ind in index:
            if A[ind] < limite_inf or A[ind] > limite_sup:
                # Add outlier to error list
                errors.append(str(ind)+':'+str(A[ind]))
    except Exception as e:
        print('Invalid data type\n Must be numeric')
        print(e.__class__.__name__, e)
    return errors
```
```python
outliers = []

for col in nom_col:
    list_outliers = outliers_list(data[col])
    print('Outliers from ', col, '\n', list_outliers)
    outliers.append(len(list_outliers))
    
print(outliers)
```
