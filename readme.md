
# Heroes of Pymoli Data Analysis
*Observable Trend 1: The vast majority, 81%, of purchasing players identify as male and are between 20-24 years old (56.8%). The distribution of purchasers ages is approximately gaussian, centered around 20-24 years old.
*Observable Trend 2: Despite females being disproportionately lower represented in the purchasing population, those who purchase do so at similar rates (~1.3 purchases per player) as males. The average amount females who have made purchases spend is also within 5% of males. This suggests that of those HoP players who purchase items, there is approximately equal proclivities for puchase rates and amounts. Further statistical methods should be employed to validate.
*Observable Trend 3: Among the highest spending players, these players do not generally spend more per purchase than the average (~$4/purchase) and several spent less. Of the items purchased, the most commonly purchased are lower than the average price, but the highest grossing items are almost all (4/5) above the average purchase price. Further analysis is needed to determine the impact of price on purchasing decisions than is available herein.


```python
#Declare and import libraries
import pandas as pd
import numpy as np
#Read data from local JSON into initial dataframe HoPdf
HoPdf = pd.read_json('purchase_data.json',encoding='UTF-8')
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



## Player Count


```python
#Drop duplicate values from the SN column keeping the first instance to show unique purchasers
HoPdf_unique_sn = HoPdf.drop_duplicates(subset=['SN'],keep='first')
#Use the count method on the SN column (or any column) to return a count of purchasers
player_count = HoPdf_unique_sn['SN'].count()
#Use the DataFrame constructor to build this into a DF
player_count_df = pd.DataFrame(data={'Player Count':[player_count]},index=[0],columns=['Player Count'])
player_count_df
```

    573 <class 'numpy.int32'>
    




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



## Purchasing Analysis


```python
#Again drop duplicates from the Item ID field, retaining the first instance in a new DF
#Use count method on ItemID to count the number of unique item ID's purchased
HoPdf_unique_itemid = HoPdf.drop_duplicates(subset=['Item ID'],keep='first')
unique_weapon_int = HoPdf_unique_itemid['Item ID'].count()
#Using the mean method on the Price column of the original DF, store this value in a variable
mean_item_price = round(HoPdf['Price'].mean(),2)
#Use the sum() method on the ['Price'] column which will sum the cost of all transactions
#producing total revenue
total_revenue = round(HoPdf['Price'].sum(),2)
#Because the JSON is a record of in-game transactions over some period, the number of rows 
#in any category is the number of total transactions
total_purchases = HoPdf['Price'].count()
#Construct a DataFrame with these values
total_purch_data = pd.DataFrame(data={'Number of Unique Items':[unique_weapon_int],'Avg Purchase Price':[mean_item_price],
                                     'Total Number of Purchases':[total_purchases],'Total Revenue($)':[total_revenue]})
total_purch_data.head()
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
      <th>Avg Purchase Price</th>
      <th>Number of Unique Items</th>
      <th>Total Number of Purchases</th>
      <th>Total Revenue($)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2.93</td>
      <td>183</td>
      <td>780</td>
      <td>2286.33</td>
    </tr>
  </tbody>
</table>
</div>



## Gender Demographics


```python
#Create a new data frame from the original dataframe with just screennames and genders
unique_sn_gender = HoPdf[['SN','Gender']]
#Drop duplicated SN values, but keep the first instance
unique_sn_gender = unique_sn_gender.drop_duplicates(subset=['SN'],keep='first')
unique_sn_gender_valcounts = unique_sn_gender['Gender'].value_counts() #series
#Use the total_players_int to convert counts to a rounded percentage and rename the column to show it is a percentage value
percent_gender = pd.DataFrame(round((unique_sn_gender['Gender'].value_counts() / total_players_int)*100))
percent_gender = percent_gender.rename(columns={'Gender':'Gender (%)'})
gender_demo = percent_gender.join(unique_sn_gender_valcounts,how='inner')
gender_demo = gender_demo.rename(columns={'Gender':'Total Count'})
gender_demo.head()
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
      <th>Gender (%)</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.0</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.0</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.0</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Gender)


```python
#Create a new data frame using groupby on Gender and count price
purchase_count_gender = HoPdf.groupby(['Gender'])['Price'].count()
purchase_count_gender = pd.DataFrame(purchase_count_gender)
#The below line is not needed in this
purchase_count_gender = purchase_count_gender.join(unique_sn_gender_valcounts,how='right')
purchase_count_gender = purchase_count_gender.join(round(HoPdf.groupby(['Gender'])['Price'].mean(),2),rsuffix='mean')
purchase_count_gender = purchase_count_gender.join(round(HoPdf.groupby(['Gender'])['Price'].sum(),2),rsuffix='sum')
purchase_count_gender['Normalized Totals'] = round(purchase_count_gender['Pricesum']/purchase_count_gender['Gender'],2)
purchase_count_gender = purchase_count_gender.rename(columns={'Price':'Count of Purchases','Pricemean':'Mean Price($)','Pricesum':'Total Revenue($)',
                                                              'Gender':'Player Count','Normalized Totals':'Normalized $/Player'})
