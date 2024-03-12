# Analyzing-Data-Through-Visualization

# Scenario

In this assignment you will be tasked with creating plots which answer questions for analysing "historical_automobile_sales" data to understand the historical trends in automobile sales during recession periods.<br>
recession period 1 - year 1980 <br>
recession period 2 - year 1981 to 1982<br>
recession period 3 - year 1991<br>
recession period 4 - year 2000 to 2001<br>
recession period 5 - year end 2007 to mid 2009<br>
recession period 6 - year 2020 -Feb to April (Covid-19 Impact)<br>

# Data Description

The dataset used for this visualization assignment contains *historical_automobile_sales* data representing automobile sales and related variables during recession and non-recession period. 

The dataset includes the following variables:
<br>1. Date: The date of the observation.
<br>2. Recession: A binary variable indicating recession perion; 1 means it was recession, 0 means it was normal.
<br>3. Automobile_Sales: The number of vehicles sold during the period.
<br>4. GDP: The per capita GDP value in USD.
<br>5. Unemployment_Rate: The monthly unemployment rate.
<br>6. Consumer_Confidence: A synthetic index representing consumer confidence, which can impact consumer spending and automobile purchases.
<br>7. Seasonality_Weight: The weight representing the seasonality effect on automobile sales during the period.
<br>8. Price: The average vehicle price during the period.
<br>9. Advertising_Expenditure: The advertising expenditure of the company.
<br>10.Vehicle_Type: The type of vehicles sold; Supperminicar, Smallfamiliycar,                 Mediumfamilycar, Executivecar, Sports.
<br>11.Competition: The measure of competition in the market, such as the number of competitors or market share of major manufacturers.
<br>12.Month: Month of the observation extracted from Date..
<br>13.Year: Year of the observation extracted from Date.
<br>
By examining various factors mentioned above from the dataset, you aim to gain insights into how recessions impacted automobile sales for your company.

