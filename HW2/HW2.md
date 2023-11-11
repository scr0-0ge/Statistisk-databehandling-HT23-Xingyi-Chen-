# HW2

## Apartment Prices
The data found in Booli_sold.csv contains data on sales of apartments in Ekhagen (next to Lappis). Download this file and read it into your code. Note that the data file is of type .csv. If you run in to some issue whilst reading in the data, use Google!

Your task is to :

1. Calculate the price per square meter (ppsqm) for each apartment sold. That is, add a new variable to your table containing the data representing the ppsqm.
2. Rank the top 5 most expensive appartments w.r.t ppsqm? Present this in a table. Hint: Use a sort function.
3. What is the avereage ppsqm in Ekhagen?
4. Highlight an aspect of the data that you find interesting. Explain you choice.



```python
import pandas as pd

# Load the data from the CSV file
file_path = '/home/xyc/Downloads/HW2/Booli_sold.csv'
df = pd.read_csv(file_path)

# Display the first few rows of the dataframe to understand its structure
df.head()

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
      <th>listPrice</th>
      <th>rent</th>
      <th>livingArea</th>
      <th>rooms</th>
      <th>published</th>
      <th>constructionYear</th>
      <th>objectType</th>
      <th>booliId</th>
      <th>soldDate</th>
      <th>soldPrice</th>
      <th>...</th>
      <th>location.position.latitude</th>
      <th>location.position.longitude</th>
      <th>location.position.isApproximate</th>
      <th>location.region.municipalityName</th>
      <th>location.region.countyName</th>
      <th>location.distance.ocean</th>
      <th>source.name</th>
      <th>source.id</th>
      <th>source.type</th>
      <th>source.url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3995000</td>
      <td>4467.0</td>
      <td>73.0</td>
      <td>3.0</td>
      <td>2018-10-15 13:33:18</td>
      <td>1935.0</td>
      <td>Lägenhet</td>
      <td>3263989</td>
      <td>2018-11-08</td>
      <td>3820000</td>
      <td>...</td>
      <td>59.371033</td>
      <td>18.054057</td>
      <td>NaN</td>
      <td>Stockholm</td>
      <td>Stockholms län</td>
      <td>260</td>
      <td>MOHV</td>
      <td>1901865</td>
      <td>Broker</td>
      <td>http://www.mohv.se/</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1995000</td>
      <td>1773.0</td>
      <td>36.0</td>
      <td>1.0</td>
      <td>2018-10-05 14:29:28</td>
      <td>1968.0</td>
      <td>Lägenhet</td>
      <td>3256231</td>
      <td>2018-10-19</td>
      <td>2355000</td>
      <td>...</td>
      <td>59.371242</td>
      <td>18.057821</td>
      <td>NaN</td>
      <td>Stockholm</td>
      <td>Stockholms län</td>
      <td>218</td>
      <td>Notar</td>
      <td>1566</td>
      <td>Broker</td>
      <td>http://www.notar.se/</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5100000</td>
      <td>3839.0</td>
      <td>81.0</td>
      <td>3.0</td>
      <td>2018-09-11 13:44:43</td>
      <td>1936.0</td>
      <td>Lägenhet</td>
      <td>3236660</td>
      <td>2018-09-27</td>
      <td>6110000</td>
      <td>...</td>
      <td>59.371617</td>
      <td>18.054716</td>
      <td>NaN</td>
      <td>Stockholm</td>
      <td>Stockholms län</td>
      <td>186</td>
      <td>Historiska Hem AB</td>
      <td>65645750</td>
      <td>Broker</td>
      <td>http://historiskahem.se/</td>
    </tr>
    <tr>
      <th>3</th>
      <td>5495000</td>
      <td>4483.0</td>
      <td>107.0</td>
      <td>4.0</td>
      <td>2018-08-25 02:56:56</td>
      <td>1936.0</td>
      <td>Lägenhet</td>
      <td>3224374</td>
      <td>2018-09-06</td>
      <td>8050000</td>
      <td>...</td>
      <td>59.371480</td>
      <td>18.053880</td>
      <td>NaN</td>
      <td>Stockholm</td>
      <td>Stockholms län</td>
      <td>218</td>
      <td>Notar</td>
      <td>1566</td>
      <td>Broker</td>
      <td>http://www.notar.se/</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1995000</td>
      <td>1696.0</td>
      <td>29.0</td>
      <td>1.0</td>
      <td>2018-06-15 17:16:19</td>
      <td>1936.0</td>
      <td>Lägenhet</td>
      <td>3185496</td>
      <td>2018-06-18</td>
      <td>2400000</td>
      <td>...</td>
      <td>59.372160</td>
      <td>18.053542</td>
      <td>True</td>
      <td>Stockholm</td>
      <td>Stockholms län</td>
      <td>166</td>
      <td>Mäklarhuset</td>
      <td>204</td>
      <td>Broker</td>
      <td>http://www.maklarhuset.se/</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 27 columns</p>
</div>



### lets do these step by step ：


#### 1. Calculate the price per square meter (ppsqm) for each apartment sold. That is, add a new variable to your table containing the data representing the ppsqm.


```python
# We have already calculated the price per square meter (ppsqm) for each apartment and added it as a new column in the dataframe.
# Let's show the first few rows of the dataframe to illustrate this.

