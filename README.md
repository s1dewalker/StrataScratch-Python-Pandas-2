# StrataScratch Analytical Questions | Python-Pandas

#### View StrataScratch Analytical Questions | Python-Pandas, here: [stratascratch.com](https://platform.stratascratch.com/coding?code_type=2&is_freemium=1&order_field=difficulty)

#### [Easy](https://github.com/s1dewalker/StrataScratch-Python-Pandas) | Medium | Hard
<br/>

## [Number Of Units Per Nationality](https://platform.stratascratch.com/coding/10156-number-of-units-per-nationality?code_type=2)

First attempt:
```python
joined_df = pd.merge(airbnb_units, airbnb_hosts, how = 'left', on = 'host_id' )

c = (joined_df['age'] < 30) & (joined_df['unit_type'] == 'Apartment')

filtered_df = joined_df[c].groupby(['country'], as_index = False).nunique()

filtered_df[['country', 'unit_id']]
```
<br/>
Notes: We can filter the merged dataframe to include only rows where the age is less than 30 and the unit_type is 'Apartment'. Finally, we can group the filtered dataframe by nationality and count the unique unit_ids.
