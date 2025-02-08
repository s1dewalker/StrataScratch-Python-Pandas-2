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
Notes: Elegant coding
<br/>

## [User with Most Approved Flags](https://platform.stratascratch.com/coding/2104-user-with-most-approved-flags?code_type=2)

```python
# Combine first and last names, fill missing values with empty strings
user_flags['full_name'] = user_flags['user_firstname'].fillna('') + ' ' + user_flags['user_lastname'].fillna('')

reqd_df = user_flags.drop(columns = ['user_firstname', 'user_lastname'])

reqd_df = reqd_df.dropna(subset = 'flag_id')

reqd_df2 = reqd_df[['full_name', 'video_id']].sort_values('full_name')

# To get unique counts of 'video_id' per user
final_df = reqd_df2.groupby('full_name', as_index = False).agg(unique_vid = ('video_id', 'nunique'))

final_df[final_df['unique_vid'] == final_df['unique_vid'].max()][['full_name']]
```
Notes: Elegant coding. To get unique counts of 'video_id' per user: `.groupby('full_name')` + `.agg(unique_vid = ('video_id', 'nunique'))`
<br/>

## [Find Students At Median Writing](https://platform.stratascratch.com/coding/9610-find-students-with-a-median-writing-score?code_type=2)

```python
sat_w_median = sat_scores['sat_writing'].median()

sat_scores[sat_scores['sat_writing'] == sat_w_median][['student_id']]
```

<br/>

## [Find the top 10 ranked songs in 2010](https://platform.stratascratch.com/coding/9650-find-the-top-10-ranked-songs-in-2010?code_type=2)

```python
reqd_df = billboard_top_100_year_end[(billboard_top_100_year_end['year'] == 2010) & (billboard_top_100_year_end['year_rank']<=10)] 

# To not show the same song twice.
reqd_df[['year_rank', 'group_name', 'song_name']].drop_duplicates(subset = 'song_name').sort_values('year_rank')
```
Notes: To not show the same song twice: `.drop_duplicates(subset = 'song_name')`
<br/>

## [Classify Business Type](https://platform.stratascratch.com/coding/9726-classify-business-type?code_type=2)

```python
df = sf_restaurant_health_violations.copy()

def classify(name):
    name_l = name.lower()
    if 'restaurant' in name_l:
        return 'restaurant'
    elif 'cafe' in name_l or 'café' in name_l or 'café' in name_l or 'coffee' in name_l:
        return 'cafe'
    elif 'school' in name_l:
        return 'school'
    else:
        return 'other'
        

df['business_type'] = df['business_name'].apply(classify)

# Remove any duplicate entries so that each unique business name appears only once in the final output.
df = df.drop_duplicates(subset = 'business_name')

df[['business_name','business_type']]
```
<br/>
