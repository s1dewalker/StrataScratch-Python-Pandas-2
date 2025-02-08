# StrataScratch Analytical Questions | Python-Pandas

#### View StrataScratch Analytical Questions | Python-Pandas, here: [stratascratch.com](https://platform.stratascratch.com/coding?code_type=2&is_freemium=1&order_field=difficulty)

#### [Easy](https://github.com/s1dewalker/StrataScratch-Python-Pandas) | Medium | Hard
<br/>

## [Number Of Units Per Nationality](https://platform.stratascratch.com/coding/10156-number-of-units-per-nationality?code_type=2)

```python
joined_df = pd.merge(airbnb_units, airbnb_hosts, how = 'left', on = 'host_id' )

c = (joined_df['age'] < 30) & (joined_df['unit_type'] == 'Apartment')

filtered_df = joined_df[c].groupby(['country'], as_index = False).nunique()

filtered_df[['country', 'unit_id']]
```
<br/>
Notes: We can filter the merged dataframe to include only rows where the age is less than 30 and the unit_type is 'Apartment'. Finally, we can group the filtered dataframe by nationality and count the unique unit_ids.

## [Share of Active Users](https://platform.stratascratch.com/coding/2005-share-of-active-users?code_type=2)
First Attempt:
```python
c = fb_active_users['country'] == 'USA'
grouped_df = fb_active_users[c].groupby('status', as_index = False).size()

t = grouped_df.sum().values[1]
s_o = grouped_df[grouped_df['status'] == 'open'].sum().values[1]
round(s_o/t,1)
```

Notes: `.values[1]` to get the values

<br/>

Key refinements:
```python
c = fb_active_users['country'] == 'USA'
grouped_df = fb_active_users[c].groupby('status', as_index = False).size()

# Calculate percentage
grouped_df['percentage'] = (grouped_df['size'] / grouped_df['size'].sum()) 

grouped_df[grouped_df['status'] == 'open'][['percentage']]
```
Notes: `grouped_df['size'] / grouped_df['size'].sum()` for percentage calculation
