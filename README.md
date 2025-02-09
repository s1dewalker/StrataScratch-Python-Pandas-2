# StrataScratch Analytical Questions | Python-Pandas

#### View StrataScratch Analytical Questions | Python-Pandas, here: [stratascratch.com](https://platform.stratascratch.com/coding?code_type=2&is_freemium=1&order_field=difficulty)

#### [Easy](https://github.com/s1dewalker/StrataScratch-Python-Pandas) | Medium | [Hard](https://github.com/s1dewalker/StrataScratch-Python-Pandas-3)
<br/>

## #1. [Number Of Units Per Nationality](https://platform.stratascratch.com/coding/10156-number-of-units-per-nationality?code_type=2)

```python
joined_df = pd.merge(airbnb_units, airbnb_hosts, how = 'left', on = 'host_id' )

c = (joined_df['age'] < 30) & (joined_df['unit_type'] == 'Apartment')

filtered_df = joined_df[c].groupby(['country'], as_index = False).nunique()

filtered_df[['country', 'unit_id']]
```
<br/>
Notes: We can filter the merged dataframe to include only rows where the age is less than 30 and the unit_type is 'Apartment'. Finally, we can group the filtered dataframe by nationality and count the unique unit_ids.

## #2. [Share of Active Users](https://platform.stratascratch.com/coding/2005-share-of-active-users?code_type=2) ⭐
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

## #3. [Election Results](https://platform.stratascratch.com/coding/2099-election-results?code_type=2)

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

## #4. [Flags per Video](https://platform.stratascratch.com/coding/2102-flags-per-video?code_type=2)

```python
# Combine first and last names, fill missing values with empty strings
user_flags['full_name'] = user_flags['user_firstname'].fillna('') + ' ' + user_flags['user_lastname'].fillna('')

reqd_df = user_flags.drop(columns = ['user_firstname', 'user_lastname'])

reqd_df = reqd_df.dropna(subset = 'flag_id')

reqd_df.groupby(['video_id'], as_index = False).agg(num_unique_users = ('full_name', 'nunique'))
```
Notes: Elegant coding
<br/>

## #5. [User with Most Approved Flags](https://platform.stratascratch.com/coding/2104-user-with-most-approved-flags?code_type=2) ⭐

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

## #6. [Find Students At Median Writing](https://platform.stratascratch.com/coding/9610-find-students-with-a-median-writing-score?code_type=2)

```python
sat_w_median = sat_scores['sat_writing'].median()

sat_scores[sat_scores['sat_writing'] == sat_w_median][['student_id']]
```

<br/>

## #7. [Find the top 10 ranked songs in 2010](https://platform.stratascratch.com/coding/9650-find-the-top-10-ranked-songs-in-2010?code_type=2)

```python
reqd_df = billboard_top_100_year_end[(billboard_top_100_year_end['year'] == 2010) & (billboard_top_100_year_end['year_rank']<=10)] 

# To not show the same song twice.
reqd_df[['year_rank', 'group_name', 'song_name']].drop_duplicates(subset = 'song_name').sort_values('year_rank')
```
Notes: To not show the same song twice: `.drop_duplicates(subset = 'song_name')`
<br/>

## #8. [Classify Business Type](https://platform.stratascratch.com/coding/9726-classify-business-type?code_type=2)

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

## #9. [Processed Ticket Rate By Type](https://platform.stratascratch.com/coding/9781-find-the-rate-of-processed-tickets-for-each-type?code_type=2) ⭐
First Attempt:
```python
total = facebook_complaints.groupby('type', as_index = False).agg(total = ('processed', 'size'))
t = facebook_complaints[facebook_complaints['processed'] == True].groupby('type', as_index = False).agg(true = ('processed', 'size'))

grouped_df = pd.merge(total, t, on = 'type')

grouped_df['processed_rate'] = grouped_df['true'] / grouped_df['total']

grouped_df[['type','processed_rate']]
```
<br/>

After Refinements:
```python
facebook_complaints['processed_rate'] = facebook_complaints.groupby('type')['processed']\
.transform(lambda x: x.sum() / len(x))

# Remove duplicate rows for unique types
result = facebook_complaints[['type', 'processed_rate']].drop_duplicates()
```
<br/>

Notes: **`transform` is `apply` for grouped df.** <br/>
`transform`: Applies a function to each group and returns a result with the same shape as the original group.<br/>
Elegant coding. <br/>

## #10. [Customer Revenue In March](https://platform.stratascratch.com/coding/9782-customer-revenue-in-march?code_type=2)

```python
reqd_df = orders[(orders['order_date'].dt.year == 2019) & (orders['order_date'].dt.month == 3)]

rev_df = reqd_df.groupby('cust_id', as_index = False).agg(revenue = ('total_order_cost', 'sum'))

rev_df.sort_values('revenue', ascending = False)
```
Notes: For difference in dates, use **.dt.days** on the difference: `(df['end_time'] - df['start_time']).dt.days`
<br/>

## #11. [Titanic Survivors and Non-Survivors](https://platform.stratascratch.com/coding/9881-Titanic-Survivors-and-Non--Survivors?code_type=2)

```python
grouped_df = titanic.groupby(['survived','pclass'], as_index = False).size()

pivot_df = grouped_df.pivot_table(index = 'survived', columns = 'pclass', aggfunc = 'sum').reset_index()

# Directly modify column names
pivot_df.columns = ['survived', 'first_class', 'second_class', 'third_class']

pivot_df
```
<br/>

## #12. [Second Highest Salary](https://platform.stratascratch.com/coding/9892-second-highest-salary?code_type=2)

```python
employee['rank'] = employee['salary'].rank(method = 'dense', ascending = False)
employee[employee['rank'] == 2][['salary']].drop_duplicates()
```
<br/>