df_with_ppsqm = df.copy()
df_with_ppsqm['ppsqm'] = df_with_ppsqm['soldPrice'] / df_with_ppsqm['livingArea']  # Recalculating to confirm

# Display the first few rows of the dataframe with the new ppsqm column
df_with_ppsqm.head()

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
      <th>listPrice</th>
      <th>rent</th>
      <th>livingArea</th>
      <th>rooms</th>
      <th>published</th>
      <th>constructionYear</th>
      <th>objectType</th>
      <th>booliId</th>
      <th>soldDate</th>
      <th>soldPrice</th>
      <th>...</th>
      <th>location.position.longitude</th>
      <th>location.position.isApproximate</th>
      <th>location.region.municipalityName</th>
      <th>location.region.countyName</th>
      <th>location.distance.ocean</th>
      <th>source.name</th>
      <th>source.id</th>
      <th>source.type</th>
      <th>source.url</th>
      <th>ppsqm</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3995000</td>
      <td>4467.0</td>
      <td>73.0</td>
      <td>3.0</td>
      <td>2018-10-15 13:33:18</td>
      <td>1935.0</td>
      <td>Lägenhet</td>
      <td>3263989</td>
      <td>2018-11-08</td>
      <td>3820000</td>
      <td>...</td>
      <td>18.054057</td>
      <td>NaN</td>
      <td>Stockholm</td>
      <td>Stockholms län</td>
      <td>260</td>
      <td>MOHV</td>
      <td>1901865</td>
      <td>Broker</td>
      <td>http://www.mohv.se/</td>
      <td>52328.767123</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1995000</td>
      <td>1773.0</td>
      <td>36.0</td>
      <td>1.0</td>
      <td>2018-10-05 14:29:28</td>
      <td>1968.0</td>
      <td>Lägenhet</td>
      <td>3256231</td>
      <td>2018-10-19</td>
      <td>2355000</td>
      <td>...</td>
      <td>18.057821</td>
      <td>NaN</td>
      <td>Stockholm</td>
      <td>Stockholms län</td>
      <td>218</td>
      <td>Notar</td>
      <td>1566</td>
      <td>Broker</td>
      <td>http://www.notar.se/</td>
      <td>65416.666667</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5100000</td>
      <td>3839.0</td>
      <td>81.0</td>
      <td>3.0</td>
      <td>2018-09-11 13:44:43</td>
      <td>1936.0</td>
      <td>Lägenhet</td>
      <td>3236660</td>
      <td>2018-09-27</td>
      <td>6110000</td>
      <td>...</td>
      <td>18.054716</td>
      <td>NaN</td>
      <td>Stockholm</td>
      <td>Stockholms län</td>
      <td>186</td>
      <td>Historiska Hem AB</td>
      <td>65645750</td>
      <td>Broker</td>
      <td>http://historiskahem.se/</td>
      <td>75432.098765</td>
    </tr>
    <tr>
      <th>3</th>
      <td>5495000</td>
      <td>4483.0</td>
      <td>107.0</td>
      <td>4.0</td>
      <td>2018-08-25 02:56:56</td>
      <td>1936.0</td>
      <td>Lägenhet</td>
      <td>3224374</td>
      <td>2018-09-06</td>
      <td>8050000</td>
      <td>...</td>
      <td>18.053880</td>
      <td>NaN</td>
      <td>Stockholm</td>
      <td>Stockholms län</td>
      <td>218</td>
      <td>Notar</td>
      <td>1566</td>
      <td>Broker</td>
      <td>http://www.notar.se/</td>
      <td>75233.644860</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1995000</td>
      <td>1696.0</td>
      <td>29.0</td>
      <td>1.0</td>
      <td>2018-06-15 17:16:19</td>
      <td>1936.0</td>
      <td>Lägenhet</td>
      <td>3185496</td>
      <td>2018-06-18</td>
      <td>2400000</td>
      <td>...</td>
      <td>18.053542</td>
      <td>True</td>
      <td>Stockholm</td>
      <td>Stockholms län</td>
      <td>166</td>
      <td>Mäklarhuset</td>
      <td>204</td>
      <td>Broker</td>
      <td>http://www.maklarhuset.se/</td>
      <td>82758.620690</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 28 columns</p>
