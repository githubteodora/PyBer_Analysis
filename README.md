# Ride-Sharing Analysis 
## Overview
The goal of this analysis is to reveal how a ride-sharing business called Pyber performs in urban, suburban and rural cities. The rides data analyszed is dated Y2019 and is aggregared per city.

## Software
 - Python 3
 - Libraries: Pandas, Matplotlib, Numpy
 - Jupyter Notebook

## Results
1. Of all rides, 68.4% were done in urban cities, 26.3% in Suburban cities and only 5.3% in rural cities.
![Fig. 1](https://github.com/githubteodora/PyBer_Analysis/blob/main/analysis/Fig6.png)
2. 80% of all drivers work in urban cities.
![Fig. 2](https://github.com/githubteodora/PyBer_Analysis/blob/main/analysis/Fig7.png)
3. 37.3% of all fares were collected by drivers in rural and suburban cities.
![Fig. 3](https://github.com/githubteodora/PyBer_Analysis/blob/main/analysis/Fig5.png)
4. Summary scatterchart to illustrate average fare, total rides and count of drivers per city, colour-coded by city type:
![Fig. 4](https://github.com/githubteodora/PyBer_Analysis/blob/main/analysis/fig1.png)

## Code
 - [Link to full code](https://github.com/githubteodora/PyBer_Analysis/blob/main/PyBer_Challenge.ipynb)
 - The following code snippet was used to combine per city type statistics into a single dataframe and achieve the below table view:
![table](https://github.com/githubteodora/PyBer_Analysis/blob/main/analysis/Capture.PNG)
```
# Add Matplotlib inline magic command
%matplotlib inline
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import matplotlib.dates as mdates

# File to Load (Remember to change these)
city_data_to_load = "Resources/city_data.csv"
ride_data_to_load = "Resources/ride_data.csv"

# Read the City and Ride Data
city_data_df = pd.read_csv(city_data_to_load)
ride_data_df = pd.read_csv(ride_data_to_load)
Merge the DataFrames
In [41]:
# Combine the data into a single dataset
pyber_data_df = pd.merge(ride_data_df, city_data_df, how="left", on=["city", "city"])

# Display the data table for preview
pyber_data_df.head()
Out[41]:
city	date	fare	ride_id	driver_count	type
0	Lake Jonathanshire	2019-01-14 10:14:22	13.83	5739410935873	5	Urban
1	South Michelleport	2019-03-04 18:24:09	30.24	2343912425577	72	Urban
2	Port Samanthamouth	2019-02-24 04:29:00	33.44	2005065760003	57	Urban
3	Rodneyfort	2019-02-10 23:22:03	23.44	5149245426178	34	Urban
4	South Jack	2019-03-06 04:28:35	34.58	3908451377344	46	Urban
Deliverable 1: Get a Summary DataFrame
In [42]:
#  1. Get the total rides for each city type
total_rides_percitytype = pyber_data_df.groupby(["type"]).count()["ride_id"]
total_rides_percitytype
Out[42]:
type
Rural        125
Suburban     625
Urban       1625
Name: ride_id, dtype: int64
In [43]:
# 2. Get the total drivers for each city type
total_drivers_percitytype = pyber_data_df.groupby(["type"]).sum()["driver_count"]
total_drivers_percitytype
Out[43]:
type
Rural         537
Suburban     8570
Urban       59602
Name: driver_count, dtype: int64
In [44]:
#  3. Get the total amount of fares for each city type
total_fares_percitytype = pyber_data_df.groupby(["type"]).sum()["fare"]
total_fares_percitytype
Out[44]:
type
Rural        4327.93
Suburban    19356.33
Urban       39854.38
Name: fare, dtype: float64
In [45]:
#  4. Get the average fare per ride for each city type. 
avrg_fare_percitytype = pyber_data_df.groupby(["type"]).mean()["fare"]
avrg_fare_percitytype2 = total_fares_percitytype / total_rides_percitytype

avrg_fare_percitytype, avrg_fare_percitytype2
Out[45]:
(type
 Rural       34.623440
 Suburban    30.970128
 Urban       24.525772
 Name: fare, dtype: float64,
 type
 Rural       34.623440
 Suburban    30.970128
 Urban       24.525772
 dtype: float64)
In [46]:
# 5. Get the average fare per driver for each city type. 
avgr_fare_perdriver_bycitytype = total_fares_percitytype / total_drivers_percitytype
avgr_fare_perdriver_bycitytype
Out[46]:
type
Rural       8.059460
Suburban    2.258615
Urban       0.668675
dtype: float64
In [47]:
#  6. Create a PyBer summary DataFrame. 
summary_df1 = pd.merge(total_rides_percitytype, total_drivers_percitytype, how="left", on="type")
summary_df2 = pd.merge(summary_df1, total_fares_percitytype, how="left", on="type")
summary_df3 = pd.merge(summary_df2, avrg_fare_percitytype, how="left", on="type")
summary_final = pd.concat([summary_df3, avgr_fare_perdriver_bycitytype], axis=1)
summary_normalized = summary_final.rename(columns={"ride_id": "Total Rides", "driver_count": "Total Drivers", "fare_x": "Total Fares", "fare_y": "Average Fare per Ride", 0: "Average Fare per Driver"})
summary_normalized
Out[47]:
Total Rides	Total Drivers	Total Fares	Average Fare per Ride	Average Fare per Driver
type					
Rural	125	537	4327.93	34.623440	8.059460
Suburban	625	8570	19356.33	30.970128	2.258615
Urban	1625	59602	39854.38	24.525772	0.668675
In [48]:
#  7. Cleaning up the DataFrame. Delete the index name
summary_normalized.index.name = None
In [49]:
#  8. Format the columns.
summary_normalized["Total Fares"]=summary_normalized["Total Fares"].map('${:,.2f}'.format)
summary_normalized["Average Fare per Ride"]=summary_normalized["Average Fare per Ride"].map('${:,.2f}'.format)
summary_normalized["Average Fare per Driver"]=summary_normalized["Average Fare per Driver"].map('${:,.2f}'.format)
summary_normalized["Total Rides"]=summary_normalized["Total Rides"].map('{:,}'.format)
summary_normalized["Total Drivers"]=summary_normalized["Total Drivers"].map('{:,}'.format)
summary_normalized
Out[49]:
Total Rides	Total Drivers	Total Fares	Average Fare per Ride	Average Fare per Driver
Rural	125	537	$4,327.93	$34.62</td> <td>$8.06
Suburban	625	8,570	$19,356.33</td> <td>$30.97	$2.26</td> </tr> <tr> <th>Urban</th> <td>1,625</td> <td>59,602</td> <td>$39,854.38	$24.53</td> <td>$0.67
```

## Summary and Recommendations
1. Urban areas exibit the higest demand for ride-sharing services. Form a business perspective, it makes sense to continue operating and expanding the business in urban cities.
2. Suburban areas are a potential opportunity for futrher development - they exisbit high average fares, and the number of drivers seems low as compared with urban areas, which means there is a lot of room for growth. Demand needs to be carefully evaluated. The recommendation to the business is to explore in-demand routes and recruit more drivers in suburban areas. 
3. Rural cities have a very high average fare, but the demand for a ride-sharing service looks low. It might be worth investigating options such as service scheduling and using larger vehicles in rural areas, as it looks like customers are travelling to destionations farther away and probably carry more luggage. It is worth evaluating customer demand in those areas, as local services will have to be tailoured to the needs of the communities.
