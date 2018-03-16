

```python
import pandas as pd   #Import Pandas Library 
import numpy as np      #Import Numpy library
```


```python
jsonpath = 'purchase_data.json'   #Assign file path to a variable
new_df = pd.read_json(jsonpath)   #Import file into a dataframe
new_df.head()                     #Print first 5 rows of dataframe for validation
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
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_purchases = new_df["Item Name"].count()             #Count total rows to see all purchases
df_players = new_df["SN"].nunique()                    #Count unique number of players
df_items = new_df["Item ID"].nunique()                 #Count unique number of items
df_average_price = round(new_df["Price"].mean(),2)     #Find average price
df_total_revenue = new_df["Price"].sum()               #Find total revenue
```


```python
#Create dataframe with total number of players
players_df = pd.DataFrame({"Player Count": [df_players]})
players_df
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
      <th>Player Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Create a summary Dataframe using above values
pur_analysis_df = pd.DataFrame({"Number of unique items": [df_items],
                           "Average Price": "$" + str(df_average_price),
                           "Number of purchases": [df_purchases],
                           "Total Revenue": "$" + str(df_total_revenue)})
pur_analysis_df
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
      <th>Average Price</th>
      <th>Number of purchases</th>
      <th>Number of unique items</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>$2.93</td>
      <td>780</td>
      <td>183</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create a new datafram with unique players. Keep last value
unique_player_df = new_df.drop_duplicates(["SN"], keep = 'last')

#Count the number of male/female/undisclosed with value counts and use reset index to create dataframe
gender_cnt_df = unique_player_df["Gender"].value_counts().reset_index()

#Add new Column with percentage values
gender_cnt_df['Percentage of players'] = round(gender_cnt_df['Gender']/df_players*100,2)

#Rename the columns
gender_cnt_df.rename(columns = {'index': 'Gender', 'Gender': 'Total Count'}, inplace = True)

#Set Gender as index to display data as per homework instructions
gender_cnt_df.set_index(["Gender"], inplace = True)

#Display PErcentage with correct format
gender_cnt_df["Percentage of players"] = gender_cnt_df["Percentage of players"].map("{:.2f}%".format)
```


```python
gender_cnt_df
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
      <th>Total Count</th>
      <th>Percentage of players</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>465</td>
      <td>81.15%</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>100</td>
      <td>17.45%</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>8</td>
      <td>1.40%</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Purchase analysis (Gender)
purch_anlys_avg_by_gender = new_df.groupby("Gender").Price.mean().reset_index()

#Calculate Total purchase value by gender
purch_anlys_tot_by_gender = new_df.groupby("Gender").Price.sum().reset_index()

#Merge Purchase analysis with total purchase dataframe on gender
merge1 = pd.merge(purch_anlys_avg_by_gender, purch_anlys_tot_by_gender, on="Gender")

#Set Gender as index value
merge1.set_index(["Gender"], inplace = True)

#Merge total counts dataframe with total price and average price dataframe
merge2 = merge1.merge(gender_cnt_df, how = "inner", left_index = True, right_index = True)

#Calculate normalized Totals
merge2['Normalized Totals'] = merge2['Price_y']/merge2['Total Count']

#Format and rename columns to crate final gender purchase analysis dataframe
merge2.rename(columns = {'Price_x': 'Average Purchase Price', 'Price_y': 'Total Purchase Value'}, inplace = True)
merge2.drop(columns = ['Percentage of players'], inplace = True)
merge2["Average Purchase Price"] = merge2["Average Purchase Price"].map("${:.2f}".format)
merge2["Total Purchase Value"] = merge2["Total Purchase Value"].map("${:.2f}".format)
merge2["Normalized Totals"] = merge2["Normalized Totals"].map("${:.2f}".format)
merge2.head()


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
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Total Count</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>100</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>$2.95</td>
      <td>$1867.68</td>
      <td>465</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>8</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>




```python
unique_player_df.head()
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
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
    <tr>
      <th>5</th>
      <td>20</td>
      <td>Male</td>
      <td>10</td>
      <td>Sleepwalker</td>
      <td>1.73</td>
      <td>Tanimnya91</td>
    </tr>
    <tr>
      <th>6</th>
      <td>20</td>
      <td>Male</td>
      <td>153</td>
      <td>Mercenary Sabre</td>
      <td>4.57</td>
      <td>Undjaskla97</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Purchase Analysis Age
#Find % of players
unique_player_df.count()

#Create bins
bins = [0, 9, 14, 19, 24, 29, 34, 39, 49]

#Create labels
group_labels = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]

#Assign bin values to ranges
pd.cut(unique_player_df["Age"], bins, labels = group_labels)

#Assign bins and ranges to unique player dataframe
unique_player_df.loc[:, "Age Range"] = pd.cut(unique_player_df["Age"], bins, labels = group_labels)

#Create dataframe with age range and count unique usernames
age_analysis_df = unique_player_df.groupby("Age Range").SN.count().reset_index()

#Add new Column with percentage values
age_analysis_df['Percentage of players'] = round(age_analysis_df['SN']/df_players*100,2)