</div>



#### 2. Rank the top 5 most expensive appartments w.r.t ppsqm? Present this in a table. Hint: Use a sort function.



```python
# Sorting the dataframe by 'ppsqm' in descending order to find the top 5 most expensive apartments
top_5_expensive_sorted = df_with_ppsqm.sort_values(by='ppsqm', ascending=False).head(5)

# Selecting relevant columns for presentation
columns_to_display = ['booliId', 'soldPrice', 'livingArea', 'ppsqm']
top_5_expensive_table = top_5_expensive_sorted[columns_to_display]

top_5_expensive_table.reset_index(drop=True)

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
      <th>booliId</th>
      <th>soldPrice</th>
      <th>livingArea</th>
      <th>ppsqm</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2018259</td>
      <td>2500000</td>
      <td>24.0</td>
      <td>104166.666667</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2125576</td>
      <td>2450000</td>
      <td>24.0</td>
      <td>102083.333333</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2078171</td>
      <td>2300000</td>
      <td>23.0</td>
      <td>100000.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2330886</td>
      <td>2210000</td>
      <td>25.0</td>
      <td>88400.000000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2067384</td>
      <td>2300000</td>
      <td>26.9</td>
      <td>85501.858736</td>
    </tr>
  </tbody>
</table>
</div>



#### 3. What is the avereage ppsqm in Ekhagen?


```python
# Calculate the average price per square meter (ppsqm) in Ekhagen
average_ppsqm_ekhagen = df_with_ppsqm['ppsqm'].mean()
average_ppsqm_ekhagen

```




    58759.382760081724



#### 4. Highlight an aspect of the data that you find interesting. Explain you choice.

An interesting aspect of the dataset is the potential relationship between the construction year of the apartments and their selling price per square meter (ppsqm). This relationship can provide insights into how the age or historical value of a building influences its market value.

Older buildings might have a higher value due to their historical significance, architectural uniqueness, or because they are located in well-established, desirable neighborhoods. Conversely, newer buildings might command higher prices due to modern amenities, energy efficiency, and contemporary design.

Analyzing this relationship can shed light on the real estate market's dynamics in Ekhagen, particularly how buyers value historical charm versus modern conveniences. This aspect is not only relevant for understanding current market trends but also for predicting future real estate developments and investments. It would be fascinating to explore this further, possibly revealing trends that could inform both buyers and sellers in the market.

## The Swedish Election of 2018

The data found in 2018_R_per_kommun.csv contains data on voting in the 2018 Swedish election. Your job is to analyize the data by completeing the tasks below.

Note that csv files can have different seperators, delimeters and decimal signs. Take this into account when you are reading in this dataset.

Your task is to：
1. Calculate the total number of legitimate votes (Giltiga Röster) in Stockholm during the election. That is, sum upp the number of legitimate votes for all municipalities (kommun) in Stockholm.
2. In which municipality did the social democratic party (Social demokraterna, S) garner the hightest voting percentage?
3. Rank the (3) municipialities with highest participation (valdeltagande). Present this in the form of a table.


```python
import pandas as pd

# Load the data from the CSV file
file_path = '/home/xyc/Downloads/2018_R_per_kommun.csv'
df = pd.read_csv(file_path, delimiter=';', decimal=',', encoding='utf-8-sig')

