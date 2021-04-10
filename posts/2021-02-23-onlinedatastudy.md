온라인 판매 데이터 분석 공부


```python
import numpy as np
import pandas as pd
import math
import datetime
import matplotlib.pyplot as plt
import openpyxl
%matplotlib inline
```


```python
retail_df = pd.read_csv("Online Retail.csv")
```


```python
retail_df['InvoiceDate'] = pd.to_datetime(retail_df['InvoiceDate'])
```


```python
retail_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>InvoiceNo</th>
      <th>StockCode</th>
      <th>Description</th>
      <th>Quantity</th>
      <th>InvoiceDate</th>
      <th>UnitPrice</th>
      <th>CustomerID</th>
      <th>Country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>536365</td>
      <td>85123A</td>
      <td>WHITE HANGING HEART T-LIGHT HOLDER</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.55</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>1</th>
      <td>536365</td>
      <td>71053</td>
      <td>WHITE METAL LANTERN</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>2</th>
      <td>536365</td>
      <td>84406B</td>
      <td>CREAM CUPID HEARTS COAT HANGER</td>
      <td>8</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.75</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>3</th>
      <td>536365</td>
      <td>84029G</td>
      <td>KNITTED UNION FLAG HOT WATER BOTTLE</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>4</th>
      <td>536365</td>
      <td>84029E</td>
      <td>RED WOOLLY HOTTIE WHITE HEART.</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
  </tbody>
</table>
</div>




```python
retail_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 541909 entries, 0 to 541908
    Data columns (total 8 columns):
     #   Column       Non-Null Count   Dtype         
    ---  ------       --------------   -----         
     0   InvoiceNo    541909 non-null  object        
     1   StockCode    541909 non-null  object        
     2   Description  540455 non-null  object        
     3   Quantity     541909 non-null  int64         
     4   InvoiceDate  541909 non-null  datetime64[ns]
     5   UnitPrice    541909 non-null  float64       
     6   CustomerID   406829 non-null  float64       
     7   Country      541909 non-null  object        
    dtypes: datetime64[ns](1), float64(2), int64(1), object(4)
    memory usage: 33.1+ MB



```python
retail_df = retail_df[retail_df['Quantity']>0]
retail_df = retail_df[retail_df['UnitPrice']>0]
retail_df = retail_df[retail_df['CustomerID'].notnull()]
retail_df = retail_df[retail_df['Country']=='United Kingdom']
```


```python
retail_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 354321 entries, 0 to 541893
    Data columns (total 8 columns):
     #   Column       Non-Null Count   Dtype         
    ---  ------       --------------   -----         
     0   InvoiceNo    354321 non-null  object        
     1   StockCode    354321 non-null  object        
     2   Description  354321 non-null  object        
     3   Quantity     354321 non-null  int64         
     4   InvoiceDate  354321 non-null  datetime64[ns]
     5   UnitPrice    354321 non-null  float64       
     6   CustomerID   354321 non-null  float64       
     7   Country      354321 non-null  object        
    dtypes: datetime64[ns](1), float64(2), int64(1), object(4)
    memory usage: 24.3+ MB



```python
retail_df['sale_amount'] = retail_df['Quantity']*retail_df['UnitPrice']
retail_df['CustomerID'] = retail_df['CustomerID'].astype(int)
```


```python
aggregations = {'InvoiceDate':'max','InvoiceNo':'count','sale_amount':'sum'}
cust_df = retail_df.groupby('CustomerID').agg(aggregations)
cust_df = cust_df.rename(columns = {'InvoiceDate':'Recency','InvoiceNo':'Frequency','sale_amount':'Monetary'})
cust_df = cust_df.reset_index()
```


```python
cust_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CustomerID</th>
      <th>Recency</th>
      <th>Frequency</th>
      <th>Monetary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>12346</td>
      <td>2011-01-18 10:01:00</td>
      <td>1</td>
      <td>77183.60</td>
    </tr>
    <tr>
      <th>1</th>
      <td>12747</td>
      <td>2011-12-07 14:34:00</td>
      <td>103</td>
      <td>4196.01</td>
    </tr>
    <tr>
      <th>2</th>
      <td>12748</td>
      <td>2011-12-09 12:20:00</td>
      <td>4595</td>
      <td>33719.73</td>
    </tr>
    <tr>
      <th>3</th>
      <td>12749</td>
      <td>2011-12-06 09:56:00</td>
      <td>199</td>
      <td>4090.88</td>
    </tr>
    <tr>
      <th>4</th>
      <td>12820</td>
      <td>2011-12-06 15:12:00</td>
      <td>59</td>
      <td>942.34</td>
    </tr>
  </tbody>
</table>
</div>




```python
import datetime as dt
cust_df['Recency'] = dt.datetime(2011,12,10) - cust_df['Recency']
cust_df['Recency'] = cust_df['Recency'].apply(lambda x : x.days+1)
```


```python
fig,(ax1,ax2,ax3) = plt.subplots(figsize=(12,4),nrows =1, ncols =3)
ax1.set_title('Recency Histogram')
ax1.hist(cust_df['Recency'])
ax2.set_title('Frequency Histogram')
ax2.hist(cust_df['Frequency'])
ax3.set_title('Monetary Histogram')
ax3.hist(cust_df['Monetary'])
```




    (array([3.887e+03, 1.900e+01, 9.000e+00, 2.000e+00, 0.000e+00, 0.000e+00,
            1.000e+00, 1.000e+00, 0.000e+00, 1.000e+00]),
     array([3.75000000e+00, 2.59691050e+04, 5.19344600e+04, 7.78998150e+04,
            1.03865170e+05, 1.29830525e+05, 1.55795880e+05, 1.81761235e+05,
            2.07726590e+05, 2.33691945e+05, 2.59657300e+05]),
     <BarContainer object of 10 artists>)




    
![png](output_12_1.png)
    



```python

```