### Importing Lib
```python
import numpy as np
import pandas as pd
%matplotlib inline
import matplotlib as mpl
import matplotlib.pyplot as plt
import seaborn as sns
import folium
```
### Importing Data
```python
URL = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DV0101EN-SkillsNetwork/Data%20Files/historical_automobile_sales.csv"
df = pd.read_csv(URL)
print('Data downloaded and read into a dataframe!')
```
### *Line chart* using the functionality of pandas to show how automobile sales fluctuate from year to year
```python
df_line = df.groupby(df['Year'])['Automobile_Sales'].mean()
plt.figure(figsize=(10, 6))
df_line.plot(kind = 'line', color = 'skyblue', marker = 'D', mec = 'black')
plt.xlabel('Year')
plt.ylabel('Sales Volume')
plt.title('Automobile Sales Over Time')
plt.show()
```
![image](https://github.com/ravsain/Analyzing-Data-Through-Visualization/assets/155238122/2f71a524-9e68-4ac5-b2c0-5e89245d058c)

#### More detail maodel
```python
df_line = df.groupby(df['Year'])['Automobile_Sales'].mean()
plt.figure(figsize=(10, 6))
df_line.plot(kind = 'line', color = 'skyblue', marker = 'D', mec = 'black', grid = True)
plt.xticks(list(range(1980, 2024)), rotation = 75)
plt.xlabel('Year')
plt.ylabel('Sales Volume')
plt.title('Automobile Sales Over Time')
plt.text(1982, 650, '1981-82 Recession', rotation = 45)
plt.text(2020, 2400, ' Covid Recession', rotation = 45)
plt.legend()
plt.savefig("Line_Plot_1.png")
plt.show()
```
### Different lines for categories of vehicle type and analyse the trend to to find out is there a noticeable difference in sales trends between different vehicle types during recession periods.
```pyhton
df_Mline = df[df['Recession'] == 1].groupby(['Year','Vehicle_Type'], as_index=False)['Automobile_Sales'].sum()
df_Mline.set_index('Year', inplace=True)
df_Mline = df_Mline.groupby(['Vehicle_Type'])['Automobile_Sales']
plt.figure(figsize=(10, 8))
df_Mline.plot(kind='line', ax = plt.gca(), grid= True)
plt.ylabel('SalesVolume')
plt.title('Sales Trend Vehicle-wise during Recession')
plt.legend(title = 'Vechile Type' )
plt.tight_layout()
plt.savefig("Line_Plot_2.png")
plt.show()
```
![image](https://github.com/ravsain/Analyzing-Data-Through-Visualization/assets/155238122/49e5021f-a7ae-4d4b-8da7-96848ffd1ed9)

we can understand that during recession period, the sales for 'Sports type vehicles' declined because of the high cost of the vehicle.
while sales of the superminicar and smallfamilycar increased.

### Comparing the sales trend per vehicle type for a recession period with a non-recession period.
```Python
new_df = df.groupby('Recession')['Automobile_Sales'].mean().reset_index()
plt.figure(figsize=(10, 8))
sns.barplot(x = 'Recession', y = 'Automobile_Sales', hue = 'Recession', data = new_df)
plt.xlabel('period')
plt.ylabel('Average Sales volume')
plt.title('Average Automobile Sales during Recession and Non-Recession')
plt.xticks(ticks=[0, 1], labels=['Non-Recession', 'Recession'])
plt.show()
```
![image](https://github.com/ravsain/Analyzing-Data-Through-Visualization/assets/155238122/280582f7-7a6e-4b18-a60b-4a9a5b28c450)

```python
new_df = df.groupby(['Recession', 'Vehicle_Type'])['Automobile_Sales'].mean().reset_index()
plt.figure(figsize=(10, 8))
ax = sns.barplot(x = 'Recession', y = 'Automobile_Sales', hue = 'Vehicle_Type', data = new_df)
for i in ax.containers:
    ax.bar_label(i)

plt.xlabel('period')
plt.ylabel('Average Sales volume')
plt.title('Average Automobile Sales during Recession and Non-Recession')
plt.xticks(ticks=[0, 1], labels=['Non-Recession', 'Recession'])
plt.savefig('Bar_Chart.png')
plt.show()
```
![image](https://github.com/ravsain/Analyzing-Data-Through-Visualization/assets/155238122/aba5eed3-52ff-40bc-a814-bf9a20be93f4)

Through this we can understand that there is a drastic decline in the overall sales of the automobiles during recession.
However, the most affected type of vehicle is executivecar and sports.

### Sub Plotting to compare the variations in GDP during recession and non-recession period by developing line plots for each period.
```python
# Make use of <u>add_subplot() from Matplotlib for this comparision.
rec_data = df[df['Recession'] == 1]
non_rec_data = df[df['Recession'] == 0]

fig = plt.figure(figsize=(12, 6))

ax0 = fig.add_subplot(1, 2, 1)
ax1 = fig.add_subplot(1, 2, 2)

sns.lineplot(x = 'Year', y = 'GDP', marker = '*',
             markerfacecolor = 'green', mec = 'green', data = rec_data, label = 'Recession', ax = ax0 )
ax0.set_xlabel('year')
ax0.set_ylabel('GDP')
ax0.set_title('GDP Variation During Recession Period')

sns.lineplot(x = 'Year', y = 'GDP', marker = '*', 
             markerfacecolor = 'red', mec = 'red', data = non_rec_data, label = 'Non Recession', ax = ax1)
ax1.set_xlabel('year')
ax1.set_ylabel('GDP')
ax1.set_title('GDP Variation During Non Recession Period')

plt.tight_layout()
plt.savefig("Subplot.png")
plt.show()
```
![image](https://github.com/ravsain/Analyzing-Data-Through-Visualization/assets/155238122/dc4b23a3-0c0e-4b2d-9425-6624638a41db)

From this plot, it is evident that during recession, the GDP of the country was in a low range, might have afected the overall sales of the company.

### Bubble plot for displaying the impact of seasonality on Automobile Sales.
<br>How has seasonality impacted the sales, in which months the sales were high or low? Need to Check it for non-recession years to understand the trend.

```python
size = non_rec_data['Seasonality_Weight']

sns.scatterplot(data = non_rec_data, x = 'Month', y = 'Automobile_Sales', size =size)
plt.xlabel('Month')
plt.ylabel('Automobile_Sales')
plt.title('Seasonality impact on Automobile Sales')
plt.savefig('Bubble.png')
plt.show()
```
![image](https://github.com/ravsain/Analyzing-Data-Through-Visualization/assets/155238122/f19797a1-f8c0-456a-9e2f-e288a29e77f4)

it is evident that seasonality has not affected on the overall sales. However, there is a drastic raise in sales in the month of April

### Pie chart to display the portion of advertising expenditure of XYZAutomotives during recession and non-recession periods to analyze the changes.
```python
RAtotal = rec_data['Advertising_Expenditure'].sum()
NRAtotal = non_rec_data['Advertising_Expenditure'].sum()

plt.figure(figsize=(10, 6))

labels = ('Recession', 'Non-Recession')
sizes = [RAtotal, NRAtotal]
plt.pie(sizes, labels=labels, autopct = '%1.1f%%', startangle = 90)
plt.title('Advertising Expenditure during Recession and Non-Recession Periods')
plt.savefig('Pie_1.png')
plt.show()
```
![image](https://github.com/ravsain/Analyzing-Data-Through-Visualization/assets/155238122/7c9b100d-c4f4-4c3a-b96e-cc18fc384a9f)

It seems ABCAutomotives has been spending much more on the advertisements during non-recession periods as compared to during recession times. Fair enough!

## Pie chart to display the total Advertisement expenditure for each vehicle type during recession period to observe the share of each vehicle type in total expenditure during recessions.

```pyhton
sales_vol_by_vech_type = rec_data.groupby('Vehicle_Type')['Advertising_Expenditure'].sum()
plt.figure(figsize=(10, 6))
sizes = sales_vol_by_vech_type.values
plt.pie(sizes, labels = sales_vol_by_vech_type.index,
        autopct = '%1.1f%%', startangle = 90)
plt.title('Share of Each Vehicle Type in Total Expenditure during Recessions')
plt.savefig('Pie_2.png')
plt.show()
```
![image](https://github.com/ravsain/Analyzing-Data-Through-Visualization/assets/155238122/68d1d29a-b3da-4df7-ac50-5dd5c108c0e6)


### Create a map on the hightest sales region/offices of the company during recession period
```python
from pyodide.http import pyfetch

async def download(url, filename):
    response = await pyfetch(url)
    if response.status == 200:
        with open(filename, "wb") as f:
            f.write(await response.bytes())

path = 'https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DV0101EN-SkillsNetwork/Data%20Files/us-states.json'
await download(path, "us-states.json")

filename = "us-states.json"
```
We found that the datset also contains the location/city for company offices. Now you want to show the recession impact on various offices/city sales by developing a choropleth
```python
    # Filter the data for the recession period and specific cities
    recession_data = df[df['Recession'] == 1]

    # Calculate the total sales by city
    sales_by_city = recession_data.groupby('City')['Automobile_Sales'].sum().reset_index()

    # Create a base map centered on the United States
    map1 = folium.Map(location=[37.0902, -95.7129], zoom_start=4)

    # Create a choropleth layer using Folium
    choropleth = folium.Choropleth(
        geo_data= 'us-states.json',  # GeoJSON file with state boundaries
        data=sales_by_city,
        columns=['City', 'Automobile_Sales'],
        key_on='feature.properties.name',
        fill_color='YlOrRd',
        fill_opacity=0.7,
        line_opacity=0.2,
        legend_name='Automobile Sales during Recession'
    ).add_to(map1)


    # Add tooltips to the choropleth layer
    choropleth.geojson.add_child(
        folium.features.GeoJsonTooltip(['name'], labels=True)
    )

    # Display the map
    map1
```
