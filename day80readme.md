	#pandas #scipy
 
## Goals
 
 1. How to make a compelling argument using data
 2. How to superimpose histograms ot show differences in distributions
 3. How to use  Kernel Density Estimate (KDE) to show a graphic estimate of a distribution.
 4. How to use scipy and test for statistical significance by looking at p-values.
 5. How to highlight different parts of a time series chart in matplotlib.
 6. How to add and configure a Legend in matplotlib
 7. use numpys `.where()` function to process elements depending on a condition
## Preliminary Data Exploration
1. What is the shape of `df_yearly` and `df_monthly`?
```
df_yearly has 12 row and 4 columns: year, births, deaths, clinic
 df_monthly has 98 rows and 3 columns: date, births, deaths
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 12 entries, 0 to 11
Data columns (total 4 columns):
 #   Column  Non-Null Count  Dtype 
---  ------  --------------  ----- 
 0   year    12 non-null     int64 
 1   births  12 non-null     int64 
 2   deaths  12 non-null     int64 
 3   clinic  12 non-null     object
dtypes: int64(3), object(1)
memory usage: 512.0+ bytes
None
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 98 entries, 0 to 97
Data columns (total 3 columns):
 #   Column  Non-Null Count  Dtype         
---  ------  --------------  -----         
 0   date    98 non-null     datetime64[ns]
 1   births  98 non-null     int64         
 2   deaths  98 non-null     int64         
dtypes: datetime64[ns](1), int64(2)
memory usage: 2.4 KB
None
```

`#df_monthly.describe()`

| i | births | deaths |
| ---- | ---- | ---- |
|count|98.00|98.00|
|mean|267.00|22.47|
|std|41.77|18.14|
|min|190.00|0.00|
|25%|242.50|8.00|
|50%|264.00|16.50|
|75%|292.75|36.75|
|max|406.00|75.00|


Average births per month: 22.5
average deaths per month: 267

### Using the annual data, calculate the percentage of women giving birth who died throughout the 1840s at the hospital.

```python
the_1840s = df_yearly[(df_yearly.year > 1839) & (df_yearly.year < 1851)]

total_decade_deaths = the_1840s.groupby('year', as_index=False).deaths.sum().deaths.sum()

total_decade_births = the_1840s.groupby('year', as_index=False).births.sum().births.sum()

percent_deaths = 100 * (total_decade_deaths / total_decade_births)

print(f"{percent_deaths: .2f}% of the women giving birth died.")
```

solution:
```python
prob = df_yearly.deaths.sum() / df_yearly.births.sum() * 100
print(f"the chances of dying : {prob: .3}%")
```

### Twin Axes Plot
#axis
#matplotlib 

```python
years_fmt = mdates.DateFormatter("%Y")
plt.figure(figsize=(14,8), dpi=200)
plt.grid(linestyle="--")
death_ax = plt.gca()
death_ax.set_xlim(left=df_monthly.date.min(),
                 right=df_monthly.date.max())
death_ax.xaxis.set_major_locator(mdates.YearLocator())
death_ax.xaxis.set_minor_locator(mdates.MonthLocator())
death_ax.plot(df_monthly.date,
              df_monthly.deaths,
              color="crimson",
              linestyle="dashed",
             linewidth=2,)

birth_ax = death_ax.twinx()
birth_ax.plot(df_monthly.date,
              df_monthly.births,
              color="skyblue",
             linewidth=3)
death_ax.set_ylabel("Number of Deaths per Month", color="crimson")
birth_ax.set_ylabel("Number of Birthsp per Month", color="Skyblue")
```

## Yearly Data Split by Clinic

**Challenge**: Use plotly to create line charts of the births and deaths of the two different clinics at the Vienna General Hospital. 

```python
death_fig = px.line(df_yearly,
       "year",
       "deaths",
       color="clinic")


birth_fig = px.line(df_yearly,
                          "year",
                          "births",
                          color="clinic")

death_fig.show()
birth_fig.show()

# Clinic 1 appears busier.
# Clinic 1 highest deaths = 518
# Clinic 2 highest deaths = 202
# Hospital has had more patients over time
```

## Calculate the proportion of maternal deaths per clinic.

* Work out the percentage of deaths for each row in the `df_yearly` DataFrame by adding a column called "pct_deaths"