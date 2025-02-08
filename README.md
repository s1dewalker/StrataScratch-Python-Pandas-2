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

## [Election Results](https://platform.stratascratch.com/coding/2099-election-results?code_type=2)

```python
voting_results = voting_results[~voting_results["candidate"].isna()]

voting_results['value'] = voting_results['voter'].apply(lambda x: 1 / (voting_results["voter"] == x).sum())
#voting_results

final_df = voting_results.groupby('candidate', as_index = False)['value'].sum()

final_df[(final_df['value'] == final_df['value'].max())][['candidate']]
```

Notes: we need to calculate the vote value for each voter. <br/>
We can do this by applying a lambda function to the 'voter' column. The lambda function divides 1 by the total number of times the voter appears in the DataFrame
<br/>

## [Flags per Video](https://platform.stratascratch.com/coding/2102-flags-per-video?code_type=2)

```python
# Combine first and last names, fill missing values with empty strings
user_flags['full_name'] = user_flags['user_firstname'].fillna('') + ' ' + user_flags['user_lastname'].fillna('')

reqd_df = user_flags.drop(columns = ['user_firstname', 'user_lastname'])

reqd_df = reqd_df.dropna(subset = 'flag_id')

reqd_df.groupby(['video_id'], as_index = False).agg(num_unique_users = ('full_name', 'nunique'))
```
<br/>