## #13. [Employee and Manager Salaries](https://platform.stratascratch.com/coding/9894-employee-and-manager-salaries?code_type=2)

```python
joined_df = employee.merge(employee, left_on ='manager_id', right_on = 'id', suffixes = ("", "_mgr"))

result = joined_df[joined_df['salary'] > joined_df['salary_mgr']][['first_name', 'salary']]
```

Notes: <br/>
This joins: <br/>
Each employee (left table) <br/>
With their manager (right table) <br/>

Using:<br/>
left_on="manager_id" → The employee's manager_id<br/>
right_on="id" → The manager's id<br/>
<br/>

## #14. [Highest Salary In Department](https://platform.stratascratch.com/coding/9897-highest-salary-in-department?code_type=2)

```python
max_sal_dep = employee.groupby(['department'], as_index = False).agg(max_sal = ('salary', 'max'))

merged_df = employee.merge(max_sal_dep, left_on = ['department', 'salary'], right_on = ['department', 'max_sal'])

merged_df[['department', 'first_name', 'salary']]
```
<br/>

## #15. [Highest Number Of Orders](https://platform.stratascratch.com/coding/9909-highest-number-of-orders?code_type=2)

```python
n_of_orders = orders.groupby('cust_id', as_index = False).agg(total_orders = ('total_order_cost', 'size'))

n_of_orders[n_of_orders['total_orders'] == n_of_orders['total_orders'].max()][['cust_id', 'total_orders']]
```
<br/>

## #16. [Highest Cost Orders](https://platform.stratascratch.com/coding/9915-highest-cost-orders?code_type=2)

```python
reqd_o = orders[orders['order_date'].between('2019-02-01','2019-05-01')]

agg_df = reqd_o.groupby(['cust_id','order_date'], as_index = False)\
.agg(max_cost = ('total_order_cost', 'sum'), counts = ('cust_id','count'))

joined_df = pd.merge(agg_df, customers, how = 'left', left_on = 'cust_id', right_on = 'id')

c = joined_df['max_cost'] == joined_df['max_cost'].max()

joined_df[c][['first_name', 'order_date', 'max_cost']]
```
<br/>

## #17. [Largest Olympics](https://platform.stratascratch.com/coding/9942-largest-olympics?code_type=2)

```python
ath = olympics_athletes_events.groupby('games', as_index = False).agg(athletes_count = ('name', 'nunique'))

ath[ath['athletes_count'] == ath['athletes_count'].max()]
```
<br/>

## #18. [Ranking Most Active Guests](https://platform.stratascratch.com/coding/10159-ranking-most-active-guests?code_type=2)

```python
sum_mssgs = airbnb_contacts.groupby('id_guest', as_index = False).agg(sum_n_messages = ('n_messages', 'sum'))

sum_mssgs['ranking'] = sum_mssgs['sum_n_messages'].rank(method = 'dense', ascending = False)
sum_mssgs.sort_values('ranking')[['ranking', 'id_guest', 'sum_n_messages']]
```
<br/>

## #19. [New Products](https://platform.stratascratch.com/coding/10318-new-products?code_type=2)

```python
car_launches_2019 = car_launches[car_launches['year'] == 2019]
car_launches_2020 = car_launches[car_launches['year'] == 2020]

grouped_df1 = car_launches_2019.groupby('company_name', as_index = False)['product_name'].nunique()
grouped_df2 = car_launches_2020.groupby('company_name', as_index = False)['product_name'].nunique()

joined_df = grouped_df1.merge(grouped_df2, how = 'left', on = 'company_name', suffixes = ('_19','_20'))

joined_df['net_new_products'] = joined_df['product_name_20'] - joined_df['product_name_19']
joined_df[['company_name', 'net_new_products']]
```
<br/>

## #20. [Activity Rank](https://platform.stratascratch.com/coding/10351-activity-rank?code_type=2)

```python
grouped_df = google_gmail_emails.groupby('from_user', as_index = False).size()

grouped_df['rank'] = grouped_df['size'].rank(method = 'first', ascending = False)
grouped_df.sort_values('rank')
```
<br/>

## #21. [Highest Energy Consumption](https://platform.stratascratch.com/coding/10064-highest-energy-consumption?code_type=2)

```python
# Joining more than 2 tables:
full_df = fb_eu_energy.merge(fb_asia_energy, how = "outer", on = 'date', suffixes = ("_eu","_asia"))\
.merge(fb_na_energy, how = "outer" , on ="date", suffixes = ('_na')).fillna(0)

full_df['consumption_'] = full_df['consumption_eu'] + full_df['consumption_asia'] + full_df['consumption']

reqd_df = full_df[['date','consumption_']].rename(columns = {'consumption_':'consumption'})

reqd_df[reqd_df['consumption'] == reqd_df['consumption'].max()]
```
<br/>

## #22. [Find the percentage of shipable orders](https://platform.stratascratch.com/coding/10090-find-the-percentage-of-shipable-orders?code_type=2) ⭐

```python
shipable_cust = customers.dropna(subset = 'address')

ids_shipable = shipable_cust['id']

shipable_orders = orders[orders['cust_id'].isin(ids_shipable)]

pct_shipable_orders = shipable_orders.shape[0] * 100 / orders.shape[0]
```
<br/>

## #23. [No Order Customers](https://platform.stratascratch.com/coding/10142-no-order-customers?code_type=2)

```python
orders_feb = orders[(orders['order_date']>='2019-02-01') & (orders['order_date']<='2019-03-01')]

customers[~customers['id'].isin(list(orders_feb['cust_id']))][['first_name']]
```
<br/>