purchase_count_gender
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
      <th>Count of Purchases</th>
      <th>Player Count</th>
      <th>Mean Price($)</th>
      <th>Total Revenue($)</th>
      <th>Normalized $/Player</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>465</td>
      <td>2.95</td>
      <td>1867.68</td>
      <td>4.02</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>100</td>
      <td>2.82</td>
      <td>382.91</td>
      <td>3.83</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>8</td>
      <td>3.25</td>
      <td>35.74</td>
      <td>4.47</td>
    </tr>
  </tbody>
</table>
</div>



## Age Demographics and Purchasing Analysis


```python
#Create a list of age bins up to 51 since the max age (HoPdf['Age'].max() returns 45) is 45
age_bins = [0,9,14,19,24,29,34,39,44,49,54]
#Make bin labels that can be used to easily parse the data when tabulated into a df
bin_labels = ['0-9','10-14','15-19','20-24','25-29','30-34','35-39','40-44','45-49','50-54']
#perform the cut on the original dataframe on the ['Age'] series
HoPdf['Age Group'] = pd.cut(HoPdf['Age'],bins=age_bins,labels=bin_labels)
#Use groupby to isolate data by the age bins
age_group_groupby = HoPdf.groupby(['Age Group'])
#As above, use the count, mean, and sum methods on relevant data series to establish
age_group_purchase_counts = age_group_groupby['SN'].count()
age_group_purchase_mean = round(age_group_groupby['Price'].mean(),2)
age_group_purchase_sum = round(age_group_groupby['Price'].sum(),2)
#Import SN's and AgeGroup to identify unique SN's and therefore players
unique_sn_age = HoPdf[['SN','Age Group']]
#Drop duplicated SN values, but keep the first instance
unique_sn_age = unique_sn_age.drop_duplicates(subset=['SN'],keep='first')
#Assign the counts of each age group
unique_sn_age = unique_sn_age['Age Group'].value_counts()
#Use the above to generate a percentage each age bin is of the overall purchasing population
age_group_pct = round((unique_sn_age/player_count)*100,2)
#generate a new dataframe to incorporate the above data
age_demographics_df = pd.DataFrame(age_group_purchase_counts)
age_demographics_df = age_demographics_df.rename(columns={'SN':'Purchase Count'})
age_demographics_df['% of Players'] = age_group_pct
age_demographics_df['Average Purchase Price ($)'] = age_group_purchase_mean
age_demographics_df['Total Revenue ($)'] = age_group_purchase_sum
age_demographics_df["Players"] = unique_sn_age
age_demographics_df['Normalized Totals ($/player)'] = round(age_demographics_df['Total Revenue ($)']/age_demographics_df['Players'],2)
age_demographics_df
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
      <th>% of Players</th>
      <th>Average Purchase Price ($)</th>
      <th>Total Revenue ($)</th>
      <th>Players</th>
      <th>Normalized Totals ($/player)</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0-9</th>
      <td>28</td>
      <td>3.32</td>
      <td>2.98</td>
      <td>83.46</td>
      <td>19</td>
      <td>4.39</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>4.01</td>
      <td>2.77</td>
      <td>96.95</td>
      <td>23</td>
      <td>4.22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>17.45</td>
      <td>2.91</td>
      <td>386.42</td>
      <td>100</td>
      <td>3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>45.20</td>
      <td>2.91</td>
      <td>978.77</td>
      <td>259</td>
      <td>3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>15.18</td>
      <td>2.96</td>
      <td>370.33</td>
      <td>87</td>
      <td>4.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>8.20</td>
      <td>3.08</td>
      <td>197.25</td>
      <td>47</td>
      <td>4.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>4.71</td>
      <td>2.84</td>
      <td>119.40</td>
      <td>27</td>
      <td>4.42</td>
    </tr>
    <tr>
      <th>40-44</th>
      <td>16</td>
      <td>1.75</td>
      <td>3.19</td>
      <td>51.03</td>
      <td>10</td>
      <td>5.10</td>
    </tr>
    <tr>
      <th>45-49</th>
      <td>1</td>
      <td>0.17</td>
      <td>2.72</td>
      <td>2.72</td>
      <td>1</td>
      <td>2.72</td>
    </tr>
    <tr>
      <th>50-54</th>
      <td>0</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.00</td>
      <td>0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



## Top Spenders


```python
#To identify the players (SN) with the highest cost, groupby SN then sum on Price
SN_groupby = HoPdf.groupby(by=['SN'])
#Join on price count, sum, and mean of the groupby object
HoPdf_with_total = HoPdf.join(SN_groupby['Price'].sum(),on='SN',how='inner',rsuffix='Total')
HoPdf_with_total = HoPdf_with_total.join(SN_groupby['Price'].count(),on='SN',how='inner',rsuffix='Count')
HoPdf_with_total = HoPdf_with_total.join(round(SN_groupby['Price'].mean(),2),on='SN',how='inner',rsuffix='Mean')
#Rename PriceTotal to Total Spent($) for clarity
#Sort on newly named column
HoPdf_with_total = HoPdf_with_total.rename(columns={'PriceTotal':'Total Spent($)','PriceCount':'Number of Purchases','PriceMean':'Average Purchase($)'})
HoPdf_with_total = HoPdf_with_total.sort_values(['Total Spent($)'],ascending=False)
#Drop duplicate SNs, keeping first instance to extract top 5 spenders
HoPdf_total_fixed = HoPdf_with_total.drop_duplicates(subset=['SN'],keep='first')
#Drop all rows outside the top 5
HoPdf_total_fixed_five = HoPdf_total_fixed[:5]
#Remove extraneous columns and set index to SN's
HoPdf_topfive = HoPdf_total_fixed_five.drop(labels=['Gender','Item ID','Item Name','Age Group','Age','Price'],axis=1)
HoPdf_topfive = HoPdf_topfive.set_index(['SN'])
HoPdf_topfive.head(5)
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
      <th>Total Spent($)</th>
      <th>Number of Purchases</th>
      <th>Average Purchase($)</th>
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
      <td>17.06</td>
      <td>5</td>
      <td>3.41</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>13.56</td>
      <td>4</td>
      <td>3.39</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>12.74</td>
      <td>4</td>
      <td>3.18</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>12.73</td>
      <td>3</td>
      <td>4.24</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>11.58</td>
      <td>3</td>
      <td>3.86</td>
    </tr>
  </tbody>