# Display the first few rows of the dataframe to understand its structure
df.head()

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
      <th>LÄNSKOD</th>
      <th>KOMMUNKOD</th>
      <th>LÄNSNAMN</th>
      <th>KOMMUNNAMN</th>
      <th>M</th>
      <th>C</th>
      <th>L</th>
      <th>KD</th>
      <th>S</th>
      <th>V</th>
      <th>...</th>
      <th>TRP</th>
      <th>VL-S</th>
      <th>ÖVR</th>
      <th>OGEJ</th>
      <th>BLANK</th>
      <th>OG</th>
      <th>RÖSTER GILTIGA</th>
      <th>RÖSTANDE</th>
      <th>RÖSTBERÄTTIGADE</th>
      <th>VALDELTAGANDE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>14</td>
      <td>Stockholms län</td>
      <td>Upplands Väsby</td>
      <td>23.11</td>
      <td>6.26</td>
      <td>5.66</td>
      <td>6.71</td>
      <td>26.97</td>
      <td>8.01</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.04</td>
      <td>0.69</td>
      <td>0.06</td>
      <td>25830</td>
      <td>26036</td>
      <td>30740</td>
      <td>84.70</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>15</td>
      <td>Stockholms län</td>
      <td>Vallentuna</td>
      <td>28.29</td>
      <td>10.11</td>
      <td>7.73</td>
      <td>7.58</td>
      <td>18.91</td>
      <td>4.47</td>
      <td>...</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.00</td>
      <td>0.02</td>
      <td>0.59</td>
      <td>0.08</td>
      <td>20952</td>
      <td>21099</td>
      <td>23438</td>
      <td>90.02</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>17</td>
      <td>Stockholms län</td>
      <td>Österåker</td>
      <td>29.68</td>
      <td>9.35</td>
      <td>7.48</td>
      <td>7.13</td>
      <td>19.71</td>
      <td>4.97</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.01</td>
      <td>0.03</td>
      <td>0.69</td>
      <td>0.13</td>
      <td>27711</td>
      <td>27947</td>
      <td>31309</td>
      <td>89.26</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>20</td>
      <td>Stockholms län</td>
      <td>Värmdö</td>
      <td>27.49</td>
      <td>9.76</td>
      <td>6.40</td>
      <td>5.89</td>
      <td>20.48</td>
      <td>6.02</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.01</td>
      <td>0.04</td>
      <td>0.67</td>
      <td>0.07</td>
      <td>28115</td>
      <td>28335</td>
      <td>31371</td>
      <td>90.32</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>23</td>
      <td>Stockholms län</td>
      <td>Järfälla</td>
      <td>23.96</td>
      <td>6.31</td>
      <td>6.04</td>
      <td>6.29</td>
      <td>27.68</td>
      <td>8.75</td>
      <td>...</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0.02</td>
      <td>0.04</td>
      <td>0.53</td>
      <td>0.07</td>
      <td>45654</td>
      <td>45948</td>
      <td>53230</td>
      <td>86.32</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 45 columns</p>
</div>



### lets do these step by step ：


#### 1. Calculate the total number of legitimate votes (Giltiga Röster) in Stockholm during the election. That is, sum upp the number of legitimate votes for all municipalities (kommun) in Stockholm.


```python
# Filter the dataframe to include only rows for Stockholm County
stockholm_votes = df[df['LÄNSNAMN'] == 'Stockholms län']

# Sum up the legitimate votes
total_legitimate_votes = stockholm_votes['RÖSTER GILTIGA'].sum()

# Print the result
print(f'Total number of legitimate votes in Stockholm: {total_legitimate_votes}')
```

    Total number of legitimate votes in Stockholm: 1426237


#### 2. In which municipality did the social democratic party (Social demokraterna, S) garner the hightest voting percentage?



```python
# Find the row where the Social Democratic Party got the highest voting percentage
max_s_vote_row = df[df['S'] == df['S'].max()]

# Extract the municipality name
municipality_with_max_s_vote = max_s_vote_row['KOMMUNNAMN'].iloc[0]

# Print the result
print(f'The Social Democratic Party received the highest voting percentage in {municipality_with_max_s_vote}.')
```

    The Social Democratic Party received the highest voting percentage in Munkfors.


#### 3. Rank the (3) municipialities with highest participation (valdeltagande). Present this in the form of a table.


```python
# Sort the dataframe by voter participation in descending order
sorted_df = df.sort_values(by='VALDELTAGANDE', ascending=False)

# Select the top 3 municipalities
top_3_municipalities = sorted_df.head(3)

# Select only the relevant columns - 'KOMMUNNAMN' and 'VALDELTAGANDE'
top_3_municipalities = top_3_municipalities[['KOMMUNNAMN', 'VALDELTAGANDE']]

# Present the data as a table
top_3_municipalities_table = top_3_municipalities.to_string(index=False)
print(top_3_municipalities_table)
```

    KOMMUNNAMN  VALDELTAGANDE
         Lomma          93.86
          Habo          93.35
      Vellinge          93.13



```python

```
