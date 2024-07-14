
# Indian Election Result 2024 Analysis

This project aims to analyze the Indian Election Results for 2024. The analysis involves web scraping, database management, and data visualization using various tools and libraries.

## Skills Used
- **Web Scraping**: Beautiful Soup
- **Database**: SQL
- **Visualization**: Power BI, Python (matplotlib, seaborn, plotly, pandas)

## Table of Contents
- [Project Overview](#project-overview)
- [Web Scraping Procedure](#web-scraping-procedure)
  - [Step 1: Import Necessary Libraries](#step-1-import-necessary-libraries)
  - [Step 2: Fetch the Web Page](#step-2-fetch-the-web-page)
  - [Step 3: Parse the Web Page](#step-3-parse-the-web-page)
  - [Step 4: Create a DataFrame](#step-4-create-a-dataframe)
- [Database Management](#database-management)
  - [Step 1: Create Database and Table](#step-1-create-database-and-table)
  - [Step 2: Insert Data into Table](#step-2-insert-data-into-table)
- [Data Visualization](#data-visualization)
  - [Using Power BI](#using-power-bi)
  - [Using Python Libraries](#using-python-libraries)
- [Setup Instructions](#setup-instructions)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

## Project Overview
This project involves scraping election data from the 'jagranjosh' website, organizing the data using SQL, and visualizing the results using Power BI and Python libraries.

## Web Scraping Procedure

### Step 1: Import Necessary Libraries
First, import the required libraries to perform web scraping and data handling.
```python
import pandas as pd
from bs4 import BeautifulSoup
import requests
import numpy as np

```

### Step 2: Fetch the Web Page
Use the `requests` library to fetch the web page containing the election results.
```python
url = 'https://www.jagranjosh.com/general-knowledge/india-election-results-2024-check-party-wise-lok-sabha-elections-vote-counting-by-eci-1717472957-1'
response = requests.get(url)
response.text
```

### Step 3: Parse the Web Page
Parse the web page to extract state-wise data regarding seats won, total seats, and parties in the election. This involves finding the relevant HTML elements that contain the data and extracting their text content.
```python
soup = BeautifulSoup(response.text,'html.parser')
soup

```
### Step 4 : Extracting state-wise details
__1.__ Extracting the Party, Total Seat, Seat Won data with the help of BeautifulSoup and python's functions from the State-wise data

Let's see the procedure of 1 state to extract the above details with the help of beautifulsoup and python.

It will extract only 2 index table from all the tables present in the web-page and as you all know it will return a list so we converted that to text.
```python
and_data = soup.find_all('table')[2].text
print(and_data)

```
__2.__  Create a python function that separate_values takes a list of lists data0 as input, where each inner list represents data related to political parties. It then extracts and separates this data into four lists:

#### parties: `Contains the names of the political parties.`

#### won: `Contains the number of seats won by each party (converted to integers).`

#### leading: `Contains the number of seats currently leading by each party (converted to integers).`

#### total: `Contains the total number of seats contested by each party (converted to integers).`

Finally, it returns these four lists. The subsequent code snippet assigns these lists to variables parties, won, leading, and total, and prints them to display the extracted data in a readable format.

```python
def separate_values(data0):
    parties = []
    won = []
    leading = []
    total = []

    for row in data0:
        parties.append(row[0])
        won.append(int(row[1]))
        leading.append(int(row[2]))
        total.append(int(row[3]))

    return parties, won, leading, total

parties, won, leading, total = separate_values(data0)

print("Parties:", parties)
print("Won:", won)
print("Leading:", leading)
print("Total:", total)

```

__3.__ Then used that data that will extrated from the `separate_values()` function and create a DataFrame from it.

__4.__ Giving the data that was extracted from `1.` into that `separate_values()` function and put into a variable.
```python
andamar = separate_values(data0)
```

__5.__ Create a DataFrame for that particular State.
```python
andamar_data = pd.DataFrame(andamar).transpose()
andamar_data
```

__6.__ Giving an appropriate `column name` and `state name` to the DataFrame.
```python
andamar_data.rename(columns={0:'Parties',1:'Won',2:'Leading',3:'Total'},inplace=True)
```
```python
andamar_data['States'] = 'Andaman and Nicobar Islands'
```


__7.__ Repeat same procedure for the following rest of the states.

### Step 5: Create a DataFrame
Use Pandas to collect the data and create a DataFrame for each state, then combine all state data together.
```python
# Assuming you have 15 dataframes named df1, df2, ..., df15
dfs =  [andamar_data,andra_data,arunachal_data,asssam_data,bihar_data,chandigarh_data,chattisgarh_data,daman_data,goa_data,guj_data,haryana_data,himachal_data,jk_data,jharkhand_data,karnataka_data,kerla_data,ladakh_data,lakshdeep_data,mp_data,maharashtra_data,manipur_data,meghalaya_data,mizoram_data,nagaland_data,delhi_data,oddisa_data,puducherry_data,punjab_data,rajasthan_data,sikkim_data,tamil_nadu_data,telengana_data,tripura_data,up_data,ut_data,west_bengal_data]

# Concatenate all dataframes into a single dataframe
election_results = pd.concat(dfs, ignore_index=True)

# Display the resulting dataframe
print(election_results)
```

Creating a csv file from the above puthon code to analyze with further.

```python
election_results.to_csv('Election_Results_Overall_file',index=False)
```

## Database Management

### Step 1: Create Database and Table
Create an SQL database and a table to store the scraped data. Here is an example of how to create a table in SQL:
```sql
CREATE TABLE election_results (
    State VARCHAR(50),
    Party VARCHAR(100),
    Won INT,
    Total_seats INT
);
```

### Step 2: Insert Data into Table
Insert the data from the CSV file into the SQL table. This can be done using the `LOAD DATA INFILE` command in SQL.
```sql
LOAD DATA INFILE 'Election_Results_Overall_file.csv'
INTO TABLE election_results
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '
'
IGNORE 1 ROWS;
```
Performing some SQL queries to cross-check the results.

### To see total `states` that we have in our data.
 ```sql
 SELECT DISTINCT(States) FROM election_result;
 ```

![toatl_number_of_states](https://github.com/user-attachments/assets/154b4696-8bb0-48cf-a749-9f2fea558e84)

### `State-wise` `party name` and `seats_won` in `Descending` order.
```sql
SELECT States, Party, Won
FROM (
    SELECT States, Party, Won,
           ROW_NUMBER() OVER (PARTITION BY States ORDER BY Won DESC) as rn
    FROM election_result
) AS ranked
WHERE rn = 1
```

![state-wise_party_won](https://github.com/user-attachments/assets/549fb2e6-d785-4bcf-b382-f8e9954459ce)


### Overall votes `Party` gets
```sql
SELECT Party , SUM(Won) AS Total_Seats_Won
FROM election_result
GROUP BY Party
ORDER BY Total_Seats_Won DESC;
```
![Screenshot 2024-07-14 185927](https://github.com/user-attachments/assets/8a620711-7d92-4794-bf31-54163cf50f43)


### Count of `Party` that will appear in who many `States`
```sql
SELECT Party,COUNT(Party) AS state
FROM election_result
GROUP BY Party
ORDER BY state DESC
```
![Screenshot 2024-07-14 190157](https://github.com/user-attachments/assets/8eef58d8-3a16-4403-8366-b54b0e816d0f)

## Data Visualization

### Using Power BI
1. **Load the CSV Data**: Open Power BI Desktop and load the CSV file containing the election results.
2. **Create Visualizations**: Use various Power BI features to create charts and dashboards to visualize the election results. For example, you can create bar charts to compare the number of seats won by different parties in each state, pie charts to show the proportion of seats won by each party, and maps to visualize the geographic distribution of election results.

![Screenshot 2024-07-14 231037](https://github.com/user-attachments/assets/394350c3-b614-4089-b224-342b75c4be2e)

### Using Python Libraries
Use matplotlib, seaborn, and plotly to create visualizations in Python.

#### Distribution of votes with each `Party` (Total number of votes)
```python
fig = px.histogram(x=data['won'], y=data['party'])
fig.update_layout(xaxis_title='Seats Won', yaxis_title='Party',title={'text': 'Total seats won by each party', 'x': 0.5, 'xanchor': 'center', 'y': 0.95, 'yanchor': 'top'})
# Show the chart
fig.show()
```
![count_of_party_visually](https://github.com/user-attachments/assets/16c589ec-e51e-447e-a61e-15c5d13a968d)

#### Which `Party` won most of the `Seats` in particular `States`
```python
data.groupby(['state','party'], as_index=False).max()[['state','won','party','total_seats']].sort_values(by='won', ascending=False).head(15)
```
![most_seats_won_per_states_table](https://github.com/user-attachments/assets/03554f71-3ff7-4783-a6f7-cf5cca1eb00d)

#### Visually
```python
party_won = data.groupby(['state','party'], as_index=False).max()[['state','won','party','total_seats']].sort_values(by='won', ascending=False).head(15)
fig = px.histogram(x='party',y='won',color='state',data_frame=party_won)
fig.show()
```
![seats_won_visually](https://github.com/user-attachments/assets/df3a0b62-b1bc-4da7-92c4-33fda320828e)

#### Count of `Party` who fight the election `State-Wise`
```python
data[['state', 'party']].groupby('party', as_index=False).count()
```
![count_of_party_state_wise_table](https://github.com/user-attachments/assets/983dead9-0275-4d08-9b65-17eb1cef0601)

#### Visually
```python
party_count = data[['state', 'party']].groupby('party', as_index=False).count()
fig = px.bar(x='state',y='party',data_frame=party_count)
fig.show()
```
![count_of_party_visually](https://github.com/user-attachments/assets/6466ca86-f51c-4309-abc0-fb3c989ca90d)


## Contributing
Contributions are welcome! Please open an issue or submit a pull request for any changes or improvements.
