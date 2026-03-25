# Run cell to import libraries and load data sets
import geopandas as gpd 
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib.dates as mdates
import matplotlib.ticker as ticker
import contextily 
import mapclassify 
import folium
import aiohttp
import fsspec
import warnings
warnings.filterwarnings('ignore')
plt.style.use("ggplot")

# Load the COVID-19 data:
df_cases = pd.read_csv("https://raw.githubusercontent.com/babdelfa/project/refs/heads/main/cases_data.csv")
df_deaths = pd.read_csv("https://raw.githubusercontent.com/babdelfa/project/refs/heads/main/deaths_data.csv")    

# Load the GeoDataFrame containing United States geometry shapes (at a county-level):
county_shapes = "https://github.com/babdelfa/gis/blob/main/counties_geometry.zip?raw=true"
with fsspec.open(county_shapes) as counties_file:
    county_shapes = gpd.read_file(counties_file)

# COVID-19 state-level analysis and county choropleth

df_cases.drop(columns=['UID', 'ISO3', 'CODE3', 'LAT', 'LONG_', 'COMBINED_KEY'], inplace=True, errors='ignore')
df_deaths.drop(columns=['iso3', 'late', 'long_', 'combined_key', 'population'], inplace=True, errors='ignore')
df_cases.rename(columns={'FIPS':'fips', 'COUNTY': 'county', 'STATE':'state'}, inplace=True, errors='ignore')
dfcases = pd.melt(df_cases, id_vars=['fips','county','state'], var_name='date', value_name='cases')
dfdeaths = pd.melt(df_deaths, id_vars=['fips','county','state'], var_name='date', value_name='deaths')
df = pd.merge(left=dfcases, right=dfdeaths)
df.date = pd.to_datetime(df['date'])

# Clean and reshape datasets

nameinput = ('Rashed Abusaif')
print("Which state's COVID-19 information would you like to see?")
stateinput = ('Virginia')
print()
print(f'COVID-19 in {stateinput}: Key Statistics')
print('Timeline:')

## 

statedf = df[df['state'] == stateinput]
startdate = statedf[statedf['cases'] > 0]['date'].min()
startdateprint = startdate.strftime("%B %-d, %Y")


# Filter selected state

print(f'Day 0 of COVID-19 in {stateinput}: {startdateprint}')
print()
print(f'{stateinput} Data by Year:')
print(f'2020 (from {startdateprint}):')

year2020 = statedf[(statedf['date'] >= startdate) & (statedf['date'] <= '2020-12-31')]
year2021 = statedf[(statedf['date'] >= '2021-01-01') & (statedf['date'] <= '2021-12-31')]

casesperday2020 = year2020.groupby('date')[['cases']].sum()
cases20 = casesperday2020['cases']
newcases20 = [cases20.iloc[0]] 
for day in range(1, len(cases20)):
    newcases20.append(cases20.iloc[day] - cases20.iloc[day-1])
casesperday2020['newcases'] = newcases20
totalcases20 = casesperday2020['newcases'].sum()
averagedailycases20 = casesperday2020['newcases'].mean().round(2)

# Compute 2020 metrics

print(f' - Total reported cases: {totalcases20:,}')
print(f' - Average daily new cases: {averagedailycases20:,}')

deathsperday2020 = year2020.groupby('date')[['deaths']].sum()
deaths20 = deathsperday2020['deaths']
newdeaths20 = [deaths20.iloc[0]] 
for day in range(1, len(deaths20)):
    newdeaths20.append(deaths20.iloc[day] - deaths20.iloc[day-1])
deathsperday2020['newdeaths'] = newdeaths20
totaldeaths20 = deathsperday2020['newdeaths'].sum()
averagedailydeaths20 = deathsperday2020['newdeaths'].mean().round(2)

# Compute 2020 metrics

print(f' - Total reported deaths: {totaldeaths20:,}')
print(f' - Average daily new deaths: {averagedailydeaths20:,}')

casesperday2021 = year2021.groupby('date')[['cases']].sum()
cases21 = casesperday2021['cases']
newcases21 = [cases21.iloc[0] - cases20.iloc[-1]]
for day in range(1, len(cases21)):
    newcases21.append(cases21.iloc[day] - cases21.iloc[day-1])
casesperday2021['newcases'] = newcases21
totalcases21 = casesperday2021['newcases'].sum()
averagedailycases21 = casesperday2021['newcases'].mean().round(2)

# Compute 2021 metrics

print()
print('2021:')
print(f' - Total reported cases: {totalcases21:,}')
print(f' - Average daily new cases: {averagedailycases21:,}')

deathsperday2021 = year2021.groupby('date')[['deaths']].sum()
deaths21 = deathsperday2021['deaths']
newdeaths21 = [deaths21.iloc[0] - deaths20.iloc[-1]] 
for day in range(1, len(deaths21)):
    newdeaths21.append(deaths21.iloc[day] - deaths21.iloc[day-1])
deathsperday2021['newdeaths'] = newdeaths21
totaldeaths21 = deathsperday2021['newdeaths'].sum()
averagedailydeaths21 = deathsperday2021['newdeaths'].mean().round(2)

# Compute 2021 metrics

print(f' - Total reported deaths: {totaldeaths21:,}')
print(f' - Average daily new deaths: {averagedailydeaths21:,}')
print()

totalcases = (totalcases20 + totalcases21)
totaldeaths = (totaldeaths20 + totaldeaths21)