#Rename the columns
age_analysis_df.rename(columns = {'SN': 'Total Count'}, inplace = True)

#Assign age range as index
age_analysis_df.set_index(["Age Range"], inplace = True)

#Print Age Demographics Final
age_analysis_df.head(10)
```

    C:\Users\robin\Anaconda3\lib\site-packages\pandas\core\indexing.py:537: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      self.obj[item] = s
    




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
      <th>Total Count</th>
      <th>Percentage of players</th>
    </tr>
    <tr>
      <th>Age Range</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>19</td>
      <td>3.32</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>23</td>
      <td>4.01</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>100</td>
      <td>17.45</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>259</td>
      <td>45.20</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>87</td>
      <td>15.18</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>47</td>
      <td>8.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>27</td>
      <td>4.71</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>11</td>
      <td>1.92</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Purchase analysis by Age
#Add bin information defined above to original new_df dataframe with all rows
pd.cut(new_df["Age"], bins, labels = group_labels)
new_df.loc[:, "Age Range"] = pd.cut(new_df["Age"], bins, labels = group_labels)

#Calculate count for each age group
purch_by_age_count = new_df.groupby("Age Range").Price.count().reset_index()

#Calculate total purchase value for each age group
purch_by_age_sum = new_df.groupby("Age Range").Price.sum().reset_index()

#Merge the count and purchase value dataframes
purch_by_age_merged = purch_by_age_count.merge(purch_by_age_sum, how = 'inner', left_index = True, right_index= True)

#Calculate Average purchase price
purch_by_age_merged["Average Purchase Price"] = round(purch_by_age_merged['Price_y']/purch_by_age_merged['Price_x'],2)

#Drop extra columns and rename other columns in previous dataframe
purch_by_age_merged.drop(['Age Range_y'], axis = 1, inplace = True)

#Rename columns as per instructions in PDF document in Homework section
purch_by_age_merged.rename(columns = {'Age Range_x': 'Age Range', 'Price_x': 'Purchase Count', 'Price_y': 'Total Purchase Value'}, inplace = True)

# Set Age Range as index
purch_by_age_merged.set_index(["Age Range"], inplace = True)

#Merge Age Demographics dataframe to Purchase analysis (by age) dataframe
purch_by_age_final = purch_by_age_merged.merge(age_analysis_df, how = "inner", left_index = True, right_index = True)

#Calculate Normalized Total
purch_by_age_final['Normalized Totals'] = round(purch_by_age_final['Total Purchase Value']/purch_by_age_final['Total Count'],2)

#Drop extra columns and create final Purchase analysis by age dataframe
purch_by_age_final.drop(['Total Count', 'Percentage of players'], axis=1, inplace = True)

#Format amount column values with $ signs
purch_by_age_final["Total Purchase Value"] = purch_by_age_final["Total Purchase Value"].map("${:.2f}".format)
purch_by_age_final["Average Purchase Price"] = purch_by_age_final["Average Purchase Price"].map("${:.2f}".format)
purch_by_age_final["Normalized Totals"] = purch_by_age_final["Normalized Totals"].map("${:.2f}".format)

#Print final purchase analysis by gender dataframe
purch_by_age_final.head(10)

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
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
      <th>Average Purchase Price</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Range</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>28</td>
      <td>$83.46</td>
      <td>$2.98</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>$96.95</td>
      <td>$2.77</td>
      <td>$4.22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>$386.42</td>
      <td>$2.91</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>$978.77</td>
      <td>$2.91</td>
      <td>$3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>$370.33</td>
      <td>$2.96</td>
      <td>$4.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>$197.25</td>
      <td>$3.08</td>
      <td>$4.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>$119.40</td>
      <td>$2.84</td>
      <td>$4.42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>$53.75</td>
      <td>$3.16</td>
      <td>$4.89</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create a data frame form original dataframe to count # of purchases and sum of purchases per player
purch_by_player = new_df.groupby("SN").agg({'Item ID': 'count', 'Price': 'sum'}).reset_index().rename(columns={'Item ID': 'Purchase Count', 'Price': 'Total Purchase Value'})

#Calculate avrage price spent per player by dividing total purchase value by purchase count 
purch_by_player["Average Purchase Price"] = round(purch_by_player['Total Purchase Value']/purch_by_player['Purchase Count'],2)

#Sort player purchase dataframe by total purchase price to get top spenders
purch_by_player.sort_values(by='Total Purchase Value', ascending=False, inplace = True)

#Set SN as index
purch_by_player.set_index(["SN"], inplace = True)

#Format amount column values with $ signs
purch_by_player["Total Purchase Value"] = purch_by_player["Total Purchase Value"].map("${:.2f}".format)
purch_by_player["Average Purchase Price"] = purch_by_player["Average Purchase Price"].map("${:.2f}".format)

#Print top 5 spenders
purch_by_player.head(5)
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
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
      <th>Average Purchase Price</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$17.06</td>
      <td>$3.41</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$13.56</td>
      <td>$3.39</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$12.74</td>
      <td>$3.18</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$12.73</td>
      <td>$4.24</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$11.58</td>
      <td>$3.86</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create a data frame to count items by amount sold
items_info1 = new_df.groupby("Item ID").agg({'Price': 'count'}).reset_index().rename(columns={'Price': 'Purchase Count'})

#Create data fram to find total purchase value by items
items_info2 = new_df.groupby("Item ID").agg({'Price': 'sum'}).reset_index().rename(columns={'Price': 'Total Purchase Value'})

#Create a new datafram with unique items
items_info3 = new_df.drop_duplicates(["Item ID"], keep = 'last')

#Keep Desired columns for final display output
items_info4 = items_info3[['Item ID', 'Item Name', 'Price']]

#Merge all dataframes to get desired information
merge_data1 = pd.merge(items_info4, items_info1, on="Item ID", how = 'inner')
item_info_final = pd.merge(merge_data1, items_info2, on="Item ID", how = 'inner')

#Sort player purchase dataframe by total purchase count to get top items
item_info_final.sort_values(by='Purchase Count', ascending=False, inplace = True)

#Set Item ID and Item Name as index
item_info_final.set_index(["Item ID", "Item Name"], inplace = True)

#Sort item info dataframe by descending total count and purchase value to get most popular items
item_info_final.sort_values(['Purchase Count', 'Total Purchase Value'] , ascending=[False, False], inplace = True)

#Format amount column values with $ signs
item_info_final["Total Purchase Value"] = item_info_final["Total Purchase Value"].map("${:.2f}".format)
item_info_final["Price"] = item_info_final["Price"].map("${:.2f}".format)

#Print most popular items!
item_info_final.head(10)
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
      <th></th>
      <th>Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>$2.35</td>
      <td>11</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>$2.23</td>
      <td>11</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>$4.14</td>
      <td>9</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>$2.07</td>
      <td>9</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>$1.49</td>
      <td>9</td>
      <td>$13.41</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>$1.24</td>
      <td>9</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>$3.61</td>
      <td>8</td>
      <td>$28.88</td>
    </tr>
    <tr>
      <th>152</th>
      <th>Darkheart</th>
      <td>$3.15</td>
      <td>8</td>
      <td>$25.20</td>
    </tr>
    <tr>
      <th>44</th>
      <th>Bonecarvin Battle Axe</th>
      <td>$2.46</td>
      <td>8</td>
      <td>$19.68</td>
    </tr>
    <tr>
      <th>106</th>
      <th>Crying Steel Sickle</th>
      <td>$2.29</td>
      <td>8</td>
      <td>$18.32</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create a data frame to count items by amount sold
item_info1 = new_df.groupby("Item ID").agg({'Price': 'count'}).reset_index().rename(columns={'Price': 'Purchase Count'})

#Create data fram to find total purchase value by items
item_info2 = new_df.groupby("Item ID").agg({'Price': 'sum'}).reset_index().rename(columns={'Price': 'Total Purchase Value'})

#Create a new datafram with unique items
item_info3 = new_df.drop_duplicates(["Item ID"], keep = 'last')

#Keep Desired columns for final display output
item_info4 = item_info3[['Item ID', 'Item Name', 'Price']]

#Merge all dataframes to get desired information
merge_data1 = pd.merge(item_info4, item_info1, on="Item ID", how = 'inner')
itemz_info_final = pd.merge(merge_data1, item_info2, on="Item ID", how = 'inner')

#Sort player purchase dataframe by total purchase count to get top items
itemz_info_final.sort_values(by='Purchase Count', ascending=False, inplace = True)

#Set Item ID and Item Name as index
itemz_info_final.set_index(["Item ID", "Item Name"], inplace = True)

#Sort item info dataframe by purchase value to get most popular items
itemz_info_final.sort_values(['Total Purchase Value'] , ascending=False, inplace = True)

#Format amount column values with $ signs
itemz_info_final["Total Purchase Value"] = itemz_info_final["Total Purchase Value"].map("${:.2f}".format)
itemz_info_final["Price"] = itemz_info_final["Price"].map("${:.2f}".format)

#Print most profitable items!
itemz_info_final.head(10)

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
      <th></th>
      <th>Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>$4.14</td>
      <td>9</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>$4.25</td>
      <td>7</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>$4.95</td>
      <td>6</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>$4.87</td>
      <td>6</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>$3.61</td>
      <td>8</td>
      <td>$28.88</td>
    </tr>
    <tr>
      <th>101</th>
      <th>Final Critic</th>
      <td>$4.62</td>
      <td>6</td>
      <td>$27.72</td>
    </tr>
    <tr>
      <th>7</th>
      <th>Thorn, Satchel of Dark Souls</th>
      <td>$4.51</td>
      <td>6</td>
      <td>$27.06</td>
    </tr>
    <tr>
      <th>145</th>
      <th>Fiery Glass Crusader</th>
      <td>$4.45</td>
      <td>6</td>
      <td>$26.70</td>
    </tr>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>$2.35</td>
      <td>11</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>152</th>
      <th>Darkheart</th>
      <td>$3.15</td>
      <td>8</td>
      <td>$25.20</td>
    </tr>
  </tbody>
</table>
</div>