</table>
</div>



## Most Popular Items


```python
#To Identify the Most Popular Items, groupby 'Item ID'
ItemID_groupby = HoPdf.groupby(by=['Item ID'])
#Join on ID count, price sum
HoPdf_items = HoPdf.join(ItemID_groupby['Item ID'].count(),on='Item ID',how='inner',rsuffix='Count')
HoPdf_items = HoPdf_items.join(ItemID_groupby['Price'].sum(),on='Item ID',how='inner',rsuffix='Sum')
#Rename Columns
HoPdf_items_fixed = HoPdf_items.rename(columns={'Price':'Item Price($)','Item IDCount':'Times Purchased','PriceSum':'Total Purchase Value($)'})
#Drop duplicate Item ID's while keeping first instance
HoPdf_items_fixed = HoPdf_items_fixed.drop_duplicates(subset=['Item ID'],keep='first')
#Sort by Purchase Count to sort by Popularity
HoPdf_items_sorted = HoPdf_items_fixed.sort_values(['Times Purchased'],ascending=False)
HoPdf_pop_items = HoPdf_items_sorted[:5]
#Remove Unneeded Columns and set index to Item ID's
HoPdf_pop_items = HoPdf_pop_items.drop(labels=['Age', 'Gender','SN','Age Group'],axis=1)
HoPdf_pop_items = HoPdf_pop_items.set_index(['Item ID'])
HoPdf_pop_items.head()
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
      <th>Item Name</th>
      <th>Item Price($)</th>
      <th>Times Purchased</th>
      <th>Total Purchase Value($)</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>2.35</td>
      <td>11</td>
      <td>25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <td>Arcane Gem</td>
      <td>2.23</td>
      <td>11</td>
      <td>24.53</td>
    </tr>
    <tr>
      <th>175</th>
      <td>Woeful Adamantite Claymore</td>
      <td>1.24</td>
      <td>9</td>
      <td>11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Serenity</td>
      <td>1.49</td>
      <td>9</td>
      <td>13.41</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Trickster</td>
      <td>2.07</td>
      <td>9</td>
      <td>18.63</td>
    </tr>
  </tbody>
</table>
</div>



## Most Profitable Items


```python
#Use the above dataframes after dropping duplicates to sort on Total Purchase Value
HoPdf_items_sorted = HoPdf_items_fixed.sort_values(['Total Purchase Value($)'],ascending=False)
HoPdf_prof_items = HoPdf_items_sorted[:5]
#Remove Unneeded Columns and set index to Item ID's
HoPdf_prof_items = HoPdf_prof_items.drop(labels=['Age', 'Gender','SN','Age Group'],axis=1)
HoPdf_prof_items = HoPdf_prof_items.set_index(['Item ID'])
HoPdf_prof_items.head()
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
      <th>Item Name</th>
      <th>Item Price($)</th>
      <th>Times Purchased</th>
      <th>Total Purchase Value($)</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <td>Retribution Axe</td>
      <td>4.14</td>
      <td>9</td>
      <td>37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <td>Spectral Diamond Doomblade</td>
      <td>4.25</td>
      <td>7</td>
      <td>29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Orenmir</td>
      <td>4.95</td>
      <td>6</td>
      <td>29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <td>Singed Scalpel</td>
      <td>4.87</td>
      <td>6</td>
      <td>29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <td>Splitter, Foe Of Subtlety</td>
      <td>3.61</td>
      <td>8</td>
      <td>28.88</td>
    </tr>
  </tbody>
</table>
</div>