print(f'Overall Totals in {stateinput} (as of December 31, 2021):')
print(f' - Total cases: {totalcases:,}')
print(f' - Total deaths: {totaldeaths:,}')
print()
print(f'{nameinput}, please select a data visualization option for {stateinput}')
print()
print(f'1. View four subplots showing COVID-19 trends in {stateinput} (2020-2021):')
print('  * Bar Chart of Daily New Cases')
print('  * Line Chart of Cumulative Cases Trend')
print('  * Bar Chart of Daily New Deaths')
print('  * Line Chart of Cumulative Deaths Trend')
print()
print(f'2. View a choropleth map showing total reported cases and deaths by county in {stateinput} as of December 31, 2021.')
print()


##

statedf = statedf[statedf['date'] >= startdate]
casesperday = statedf.groupby('date')[['cases']].sum()
cases = casesperday['cases']
newcases = [cases.iloc[0]] 
for day in range(1, len(cases)):
    newcases.append(cases.iloc[day] - cases.iloc[day-1])
casesperday['newcases'] = newcases
totalcases = casesperday['newcases'].sum()
dailycases = casesperday[['newcases']]

# Build visualization data

deathsperday = statedf.groupby('date')[['deaths']].sum()
deaths = deathsperday['deaths']
newdeaths = [deaths.iloc[0]] 
for day in range(1, len(deaths)):
    newdeaths.append(deaths.iloc[day] - deaths.iloc[day-1])
deathsperday['newdeaths'] = newdeaths
totaldeaths = deathsperday['newdeaths'].sum()
dailydeaths = deathsperday[['newdeaths']]

# Build visualization data

geodf = county_shapes.merge(statedf,left_on='FIPS_BEA', right_on='fips') 
stategeodf = geodf.groupby(['OBJECTID', 'FIPS_BEA', 'Shape_Leng', 'Shape_Area','geometry','county'], as_index=False)[['cases', 'deaths', 'state']].max()
geodf = gpd.GeoDataFrame(stategeodf, geometry='geometry', crs=geodf.crs)
m = geodf.explore(column='cases', cmap='YlOrRd', tooltip=['state','county', 'cases', 'deaths'], tooltip_kwds={'aliases': ['State Name', 'County Name', 'Total Cases','Total Deaths']},scheme='equalinterval', legend=True)

# Build county choropleth

fig, axes = plt.subplots(nrows=2, ncols=2, figsize=(12, 9))
    
axes[0,0].bar(dailycases.index, dailycases['newcases'])
axes[0,0].set_title('Bar Chart of Daily New Cases')
axes[0,0].set_ylabel('Count')
axes[0,0].set_xlabel('Date')
    
axes[0, 0].xaxis.set_major_locator(mdates.MonthLocator(interval=3)) 
axes[0, 0].xaxis.set_major_formatter(mdates.DateFormatter('%m-%-d-%Y'))
axes[0, 0].tick_params(axis='x', rotation=45)
axes[0, 0].yaxis.set_major_formatter(ticker.StrMethodFormatter('{x:,.0f}')) 
    
    #  Bar Chart of Daily New Cases ^^
axes[0,1].plot(casesperday.index, casesperday['cases'])
axes[0,1].set_title('Line Chart of Cumulative Cases Trend')
axes[0,1].set_ylabel('Total Cases')
axes[0,1].set_xlabel('Date')

axes[0, 1].xaxis.set_major_locator(mdates.MonthLocator(interval=3)) 
axes[0, 1].xaxis.set_major_formatter(mdates.DateFormatter('%m-%-d-%Y'))
axes[0, 1].tick_params(axis='x', rotation=45)
axes[0, 1].yaxis.set_major_formatter(ticker.StrMethodFormatter('{x:,.0f}'))
    
    #  Line Chart of Cumulative Cases Trend ^^    
    
axes[1,0].bar(dailydeaths.index, dailydeaths['newdeaths'])
axes[1,0].set_title('Bar Chart of Daily New Deaths')
axes[1,0].set_ylabel('Count')
axes[1,0].set_xlabel('Date')
    
axes[1, 0].xaxis.set_major_locator(mdates.MonthLocator(interval=3)) 
axes[1, 0].xaxis.set_major_formatter(mdates.DateFormatter('%m-%-d-%Y'))
axes[1, 0].tick_params(axis='x', rotation=45)
axes[1, 0].yaxis.set_major_formatter(ticker.StrMethodFormatter('{x:,.0f}'))
    
    #  Bar Chart of Daily New Deaths ^^ 
    
axes[1,1].plot(deathsperday.index, deathsperday['deaths'])
axes[1,1].set_title('Line Chart of Cumulative Deaths Trend')
axes[1,1].set_ylabel('Total Deaths')
axes[1,1].set_xlabel('Date')
    
axes[1, 1].xaxis.set_major_locator(mdates.MonthLocator(interval=3)) 
axes[1, 1].xaxis.set_major_formatter(mdates.DateFormatter('%m-%-d-%Y'))
axes[1, 1].tick_params(axis='x', rotation=45)
axes[1, 1].yaxis.set_major_formatter(ticker.StrMethodFormatter('{x:,.0f}'))
    
    #  Line Chart of Cumulative Deaths Trend ^^
    
fig.suptitle(f"{stateinput} COVID-19 Report for {nameinput}", fontsize=20)
plt.tight_layout()
plt.show()

print(f'Choropleth Map: Reported COVID-19 Cases and Deaths by County as of December 31, 2021 in {stateinput}')
display(m)

## All done
