
Week 3 - Toronto Clusterning


```python
import pandas as pd
import numpy as np
import requests
#importing BeautifulSoup lib. (Allready istalled in JupiterLab)
from bs4 import BeautifulSoup 
```

### Using requests to get the content of web-page and parse it using BeautifulSoup


```python
#Using requests to get the content of web-page and parse it using BeautifulSoup
link = 'https://en.wikipedia.org/wiki/List_of_postal_codes_of_Canada:_M'
page = requests.get(link)
soup = BeautifulSoup(page.content,'html.parser')

postal_df = pd.DataFrame()
i=0
end = False
#Looping <tr> tag to get rows of the table
for tr in soup.find_all('tr'):
    j=0
    #Looping <td> tag to get columns within eath row
    for tds in tr.find_all('td'):
        #Using empty value as the end criteria. There is another table at this page, with we do not whant to be appended.
        if (len(tds.text) > 1)&(not end): 
            postal_df.loc[i,j]=tds.text
            j=j+1
        else:
            end=True
    i=i+1

postal_df.columns=['PostCode','Borough','Neighborhood']
```


```python
postal_df.shape
```




    (288, 3)



### Cleaning the data


```python
# Deleting new line tag "/n" in last column
postal_df['Neighborhood']=postal_df['Neighborhood'].str.replace('\n','')
postal_df=postal_df.reset_index(drop=True)
postal_df.head(15)

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PostCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M2A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Heights</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M7A</td>
      <td>Queen's Park</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M8A</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Rouge</td>
    </tr>
    <tr>
      <th>12</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern</td>
    </tr>
    <tr>
      <th>13</th>
      <td>M2B</td>
      <td>Not assigned</td>
      <td>Not assigned</td>
    </tr>
    <tr>
      <th>14</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills North</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Deleting rows with no Borough info. Assigning Neighborhood to Borough where is "Not assigned"
for i in range(postal_df.shape[0]):
    if postal_df.loc[i,'Borough']=='Not assigned':
        postal_df.drop(i,inplace=True)
    elif postal_df.loc[i,'Neighborhood']=='Not assigned':
        postal_df.loc[i,'Neighborhood']=postal_df.loc[i,'Borough']
postal_df=postal_df.reset_index(drop=True)
```


```python
postal_df.head(15)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PostCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Heights</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M7A</td>
      <td>Queen's Park</td>
      <td>Queen's Park</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Rouge</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills North</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Woodbine Gardens</td>
    </tr>
    <tr>
      <th>12</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill</td>
    </tr>
    <tr>
      <th>13</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Ryerson</td>
    </tr>
    <tr>
      <th>14</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District</td>
    </tr>
  </tbody>
</table>
</div>




```python
postal_df.shape
```




    (211, 3)



### Getting the geo coordinates for each Postal Code


```python
!pip install geocoder
import geocoder
print("Geocoder is ready!")
```

    Requirement already satisfied: geocoder in /home/jupyterlab/conda/lib/python3.6/site-packages (1.38.1)
    Requirement already satisfied: click in /home/jupyterlab/conda/lib/python3.6/site-packages (from geocoder) (7.0)
    Requirement already satisfied: requests in /home/jupyterlab/conda/lib/python3.6/site-packages (from geocoder) (2.21.0)
    Requirement already satisfied: ratelim in /home/jupyterlab/conda/lib/python3.6/site-packages (from geocoder) (0.1.6)
    Requirement already satisfied: six in /home/jupyterlab/conda/lib/python3.6/site-packages (from geocoder) (1.12.0)
    Requirement already satisfied: future in /home/jupyterlab/conda/lib/python3.6/site-packages (from geocoder) (0.17.1)
    Requirement already satisfied: certifi>=2017.4.17 in /home/jupyterlab/conda/lib/python3.6/site-packages (from requests->geocoder) (2019.3.9)
    Requirement already satisfied: chardet<3.1.0,>=3.0.2 in /home/jupyterlab/conda/lib/python3.6/site-packages (from requests->geocoder) (3.0.4)
    Requirement already satisfied: urllib3<1.25,>=1.21.1 in /home/jupyterlab/conda/lib/python3.6/site-packages (from requests->geocoder) (1.24.1)
    Requirement already satisfied: idna<2.9,>=2.5 in /home/jupyterlab/conda/lib/python3.6/site-packages (from requests->geocoder) (2.8)
    Requirement already satisfied: decorator in /home/jupyterlab/conda/lib/python3.6/site-packages (from ratelim->geocoder) (4.4.0)
    Geocoder is ready!



```python
#At this moment geocoder do not work (hang the kernel)

"""
for i in range(postal_df.shape[0]):
    lat_lng_coords = None
    while(lat_lng_coords is None):
        g = geocoder.google('{}, Toronto, Ontario'.format(postal_df.loc[i,'PostCode']))
        lat_lng_coords = g.latlng
    postal_df.loc[i,'Latitude'] = lat_lng_coords[0]
    postal_df.loc[i,'Longitude'] = lat_lng_coords[1]
"""

#Using csv file with coordinates
geo_df=pd.read_csv('http://cocl.us/Geospatial_data')
```


```python
geo_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postal Code</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1B</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M1C</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M1E</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1G</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M1H</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Using 'left join' to merge dataframe with Postal Codes and Geo Coordinates
postal_geo_df = pd.merge(postal_df, geo_df, left_on='PostCode',right_on='Postal Code', how='left').drop('Postal Code', axis=1)
```


```python
postal_geo_df.head(20)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PostCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
      <td>43.753259</td>
      <td>-79.329656</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
      <td>43.725882</td>
      <td>-79.315572</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront</td>
      <td>43.654260</td>
      <td>-79.360636</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park</td>
      <td>43.654260</td>
      <td>-79.360636</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Heights</td>
      <td>43.718518</td>
      <td>-79.464763</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor</td>
      <td>43.718518</td>
      <td>-79.464763</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M7A</td>
      <td>Queen's Park</td>
      <td>Queen's Park</td>
      <td>43.662301</td>
      <td>-79.389494</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue</td>
      <td>43.667856</td>
      <td>-79.532242</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Rouge</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills North</td>
      <td>43.745906</td>
      <td>-79.352188</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Woodbine Gardens</td>
      <td>43.706397</td>
      <td>-79.309937</td>
    </tr>
    <tr>
      <th>12</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill</td>
      <td>43.706397</td>
      <td>-79.309937</td>
    </tr>
    <tr>
      <th>13</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Ryerson</td>
      <td>43.657162</td>
      <td>-79.378937</td>
    </tr>
    <tr>
      <th>14</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District</td>
      <td>43.657162</td>
      <td>-79.378937</td>
    </tr>
    <tr>
      <th>15</th>
      <td>M6B</td>
      <td>North York</td>
      <td>Glencairn</td>
      <td>43.709577</td>
      <td>-79.445073</td>
    </tr>
    <tr>
      <th>16</th>
      <td>M9B</td>
      <td>Etobicoke</td>
      <td>Cloverdale</td>
      <td>43.650943</td>
      <td>-79.554724</td>
    </tr>
    <tr>
      <th>17</th>
      <td>M9B</td>
      <td>Etobicoke</td>
      <td>Islington</td>
      <td>43.650943</td>
      <td>-79.554724</td>
    </tr>
    <tr>
      <th>18</th>
      <td>M9B</td>
      <td>Etobicoke</td>
      <td>Martin Grove</td>
      <td>43.650943</td>
      <td>-79.554724</td>
    </tr>
    <tr>
      <th>19</th>
      <td>M9B</td>
      <td>Etobicoke</td>
      <td>Princess Gardens</td>
      <td>43.650943</td>
      <td>-79.554724</td>
    </tr>
  </tbody>
</table>
</div>




```python
postal_geo_df.shape
```




    (211, 5)




```python
# Matplotlib and associated plotting modules
import matplotlib.cm as cm
import matplotlib.colors as colors

# import k-means from clustering stage
from sklearn.cluster import KMeans

#!conda install -c conda-forge folium=0.5.0 --yes # uncomment this line if you haven't completed the Foursquare API lab
import folium # map rendering library

from geopy.geocoders import Nominatim # convert an address into latitude and longitude values

print('Libraries imported.')
```

    Libraries imported.


## It's interesting to draw all the points on the map to understand their spread.


```python

```


```python
address = 'Toronto, Ontario, Canada'

geolocator = Nominatim(user_agent="ny_explorer")
location = geolocator.geocode(address)
latitude = location.latitude
longitude = location.longitude
print('The geograpical coordinate of Toronto are {}, {}.'.format(latitude, longitude))
```

    The geograpical coordinate of Toronto are 43.653963, -79.387207.



```python
# create map of Toronto
map_toronto = folium.Map(location=[latitude, longitude], zoom_start=11)

# add PostCode's coordinates to the map
for lat, lng, borough, neighborhood in zip(postal_geo_df['Latitude'], postal_geo_df['Longitude'], postal_geo_df['Borough'], postal_geo_df['Neighborhood']):
    label = '{}, {}'.format(neighborhood, borough)
    label = folium.Popup(label, parse_html=True)
    folium.CircleMarker(
        [lat, lng],
        radius=5,
        popup=label,
        color='blue',
        fill=True,
        fill_color='#3186cc',
        fill_opacity=0.7,
        parse_html=False).add_to(map_toronto)  
    
map_toronto
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;base64,PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDggewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4IiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCcsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDMuNjUzOTYzLC03OS4zODcyMDddLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgem9vbTogMTEsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBtYXhCb3VuZHM6IGJvdW5kcywKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGxheWVyczogW10sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB3b3JsZENvcHlKdW1wOiBmYWxzZSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGNyczogTC5DUlMuRVBTRzM4NTcKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciB0aWxlX2xheWVyXzAwNGEzMmVmOTU2ZTQyN2I5NjkzMThjNTc0YjIwYTRkID0gTC50aWxlTGF5ZXIoCiAgICAgICAgICAgICAgICAnaHR0cHM6Ly97c30udGlsZS5vcGVuc3RyZWV0bWFwLm9yZy97en0ve3h9L3t5fS5wbmcnLAogICAgICAgICAgICAgICAgewogICJhdHRyaWJ1dGlvbiI6IG51bGwsCiAgImRldGVjdFJldGluYSI6IGZhbHNlLAogICJtYXhab29tIjogMTgsCiAgIm1pblpvb20iOiAxLAogICJub1dyYXAiOiBmYWxzZSwKICAic3ViZG9tYWlucyI6ICJhYmMiCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hZWI1ODcyMjBhMzA0YzIyOGE2ZDEzMzFlOTBlMzMyOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc1MzI1ODYsLTc5LjMyOTY1NjVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzMzYTY2OGZkNWE3NDQyY2E4ZmZiYThhMDlhNjgyZjggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjRiMDcwNDI5NzZkNDQ4YWIzMjBmOGZkODYwMTQ3YzggPSAkKCc8ZGl2IGlkPSJodG1sXzY0YjA3MDQyOTc2ZDQ0OGFiMzIwZjhmZDg2MDE0N2M4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QYXJrd29vZHMsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2MzM2E2NjhmZDVhNzQ0MmNhOGZmYmE4YTA5YTY4MmY4LnNldENvbnRlbnQoaHRtbF82NGIwNzA0Mjk3NmQ0NDhhYjMyMGY4ZmQ4NjAxNDdjOCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9hZWI1ODcyMjBhMzA0YzIyOGE2ZDEzMzFlOTBlMzMyOC5iaW5kUG9wdXAocG9wdXBfYzMzYTY2OGZkNWE3NDQyY2E4ZmZiYThhMDlhNjgyZjgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzFkYjZjMDAzNzU2NDk5YThjOWE5M2ZmMzZkZDNmMmMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MjU4ODIyOTk5OTk5OTUsLTc5LjMxNTU3MTU5OTk5OTk4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzk1MDNkMDU1NWM0MDRiZGU5NTViODNjYWJiODIwMDQxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2QxMGM0NzIzN2VlNjRjZGI4OGI0ZWY0ZjJlYjEzYzA4ID0gJCgnPGRpdiBpZD0iaHRtbF9kMTBjNDcyMzdlZTY0Y2RiODhiNGVmNGYyZWIxM2MwOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VmljdG9yaWEgVmlsbGFnZSwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTUwM2QwNTU1YzQwNGJkZTk1NWI4M2NhYmI4MjAwNDEuc2V0Q29udGVudChodG1sX2QxMGM0NzIzN2VlNjRjZGI4OGI0ZWY0ZjJlYjEzYzA4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2MxZGI2YzAwMzc1NjQ5OWE4YzlhOTNmZjM2ZGQzZjJjLmJpbmRQb3B1cChwb3B1cF85NTAzZDA1NTVjNDA0YmRlOTU1YjgzY2FiYjgyMDA0MSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81Yzc4ZGIyZTg2NjE0OWIzYTJkNzU0MGE4M2Q0MGRmYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1NDI1OTksLTc5LjM2MDYzNTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmQzOWFhZTVjNjcwNGFkMzkzMTQxOGMzNDI2NDc3NDEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzlkYjgzMzgwZmZiNDBlNDg1ZWUzODE3ZTE0Yzc4ZWUgPSAkKCc8ZGl2IGlkPSJodG1sXzM5ZGI4MzM4MGZmYjQwZTQ4NWVlMzgxN2UxNGM3OGVlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IYXJib3VyZnJvbnQsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzZkMzlhYWU1YzY3MDRhZDM5MzE0MThjMzQyNjQ3NzQxLnNldENvbnRlbnQoaHRtbF8zOWRiODMzODBmZmI0MGU0ODVlZTM4MTdlMTRjNzhlZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81Yzc4ZGIyZTg2NjE0OWIzYTJkNzU0MGE4M2Q0MGRmYy5iaW5kUG9wdXAocG9wdXBfNmQzOWFhZTVjNjcwNGFkMzkzMTQxOGMzNDI2NDc3NDEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjFiNTViMGJkNWViNGY1YmIxOTQ2ZjY5NDMyYTNhZTAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTQyNTk5LC03OS4zNjA2MzU5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2E1MTcyOTQyZTE3NDQ3MWY5Y2E5YWExY2IxNmFlYzBkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Y3YWYxYTkwMmZlNzQ3Nzc4MWI3N2Y2ODNkNGZmNzJiID0gJCgnPGRpdiBpZD0iaHRtbF9mN2FmMWE5MDJmZTc0Nzc3ODFiNzdmNjgzZDRmZjcyYiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UmVnZW50IFBhcmssIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2E1MTcyOTQyZTE3NDQ3MWY5Y2E5YWExY2IxNmFlYzBkLnNldENvbnRlbnQoaHRtbF9mN2FmMWE5MDJmZTc0Nzc3ODFiNzdmNjgzZDRmZjcyYik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mMWI1NWIwYmQ1ZWI0ZjViYjE5NDZmNjk0MzJhM2FlMC5iaW5kUG9wdXAocG9wdXBfYTUxNzI5NDJlMTc0NDcxZjljYTlhYTFjYjE2YWVjMGQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjUwNDExYWUxYjQ5NDg4MDg4Nzk2OGRlYWQ1NjZlOWMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTg1MTc5OTk5OTk5OTYsLTc5LjQ2NDc2MzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzRjYzY3ZDllMzE0NTQzZmJiZTYwMGViMmRkZjAxOGJkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzQ5MTYyMDI2NzIyMDQ0ZjY4YWQyOGEyZGViZjI5YjQ5ID0gJCgnPGRpdiBpZD0iaHRtbF80OTE2MjAyNjcyMjA0NGY2OGFkMjhhMmRlYmYyOWI0OSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF3cmVuY2UgSGVpZ2h0cywgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNGNjNjdkOWUzMTQ1NDNmYmJlNjAwZWIyZGRmMDE4YmQuc2V0Q29udGVudChodG1sXzQ5MTYyMDI2NzIyMDQ0ZjY4YWQyOGEyZGViZjI5YjQ5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzY1MDQxMWFlMWI0OTQ4ODA4ODc5NjhkZWFkNTY2ZTljLmJpbmRQb3B1cChwb3B1cF80Y2M2N2Q5ZTMxNDU0M2ZiYmU2MDBlYjJkZGYwMThiZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83YzJkNWE1MjE1NDk0MmNlOGE4YzdjNjk1MjJjYzk4OCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxODUxNzk5OTk5OTk5NiwtNzkuNDY0NzYzMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZjQ0Y2NlODc4MGE3NGQ5M2JhNmM2ZDdiNDdlNWY1NjAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDMwYTNmYWFkODgxNDY4MjgxNjg4MGNiNWU1ZjYzZjkgPSAkKCc8ZGl2IGlkPSJodG1sXzQzMGEzZmFhZDg4MTQ2ODI4MTY4ODBjYjVlNWY2M2Y5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MYXdyZW5jZSBNYW5vciwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZjQ0Y2NlODc4MGE3NGQ5M2JhNmM2ZDdiNDdlNWY1NjAuc2V0Q29udGVudChodG1sXzQzMGEzZmFhZDg4MTQ2ODI4MTY4ODBjYjVlNWY2M2Y5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzdjMmQ1YTUyMTU0OTQyY2U4YThjN2M2OTUyMmNjOTg4LmJpbmRQb3B1cChwb3B1cF9mNDRjY2U4NzgwYTc0ZDkzYmE2YzZkN2I0N2U1ZjU2MCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83YTkwZTk2Y2IwZGQ0YjE2OWFlMmQyYzVjNWNmYjk5NiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MjMwMTUsLTc5LjM4OTQ5MzhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYTQ4YTllN2IwMGM3NGQ2NGIyZjI4MGUyNTNjYTc5NjIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNGQ3MzRjNWE4ZWFiNDFmYjk0NzRjNWI3YmFkZmZlNmMgPSAkKCc8ZGl2IGlkPSJodG1sXzRkNzM0YzVhOGVhYjQxZmI5NDc0YzViN2JhZGZmZTZjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5RdWVlbiYjMzk7cyBQYXJrLCBRdWVlbiYjMzk7cyBQYXJrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hNDhhOWU3YjAwYzc0ZDY0YjJmMjgwZTI1M2NhNzk2Mi5zZXRDb250ZW50KGh0bWxfNGQ3MzRjNWE4ZWFiNDFmYjk0NzRjNWI3YmFkZmZlNmMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfN2E5MGU5NmNiMGRkNGIxNjlhZTJkMmM1YzVjZmI5OTYuYmluZFBvcHVwKHBvcHVwX2E0OGE5ZTdiMDBjNzRkNjRiMmYyODBlMjUzY2E3OTYyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2QzZWY4NmZiNmRjOTQ2ZDdhOTU1NTA0N2I3ZDUzZDM2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY3ODU1NiwtNzkuNTMyMjQyNDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjMwODFmN2Q4ODlmNDY5ZWI4OTA0ZGFjNmI1YWI1MDIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNTc1MGVkNTRhMDg1NDBkNTkyMmUzOTdiZjBkM2NlZWUgPSAkKCc8ZGl2IGlkPSJodG1sXzU3NTBlZDU0YTA4NTQwZDU5MjJlMzk3YmYwZDNjZWVlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Jc2xpbmd0b24gQXZlbnVlLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2IzMDgxZjdkODg5ZjQ2OWViODkwNGRhYzZiNWFiNTAyLnNldENvbnRlbnQoaHRtbF81NzUwZWQ1NGEwODU0MGQ1OTIyZTM5N2JmMGQzY2VlZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kM2VmODZmYjZkYzk0NmQ3YTk1NTUwNDdiN2Q1M2QzNi5iaW5kUG9wdXAocG9wdXBfYjMwODFmN2Q4ODlmNDY5ZWI4OTA0ZGFjNmI1YWI1MDIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzJiOTFhNWQ5NDdjNGUwYmFhNGFmMmFjOTMxYjU0YzkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My44MDY2ODYyOTk5OTk5OTYsLTc5LjE5NDM1MzQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzU1MjA4NmMzNDIwMzQxN2Y5NzBmZmQyYmQ3OTRlNTA5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzEzZmIzZmVhNGYzNjRkZDM4MTYwNDk5ZWQ2NDg2NGFmID0gJCgnPGRpdiBpZD0iaHRtbF8xM2ZiM2ZlYTRmMzY0ZGQzODE2MDQ5OWVkNjQ4NjRhZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Um91Z2UsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81NTIwODZjMzQyMDM0MTdmOTcwZmZkMmJkNzk0ZTUwOS5zZXRDb250ZW50KGh0bWxfMTNmYjNmZWE0ZjM2NGRkMzgxNjA0OTllZDY0ODY0YWYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzJiOTFhNWQ5NDdjNGUwYmFhNGFmMmFjOTMxYjU0YzkuYmluZFBvcHVwKHBvcHVwXzU1MjA4NmMzNDIwMzQxN2Y5NzBmZmQyYmQ3OTRlNTA5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzIyMDYyYzdlZWMyZDQwNWE4M2VkZjRmMzFkM2FiZmRjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuODA2Njg2Mjk5OTk5OTk2LC03OS4xOTQzNTM0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80ZTg3ZGZhYjc2OWM0MzdkOGNhNjVhZDEzNjMyYjQwNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lNmMxYjM5M2FiOWM0NGJmYjliZjFmZTM1NTVlZThiMiA9ICQoJzxkaXYgaWQ9Imh0bWxfZTZjMWIzOTNhYjljNDRiZmI5YmYxZmUzNTU1ZWU4YjIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1hbHZlcm4sIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80ZTg3ZGZhYjc2OWM0MzdkOGNhNjVhZDEzNjMyYjQwNi5zZXRDb250ZW50KGh0bWxfZTZjMWIzOTNhYjljNDRiZmI5YmYxZmUzNTU1ZWU4YjIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMjIwNjJjN2VlYzJkNDA1YTgzZWRmNGYzMWQzYWJmZGMuYmluZFBvcHVwKHBvcHVwXzRlODdkZmFiNzY5YzQzN2Q4Y2E2NWFkMTM2MzJiNDA2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2Q5OTQ2NzliZDA1NzRjNGE5NDlmMzRkN2ZkNzczY2IxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzQ1OTA1Nzk5OTk5OTk2LC03OS4zNTIxODhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzIxOTVjMTA0ZTQ2NDhlZmI5ZDEyYzQ1OGM5YmE4ZGMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMmEzNTAwZjE2MGRjNDhlYmFhMmY5NjU2OTIxMmE3M2QgPSAkKCc8ZGl2IGlkPSJodG1sXzJhMzUwMGYxNjBkYzQ4ZWJhYTJmOTY1NjkyMTJhNzNkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Eb24gTWlsbHMgTm9ydGgsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzMyMTk1YzEwNGU0NjQ4ZWZiOWQxMmM0NThjOWJhOGRjLnNldENvbnRlbnQoaHRtbF8yYTM1MDBmMTYwZGM0OGViYWEyZjk2NTY5MjEyYTczZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kOTk0Njc5YmQwNTc0YzRhOTQ5ZjM0ZDdmZDc3M2NiMS5iaW5kUG9wdXAocG9wdXBfMzIxOTVjMTA0ZTQ2NDhlZmI5ZDEyYzQ1OGM5YmE4ZGMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjE2Mzk5ZDAyODVmNDI5YjlmN2JlNmFlMzU2NjkzNjYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MDYzOTcyLC03OS4zMDk5MzddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfN2FkOThiYTlmY2JjNDNhMzk0MzU2MzdjMDAyZjQ1MmMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYWIwZDkxNmVkMjNmNDQyNmEzOTJhNjljNDdiY2MyZDcgPSAkKCc8ZGl2IGlkPSJodG1sX2FiMGQ5MTZlZDIzZjQ0MjZhMzkyYTY5YzQ3YmNjMmQ3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Xb29kYmluZSBHYXJkZW5zLCBFYXN0IFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzdhZDk4YmE5ZmNiYzQzYTM5NDM1NjM3YzAwMmY0NTJjLnNldENvbnRlbnQoaHRtbF9hYjBkOTE2ZWQyM2Y0NDI2YTM5MmE2OWM0N2JjYzJkNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82MTYzOTlkMDI4NWY0MjliOWY3YmU2YWUzNTY2OTM2Ni5iaW5kUG9wdXAocG9wdXBfN2FkOThiYTlmY2JjNDNhMzk0MzU2MzdjMDAyZjQ1MmMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNWVjNmZmZGJjYzk0NGQwMTk2ODhlMjRmY2I0NzYzMmQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MDYzOTcyLC03OS4zMDk5MzddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDVmOGYzMDVjZTczNDdiZTg0MDY0NGYxOWI0ZTJhZDggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODg0NGYzNTk2ZTY3NDdhOWEzMjk0YzkwMDdmNDZiYWIgPSAkKCc8ZGl2IGlkPSJodG1sXzg4NDRmMzU5NmU2NzQ3YTlhMzI5NGM5MDA3ZjQ2YmFiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QYXJrdmlldyBIaWxsLCBFYXN0IFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQ1ZjhmMzA1Y2U3MzQ3YmU4NDA2NDRmMTliNGUyYWQ4LnNldENvbnRlbnQoaHRtbF84ODQ0ZjM1OTZlNjc0N2E5YTMyOTRjOTAwN2Y0NmJhYik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81ZWM2ZmZkYmNjOTQ0ZDAxOTY4OGUyNGZjYjQ3NjMyZC5iaW5kUG9wdXAocG9wdXBfNDVmOGYzMDVjZTczNDdiZTg0MDY0NGYxOWI0ZTJhZDgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjA5NTVkMmQ5MTJkNDQ0N2FlNTMyYTAwY2NjOGZjZDQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTcxNjE4LC03OS4zNzg5MzcwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80MDg1NTVlYjMwNTc0YTEyYTA0Zjc5MzkxZDU2Yjc4NSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lNmZhYTQ2ZDllY2Y0NTc1YTUxNjQ1ODFiYzUzMDYxZSA9ICQoJzxkaXYgaWQ9Imh0bWxfZTZmYWE0NmQ5ZWNmNDU3NWE1MTY0NTgxYmM1MzA2MWUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJ5ZXJzb24sIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQwODU1NWViMzA1NzRhMTJhMDRmNzkzOTFkNTZiNzg1LnNldENvbnRlbnQoaHRtbF9lNmZhYTQ2ZDllY2Y0NTc1YTUxNjQ1ODFiYzUzMDYxZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yMDk1NWQyZDkxMmQ0NDQ3YWU1MzJhMDBjY2M4ZmNkNC5iaW5kUG9wdXAocG9wdXBfNDA4NTU1ZWIzMDU3NGExMmEwNGY3OTM5MWQ1NmI3ODUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDIwODYwMWMzMjA3NDAwOWI1OWMxYmJiNzc4NTVhNWYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTcxNjE4LC03OS4zNzg5MzcwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lODhjYjk4YWIxMzk0ZDkwOTkzNjY2M2Y4YTgyMWRlMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84OGI4NTlhZjNiYmM0NWQ1OTg4Y2RlMzI4Mjg4ZjY0NSA9ICQoJzxkaXYgaWQ9Imh0bWxfODhiODU5YWYzYmJjNDVkNTk4OGNkZTMyODI4OGY2NDUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkdhcmRlbiBEaXN0cmljdCwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTg4Y2I5OGFiMTM5NGQ5MDk5MzY2NjNmOGE4MjFkZTIuc2V0Q29udGVudChodG1sXzg4Yjg1OWFmM2JiYzQ1ZDU5ODhjZGUzMjgyODhmNjQ1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2QyMDg2MDFjMzIwNzQwMDliNTljMWJiYjc3ODU1YTVmLmJpbmRQb3B1cChwb3B1cF9lODhjYjk4YWIxMzk0ZDkwOTkzNjY2M2Y4YTgyMWRlMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jYWI5NmIyNWRmZTE0YWY3Yjk1NzA0ZDViYjZhMmUwZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcwOTU3NywtNzkuNDQ1MDcyNTk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTFiZjdkMDg1MTk0NDI1YmIyNTljM2M4NThhNjc5NWMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfM2I5MTY3MDQyZWNhNDJlODg4OWM2MWJlZTJjMTc5NTggPSAkKCc8ZGl2IGlkPSJodG1sXzNiOTE2NzA0MmVjYTQyZTg4ODljNjFiZWUyYzE3OTU4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5HbGVuY2Fpcm4sIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkxYmY3ZDA4NTE5NDQyNWJiMjU5YzNjODU4YTY3OTVjLnNldENvbnRlbnQoaHRtbF8zYjkxNjcwNDJlY2E0MmU4ODg5YzYxYmVlMmMxNzk1OCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jYWI5NmIyNWRmZTE0YWY3Yjk1NzA0ZDViYjZhMmUwZC5iaW5kUG9wdXAocG9wdXBfOTFiZjdkMDg1MTk0NDI1YmIyNTljM2M4NThhNjc5NWMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDg2ZTNmZWUwNzBkNDYzYjlhODg2YmZhYTM1YjVjZGQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTA5NDMyLC03OS41NTQ3MjQ0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xN2ZjNTQ2ZWY2NjU0OWVkOWQ1MjVhZTFiMWE3MGJmZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kNDE4MzFkZGZiNDg0YTdkOGI0N2QzY2JiNzE1MDRjNyA9ICQoJzxkaXYgaWQ9Imh0bWxfZDQxODMxZGRmYjQ4NGE3ZDhiNDdkM2NiYjcxNTA0YzciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNsb3ZlcmRhbGUsIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTdmYzU0NmVmNjY1NDllZDlkNTI1YWUxYjFhNzBiZmYuc2V0Q29udGVudChodG1sX2Q0MTgzMWRkZmI0ODRhN2Q4YjQ3ZDNjYmI3MTUwNGM3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzA4NmUzZmVlMDcwZDQ2M2I5YTg4NmJmYWEzNWI1Y2RkLmJpbmRQb3B1cChwb3B1cF8xN2ZjNTQ2ZWY2NjU0OWVkOWQ1MjVhZTFiMWE3MGJmZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wYjBmNjlhYTNkNjc0ODIyOTk0ODkyNGNjNmQwNTUyNyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MDk0MzIsLTc5LjU1NDcyNDQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2RmM2Y2MTk2N2Q2NTRiODFiY2Y1MWY3OTVlMjFjNWVhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2QxZTc3ODYzYzYyZjQxNzNiYmI3ZGI0YmExYTA1MGY5ID0gJCgnPGRpdiBpZD0iaHRtbF9kMWU3Nzg2M2M2MmY0MTczYmJiN2RiNGJhMWEwNTBmOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SXNsaW5ndG9uLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2RmM2Y2MTk2N2Q2NTRiODFiY2Y1MWY3OTVlMjFjNWVhLnNldENvbnRlbnQoaHRtbF9kMWU3Nzg2M2M2MmY0MTczYmJiN2RiNGJhMWEwNTBmOSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wYjBmNjlhYTNkNjc0ODIyOTk0ODkyNGNjNmQwNTUyNy5iaW5kUG9wdXAocG9wdXBfZGYzZjYxOTY3ZDY1NGI4MWJjZjUxZjc5NWUyMWM1ZWEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOWI4NTRmZjQxNmE2NGIyYmI5NTcxOTUzN2Y1NTE0ZTMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTA5NDMyLC03OS41NTQ3MjQ0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zNTQxZDgzOGE3NWU0MjIyYThlNTY4NzEyM2M3ZjA2ZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lNjcyNDczOTNiMGI0YTQ1OWY3NjFjMTc4NGQ3ZDVjNSA9ICQoJzxkaXYgaWQ9Imh0bWxfZTY3MjQ3MzkzYjBiNGE0NTlmNzYxYzE3ODRkN2Q1YzUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1hcnRpbiBHcm92ZSwgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zNTQxZDgzOGE3NWU0MjIyYThlNTY4NzEyM2M3ZjA2ZS5zZXRDb250ZW50KGh0bWxfZTY3MjQ3MzkzYjBiNGE0NTlmNzYxYzE3ODRkN2Q1YzUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOWI4NTRmZjQxNmE2NGIyYmI5NTcxOTUzN2Y1NTE0ZTMuYmluZFBvcHVwKHBvcHVwXzM1NDFkODM4YTc1ZTQyMjJhOGU1Njg3MTIzYzdmMDZlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzYyMDMwNWE1ZTQyMjQwYzdhYzg5MjJlN2RiNjQwNzlkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUwOTQzMiwtNzkuNTU0NzI0NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTMxM2M3OTYzYWM3NDY0MTkzMmY4ZDliOTJjYWRjNGQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMGNiOWZkNWI2ZjViNDdkMWI5NzZhMTQ3YjFkMzg4ZjQgPSAkKCc8ZGl2IGlkPSJodG1sXzBjYjlmZDViNmY1YjQ3ZDFiOTc2YTE0N2IxZDM4OGY0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QcmluY2VzcyBHYXJkZW5zLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkzMTNjNzk2M2FjNzQ2NDE5MzJmOGQ5YjkyY2FkYzRkLnNldENvbnRlbnQoaHRtbF8wY2I5ZmQ1YjZmNWI0N2QxYjk3NmExNDdiMWQzODhmNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82MjAzMDVhNWU0MjI0MGM3YWM4OTIyZTdkYjY0MDc5ZC5iaW5kUG9wdXAocG9wdXBfOTMxM2M3OTYzYWM3NDY0MTkzMmY4ZDliOTJjYWRjNGQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTBmNGE2NmFlYzgyNDE5YWJmNWRlMGRkZmEyNjlkN2YgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTA5NDMyLC03OS41NTQ3MjQ0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83YzdiMTBhYTJlOTA0OTQwYjM3NzExMGM2MWU3YjRiNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84ZmYxYzkyMjgzMDA0ZTdjODcyZGY0Y2QwM2FhOWEwNyA9ICQoJzxkaXYgaWQ9Imh0bWxfOGZmMWM5MjI4MzAwNGU3Yzg3MmRmNGNkMDNhYTlhMDciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPldlc3QgRGVhbmUgUGFyaywgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83YzdiMTBhYTJlOTA0OTQwYjM3NzExMGM2MWU3YjRiNi5zZXRDb250ZW50KGh0bWxfOGZmMWM5MjI4MzAwNGU3Yzg3MmRmNGNkMDNhYTlhMDcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTBmNGE2NmFlYzgyNDE5YWJmNWRlMGRkZmEyNjlkN2YuYmluZFBvcHVwKHBvcHVwXzdjN2IxMGFhMmU5MDQ5NDBiMzc3MTEwYzYxZTdiNGI2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzZhNTg5ODE1MzI2MTQzNTg4YzFiODFlM2RlYjE1YWYwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzg0NTM1MSwtNzkuMTYwNDk3MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzg0OTMxMTZjN2JkNDBkOGE1NWYzOWIyZmU4NzMxZGEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjgwMWIwN2M4Mjc0NGU4MGI5NmY0NmM5OTg2NzAzMGQgPSAkKCc8ZGl2IGlkPSJodG1sXzI4MDFiMDdjODI3NDRlODBiOTZmNDZjOTk4NjcwMzBkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IaWdobGFuZCBDcmVlaywgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzc4NDkzMTE2YzdiZDQwZDhhNTVmMzliMmZlODczMWRhLnNldENvbnRlbnQoaHRtbF8yODAxYjA3YzgyNzQ0ZTgwYjk2ZjQ2Yzk5ODY3MDMwZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82YTU4OTgxNTMyNjE0MzU4OGMxYjgxZTNkZWIxNWFmMC5iaW5kUG9wdXAocG9wdXBfNzg0OTMxMTZjN2JkNDBkOGE1NWYzOWIyZmU4NzMxZGEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzk1Yjk3NWUyNzkwNGI0ODljNjIyZjc1YjY1MzRkZDIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43ODQ1MzUxLC03OS4xNjA0OTcwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jY2UxMzYzMTEwNWE0NjJmOGMyYWIwZWY1M2U4MTUwNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yOTRmZDMxMjBkMjE0Njk3OGFkYzE5ODI3MjAxMWRlOCA9ICQoJzxkaXYgaWQ9Imh0bWxfMjk0ZmQzMTIwZDIxNDY5NzhhZGMxOTgyNzIwMTFkZTgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJvdWdlIEhpbGwsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jY2UxMzYzMTEwNWE0NjJmOGMyYWIwZWY1M2U4MTUwNS5zZXRDb250ZW50KGh0bWxfMjk0ZmQzMTIwZDIxNDY5NzhhZGMxOTgyNzIwMTFkZTgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzk1Yjk3NWUyNzkwNGI0ODljNjIyZjc1YjY1MzRkZDIuYmluZFBvcHVwKHBvcHVwX2NjZTEzNjMxMTA1YTQ2MmY4YzJhYjBlZjUzZTgxNTA1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzZlNDJkNDA2NGJjMDQ4MGQ5NjU4MzJlYzZiMWQ5ZDc1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzg0NTM1MSwtNzkuMTYwNDk3MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOGU0YmM0NmU4OGI5NDM3ODllMGVlNmU1MGI0N2VkNmMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYjVkYWQxYWEzNTE3NDljM2JmYjA4MjA2MWQ2Zjc2ZmMgPSAkKCc8ZGl2IGlkPSJodG1sX2I1ZGFkMWFhMzUxNzQ5YzNiZmIwODIwNjFkNmY3NmZjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Qb3J0IFVuaW9uLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOGU0YmM0NmU4OGI5NDM3ODllMGVlNmU1MGI0N2VkNmMuc2V0Q29udGVudChodG1sX2I1ZGFkMWFhMzUxNzQ5YzNiZmIwODIwNjFkNmY3NmZjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzZlNDJkNDA2NGJjMDQ4MGQ5NjU4MzJlYzZiMWQ5ZDc1LmJpbmRQb3B1cChwb3B1cF84ZTRiYzQ2ZTg4Yjk0Mzc4OWUwZWU2ZTUwYjQ3ZWQ2Yyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yZmM3NDdiNDUxN2Y0ZTI4YTI3N2JlNzk2YmI3ZDkxZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcyNTg5OTcwMDAwMDAxLC03OS4zNDA5MjNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzRlMjQ2ODdiNjk0NDNkMzgxN2UwZGEwMjNjZjAwOGQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMGZjNGE1YmI4ZmM2NGE5OTljNWFlZDM0ZmJlMDRiMTggPSAkKCc8ZGl2IGlkPSJodG1sXzBmYzRhNWJiOGZjNjRhOTk5YzVhZWQzNGZiZTA0YjE4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5GbGVtaW5nZG9uIFBhcmssIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzc0ZTI0Njg3YjY5NDQzZDM4MTdlMGRhMDIzY2YwMDhkLnNldENvbnRlbnQoaHRtbF8wZmM0YTViYjhmYzY0YTk5OWM1YWVkMzRmYmUwNGIxOCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yZmM3NDdiNDUxN2Y0ZTI4YTI3N2JlNzk2YmI3ZDkxZi5iaW5kUG9wdXAocG9wdXBfNzRlMjQ2ODdiNjk0NDNkMzgxN2UwZGEwMjNjZjAwOGQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOWRjODJjZDY2MTFlNDAwNTk5OGYwOTNmYmJjMTgxZDMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MjU4OTk3MDAwMDAwMSwtNzkuMzQwOTIzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzA1NmIxODYzOTZkYTRmZWI5MTQ1NzM4MmI4NmU2ZDliID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2E2N2M2YmI4OTZjMTQxYTA4OTRhNzg0YjZmNzc3ODllID0gJCgnPGRpdiBpZD0iaHRtbF9hNjdjNmJiODk2YzE0MWEwODk0YTc4NGI2Zjc3Nzg5ZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RG9uIE1pbGxzIFNvdXRoLCBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wNTZiMTg2Mzk2ZGE0ZmViOTE0NTczODJiODZlNmQ5Yi5zZXRDb250ZW50KGh0bWxfYTY3YzZiYjg5NmMxNDFhMDg5NGE3ODRiNmY3Nzc4OWUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOWRjODJjZDY2MTFlNDAwNTk5OGYwOTNmYmJjMTgxZDMuYmluZFBvcHVwKHBvcHVwXzA1NmIxODYzOTZkYTRmZWI5MTQ1NzM4MmI4NmU2ZDliKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzM2MDc3NzY1NDUyYzQzN2VhODUxZWMyZWEyMjFkNDY5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjk1MzQzOTAwMDAwMDA1LC03OS4zMTgzODg3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2NlMDYyZDU5YzQ3NzQxNjE5N2YzMDVkOGIwMGNlNDQ4ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzUwZTk5MjZjZmVhNjRhOTc4MjQwNzcwMzRjM2Q4MjkwID0gJCgnPGRpdiBpZD0iaHRtbF81MGU5OTI2Y2ZlYTY0YTk3ODI0MDc3MDM0YzNkODI5MCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V29vZGJpbmUgSGVpZ2h0cywgRWFzdCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jZTA2MmQ1OWM0Nzc0MTYxOTdmMzA1ZDhiMDBjZTQ0OC5zZXRDb250ZW50KGh0bWxfNTBlOTkyNmNmZWE2NGE5NzgyNDA3NzAzNGMzZDgyOTApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMzYwNzc3NjU0NTJjNDM3ZWE4NTFlYzJlYTIyMWQ0NjkuYmluZFBvcHVwKHBvcHVwX2NlMDYyZDU5YzQ3NzQxNjE5N2YzMDVkOGIwMGNlNDQ4KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzRmZTBkMGRhZWQ4NDQwOTI5MGU4MDhiNDhmODg1MWI3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUxNDkzOSwtNzkuMzc1NDE3OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jNTYwN2E1OWQxZjk0ZTI5OGUyODhjMjc5ODgwYjk1NyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jZWNlZmE5OTVjNGY0ZmMyODRjODM1MDY0ZTY1YWFmNSA9ICQoJzxkaXYgaWQ9Imh0bWxfY2VjZWZhOTk1YzRmNGZjMjg0YzgzNTA2NGU2NWFhZjUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0LiBKYW1lcyBUb3duLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jNTYwN2E1OWQxZjk0ZTI5OGUyODhjMjc5ODgwYjk1Ny5zZXRDb250ZW50KGh0bWxfY2VjZWZhOTk1YzRmNGZjMjg0YzgzNTA2NGU2NWFhZjUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNGZlMGQwZGFlZDg0NDA5MjkwZTgwOGI0OGY4ODUxYjcuYmluZFBvcHVwKHBvcHVwX2M1NjA3YTU5ZDFmOTRlMjk4ZTI4OGMyNzk4ODBiOTU3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzQ3OWRlZTA0YzU0MDQyNDZhOTVkOWVmMjg4NmUzNjRjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjkzNzgxMywtNzkuNDI4MTkxNDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWFmMzhkNzk3MTcwNGU5YTk2OGZjYWU5NDk3NDQ3ZDQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYjc5NzdlNWM4YmM5NDI2ZGJiOWRkZWNmYjRlMTI4NzAgPSAkKCc8ZGl2IGlkPSJodG1sX2I3OTc3ZTVjOGJjOTQyNmRiYjlkZGVjZmI0ZTEyODcwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IdW1ld29vZC1DZWRhcnZhbGUsIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2FhZjM4ZDc5NzE3MDRlOWE5NjhmY2FlOTQ5NzQ0N2Q0LnNldENvbnRlbnQoaHRtbF9iNzk3N2U1YzhiYzk0MjZkYmI5ZGRlY2ZiNGUxMjg3MCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80NzlkZWUwNGM1NDA0MjQ2YTk1ZDllZjI4ODZlMzY0Yy5iaW5kUG9wdXAocG9wdXBfYWFmMzhkNzk3MTcwNGU5YTk2OGZjYWU5NDk3NDQ3ZDQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODYyN2IxYWYxZmJiNGExZGI3MjY2YjA0NjVkOTZkZmQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDM1MTUyLC03OS41NzcyMDA3OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84YmFiZjE5MGIyZTU0MzM3ODEzNGIyZjdkNGIwZTkxMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81ZTRjMDFkYzEzM2U0NDE0OWQ3MzZjMmIzNzkzOTYxMCA9ICQoJzxkaXYgaWQ9Imh0bWxfNWU0YzAxZGMxMzNlNDQxNDlkNzM2YzJiMzc5Mzk2MTAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJsb29yZGFsZSBHYXJkZW5zLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzhiYWJmMTkwYjJlNTQzMzc4MTM0YjJmN2Q0YjBlOTEzLnNldENvbnRlbnQoaHRtbF81ZTRjMDFkYzEzM2U0NDE0OWQ3MzZjMmIzNzkzOTYxMCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84NjI3YjFhZjFmYmI0YTFkYjcyNjZiMDQ2NWQ5NmRmZC5iaW5kUG9wdXAocG9wdXBfOGJhYmYxOTBiMmU1NDMzNzgxMzRiMmY3ZDRiMGU5MTMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZWQzNjgwZTdmMGE5NGQwZTllZDM2ZDA3N2JiZjU3ZDggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDM1MTUyLC03OS41NzcyMDA3OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85OWVjMGViMmVlMjI0ZTc1YWEzZjZlMjk5MjJhOTdiZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iZDhhYWJlMDI5MmY0NmNiODNjMDYxNzM5YWZiZWJiNCA9ICQoJzxkaXYgaWQ9Imh0bWxfYmQ4YWFiZTAyOTJmNDZjYjgzYzA2MTczOWFmYmViYjQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkVyaW5nYXRlLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzk5ZWMwZWIyZWUyMjRlNzVhYTNmNmUyOTkyMmE5N2JmLnNldENvbnRlbnQoaHRtbF9iZDhhYWJlMDI5MmY0NmNiODNjMDYxNzM5YWZiZWJiNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lZDM2ODBlN2YwYTk0ZDBlOWVkMzZkMDc3YmJmNTdkOC5iaW5kUG9wdXAocG9wdXBfOTllYzBlYjJlZTIyNGU3NWFhM2Y2ZTI5OTIyYTk3YmYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjVkM2JmZGQzZmY5NGZjMzhhNWM2NGNhY2U1YTdkMjIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDM1MTUyLC03OS41NzcyMDA3OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hOTQ0NGQ5MWYwODc0OWEzOGMyNDNkMzg1ODBkM2YwMSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iZjc4ZGJmZjAxY2M0YTU1YWNiMWIxYWZjZDlmOThiZiA9ICQoJzxkaXYgaWQ9Imh0bWxfYmY3OGRiZmYwMWNjNGE1NWFjYjFiMWFmY2Q5Zjk4YmYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1hcmtsYW5kIFdvb2QsIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYTk0NDRkOTFmMDg3NDlhMzhjMjQzZDM4NTgwZDNmMDEuc2V0Q29udGVudChodG1sX2JmNzhkYmZmMDFjYzRhNTVhY2IxYjFhZmNkOWY5OGJmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Y1ZDNiZmRkM2ZmOTRmYzM4YTVjNjRjYWNlNWE3ZDIyLmJpbmRQb3B1cChwb3B1cF9hOTQ0NGQ5MWYwODc0OWEzOGMyNDNkMzg1ODBkM2YwMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kNGY5YWFiODgwYzk0ZTBhYjlhM2E5MGM2MDBlODExNyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0MzUxNTIsLTc5LjU3NzIwMDc5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2FlNzJmMmVjODc1ZTQxMDA4Y2JlNTBiMTI4ZGMzODhlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzg5NjM5MGM4ZWRmMjRhMjk4ZTI1YmZjNzFlNGZmMGQ5ID0gJCgnPGRpdiBpZD0iaHRtbF84OTYzOTBjOGVkZjI0YTI5OGUyNWJmYzcxZTRmZjBkOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+T2xkIEJ1cm5oYW10aG9ycGUsIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYWU3MmYyZWM4NzVlNDEwMDhjYmU1MGIxMjhkYzM4OGUuc2V0Q29udGVudChodG1sXzg5NjM5MGM4ZWRmMjRhMjk4ZTI1YmZjNzFlNGZmMGQ5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Q0ZjlhYWI4ODBjOTRlMGFiOWEzYTkwYzYwMGU4MTE3LmJpbmRQb3B1cChwb3B1cF9hZTcyZjJlYzg3NWU0MTAwOGNiZTUwYjEyOGRjMzg4ZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85ODU0ZjU5NjI3YWQ0OTM0YWMwOThhOTRmZDA3YjFjMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc2MzU3MjYsLTc5LjE4ODcxMTVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNjQ5MTJkYTQ4YWY5NDQ3YTgzM2QxNzg3Y2RmNGE4NTMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZjcwMTJkNGRiZTM5NDY3ZmI0MGVmMjUwNmIyY2IxYmIgPSAkKCc8ZGl2IGlkPSJodG1sX2Y3MDEyZDRkYmUzOTQ2N2ZiNDBlZjI1MDZiMmNiMWJiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5HdWlsZHdvb2QsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82NDkxMmRhNDhhZjk0NDdhODMzZDE3ODdjZGY0YTg1My5zZXRDb250ZW50KGh0bWxfZjcwMTJkNGRiZTM5NDY3ZmI0MGVmMjUwNmIyY2IxYmIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTg1NGY1OTYyN2FkNDkzNGFjMDk4YTk0ZmQwN2IxYzIuYmluZFBvcHVwKHBvcHVwXzY0OTEyZGE0OGFmOTQ0N2E4MzNkMTc4N2NkZjRhODUzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzkwZGI1YmFmNjYwNDRlYmViYzlhODcwOWIxMWRhYTNmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzYzNTcyNiwtNzkuMTg4NzExNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84ODMyNTQyZWVhNjY0NDA4YTNiMTZmZTc0OTcxNGQ5NSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80NTVmNjQxOTE5OWY0NDUyYTMxNjlkZWQ1NWYzNjNmZCA9ICQoJzxkaXYgaWQ9Imh0bWxfNDU1ZjY0MTkxOTlmNDQ1MmEzMTY5ZGVkNTVmMzYzZmQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1vcm5pbmdzaWRlLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODgzMjU0MmVlYTY2NDQwOGEzYjE2ZmU3NDk3MTRkOTUuc2V0Q29udGVudChodG1sXzQ1NWY2NDE5MTk5ZjQ0NTJhMzE2OWRlZDU1ZjM2M2ZkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzkwZGI1YmFmNjYwNDRlYmViYzlhODcwOWIxMWRhYTNmLmJpbmRQb3B1cChwb3B1cF84ODMyNTQyZWVhNjY0NDA4YTNiMTZmZTc0OTcxNGQ5NSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81NDk2MWFlZjcyYmU0MTYzOWM0NjM3MDAyZTNjMTU1NSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc2MzU3MjYsLTc5LjE4ODcxMTVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOGE0ODk5YzNlMTdhNGE5Mzk0YTg4OGUzYWVmZGU3ZTAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTI5OGQ2OWM2YmZiNDQ0OWI0ZjI2ODNmMjQ1ZTM0ZTIgPSAkKCc8ZGl2IGlkPSJodG1sX2UyOThkNjljNmJmYjQ0NDliNGYyNjgzZjI0NWUzNGUyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XZXN0IEhpbGwsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84YTQ4OTljM2UxN2E0YTkzOTRhODg4ZTNhZWZkZTdlMC5zZXRDb250ZW50KGh0bWxfZTI5OGQ2OWM2YmZiNDQ0OWI0ZjI2ODNmMjQ1ZTM0ZTIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNTQ5NjFhZWY3MmJlNDE2MzljNDYzNzAwMmUzYzE1NTUuYmluZFBvcHVwKHBvcHVwXzhhNDg5OWMzZTE3YTRhOTM5NGE4ODhlM2FlZmRlN2UwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU2ZjA4YzUyZmEyNzRmZjE4MTFlYmY4NDhhM2NmZmJkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjc2MzU3Mzk5OTk5OTksLTc5LjI5MzAzMTJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTUyZGU4MTE5Njc0NGFkZmE1ZmQ1YzJhNmQ2YWQ0ZGUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDE2MDExYTQyNjRiNDZiYjhhNzE3NDkwZDRjYTA5ZjkgPSAkKCc8ZGl2IGlkPSJodG1sX2QxNjAxMWE0MjY0YjQ2YmI4YTcxNzQ5MGQ0Y2EwOWY5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgQmVhY2hlcywgRWFzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xNTJkZTgxMTk2NzQ0YWRmYTVmZDVjMmE2ZDZhZDRkZS5zZXRDb250ZW50KGh0bWxfZDE2MDExYTQyNjRiNDZiYjhhNzE3NDkwZDRjYTA5ZjkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNTZmMDhjNTJmYTI3NGZmMTgxMWViZjg0OGEzY2ZmYmQuYmluZFBvcHVwKHBvcHVwXzE1MmRlODExOTY3NDRhZGZhNWZkNWMyYTZkNmFkNGRlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzEwOTUyYjdmYzFhMzQ3MGJiMjg5ZjFkMjk1OWYwOGE3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ0NzcwNzk5OTk5OTk2LC03OS4zNzMzMDY0XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2YxZGMwMmE3NGM1MjQ5NzhhMjQyOWQ5MTNjZDFkMDFlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2VjNGU0Y2VjM2M5YTQ3NDU4ZGUzZDdkZWMzZTU5Y2ExID0gJCgnPGRpdiBpZD0iaHRtbF9lYzRlNGNlYzNjOWE0NzQ1OGRlM2Q3ZGVjM2U1OWNhMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QmVyY3p5IFBhcmssIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2YxZGMwMmE3NGM1MjQ5NzhhMjQyOWQ5MTNjZDFkMDFlLnNldENvbnRlbnQoaHRtbF9lYzRlNGNlYzNjOWE0NzQ1OGRlM2Q3ZGVjM2U1OWNhMSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xMDk1MmI3ZmMxYTM0NzBiYjI4OWYxZDI5NTlmMDhhNy5iaW5kUG9wdXAocG9wdXBfZjFkYzAyYTc0YzUyNDk3OGEyNDI5ZDkxM2NkMWQwMWUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjhiMjJmMjg4MzY4NGU3OGI2YWUyYzFjZmZkMDlhM2IgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODkwMjU2LC03OS40NTM1MTJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNWZiOGE5ZDA2MjlhNGFkYmIzMTY0NWJlMThhNDExMGYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODFjYTg0MWJiMmMyNDUxZmJkYmNjNTI2NjE2NTQ0ZWEgPSAkKCc8ZGl2IGlkPSJodG1sXzgxY2E4NDFiYjJjMjQ1MWZiZGJjYzUyNjYxNjU0NGVhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DYWxlZG9uaWEtRmFpcmJhbmtzLCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81ZmI4YTlkMDYyOWE0YWRiYjMxNjQ1YmUxOGE0MTEwZi5zZXRDb250ZW50KGh0bWxfODFjYTg0MWJiMmMyNDUxZmJkYmNjNTI2NjE2NTQ0ZWEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjhiMjJmMjg4MzY4NGU3OGI2YWUyYzFjZmZkMDlhM2IuYmluZFBvcHVwKHBvcHVwXzVmYjhhOWQwNjI5YTRhZGJiMzE2NDViZTE4YTQxMTBmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzE1NDcxYzNhNTc2ODQ0OTc5NjZjNDYxNWRhYmY1ZmQ4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzcwOTkyMSwtNzkuMjE2OTE3NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYTU4OTllZmNhZmYyNDBiMjhhYmFlZmNmZWUzZDFlNWIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZWViMmI4YThjNWU5NDI2YjgxYTZkZmE1ZjJjMDcyNzkgPSAkKCc8ZGl2IGlkPSJodG1sX2VlYjJiOGE4YzVlOTQyNmI4MWE2ZGZhNWYyYzA3Mjc5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Xb2J1cm4sIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hNTg5OWVmY2FmZjI0MGIyOGFiYWVmY2ZlZTNkMWU1Yi5zZXRDb250ZW50KGh0bWxfZWViMmI4YThjNWU5NDI2YjgxYTZkZmE1ZjJjMDcyNzkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMTU0NzFjM2E1NzY4NDQ5Nzk2NmM0NjE1ZGFiZjVmZDguYmluZFBvcHVwKHBvcHVwX2E1ODk5ZWZjYWZmMjQwYjI4YWJhZWZjZmVlM2QxZTViKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2JhNzY1ZjdhNjdiNjRiZWVhMGU4OTFjZDE2YTdmOWQ5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzA5MDYwNCwtNzkuMzYzNDUxN10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yMzAyYWYzMjI5NmU0MjJlODQzYzdlZGY1OWUyMjA1OSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zYTNkNWRlZDhkZDg0ZjYwYWY4YjgwYTc4OTI4N2RlZSA9ICQoJzxkaXYgaWQ9Imh0bWxfM2EzZDVkZWQ4ZGQ4NGY2MGFmOGI4MGE3ODkyODdkZWUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxlYXNpZGUsIEVhc3QgWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjMwMmFmMzIyOTZlNDIyZTg0M2M3ZWRmNTllMjIwNTkuc2V0Q29udGVudChodG1sXzNhM2Q1ZGVkOGRkODRmNjBhZjhiODBhNzg5Mjg3ZGVlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2JhNzY1ZjdhNjdiNjRiZWVhMGU4OTFjZDE2YTdmOWQ5LmJpbmRQb3B1cChwb3B1cF8yMzAyYWYzMjI5NmU0MjJlODQzYzdlZGY1OWUyMjA1OSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lNmZhNTE3ZjVlMzA0YTMxYjZjZDM5YzE0NGJiZTNhOSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1Nzk1MjQsLTc5LjM4NzM4MjZdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTJkYmJlZDYxY2UyNDA1Njg1MzFlNmU5MWQ0OGRhZmEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZGNlZmY2MmVhMDI4NDI2MGI3MGIxOWQ1MDk5OGViOWEgPSAkKCc8ZGl2IGlkPSJodG1sX2RjZWZmNjJlYTAyODQyNjBiNzBiMTlkNTA5OThlYjlhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DZW50cmFsIEJheSBTdHJlZXQsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkyZGJiZWQ2MWNlMjQwNTY4NTMxZTZlOTFkNDhkYWZhLnNldENvbnRlbnQoaHRtbF9kY2VmZjYyZWEwMjg0MjYwYjcwYjE5ZDUwOTk4ZWI5YSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lNmZhNTE3ZjVlMzA0YTMxYjZjZDM5YzE0NGJiZTNhOS5iaW5kUG9wdXAocG9wdXBfOTJkYmJlZDYxY2UyNDA1Njg1MzFlNmU5MWQ0OGRhZmEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjc0ODNkZjQ2MGI5NDgyMzlhMGRhM2VmNmVjNDZjNzcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Njk1NDIsLTc5LjQyMjU2MzddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjM0ZTYzZTMxOWRkNGJhMDg3ZmU2NDJiMThiMDliNGYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTQ0YTViZTcyZDRhNDAyMWEyMzg2N2ViY2JkZjY0ZDMgPSAkKCc8ZGl2IGlkPSJodG1sX2E0NGE1YmU3MmQ0YTQwMjFhMjM4NjdlYmNiZGY2NGQzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DaHJpc3RpZSwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjM0ZTYzZTMxOWRkNGJhMDg3ZmU2NDJiMThiMDliNGYuc2V0Q29udGVudChodG1sX2E0NGE1YmU3MmQ0YTQwMjFhMjM4NjdlYmNiZGY2NGQzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Y3NDgzZGY0NjBiOTQ4MjM5YTBkYTNlZjZlYzQ2Yzc3LmJpbmRQb3B1cChwb3B1cF9iMzRlNjNlMzE5ZGQ0YmEwODdmZTY0MmIxOGIwOWI0Zik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84Yzg4Mjg1YWViMDA0YWVhYjRiMzYzYWFmYzljMzBkNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc3MzEzNiwtNzkuMjM5NDc2MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODc4ZmViNWIxMTdmNGYxOGJjNTViMWZiNGI5YzcwMDAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDYwNTEyZDM0OTY1NDNjY2E4MTQyMWI5MDk4ODRjN2QgPSAkKCc8ZGl2IGlkPSJodG1sX2Q2MDUxMmQzNDk2NTQzY2NhODE0MjFiOTA5ODg0YzdkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DZWRhcmJyYWUsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84NzhmZWI1YjExN2Y0ZjE4YmM1NWIxZmI0YjljNzAwMC5zZXRDb250ZW50KGh0bWxfZDYwNTEyZDM0OTY1NDNjY2E4MTQyMWI5MDk4ODRjN2QpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOGM4ODI4NWFlYjAwNGFlYWI0YjM2M2FhZmM5YzMwZDUuYmluZFBvcHVwKHBvcHVwXzg3OGZlYjViMTE3ZjRmMThiYzU1YjFmYjRiOWM3MDAwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzliYTRmNGRkYzE3ZDQxMGE5MGNhZGUwNDgwNzI0NGYyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuODAzNzYyMiwtNzkuMzYzNDUxN10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kZWRkMTJlMDg2Y2Q0NTg0YWE3MjEwZGM4ZGU4NWYzNCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xMDQ4ODg4M2U2MmE0MGFiYWJmYzk4ZDliOGRjMmEyMyA9ICQoJzxkaXYgaWQ9Imh0bWxfMTA0ODg4ODNlNjJhNDBhYmFiZmM5OGQ5YjhkYzJhMjMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhpbGxjcmVzdCBWaWxsYWdlLCBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kZWRkMTJlMDg2Y2Q0NTg0YWE3MjEwZGM4ZGU4NWYzNC5zZXRDb250ZW50KGh0bWxfMTA0ODg4ODNlNjJhNDBhYmFiZmM5OGQ5YjhkYzJhMjMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOWJhNGY0ZGRjMTdkNDEwYTkwY2FkZTA0ODA3MjQ0ZjIuYmluZFBvcHVwKHBvcHVwX2RlZGQxMmUwODZjZDQ1ODRhYTcyMTBkYzhkZTg1ZjM0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2RmNjAxZWVmZTM5MzRjM2ZhN2QwYjE5NmE4ZDUzYjMxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzU0MzI4MywtNzkuNDQyMjU5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lMzg3YmM3ODAzZjE0MjY1YTBiMGU3YWQyYzVlYjk4ZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mODJlNzFhNTg0Yzc0YWU5YjlkMTE5OTFlMTY5ZTg3OCA9ICQoJzxkaXYgaWQ9Imh0bWxfZjgyZTcxYTU4NGM3NGFlOWI5ZDExOTkxZTE2OWU4NzgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJhdGh1cnN0IE1hbm9yLCBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lMzg3YmM3ODAzZjE0MjY1YTBiMGU3YWQyYzVlYjk4ZC5zZXRDb250ZW50KGh0bWxfZjgyZTcxYTU4NGM3NGFlOWI5ZDExOTkxZTE2OWU4NzgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZGY2MDFlZWZlMzkzNGMzZmE3ZDBiMTk2YThkNTNiMzEuYmluZFBvcHVwKHBvcHVwX2UzODdiYzc4MDNmMTQyNjVhMGIwZTdhZDJjNWViOThkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzA2MWQ5Y2UxM2U4MTRkMTRiOGYzNDBlMzZjYjY0Nzc5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzU0MzI4MywtNzkuNDQyMjU5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jMTczNzFlNGRlN2E0NWE2ODJmZjA1MWQ4MTcwMGNmMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jNmIxZWQ5OGZjZjI0ZWZlODk4OGMyYjE0Njk2Nzg3ZSA9ICQoJzxkaXYgaWQ9Imh0bWxfYzZiMWVkOThmY2YyNGVmZTg5ODhjMmIxNDY5Njc4N2UiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRvd25zdmlldyBOb3J0aCwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzE3MzcxZTRkZTdhNDVhNjgyZmYwNTFkODE3MDBjZjAuc2V0Q29udGVudChodG1sX2M2YjFlZDk4ZmNmMjRlZmU4OTg4YzJiMTQ2OTY3ODdlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzA2MWQ5Y2UxM2U4MTRkMTRiOGYzNDBlMzZjYjY0Nzc5LmJpbmRQb3B1cChwb3B1cF9jMTczNzFlNGRlN2E0NWE2ODJmZjA1MWQ4MTcwMGNmMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wNzQ2MmU4ODRlZDc0OTJkYjI3ZDZjY2FlMGIzNjdmYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc1NDMyODMsLTc5LjQ0MjI1OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzI0ZDQ4OWExMGE4NGNhZWJhMGFjYjU1OTc5YjE3Y2UgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYzhjMDgwMDJlYzVjNDk1NDljMjIyY2I1MjhhZDljNTMgPSAkKCc8ZGl2IGlkPSJodG1sX2M4YzA4MDAyZWM1YzQ5NTQ5YzIyMmNiNTI4YWQ5YzUzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5XaWxzb24gSGVpZ2h0cywgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzI0ZDQ4OWExMGE4NGNhZWJhMGFjYjU1OTc5YjE3Y2Uuc2V0Q29udGVudChodG1sX2M4YzA4MDAyZWM1YzQ5NTQ5YzIyMmNiNTI4YWQ5YzUzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzA3NDYyZTg4NGVkNzQ5MmRiMjdkNmNjYWUwYjM2N2ZjLmJpbmRQb3B1cChwb3B1cF83MjRkNDg5YTEwYTg0Y2FlYmEwYWNiNTU5NzliMTdjZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hMjMyYjUwZGE1ZmE0ZTVlYThlZThjN2I4ZWY4MGEyZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcwNTM2ODksLTc5LjM0OTM3MTkwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2NjNTMyMmNhMjg2NjQyYmU5YjI1MDMyYTQ0NzI4MDc3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzY2MjAxMjVkYzg2ZDQ0YjlhNzI0Yzg1OGRlNjhjNjcyID0gJCgnPGRpdiBpZD0iaHRtbF82NjIwMTI1ZGM4NmQ0NGI5YTcyNGM4NThkZTY4YzY3MiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VGhvcm5jbGlmZmUgUGFyaywgRWFzdCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jYzUzMjJjYTI4NjY0MmJlOWIyNTAzMmE0NDcyODA3Ny5zZXRDb250ZW50KGh0bWxfNjYyMDEyNWRjODZkNDRiOWE3MjRjODU4ZGU2OGM2NzIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYTIzMmI1MGRhNWZhNGU1ZWE4ZWU4YzdiOGVmODBhMmUuYmluZFBvcHVwKHBvcHVwX2NjNTMyMmNhMjg2NjQyYmU5YjI1MDMyYTQ0NzI4MDc3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzc5MzZlMmM5ZDQyYzQxMmFhNTE0M2YwNzFjYzkwYjIyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUwNTcxMjAwMDAwMDEsLTc5LjM4NDU2NzVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTIwOWNmZjBlYTg0NDZiMmI2MDYzZWNhNTI5MmQ2MWMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWNkM2E5ZjYzMzc5NGVmOTk5ZjZjMjY2OTM0ODJmZTUgPSAkKCc8ZGl2IGlkPSJodG1sXzVjZDNhOWY2MzM3OTRlZjk5OWY2YzI2NjkzNDgyZmU1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BZGVsYWlkZSwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTIwOWNmZjBlYTg0NDZiMmI2MDYzZWNhNTI5MmQ2MWMuc2V0Q29udGVudChodG1sXzVjZDNhOWY2MzM3OTRlZjk5OWY2YzI2NjkzNDgyZmU1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzc5MzZlMmM5ZDQyYzQxMmFhNTE0M2YwNzFjYzkwYjIyLmJpbmRQb3B1cChwb3B1cF9lMjA5Y2ZmMGVhODQ0NmIyYjYwNjNlY2E1MjkyZDYxYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83ZDBjNDQxNGM2ZWY0MjcwOTIzYjAzMzViNDUzYzM2YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MDU3MTIwMDAwMDAxLC03OS4zODQ1Njc1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Q4Y2IzMmYxZDJlNTRiMTQ5ODQ4Y2FjNWRlZTg2MWJjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzY2N2I2ZDRjZGY0ZjRiNTZhOGIxYjcyNTcxNThiZWYwID0gJCgnPGRpdiBpZD0iaHRtbF82NjdiNmQ0Y2RmNGY0YjU2YThiMWI3MjU3MTU4YmVmMCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+S2luZywgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDhjYjMyZjFkMmU1NGIxNDk4NDhjYWM1ZGVlODYxYmMuc2V0Q29udGVudChodG1sXzY2N2I2ZDRjZGY0ZjRiNTZhOGIxYjcyNTcxNThiZWYwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzdkMGM0NDE0YzZlZjQyNzA5MjNiMDMzNWI0NTNjMzZjLmJpbmRQb3B1cChwb3B1cF9kOGNiMzJmMWQyZTU0YjE0OTg0OGNhYzVkZWU4NjFiYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jOWYxZmM5NWQ3NGM0ZTc5OWU4OTRhOTE4ODNlNDc4YiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MDU3MTIwMDAwMDAxLC03OS4zODQ1Njc1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzJhMzg4OTdjMWQzODQyNTNhMTJmN2I5MTBjMTMxODg1ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2ZjNTcwYjQ4OWYzNjQ5NTdhOWU4NGRlOWQ0MTc0Y2I0ID0gJCgnPGRpdiBpZD0iaHRtbF9mYzU3MGI0ODlmMzY0OTU3YTllODRkZTlkNDE3NGNiNCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UmljaG1vbmQsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzJhMzg4OTdjMWQzODQyNTNhMTJmN2I5MTBjMTMxODg1LnNldENvbnRlbnQoaHRtbF9mYzU3MGI0ODlmMzY0OTU3YTllODRkZTlkNDE3NGNiNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jOWYxZmM5NWQ3NGM0ZTc5OWU4OTRhOTE4ODNlNDc4Yi5iaW5kUG9wdXAocG9wdXBfMmEzODg5N2MxZDM4NDI1M2ExMmY3YjkxMGMxMzE4ODUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfN2RmZmI1NGUxODE1NGU2YTkwMGJiODUzOWRiOGFiNjkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjkwMDUxMDAwMDAwMSwtNzkuNDQyMjU5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85OTAxM2JmNTE3ZmE0ZTM2OWEwZWIwNDM4ZjcwY2JlZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wODc1MDFjMTQ2MTA0NDU3YjQ0YTllYjczMmM1OWY3MyA9ICQoJzxkaXYgaWQ9Imh0bWxfMDg3NTAxYzE0NjEwNDQ1N2I0NGE5ZWI3MzJjNTlmNzMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRvdmVyY291cnQgVmlsbGFnZSwgV2VzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85OTAxM2JmNTE3ZmE0ZTM2OWEwZWIwNDM4ZjcwY2JlZC5zZXRDb250ZW50KGh0bWxfMDg3NTAxYzE0NjEwNDQ1N2I0NGE5ZWI3MzJjNTlmNzMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfN2RmZmI1NGUxODE1NGU2YTkwMGJiODUzOWRiOGFiNjkuYmluZFBvcHVwKHBvcHVwXzk5MDEzYmY1MTdmYTRlMzY5YTBlYjA0MzhmNzBjYmVkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzBjMzcyMjRkZTVjMzQ2ZDE4NTI3MWJjNGU3YTVlMzAzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY5MDA1MTAwMDAwMDEsLTc5LjQ0MjI1OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNjVlZmNlYjQxMzM4NDg2ZjhiNTEyODk3MzA5NjRmZjIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWFkYjdkNWEwMGViNDEzNDkwNTNiY2M5MmI3Nzk1MDMgPSAkKCc8ZGl2IGlkPSJodG1sXzFhZGI3ZDVhMDBlYjQxMzQ5MDUzYmNjOTJiNzc5NTAzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5EdWZmZXJpbiwgV2VzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82NWVmY2ViNDEzMzg0ODZmOGI1MTI4OTczMDk2NGZmMi5zZXRDb250ZW50KGh0bWxfMWFkYjdkNWEwMGViNDEzNDkwNTNiY2M5MmI3Nzk1MDMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMGMzNzIyNGRlNWMzNDZkMTg1MjcxYmM0ZTdhNWUzMDMuYmluZFBvcHVwKHBvcHVwXzY1ZWZjZWI0MTMzODQ4NmY4YjUxMjg5NzMwOTY0ZmYyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzJmY2JhZTZmMmM5YzRkZjM4Y2MyYjViZWQwOTI5N2VmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzQ0NzM0MiwtNzkuMjM5NDc2MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZWNiYzBhNTQ2MjAwNDhiYjhkYzFmOGE4YTIyNDliNmYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWEyNDM2YWM2ZDZmNDJlNThmNGViYzg5OGE5NWZiZmIgPSAkKCc8ZGl2IGlkPSJodG1sXzFhMjQzNmFjNmQ2ZjQyZTU4ZjRlYmM4OThhOTVmYmZiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TY2FyYm9yb3VnaCBWaWxsYWdlLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZWNiYzBhNTQ2MjAwNDhiYjhkYzFmOGE4YTIyNDliNmYuc2V0Q29udGVudChodG1sXzFhMjQzNmFjNmQ2ZjQyZTU4ZjRlYmM4OThhOTVmYmZiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzJmY2JhZTZmMmM5YzRkZjM4Y2MyYjViZWQwOTI5N2VmLmJpbmRQb3B1cChwb3B1cF9lY2JjMGE1NDYyMDA0OGJiOGRjMWY4YThhMjI0OWI2Zik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wNDBlOTI0NjE4ZjY0ZTE1OGY5N2UxZWU0YzkxNjgyZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc3ODUxNzUsLTc5LjM0NjU1NTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTg3NDMxMzVjY2E5NDEzZGE5YjQzZjdhYTEyYTdlY2EgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMDI3MzUyMjZhMDQ3NDA4YjliNGE2NzZmMTliNjllN2UgPSAkKCc8ZGl2IGlkPSJodG1sXzAyNzM1MjI2YTA0NzQwOGI5YjRhNjc2ZjE5YjY5ZTdlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5GYWlydmlldywgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTg3NDMxMzVjY2E5NDEzZGE5YjQzZjdhYTEyYTdlY2Euc2V0Q29udGVudChodG1sXzAyNzM1MjI2YTA0NzQwOGI5YjRhNjc2ZjE5YjY5ZTdlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzA0MGU5MjQ2MThmNjRlMTU4Zjk3ZTFlZTRjOTE2ODJmLmJpbmRQb3B1cChwb3B1cF9lODc0MzEzNWNjYTk0MTNkYTliNDNmN2FhMTJhN2VjYSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iZTllZWE0N2UxMWI0ZTY1OWVhYTRjNzE4NGU3YjU1MyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc3ODUxNzUsLTc5LjM0NjU1NTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfM2NmNGViNWM1OGQ5NGJmNzk4NGIwMTdmYjEzODMwNTMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZmE0YjM3MjU4YmVlNGQ1YThiOWY3MDc2MDc3NjQyODQgPSAkKCc8ZGl2IGlkPSJodG1sX2ZhNGIzNzI1OGJlZTRkNWE4YjlmNzA3NjA3NzY0Mjg0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IZW5yeSBGYXJtLCBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zY2Y0ZWI1YzU4ZDk0YmY3OTg0YjAxN2ZiMTM4MzA1My5zZXRDb250ZW50KGh0bWxfZmE0YjM3MjU4YmVlNGQ1YThiOWY3MDc2MDc3NjQyODQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYmU5ZWVhNDdlMTFiNGU2NTllYWE0YzcxODRlN2I1NTMuYmluZFBvcHVwKHBvcHVwXzNjZjRlYjVjNThkOTRiZjc5ODRiMDE3ZmIxMzgzMDUzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzIxMzI4NzY1MTAyNDRkYTNhNTE5MmUzNjZlNmMwZDZiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzc4NTE3NSwtNzkuMzQ2NTU1N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85ZGNkMDMxYTFhNzU0OWViOTdkYmYyY2UyMTZlNThhYiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kMzUyZTY1ZDM5ZGU0Y2QyODg1NmUzYjRhNTg1MDA2NyA9ICQoJzxkaXYgaWQ9Imh0bWxfZDM1MmU2NWQzOWRlNGNkMjg4NTZlM2I0YTU4NTAwNjciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk9yaW9sZSwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOWRjZDAzMWExYTc1NDllYjk3ZGJmMmNlMjE2ZTU4YWIuc2V0Q29udGVudChodG1sX2QzNTJlNjVkMzlkZTRjZDI4ODU2ZTNiNGE1ODUwMDY3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzIxMzI4NzY1MTAyNDRkYTNhNTE5MmUzNjZlNmMwZDZiLmJpbmRQb3B1cChwb3B1cF85ZGNkMDMxYTFhNzU0OWViOTdkYmYyY2UyMTZlNThhYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lMzM4MDdmMTQzZjE0ZDVkYmU2ZDQzZTEyNDdkYzg3NCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc2Nzk4MDMsLTc5LjQ4NzI2MTkwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzA2NDQyY2I4MTkzODQ3ZTc5Nzc3MGE3OTZkM2NlNDQxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzE5ZDI2NGEwZTJjNDRmZmI4NmY4M2UyNGMzNjYwYmFjID0gJCgnPGRpdiBpZD0iaHRtbF8xOWQyNjRhMGUyYzQ0ZmZiODZmODNlMjRjMzY2MGJhYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Tm9ydGh3b29kIFBhcmssIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzA2NDQyY2I4MTkzODQ3ZTc5Nzc3MGE3OTZkM2NlNDQxLnNldENvbnRlbnQoaHRtbF8xOWQyNjRhMGUyYzQ0ZmZiODZmODNlMjRjMzY2MGJhYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lMzM4MDdmMTQzZjE0ZDVkYmU2ZDQzZTEyNDdkYzg3NC5iaW5kUG9wdXAocG9wdXBfMDY0NDJjYjgxOTM4NDdlNzk3NzcwYTc5NmQzY2U0NDEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDAzMDIzMmZjMDk3NGQ4MmI5OWQ2NTAxMDA3NGFkOTUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Njc5ODAzLC03OS40ODcyNjE5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82N2E3MjAxZDRiMWU0YmM1YWI3M2IxYTU4Zjk5ZDMxMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85YjA5ZDIzY2QxNjk0OTUzOGQ3NzFjN2JjM2I4ZWJkMSA9ICQoJzxkaXYgaWQ9Imh0bWxfOWIwOWQyM2NkMTY5NDk1MzhkNzcxYzdiYzNiOGViZDEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPllvcmsgVW5pdmVyc2l0eSwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjdhNzIwMWQ0YjFlNGJjNWFiNzNiMWE1OGY5OWQzMTIuc2V0Q29udGVudChodG1sXzliMDlkMjNjZDE2OTQ5NTM4ZDc3MWM3YmMzYjhlYmQxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzAwMzAyMzJmYzA5NzRkODJiOTlkNjUwMTAwNzRhZDk1LmJpbmRQb3B1cChwb3B1cF82N2E3MjAxZDRiMWU0YmM1YWI3M2IxYTU4Zjk5ZDMxMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85MzllOTA1ZjdlNTY0Y2VlYjAxODA3ZGMzMmI0MWJkZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY4NTM0NywtNzkuMzM4MTA2NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80MDNkODFhMTRkNzU0NTQ3ODNlMWIzOTY5ZDBhOWZjZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81MjM2NWE3ZWIxMmE0NDM0ODllYWRjYjMwZGZlOThkOCA9ICQoJzxkaXYgaWQ9Imh0bWxfNTIzNjVhN2ViMTJhNDQzNDg5ZWFkY2IzMGRmZTk4ZDgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkVhc3QgVG9yb250bywgRWFzdCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80MDNkODFhMTRkNzU0NTQ3ODNlMWIzOTY5ZDBhOWZjZS5zZXRDb250ZW50KGh0bWxfNTIzNjVhN2ViMTJhNDQzNDg5ZWFkY2IzMGRmZTk4ZDgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTM5ZTkwNWY3ZTU2NGNlZWIwMTgwN2RjMzJiNDFiZGYuYmluZFBvcHVwKHBvcHVwXzQwM2Q4MWExNGQ3NTQ1NDc4M2UxYjM5NjlkMGE5ZmNlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzVkM2M3MDk0NTY1OTQyM2RhYWMxZGY0ZWVkM2UwNDhmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQwODE1NywtNzkuMzgxNzUyMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTJkNWM1MjI1YmEwNGZkYzk0N2IyMDJlZDU2NDkxN2IgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWJlMWVhNmUxZWZmNDEzMzg5ZWU4OGRlNWY1ZjlmNDcgPSAkKCc8ZGl2IGlkPSJodG1sXzFiZTFlYTZlMWVmZjQxMzM4OWVlODhkZTVmNWY5ZjQ3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IYXJib3VyZnJvbnQgRWFzdCwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTJkNWM1MjI1YmEwNGZkYzk0N2IyMDJlZDU2NDkxN2Iuc2V0Q29udGVudChodG1sXzFiZTFlYTZlMWVmZjQxMzM4OWVlODhkZTVmNWY5ZjQ3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzVkM2M3MDk0NTY1OTQyM2RhYWMxZGY0ZWVkM2UwNDhmLmJpbmRQb3B1cChwb3B1cF8xMmQ1YzUyMjViYTA0ZmRjOTQ3YjIwMmVkNTY0OTE3Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81MWI1ZTVmMDE0NjY0NDZkODJkYTRhOTk0MzUxZWJiNyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0MDgxNTcsLTc5LjM4MTc1MjI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzEyYzZiNDFhMWVhNzQ3MjU4MDkxOTlkN2NlODFmNDRhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzRhMDc5ZTQ1NGM5NDQzNzFiZmRkYjU3NDZmMTQzYTIwID0gJCgnPGRpdiBpZD0iaHRtbF80YTA3OWU0NTRjOTQ0MzcxYmZkZGI1NzQ2ZjE0M2EyMCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VG9yb250byBJc2xhbmRzLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xMmM2YjQxYTFlYTc0NzI1ODA5MTk5ZDdjZTgxZjQ0YS5zZXRDb250ZW50KGh0bWxfNGEwNzllNDU0Yzk0NDM3MWJmZGRiNTc0NmYxNDNhMjApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNTFiNWU1ZjAxNDY2NDQ2ZDgyZGE0YTk5NDM1MWViYjcuYmluZFBvcHVwKHBvcHVwXzEyYzZiNDFhMWVhNzQ3MjU4MDkxOTlkN2NlODFmNDRhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzEwNDBlYjU5YmUzODQ2YjBhNmVlMGVlYjMyMzc0MDVjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQwODE1NywtNzkuMzgxNzUyMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTBkOWJhMTBmYWQ3NDczZGJmOTdlMGZkMmE1ZjcxMTcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDZkNGIzZjcwYTRiNDI0ZWEzOWIxNGFlYjIwZGY4YjggPSAkKCc8ZGl2IGlkPSJodG1sX2Q2ZDRiM2Y3MGE0YjQyNGVhMzliMTRhZWIyMGRmOGI4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5VbmlvbiBTdGF0aW9uLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85MGQ5YmExMGZhZDc0NzNkYmY5N2UwZmQyYTVmNzExNy5zZXRDb250ZW50KGh0bWxfZDZkNGIzZjcwYTRiNDI0ZWEzOWIxNGFlYjIwZGY4YjgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMTA0MGViNTliZTM4NDZiMGE2ZWUwZWViMzIzNzQwNWMuYmluZFBvcHVwKHBvcHVwXzkwZDliYTEwZmFkNzQ3M2RiZjk3ZTBmZDJhNWY3MTE3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2UxYTRiZjQ2Zjc1NzRmNTg5YWE4NDg0OTc5ZTdhODgwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ3OTI2NzAwMDAwMDA2LC03OS40MTk3NDk3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2RhNzk1NzVhMTNhNTQxMzNiN2ZiZjZjMmZlYjcwOThlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzVkNDBlZmJlY2UxMzQzZDBiYWI1NDViMGMyMDQxZGQ3ID0gJCgnPGRpdiBpZD0iaHRtbF81ZDQwZWZiZWNlMTM0M2QwYmFiNTQ1YjBjMjA0MWRkNyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGl0dGxlIFBvcnR1Z2FsLCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2RhNzk1NzVhMTNhNTQxMzNiN2ZiZjZjMmZlYjcwOThlLnNldENvbnRlbnQoaHRtbF81ZDQwZWZiZWNlMTM0M2QwYmFiNTQ1YjBjMjA0MWRkNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lMWE0YmY0NmY3NTc0ZjU4OWFhODQ4NDk3OWU3YTg4MC5iaW5kUG9wdXAocG9wdXBfZGE3OTU3NWExM2E1NDEzM2I3ZmJmNmMyZmViNzA5OGUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNDNmZTUzYWQwMzNhNGNhNzk0YzYxMTUxNDU4NTRkMzYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDc5MjY3MDAwMDAwMDYsLTc5LjQxOTc0OTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODY5MmZmNzlhNzZmNDA0Yzk5ODVhODNlZjc2N2IzYTggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDY4NGQwZjAyOGEzNGI4YThmZjM4NzE0NDJkMzVlMTYgPSAkKCc8ZGl2IGlkPSJodG1sXzQ2ODRkMGYwMjhhMzRiOGE4ZmYzODcxNDQyZDM1ZTE2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UcmluaXR5LCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzg2OTJmZjc5YTc2ZjQwNGM5OTg1YTgzZWY3NjdiM2E4LnNldENvbnRlbnQoaHRtbF80Njg0ZDBmMDI4YTM0YjhhOGZmMzg3MTQ0MmQzNWUxNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80M2ZlNTNhZDAzM2E0Y2E3OTRjNjExNTE0NTg1NGQzNi5iaW5kUG9wdXAocG9wdXBfODY5MmZmNzlhNzZmNDA0Yzk5ODVhODNlZjc2N2IzYTgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjcxYTI1ZTZjZDJiNDkzYjhjZTNkZTIzYTFjNmQ4OWIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Mjc5MjkyLC03OS4yNjIwMjk0MDAwMDAwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iM2Y4YmJiZWQ5MzQ0MDhlOTJjNGFhNTIzMDM1OTk5NiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yZjI5NjAyY2QwZWE0YTI4YTAyZTYzMjIzMGE0YWM0MiA9ICQoJzxkaXYgaWQ9Imh0bWxfMmYyOTYwMmNkMGVhNGEyOGEwMmU2MzIyMzBhNGFjNDIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkVhc3QgQmlyY2htb3VudCBQYXJrLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjNmOGJiYmVkOTM0NDA4ZTkyYzRhYTUyMzAzNTk5OTYuc2V0Q29udGVudChodG1sXzJmMjk2MDJjZDBlYTRhMjhhMDJlNjMyMjMwYTRhYzQyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Y3MWEyNWU2Y2QyYjQ5M2I4Y2UzZGUyM2ExYzZkODliLmJpbmRQb3B1cChwb3B1cF9iM2Y4YmJiZWQ5MzQ0MDhlOTJjNGFhNTIzMDM1OTk5Nik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wNzRkMDUwNWNhZWU0OGJiOTRiZmFlYzVjMTRkMGMzYSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcyNzkyOTIsLTc5LjI2MjAyOTQwMDAwMDAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2FkOGFmOTI5YjcwNzQ1MDBhNGE3ZTE5YTVjOThmYzQ1ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2M3ZTRjYmU3MGMxMTQ5NDc5OTFmOTA4YWY2MTRlMzEzID0gJCgnPGRpdiBpZD0iaHRtbF9jN2U0Y2JlNzBjMTE0OTQ3OTkxZjkwOGFmNjE0ZTMxMyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SW9udmlldywgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2FkOGFmOTI5YjcwNzQ1MDBhNGE3ZTE5YTVjOThmYzQ1LnNldENvbnRlbnQoaHRtbF9jN2U0Y2JlNzBjMTE0OTQ3OTkxZjkwOGFmNjE0ZTMxMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wNzRkMDUwNWNhZWU0OGJiOTRiZmFlYzVjMTRkMGMzYS5iaW5kUG9wdXAocG9wdXBfYWQ4YWY5MjliNzA3NDUwMGE0YTdlMTlhNWM5OGZjNDUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYjdjYmVmNmE3NWNkNGU1MDg2ZDdmOGYyYWY5YTVhYTYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Mjc5MjkyLC03OS4yNjIwMjk0MDAwMDAwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lNDZhNzg0YWQ0N2U0ZDg2OTRlZWRhMjY5MDgxNWU4MCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85MTRkYTBlZDhhZWY0MWRmODcyNDE3NDRkNjFkYzMwMyA9ICQoJzxkaXYgaWQ9Imh0bWxfOTE0ZGEwZWQ4YWVmNDFkZjg3MjQxNzQ0ZDYxZGMzMDMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPktlbm5lZHkgUGFyaywgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2U0NmE3ODRhZDQ3ZTRkODY5NGVlZGEyNjkwODE1ZTgwLnNldENvbnRlbnQoaHRtbF85MTRkYTBlZDhhZWY0MWRmODcyNDE3NDRkNjFkYzMwMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iN2NiZWY2YTc1Y2Q0ZTUwODZkN2Y4ZjJhZjlhNWFhNi5iaW5kUG9wdXAocG9wdXBfZTQ2YTc4NGFkNDdlNGQ4Njk0ZWVkYTI2OTA4MTVlODApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfN2YxM2M0MGI4NWQ3NDA3OGJiY2RkNGUxYzVmYmM0OGUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43ODY5NDczLC03OS4zODU5NzVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZGE0ZTk4ZTM0MDgzNGYzM2I4MDYxNjI0MTMzZTZhYjIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzAzNGY0NmFhMTdjNDBjNmFkM2EwMGFmNDA5MDc4NjUgPSAkKCc8ZGl2IGlkPSJodG1sXzMwMzRmNDZhYTE3YzQwYzZhZDNhMDBhZjQwOTA3ODY1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CYXl2aWV3IFZpbGxhZ2UsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2RhNGU5OGUzNDA4MzRmMzNiODA2MTYyNDEzM2U2YWIyLnNldENvbnRlbnQoaHRtbF8zMDM0ZjQ2YWExN2M0MGM2YWQzYTAwYWY0MDkwNzg2NSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83ZjEzYzQwYjg1ZDc0MDc4YmJjZGQ0ZTFjNWZiYzQ4ZS5iaW5kUG9wdXAocG9wdXBfZGE0ZTk4ZTM0MDgzNGYzM2I4MDYxNjI0MTMzZTZhYjIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMWFhNDcwMWZlOTlmNDFjOTg2NDdkNDlkOTkwNzJlZjkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Mzc0NzMyMDAwMDAwMDQsLTc5LjQ2NDc2MzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzAyY2M1ZDQ2NmZhZTRmN2U5ODEyOTEwNTU2N2E1NGE3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzIxYWJkYzZmOTg0ZDQxODVhYWM5OTBlNDk5NGVmZmMwID0gJCgnPGRpdiBpZD0iaHRtbF8yMWFiZGM2Zjk4NGQ0MTg1YWFjOTkwZTQ5OTRlZmZjMCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q0ZCIFRvcm9udG8sIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzAyY2M1ZDQ2NmZhZTRmN2U5ODEyOTEwNTU2N2E1NGE3LnNldENvbnRlbnQoaHRtbF8yMWFiZGM2Zjk4NGQ0MTg1YWFjOTkwZTQ5OTRlZmZjMCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xYWE0NzAxZmU5OWY0MWM5ODY0N2Q0OWQ5OTA3MmVmOS5iaW5kUG9wdXAocG9wdXBfMDJjYzVkNDY2ZmFlNGY3ZTk4MTI5MTA1NTY3YTU0YTcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYTdlMDI2ZjQyNTkzNGY3MGJlYTVmYjhhM2NlNmZlMTggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Mzc0NzMyMDAwMDAwMDQsLTc5LjQ2NDc2MzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2IyOTRlZmE5YThhZjRkMzM4NGJmYjdlYTUwMzhiNjUwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzA3ODFiOWU4MDI5YTQ0ZmFhOWRlNzlkNGI0MzExYTUzID0gJCgnPGRpdiBpZD0iaHRtbF8wNzgxYjllODAyOWE0NGZhYTlkZTc5ZDRiNDMxMWE1MyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RG93bnN2aWV3IEVhc3QsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2IyOTRlZmE5YThhZjRkMzM4NGJmYjdlYTUwMzhiNjUwLnNldENvbnRlbnQoaHRtbF8wNzgxYjllODAyOWE0NGZhYTlkZTc5ZDRiNDMxMWE1Myk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9hN2UwMjZmNDI1OTM0ZjcwYmVhNWZiOGEzY2U2ZmUxOC5iaW5kUG9wdXAocG9wdXBfYjI5NGVmYTlhOGFmNGQzMzg0YmZiN2VhNTAzOGI2NTApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZGYwM2Y4NTc1NTRkNGNlOWI3OTM3OThmNGU0YTQzZTMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Nzk1NTcxLC03OS4zNTIxODhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDljYWViMzZmODRmNDE5Njk0ODdmMDczYzFkNjAzNDQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjcwODNjNmUwZjk4NDJjYjk3ZjZhNDg5NGE5MDA1NmEgPSAkKCc8ZGl2IGlkPSJodG1sXzI3MDgzYzZlMGY5ODQyY2I5N2Y2YTQ4OTRhOTAwNTZhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgRGFuZm9ydGggV2VzdCwgRWFzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80OWNhZWIzNmY4NGY0MTk2OTQ4N2YwNzNjMWQ2MDM0NC5zZXRDb250ZW50KGh0bWxfMjcwODNjNmUwZjk4NDJjYjk3ZjZhNDg5NGE5MDA1NmEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZGYwM2Y4NTc1NTRkNGNlOWI3OTM3OThmNGU0YTQzZTMuYmluZFBvcHVwKHBvcHVwXzQ5Y2FlYjM2Zjg0ZjQxOTY5NDg3ZjA3M2MxZDYwMzQ0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU3MjBlYmRmYzc0OTRhNTY4YmIyZTNjNDI5ODQ1NmM3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjc5NTU3MSwtNzkuMzUyMTg4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzQxNzYwMzUzYzEzZDQ4M2U5ZWVlODk2YWY0NzQ4OTcxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzc3ZmFkYWVjZGExYTQ2ZGQ4MzY4OTBiMjI0YjMzNGM3ID0gJCgnPGRpdiBpZD0iaHRtbF83N2ZhZGFlY2RhMWE0NmRkODM2ODkwYjIyNGIzMzRjNyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Uml2ZXJkYWxlLCBFYXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQxNzYwMzUzYzEzZDQ4M2U5ZWVlODk2YWY0NzQ4OTcxLnNldENvbnRlbnQoaHRtbF83N2ZhZGFlY2RhMWE0NmRkODM2ODkwYjIyNGIzMzRjNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81NzIwZWJkZmM3NDk0YTU2OGJiMmUzYzQyOTg0NTZjNy5iaW5kUG9wdXAocG9wdXBfNDE3NjAzNTNjMTNkNDgzZTllZWU4OTZhZjQ3NDg5NzEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDc2NzMzMTUwNzc4NDk5ZTgyZDI0YzhhOWFiYTFjZGQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDcxNzY4LC03OS4zODE1NzY0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xYTNlMGQxNjdlMjc0OGNiYTViMmVjMGFlN2Y5NTI4ZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85NDRhZWRlMWRhMWE0NjYzOTU1ZjllNzE4ZTMwNDczOSA9ICQoJzxkaXYgaWQ9Imh0bWxfOTQ0YWVkZTFkYTFhNDY2Mzk1NWY5ZTcxOGUzMDQ3MzkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRlc2lnbiBFeGNoYW5nZSwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMWEzZTBkMTY3ZTI3NDhjYmE1YjJlYzBhZTdmOTUyOGUuc2V0Q29udGVudChodG1sXzk0NGFlZGUxZGExYTQ2NjM5NTVmOWU3MThlMzA0NzM5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzA3NjczMzE1MDc3ODQ5OWU4MmQyNGM4YTlhYmExY2RkLmJpbmRQb3B1cChwb3B1cF8xYTNlMGQxNjdlMjc0OGNiYTViMmVjMGFlN2Y5NTI4ZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lZTAyOTdmNDU4NDA0OWNiYTVlMmFlMjJkYTJjMzliMCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NzE3NjgsLTc5LjM4MTU3NjQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2I2NjA2MjdhZTAyMDQ1ODJhODgwOWZiNmFhZTQyYzQyID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzQ4NWNiZGJmYTYyNzQwNzA5MWRmNWQ1MjQxYmQyMjFkID0gJCgnPGRpdiBpZD0iaHRtbF80ODVjYmRiZmE2Mjc0MDcwOTFkZjVkNTI0MWJkMjIxZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VG9yb250byBEb21pbmlvbiBDZW50cmUsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2I2NjA2MjdhZTAyMDQ1ODJhODgwOWZiNmFhZTQyYzQyLnNldENvbnRlbnQoaHRtbF80ODVjYmRiZmE2Mjc0MDcwOTFkZjVkNTI0MWJkMjIxZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lZTAyOTdmNDU4NDA0OWNiYTVlMmFlMjJkYTJjMzliMC5iaW5kUG9wdXAocG9wdXBfYjY2MDYyN2FlMDIwNDU4MmE4ODA5ZmI2YWFlNDJjNDIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzk1MTdhZWZhMGM3NDAwNDkzY2QyMWM5MDc5YWY0NDQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42MzY4NDcyLC03OS40MjgxOTE0MDAwMDAwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81N2U0ZDY3MTdkYzA0ZWE1OGNhMmUwNTk0NjEyOGQ0NiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lYTc3Nzc0MzI1MTg0ZWMxODkyYmM0MDhhZjRiYmQ5NyA9ICQoJzxkaXYgaWQ9Imh0bWxfZWE3Nzc3NDMyNTE4NGVjMTg5MmJjNDA4YWY0YmJkOTciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJyb2NrdG9uLCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzU3ZTRkNjcxN2RjMDRlYTU4Y2EyZTA1OTQ2MTI4ZDQ2LnNldENvbnRlbnQoaHRtbF9lYTc3Nzc0MzI1MTg0ZWMxODkyYmM0MDhhZjRiYmQ5Nyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83OTUxN2FlZmEwYzc0MDA0OTNjZDIxYzkwNzlhZjQ0NC5iaW5kUG9wdXAocG9wdXBfNTdlNGQ2NzE3ZGMwNGVhNThjYTJlMDU5NDYxMjhkNDYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzVlOWM5MDljOTJhNDk2OTliY2RhOTA3MmY2ZTAxNTUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42MzY4NDcyLC03OS40MjgxOTE0MDAwMDAwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83NmRhMDMyOGYyMjc0MDk4OWFlM2Y5ZGViODYxYmVmNyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83NjQ4OTYxNjk5OTg0ZWIzODY5ZTU3MDExY2Y0YzNiZiA9ICQoJzxkaXYgaWQ9Imh0bWxfNzY0ODk2MTY5OTk4NGViMzg2OWU1NzAxMWNmNGMzYmYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkV4aGliaXRpb24gUGxhY2UsIFdlc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzZkYTAzMjhmMjI3NDA5ODlhZTNmOWRlYjg2MWJlZjcuc2V0Q29udGVudChodG1sXzc2NDg5NjE2OTk5ODRlYjM4NjllNTcwMTFjZjRjM2JmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzc1ZTljOTA5YzkyYTQ5Njk5YmNkYTkwNzJmNmUwMTU1LmJpbmRQb3B1cChwb3B1cF83NmRhMDMyOGYyMjc0MDk4OWFlM2Y5ZGViODYxYmVmNyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kMDk4OWYzZmU0YjU0Zjk5OWM2MDFkZGQyMDIwMTVlYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYzNjg0NzIsLTc5LjQyODE5MTQwMDAwMDAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzExODhlYjVlYzAxODQ1ZDE5MDIwNjUzMmI3MjQzYTBlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzdmNDBmOTczNjZmZTQ0YTI4MTNiMTRmZTQ1YjYwYzVhID0gJCgnPGRpdiBpZD0iaHRtbF83ZjQwZjk3MzY2ZmU0NGEyODEzYjE0ZmU0NWI2MGM1YSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UGFya2RhbGUgVmlsbGFnZSwgV2VzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xMTg4ZWI1ZWMwMTg0NWQxOTAyMDY1MzJiNzI0M2EwZS5zZXRDb250ZW50KGh0bWxfN2Y0MGY5NzM2NmZlNDRhMjgxM2IxNGZlNDViNjBjNWEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZDA5ODlmM2ZlNGI1NGY5OTljNjAxZGRkMjAyMDE1ZWIuYmluZFBvcHVwKHBvcHVwXzExODhlYjVlYzAxODQ1ZDE5MDIwNjUzMmI3MjQzYTBlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2ZkNTZjN2FlNDU1ODRkMzA5MGVlZmJlYWU1NmMxYjVjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzExMTExNzAwMDAwMDA0LC03OS4yODQ1NzcyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzJjY2ZjZWY0YjJjYzQ0MTc4NzQxYTA0OWQ3NzYyMmJmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2YyZmRkMjVhZDU0MTRlYTU4MTQ1OTdkYTA4YTNhOGIyID0gJCgnPGRpdiBpZD0iaHRtbF9mMmZkZDI1YWQ1NDE0ZWE1ODE0NTk3ZGEwOGEzYThiMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2xhaXJsZWEsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yY2NmY2VmNGIyY2M0NDE3ODc0MWEwNDlkNzc2MjJiZi5zZXRDb250ZW50KGh0bWxfZjJmZGQyNWFkNTQxNGVhNTgxNDU5N2RhMDhhM2E4YjIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZmQ1NmM3YWU0NTU4NGQzMDkwZWVmYmVhZTU2YzFiNWMuYmluZFBvcHVwKHBvcHVwXzJjY2ZjZWY0YjJjYzQ0MTc4NzQxYTA0OWQ3NzYyMmJmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzJjNzYwM2Y5ODAwNTRhNGNiMDNjN2NjODJkOGQ0Y2E0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzExMTExNzAwMDAwMDA0LC03OS4yODQ1NzcyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzkzMDQ4NGI0ZTQ5MjQwZmRiN2RhMDI5NGRjNzJlYTYwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2UxMmExM2Y3OGFmMDQwOWM5MTBlOTdmMDQyODAwZWQxID0gJCgnPGRpdiBpZD0iaHRtbF9lMTJhMTNmNzhhZjA0MDljOTEwZTk3ZjA0MjgwMGVkMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+R29sZGVuIE1pbGUsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85MzA0ODRiNGU0OTI0MGZkYjdkYTAyOTRkYzcyZWE2MC5zZXRDb250ZW50KGh0bWxfZTEyYTEzZjc4YWYwNDA5YzkxMGU5N2YwNDI4MDBlZDEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMmM3NjAzZjk4MDA1NGE0Y2IwM2M3Y2M4MmQ4ZDRjYTQuYmluZFBvcHVwKHBvcHVwXzkzMDQ4NGI0ZTQ5MjQwZmRiN2RhMDI5NGRjNzJlYTYwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzM3YmQ0YTgyNTEzMjRkNzg5NDBiODY2ZjlhZDlhMDNiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzExMTExNzAwMDAwMDA0LC03OS4yODQ1NzcyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzNmM2YyODAyZmU3OTRiMjE5NDQwYjIyZmQyZGRkNzdlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzdjYTU4M2FlZWVlZTRkNzFhNzU3MTJiMjk0ZTcxYzgyID0gJCgnPGRpdiBpZD0iaHRtbF83Y2E1ODNhZWVlZWU0ZDcxYTc1NzEyYjI5NGU3MWM4MiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+T2FrcmlkZ2UsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zZjNmMjgwMmZlNzk0YjIxOTQ0MGIyMmZkMmRkZDc3ZS5zZXRDb250ZW50KGh0bWxfN2NhNTgzYWVlZWVlNGQ3MWE3NTcxMmIyOTRlNzFjODIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMzdiZDRhODI1MTMyNGQ3ODk0MGI4NjZmOWFkOWEwM2IuYmluZFBvcHVwKHBvcHVwXzNmM2YyODAyZmU3OTRiMjE5NDQwYjIyZmQyZGRkNzdlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzhjOGFjNGIwMzZhZDQ2ZjQ5MWQzOWE4ZjRkNjdlYjgzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzU3NDkwMiwtNzkuMzc0NzE0MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmRmNmM4OTM0YTkxNDg0ZmFkOGM4ZmJjYTMzNmExYTEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMGNhODRhODk3NGE1NDZiMWJiYTRkYzQxMWZiZTlmZWIgPSAkKCc8ZGl2IGlkPSJodG1sXzBjYTg0YTg5NzRhNTQ2YjFiYmE0ZGM0MTFmYmU5ZmViIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TaWx2ZXIgSGlsbHMsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzZkZjZjODkzNGE5MTQ4NGZhZDhjOGZiY2EzMzZhMWExLnNldENvbnRlbnQoaHRtbF8wY2E4NGE4OTc0YTU0NmIxYmJhNGRjNDExZmJlOWZlYik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84YzhhYzRiMDM2YWQ0NmY0OTFkMzlhOGY0ZDY3ZWI4My5iaW5kUG9wdXAocG9wdXBfNmRmNmM4OTM0YTkxNDg0ZmFkOGM4ZmJjYTMzNmExYTEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjE0YzU4YTlhY2Y3NDAyOWIxN2M3YzQ1NTliMjViM2EgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43NTc0OTAyLC03OS4zNzQ3MTQwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hYjNjYmZhOTVmMmU0NjhjOGMyZWVlYTQyNGM2YTJmMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83YTI2OWVlYzA0OGE0YjFlOGRhOWEyOTRjMTFkMDlhMyA9ICQoJzxkaXYgaWQ9Imh0bWxfN2EyNjllZWMwNDhhNGIxZThkYTlhMjk0YzExZDA5YTMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPllvcmsgTWlsbHMsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2FiM2NiZmE5NWYyZTQ2OGM4YzJlZWVhNDI0YzZhMmYzLnNldENvbnRlbnQoaHRtbF83YTI2OWVlYzA0OGE0YjFlOGRhOWEyOTRjMTFkMDlhMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yMTRjNThhOWFjZjc0MDI5YjE3YzdjNDU1OWIyNWIzYS5iaW5kUG9wdXAocG9wdXBfYWIzY2JmYTk1ZjJlNDY4YzhjMmVlZWE0MjRjNmEyZjMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYjEzOGY2NTM0MTdlNGE4M2JjYmExZDM1ZjI5ZTVjOWQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MzkwMTQ2LC03OS41MDY5NDM2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzc4N2NmZjU0ZDE0NDRkZmI4NDMzZWNiNmNlNzBjNzk3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzEzNmQ3OWQzZTVhNzQ4OWRiNjA2OWMyOTMxZjNiYmEyID0gJCgnPGRpdiBpZD0iaHRtbF8xMzZkNzlkM2U1YTc0ODlkYjYwNjljMjkzMWYzYmJhMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RG93bnN2aWV3IFdlc3QsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzc4N2NmZjU0ZDE0NDRkZmI4NDMzZWNiNmNlNzBjNzk3LnNldENvbnRlbnQoaHRtbF8xMzZkNzlkM2U1YTc0ODlkYjYwNjljMjkzMWYzYmJhMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iMTM4ZjY1MzQxN2U0YTgzYmNiYTFkMzVmMjllNWM5ZC5iaW5kUG9wdXAocG9wdXBfNzg3Y2ZmNTRkMTQ0NGRmYjg0MzNlY2I2Y2U3MGM3OTcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYjUyYzdhMDEwNDE1NDU4Njg5MDJmMjI5OGZkZDg1NjIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Njg5OTg1LC03OS4zMTU1NzE1OTk5OTk5OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84N2UyYmI4YzA2NDk0MWIwYjI5M2Y3NTViMmM3ZGVlOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wZGUzZjc4OTgxMWY0MDExYTRhYzkyYjc1NDdiYzY1YiA9ICQoJzxkaXYgaWQ9Imh0bWxfMGRlM2Y3ODk4MTFmNDAxMWE0YWM5MmI3NTQ3YmM2NWIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRoZSBCZWFjaGVzIFdlc3QsIEVhc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODdlMmJiOGMwNjQ5NDFiMGIyOTNmNzU1YjJjN2RlZTkuc2V0Q29udGVudChodG1sXzBkZTNmNzg5ODExZjQwMTFhNGFjOTJiNzU0N2JjNjViKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2I1MmM3YTAxMDQxNTQ1ODY4OTAyZjIyOThmZGQ4NTYyLmJpbmRQb3B1cChwb3B1cF84N2UyYmI4YzA2NDk0MWIwYjI5M2Y3NTViMmM3ZGVlOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83YzFiMzA4YThiMjU0YWY3OTlhZTQ3NDY4MjBkMTM3OSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2ODk5ODUsLTc5LjMxNTU3MTU5OTk5OTk4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2E3YmQ0OGZhNGI4MjQzNTA4YzgwYTM2NzQ3ZGY3YzRkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzBjNjFjMjMwNTg3MzQyZGViMmQyY2NkMTA1MTkzNWM0ID0gJCgnPGRpdiBpZD0iaHRtbF8wYzYxYzIzMDU4NzM0MmRlYjJkMmNjZDEwNTE5MzVjNCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SW5kaWEgQmF6YWFyLCBFYXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2E3YmQ0OGZhNGI4MjQzNTA4YzgwYTM2NzQ3ZGY3YzRkLnNldENvbnRlbnQoaHRtbF8wYzYxYzIzMDU4NzM0MmRlYjJkMmNjZDEwNTE5MzVjNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83YzFiMzA4YThiMjU0YWY3OTlhZTQ3NDY4MjBkMTM3OS5iaW5kUG9wdXAocG9wdXBfYTdiZDQ4ZmE0YjgyNDM1MDhjODBhMzY3NDdkZjdjNGQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNDM0MDhhNGNlZGVhNDEwN2I1YTU1N2E4MzQ5MjBiMmQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDgxOTg1LC03OS4zNzk4MTY5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83NmVjMmM5NDQ3MGE0ZjY5OGMwMDc0OTk2YzJlODlhYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84YjM1YjhjY2U5ZjE0NDc3YWZhNGNjYmQzMTc0ZDUwOSA9ICQoJzxkaXYgaWQ9Imh0bWxfOGIzNWI4Y2NlOWYxNDQ3N2FmYTRjY2JkMzE3NGQ1MDkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNvbW1lcmNlIENvdXJ0LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83NmVjMmM5NDQ3MGE0ZjY5OGMwMDc0OTk2YzJlODlhYy5zZXRDb250ZW50KGh0bWxfOGIzNWI4Y2NlOWYxNDQ3N2FmYTRjY2JkMzE3NGQ1MDkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNDM0MDhhNGNlZGVhNDEwN2I1YTU1N2E4MzQ5MjBiMmQuYmluZFBvcHVwKHBvcHVwXzc2ZWMyYzk0NDcwYTRmNjk4YzAwNzQ5OTZjMmU4OWFjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2M3NjFjNTYzOTViZjRmOGVhNTBjMzA5ZGRkMDkwNjFmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ4MTk4NSwtNzkuMzc5ODE2OTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzA4MjcyNTYzZTA1NGRkMGE3NWFiYTllZDU4NjViYmEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOTU0ZjE0MjVhNDBlNDJmZmE1ZWU5MWUwYzZiMzc5M2QgPSAkKCc8ZGl2IGlkPSJodG1sXzk1NGYxNDI1YTQwZTQyZmZhNWVlOTFlMGM2YjM3OTNkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5WaWN0b3JpYSBIb3RlbCwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzA4MjcyNTYzZTA1NGRkMGE3NWFiYTllZDU4NjViYmEuc2V0Q29udGVudChodG1sXzk1NGYxNDI1YTQwZTQyZmZhNWVlOTFlMGM2YjM3OTNkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2M3NjFjNTYzOTViZjRmOGVhNTBjMzA5ZGRkMDkwNjFmLmJpbmRQb3B1cChwb3B1cF83MDgyNzI1NjNlMDU0ZGQwYTc1YWJhOWVkNTg2NWJiYSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82MGFjOTAyZTNhNTQ0MjIyYjBjMTMzNGRkNTAxMGYxZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxMzc1NjIwMDAwMDAwNiwtNzkuNDkwMDczOF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85OTVmZmQ1OGZjMmY0OWYyOWVlOTRkNzFiZDZlMTNhMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84ZjI4ZTZkOGUzZjU0YzgxYjRkNDBiMjUxYmZmMmY0OSA9ICQoJzxkaXYgaWQ9Imh0bWxfOGYyOGU2ZDhlM2Y1NGM4MWI0ZDQwYjI1MWJmZjJmNDkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRvd25zdmlldywgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTk1ZmZkNThmYzJmNDlmMjllZTk0ZDcxYmQ2ZTEzYTMuc2V0Q29udGVudChodG1sXzhmMjhlNmQ4ZTNmNTRjODFiNGQ0MGIyNTFiZmYyZjQ5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzYwYWM5MDJlM2E1NDQyMjJiMGMxMzM0ZGQ1MDEwZjFmLmJpbmRQb3B1cChwb3B1cF85OTVmZmQ1OGZjMmY0OWYyOWVlOTRkNzFiZDZlMTNhMyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85YWNhYmY4NjQ5ZjM0YTVmYTJhYjk5NDU0MzFmNTMxOSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxMzc1NjIwMDAwMDAwNiwtNzkuNDkwMDczOF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hMzFjZTZiOWY5MzY0Njk5YTY0MTNjZjQyZTFiZGVmNyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83NmEyNDFjZmZlODA0ZTZjOWM2MDk4ZDYxM2YwNGY0MiA9ICQoJzxkaXYgaWQ9Imh0bWxfNzZhMjQxY2ZmZTgwNGU2YzljNjA5OGQ2MTNmMDRmNDIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5vcnRoIFBhcmssIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2EzMWNlNmI5ZjkzNjQ2OTlhNjQxM2NmNDJlMWJkZWY3LnNldENvbnRlbnQoaHRtbF83NmEyNDFjZmZlODA0ZTZjOWM2MDk4ZDYxM2YwNGY0Mik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85YWNhYmY4NjQ5ZjM0YTVmYTJhYjk5NDU0MzFmNTMxOS5iaW5kUG9wdXAocG9wdXBfYTMxY2U2YjlmOTM2NDY5OWE2NDEzY2Y0MmUxYmRlZjcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDBiMjNhM2Q5OTgxNDlkMGJhYzY2MTRjODhiMzE4N2IgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTM3NTYyMDAwMDAwMDYsLTc5LjQ5MDA3MzhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYmY2NTA3MzEwMzU5NGI4MzhhOGQzNGM5NDRlMWE2NmIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOTcxMzUyNzRhNWM4NDdhYWJlOTg4ZTY5MDkxZWNiZTUgPSAkKCc8ZGl2IGlkPSJodG1sXzk3MTM1Mjc0YTVjODQ3YWFiZTk4OGU2OTA5MWVjYmU1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5VcHdvb2QgUGFyaywgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYmY2NTA3MzEwMzU5NGI4MzhhOGQzNGM5NDRlMWE2NmIuc2V0Q29udGVudChodG1sXzk3MTM1Mjc0YTVjODQ3YWFiZTk4OGU2OTA5MWVjYmU1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2QwYjIzYTNkOTk4MTQ5ZDBiYWM2NjE0Yzg4YjMxODdiLmJpbmRQb3B1cChwb3B1cF9iZjY1MDczMTAzNTk0YjgzOGE4ZDM0Yzk0NGUxYTY2Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8xYzBlYzlhZTg4OTE0NWE4YTBiNGUyNDdkOTRjY2E4ZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc1NjMwMzMsLTc5LjU2NTk2MzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzNmYTNjNDE2YzQ1OTRkN2U4MTc0ODc4NWU2ZWIyNDc4ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzc3ODJiMmY0YTIzNzQ2MzFiNDJlZWQ4MTRiZDk0NDk3ID0gJCgnPGRpdiBpZD0iaHRtbF83NzgyYjJmNGEyMzc0NjMxYjQyZWVkODE0YmQ5NDQ5NyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SHVtYmVyIFN1bW1pdCwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfM2ZhM2M0MTZjNDU5NGQ3ZTgxNzQ4Nzg1ZTZlYjI0Nzguc2V0Q29udGVudChodG1sXzc3ODJiMmY0YTIzNzQ2MzFiNDJlZWQ4MTRiZDk0NDk3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzFjMGVjOWFlODg5MTQ1YThhMGI0ZTI0N2Q5NGNjYThmLmJpbmRQb3B1cChwb3B1cF8zZmEzYzQxNmM0NTk0ZDdlODE3NDg3ODVlNmViMjQ3OCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lZmY2MDlhMmVkYWY0MGE2OTFhZTRlN2E2YmUwNjdjYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxNjMxNiwtNzkuMjM5NDc2MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOGYyZDExNTVlOWZkNDg1NTk4YjU5MzNiNzU0NGJkNzIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZjAwNzA2YWRmOTdhNGJiMmIwZmJmYWUwM2VhZjkxZTIgPSAkKCc8ZGl2IGlkPSJodG1sX2YwMDcwNmFkZjk3YTRiYjJiMGZiZmFlMDNlYWY5MWUyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DbGlmZmNyZXN0LCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOGYyZDExNTVlOWZkNDg1NTk4YjU5MzNiNzU0NGJkNzIuc2V0Q29udGVudChodG1sX2YwMDcwNmFkZjk3YTRiYjJiMGZiZmFlMDNlYWY5MWUyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2VmZjYwOWEyZWRhZjQwYTY5MWFlNGU3YTZiZTA2N2NjLmJpbmRQb3B1cChwb3B1cF84ZjJkMTE1NWU5ZmQ0ODU1OThiNTkzM2I3NTQ0YmQ3Mik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jNmJmNjI1N2IwYWY0YTUwYTdhMGJlNmZlZjFjYThkZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxNjMxNiwtNzkuMjM5NDc2MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYmY3ZmVkZTE2YTU0NDc2MGFiODg2ZmYyMDEwMjRjZGIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDA5MjYzMTcyMjlkNDc1ZjkwOWI1YTcwMTk5YjI3MzcgPSAkKCc8ZGl2IGlkPSJodG1sXzQwOTI2MzE3MjI5ZDQ3NWY5MDliNWE3MDE5OWIyNzM3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DbGlmZnNpZGUsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iZjdmZWRlMTZhNTQ0NzYwYWI4ODZmZjIwMTAyNGNkYi5zZXRDb250ZW50KGh0bWxfNDA5MjYzMTcyMjlkNDc1ZjkwOWI1YTcwMTk5YjI3MzcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzZiZjYyNTdiMGFmNGE1MGE3YTBiZTZmZWYxY2E4ZGQuYmluZFBvcHVwKHBvcHVwX2JmN2ZlZGUxNmE1NDQ3NjBhYjg4NmZmMjAxMDI0Y2RiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU2NjE0N2U4M2JjMTRmMWJiZGMyYjgxNzk4ZDAxYjc1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzE2MzE2LC03OS4yMzk0NzYwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wMjEwYTU2NWQ0ZDM0NWQ3YTAxZDFlMmIwZTFlZWIzYiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80Y2NlNGNhYzIyMTM0Y2IzYWY4Y2RmYWNiYzdlYTY1MCA9ICQoJzxkaXYgaWQ9Imh0bWxfNGNjZTRjYWMyMjEzNGNiM2FmOGNkZmFjYmM3ZWE2NTAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlNjYXJib3JvdWdoIFZpbGxhZ2UgV2VzdCwgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzAyMTBhNTY1ZDRkMzQ1ZDdhMDFkMWUyYjBlMWVlYjNiLnNldENvbnRlbnQoaHRtbF80Y2NlNGNhYzIyMTM0Y2IzYWY4Y2RmYWNiYzdlYTY1MCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81NjYxNDdlODNiYzE0ZjFiYmRjMmI4MTc5OGQwMWI3NS5iaW5kUG9wdXAocG9wdXBfMDIxMGE1NjVkNGQzNDVkN2EwMWQxZTJiMGUxZWViM2IpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTk0Y2VhZDhkYmMwNDZkMDk3M2VhOTdjZTRjNjRjMzIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43ODkwNTMsLTc5LjQwODQ5Mjc5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzUxMDdhYzNjNTUzZjRlNTA5ZTNjZjEyZmUzYzAzZmM1ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzgyYzc1ZTdlMTdkMTQ4MGM5OTNmN2U1NmI3OWM0ZTY4ID0gJCgnPGRpdiBpZD0iaHRtbF84MmM3NWU3ZTE3ZDE0ODBjOTkzZjdlNTZiNzljNGU2OCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TmV3dG9uYnJvb2ssIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzUxMDdhYzNjNTUzZjRlNTA5ZTNjZjEyZmUzYzAzZmM1LnNldENvbnRlbnQoaHRtbF84MmM3NWU3ZTE3ZDE0ODBjOTkzZjdlNTZiNzljNGU2OCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85OTRjZWFkOGRiYzA0NmQwOTczZWE5N2NlNGM2NGMzMi5iaW5kUG9wdXAocG9wdXBfNTEwN2FjM2M1NTNmNGU1MDllM2NmMTJmZTNjMDNmYzUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODEyZGYzNmI1MzhmNGU0NmEwZTU4MWYwNTRiNTY5ZDAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43ODkwNTMsLTc5LjQwODQ5Mjc5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzAyNTJiNWIxYjE0ODQ2MWE4MmNmNzVjZGMxZWIwYWRjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzY1NjQwM2JkMWNhOTQ3NWY4MDNmMTkzYjkxZGFkNTNhID0gJCgnPGRpdiBpZD0iaHRtbF82NTY0MDNiZDFjYTk0NzVmODAzZjE5M2I5MWRhZDUzYSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V2lsbG93ZGFsZSwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDI1MmI1YjFiMTQ4NDYxYTgyY2Y3NWNkYzFlYjBhZGMuc2V0Q29udGVudChodG1sXzY1NjQwM2JkMWNhOTQ3NWY4MDNmMTkzYjkxZGFkNTNhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzgxMmRmMzZiNTM4ZjRlNDZhMGU1ODFmMDU0YjU2OWQwLmJpbmRQb3B1cChwb3B1cF8wMjUyYjViMWIxNDg0NjFhODJjZjc1Y2RjMWViMGFkYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zYmM4YzQ2YmYxOWQ0YTNiYWY4YTQ4ZGFlYzNjMjdmNyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcyODQ5NjQsLTc5LjQ5NTY5NzQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Y5YWZjOTY2MWJkNTRjZTc4OTczYjdiZDVhNGFiZmIzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzIxZTQzYTdjOWUxNDQ1OTk5ZDQ5MmQwZGE0MTcwNTdhID0gJCgnPGRpdiBpZD0iaHRtbF8yMWU0M2E3YzllMTQ0NTk5OWQ0OTJkMGRhNDE3MDU3YSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RG93bnN2aWV3IENlbnRyYWwsIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Y5YWZjOTY2MWJkNTRjZTc4OTczYjdiZDVhNGFiZmIzLnNldENvbnRlbnQoaHRtbF8yMWU0M2E3YzllMTQ0NTk5OWQ0OTJkMGRhNDE3MDU3YSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zYmM4YzQ2YmYxOWQ0YTNiYWY4YTQ4ZGFlYzNjMjdmNy5iaW5kUG9wdXAocG9wdXBfZjlhZmM5NjYxYmQ1NGNlNzg5NzNiN2JkNWE0YWJmYjMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDBlZDliMzFlZGI4NDFmNTk4MTY1YjQ3MDVkZWVhZDEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTk1MjU1LC03OS4zNDA5MjNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTZhOWY2ZmJhNjEyNDQ2N2I3NmNjMjNhMzk2NTkyY2QgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTVhMGRhZjEyMDgxNGMxYWJlN2U3ODEzZWYxYjQ4NjAgPSAkKCc8ZGl2IGlkPSJodG1sXzE1YTBkYWYxMjA4MTRjMWFiZTdlNzgxM2VmMWI0ODYwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdHVkaW8gRGlzdHJpY3QsIEVhc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTZhOWY2ZmJhNjEyNDQ2N2I3NmNjMjNhMzk2NTkyY2Quc2V0Q29udGVudChodG1sXzE1YTBkYWYxMjA4MTRjMWFiZTdlNzgxM2VmMWI0ODYwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2QwZWQ5YjMxZWRiODQxZjU5ODE2NWI0NzA1ZGVlYWQxLmJpbmRQb3B1cChwb3B1cF85NmE5ZjZmYmE2MTI0NDY3Yjc2Y2MyM2EzOTY1OTJjZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wNGQyZGZmNDdkZGE0MmI4YWVmZTA3MGY4NDQ1YjMyOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjczMzI4MjUsLTc5LjQxOTc0OTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMjAxYzFmNzkzNzY3NDQ1M2FmZWVjNTgyNjNjNTBiMWMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzEwNTY3ODY0NzQ4NDc1OGEwMTAyMDYzNGRmMWVlN2UgPSAkKCc8ZGl2IGlkPSJodG1sXzcxMDU2Nzg2NDc0ODQ3NThhMDEwMjA2MzRkZjFlZTdlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CZWRmb3JkIFBhcmssIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzIwMWMxZjc5Mzc2NzQ0NTNhZmVlYzU4MjYzYzUwYjFjLnNldENvbnRlbnQoaHRtbF83MTA1Njc4NjQ3NDg0NzU4YTAxMDIwNjM0ZGYxZWU3ZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wNGQyZGZmNDdkZGE0MmI4YWVmZTA3MGY4NDQ1YjMyOC5iaW5kUG9wdXAocG9wdXBfMjAxYzFmNzkzNzY3NDQ1M2FmZWVjNTgyNjNjNTBiMWMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjQyZWE0YTc2NzYzNDUyYjgyNDM0NjY5YTEzZDYxOWEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MzMyODI1LC03OS40MTk3NDk3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzBhYWMyNzk4NjdlYzRmMTliZjNjY2EwNGI5YzRkMzI3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzBlNGVkZDZjOTk4ODQzMjFhYzgxZDAyMWYzZmQ0NjgzID0gJCgnPGRpdiBpZD0iaHRtbF8wZTRlZGQ2Yzk5ODg0MzIxYWM4MWQwMjFmM2ZkNDY4MyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF3cmVuY2UgTWFub3IgRWFzdCwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMGFhYzI3OTg2N2VjNGYxOWJmM2NjYTA0YjljNGQzMjcuc2V0Q29udGVudChodG1sXzBlNGVkZDZjOTk4ODQzMjFhYzgxZDAyMWYzZmQ0NjgzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzI0MmVhNGE3Njc2MzQ1MmI4MjQzNDY2OWExM2Q2MTlhLmJpbmRQb3B1cChwb3B1cF8wYWFjMjc5ODY3ZWM0ZjE5YmYzY2NhMDRiOWM0ZDMyNyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zNzJkN2MxNzIyZTU0MzFjYWEyNjAzNDYzYzRiMjBlYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY5MTExNTgsLTc5LjQ3NjAxMzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzc3MjdjMWRkNDBlYzQwMjVhOTc0ZGI5MWVlMzEwMjk3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzExNTkxYzRlNTYzYTRlNjZiMmUwYzk4OThjZTQ5MGM1ID0gJCgnPGRpdiBpZD0iaHRtbF8xMTU5MWM0ZTU2M2E0ZTY2YjJlMGM5ODk4Y2U0OTBjNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RGVsIFJheSwgWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzcyN2MxZGQ0MGVjNDAyNWE5NzRkYjkxZWUzMTAyOTcuc2V0Q29udGVudChodG1sXzExNTkxYzRlNTYzYTRlNjZiMmUwYzk4OThjZTQ5MGM1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzM3MmQ3YzE3MjJlNTQzMWNhYTI2MDM0NjNjNGIyMGVjLmJpbmRQb3B1cChwb3B1cF83NzI3YzFkZDQwZWM0MDI1YTk3NGRiOTFlZTMxMDI5Nyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kMjY1ZTZiOTdlYTk0YmI2ODdkOWNhODU2YzAxNzg2ZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY5MTExNTgsLTc5LjQ3NjAxMzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzgyZGU2ODg3YmFhMjRjMDY4Mzg0OTkxZmY3MDg2ZmMxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzNiNmFkMjJkMTEwYTQ3MzY4YmQ1NjNiYTFkZjAyNDM1ID0gJCgnPGRpdiBpZD0iaHRtbF8zYjZhZDIyZDExMGE0NzM2OGJkNTYzYmExZGYwMjQzNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+S2VlbGVzZGFsZSwgWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODJkZTY4ODdiYWEyNGMwNjgzODQ5OTFmZjcwODZmYzEuc2V0Q29udGVudChodG1sXzNiNmFkMjJkMTEwYTQ3MzY4YmQ1NjNiYTFkZjAyNDM1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2QyNjVlNmI5N2VhOTRiYjY4N2Q5Y2E4NTZjMDE3ODZkLmJpbmRQb3B1cChwb3B1cF84MmRlNjg4N2JhYTI0YzA2ODM4NDk5MWZmNzA4NmZjMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jNGI2ZWNiMTkyNjQ0YzZhOTVjM2Y4M2ExODg1NWNkNCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY5MTExNTgsLTc5LjQ3NjAxMzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzZiOWM2ZjljNTBkZTQyOThiZTY2OWY5MzJmZjA4NzIwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Y5MWY2NGZlMDYxYzRlMmQ4Y2ViMGIxN2QzNGM4Nzk5ID0gJCgnPGRpdiBpZD0iaHRtbF9mOTFmNjRmZTA2MWM0ZTJkOGNlYjBiMTdkMzRjODc5OSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TW91bnQgRGVubmlzLCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82YjljNmY5YzUwZGU0Mjk4YmU2NjlmOTMyZmYwODcyMC5zZXRDb250ZW50KGh0bWxfZjkxZjY0ZmUwNjFjNGUyZDhjZWIwYjE3ZDM0Yzg3OTkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzRiNmVjYjE5MjY0NGM2YTk1YzNmODNhMTg4NTVjZDQuYmluZFBvcHVwKHBvcHVwXzZiOWM2ZjljNTBkZTQyOThiZTY2OWY5MzJmZjA4NzIwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2UyM2E4ZGZjMmQ1ZjRiMWM5OWRmNzNlZmJmMTRmMDc2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjkxMTE1OCwtNzkuNDc2MDEzMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNTEyMWIxODJmODU3NDc3MWJkYWU1ZDE2YTIyOTVlM2MgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODlhOWI3YzQ4ODJkNGU1MWE4MjY2YjBiZWMwMjg3YjggPSAkKCc8ZGl2IGlkPSJodG1sXzg5YTliN2M0ODgyZDRlNTFhODI2NmIwYmVjMDI4N2I4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TaWx2ZXJ0aG9ybiwgWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNTEyMWIxODJmODU3NDc3MWJkYWU1ZDE2YTIyOTVlM2Muc2V0Q29udGVudChodG1sXzg5YTliN2M0ODgyZDRlNTFhODI2NmIwYmVjMDI4N2I4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2UyM2E4ZGZjMmQ1ZjRiMWM5OWRmNzNlZmJmMTRmMDc2LmJpbmRQb3B1cChwb3B1cF81MTIxYjE4MmY4NTc0NzcxYmRhZTVkMTZhMjI5NWUzYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jMWRhNWEyYjJmMmY0MDEzOTQ5ZjNlOWI3NGNjNmE4YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcyNDc2NTksLTc5LjUzMjI0MjQwMDAwMDAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzg0MmZiM2Q5NzkxZTQ5Yzc4N2E4ZmRmNTkyMDk2ZmQ2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzJmMTNmMDRkYWM0MjRiNzhhYjg2ODQwMjgwOGExZjZjID0gJCgnPGRpdiBpZD0iaHRtbF8yZjEzZjA0ZGFjNDI0Yjc4YWI4Njg0MDI4MDhhMWY2YyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RW1lcnksIE5vcnRoIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzg0MmZiM2Q5NzkxZTQ5Yzc4N2E4ZmRmNTkyMDk2ZmQ2LnNldENvbnRlbnQoaHRtbF8yZjEzZjA0ZGFjNDI0Yjc4YWI4Njg0MDI4MDhhMWY2Yyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jMWRhNWEyYjJmMmY0MDEzOTQ5ZjNlOWI3NGNjNmE4Yy5iaW5kUG9wdXAocG9wdXBfODQyZmIzZDk3OTFlNDljNzg3YThmZGY1OTIwOTZmZDYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYWY2MDc5NjM0NzY1NGExMGFhYWZlYmNjNDEyNDk3YWQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MjQ3NjU5LC03OS41MzIyNDI0MDAwMDAwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mMTYwODFlZDBiY2Y0YTc1OWZjOTNmNzQ1YjUxODZiOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80MzViNDcxY2ZjMmQ0NzNhYWI3ZjU4M2YxYzBmMjM4YiA9ICQoJzxkaXYgaWQ9Imh0bWxfNDM1YjQ3MWNmYzJkNDczYWFiN2Y1ODNmMWMwZjIzOGIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkh1bWJlcmxlYSwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZjE2MDgxZWQwYmNmNGE3NTlmYzkzZjc0NWI1MTg2Yjkuc2V0Q29udGVudChodG1sXzQzNWI0NzFjZmMyZDQ3M2FhYjdmNTgzZjFjMGYyMzhiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2FmNjA3OTYzNDc2NTRhMTBhYWFmZWJjYzQxMjQ5N2FkLmJpbmRQb3B1cChwb3B1cF9mMTYwODFlZDBiY2Y0YTc1OWZjOTNmNzQ1YjUxODZiOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zMDM4YTJmOWY5ZjI0ODY2YTczMDBlNjkyYzEzODYxZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY5MjY1NzAwMDAwMDAwNCwtNzkuMjY0ODQ4MV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jNzNlOWZlOTYwNzU0Mjk3ODQ2OTQwZmFlYmUxZTBkMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82NzZjZTk1NjQ5NDE0OGEwODlkYzJiZTRmNWE1ZTUyYSA9ICQoJzxkaXYgaWQ9Imh0bWxfNjc2Y2U5NTY0OTQxNDhhMDg5ZGMyYmU0ZjVhNWU1MmEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJpcmNoIENsaWZmLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzczZTlmZTk2MDc1NDI5Nzg0Njk0MGZhZWJlMWUwZDIuc2V0Q29udGVudChodG1sXzY3NmNlOTU2NDk0MTQ4YTA4OWRjMmJlNGY1YTVlNTJhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzMwMzhhMmY5ZjlmMjQ4NjZhNzMwMGU2OTJjMTM4NjFlLmJpbmRQb3B1cChwb3B1cF9jNzNlOWZlOTYwNzU0Mjk3ODQ2OTQwZmFlYmUxZTBkMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zNjhhZDU5MmRjYTQ0MmY3YTgxN2NjOWU5OTRlMDE4YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY5MjY1NzAwMDAwMDAwNCwtNzkuMjY0ODQ4MV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80Yjk5NzliYTBkMjM0ODRhODVjZDg0NWM5NmNiNjIzNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jMWRjNzEwMTE2MTU0YzFjYjkxMmEwOGY0ZmM1ZjMyMSA9ICQoJzxkaXYgaWQ9Imh0bWxfYzFkYzcxMDExNjE1NGMxY2I5MTJhMDhmNGZjNWYzMjEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNsaWZmc2lkZSBXZXN0LCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNGI5OTc5YmEwZDIzNDg0YTg1Y2Q4NDVjOTZjYjYyMzUuc2V0Q29udGVudChodG1sX2MxZGM3MTAxMTYxNTRjMWNiOTEyYTA4ZjRmYzVmMzIxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzM2OGFkNTkyZGNhNDQyZjdhODE3Y2M5ZTk5NGUwMThjLmJpbmRQb3B1cChwb3B1cF80Yjk5NzliYTBkMjM0ODRhODVjZDg0NWM5NmNiNjIzNSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hOWQ2NGM0MTI1YjU0ODYwOGJhMmI5MTAxZjgwMTFmOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc3MDExOTksLTc5LjQwODQ5Mjc5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzI2YTllYTE1MDljMzQwMzk4ZDI4NjQ5NGE0NzQxMmI0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzhlYmJlMDM4OGFmNTRlODRhYjkzM2RiMjgzYjM5ODc3ID0gJCgnPGRpdiBpZD0iaHRtbF84ZWJiZTAzODhhZjU0ZTg0YWI5MzNkYjI4M2IzOTg3NyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V2lsbG93ZGFsZSBTb3V0aCwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjZhOWVhMTUwOWMzNDAzOThkMjg2NDk0YTQ3NDEyYjQuc2V0Q29udGVudChodG1sXzhlYmJlMDM4OGFmNTRlODRhYjkzM2RiMjgzYjM5ODc3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2E5ZDY0YzQxMjViNTQ4NjA4YmEyYjkxMDFmODAxMWY4LmJpbmRQb3B1cChwb3B1cF8yNmE5ZWExNTA5YzM0MDM5OGQyODY0OTRhNDc0MTJiNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hYjE1YTI3NjJlMDU0NDUzOGI1ZmNiZjdkNWMzOTJjZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc2MTYzMTMsLTc5LjUyMDk5OTQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2ViNGFmMGJiNWJkZDRmZGE5N2ZlNzVmMzA5Nzg4ZDExID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2M1MWM1ZTY0MDM0NzRmNDlhYzgyOGQzMjI0YTc4MjhjID0gJCgnPGRpdiBpZD0iaHRtbF9jNTFjNWU2NDAzNDc0ZjQ5YWM4MjhkMzIyNGE3ODI4YyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RG93bnN2aWV3IE5vcnRod2VzdCwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZWI0YWYwYmI1YmRkNGZkYTk3ZmU3NWYzMDk3ODhkMTEuc2V0Q29udGVudChodG1sX2M1MWM1ZTY0MDM0NzRmNDlhYzgyOGQzMjI0YTc4MjhjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2FiMTVhMjc2MmUwNTQ0NTM4YjVmY2JmN2Q1YzM5MmNlLmJpbmRQb3B1cChwb3B1cF9lYjRhZjBiYjViZGQ0ZmRhOTdmZTc1ZjMwOTc4OGQxMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kMGE1ZTBiOGYyMWM0YTViYjkwM2Y0ZTgzNTAyODY4OCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcyODAyMDUsLTc5LjM4ODc5MDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDRiODc5NzFiNGRiNGY5NDk3MTYyOGVkNTIzMzdlYjggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTEwM2Q2ZWY3MjBlNDg3MGE2MjYzNDdiYmE1MzYyOTggPSAkKCc8ZGl2IGlkPSJodG1sXzExMDNkNmVmNzIwZTQ4NzBhNjI2MzQ3YmJhNTM2Mjk4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MYXdyZW5jZSBQYXJrLCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzA0Yjg3OTcxYjRkYjRmOTQ5NzE2MjhlZDUyMzM3ZWI4LnNldENvbnRlbnQoaHRtbF8xMTAzZDZlZjcyMGU0ODcwYTYyNjM0N2JiYTUzNjI5OCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kMGE1ZTBiOGYyMWM0YTViYjkwM2Y0ZTgzNTAyODY4OC5iaW5kUG9wdXAocG9wdXBfMDRiODc5NzFiNGRiNGY5NDk3MTYyOGVkNTIzMzdlYjgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMWUxZjBjMWZmMjZkNGI0NTg1NDcwYTg3YzU4NGRlZDUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTE2OTQ4LC03OS40MTY5MzU1OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iNTkxYzBiNGRkNTc0OGYwYmE5YTc3OTgyZTE5OWY2MSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81ZDAwYjZlYWM1N2M0ZmZlODI5YmE1YjJhNzk5NzNlZCA9ICQoJzxkaXYgaWQ9Imh0bWxfNWQwMGI2ZWFjNTdjNGZmZTgyOWJhNWIyYTc5OTczZWQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJvc2VsYXduLCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2I1OTFjMGI0ZGQ1NzQ4ZjBiYTlhNzc5ODJlMTk5ZjYxLnNldENvbnRlbnQoaHRtbF81ZDAwYjZlYWM1N2M0ZmZlODI5YmE1YjJhNzk5NzNlZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xZTFmMGMxZmYyNmQ0YjQ1ODU0NzBhODdjNTg0ZGVkNS5iaW5kUG9wdXAocG9wdXBfYjU5MWMwYjRkZDU3NDhmMGJhOWE3Nzk4MmUxOTlmNjEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzIxOWUzOGNkOWJmNDcwZDk4YzhmN2ExYjI1MzExZjkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NzMxODUyOTk5OTk5OSwtNzkuNDg3MjYxOTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMGJjZTllMTdjN2UxNGEwMmJjODFiODM5OTYzNmVhZDcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYjQxYWJhMGVkODA2NDRkNTllNzNkMzcyYmE4YTE5MWUgPSAkKCc8ZGl2IGlkPSJodG1sX2I0MWFiYTBlZDgwNjQ0ZDU5ZTczZDM3MmJhOGExOTFlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgSnVuY3Rpb24gTm9ydGgsIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzBiY2U5ZTE3YzdlMTRhMDJiYzgxYjgzOTk2MzZlYWQ3LnNldENvbnRlbnQoaHRtbF9iNDFhYmEwZWQ4MDY0NGQ1OWU3M2QzNzJiYThhMTkxZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zMjE5ZTM4Y2Q5YmY0NzBkOThjOGY3YTFiMjUzMTFmOS5iaW5kUG9wdXAocG9wdXBfMGJjZTllMTdjN2UxNGEwMmJjODFiODM5OTYzNmVhZDcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOGM1YWZmOGJmOTE3NGI1ZDkxNTcwN2MyYjhjNDkxZGEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NzMxODUyOTk5OTk5OSwtNzkuNDg3MjYxOTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODNkOGVjODkzZmU2NDIwM2FlNThiYTNmMTcxZWY3MmYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZmEzZTRjNDBhMzQwNDQxYzk1NGZlNDVmN2NiODZhZDQgPSAkKCc8ZGl2IGlkPSJodG1sX2ZhM2U0YzQwYTM0MDQ0MWM5NTRmZTQ1ZjdjYjg2YWQ0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5SdW5ueW1lZGUsIFlvcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzgzZDhlYzg5M2ZlNjQyMDNhZTU4YmEzZjE3MWVmNzJmLnNldENvbnRlbnQoaHRtbF9mYTNlNGM0MGEzNDA0NDFjOTU0ZmU0NWY3Y2I4NmFkNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84YzVhZmY4YmY5MTc0YjVkOTE1NzA3YzJiOGM0OTFkYS5iaW5kUG9wdXAocG9wdXBfODNkOGVjODkzZmU2NDIwM2FlNThiYTNmMTcxZWY3MmYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfN2U3MjgzODBjOTYyNGVhZWI0YTMyYjU1NjVmM2FiMDIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MDY4NzYsLTc5LjUxODE4ODQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2NiZDg3MWVkNGFmMTQ0MGI4ODBlY2Y4NWE3MGNiYTQ2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzlmNmJmNWZmYWZjZDRkYTg5ZTU1MTEyOTViOTE4NGI3ID0gJCgnPGRpdiBpZD0iaHRtbF85ZjZiZjVmZmFmY2Q0ZGE4OWU1NTExMjk1YjkxODRiNyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V2VzdG9uLCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jYmQ4NzFlZDRhZjE0NDBiODgwZWNmODVhNzBjYmE0Ni5zZXRDb250ZW50KGh0bWxfOWY2YmY1ZmZhZmNkNGRhODllNTUxMTI5NWI5MTg0YjcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfN2U3MjgzODBjOTYyNGVhZWI0YTMyYjU1NjVmM2FiMDIuYmluZFBvcHVwKHBvcHVwX2NiZDg3MWVkNGFmMTQ0MGI4ODBlY2Y4NWE3MGNiYTQ2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2E1NmQzODM5ZmVjNTQwNGNhODIzMTdlZDA0ZTdlNTBlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzU3NDA5NiwtNzkuMjczMzA0MDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTkwZTBmZjAyYWU1NDIyN2I0NjAwNDczODhiNGY0NDUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjNiM2Y5ZTUyMzhkNDRiNzkyNjY1M2M4YjY2NDUwYWMgPSAkKCc8ZGl2IGlkPSJodG1sXzYzYjNmOWU1MjM4ZDQ0Yjc5MjY2NTNjOGI2NjQ1MGFjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Eb3JzZXQgUGFyaywgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2U5MGUwZmYwMmFlNTQyMjdiNDYwMDQ3Mzg4YjRmNDQ1LnNldENvbnRlbnQoaHRtbF82M2IzZjllNTIzOGQ0NGI3OTI2NjUzYzhiNjY0NTBhYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9hNTZkMzgzOWZlYzU0MDRjYTgyMzE3ZWQwNGU3ZTUwZS5iaW5kUG9wdXAocG9wdXBfZTkwZTBmZjAyYWU1NDIyN2I0NjAwNDczODhiNGY0NDUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZmMzZDdhZWViYzQ2NDE2MmJhNzM0YzVkMmI5YjdhYTQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43NTc0MDk2LC03OS4yNzMzMDQwMDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82MjE3NjU1Y2FjNDI0OWQ3ODYwNzM4OWZmNGIxN2FiNyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83ZjM3M2U5ZWFiNjE0NTA2YTcxNDU3NWE5MGJkNWY4OCA9ICQoJzxkaXYgaWQ9Imh0bWxfN2YzNzNlOWVhYjYxNDUwNmE3MTQ1NzVhOTBiZDVmODgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlNjYXJib3JvdWdoIFRvd24gQ2VudHJlLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjIxNzY1NWNhYzQyNDlkNzg2MDczODlmZjRiMTdhYjcuc2V0Q29udGVudChodG1sXzdmMzczZTllYWI2MTQ1MDZhNzE0NTc1YTkwYmQ1Zjg4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2ZjM2Q3YWVlYmM0NjQxNjJiYTczNGM1ZDJiOWI3YWE0LmJpbmRQb3B1cChwb3B1cF82MjE3NjU1Y2FjNDI0OWQ3ODYwNzM4OWZmNGIxN2FiNyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zNzdhMWM0YjdjYjQ0MDg1YmRjODhmMDJjYmYwYmU5ZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc1NzQwOTYsLTc5LjI3MzMwNDAwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzg3OTY4MmU3ZTQyNzQ3NTNhZmU2ZDliNGE2ZDIxNmI4ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2RjMjA1NGQ2Y2E1ZjQ1OTRiYWVhOWVjYTNlNzFiNGYzID0gJCgnPGRpdiBpZD0iaHRtbF9kYzIwNTRkNmNhNWY0NTk0YmFlYTllY2EzZTcxYjRmMyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V2V4Zm9yZCBIZWlnaHRzLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODc5NjgyZTdlNDI3NDc1M2FmZTZkOWI0YTZkMjE2Yjguc2V0Q29udGVudChodG1sX2RjMjA1NGQ2Y2E1ZjQ1OTRiYWVhOWVjYTNlNzFiNGYzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzM3N2ExYzRiN2NiNDQwODViZGM4OGYwMmNiZjBiZTlmLmJpbmRQb3B1cChwb3B1cF84Nzk2ODJlN2U0Mjc0NzUzYWZlNmQ5YjRhNmQyMTZiOCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kYjUwMTg5ZDE3ZGE0YzEzYjkzZDk5YmYzYTAyOTI0NCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc1Mjc1ODI5OTk5OTk5NiwtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82OGJmMzc4YjdkNzI0Mzk2OWYyODliNzQ3NzhhMzhjNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xZTIzODliNzU5Mjk0NmFkOWVlNWRmMWE5MzExZWQ1MCA9ICQoJzxkaXYgaWQ9Imh0bWxfMWUyMzg5Yjc1OTI5NDZhZDllZTVkZjFhOTMxMWVkNTAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPllvcmsgTWlsbHMgV2VzdCwgTm9ydGggWW9yazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjhiZjM3OGI3ZDcyNDM5NjlmMjg5Yjc0Nzc4YTM4YzUuc2V0Q29udGVudChodG1sXzFlMjM4OWI3NTkyOTQ2YWQ5ZWU1ZGYxYTkzMTFlZDUwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2RiNTAxODlkMTdkYTRjMTNiOTNkOTliZjNhMDI5MjQ0LmJpbmRQb3B1cChwb3B1cF82OGJmMzc4YjdkNzI0Mzk2OWYyODliNzQ3NzhhMzhjNSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zODM2YzllMTZjMjk0MWFjODE1MzZiY2E0YzA0YmEwZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxMjc1MTEsLTc5LjM5MDE5NzVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMjhmZmMwZWViY2MwNDg3MWEyMjgwYzZiZmM5MGU2ZTYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZWQ5YTk1NjczMzM3NDIzN2JmYmVmZjQ5ODE5YTk5M2QgPSAkKCc8ZGl2IGlkPSJodG1sX2VkOWE5NTY3MzMzNzQyMzdiZmJlZmY0OTgxOWE5OTNkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5EYXZpc3ZpbGxlIE5vcnRoLCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzI4ZmZjMGVlYmNjMDQ4NzFhMjI4MGM2YmZjOTBlNmU2LnNldENvbnRlbnQoaHRtbF9lZDlhOTU2NzMzMzc0MjM3YmZiZWZmNDk4MTlhOTkzZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zODM2YzllMTZjMjk0MWFjODE1MzZiY2E0YzA0YmEwZS5iaW5kUG9wdXAocG9wdXBfMjhmZmMwZWViY2MwNDg3MWEyMjgwYzZiZmM5MGU2ZTYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzk5N2I0NGNkNmMyNDk1ZThiMmY2Y2RhN2IxYmFmOGEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42OTY5NDc2LC03OS40MTEzMDcyMDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hN2IwZjU5NTUxOGU0MGY2ODlmMmE5YzJlNjJkZjJiMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kODAxYmM0NjQxMzI0NTM2YTQ5NjI5OTQzZTBjYTg4NCA9ICQoJzxkaXYgaWQ9Imh0bWxfZDgwMWJjNDY0MTMyNDUzNmE0OTYyOTk0M2UwY2E4ODQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZvcmVzdCBIaWxsIE5vcnRoLCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2E3YjBmNTk1NTE4ZTQwZjY4OWYyYTljMmU2MmRmMmIyLnNldENvbnRlbnQoaHRtbF9kODAxYmM0NjQxMzI0NTM2YTQ5NjI5OTQzZTBjYTg4NCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zOTk3YjQ0Y2Q2YzI0OTVlOGIyZjZjZGE3YjFiYWY4YS5iaW5kUG9wdXAocG9wdXBfYTdiMGY1OTU1MThlNDBmNjg5ZjJhOWMyZTYyZGYyYjIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzU5YzY4NTdhYjg3NDA4ZWI2YjQ1OGUxOGUxZmE3YmUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42OTY5NDc2LC03OS40MTEzMDcyMDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ZWM0NTFkZjZkMDI0ODE3YjA5ZmY4MGY4ZDhhZDkxNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mMmY2NWM0NThlMTg0NzZmYjkxNTk0M2U2YWZjNmIzZSA9ICQoJzxkaXYgaWQ9Imh0bWxfZjJmNjVjNDU4ZTE4NDc2ZmI5MTU5NDNlNmFmYzZiM2UiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZvcmVzdCBIaWxsIFdlc3QsIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfN2VjNDUxZGY2ZDAyNDgxN2IwOWZmODBmOGQ4YWQ5MTYuc2V0Q29udGVudChodG1sX2YyZjY1YzQ1OGUxODQ3NmZiOTE1OTQzZTZhZmM2YjNlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzM1OWM2ODU3YWI4NzQwOGViNmI0NThlMThlMWZhN2JlLmJpbmRQb3B1cChwb3B1cF83ZWM0NTFkZjZkMDI0ODE3YjA5ZmY4MGY4ZDhhZDkxNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83OGRkNDlmYTU4ZDQ0ODlmOTJiYTA3YjFjYjRmMmFhMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MTYwODMsLTc5LjQ2NDc2MzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Y2YmI5NjUzYTkxNjRmODhhZGVmMTg2MDQwODU4OTE5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzkzZDM2NjRmZWQ0MzQzY2FiOTcwYzQ3ZDcyMThlZGY2ID0gJCgnPGRpdiBpZD0iaHRtbF85M2QzNjY0ZmVkNDM0M2NhYjk3MGM0N2Q3MjE4ZWRmNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SGlnaCBQYXJrLCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Y2YmI5NjUzYTkxNjRmODhhZGVmMTg2MDQwODU4OTE5LnNldENvbnRlbnQoaHRtbF85M2QzNjY0ZmVkNDM0M2NhYjk3MGM0N2Q3MjE4ZWRmNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83OGRkNDlmYTU4ZDQ0ODlmOTJiYTA3YjFjYjRmMmFhMS5iaW5kUG9wdXAocG9wdXBfZjZiYjk2NTNhOTE2NGY4OGFkZWYxODYwNDA4NTg5MTkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNWNiYzJjNjJjOTgyNGJkYWI1OTcwN2Y5M2MxZmE5NGUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjE2MDgzLC03OS40NjQ3NjMyOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hMzZmZDZkYWQ4ZWE0NGZmYjdkNWFjN2Y3ZTFiMmM5NiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mNWFhNmYxMWU1M2Q0ZDNmOTAzN2M5NGIwNGM1MjJkYiA9ICQoJzxkaXYgaWQ9Imh0bWxfZjVhYTZmMTFlNTNkNGQzZjkwMzdjOTRiMDRjNTIyZGIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRoZSBKdW5jdGlvbiBTb3V0aCwgV2VzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hMzZmZDZkYWQ4ZWE0NGZmYjdkNWFjN2Y3ZTFiMmM5Ni5zZXRDb250ZW50KGh0bWxfZjVhYTZmMTFlNTNkNGQzZjkwMzdjOTRiMDRjNTIyZGIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNWNiYzJjNjJjOTgyNGJkYWI1OTcwN2Y5M2MxZmE5NGUuYmluZFBvcHVwKHBvcHVwX2EzNmZkNmRhZDhlYTQ0ZmZiN2Q1YWM3ZjdlMWIyYzk2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzNmMWVmMWIyNzdmMTRjYmZiOWRhNjdmMDM5NDIyMDkwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjk2MzE5LC03OS41MzIyNDI0MDAwMDAwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lZWEwNzRmOGU3YzY0YmI4OWEzYTA4MDMwOTkxMjEyNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82ZTllYjU3Yjk0NTI0ZTUxYjkzNDQ3N2Y0MWI3ZTI5ZiA9ICQoJzxkaXYgaWQ9Imh0bWxfNmU5ZWI1N2I5NDUyNGU1MWI5MzQ0NzdmNDFiN2UyOWYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPldlc3Rtb3VudCwgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lZWEwNzRmOGU3YzY0YmI4OWEzYTA4MDMwOTkxMjEyNS5zZXRDb250ZW50KGh0bWxfNmU5ZWI1N2I5NDUyNGU1MWI5MzQ0NzdmNDFiN2UyOWYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfM2YxZWYxYjI3N2YxNGNiZmI5ZGE2N2YwMzk0MjIwOTAuYmluZFBvcHVwKHBvcHVwX2VlYTA3NGY4ZTdjNjRiYjg5YTNhMDgwMzA5OTEyMTI1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU5Njk4NzgzOTU4ZTQxNTQ5MGFlMjg1ODkyMjQxNDczID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzUwMDcxNTAwMDAwMDA0LC03OS4yOTU4NDkxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzRmNTkwNjJiOGY5NTQ5YTU4MTk3YjZiYWM4ZmU3NGQ3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzdjOWY0ZjE2MGRjNzQ4MGZhZWQ1Y2E3MzBkZWEyODc2ID0gJCgnPGRpdiBpZD0iaHRtbF83YzlmNGYxNjBkYzc0ODBmYWVkNWNhNzMwZGVhMjg3NiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TWFyeXZhbGUsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80ZjU5MDYyYjhmOTU0OWE1ODE5N2I2YmFjOGZlNzRkNy5zZXRDb250ZW50KGh0bWxfN2M5ZjRmMTYwZGM3NDgwZmFlZDVjYTczMGRlYTI4NzYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNTk2OTg3ODM5NThlNDE1NDkwYWUyODU4OTIyNDE0NzMuYmluZFBvcHVwKHBvcHVwXzRmNTkwNjJiOGY5NTQ5YTU4MTk3YjZiYWM4ZmU3NGQ3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2QxOGQ3MDEzM2Q5ODRlNDM4NTg0YzI0ZTNmZDhhOTc4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzUwMDcxNTAwMDAwMDA0LC03OS4yOTU4NDkxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Y1MzEzMDRmMTJhNjRmNGNiMzlmZDFjMWNmNWRiMjVlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzhhYzJjNWViMzViODQzYzQ4YjEyOWU5OTIyNGM2NWNjID0gJCgnPGRpdiBpZD0iaHRtbF84YWMyYzVlYjM1Yjg0M2M0OGIxMjllOTkyMjRjNjVjYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V2V4Zm9yZCwgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Y1MzEzMDRmMTJhNjRmNGNiMzlmZDFjMWNmNWRiMjVlLnNldENvbnRlbnQoaHRtbF84YWMyYzVlYjM1Yjg0M2M0OGIxMjllOTkyMjRjNjVjYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kMThkNzAxMzNkOTg0ZTQzODU4NGMyNGUzZmQ4YTk3OC5iaW5kUG9wdXAocG9wdXBfZjUzMTMwNGYxMmE2NGY0Y2IzOWZkMWMxY2Y1ZGIyNWUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYjU4ZjdhZDE4YWY4NDYyZGEzNzIxM2Q4OTZjMTBjM2IgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43ODI3MzY0LC03OS40NDIyNTkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2M4ODAwNzFkODQxNjQzMTM4MzE0ODRlYjlmNDQ2MTcxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2ExZWQxOWQzNWY0MDRmOTc4ZWU0ZmRkZDQ0OWMyZWM1ID0gJCgnPGRpdiBpZD0iaHRtbF9hMWVkMTlkMzVmNDA0Zjk3OGVlNGZkZGQ0NDljMmVjNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+V2lsbG93ZGFsZSBXZXN0LCBOb3J0aCBZb3JrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jODgwMDcxZDg0MTY0MzEzODMxNDg0ZWI5ZjQ0NjE3MS5zZXRDb250ZW50KGh0bWxfYTFlZDE5ZDM1ZjQwNGY5NzhlZTRmZGRkNDQ5YzJlYzUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYjU4ZjdhZDE4YWY4NDYyZGEzNzIxM2Q4OTZjMTBjM2IuYmluZFBvcHVwKHBvcHVwX2M4ODAwNzFkODQxNjQzMTM4MzE0ODRlYjlmNDQ2MTcxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzliYTkzNGI5ZDAxODQ3NWRiYWYwMjljYzM2MjliOTRhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzE1MzgzNCwtNzkuNDA1Njc4NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYmQ4ZGI0NWRkNTI3NDc1NDlmY2FkODRiZGJjZjY2OTYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOTY4MDZjZGUyY2IxNDU0NmE2MjQ4N2Q4ZTdmZTViNzggPSAkKCc8ZGl2IGlkPSJodG1sXzk2ODA2Y2RlMmNiMTQ1NDZhNjI0ODdkOGU3ZmU1Yjc4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Ob3J0aCBUb3JvbnRvIFdlc3QsIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYmQ4ZGI0NWRkNTI3NDc1NDlmY2FkODRiZGJjZjY2OTYuc2V0Q29udGVudChodG1sXzk2ODA2Y2RlMmNiMTQ1NDZhNjI0ODdkOGU3ZmU1Yjc4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzliYTkzNGI5ZDAxODQ3NWRiYWYwMjljYzM2MjliOTRhLmJpbmRQb3B1cChwb3B1cF9iZDhkYjQ1ZGQ1Mjc0NzU0OWZjYWQ4NGJkYmNmNjY5Nik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84MmVmMGU5Yjg5NGI0ODJlOGY1NmJhYjlkZDY3Njk4YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3MjcwOTcsLTc5LjQwNTY3ODQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzJjYjY5YjVmYTY4ODQwNzRhZTVmMTgzNWJhMTRlYWY3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2YxYjlmYjVmYThmYzQwODU5NjNlN2I2MDhlZDcwZDE3ID0gJCgnPGRpdiBpZD0iaHRtbF9mMWI5ZmI1ZmE4ZmM0MDg1OTYzZTdiNjA4ZWQ3MGQxNyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VGhlIEFubmV4LCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzJjYjY5YjVmYTY4ODQwNzRhZTVmMTgzNWJhMTRlYWY3LnNldENvbnRlbnQoaHRtbF9mMWI5ZmI1ZmE4ZmM0MDg1OTYzZTdiNjA4ZWQ3MGQxNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84MmVmMGU5Yjg5NGI0ODJlOGY1NmJhYjlkZDY3Njk4YS5iaW5kUG9wdXAocG9wdXBfMmNiNjliNWZhNjg4NDA3NGFlNWYxODM1YmExNGVhZjcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMTM3MGJhYjUxOTIzNDMyOWEzYjFmYzliZDUwZjdhY2MgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NzI3MDk3LC03OS40MDU2Nzg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lNzI3YzQ2NDViMWQ0NDBhYTI0MjY4ODRjMTc4MjU0MSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81NzdjODM5YTg4OTE0NzZiYmM2MjU0NzBlZDQzMzUzNSA9ICQoJzxkaXYgaWQ9Imh0bWxfNTc3YzgzOWE4ODkxNDc2YmJjNjI1NDcwZWQ0MzM1MzUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5vcnRoIE1pZHRvd24sIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTcyN2M0NjQ1YjFkNDQwYWEyNDI2ODg0YzE3ODI1NDEuc2V0Q29udGVudChodG1sXzU3N2M4MzlhODg5MTQ3NmJiYzYyNTQ3MGVkNDMzNTM1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzEzNzBiYWI1MTkyMzQzMjlhM2IxZmM5YmQ1MGY3YWNjLmJpbmRQb3B1cChwb3B1cF9lNzI3YzQ2NDViMWQ0NDBhYTI0MjY4ODRjMTc4MjU0MSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82YzM2ZTQ1MzdhZTk0YmJjODRlZmZhNjFhMmFkMjc2NyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3MjcwOTcsLTc5LjQwNTY3ODQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzE4ZWNmZWRkNzFhNTRiZDY5MTc5OGRhZjhhOGIyYTkzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Y3MGIxZjc2ZmJjNTQ3YWVhYWU1NDZiYzQ3ZWU0MzNkID0gJCgnPGRpdiBpZD0iaHRtbF9mNzBiMWY3NmZiYzU0N2FlYWFlNTQ2YmM0N2VlNDMzZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+WW9ya3ZpbGxlLCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE4ZWNmZWRkNzFhNTRiZDY5MTc5OGRhZjhhOGIyYTkzLnNldENvbnRlbnQoaHRtbF9mNzBiMWY3NmZiYzU0N2FlYWFlNTQ2YmM0N2VlNDMzZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82YzM2ZTQ1MzdhZTk0YmJjODRlZmZhNjFhMmFkMjc2Ny5iaW5kUG9wdXAocG9wdXBfMThlY2ZlZGQ3MWE1NGJkNjkxNzk4ZGFmOGE4YjJhOTMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzViNzg5NWI1M2U2NDdlNjk5M2NkOWFiMDQ1ZGI1ZDQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDg5NTk3LC03OS40NTYzMjVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzhmZjVjMTEyZTllNGQwZDg4MjA4MzUxNGQyZmJlMWYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTliMTI4YTEwYzE5NDE4NDg0ZTI0NDU5OTgyMThjMmYgPSAkKCc8ZGl2IGlkPSJodG1sX2E5YjEyOGExMGMxOTQxODQ4NGUyNDQ1OTk4MjE4YzJmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QYXJrZGFsZSwgV2VzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zOGZmNWMxMTJlOWU0ZDBkODgyMDgzNTE0ZDJmYmUxZi5zZXRDb250ZW50KGh0bWxfYTliMTI4YTEwYzE5NDE4NDg0ZTI0NDU5OTgyMThjMmYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzViNzg5NWI1M2U2NDdlNjk5M2NkOWFiMDQ1ZGI1ZDQuYmluZFBvcHVwKHBvcHVwXzM4ZmY1YzExMmU5ZTRkMGQ4ODIwODM1MTRkMmZiZTFmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2FlNjM3MzU0NDdlNDRiMmM5ZTA2ZDM5MzNlODA4ZTdmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ4OTU5NywtNzkuNDU2MzI1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzZlNTk2MjFmZTAxYTQ4Yzg4NDVlZDJlZjlmZGMxMDVlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzMxNTIxZTIxZDA5ODQ2ZTliNWM4ZGNhYjljN2MxMDM1ID0gJCgnPGRpdiBpZD0iaHRtbF8zMTUyMWUyMWQwOTg0NmU5YjVjOGRjYWI5YzdjMTAzNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Um9uY2VzdmFsbGVzLCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzZlNTk2MjFmZTAxYTQ4Yzg4NDVlZDJlZjlmZGMxMDVlLnNldENvbnRlbnQoaHRtbF8zMTUyMWUyMWQwOTg0NmU5YjVjOGRjYWI5YzdjMTAzNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9hZTYzNzM1NDQ3ZTQ0YjJjOWUwNmQzOTMzZTgwOGU3Zi5iaW5kUG9wdXAocG9wdXBfNmU1OTYyMWZlMDFhNDhjODg0NWVkMmVmOWZkYzEwNWUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNTAzNTIyMzQwYjU2NGVjODg0ZWZmNTg3NjY2MjMyZjkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42MzY5NjU2LC03OS42MTU4MTg5OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84NGI5M2YwNDc5NzU0YzVhYjQ1ZDE0ZTMzNzJlNjZkYiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kZjA5N2MyODQxNDI0ZmRmODdlZGVmOTUwNzJhYTI5ZCA9ICQoJzxkaXYgaWQ9Imh0bWxfZGYwOTdjMjg0MTQyNGZkZjg3ZWRlZjk1MDcyYWEyOWQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNhbmFkYSBQb3N0IEdhdGV3YXkgUHJvY2Vzc2luZyBDZW50cmUsIE1pc3Npc3NhdWdhPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84NGI5M2YwNDc5NzU0YzVhYjQ1ZDE0ZTMzNzJlNjZkYi5zZXRDb250ZW50KGh0bWxfZGYwOTdjMjg0MTQyNGZkZjg3ZWRlZjk1MDcyYWEyOWQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNTAzNTIyMzQwYjU2NGVjODg0ZWZmNTg3NjY2MjMyZjkuYmluZFBvcHVwKHBvcHVwXzg0YjkzZjA0Nzk3NTRjNWFiNDVkMTRlMzM3MmU2NmRiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzYwNWQ5N2JiMzcxMDQ4MGE4MjgzNjE4NWJlNWQ4MWI4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg4OTA1NCwtNzkuNTU0NzI0NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYmUyNTE3ZTM1ZmMxNDdlNmI5MTllZTVkYzJiOGYyN2MgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZGI4ZGZlZmI3MDUyNGMyODgzN2E0ZTNmODU1ZGI3NzggPSAkKCc8ZGl2IGlkPSJodG1sX2RiOGRmZWZiNzA1MjRjMjg4MzdhNGUzZjg1NWRiNzc4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5LaW5nc3ZpZXcgVmlsbGFnZSwgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iZTI1MTdlMzVmYzE0N2U2YjkxOWVlNWRjMmI4ZjI3Yy5zZXRDb250ZW50KGh0bWxfZGI4ZGZlZmI3MDUyNGMyODgzN2E0ZTNmODU1ZGI3NzgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNjA1ZDk3YmIzNzEwNDgwYTgyODM2MTg1YmU1ZDgxYjguYmluZFBvcHVwKHBvcHVwX2JlMjUxN2UzNWZjMTQ3ZTZiOTE5ZWU1ZGMyYjhmMjdjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzljMTk5YjMzY2Y4MzQ2ZmU5MjA2YTQzNDA3ODkzMjQzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg4OTA1NCwtNzkuNTU0NzI0NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWRjZGRiMTcxMTBmNDAyY2JhNWE5N2MyM2I2NTNlYTkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMmFkNzIxNWYwZjQyNDEyMmJlNTkwYzk0YzI3YzI0ZTYgPSAkKCc8ZGl2IGlkPSJodG1sXzJhZDcyMTVmMGY0MjQxMjJiZTU5MGM5NGMyN2MyNGU2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5NYXJ0aW4gR3JvdmUgR2FyZGVucywgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85ZGNkZGIxNzExMGY0MDJjYmE1YTk3YzIzYjY1M2VhOS5zZXRDb250ZW50KGh0bWxfMmFkNzIxNWYwZjQyNDEyMmJlNTkwYzk0YzI3YzI0ZTYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOWMxOTliMzNjZjgzNDZmZTkyMDZhNDM0MDc4OTMyNDMuYmluZFBvcHVwKHBvcHVwXzlkY2RkYjE3MTEwZjQwMmNiYTVhOTdjMjNiNjUzZWE5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2YxNzBjMmQyMWUyZDQxZmM5YjlhOWVkNDM0ZDY2OWNmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg4OTA1NCwtNzkuNTU0NzI0NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZWFjNDEzOWFkYTI5NGRmNjkxYTE0M2U4NjY1NjdjMGIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzRmMjYyYWJhMjI3NDQ3MGI1MjgwMjQ0ZGVmMzBjMzQgPSAkKCc8ZGl2IGlkPSJodG1sXzM0ZjI2MmFiYTIyNzQ0NzBiNTI4MDI0NGRlZjMwYzM0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5SaWNodmlldyBHYXJkZW5zLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2VhYzQxMzlhZGEyOTRkZjY5MWExNDNlODY2NTY3YzBiLnNldENvbnRlbnQoaHRtbF8zNGYyNjJhYmEyMjc0NDcwYjUyODAyNDRkZWYzMGMzNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mMTcwYzJkMjFlMmQ0MWZjOWI5YTllZDQzNGQ2NjljZi5iaW5kUG9wdXAocG9wdXBfZWFjNDEzOWFkYTI5NGRmNjkxYTE0M2U4NjY1NjdjMGIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNWE4MjhjOGI0MGU4NDU3MGExNTc1NzRjOTIyMGU2YjEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODg5MDU0LC03OS41NTQ3MjQ0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hNjQwNWQzZDA1ZjY0OTExODkzYTllNzg1ZDI1OGE1MSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83NmFjMjVhZWJhNTU0YzY2YTAzNTRlMWE3NzAzMmFhNCA9ICQoJzxkaXYgaWQ9Imh0bWxfNzZhYzI1YWViYTU1NGM2NmEwMzU0ZTFhNzcwMzJhYTQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0LiBQaGlsbGlwcywgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hNjQwNWQzZDA1ZjY0OTExODkzYTllNzg1ZDI1OGE1MS5zZXRDb250ZW50KGh0bWxfNzZhYzI1YWViYTU1NGM2NmEwMzU0ZTFhNzcwMzJhYTQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNWE4MjhjOGI0MGU4NDU3MGExNTc1NzRjOTIyMGU2YjEuYmluZFBvcHVwKHBvcHVwX2E2NDA1ZDNkMDVmNjQ5MTE4OTNhOWU3ODVkMjU4YTUxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzI2MTE1ODg2YjkxOTQ3MmI5MTA3YmM3OGNlNjY5MjgyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzk0MjAwMywtNzkuMjYyMDI5NDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWU4NWU3YWU1MjI1NDQ4Mjk2MmM5NmFjNjAyOTY2YTEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzI5MmZhMThhY2Y5NGMzNGI2M2ZjNzMwN2Q2ZGU3YjYgPSAkKCc8ZGl2IGlkPSJodG1sXzMyOTJmYTE4YWNmOTRjMzRiNjNmYzczMDdkNmRlN2I2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BZ2luY291cnQsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hZTg1ZTdhZTUyMjU0NDgyOTYyYzk2YWM2MDI5NjZhMS5zZXRDb250ZW50KGh0bWxfMzI5MmZhMThhY2Y5NGMzNGI2M2ZjNzMwN2Q2ZGU3YjYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMjYxMTU4ODZiOTE5NDcyYjkxMDdiYzc4Y2U2NjkyODIuYmluZFBvcHVwKHBvcHVwX2FlODVlN2FlNTIyNTQ0ODI5NjJjOTZhYzYwMjk2NmExKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzBlN2IyMDdlY2VkMTRhMjZiOTMwNmQ1ZDk3OWMyNmM4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzA0MzI0NCwtNzkuMzg4NzkwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82ZmE5YWNjOTI2ZWU0ZDZjOGVlYmMwODgzN2MzNmU4YyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hODgwYjk0MTQ1NzQ0MjM4YmJiMmFiYmY5MTJhNjg3MyA9ICQoJzxkaXYgaWQ9Imh0bWxfYTg4MGI5NDE0NTc0NDIzOGJiYjJhYmJmOTEyYTY4NzMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRhdmlzdmlsbGUsIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNmZhOWFjYzkyNmVlNGQ2YzhlZWJjMDg4MzdjMzZlOGMuc2V0Q29udGVudChodG1sX2E4ODBiOTQxNDU3NDQyMzhiYmIyYWJiZjkxMmE2ODczKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzBlN2IyMDdlY2VkMTRhMjZiOTMwNmQ1ZDk3OWMyNmM4LmJpbmRQb3B1cChwb3B1cF82ZmE5YWNjOTI2ZWU0ZDZjOGVlYmMwODgzN2MzNmU4Yyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iYTU5MGU0NTRkN2M0ZDg3YjMxOTNiODFjY2FkMTA2MSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MjY5NTYsLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNGRhMjdjZDk2NDM3NDg4NmEwYTgwMjc0ZDFiM2RlMWYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTE0MjBlZDZiNGVjNGFlNDhhNmQ5ZjhmY2Q3ZjQ1ZmEgPSAkKCc8ZGl2IGlkPSJodG1sX2ExNDIwZWQ2YjRlYzRhZTQ4YTZkOWY4ZmNkN2Y0NWZhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IYXJib3JkLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80ZGEyN2NkOTY0Mzc0ODg2YTBhODAyNzRkMWIzZGUxZi5zZXRDb250ZW50KGh0bWxfYTE0MjBlZDZiNGVjNGFlNDhhNmQ5ZjhmY2Q3ZjQ1ZmEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYmE1OTBlNDU0ZDdjNGQ4N2IzMTkzYjgxY2NhZDEwNjEuYmluZFBvcHVwKHBvcHVwXzRkYTI3Y2Q5NjQzNzQ4ODZhMGE4MDI3NGQxYjNkZTFmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzQ2ZmRkZDI3MjY4MzQzMjM5MmVhYjAyYWIxYTYwY2ZhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjYyNjk1NiwtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81NGEwOTVjY2NlZTA0ZTEyOThiY2E1ZDBmNTUwZWJhOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xYmNjNTYzODBkYjY0ODA3OGJhNmU4ZjU5OWQyMjQ2ZiA9ICQoJzxkaXYgaWQ9Imh0bWxfMWJjYzU2MzgwZGI2NDgwNzhiYTZlOGY1OTlkMjI0NmYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlVuaXZlcnNpdHkgb2YgVG9yb250bywgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNTRhMDk1Y2NjZWUwNGUxMjk4YmNhNWQwZjU1MGViYTkuc2V0Q29udGVudChodG1sXzFiY2M1NjM4MGRiNjQ4MDc4YmE2ZThmNTk5ZDIyNDZmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzQ2ZmRkZDI3MjY4MzQzMjM5MmVhYjAyYWIxYTYwY2ZhLmJpbmRQb3B1cChwb3B1cF81NGEwOTVjY2NlZTA0ZTEyOThiY2E1ZDBmNTUwZWJhOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mYjc1ZjhhNWE4ODg0MmQxOGExN2E5NDYxMjYzZWE2NiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MTU3MDYsLTc5LjQ4NDQ0OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjU1ODRiYjE2MDY4NDE4Nzg4NjRjNjZhM2QzMzgwNmQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODY3ZDU5YTJiYjNjNDNmOWE4YTc1OWI3MGJkY2UxNmYgPSAkKCc8ZGl2IGlkPSJodG1sXzg2N2Q1OWEyYmIzYzQzZjlhOGE3NTliNzBiZGNlMTZmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5SdW5ueW1lZGUsIFdlc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjU1ODRiYjE2MDY4NDE4Nzg4NjRjNjZhM2QzMzgwNmQuc2V0Q29udGVudChodG1sXzg2N2Q1OWEyYmIzYzQzZjlhOGE3NTliNzBiZGNlMTZmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2ZiNzVmOGE1YTg4ODQyZDE4YTE3YTk0NjEyNjNlYTY2LmJpbmRQb3B1cChwb3B1cF9iNTU4NGJiMTYwNjg0MTg3ODg2NGM2NmEzZDMzODA2ZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mMGIzOWE2Zjc0OWY0ZDc1YWQ2OWYxY2I3OTE0Y2YzNCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MTU3MDYsLTc5LjQ4NDQ0OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWMyNzBkZTllZjllNDk2NTg5OTE2N2RlNDY4OTJhMDQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNTU5NjVjNTZjYjAwNDRiN2JlNjE2MDA4ODMwNzRmNmMgPSAkKCc8ZGl2IGlkPSJodG1sXzU1OTY1YzU2Y2IwMDQ0YjdiZTYxNjAwODgzMDc0ZjZjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Td2Fuc2VhLCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2FjMjcwZGU5ZWY5ZTQ5NjU4OTkxNjdkZTQ2ODkyYTA0LnNldENvbnRlbnQoaHRtbF81NTk2NWM1NmNiMDA0NGI3YmU2MTYwMDg4MzA3NGY2Yyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mMGIzOWE2Zjc0OWY0ZDc1YWQ2OWYxY2I3OTE0Y2YzNC5iaW5kUG9wdXAocG9wdXBfYWMyNzBkZTllZjllNDk2NTg5OTE2N2RlNDY4OTJhMDQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzAwYjVlYjYyY2NhNGFlZTk5MTNjNGZmOTE3YTYyZDIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43ODE2Mzc1LC03OS4zMDQzMDIxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2MwYjQwMjlkOTRlNDQyNGJhMTExMDAwZTZmYmQxNzk0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2JhMzUyZTMyMTljNDRiMDM5ZDYwNGVhYTlhOGY1ZjE0ID0gJCgnPGRpdiBpZD0iaHRtbF9iYTM1MmUzMjE5YzQ0YjAzOWQ2MDRlYWE5YThmNWYxNCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2xhcmtzIENvcm5lcnMsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jMGI0MDI5ZDk0ZTQ0MjRiYTExMTAwMGU2ZmJkMTc5NC5zZXRDb250ZW50KGh0bWxfYmEzNTJlMzIxOWM0NGIwMzlkNjA0ZWFhOWE4ZjVmMTQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzAwYjVlYjYyY2NhNGFlZTk5MTNjNGZmOTE3YTYyZDIuYmluZFBvcHVwKHBvcHVwX2MwYjQwMjlkOTRlNDQyNGJhMTExMDAwZTZmYmQxNzk0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzczOWVmYjA5NmU0YjQ2YTZhNTNlZjU5M2I0MTM0NTBmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzgxNjM3NSwtNzkuMzA0MzAyMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hNjc4ZjU5MWY0Zjk0Zjk2YjZkODhmMTI5ZmQxNzM4ZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hNjJkMDBkOTc2ZjY0YmVlOGRmODY0NDVmNWVkYTc3YSA9ICQoJzxkaXYgaWQ9Imh0bWxfYTYyZDAwZDk3NmY2NGJlZThkZjg2NDQ1ZjVlZGE3N2EiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN1bGxpdmFuLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYTY3OGY1OTFmNGY5NGY5NmI2ZDg4ZjEyOWZkMTczOGYuc2V0Q29udGVudChodG1sX2E2MmQwMGQ5NzZmNjRiZWU4ZGY4NjQ0NWY1ZWRhNzdhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzczOWVmYjA5NmU0YjQ2YTZhNTNlZjU5M2I0MTM0NTBmLmJpbmRQb3B1cChwb3B1cF9hNjc4ZjU5MWY0Zjk0Zjk2YjZkODhmMTI5ZmQxNzM4Zik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yYWM3ODM5OTYwZjc0NTcyOGFmMjJiMjlmODdjZDU2OCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc4MTYzNzUsLTc5LjMwNDMwMjFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDBmZTE4NGQxNjYyNDg3OTgwYTAyYjJhZjA0ZDI1NzEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDhjNTI4YzVlMzI3NGE3NTljYjU1ODVkN2UzNmRhMWMgPSAkKCc8ZGl2IGlkPSJodG1sX2Q4YzUyOGM1ZTMyNzRhNzU5Y2I1NTg1ZDdlMzZkYTFjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UYW0gTyYjMzk7U2hhbnRlciwgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQwZmUxODRkMTY2MjQ4Nzk4MGEwMmIyYWYwNGQyNTcxLnNldENvbnRlbnQoaHRtbF9kOGM1MjhjNWUzMjc0YTc1OWNiNTU4NWQ3ZTM2ZGExYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yYWM3ODM5OTYwZjc0NTcyOGFmMjJiMjlmODdjZDU2OC5iaW5kUG9wdXAocG9wdXBfNDBmZTE4NGQxNjYyNDg3OTgwYTAyYjJhZjA0ZDI1NzEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNmU5Njk2NDU5MjVmNGEyYWE3ZjViYzQ4MzhmMWM1MjAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODk1NzQzLC03OS4zODMxNTk5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84ZDNjN2Y5NjhjZjI0MTY2YjI3NThjODVhN2YzZjgwNCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hZDNiNzgxMDdmMWM0NmNlYWY3N2M2Mzc1MjllODgyMCA9ICQoJzxkaXYgaWQ9Imh0bWxfYWQzYjc4MTA3ZjFjNDZjZWFmNzdjNjM3NTI5ZTg4MjAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1vb3JlIFBhcmssIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOGQzYzdmOTY4Y2YyNDE2NmIyNzU4Yzg1YTdmM2Y4MDQuc2V0Q29udGVudChodG1sX2FkM2I3ODEwN2YxYzQ2Y2VhZjc3YzYzNzUyOWU4ODIwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzZlOTY5NjQ1OTI1ZjRhMmFhN2Y1YmM0ODM4ZjFjNTIwLmJpbmRQb3B1cChwb3B1cF84ZDNjN2Y5NjhjZjI0MTY2YjI3NThjODVhN2YzZjgwNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wMGY3NzIxZTk1MDg0ZmE4YjI0ZjI5ZWRiMTUxOTJhYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY4OTU3NDMsLTc5LjM4MzE1OTkwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2UxZjMxY2ZhNThhMTRiYzhiN2FhYjJhMzkxMmIyZDVlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzE4MTMwMmY3ZGE5OTQzMWJhNjQwMjQ3YTBhMDYwYjc5ID0gJCgnPGRpdiBpZD0iaHRtbF8xODEzMDJmN2RhOTk0MzFiYTY0MDI0N2EwYTA2MGI3OSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U3VtbWVyaGlsbCBFYXN0LCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2UxZjMxY2ZhNThhMTRiYzhiN2FhYjJhMzkxMmIyZDVlLnNldENvbnRlbnQoaHRtbF8xODEzMDJmN2RhOTk0MzFiYTY0MDI0N2EwYTA2MGI3OSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wMGY3NzIxZTk1MDg0ZmE4YjI0ZjI5ZWRiMTUxOTJhYy5iaW5kUG9wdXAocG9wdXBfZTFmMzFjZmE1OGExNGJjOGI3YWFiMmEzOTEyYjJkNWUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMGMyOTYxOGE3N2UzNGQ2OThhYWMwNzIzMGViOTcwNmYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTMyMDU3LC03OS40MDAwNDkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2E3NTllMjc3MDVjNTQzZmViNmRjZjViYzg0ZTYyNGFjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzMzNjI0MzY4YmViODRmMTg4ZTk1ZjNhZWVlNzAzOTkxID0gJCgnPGRpdiBpZD0iaHRtbF8zMzYyNDM2OGJlYjg0ZjE4OGU5NWYzYWVlZTcwMzk5MSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2hpbmF0b3duLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hNzU5ZTI3NzA1YzU0M2ZlYjZkY2Y1YmM4NGU2MjRhYy5zZXRDb250ZW50KGh0bWxfMzM2MjQzNjhiZWI4NGYxODhlOTVmM2FlZWU3MDM5OTEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMGMyOTYxOGE3N2UzNGQ2OThhYWMwNzIzMGViOTcwNmYuYmluZFBvcHVwKHBvcHVwX2E3NTllMjc3MDVjNTQzZmViNmRjZjViYzg0ZTYyNGFjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzY5ZTA2OWI5MDQ1YzRmZTFiYzA0OTU3MzAyZTQwMzZkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUzMjA1NywtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ZTUzYmRjNDhjOTQ0NDM1YjM3NzUxYTFhNWU0ZDljYiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yZWY5ZjFiNmNiN2Y0MjJmYWE5YTAxNzc4NjgxNDI4OSA9ICQoJzxkaXYgaWQ9Imh0bWxfMmVmOWYxYjZjYjdmNDIyZmFhOWEwMTc3ODY4MTQyODkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkdyYW5nZSBQYXJrLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83ZTUzYmRjNDhjOTQ0NDM1YjM3NzUxYTFhNWU0ZDljYi5zZXRDb250ZW50KGh0bWxfMmVmOWYxYjZjYjdmNDIyZmFhOWEwMTc3ODY4MTQyODkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNjllMDY5YjkwNDVjNGZlMWJjMDQ5NTczMDJlNDAzNmQuYmluZFBvcHVwKHBvcHVwXzdlNTNiZGM0OGM5NDQ0MzViMzc3NTFhMWE1ZTRkOWNiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzNiYzk0MDJjMzJiMzQxMzZhMThlOTk5Yjk5NjliZGY1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUzMjA1NywtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82Yjg2NjNmY2FiMzg0ZjNkOWJiNWYwMWQ0MzNiZGI4MCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mYjg4NDczNjUxMWM0ODBkYmY3NWFmZjM1MGY3YmYzZiA9ICQoJzxkaXYgaWQ9Imh0bWxfZmI4ODQ3MzY1MTFjNDgwZGJmNzVhZmYzNTBmN2JmM2YiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPktlbnNpbmd0b24gTWFya2V0LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82Yjg2NjNmY2FiMzg0ZjNkOWJiNWYwMWQ0MzNiZGI4MC5zZXRDb250ZW50KGh0bWxfZmI4ODQ3MzY1MTFjNDgwZGJmNzVhZmYzNTBmN2JmM2YpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfM2JjOTQwMmMzMmIzNDEzNmExOGU5OTliOTk2OWJkZjUuYmluZFBvcHVwKHBvcHVwXzZiODY2M2ZjYWIzODRmM2Q5YmI1ZjAxZDQzM2JkYjgwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzBhZWE0OGMzNzk5YTRkYTVhMWY2YWRjZDQ4MTUzNjIwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuODE1MjUyMiwtNzkuMjg0NTc3Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zM2IyNmJiNDJmM2M0MWNiODQwMTFhNWUyNWE1YzE0OSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iOTM3N2EyZTEzYjM0NTUzYTdjNzJiMTQ0OWVjMGNiOSA9ICQoJzxkaXYgaWQ9Imh0bWxfYjkzNzdhMmUxM2IzNDU1M2E3YzcyYjE0NDllYzBjYjkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkFnaW5jb3VydCBOb3J0aCwgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzMzYjI2YmI0MmYzYzQxY2I4NDAxMWE1ZTI1YTVjMTQ5LnNldENvbnRlbnQoaHRtbF9iOTM3N2EyZTEzYjM0NTUzYTdjNzJiMTQ0OWVjMGNiOSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wYWVhNDhjMzc5OWE0ZGE1YTFmNmFkY2Q0ODE1MzYyMC5iaW5kUG9wdXAocG9wdXBfMzNiMjZiYjQyZjNjNDFjYjg0MDExYTVlMjVhNWMxNDkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfN2RlMjY1OTU4OTQ0NDhhY2E3Y2I2Mjk0YmMxNTk2OTYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My44MTUyNTIyLC03OS4yODQ1NzcyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzYxZmZiZjE0NTJlMzQ5MWI4OTU0OTZiOGQ2NmM5YmVlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzg2MzE5YzA3MjY4NTQzYTBiOTk5YmIxN2UxZmNiYjc4ID0gJCgnPGRpdiBpZD0iaHRtbF84NjMxOWMwNzI2ODU0M2EwYjk5OWJiMTdlMWZjYmI3OCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TCYjMzk7QW1vcmVhdXggRWFzdCwgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzYxZmZiZjE0NTJlMzQ5MWI4OTU0OTZiOGQ2NmM5YmVlLnNldENvbnRlbnQoaHRtbF84NjMxOWMwNzI2ODU0M2EwYjk5OWJiMTdlMWZjYmI3OCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83ZGUyNjU5NTg5NDQ0OGFjYTdjYjYyOTRiYzE1OTY5Ni5iaW5kUG9wdXAocG9wdXBfNjFmZmJmMTQ1MmUzNDkxYjg5NTQ5NmI4ZDY2YzliZWUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDkxYzA4MjdjNGUyNGM1ZWE2MWZlM2NiZTJmZjJkZGYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My44MTUyNTIyLC03OS4yODQ1NzcyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzk4NmZhNmQ5Mjg5YTQ3NmU5MjQzMDBlNjY2MzNlNTc2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2RjZTU2MGIxYTY0MTRkNDQ4MDc1NGRjNWIxYmU2ZjI5ID0gJCgnPGRpdiBpZD0iaHRtbF9kY2U1NjBiMWE2NDE0ZDQ0ODA3NTRkYzViMWJlNmYyOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TWlsbGlrZW4sIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85ODZmYTZkOTI4OWE0NzZlOTI0MzAwZTY2NjMzZTU3Ni5zZXRDb250ZW50KGh0bWxfZGNlNTYwYjFhNjQxNGQ0NDgwNzU0ZGM1YjFiZTZmMjkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDkxYzA4MjdjNGUyNGM1ZWE2MWZlM2NiZTJmZjJkZGYuYmluZFBvcHVwKHBvcHVwXzk4NmZhNmQ5Mjg5YTQ3NmU5MjQzMDBlNjY2MzNlNTc2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2U5OGI5MjFiN2E3ZTQ3MjFhODYzMjg5MWRmZDllNmIxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuODE1MjUyMiwtNzkuMjg0NTc3Ml0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jODk3NGUxYmE1ZWE0YjRjOWYxYzNjMjY2NDE5MmFhNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82ZTVlMGQ1MGQyYWU0MjJlYmFkOGIxM2U3YTUyYmY5ZSA9ICQoJzxkaXYgaWQ9Imh0bWxfNmU1ZTBkNTBkMmFlNDIyZWJhZDhiMTNlN2E1MmJmOWUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0ZWVsZXMgRWFzdCwgU2NhcmJvcm91Z2g8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2M4OTc0ZTFiYTVlYTRiNGM5ZjFjM2MyNjY0MTkyYWE2LnNldENvbnRlbnQoaHRtbF82ZTVlMGQ1MGQyYWU0MjJlYmFkOGIxM2U3YTUyYmY5ZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lOThiOTIxYjdhN2U0NzIxYTg2MzI4OTFkZmQ5ZTZiMS5iaW5kUG9wdXAocG9wdXBfYzg5NzRlMWJhNWVhNGI0YzlmMWMzYzI2NjQxOTJhYTYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZWVjZThmMWQyMGQ3NDJiY2JhNzNmN2YxNTc1YzM5ODggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODY0MTIyOTk5OTk5OSwtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81OWNhNmI1Mjg3NWQ0NmY3ODdlZDFkOThlMjAzMmY3OCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hY2EwMTZlOWU5M2Q0MGZjOTA0YTM2YWU5ZTgxMmJjZSA9ICQoJzxkaXYgaWQ9Imh0bWxfYWNhMDE2ZTllOTNkNDBmYzkwNGEzNmFlOWU4MTJiY2UiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRlZXIgUGFyaywgQ2VudHJhbCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81OWNhNmI1Mjg3NWQ0NmY3ODdlZDFkOThlMjAzMmY3OC5zZXRDb250ZW50KGh0bWxfYWNhMDE2ZTllOTNkNDBmYzkwNGEzNmFlOWU4MTJiY2UpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZWVjZThmMWQyMGQ3NDJiY2JhNzNmN2YxNTc1YzM5ODguYmluZFBvcHVwKHBvcHVwXzU5Y2E2YjUyODc1ZDQ2Zjc4N2VkMWQ5OGUyMDMyZjc4KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2FmMGIwMDdhNjdiMjQyNzJiMjFhYjAyNTI2ODZlYWIzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg2NDEyMjk5OTk5OTksLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfN2Y3MjIzMTA4MjE3NDY4NmJkYzgwMDU4NTUzYzllMjkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNTM4OTI0ZThiZDQ0NGQ1ZTlkYmE0ZmIxMTZlMGVjN2QgPSAkKCc8ZGl2IGlkPSJodG1sXzUzODkyNGU4YmQ0NDRkNWU5ZGJhNGZiMTE2ZTBlYzdkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Gb3Jlc3QgSGlsbCBTRSwgQ2VudHJhbCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83ZjcyMjMxMDgyMTc0Njg2YmRjODAwNTg1NTNjOWUyOS5zZXRDb250ZW50KGh0bWxfNTM4OTI0ZThiZDQ0NGQ1ZTlkYmE0ZmIxMTZlMGVjN2QpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYWYwYjAwN2E2N2IyNDI3MmIyMWFiMDI1MjY4NmVhYjMuYmluZFBvcHVwKHBvcHVwXzdmNzIyMzEwODIxNzQ2ODZiZGM4MDA1ODU1M2M5ZTI5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2VmY2I4ZTg4YjI4MDRhNTViZTMzMzk1NDk1ZDY5NjNhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg2NDEyMjk5OTk5OTksLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTExMWNhMjE2ZWNjNDlkMTkyYjUyZDY5Njk2ZGJmMDkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDYyYmQ3YWEzOWMxNDk1MTk0ODZmMjgxNjFkMzk4NzMgPSAkKCc8ZGl2IGlkPSJodG1sX2Q2MmJkN2FhMzljMTQ5NTE5NDg2ZjI4MTYxZDM5ODczIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5SYXRobmVsbHksIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTExMWNhMjE2ZWNjNDlkMTkyYjUyZDY5Njk2ZGJmMDkuc2V0Q29udGVudChodG1sX2Q2MmJkN2FhMzljMTQ5NTE5NDg2ZjI4MTYxZDM5ODczKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2VmY2I4ZTg4YjI4MDRhNTViZTMzMzk1NDk1ZDY5NjNhLmJpbmRQb3B1cChwb3B1cF8xMTExY2EyMTZlY2M0OWQxOTJiNTJkNjk2OTZkYmYwOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iNmRkYTZjNzZmNGE0NjY3OGNlMWZhMWNlMDM1NTZhNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY4NjQxMjI5OTk5OTk5LC03OS40MDAwNDkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzBlMzNiMThkODdmZTQ0ZjU5M2MwNGUyMTRmNmZjOWRkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2U0NzM1ZDBjYmQ2MTQwNjhiZjlkYmYwZmE4NTcyMjdlID0gJCgnPGRpdiBpZD0iaHRtbF9lNDczNWQwY2JkNjE0MDY4YmY5ZGJmMGZhODU3MjI3ZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U291dGggSGlsbCwgQ2VudHJhbCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wZTMzYjE4ZDg3ZmU0NGY1OTNjMDRlMjE0ZjZmYzlkZC5zZXRDb250ZW50KGh0bWxfZTQ3MzVkMGNiZDYxNDA2OGJmOWRiZjBmYTg1NzIyN2UpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYjZkZGE2Yzc2ZjRhNDY2NzhjZTFmYTFjZTAzNTU2YTUuYmluZFBvcHVwKHBvcHVwXzBlMzNiMThkODdmZTQ0ZjU5M2MwNGUyMTRmNmZjOWRkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2Y4Y2I5YTYwMDQxNjQwNDQ5OTJjOTg2NmI4NzJhMDk0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg2NDEyMjk5OTk5OTksLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTdlOTUxZjc0MDgyNDg5ODk5OTI5ZDhjN2YzZjQ3NzEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjgzNjIwMmMwMmU0NDAwMWI4ZjZkOTUwN2U4YWQ3NjIgPSAkKCc8ZGl2IGlkPSJodG1sXzY4MzYyMDJjMDJlNDQwMDFiOGY2ZDk1MDdlOGFkNzYyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdW1tZXJoaWxsIFdlc3QsIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTdlOTUxZjc0MDgyNDg5ODk5OTI5ZDhjN2YzZjQ3NzEuc2V0Q29udGVudChodG1sXzY4MzYyMDJjMDJlNDQwMDFiOGY2ZDk1MDdlOGFkNzYyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Y4Y2I5YTYwMDQxNjQwNDQ5OTJjOTg2NmI4NzJhMDk0LmJpbmRQb3B1cChwb3B1cF8xN2U5NTFmNzQwODI0ODk4OTk5MjlkOGM3ZjNmNDc3MSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zZmUxYzgwN2RmZmM0ZDkxYTUzNWI0Y2NiN2Y4NzI1OSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYyODk0NjcsLTc5LjM5NDQxOTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWU3MTJiN2MxMDYyNDBkNTgxMzk1MDk3YTZiMjU1ZDUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDkzYTM5ZDE3NzcxNGNlODg0MmNmMGViYzFlOTgwOTMgPSAkKCc8ZGl2IGlkPSJodG1sXzQ5M2EzOWQxNzc3MTRjZTg4NDJjZjBlYmMxZTk4MDkzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DTiBUb3dlciwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYWU3MTJiN2MxMDYyNDBkNTgxMzk1MDk3YTZiMjU1ZDUuc2V0Q29udGVudChodG1sXzQ5M2EzOWQxNzc3MTRjZTg4NDJjZjBlYmMxZTk4MDkzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzNmZTFjODA3ZGZmYzRkOTFhNTM1YjRjY2I3Zjg3MjU5LmJpbmRQb3B1cChwb3B1cF9hZTcxMmI3YzEwNjI0MGQ1ODEzOTUwOTdhNmIyNTVkNSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85NTg3YWEwYTdkOGU0MDBlOWNkNDRiN2E3ZjFkYThmYSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYyODk0NjcsLTc5LjM5NDQxOTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTIyY2M0NDgxOWU2NDJiZGEzMDEwMzg4ZjQ5MTM0ZTIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTMxYWE5Y2RlNTdiNGY5M2JjZDc0ZjkxZGM2ZjhmOGUgPSAkKCc8ZGl2IGlkPSJodG1sXzEzMWFhOWNkZTU3YjRmOTNiY2Q3NGY5MWRjNmY4ZjhlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CYXRodXJzdCBRdWF5LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85MjJjYzQ0ODE5ZTY0MmJkYTMwMTAzODhmNDkxMzRlMi5zZXRDb250ZW50KGh0bWxfMTMxYWE5Y2RlNTdiNGY5M2JjZDc0ZjkxZGM2ZjhmOGUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTU4N2FhMGE3ZDhlNDAwZTljZDQ0YjdhN2YxZGE4ZmEuYmluZFBvcHVwKHBvcHVwXzkyMmNjNDQ4MTllNjQyYmRhMzAxMDM4OGY0OTEzNGUyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzhhM2EzOWQyNzcyMTRmN2ZhYWQxNzFlNGI5NWJkMjMwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjI4OTQ2NywtNzkuMzk0NDE5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yZjI5NmRmYzBlZjM0YjkxYmExYTIwNzk3OGYzNmIyZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82Yzk5ZWYxMTQ5ZGE0YWI2YjkzODE4OTVjYmRkNjIwZiA9ICQoJzxkaXYgaWQ9Imh0bWxfNmM5OWVmMTE0OWRhNGFiNmI5MzgxODk1Y2JkZDYyMGYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPklzbGFuZCBhaXJwb3J0LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yZjI5NmRmYzBlZjM0YjkxYmExYTIwNzk3OGYzNmIyZi5zZXRDb250ZW50KGh0bWxfNmM5OWVmMTE0OWRhNGFiNmI5MzgxODk1Y2JkZDYyMGYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOGEzYTM5ZDI3NzIxNGY3ZmFhZDE3MWU0Yjk1YmQyMzAuYmluZFBvcHVwKHBvcHVwXzJmMjk2ZGZjMGVmMzRiOTFiYTFhMjA3OTc4ZjM2YjJmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2FiYzZmZDU1M2U4NjRjOTdiMmExNmM1ZTgyZWY5NGZlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjI4OTQ2NywtNzkuMzk0NDE5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hOTcxYTk1MWU3MTU0NDVjYWNlODA1MTQ5Y2UzMDIyYiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81ZGNhMmZmZWFiMWQ0MjQ2ODE4ZTE2MWM0NWIwYzUwNCA9ICQoJzxkaXYgaWQ9Imh0bWxfNWRjYTJmZmVhYjFkNDI0NjgxOGUxNjFjNDViMGM1MDQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhhcmJvdXJmcm9udCBXZXN0LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hOTcxYTk1MWU3MTU0NDVjYWNlODA1MTQ5Y2UzMDIyYi5zZXRDb250ZW50KGh0bWxfNWRjYTJmZmVhYjFkNDI0NjgxOGUxNjFjNDViMGM1MDQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYWJjNmZkNTUzZTg2NGM5N2IyYTE2YzVlODJlZjk0ZmUuYmluZFBvcHVwKHBvcHVwX2E5NzFhOTUxZTcxNTQ0NWNhY2U4MDUxNDljZTMwMjJiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzI0YTk4ZGNkYmVmMjQ1YzRiNGIyODUwYmNhZGI0NGUyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjI4OTQ2NywtNzkuMzk0NDE5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yMzQ3NWQ1MGJmMjA0Y2Q4YmU5NTZiMWVlMjRhMWUyMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jMGViMTNmMzc3Mzg0NmM4YTA3ODJiMzBiMTVjYjY0OCA9ICQoJzxkaXYgaWQ9Imh0bWxfYzBlYjEzZjM3NzM4NDZjOGEwNzgyYjMwYjE1Y2I2NDgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPktpbmcgYW5kIFNwYWRpbmEsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzIzNDc1ZDUwYmYyMDRjZDhiZTk1NmIxZWUyNGExZTIyLnNldENvbnRlbnQoaHRtbF9jMGViMTNmMzc3Mzg0NmM4YTA3ODJiMzBiMTVjYjY0OCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yNGE5OGRjZGJlZjI0NWM0YjRiMjg1MGJjYWRiNDRlMi5iaW5kUG9wdXAocG9wdXBfMjM0NzVkNTBiZjIwNGNkOGJlOTU2YjFlZTI0YTFlMjIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDJhMmUzMDE0NDBjNDRjZWExYTQ0YTY4NTI5MTYwOGUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Mjg5NDY3LC03OS4zOTQ0MTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzVkMWI4M2UxNWFkMjQyOTM4NGUxNGUzNzE5ZDQ1ODMxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzY0NjRlYjcwOTY1ZTRmMzliNzUxODM1ZTUwNTQ0YjVhID0gJCgnPGRpdiBpZD0iaHRtbF82NDY0ZWI3MDk2NWU0ZjM5Yjc1MTgzNWU1MDU0NGI1YSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UmFpbHdheSBMYW5kcywgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNWQxYjgzZTE1YWQyNDI5Mzg0ZTE0ZTM3MTlkNDU4MzEuc2V0Q29udGVudChodG1sXzY0NjRlYjcwOTY1ZTRmMzliNzUxODM1ZTUwNTQ0YjVhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2QyYTJlMzAxNDQwYzQ0Y2VhMWE0NGE2ODUyOTE2MDhlLmJpbmRQb3B1cChwb3B1cF81ZDFiODNlMTVhZDI0MjkzODRlMTRlMzcxOWQ0NTgzMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yMjdhYmEzMDY0YjA0Yjg1YWRjNDM4ZTlmN2NlODdjMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYyODk0NjcsLTc5LjM5NDQxOTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfY2M3MmFhZDJmNzQzNGY2Njg2MTY5NWVjZmVjNjBjNGYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzNhYWI4MDI3NDQ3NGEwNGE3NTMwNWE4YWQ1NGE1MWUgPSAkKCc8ZGl2IGlkPSJodG1sXzczYWFiODAyNzQ0NzRhMDRhNzUzMDVhOGFkNTRhNTFlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Tb3V0aCBOaWFnYXJhLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jYzcyYWFkMmY3NDM0ZjY2ODYxNjk1ZWNmZWM2MGM0Zi5zZXRDb250ZW50KGh0bWxfNzNhYWI4MDI3NDQ3NGEwNGE3NTMwNWE4YWQ1NGE1MWUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMjI3YWJhMzA2NGIwNGI4NWFkYzQzOGU5ZjdjZTg3YzIuYmluZFBvcHVwKHBvcHVwX2NjNzJhYWQyZjc0MzRmNjY4NjE2OTVlY2ZlYzYwYzRmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzNlNjdlODRmZDg3MTQ5YTBiYmM1MGYzZDk5YjI1YjA2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjA1NjQ2NiwtNzkuNTAxMzIwNzAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzkzNWFjYzBmMTQ2NDkxYjgzMTJkNzIyMGFhNzliZjggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZmExNjQwZjUyOWJjNDM2Y2JkY2ZlNDg2OWVjNTMxN2EgPSAkKCc8ZGl2IGlkPSJodG1sX2ZhMTY0MGY1MjliYzQzNmNiZGNmZTQ4NjllYzUzMTdhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IdW1iZXIgQmF5IFNob3JlcywgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jOTM1YWNjMGYxNDY0OTFiODMxMmQ3MjIwYWE3OWJmOC5zZXRDb250ZW50KGh0bWxfZmExNjQwZjUyOWJjNDM2Y2JkY2ZlNDg2OWVjNTMxN2EpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfM2U2N2U4NGZkODcxNDlhMGJiYzUwZjNkOTliMjViMDYuYmluZFBvcHVwKHBvcHVwX2M5MzVhY2MwZjE0NjQ5MWI4MzEyZDcyMjBhYTc5YmY4KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2I5NTFlNTVhMWRlNDQ2N2RhOWYyZWZkNTVlNjk0MzU1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjA1NjQ2NiwtNzkuNTAxMzIwNzAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNjFkYzdjZGJhNTk4NDI1ZDg4MGRjZGEwZjRkZmYzMjEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWQxNTczZTY4MmZiNDEwYWEwZjc1NTJiZjg3MjVkODcgPSAkKCc8ZGl2IGlkPSJodG1sXzFkMTU3M2U2ODJmYjQxMGFhMGY3NTUyYmY4NzI1ZDg3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5NaW1pY28gU291dGgsIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjFkYzdjZGJhNTk4NDI1ZDg4MGRjZGEwZjRkZmYzMjEuc2V0Q29udGVudChodG1sXzFkMTU3M2U2ODJmYjQxMGFhMGY3NTUyYmY4NzI1ZDg3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2I5NTFlNTVhMWRlNDQ2N2RhOWYyZWZkNTVlNjk0MzU1LmJpbmRQb3B1cChwb3B1cF82MWRjN2NkYmE1OTg0MjVkODgwZGNkYTBmNGRmZjMyMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kNDNiYzVkYjNmMzg0OWU5YTQ1ZTI0NjgxZDM0Y2Q0NyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYwNTY0NjYsLTc5LjUwMTMyMDcwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzA1Y2I1OTBiOWMzZjRjMmVhNGYwYzg4ZjBiODgwN2QzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzBiNjk0YmRlMTNkMDQ3YjI4Y2I0MzNiNjhmMmUyNTM4ID0gJCgnPGRpdiBpZD0iaHRtbF8wYjY5NGJkZTEzZDA0N2IyOGNiNDMzYjY4ZjJlMjUzOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TmV3IFRvcm9udG8sIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDVjYjU5MGI5YzNmNGMyZWE0ZjBjODhmMGI4ODA3ZDMuc2V0Q29udGVudChodG1sXzBiNjk0YmRlMTNkMDQ3YjI4Y2I0MzNiNjhmMmUyNTM4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Q0M2JjNWRiM2YzODQ5ZTlhNDVlMjQ2ODFkMzRjZDQ3LmJpbmRQb3B1cChwb3B1cF8wNWNiNTkwYjljM2Y0YzJlYTRmMGM4OGYwYjg4MDdkMyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84NWY2MTliYzMzNmY0NzNlOGRmNTY3ZjM1ZjQyNDA3MiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjczOTQxNjM5OTk5OTk5NiwtNzkuNTg4NDM2OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iODRlOGRiZDExYjk0NTA4YmEzN2U0ZjYxYzI3NmIxZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zMGRiN2FjMWFiMzM0ZThjODk2ODE4OWZiNDhkZmJmMCA9ICQoJzxkaXYgaWQ9Imh0bWxfMzBkYjdhYzFhYjMzNGU4Yzg5NjgxODlmYjQ4ZGZiZjAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkFsYmlvbiBHYXJkZW5zLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2I4NGU4ZGJkMTFiOTQ1MDhiYTM3ZTRmNjFjMjc2YjFkLnNldENvbnRlbnQoaHRtbF8zMGRiN2FjMWFiMzM0ZThjODk2ODE4OWZiNDhkZmJmMCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84NWY2MTliYzMzNmY0NzNlOGRmNTY3ZjM1ZjQyNDA3Mi5iaW5kUG9wdXAocG9wdXBfYjg0ZThkYmQxMWI5NDUwOGJhMzdlNGY2MWMyNzZiMWQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTg3YWU1NmU5ZjJhNGQ2OTk2ZjNmNjhhOTYyMGFjYzQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Mzk0MTYzOTk5OTk5OTYsLTc5LjU4ODQzNjldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNjY3MTdmY2IyN2IwNGE3MThiNTM3M2EzZTE5MTFhNTggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTIyNWQ1YmU3MjE0NGEzNGFiYTcwNGUxYjIyZGI4YTIgPSAkKCc8ZGl2IGlkPSJodG1sXzEyMjVkNWJlNzIxNDRhMzRhYmE3MDRlMWIyMmRiOGEyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CZWF1bW9uZCBIZWlnaHRzLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzY2NzE3ZmNiMjdiMDRhNzE4YjUzNzNhM2UxOTExYTU4LnNldENvbnRlbnQoaHRtbF8xMjI1ZDViZTcyMTQ0YTM0YWJhNzA0ZTFiMjJkYjhhMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85ODdhZTU2ZTlmMmE0ZDY5OTZmM2Y2OGE5NjIwYWNjNC5iaW5kUG9wdXAocG9wdXBfNjY3MTdmY2IyN2IwNGE3MThiNTM3M2EzZTE5MTFhNTgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjBhMTEyZmIwYjMwNDI0OWJhMmIwNTJmMTRhMTA4MzQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Mzk0MTYzOTk5OTk5OTYsLTc5LjU4ODQzNjldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNThjMDRmNGQ0NDU2NDdhODg0MzU2ZDgzMWQ1MjMyNGMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDY0YjdjMTkwMDUyNDlmYTkxZjUzNzY5NDk4YTQ0OTUgPSAkKCc8ZGl2IGlkPSJodG1sXzQ2NGI3YzE5MDA1MjQ5ZmE5MWY1Mzc2OTQ5OGE0NDk1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IdW1iZXJnYXRlLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzU4YzA0ZjRkNDQ1NjQ3YTg4NDM1NmQ4MzFkNTIzMjRjLnNldENvbnRlbnQoaHRtbF80NjRiN2MxOTAwNTI0OWZhOTFmNTM3Njk0OThhNDQ5NSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mMGExMTJmYjBiMzA0MjQ5YmEyYjA1MmYxNGExMDgzNC5iaW5kUG9wdXAocG9wdXBfNThjMDRmNGQ0NDU2NDdhODg0MzU2ZDgzMWQ1MjMyNGMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNTQyZjcxZDA1YTdjNDUyYjgyMGFmMWY2YmIwZDU3ZTIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Mzk0MTYzOTk5OTk5OTYsLTc5LjU4ODQzNjldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTM3YjkwNjE5NDhiNGMwNGE0NzdlNzE2ZDI3ODcwZDkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfM2E1MDU0MDI5MDdiNGI3OTg5MjliMDdmYjE4NjFkZDYgPSAkKCc8ZGl2IGlkPSJodG1sXzNhNTA1NDAyOTA3YjRiNzk4OTI5YjA3ZmIxODYxZGQ2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5KYW1lc3Rvd24sIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTM3YjkwNjE5NDhiNGMwNGE0NzdlNzE2ZDI3ODcwZDkuc2V0Q29udGVudChodG1sXzNhNTA1NDAyOTA3YjRiNzk4OTI5YjA3ZmIxODYxZGQ2KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzU0MmY3MWQwNWE3YzQ1MmI4MjBhZjFmNmJiMGQ1N2UyLmJpbmRQb3B1cChwb3B1cF9lMzdiOTA2MTk0OGI0YzA0YTQ3N2U3MTZkMjc4NzBkOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84ZTFiYjQzNGZkZGU0OGU4YTUzMjI4MGE4Njk1ZWQyMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjczOTQxNjM5OTk5OTk5NiwtNzkuNTg4NDM2OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iMTk1YjExZmQ5MDI0YzUwOTMxOTRkMzg2MTM3NzA0OCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81MmUyZWIxZWFiMWE0NjRhYTg5MWMzMDUwNzE0NGQ1YyA9ICQoJzxkaXYgaWQ9Imh0bWxfNTJlMmViMWVhYjFhNDY0YWE4OTFjMzA1MDcxNDRkNWMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1vdW50IE9saXZlLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2IxOTViMTFmZDkwMjRjNTA5MzE5NGQzODYxMzc3MDQ4LnNldENvbnRlbnQoaHRtbF81MmUyZWIxZWFiMWE0NjRhYTg5MWMzMDUwNzE0NGQ1Yyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84ZTFiYjQzNGZkZGU0OGU4YTUzMjI4MGE4Njk1ZWQyMS5iaW5kUG9wdXAocG9wdXBfYjE5NWIxMWZkOTAyNGM1MDkzMTk0ZDM4NjEzNzcwNDgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDAyMDQ5YjI3N2QwNGY2ZjhiYmQxNzNlZDM4MTM4OWMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43Mzk0MTYzOTk5OTk5OTYsLTc5LjU4ODQzNjldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDMwZTRmNzM4ZDhlNGQyOWExZGY3YzNiODc5NzQ4ZjMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOWRjODg4MDExYTdmNDM3NThmZGM5YjY3NjZhYmUzYjcgPSAkKCc8ZGl2IGlkPSJodG1sXzlkYzg4ODAxMWE3ZjQzNzU4ZmRjOWI2NzY2YWJlM2I3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TaWx2ZXJzdG9uZSwgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80MzBlNGY3MzhkOGU0ZDI5YTFkZjdjM2I4Nzk3NDhmMy5zZXRDb250ZW50KGh0bWxfOWRjODg4MDExYTdmNDM3NThmZGM5YjY3NjZhYmUzYjcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDAyMDQ5YjI3N2QwNGY2ZjhiYmQxNzNlZDM4MTM4OWMuYmluZFBvcHVwKHBvcHVwXzQzMGU0ZjczOGQ4ZTRkMjlhMWRmN2MzYjg3OTc0OGYzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzMyMDExZjZkZmRmZjQyMDRhOGNkYzE4MjgwYzZhOThiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzM5NDE2Mzk5OTk5OTk2LC03OS41ODg0MzY5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzNhYTJlZGQwN2JjODQ2ZjViYzUzNGIwNzI2Njg4NDczID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Y0N2JiNDM3YWYyMzQzMzM5OGExOTAxM2YyMDhlZWM0ID0gJCgnPGRpdiBpZD0iaHRtbF9mNDdiYjQzN2FmMjM0MzMzOThhMTkwMTNmMjA4ZWVjNCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U291dGggU3RlZWxlcywgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zYWEyZWRkMDdiYzg0NmY1YmM1MzRiMDcyNjY4ODQ3My5zZXRDb250ZW50KGh0bWxfZjQ3YmI0MzdhZjIzNDMzMzk4YTE5MDEzZjIwOGVlYzQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMzIwMTFmNmRmZGZmNDIwNGE4Y2RjMTgyODBjNmE5OGIuYmluZFBvcHVwKHBvcHVwXzNhYTJlZGQwN2JjODQ2ZjViYzUzNGIwNzI2Njg4NDczKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2FjNDI3ODFhODY1YjRjZjc4ZGJjODk3MWRmMmNiZDhlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzM5NDE2Mzk5OTk5OTk2LC03OS41ODg0MzY5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzc1NDUxOWM3YzU2MTQ0YmJhNGVkYjExZDkzMDNlOTAwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzQzOWY4YWYxNjRlNTQ5ZDlhMzUwYzgzNjcyYmQzZTQ2ID0gJCgnPGRpdiBpZD0iaHRtbF80MzlmOGFmMTY0ZTU0OWQ5YTM1MGM4MzY3MmJkM2U0NiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VGhpc3RsZXRvd24sIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzU0NTE5YzdjNTYxNDRiYmE0ZWRiMTFkOTMwM2U5MDAuc2V0Q29udGVudChodG1sXzQzOWY4YWYxNjRlNTQ5ZDlhMzUwYzgzNjcyYmQzZTQ2KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2FjNDI3ODFhODY1YjRjZjc4ZGJjODk3MWRmMmNiZDhlLmJpbmRQb3B1cChwb3B1cF83NTQ1MTljN2M1NjE0NGJiYTRlZGIxMWQ5MzAzZTkwMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hOTJhZjIyZjZjMTQ0Nzc1YWE5ODc4MWU1ODgwYzlhOSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjc5OTUyNTIwMDAwMDAwNSwtNzkuMzE4Mzg4N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85OGZhNDAzYWVmOTk0NjE0YWI3Y2EyOGYzZDY1ZTdkYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zNzI2MmUyM2VmNTQ0NjdjYTI5ZGZlNWFkODdjZjI4MiA9ICQoJzxkaXYgaWQ9Imh0bWxfMzcyNjJlMjNlZjU0NDY3Y2EyOWRmZTVhZDg3Y2YyODIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkwmIzM5O0Ftb3JlYXV4IFdlc3QsIFNjYXJib3JvdWdoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85OGZhNDAzYWVmOTk0NjE0YWI3Y2EyOGYzZDY1ZTdkYy5zZXRDb250ZW50KGh0bWxfMzcyNjJlMjNlZjU0NDY3Y2EyOWRmZTVhZDg3Y2YyODIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYTkyYWYyMmY2YzE0NDc3NWFhOTg3ODFlNTg4MGM5YTkuYmluZFBvcHVwKHBvcHVwXzk4ZmE0MDNhZWY5OTQ2MTRhYjdjYTI4ZjNkNjVlN2RjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzA4ZTE0ZjU2YWY0ZjQ3ZTBhODllMTJhNzEwNTI1OWY2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjc5NTYyNiwtNzkuMzc3NTI5NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZWMxOWNmZGJlOWM4NGZlZDkwNmQyOTBmYjI2NGViYzAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYmFmYzE0MDEzNzk4NGI3MDhhZDk2NmY5ODM4ODJiNjggPSAkKCc8ZGl2IGlkPSJodG1sX2JhZmMxNDAxMzc5ODRiNzA4YWQ5NjZmOTgzODgyYjY4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Sb3NlZGFsZSwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZWMxOWNmZGJlOWM4NGZlZDkwNmQyOTBmYjI2NGViYzAuc2V0Q29udGVudChodG1sX2JhZmMxNDAxMzc5ODRiNzA4YWQ5NjZmOTgzODgyYjY4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzA4ZTE0ZjU2YWY0ZjQ3ZTBhODllMTJhNzEwNTI1OWY2LmJpbmRQb3B1cChwb3B1cF9lYzE5Y2ZkYmU5Yzg0ZmVkOTA2ZDI5MGZiMjY0ZWJjMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83ZmQxNWFkMjBkMTU0MzQ4YjZmYjEyZGQ1MWVhM2NjMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NjQzNTIsLTc5LjM3NDg0NTk5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzVlY2FiOTY5YTcyYTQ0ZGQ4ZmE4YzA5N2NlYjVmMGFjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzc3M2NlNTkzYmI3NTRmYjhhZWMyZTkyZGYzODVmMWI2ID0gJCgnPGRpdiBpZD0iaHRtbF83NzNjZTU5M2JiNzU0ZmI4YWVjMmU5MmRmMzg1ZjFiNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U3RuIEEgUE8gQm94ZXMgMjUgVGhlIEVzcGxhbmFkZSwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNWVjYWI5NjlhNzJhNDRkZDhmYThjMDk3Y2ViNWYwYWMuc2V0Q29udGVudChodG1sXzc3M2NlNTkzYmI3NTRmYjhhZWMyZTkyZGYzODVmMWI2KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzdmZDE1YWQyMGQxNTQzNDhiNmZiMTJkZDUxZWEzY2MyLmJpbmRQb3B1cChwb3B1cF81ZWNhYjk2OWE3MmE0NGRkOGZhOGMwOTdjZWI1ZjBhYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lZWViNDNhYTM2ZjU0NDZjYTg1NDcxMDdmOWE2YzUyMyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYwMjQxMzcwMDAwMDAxLC03OS41NDM0ODQwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wZGQ0ZTBjMzhkY2E0MmU4YjVjZWZkMTI4NmJkYzkyYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jZWViNWRjYTQ3MDY0YzhmYTcyYmI2ZDVlYzRjN2JiNyA9ICQoJzxkaXYgaWQ9Imh0bWxfY2VlYjVkY2E0NzA2NGM4ZmE3MmJiNmQ1ZWM0YzdiYjciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkFsZGVyd29vZCwgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wZGQ0ZTBjMzhkY2E0MmU4YjVjZWZkMTI4NmJkYzkyYS5zZXRDb250ZW50KGh0bWxfY2VlYjVkY2E0NzA2NGM4ZmE3MmJiNmQ1ZWM0YzdiYjcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZWVlYjQzYWEzNmY1NDQ2Y2E4NTQ3MTA3ZjlhNmM1MjMuYmluZFBvcHVwKHBvcHVwXzBkZDRlMGMzOGRjYTQyZThiNWNlZmQxMjg2YmRjOTJhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzYwY2E4MzhjOGNkYTQxMWViZjUzYWUwYWM3OTRmNWU4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjAyNDEzNzAwMDAwMDEsLTc5LjU0MzQ4NDA5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2I1ZDllZGNlZjgxMTRhNTVhZGRhNjEyMjNhNTVmZTVhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzJjMzNjNmYwYTljZjQwYzk5NDQ3ZGVkZGExNGIzZWU1ID0gJCgnPGRpdiBpZD0iaHRtbF8yYzMzYzZmMGE5Y2Y0MGM5OTQ0N2RlZGRhMTRiM2VlNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TG9uZyBCcmFuY2gsIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjVkOWVkY2VmODExNGE1NWFkZGE2MTIyM2E1NWZlNWEuc2V0Q29udGVudChodG1sXzJjMzNjNmYwYTljZjQwYzk5NDQ3ZGVkZGExNGIzZWU1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzYwY2E4MzhjOGNkYTQxMWViZjUzYWUwYWM3OTRmNWU4LmJpbmRQb3B1cChwb3B1cF9iNWQ5ZWRjZWY4MTE0YTU1YWRkYTYxMjIzYTU1ZmU1YSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wZjA2M2VkOTcxNDQ0NzczOWQ3YzRiNWFmNjg0OGM3YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcwNjc0ODI5OTk5OTk5NCwtNzkuNTk0MDU0NF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lYmFkYTgxMWY2OGI0MjVjOGQyOTE1NTNiOTcwN2MyNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xYzMwYzI4OTNmNWE0NjRjYWY0NDNlMGY2Njk1MjI4NSA9ICQoJzxkaXYgaWQ9Imh0bWxfMWMzMGMyODkzZjVhNDY0Y2FmNDQzZTBmNjY5NTIyODUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5vcnRod2VzdCwgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lYmFkYTgxMWY2OGI0MjVjOGQyOTE1NTNiOTcwN2MyNS5zZXRDb250ZW50KGh0bWxfMWMzMGMyODkzZjVhNDY0Y2FmNDQzZTBmNjY5NTIyODUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMGYwNjNlZDk3MTQ0NDc3MzlkN2M0YjVhZjY4NDhjN2EuYmluZFBvcHVwKHBvcHVwX2ViYWRhODExZjY4YjQyNWM4ZDI5MTU1M2I5NzA3YzI1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzFlNTdjMmZmOGJjMjQ0YzE5ODZkYjE5Y2UxM2QzNWE4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuODM2MTI0NzAwMDAwMDA2LC03OS4yMDU2MzYwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xYjZjY2ZjMWViNDM0MTQ4OTJjMjc0MWRmYzYyMzAxNCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jMTg0MmE5NTJhZjI0ODA1YTVhMTExZWYxZTZiZDI3ZCA9ICQoJzxkaXYgaWQ9Imh0bWxfYzE4NDJhOTUyYWYyNDgwNWE1YTExMWVmMWU2YmQyN2QiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlVwcGVyIFJvdWdlLCBTY2FyYm9yb3VnaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMWI2Y2NmYzFlYjQzNDE0ODkyYzI3NDFkZmM2MjMwMTQuc2V0Q29udGVudChodG1sX2MxODQyYTk1MmFmMjQ4MDVhNWExMTFlZjFlNmJkMjdkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzFlNTdjMmZmOGJjMjQ0YzE5ODZkYjE5Y2UxM2QzNWE4LmJpbmRQb3B1cChwb3B1cF8xYjZjY2ZjMWViNDM0MTQ4OTJjMjc0MWRmYzYyMzAxNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wOWM2MDY0MDcxZTY0NDdhYjVlMjQyYTcwMGMzZGQ3YiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2Nzk2NywtNzkuMzY3Njc1M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83YTk5NDRmZGRiNDE0MmNiOTBlZGExZWRkYzcxNzZkMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zNjdjMzA2MDQwYmE0N2I5OGQwZDk4NmMyZDYyYmYyYyA9ICQoJzxkaXYgaWQ9Imh0bWxfMzY3YzMwNjA0MGJhNDdiOThkMGQ5ODZjMmQ2MmJmMmMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNhYmJhZ2V0b3duLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83YTk5NDRmZGRiNDE0MmNiOTBlZGExZWRkYzcxNzZkMi5zZXRDb250ZW50KGh0bWxfMzY3YzMwNjA0MGJhNDdiOThkMGQ5ODZjMmQ2MmJmMmMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDljNjA2NDA3MWU2NDQ3YWI1ZTI0MmE3MDBjM2RkN2IuYmluZFBvcHVwKHBvcHVwXzdhOTk0NGZkZGI0MTQyY2I5MGVkYTFlZGRjNzE3NmQyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU2Zjk2MWE2MGZiMzRkODRhY2U1OWUyOGE1NGEwNDE1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY3OTY3LC03OS4zNjc2NzUzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzk4ODc0M2Q3MDEwZjQ4ODE4Yzc4ZDY3YjUxYWMxMDMwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzNhN2Q5ZTc4ZTU5MDQ4NWVhYzIyMGRkODZhYzhkOWI2ID0gJCgnPGRpdiBpZD0iaHRtbF8zYTdkOWU3OGU1OTA0ODVlYWMyMjBkZDg2YWM4ZDliNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U3QuIEphbWVzIFRvd24sIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzk4ODc0M2Q3MDEwZjQ4ODE4Yzc4ZDY3YjUxYWMxMDMwLnNldENvbnRlbnQoaHRtbF8zYTdkOWU3OGU1OTA0ODVlYWMyMjBkZDg2YWM4ZDliNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81NmY5NjFhNjBmYjM0ZDg0YWNlNTllMjhhNTRhMDQxNS5iaW5kUG9wdXAocG9wdXBfOTg4NzQzZDcwMTBmNDg4MThjNzhkNjdiNTFhYzEwMzApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODVjOWVlMzdkMjU3NDk1NzliYmZmZDFiZTRmYWExOGIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDg0MjkyLC03OS4zODIyODAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2M5NjQzM2JkMmY3YzQ4NmM5NTJhN2MzZTEwM2NiMDQ5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2E2NWRmZTQ0ZGM2ODRjNDc4YTlkNzNmN2UwYWM2NWQ4ID0gJCgnPGRpdiBpZD0iaHRtbF9hNjVkZmU0NGRjNjg0YzQ3OGE5ZDczZjdlMGFjNjVkOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Rmlyc3QgQ2FuYWRpYW4gUGxhY2UsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2M5NjQzM2JkMmY3YzQ4NmM5NTJhN2MzZTEwM2NiMDQ5LnNldENvbnRlbnQoaHRtbF9hNjVkZmU0NGRjNjg0YzQ3OGE5ZDczZjdlMGFjNjVkOCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84NWM5ZWUzN2QyNTc0OTU3OWJiZmZkMWJlNGZhYTE4Yi5iaW5kUG9wdXAocG9wdXBfYzk2NDMzYmQyZjdjNDg2Yzk1MmE3YzNlMTAzY2IwNDkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzc5ZjQ2MTFhMTA5NDE4MzlmYzMzZDc1ZDg5ZDNjNDQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDg0MjkyLC03OS4zODIyODAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2I3ZGQxOGZjMTQ1MTRlOTg5OTRkZWRiOWNlMTk1OTQ1ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzZkNGJkODI4NTVhZTQ2ZjE4N2VlOTViYzM1MmQ1NDRlID0gJCgnPGRpdiBpZD0iaHRtbF82ZDRiZDgyODU1YWU0NmYxODdlZTk1YmMzNTJkNTQ0ZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VW5kZXJncm91bmQgY2l0eSwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjdkZDE4ZmMxNDUxNGU5ODk5NGRlZGI5Y2UxOTU5NDUuc2V0Q29udGVudChodG1sXzZkNGJkODI4NTVhZTQ2ZjE4N2VlOTViYzM1MmQ1NDRlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2M3OWY0NjExYTEwOTQxODM5ZmMzM2Q3NWQ4OWQzYzQ0LmJpbmRQb3B1cChwb3B1cF9iN2RkMThmYzE0NTE0ZTk4OTk0ZGVkYjljZTE5NTk0NSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lYTAyYTY3N2Y1MmY0ODQ5ODIzNTIxMTkyZDgwZTc2YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MzY1MzYwMDAwMDAwNSwtNzkuNTA2OTQzNl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ODU3MzUwMmUwNWU0YjYzYmYwZTczOTg5YjgyY2E2ZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80ZmRiMzZjNDUwNjg0M2NmYmNjNWVjMWEzMDA1OGZjZSA9ICQoJzxkaXYgaWQ9Imh0bWxfNGZkYjM2YzQ1MDY4NDNjZmJjYzVlYzFhMzAwNThmY2UiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRoZSBLaW5nc3dheSwgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83ODU3MzUwMmUwNWU0YjYzYmYwZTczOTg5YjgyY2E2ZC5zZXRDb250ZW50KGh0bWxfNGZkYjM2YzQ1MDY4NDNjZmJjYzVlYzFhMzAwNThmY2UpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZWEwMmE2NzdmNTJmNDg0OTgyMzUyMTE5MmQ4MGU3NmEuYmluZFBvcHVwKHBvcHVwXzc4NTczNTAyZTA1ZTRiNjNiZjBlNzM5ODliODJjYTZkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2IxNTNmNWM1OWQwZjRjYTA5NTAzYjA4ZTZkOWI2MDc0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUzNjUzNjAwMDAwMDA1LC03OS41MDY5NDM2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzRlYjI3ODEyYWY4YzQwNjBhNDRlNWQ1NDY5NGZmYzUyID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2E5YTM4ZGU0YWZlMjQ0MTc4NTNjY2JjNTI3MTYzZTcxID0gJCgnPGRpdiBpZD0iaHRtbF9hOWEzOGRlNGFmZTI0NDE3ODUzY2NiYzUyNzE2M2U3MSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TW9udGdvbWVyeSBSb2FkLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzRlYjI3ODEyYWY4YzQwNjBhNDRlNWQ1NDY5NGZmYzUyLnNldENvbnRlbnQoaHRtbF9hOWEzOGRlNGFmZTI0NDE3ODUzY2NiYzUyNzE2M2U3MSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iMTUzZjVjNTlkMGY0Y2EwOTUwM2IwOGU2ZDliNjA3NC5iaW5kUG9wdXAocG9wdXBfNGViMjc4MTJhZjhjNDA2MGE0NGU1ZDU0Njk0ZmZjNTIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDcwZTAwMmQ2ZGJhNDUyYjgzZDk2Yjc2MmUyZWIwMmYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTM2NTM2MDAwMDAwMDUsLTc5LjUwNjk0MzZdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzQ4N2VlMjQ0YjFlNGVkZDk1Mzc0ODE0NWZkYzA3MGMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDY1ZWFkMmY2Y2EzNDQ2MGFjYzEyYWNkZDlhZGM1NjAgPSAkKCc8ZGl2IGlkPSJodG1sXzQ2NWVhZDJmNmNhMzQ0NjBhY2MxMmFjZGQ5YWRjNTYwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5PbGQgTWlsbCBOb3J0aCwgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jNDg3ZWUyNDRiMWU0ZWRkOTUzNzQ4MTQ1ZmRjMDcwYy5zZXRDb250ZW50KGh0bWxfNDY1ZWFkMmY2Y2EzNDQ2MGFjYzEyYWNkZDlhZGM1NjApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDcwZTAwMmQ2ZGJhNDUyYjgzZDk2Yjc2MmUyZWIwMmYuYmluZFBvcHVwKHBvcHVwX2M0ODdlZTI0NGIxZTRlZGQ5NTM3NDgxNDVmZGMwNzBjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzMxNTgyMWJjZDc3ZTQ0MDE4M2E4NmM0YjJiNDQyYjQ5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY1ODU5OSwtNzkuMzgzMTU5OTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMGFhMzIyM2JmNmJiNGE5YWJjMjExZmI3ZWQ4YmU5ZTcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzg5NGMxYjJiMTUyNDQ5ZDg0YjhkN2E3ZTY2NzUwMmMgPSAkKCc8ZGl2IGlkPSJodG1sXzc4OTRjMWIyYjE1MjQ0OWQ4NGI4ZDdhN2U2Njc1MDJjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DaHVyY2ggYW5kIFdlbGxlc2xleSwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMGFhMzIyM2JmNmJiNGE5YWJjMjExZmI3ZWQ4YmU5ZTcuc2V0Q29udGVudChodG1sXzc4OTRjMWIyYjE1MjQ0OWQ4NGI4ZDdhN2U2Njc1MDJjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzMxNTgyMWJjZDc3ZTQ0MDE4M2E4NmM0YjJiNDQyYjQ5LmJpbmRQb3B1cChwb3B1cF8wYWEzMjIzYmY2YmI0YTlhYmMyMTFmYjdlZDhiZTllNyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wM2VhODMzNDZiOGI0OWJjYjA1YWFkZmMxODYwNDllYSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2Mjc0MzksLTc5LjMyMTU1OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yMTNkMTI5ZGJjOWY0M2Y2OWYxZjkyNzE3MDI3YzYyZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jMTRmZGVlM2QzOWQ0M2EwOGQ1NTk3Mzk5MTQwNjc4OSA9ICQoJzxkaXYgaWQ9Imh0bWxfYzE0ZmRlZTNkMzlkNDNhMDhkNTU5NzM5OTE0MDY3ODkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJ1c2luZXNzIFJlcGx5IE1haWwgUHJvY2Vzc2luZyBDZW50cmUgOTY5IEVhc3Rlcm4sIEVhc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjEzZDEyOWRiYzlmNDNmNjlmMWY5MjcxNzAyN2M2MmYuc2V0Q29udGVudChodG1sX2MxNGZkZWUzZDM5ZDQzYTA4ZDU1OTczOTkxNDA2Nzg5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzAzZWE4MzM0NmI4YjQ5YmNiMDVhYWRmYzE4NjA0OWVhLmJpbmRQb3B1cChwb3B1cF8yMTNkMTI5ZGJjOWY0M2Y2OWYxZjkyNzE3MDI3YzYyZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zOWM3Njc1NTc1YTU0OTg4OTQ2OGQwZWQyZDg3MTAxMCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYzNjI1NzksLTc5LjQ5ODUwOTA5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2UxZTk0MGIyMjExMzQ1Mzg4NDczYmQxMjcwNzdmNTU5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzViZWQzNjk3YjYyNzRkMjJhN2EzOTUwMTEwOWE1OWY0ID0gJCgnPGRpdiBpZD0iaHRtbF81YmVkMzY5N2I2Mjc0ZDIyYTdhMzk1MDExMDlhNTlmNCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SHVtYmVyIEJheSwgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lMWU5NDBiMjIxMTM0NTM4ODQ3M2JkMTI3MDc3ZjU1OS5zZXRDb250ZW50KGh0bWxfNWJlZDM2OTdiNjI3NGQyMmE3YTM5NTAxMTA5YTU5ZjQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMzljNzY3NTU3NWE1NDk4ODk0NjhkMGVkMmQ4NzEwMTAuYmluZFBvcHVwKHBvcHVwX2UxZTk0MGIyMjExMzQ1Mzg4NDczYmQxMjcwNzdmNTU5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzAyMDM5ZmMyNTliNTRjY2NiYWM4ZmI3NGE1N2NmMjlkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjM2MjU3OSwtNzkuNDk4NTA5MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZGVkY2VkNmFiMTEwNDAxMmI5NjBmYjNkNzE5ZDA0NDIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzBkY2U1Y2MzMDc1NGIzNGE1MWU3OTc1MThmMmEyZDYgPSAkKCc8ZGl2IGlkPSJodG1sXzMwZGNlNWNjMzA3NTRiMzRhNTFlNzk3NTE4ZjJhMmQ2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5LaW5nJiMzOTtzIE1pbGwgUGFyaywgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kZWRjZWQ2YWIxMTA0MDEyYjk2MGZiM2Q3MTlkMDQ0Mi5zZXRDb250ZW50KGh0bWxfMzBkY2U1Y2MzMDc1NGIzNGE1MWU3OTc1MThmMmEyZDYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDIwMzlmYzI1OWI1NGNjY2JhYzhmYjc0YTU3Y2YyOWQuYmluZFBvcHVwKHBvcHVwX2RlZGNlZDZhYjExMDQwMTJiOTYwZmIzZDcxOWQwNDQyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzIwODg2MTM1MDEwMzQwOTJhN2M0ZTMxMmY5MDEzZTYzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjM2MjU3OSwtNzkuNDk4NTA5MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWIzYjI3ODkwNzlkNGQwYWFkNGUzMGJkMTBmYjU5Y2QgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNGJjNTA1OWNmNDMwNGRmZWE4ZGE2YjcwNzM1YTUzMTkgPSAkKCc8ZGl2IGlkPSJodG1sXzRiYzUwNTljZjQzMDRkZmVhOGRhNmI3MDczNWE1MzE5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5LaW5nc3dheSBQYXJrIFNvdXRoIEVhc3QsIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYWIzYjI3ODkwNzlkNGQwYWFkNGUzMGJkMTBmYjU5Y2Quc2V0Q29udGVudChodG1sXzRiYzUwNTljZjQzMDRkZmVhOGRhNmI3MDczNWE1MzE5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzIwODg2MTM1MDEwMzQwOTJhN2M0ZTMxMmY5MDEzZTYzLmJpbmRQb3B1cChwb3B1cF9hYjNiMjc4OTA3OWQ0ZDBhYWQ0ZTMwYmQxMGZiNTljZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wMzgxMjlmNmI5Nzg0MDdkODkwOTViZmFkMDIwMDI4NCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYzNjI1NzksLTc5LjQ5ODUwOTA5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzkwZjU1ZGQyMzM2ZTQ4MWQ4NzI3OGYxMjU4ODgzNjY4ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzIyOTA3MDc2MjY1YjRjMmY4Yzk1NjllYTdlMGVlMzFmID0gJCgnPGRpdiBpZD0iaHRtbF8yMjkwNzA3NjI2NWI0YzJmOGM5NTY5ZWE3ZTBlZTMxZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TWltaWNvIE5FLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkwZjU1ZGQyMzM2ZTQ4MWQ4NzI3OGYxMjU4ODgzNjY4LnNldENvbnRlbnQoaHRtbF8yMjkwNzA3NjI2NWI0YzJmOGM5NTY5ZWE3ZTBlZTMxZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wMzgxMjlmNmI5Nzg0MDdkODkwOTViZmFkMDIwMDI4NC5iaW5kUG9wdXAocG9wdXBfOTBmNTVkZDIzMzZlNDgxZDg3Mjc4ZjEyNTg4ODM2NjgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTc4Njg5YzY1YWUzNGQyMWFmNTI1OWQ1MGRiNzU2MWYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42MzYyNTc5LC03OS40OTg1MDkwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85MzBiNGIyYTg0YTQ0MDZkOGNkY2I5ODU2MzRhZTE5OSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84MTljZWFiODMzNjM0NzhkOGMxZGQ5MWYyYjY1MTliZSA9ICQoJzxkaXYgaWQ9Imh0bWxfODE5Y2VhYjgzMzYzNDc4ZDhjMWRkOTFmMmI2NTE5YmUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk9sZCBNaWxsIFNvdXRoLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkzMGI0YjJhODRhNDQwNmQ4Y2RjYjk4NTYzNGFlMTk5LnNldENvbnRlbnQoaHRtbF84MTljZWFiODMzNjM0NzhkOGMxZGQ5MWYyYjY1MTliZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85Nzg2ODljNjVhZTM0ZDIxYWY1MjU5ZDUwZGI3NTYxZi5iaW5kUG9wdXAocG9wdXBfOTMwYjRiMmE4NGE0NDA2ZDhjZGNiOTg1NjM0YWUxOTkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjU4ZDNjZTk5MTdjNDIwOGEzZTQ1NWNlY2M4NDQ1YzEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42MzYyNTc5LC03OS40OTg1MDkwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85NjQ0ZGY5Yzk3MWY0ZDk0OWIwYjM4MGVkYmZjNmRjMSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80ZGMzZDAzNzZiZjI0ZDE5ODY0ZGVhYzBjMmRlZGM1YiA9ICQoJzxkaXYgaWQ9Imh0bWxfNGRjM2QwMzc2YmYyNGQxOTg2NGRlYWMwYzJkZWRjNWIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRoZSBRdWVlbnN3YXkgRWFzdCwgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85NjQ0ZGY5Yzk3MWY0ZDk0OWIwYjM4MGVkYmZjNmRjMS5zZXRDb250ZW50KGh0bWxfNGRjM2QwMzc2YmYyNGQxOTg2NGRlYWMwYzJkZWRjNWIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNjU4ZDNjZTk5MTdjNDIwOGEzZTQ1NWNlY2M4NDQ1YzEuYmluZFBvcHVwKHBvcHVwXzk2NDRkZjljOTcxZjRkOTQ5YjBiMzgwZWRiZmM2ZGMxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzc1ZjEyY2FlZmMxZTRjZDFiNjQ2ZGM2M2M1MmE4NGQ3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjM2MjU3OSwtNzkuNDk4NTA5MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODg2MDk5MDU5NWM4NDg0YzgzMmFhMDliMzgzMTQzYzcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZjVkNmQyY2U1YThlNDMzNzg1NTdlZjE3NDRmZWJmM2QgPSAkKCc8ZGl2IGlkPSJodG1sX2Y1ZDZkMmNlNWE4ZTQzMzc4NTU3ZWYxNzQ0ZmViZjNkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Sb3lhbCBZb3JrIFNvdXRoIEVhc3QsIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODg2MDk5MDU5NWM4NDg0YzgzMmFhMDliMzgzMTQzYzcuc2V0Q29udGVudChodG1sX2Y1ZDZkMmNlNWE4ZTQzMzc4NTU3ZWYxNzQ0ZmViZjNkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzc1ZjEyY2FlZmMxZTRjZDFiNjQ2ZGM2M2M1MmE4NGQ3LmJpbmRQb3B1cChwb3B1cF84ODYwOTkwNTk1Yzg0ODRjODMyYWEwOWIzODMxNDNjNyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wMmQ4NmUyNzIxOGY0ZmNiYjBmNDNkYjIyYTQyY2YxNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYzNjI1NzksLTc5LjQ5ODUwOTA5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzkwZWM0YjU5NTFlZTRhZDBiYWNhZDhjMDg5NDg1OTY4ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzJkOTVmMzY3Mjc3MzQ1MTFhMGExOTUyYzEyOWRmMjliID0gJCgnPGRpdiBpZD0iaHRtbF8yZDk1ZjM2NzI3NzM0NTExYTBhMTk1MmMxMjlkZjI5YiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U3VubnlsZWEsIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTBlYzRiNTk1MWVlNGFkMGJhY2FkOGMwODk0ODU5Njguc2V0Q29udGVudChodG1sXzJkOTVmMzY3Mjc3MzQ1MTFhMGExOTUyYzEyOWRmMjliKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzAyZDg2ZTI3MjE4ZjRmY2JiMGY0M2RiMjJhNDJjZjE1LmJpbmRQb3B1cChwb3B1cF85MGVjNGI1OTUxZWU0YWQwYmFjYWQ4YzA4OTQ4NTk2OCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80NmExZmU4Nzc2MzA0N2U4YWMyYTNkNTE2Y2NhYjczYSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYyODg0MDgsLTc5LjUyMDk5OTQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzBmY2Y2Njg1OGI0NjRmYzU5YzZhMDQxOWM3Njg1NzgwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2VkZDE5NjQxYzE5NzQwZmViZDkzNGE4MWY0YzI1YWY2ID0gJCgnPGRpdiBpZD0iaHRtbF9lZGQxOTY0MWMxOTc0MGZlYmQ5MzRhODFmNGMyNWFmNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+S2luZ3N3YXkgUGFyayBTb3V0aCBXZXN0LCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzBmY2Y2Njg1OGI0NjRmYzU5YzZhMDQxOWM3Njg1NzgwLnNldENvbnRlbnQoaHRtbF9lZGQxOTY0MWMxOTc0MGZlYmQ5MzRhODFmNGMyNWFmNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80NmExZmU4Nzc2MzA0N2U4YWMyYTNkNTE2Y2NhYjczYS5iaW5kUG9wdXAocG9wdXBfMGZjZjY2ODU4YjQ2NGZjNTljNmEwNDE5Yzc2ODU3ODApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfY2EyOTQ4MTFhN2RlNGZlODg5MTlmOTM2MDdhYmY3YTIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Mjg4NDA4LC03OS41MjA5OTk0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85NGU4Zjc5ZDg2NDI0MGM5ODZkMmNhOGU4OGFkMTllZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iMzA4ZDNhYjM4ZWM0ZTBmYTg1MjNlMTllM2Q3MmI1MiA9ICQoJzxkaXYgaWQ9Imh0bWxfYjMwOGQzYWIzOGVjNGUwZmE4NTIzZTE5ZTNkNzJiNTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1pbWljbyBOVywgRXRvYmljb2tlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85NGU4Zjc5ZDg2NDI0MGM5ODZkMmNhOGU4OGFkMTllZC5zZXRDb250ZW50KGh0bWxfYjMwOGQzYWIzOGVjNGUwZmE4NTIzZTE5ZTNkNzJiNTIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfY2EyOTQ4MTFhN2RlNGZlODg5MTlmOTM2MDdhYmY3YTIuYmluZFBvcHVwKHBvcHVwXzk0ZThmNzlkODY0MjQwYzk4NmQyY2E4ZTg4YWQxOWVkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzRiYjkwZmI0Y2Y0NjRhYzM5ODE0MjFmNjZhZDlmZDkxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjI4ODQwOCwtNzkuNTIwOTk5NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfOGY5ZDYzNDExZWYwNDA2Njg4MjU5OGU4NzRmYzJhNDgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfN2ZlMjQyMWY3YmI2NGViYmE1NmY2NmIxYWY4NDZlZjIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMGQ0MDRiYWNlZTA2NDJkNDgyZDg2ZTIwZjE0YjJkMTEgPSAkKCc8ZGl2IGlkPSJodG1sXzBkNDA0YmFjZWUwNjQyZDQ4MmQ4NmUyMGYxNGIyZDExIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgUXVlZW5zd2F5IFdlc3QsIEV0b2JpY29rZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfN2ZlMjQyMWY3YmI2NGViYmE1NmY2NmIxYWY4NDZlZjIuc2V0Q29udGVudChodG1sXzBkNDA0YmFjZWUwNjQyZDQ4MmQ4NmUyMGYxNGIyZDExKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzRiYjkwZmI0Y2Y0NjRhYzM5ODE0MjFmNjZhZDlmZDkxLmJpbmRQb3B1cChwb3B1cF83ZmUyNDIxZjdiYjY0ZWJiYTU2ZjY2YjFhZjg0NmVmMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80OWFiYjQ3YWM1MWQ0ODE3YWYyZTc2ODEyOTMwYmViMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYyODg0MDgsLTc5LjUyMDk5OTQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzhmOWQ2MzQxMWVmMDQwNjY4ODI1OThlODc0ZmMyYTQ4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzRjNzkwY2M0YWRmMjQyZTM5ZjBlODgzMTEzODNlYmE0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2IxOThmZDQyNWY4ZDQzZTdiMDI2ZDBmYzE4ZjI3NWNmID0gJCgnPGRpdiBpZD0iaHRtbF9iMTk4ZmQ0MjVmOGQ0M2U3YjAyNmQwZmMxOGYyNzVjZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Um95YWwgWW9yayBTb3V0aCBXZXN0LCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzRjNzkwY2M0YWRmMjQyZTM5ZjBlODgzMTEzODNlYmE0LnNldENvbnRlbnQoaHRtbF9iMTk4ZmQ0MjVmOGQ0M2U3YjAyNmQwZmMxOGYyNzVjZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80OWFiYjQ3YWM1MWQ0ODE3YWYyZTc2ODEyOTMwYmViMi5iaW5kUG9wdXAocG9wdXBfNGM3OTBjYzRhZGYyNDJlMzlmMGU4ODMxMTM4M2ViYTQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOWNkNGM5OTU4NDRmNDA4N2FiMjdlMTJiZDA2MjZiNDAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Mjg4NDA4LC03OS41MjA5OTk0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF84ZjlkNjM0MTFlZjA0MDY2ODgyNTk4ZTg3NGZjMmE0OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yMWJkNTI0Nzg4MGI0MGFmOTMzMzBlYzk2MzljMjU3MiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yZGMyMDZmNGVlMjc0NzhkYjVjZTc4Y2Y4ZjgwOWU3OSA9ICQoJzxkaXYgaWQ9Imh0bWxfMmRjMjA2ZjRlZTI3NDc4ZGI1Y2U3OGNmOGY4MDllNzkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlNvdXRoIG9mIEJsb29yLCBFdG9iaWNva2U8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzIxYmQ1MjQ3ODgwYjQwYWY5MzMzMGVjOTYzOWMyNTcyLnNldENvbnRlbnQoaHRtbF8yZGMyMDZmNGVlMjc0NzhkYjVjZTc4Y2Y4ZjgwOWU3OSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85Y2Q0Yzk5NTg0NGY0MDg3YWIyN2UxMmJkMDYyNmI0MC5iaW5kUG9wdXAocG9wdXBfMjFiZDUyNDc4ODBiNDBhZjkzMzMwZWM5NjM5YzI1NzIpOwoKICAgICAgICAgICAgCiAgICAgICAgCjwvc2NyaXB0Pg==" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



## The territiry is too wide.  
## Lets limit it by exploring only the city and using only boroughs that contain the word 'Toronto' 


```python
toronto_df=postal_geo_df[postal_geo_df['Borough'].str.contains('Toronto')].reset_index(drop=True)
#There are two St. James Town in Downtown with different Postal Codes. Changing the second name to keep it from dropping in analyzing stage.
toronto_df.loc[69,'Neighborhood']="{}, {}".format(toronto_df.loc[69,'Borough'],toronto_df.loc[69,'Neighborhood'])
toronto_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PostCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront</td>
      <td>43.654260</td>
      <td>-79.360636</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park</td>
      <td>43.654260</td>
      <td>-79.360636</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Ryerson</td>
      <td>43.657162</td>
      <td>-79.378937</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District</td>
      <td>43.657162</td>
      <td>-79.378937</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M5C</td>
      <td>Downtown Toronto</td>
      <td>St. James Town</td>
      <td>43.651494</td>
      <td>-79.375418</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M4E</td>
      <td>East Toronto</td>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M5E</td>
      <td>Downtown Toronto</td>
      <td>Berczy Park</td>
      <td>43.644771</td>
      <td>-79.373306</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M5G</td>
      <td>Downtown Toronto</td>
      <td>Central Bay Street</td>
      <td>43.657952</td>
      <td>-79.387383</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M6G</td>
      <td>Downtown Toronto</td>
      <td>Christie</td>
      <td>43.669542</td>
      <td>-79.422564</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M5H</td>
      <td>Downtown Toronto</td>
      <td>Adelaide</td>
      <td>43.650571</td>
      <td>-79.384568</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M5H</td>
      <td>Downtown Toronto</td>
      <td>King</td>
      <td>43.650571</td>
      <td>-79.384568</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M5H</td>
      <td>Downtown Toronto</td>
      <td>Richmond</td>
      <td>43.650571</td>
      <td>-79.384568</td>
    </tr>
    <tr>
      <th>12</th>
      <td>M6H</td>
      <td>West Toronto</td>
      <td>Dovercourt Village</td>
      <td>43.669005</td>
      <td>-79.442259</td>
    </tr>
    <tr>
      <th>13</th>
      <td>M6H</td>
      <td>West Toronto</td>
      <td>Dufferin</td>
      <td>43.669005</td>
      <td>-79.442259</td>
    </tr>
    <tr>
      <th>14</th>
      <td>M5J</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront East</td>
      <td>43.640816</td>
      <td>-79.381752</td>
    </tr>
    <tr>
      <th>15</th>
      <td>M5J</td>
      <td>Downtown Toronto</td>
      <td>Toronto Islands</td>
      <td>43.640816</td>
      <td>-79.381752</td>
    </tr>
    <tr>
      <th>16</th>
      <td>M5J</td>
      <td>Downtown Toronto</td>
      <td>Union Station</td>
      <td>43.640816</td>
      <td>-79.381752</td>
    </tr>
    <tr>
      <th>17</th>
      <td>M6J</td>
      <td>West Toronto</td>
      <td>Little Portugal</td>
      <td>43.647927</td>
      <td>-79.419750</td>
    </tr>
    <tr>
      <th>18</th>
      <td>M6J</td>
      <td>West Toronto</td>
      <td>Trinity</td>
      <td>43.647927</td>
      <td>-79.419750</td>
    </tr>
    <tr>
      <th>19</th>
      <td>M4K</td>
      <td>East Toronto</td>
      <td>The Danforth West</td>
      <td>43.679557</td>
      <td>-79.352188</td>
    </tr>
    <tr>
      <th>20</th>
      <td>M4K</td>
      <td>East Toronto</td>
      <td>Riverdale</td>
      <td>43.679557</td>
      <td>-79.352188</td>
    </tr>
    <tr>
      <th>21</th>
      <td>M5K</td>
      <td>Downtown Toronto</td>
      <td>Design Exchange</td>
      <td>43.647177</td>
      <td>-79.381576</td>
    </tr>
    <tr>
      <th>22</th>
      <td>M5K</td>
      <td>Downtown Toronto</td>
      <td>Toronto Dominion Centre</td>
      <td>43.647177</td>
      <td>-79.381576</td>
    </tr>
    <tr>
      <th>23</th>
      <td>M6K</td>
      <td>West Toronto</td>
      <td>Brockton</td>
      <td>43.636847</td>
      <td>-79.428191</td>
    </tr>
    <tr>
      <th>24</th>
      <td>M6K</td>
      <td>West Toronto</td>
      <td>Exhibition Place</td>
      <td>43.636847</td>
      <td>-79.428191</td>
    </tr>
    <tr>
      <th>25</th>
      <td>M6K</td>
      <td>West Toronto</td>
      <td>Parkdale Village</td>
      <td>43.636847</td>
      <td>-79.428191</td>
    </tr>
    <tr>
      <th>26</th>
      <td>M4L</td>
      <td>East Toronto</td>
      <td>The Beaches West</td>
      <td>43.668999</td>
      <td>-79.315572</td>
    </tr>
    <tr>
      <th>27</th>
      <td>M4L</td>
      <td>East Toronto</td>
      <td>India Bazaar</td>
      <td>43.668999</td>
      <td>-79.315572</td>
    </tr>
    <tr>
      <th>28</th>
      <td>M5L</td>
      <td>Downtown Toronto</td>
      <td>Commerce Court</td>
      <td>43.648198</td>
      <td>-79.379817</td>
    </tr>
    <tr>
      <th>29</th>
      <td>M5L</td>
      <td>Downtown Toronto</td>
      <td>Victoria Hotel</td>
      <td>43.648198</td>
      <td>-79.379817</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>44</th>
      <td>M4S</td>
      <td>Central Toronto</td>
      <td>Davisville</td>
      <td>43.704324</td>
      <td>-79.388790</td>
    </tr>
    <tr>
      <th>45</th>
      <td>M5S</td>
      <td>Downtown Toronto</td>
      <td>Harbord</td>
      <td>43.662696</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>46</th>
      <td>M5S</td>
      <td>Downtown Toronto</td>
      <td>University of Toronto</td>
      <td>43.662696</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>47</th>
      <td>M6S</td>
      <td>West Toronto</td>
      <td>Runnymede</td>
      <td>43.651571</td>
      <td>-79.484450</td>
    </tr>
    <tr>
      <th>48</th>
      <td>M6S</td>
      <td>West Toronto</td>
      <td>Swansea</td>
      <td>43.651571</td>
      <td>-79.484450</td>
    </tr>
    <tr>
      <th>49</th>
      <td>M4T</td>
      <td>Central Toronto</td>
      <td>Moore Park</td>
      <td>43.689574</td>
      <td>-79.383160</td>
    </tr>
    <tr>
      <th>50</th>
      <td>M4T</td>
      <td>Central Toronto</td>
      <td>Summerhill East</td>
      <td>43.689574</td>
      <td>-79.383160</td>
    </tr>
    <tr>
      <th>51</th>
      <td>M5T</td>
      <td>Downtown Toronto</td>
      <td>Chinatown</td>
      <td>43.653206</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>52</th>
      <td>M5T</td>
      <td>Downtown Toronto</td>
      <td>Grange Park</td>
      <td>43.653206</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>53</th>
      <td>M5T</td>
      <td>Downtown Toronto</td>
      <td>Kensington Market</td>
      <td>43.653206</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>54</th>
      <td>M4V</td>
      <td>Central Toronto</td>
      <td>Deer Park</td>
      <td>43.686412</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>55</th>
      <td>M4V</td>
      <td>Central Toronto</td>
      <td>Forest Hill SE</td>
      <td>43.686412</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>56</th>
      <td>M4V</td>
      <td>Central Toronto</td>
      <td>Rathnelly</td>
      <td>43.686412</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>57</th>
      <td>M4V</td>
      <td>Central Toronto</td>
      <td>South Hill</td>
      <td>43.686412</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>58</th>
      <td>M4V</td>
      <td>Central Toronto</td>
      <td>Summerhill West</td>
      <td>43.686412</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>59</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td>CN Tower</td>
      <td>43.628947</td>
      <td>-79.394420</td>
    </tr>
    <tr>
      <th>60</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td>Bathurst Quay</td>
      <td>43.628947</td>
      <td>-79.394420</td>
    </tr>
    <tr>
      <th>61</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td>Island airport</td>
      <td>43.628947</td>
      <td>-79.394420</td>
    </tr>
    <tr>
      <th>62</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront West</td>
      <td>43.628947</td>
      <td>-79.394420</td>
    </tr>
    <tr>
      <th>63</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td>King and Spadina</td>
      <td>43.628947</td>
      <td>-79.394420</td>
    </tr>
    <tr>
      <th>64</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td>Railway Lands</td>
      <td>43.628947</td>
      <td>-79.394420</td>
    </tr>
    <tr>
      <th>65</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td>South Niagara</td>
      <td>43.628947</td>
      <td>-79.394420</td>
    </tr>
    <tr>
      <th>66</th>
      <td>M4W</td>
      <td>Downtown Toronto</td>
      <td>Rosedale</td>
      <td>43.679563</td>
      <td>-79.377529</td>
    </tr>
    <tr>
      <th>67</th>
      <td>M5W</td>
      <td>Downtown Toronto</td>
      <td>Stn A PO Boxes 25 The Esplanade</td>
      <td>43.646435</td>
      <td>-79.374846</td>
    </tr>
    <tr>
      <th>68</th>
      <td>M4X</td>
      <td>Downtown Toronto</td>
      <td>Cabbagetown</td>
      <td>43.667967</td>
      <td>-79.367675</td>
    </tr>
    <tr>
      <th>69</th>
      <td>M4X</td>
      <td>Downtown Toronto</td>
      <td>Downtown Toronto, St. James Town</td>
      <td>43.667967</td>
      <td>-79.367675</td>
    </tr>
    <tr>
      <th>70</th>
      <td>M5X</td>
      <td>Downtown Toronto</td>
      <td>First Canadian Place</td>
      <td>43.648429</td>
      <td>-79.382280</td>
    </tr>
    <tr>
      <th>71</th>
      <td>M5X</td>
      <td>Downtown Toronto</td>
      <td>Underground city</td>
      <td>43.648429</td>
      <td>-79.382280</td>
    </tr>
    <tr>
      <th>72</th>
      <td>M4Y</td>
      <td>Downtown Toronto</td>
      <td>Church and Wellesley</td>
      <td>43.665860</td>
      <td>-79.383160</td>
    </tr>
    <tr>
      <th>73</th>
      <td>M7Y</td>
      <td>East Toronto</td>
      <td>Business Reply Mail Processing Centre 969 Eastern</td>
      <td>43.662744</td>
      <td>-79.321558</td>
    </tr>
  </tbody>
</table>
<p>74 rows × 5 columns</p>
</div>




```python
# create map of Toronto
map_toronto = folium.Map(location=[latitude, longitude], zoom_start=11)

# add PostCode's coordinates to the map
for lat, lng, borough, neighborhood in zip(toronto_df['Latitude'], toronto_df['Longitude'], toronto_df['Borough'], toronto_df['Neighborhood']):
    label = '{}, {}'.format(neighborhood, borough)
    label = folium.Popup(label, parse_html=True)
    folium.CircleMarker(
        [lat, lng],
        radius=5,
        popup=label,
        color='cyan',
        fill=True,
        fill_color='black',
        fill_opacity=0.7,
        parse_html=False).add_to(map_toronto)  
    
map_toronto
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;base64,PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiIiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYiA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYicsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDMuNjUzOTYzLC03OS4zODcyMDddLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgem9vbTogMTEsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBtYXhCb3VuZHM6IGJvdW5kcywKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGxheWVyczogW10sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB3b3JsZENvcHlKdW1wOiBmYWxzZSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGNyczogTC5DUlMuRVBTRzM4NTcKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciB0aWxlX2xheWVyXzM0YWQ0NWNiM2Q3OTRmZDRhNGQzMTFjOTA2ZGMzMGNkID0gTC50aWxlTGF5ZXIoCiAgICAgICAgICAgICAgICAnaHR0cHM6Ly97c30udGlsZS5vcGVuc3RyZWV0bWFwLm9yZy97en0ve3h9L3t5fS5wbmcnLAogICAgICAgICAgICAgICAgewogICJhdHRyaWJ1dGlvbiI6IG51bGwsCiAgImRldGVjdFJldGluYSI6IGZhbHNlLAogICJtYXhab29tIjogMTgsCiAgIm1pblpvb20iOiAxLAogICJub1dyYXAiOiBmYWxzZSwKICAic3ViZG9tYWlucyI6ICJhYmMiCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zZTc3NTNhNTZmNjE0OTM4ODFjYzk1ZWQzNTVmNzdlMiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1NDI1OTksLTc5LjM2MDYzNTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Y0ZWEwMTFlMzYzMjRjNTY5NGRlMDA2MDg5NDBmN2IzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzE5ZGJjZTcyMmY5YjQxYmI5OWFiNjBkYjhhMGI1OTM3ID0gJCgnPGRpdiBpZD0iaHRtbF8xOWRiY2U3MjJmOWI0MWJiOTlhYjYwZGI4YTBiNTkzNyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SGFyYm91cmZyb250LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mNGVhMDExZTM2MzI0YzU2OTRkZTAwNjA4OTQwZjdiMy5zZXRDb250ZW50KGh0bWxfMTlkYmNlNzIyZjliNDFiYjk5YWI2MGRiOGEwYjU5MzcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfM2U3NzUzYTU2ZjYxNDkzODgxY2M5NWVkMzU1Zjc3ZTIuYmluZFBvcHVwKHBvcHVwX2Y0ZWEwMTFlMzYzMjRjNTY5NGRlMDA2MDg5NDBmN2IzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2RkNWY4MWQzZGNhNTRmYTc4MzcwZmYzMzUwMjJlMGNiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjU0MjU5OSwtNzkuMzYwNjM1OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzhiYzA2MjcwMzQ3NDM5Njg4NTkwNjllOWE3ZjRmZGQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNTI1MmRlOTgxNTc3NGMzYzg2NTcxYTBjZDZlMzhjMjkgPSAkKCc8ZGl2IGlkPSJodG1sXzUyNTJkZTk4MTU3NzRjM2M4NjU3MWEwY2Q2ZTM4YzI5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5SZWdlbnQgUGFyaywgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzhiYzA2MjcwMzQ3NDM5Njg4NTkwNjllOWE3ZjRmZGQuc2V0Q29udGVudChodG1sXzUyNTJkZTk4MTU3NzRjM2M4NjU3MWEwY2Q2ZTM4YzI5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2RkNWY4MWQzZGNhNTRmYTc4MzcwZmYzMzUwMjJlMGNiLmJpbmRQb3B1cChwb3B1cF9jOGJjMDYyNzAzNDc0Mzk2ODg1OTA2OWU5YTdmNGZkZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82NDI5ZmMwY2RkNDU0MTkwOTkyNzM5NTMxMTk1Njk3NCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1NzE2MTgsLTc5LjM3ODkzNzA5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81NzJiYmI3ODUwN2Q0NmM0OGU4M2JlMzdkNzNkMTc1OCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xYzk0MmY2NzgzMjM0MjY0YjBmMTc1NjhjY2RlMWQ4ZiA9ICQoJzxkaXYgaWQ9Imh0bWxfMWM5NDJmNjc4MzIzNDI2NGIwZjE3NTY4Y2NkZTFkOGYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJ5ZXJzb24sIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzU3MmJiYjc4NTA3ZDQ2YzQ4ZTgzYmUzN2Q3M2QxNzU4LnNldENvbnRlbnQoaHRtbF8xYzk0MmY2NzgzMjM0MjY0YjBmMTc1NjhjY2RlMWQ4Zik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82NDI5ZmMwY2RkNDU0MTkwOTkyNzM5NTMxMTk1Njk3NC5iaW5kUG9wdXAocG9wdXBfNTcyYmJiNzg1MDdkNDZjNDhlODNiZTM3ZDczZDE3NTgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjdlNzZkMjk2ZTEyNDE1MThmZjJiNjRhZjkzNTk0YWQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTcxNjE4LC03OS4zNzg5MzcwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODc3NmIzODJmMWRlNDU3YmEwYWQ0NzQ4Y2M1Yzg3MzMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjVkOWZmNDhlY2QzNDMzODg1ZmE3ODU0YmJkZDgyNDEgPSAkKCc8ZGl2IGlkPSJodG1sXzI1ZDlmZjQ4ZWNkMzQzMzg4NWZhNzg1NGJiZGQ4MjQxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5HYXJkZW4gRGlzdHJpY3QsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzg3NzZiMzgyZjFkZTQ1N2JhMGFkNDc0OGNjNWM4NzMzLnNldENvbnRlbnQoaHRtbF8yNWQ5ZmY0OGVjZDM0MzM4ODVmYTc4NTRiYmRkODI0MSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82N2U3NmQyOTZlMTI0MTUxOGZmMmI2NGFmOTM1OTRhZC5iaW5kUG9wdXAocG9wdXBfODc3NmIzODJmMWRlNDU3YmEwYWQ0NzQ4Y2M1Yzg3MzMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODgzZGEwNWI1MTVkNGVmNGEwNTk1NjQyNzNiNDFhOGEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTE0OTM5LC03OS4zNzU0MTc5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zOWEwNjcyODBmNzg0YWExYjMwOTM2ZmE2MzA1ZDZhYiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zNTlmYmU0NTljOGU0OWM5OTNlNWEzOGU3ZmJlNzJmNyA9ICQoJzxkaXYgaWQ9Imh0bWxfMzU5ZmJlNDU5YzhlNDljOTkzZTVhMzhlN2ZiZTcyZjciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0LiBKYW1lcyBUb3duLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zOWEwNjcyODBmNzg0YWExYjMwOTM2ZmE2MzA1ZDZhYi5zZXRDb250ZW50KGh0bWxfMzU5ZmJlNDU5YzhlNDljOTkzZTVhMzhlN2ZiZTcyZjcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfODgzZGEwNWI1MTVkNGVmNGEwNTk1NjQyNzNiNDFhOGEuYmluZFBvcHVwKHBvcHVwXzM5YTA2NzI4MGY3ODRhYTFiMzA5MzZmYTYzMDVkNmFiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzRiNzgzMzdiMjcxMTQ5N2NhNzlkOGUwODYwNTZlZWIwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjc2MzU3Mzk5OTk5OTksLTc5LjI5MzAzMTJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2RlYzgxYTA2ZWRhNjRlNWM5YmQzNTQ3ZmViMGUxNmY0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzZlYjgzNDI4MzQ2NTRjZDhhMmYxNjFlMjY2YmJjODczID0gJCgnPGRpdiBpZD0iaHRtbF82ZWI4MzQyODM0NjU0Y2Q4YTJmMTYxZTI2NmJiYzg3MyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VGhlIEJlYWNoZXMsIEVhc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZGVjODFhMDZlZGE2NGU1YzliZDM1NDdmZWIwZTE2ZjQuc2V0Q29udGVudChodG1sXzZlYjgzNDI4MzQ2NTRjZDhhMmYxNjFlMjY2YmJjODczKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzRiNzgzMzdiMjcxMTQ5N2NhNzlkOGUwODYwNTZlZWIwLmJpbmRQb3B1cChwb3B1cF9kZWM4MWEwNmVkYTY0ZTVjOWJkMzU0N2ZlYjBlMTZmNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81ODVhMjMyZTM1MzM0YjkxYmFhNmMyMzJkZWUwMzI4OSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NDc3MDc5OTk5OTk5NiwtNzkuMzczMzA2NF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNWIyNDZmNjhlODQ1NDUwOGJmZjA3OTdmMjU0ZjQyMjQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjlmNTEzMGFlMTQyNDQ1YjkzYzMyZGM4MmY4MmZiN2IgPSAkKCc8ZGl2IGlkPSJodG1sXzY5ZjUxMzBhZTE0MjQ0NWI5M2MzMmRjODJmODJmYjdiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CZXJjenkgUGFyaywgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNWIyNDZmNjhlODQ1NDUwOGJmZjA3OTdmMjU0ZjQyMjQuc2V0Q29udGVudChodG1sXzY5ZjUxMzBhZTE0MjQ0NWI5M2MzMmRjODJmODJmYjdiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzU4NWEyMzJlMzUzMzRiOTFiYWE2YzIzMmRlZTAzMjg5LmJpbmRQb3B1cChwb3B1cF81YjI0NmY2OGU4NDU0NTA4YmZmMDc5N2YyNTRmNDIyNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jMmZhM2QxZDAzNDg0YTI2ODk4NmZlZGU0ZmRhOWI1NyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1Nzk1MjQsLTc5LjM4NzM4MjZdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Q1NTRhMTk2NDE4NjQxYzJhOWY4ZDM3ZmUwZjg0ZjhjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzMxZjY0NDJkOWJhZjQxYTY4MTMxNWMyNjFlMjhmNzAzID0gJCgnPGRpdiBpZD0iaHRtbF8zMWY2NDQyZDliYWY0MWE2ODEzMTVjMjYxZTI4ZjcwMyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2VudHJhbCBCYXkgU3RyZWV0LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kNTU0YTE5NjQxODY0MWMyYTlmOGQzN2ZlMGY4NGY4Yy5zZXRDb250ZW50KGh0bWxfMzFmNjQ0MmQ5YmFmNDFhNjgxMzE1YzI2MWUyOGY3MDMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzJmYTNkMWQwMzQ4NGEyNjg5ODZmZWRlNGZkYTliNTcuYmluZFBvcHVwKHBvcHVwX2Q1NTRhMTk2NDE4NjQxYzJhOWY4ZDM3ZmUwZjg0ZjhjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2E0MWY2YjM2YTFjMDRhN2ViMzRjNmM5ZmNiMjU0MTk1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY5NTQyLC03OS40MjI1NjM3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85ZDU5ODg5NDg5MmY0YmVhYTg1YTQ1MjRhMzhiMDk5NSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85ZmU1NjJmM2EzYjY0ZGIwYTk5NjE5MjA4ZjMxZDNmZSA9ICQoJzxkaXYgaWQ9Imh0bWxfOWZlNTYyZjNhM2I2NGRiMGE5OTYxOTIwOGYzMWQzZmUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNocmlzdGllLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85ZDU5ODg5NDg5MmY0YmVhYTg1YTQ1MjRhMzhiMDk5NS5zZXRDb250ZW50KGh0bWxfOWZlNTYyZjNhM2I2NGRiMGE5OTYxOTIwOGYzMWQzZmUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYTQxZjZiMzZhMWMwNGE3ZWIzNGM2YzlmY2IyNTQxOTUuYmluZFBvcHVwKHBvcHVwXzlkNTk4ODk0ODkyZjRiZWFhODVhNDUyNGEzOGIwOTk1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzlmMjE4YzYwNjI1ZTRhZjdhM2QyN2FlM2NlYzBhZmU5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUwNTcxMjAwMDAwMDEsLTc5LjM4NDU2NzVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2QzNWFhM2JmZWNjNjQzNjU5MmY2MGY0Yzc2MDRmN2NhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzQ1ODgxODhmMDc3MDQ2ODNiYjcxNzg1ZDNmNjQ2NzZjID0gJCgnPGRpdiBpZD0iaHRtbF80NTg4MTg4ZjA3NzA0NjgzYmI3MTc4NWQzZjY0Njc2YyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QWRlbGFpZGUsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2QzNWFhM2JmZWNjNjQzNjU5MmY2MGY0Yzc2MDRmN2NhLnNldENvbnRlbnQoaHRtbF80NTg4MTg4ZjA3NzA0NjgzYmI3MTc4NWQzZjY0Njc2Yyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85ZjIxOGM2MDYyNWU0YWY3YTNkMjdhZTNjZWMwYWZlOS5iaW5kUG9wdXAocG9wdXBfZDM1YWEzYmZlY2M2NDM2NTkyZjYwZjRjNzYwNGY3Y2EpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZWM5ZWRmZTM2ZGJiNGZhNjk3N2FhYTNhY2E5ZjEwZjggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTA1NzEyMDAwMDAwMSwtNzkuMzg0NTY3NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMmFiYTA0NTIyZDA5NDU5MzhlNTZmOWQyNDAxZTEzMTQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjU4MjlhNmVhYTQ2NDI1OWI4ZWFkYjBiMTMzZTZkNjYgPSAkKCc8ZGl2IGlkPSJodG1sXzY1ODI5YTZlYWE0NjQyNTliOGVhZGIwYjEzM2U2ZDY2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5LaW5nLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yYWJhMDQ1MjJkMDk0NTkzOGU1NmY5ZDI0MDFlMTMxNC5zZXRDb250ZW50KGh0bWxfNjU4MjlhNmVhYTQ2NDI1OWI4ZWFkYjBiMTMzZTZkNjYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZWM5ZWRmZTM2ZGJiNGZhNjk3N2FhYTNhY2E5ZjEwZjguYmluZFBvcHVwKHBvcHVwXzJhYmEwNDUyMmQwOTQ1OTM4ZTU2ZjlkMjQwMWUxMzE0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2QwZTg5ZTQ0NzM3ZTQ4YWRhMzFjYmRhNzIyN2FmZGIxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUwNTcxMjAwMDAwMDEsLTc5LjM4NDU2NzVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzlmYjJiZTkwYTA4ODQ2NGJhNzEzODFlOWU1YzdlMGRmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2YyYjYzYjc5OGMyNjQ2ZTdiNjJjOWJlYzhkYjM3NjNjID0gJCgnPGRpdiBpZD0iaHRtbF9mMmI2M2I3OThjMjY0NmU3YjYyYzliZWM4ZGIzNzYzYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UmljaG1vbmQsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzlmYjJiZTkwYTA4ODQ2NGJhNzEzODFlOWU1YzdlMGRmLnNldENvbnRlbnQoaHRtbF9mMmI2M2I3OThjMjY0NmU3YjYyYzliZWM4ZGIzNzYzYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kMGU4OWU0NDczN2U0OGFkYTMxY2JkYTcyMjdhZmRiMS5iaW5kUG9wdXAocG9wdXBfOWZiMmJlOTBhMDg4NDY0YmE3MTM4MWU5ZTVjN2UwZGYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjBlZGI1NDNmZmQwNDI3Njk5NzNhMjg3NjdlYWQwNzcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjkwMDUxMDAwMDAwMSwtNzkuNDQyMjU5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWZkOGQ5YmU0NTY3NGMyOWI3NjA5MDhjNmExYmU3NzEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTRlZjVlOWNkMDMwNGZlZjkzNjY1ODNhZmFjZGY1NzIgPSAkKCc8ZGl2IGlkPSJodG1sX2E0ZWY1ZTljZDAzMDRmZWY5MzY2NTgzYWZhY2RmNTcyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Eb3ZlcmNvdXJ0IFZpbGxhZ2UsIFdlc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOWZkOGQ5YmU0NTY3NGMyOWI3NjA5MDhjNmExYmU3NzEuc2V0Q29udGVudChodG1sX2E0ZWY1ZTljZDAzMDRmZWY5MzY2NTgzYWZhY2RmNTcyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzYwZWRiNTQzZmZkMDQyNzY5OTczYTI4NzY3ZWFkMDc3LmJpbmRQb3B1cChwb3B1cF85ZmQ4ZDliZTQ1Njc0YzI5Yjc2MDkwOGM2YTFiZTc3MSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81YjJlYjdmMDkzZWU0MTA3YTdmNGQyNzU4Y2ViNjE3ZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2OTAwNTEwMDAwMDAxLC03OS40NDIyNTkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82ZDNjMzE2MGIwNDE0YjNiODliODA5YTI3MjVhMTAyZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83NWVlNzQ3MTdkY2Q0ODgyODM4MjY4OGE0ZjY3YjYwYSA9ICQoJzxkaXYgaWQ9Imh0bWxfNzVlZTc0NzE3ZGNkNDg4MjgzODI2ODhhNGY2N2I2MGEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkR1ZmZlcmluLCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzZkM2MzMTYwYjA0MTRiM2I4OWI4MDlhMjcyNWExMDJkLnNldENvbnRlbnQoaHRtbF83NWVlNzQ3MTdkY2Q0ODgyODM4MjY4OGE0ZjY3YjYwYSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81YjJlYjdmMDkzZWU0MTA3YTdmNGQyNzU4Y2ViNjE3ZS5iaW5kUG9wdXAocG9wdXBfNmQzYzMxNjBiMDQxNGIzYjg5YjgwOWEyNzI1YTEwMmQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjAwNTg3NDU0YjUzNDgyYmJiMDM4ZWEzOGE4ZDg3NjEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDA4MTU3LC03OS4zODE3NTIyOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMWU3YWQ1ZDIwM2E2NDE0Zjg0M2E5NDBkNDRiMTRjYWIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZWJhNmE3NzgwMzUzNDdhZDhiODBjMzE2YjVkNTg5MWIgPSAkKCc8ZGl2IGlkPSJodG1sX2ViYTZhNzc4MDM1MzQ3YWQ4YjgwYzMxNmI1ZDU4OTFiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IYXJib3VyZnJvbnQgRWFzdCwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMWU3YWQ1ZDIwM2E2NDE0Zjg0M2E5NDBkNDRiMTRjYWIuc2V0Q29udGVudChodG1sX2ViYTZhNzc4MDM1MzQ3YWQ4YjgwYzMxNmI1ZDU4OTFiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzIwMDU4NzQ1NGI1MzQ4MmJiYjAzOGVhMzhhOGQ4NzYxLmJpbmRQb3B1cChwb3B1cF8xZTdhZDVkMjAzYTY0MTRmODQzYTk0MGQ0NGIxNGNhYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mMWNmMDQzZTg3ZDY0Njc3OGFhN2Q0NzBjMzg5MTA5YSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0MDgxNTcsLTc5LjM4MTc1MjI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wZjhjYmUyMGQwZGQ0ODdkYjg0OTJkNzM0NDZmMzAzZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83MjFhMmM5Mzk5YWE0ZTNkODA2MDAxYTJkM2IzZDZiYyA9ICQoJzxkaXYgaWQ9Imh0bWxfNzIxYTJjOTM5OWFhNGUzZDgwNjAwMWEyZDNiM2Q2YmMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRvcm9udG8gSXNsYW5kcywgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMGY4Y2JlMjBkMGRkNDg3ZGI4NDkyZDczNDQ2ZjMwM2Yuc2V0Q29udGVudChodG1sXzcyMWEyYzkzOTlhYTRlM2Q4MDYwMDFhMmQzYjNkNmJjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2YxY2YwNDNlODdkNjQ2Nzc4YWE3ZDQ3MGMzODkxMDlhLmJpbmRQb3B1cChwb3B1cF8wZjhjYmUyMGQwZGQ0ODdkYjg0OTJkNzM0NDZmMzAzZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81YmYzNTE1YzYyNzc0NjFmOTM4NzJhODQ3OWM5YjFmZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0MDgxNTcsLTc5LjM4MTc1MjI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84ODVmNDc3YTg1YzM0NzlhOWY1YWNkMTFjYTZkYzhhMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zMTExNjE2NmRkNDM0ZDlkYTdiNzRmOTIwMTg3MTZkNiA9ICQoJzxkaXYgaWQ9Imh0bWxfMzExMTYxNjZkZDQzNGQ5ZGE3Yjc0ZjkyMDE4NzE2ZDYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlVuaW9uIFN0YXRpb24sIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzg4NWY0NzdhODVjMzQ3OWE5ZjVhY2QxMWNhNmRjOGEwLnNldENvbnRlbnQoaHRtbF8zMTExNjE2NmRkNDM0ZDlkYTdiNzRmOTIwMTg3MTZkNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81YmYzNTE1YzYyNzc0NjFmOTM4NzJhODQ3OWM5YjFmZi5iaW5kUG9wdXAocG9wdXBfODg1ZjQ3N2E4NWMzNDc5YTlmNWFjZDExY2E2ZGM4YTApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNTVkYWRlMmM1ZDIzNDhmMTg2NzAzMGI4N2ExNjRlYjggPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDc5MjY3MDAwMDAwMDYsLTc5LjQxOTc0OTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzRmOTJmNjllYzczNjRjZWZiNDNjYzg4OWIzMzUyNTMzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Y0MzhhODg4YmE4NDQxMTRhZDc2OGVmYmQ4Yjk2OTBjID0gJCgnPGRpdiBpZD0iaHRtbF9mNDM4YTg4OGJhODQ0MTE0YWQ3NjhlZmJkOGI5NjkwYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGl0dGxlIFBvcnR1Z2FsLCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzRmOTJmNjllYzczNjRjZWZiNDNjYzg4OWIzMzUyNTMzLnNldENvbnRlbnQoaHRtbF9mNDM4YTg4OGJhODQ0MTE0YWQ3NjhlZmJkOGI5NjkwYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81NWRhZGUyYzVkMjM0OGYxODY3MDMwYjg3YTE2NGViOC5iaW5kUG9wdXAocG9wdXBfNGY5MmY2OWVjNzM2NGNlZmI0M2NjODg5YjMzNTI1MzMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzE5MDUwZTViNWJkNGZlM2FiYmE1NzYyMWE0ZDcwMTIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDc5MjY3MDAwMDAwMDYsLTc5LjQxOTc0OTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzg5Y2Y2NjUzMzEwMTQ1NmE5M2FhZTdiZWZiYzVkZjhiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzRiNWEwYmRmZDFjODQ3ZGVhZWJiZmIwOTcwMGQ4YzFhID0gJCgnPGRpdiBpZD0iaHRtbF80YjVhMGJkZmQxYzg0N2RlYWViYmZiMDk3MDBkOGMxYSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VHJpbml0eSwgV2VzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84OWNmNjY1MzMxMDE0NTZhOTNhYWU3YmVmYmM1ZGY4Yi5zZXRDb250ZW50KGh0bWxfNGI1YTBiZGZkMWM4NDdkZWFlYmJmYjA5NzAwZDhjMWEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzE5MDUwZTViNWJkNGZlM2FiYmE1NzYyMWE0ZDcwMTIuYmluZFBvcHVwKHBvcHVwXzg5Y2Y2NjUzMzEwMTQ1NmE5M2FhZTdiZWZiYzVkZjhiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzQwMDA1MjQyNmYwMTQ1ZTJhYTRhN2NkMDE1NWFmMjE3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjc5NTU3MSwtNzkuMzUyMTg4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jM2ZhYTJjODQzNDQ0YmE3OTBlOTFhZWRkNzFhY2EzNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81NmI2ZGM4MmM0NjY0NDg2ODA3MjRlMTEzMDkwMTcxNyA9ICQoJzxkaXYgaWQ9Imh0bWxfNTZiNmRjODJjNDY2NDQ4NjgwNzI0ZTExMzA5MDE3MTciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRoZSBEYW5mb3J0aCBXZXN0LCBFYXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2MzZmFhMmM4NDM0NDRiYTc5MGU5MWFlZGQ3MWFjYTM1LnNldENvbnRlbnQoaHRtbF81NmI2ZGM4MmM0NjY0NDg2ODA3MjRlMTEzMDkwMTcxNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80MDAwNTI0MjZmMDE0NWUyYWE0YTdjZDAxNTVhZjIxNy5iaW5kUG9wdXAocG9wdXBfYzNmYWEyYzg0MzQ0NGJhNzkwZTkxYWVkZDcxYWNhMzUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjY2ODUwY2FhODI1NDVhN2E0ZWY3YmZmYjU5ZDU0OGEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Nzk1NTcxLC03OS4zNTIxODhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzk0ODdhZDRmYTRiMDRiZjhhYzQ5MTU5YjBhZmM1MDJmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzJlZmQ4NGYzZmI0MTQ2NmNhYmJkYjBlYWE1MzdiZWQzID0gJCgnPGRpdiBpZD0iaHRtbF8yZWZkODRmM2ZiNDE0NjZjYWJiZGIwZWFhNTM3YmVkMyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Uml2ZXJkYWxlLCBFYXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzk0ODdhZDRmYTRiMDRiZjhhYzQ5MTU5YjBhZmM1MDJmLnNldENvbnRlbnQoaHRtbF8yZWZkODRmM2ZiNDE0NjZjYWJiZGIwZWFhNTM3YmVkMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82NjY4NTBjYWE4MjU0NWE3YTRlZjdiZmZiNTlkNTQ4YS5iaW5kUG9wdXAocG9wdXBfOTQ4N2FkNGZhNGIwNGJmOGFjNDkxNTliMGFmYzUwMmYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfM2ZjZTFiMGM5ZTg1NGE2ZTllOTUzMmFlMWQ3YzhmODcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDcxNzY4LC03OS4zODE1NzY0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODA4YzEyYmQ4ZmNlNDgyNzgwNzI0YmQ5MDgzN2UwMGEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZmZmOWQ0ZDc4ZDZjNGM5OGIxZDM2ODZhYWExMjY1N2MgPSAkKCc8ZGl2IGlkPSJodG1sX2ZmZjlkNGQ3OGQ2YzRjOThiMWQzNjg2YWFhMTI2NTdjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5EZXNpZ24gRXhjaGFuZ2UsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzgwOGMxMmJkOGZjZTQ4Mjc4MDcyNGJkOTA4MzdlMDBhLnNldENvbnRlbnQoaHRtbF9mZmY5ZDRkNzhkNmM0Yzk4YjFkMzY4NmFhYTEyNjU3Yyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zZmNlMWIwYzllODU0YTZlOWU5NTMyYWUxZDdjOGY4Ny5iaW5kUG9wdXAocG9wdXBfODA4YzEyYmQ4ZmNlNDgyNzgwNzI0YmQ5MDgzN2UwMGEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMTQxNjE1N2JhYjczNDIyOGI0ODNhNjY0MTQ1Mjg1MjIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDcxNzY4LC03OS4zODE1NzY0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfN2MyNjE5ODFiZDY3NGU5Y2E3NWE2OWRkNmFlZWZlNzkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNThkOThhNzljMmQ2NDI2Yzg4MGY5NDNmMWUyYWY0ZDAgPSAkKCc8ZGl2IGlkPSJodG1sXzU4ZDk4YTc5YzJkNjQyNmM4ODBmOTQzZjFlMmFmNGQwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Ub3JvbnRvIERvbWluaW9uIENlbnRyZSwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfN2MyNjE5ODFiZDY3NGU5Y2E3NWE2OWRkNmFlZWZlNzkuc2V0Q29udGVudChodG1sXzU4ZDk4YTc5YzJkNjQyNmM4ODBmOTQzZjFlMmFmNGQwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzE0MTYxNTdiYWI3MzQyMjhiNDgzYTY2NDE0NTI4NTIyLmJpbmRQb3B1cChwb3B1cF83YzI2MTk4MWJkNjc0ZTljYTc1YTY5ZGQ2YWVlZmU3OSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82MTZmMTQyYmQ1ZmU0ODcyOGEwMzNiOTMwNjlhMGQ5ZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYzNjg0NzIsLTc5LjQyODE5MTQwMDAwMDAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zMGU2MGY4ZjJmMmU0ZWY3OTYyNjllNTkzOGE2MzM1NiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wOTFlMDhlNzc1N2Q0MTVhYjA4Y2U0OGQxYzk4YjAxNiA9ICQoJzxkaXYgaWQ9Imh0bWxfMDkxZTA4ZTc3NTdkNDE1YWIwOGNlNDhkMWM5OGIwMTYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJyb2NrdG9uLCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzMwZTYwZjhmMmYyZTRlZjc5NjI2OWU1OTM4YTYzMzU2LnNldENvbnRlbnQoaHRtbF8wOTFlMDhlNzc1N2Q0MTVhYjA4Y2U0OGQxYzk4YjAxNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82MTZmMTQyYmQ1ZmU0ODcyOGEwMzNiOTMwNjlhMGQ5ZS5iaW5kUG9wdXAocG9wdXBfMzBlNjBmOGYyZjJlNGVmNzk2MjY5ZTU5MzhhNjMzNTYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzM1ZjZjODAwNjA1NDNhODk1ZWE4YzJhMGI2YTI5Y2IgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42MzY4NDcyLC03OS40MjgxOTE0MDAwMDAwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYTNkMTY4YTViYWY1NGRhZWFkNGYzOGQxMjQ0MTI1YjcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZWI0ZjAzOGI2YTE3NDJjMGI5YmQ0MzVjMDFhYWE4NTggPSAkKCc8ZGl2IGlkPSJodG1sX2ViNGYwMzhiNmExNzQyYzBiOWJkNDM1YzAxYWFhODU4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5FeGhpYml0aW9uIFBsYWNlLCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2EzZDE2OGE1YmFmNTRkYWVhZDRmMzhkMTI0NDEyNWI3LnNldENvbnRlbnQoaHRtbF9lYjRmMDM4YjZhMTc0MmMwYjliZDQzNWMwMWFhYTg1OCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83MzVmNmM4MDA2MDU0M2E4OTVlYThjMmEwYjZhMjljYi5iaW5kUG9wdXAocG9wdXBfYTNkMTY4YTViYWY1NGRhZWFkNGYzOGQxMjQ0MTI1YjcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMmUwYTVkYmQ0Yzg1NDRjNmI0ZWNkMzlkNWIyY2EwYjIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42MzY4NDcyLC03OS40MjgxOTE0MDAwMDAwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYmNjZjIxMTg1NTc3NGI0Y2E3MmE0Y2FhNDUzZTllMTQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTJkZmFhYjczNGY1NDhhZGI2YTAwZDNiYTQ4YTYzNmIgPSAkKCc8ZGl2IGlkPSJodG1sX2UyZGZhYWI3MzRmNTQ4YWRiNmEwMGQzYmE0OGE2MzZiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QYXJrZGFsZSBWaWxsYWdlLCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2JjY2YyMTE4NTU3NzRiNGNhNzJhNGNhYTQ1M2U5ZTE0LnNldENvbnRlbnQoaHRtbF9lMmRmYWFiNzM0ZjU0OGFkYjZhMDBkM2JhNDhhNjM2Yik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yZTBhNWRiZDRjODU0NGM2YjRlY2QzOWQ1YjJjYTBiMi5iaW5kUG9wdXAocG9wdXBfYmNjZjIxMTg1NTc3NGI0Y2E3MmE0Y2FhNDUzZTllMTQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMWQyODg5YmYxMWE1NGRiMGE4ZmM2MjVkYmM1NTBlOTcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Njg5OTg1LC03OS4zMTU1NzE1OTk5OTk5OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZGE5M2ZmMGNjYjYyNGE5NjkxM2FhZDk4YjIxNWNkMmUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYzE1NWVkOTRmM2QxNDUyZjliODNjZTc5YzFhYTdiMDMgPSAkKCc8ZGl2IGlkPSJodG1sX2MxNTVlZDk0ZjNkMTQ1MmY5YjgzY2U3OWMxYWE3YjAzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgQmVhY2hlcyBXZXN0LCBFYXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2RhOTNmZjBjY2I2MjRhOTY5MTNhYWQ5OGIyMTVjZDJlLnNldENvbnRlbnQoaHRtbF9jMTU1ZWQ5NGYzZDE0NTJmOWI4M2NlNzljMWFhN2IwMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xZDI4ODliZjExYTU0ZGIwYThmYzYyNWRiYzU1MGU5Ny5iaW5kUG9wdXAocG9wdXBfZGE5M2ZmMGNjYjYyNGE5NjkxM2FhZDk4YjIxNWNkMmUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYmEzZjAwYWIwZTY5NDQ1NmFkNjliYWYxZjQ4NTQ3NWYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Njg5OTg1LC03OS4zMTU1NzE1OTk5OTk5OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWM0ZmFiMmQ3ODhmNGRlYjg0Mzk1ZmE5ZDY5NTkyNDQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODQwOTViNjY4NDZkNGY0ZGJjNjg4ODY0NTRkZWE3NzUgPSAkKCc8ZGl2IGlkPSJodG1sXzg0MDk1YjY2ODQ2ZDRmNGRiYzY4ODg2NDU0ZGVhNzc1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5JbmRpYSBCYXphYXIsIEVhc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOWM0ZmFiMmQ3ODhmNGRlYjg0Mzk1ZmE5ZDY5NTkyNDQuc2V0Q29udGVudChodG1sXzg0MDk1YjY2ODQ2ZDRmNGRiYzY4ODg2NDU0ZGVhNzc1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2JhM2YwMGFiMGU2OTQ0NTZhZDY5YmFmMWY0ODU0NzVmLmJpbmRQb3B1cChwb3B1cF85YzRmYWIyZDc4OGY0ZGViODQzOTVmYTlkNjk1OTI0NCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yODFkZDhiZjAwZGU0N2QwYTY1NmEyOWI3NzEyYjYwYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODE5ODUsLTc5LjM3OTgxNjkwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83YTA4NmNmMGI0NWU0NjUzODVlOTJmODcyY2ZkMzM1NiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lNGMyMDRmMDQ4MjA0YTc2ODU3ZjRlNDQ1MGIxNjk3ZCA9ICQoJzxkaXYgaWQ9Imh0bWxfZTRjMjA0ZjA0ODIwNGE3Njg1N2Y0ZTQ0NTBiMTY5N2QiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNvbW1lcmNlIENvdXJ0LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83YTA4NmNmMGI0NWU0NjUzODVlOTJmODcyY2ZkMzM1Ni5zZXRDb250ZW50KGh0bWxfZTRjMjA0ZjA0ODIwNGE3Njg1N2Y0ZTQ0NTBiMTY5N2QpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMjgxZGQ4YmYwMGRlNDdkMGE2NTZhMjliNzcxMmI2MGMuYmluZFBvcHVwKHBvcHVwXzdhMDg2Y2YwYjQ1ZTQ2NTM4NWU5MmY4NzJjZmQzMzU2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2YwNmY3MzNmYmRkYTRjYzdiNTUxZTNjMzNlNDI3NzJhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ4MTk4NSwtNzkuMzc5ODE2OTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzg0ZmZhMTlmMWRiMzQ4ZGM4MDBhYzU2NDVjZmVmYzRmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Q1OWRjMTQxZGE4MzRjOTRhYzI4MzUwYzZjMmRlYjNjID0gJCgnPGRpdiBpZD0iaHRtbF9kNTlkYzE0MWRhODM0Yzk0YWMyODM1MGM2YzJkZWIzYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VmljdG9yaWEgSG90ZWwsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzg0ZmZhMTlmMWRiMzQ4ZGM4MDBhYzU2NDVjZmVmYzRmLnNldENvbnRlbnQoaHRtbF9kNTlkYzE0MWRhODM0Yzk0YWMyODM1MGM2YzJkZWIzYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mMDZmNzMzZmJkZGE0Y2M3YjU1MWUzYzMzZTQyNzcyYS5iaW5kUG9wdXAocG9wdXBfODRmZmExOWYxZGIzNDhkYzgwMGFjNTY0NWNmZWZjNGYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjU4YzcxOTg2OWE5NGI2YzlkOTlmMTY5ZWRlOTEyMTcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTk1MjU1LC03OS4zNDA5MjNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzY5ZGNiM2MzOGMyZjQ5ODJiZmFkZDAyNGY3NDc3ZjJlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzg3NDcxYWZkNTBiNjRkYWM4YmM0Y2E5NGNhY2ViOTYyID0gJCgnPGRpdiBpZD0iaHRtbF84NzQ3MWFmZDUwYjY0ZGFjOGJjNGNhOTRjYWNlYjk2MiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U3R1ZGlvIERpc3RyaWN0LCBFYXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzY5ZGNiM2MzOGMyZjQ5ODJiZmFkZDAyNGY3NDc3ZjJlLnNldENvbnRlbnQoaHRtbF84NzQ3MWFmZDUwYjY0ZGFjOGJjNGNhOTRjYWNlYjk2Mik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mNThjNzE5ODY5YTk0YjZjOWQ5OWYxNjllZGU5MTIxNy5iaW5kUG9wdXAocG9wdXBfNjlkY2IzYzM4YzJmNDk4MmJmYWRkMDI0Zjc0NzdmMmUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMWE5N2Q1YWZjYjEzNDUzYmJlYmE0NDcyZTZmOTI5ZTkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MjgwMjA1LC03OS4zODg3OTAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wMmExMWJjM2IyMGQ0MzIzOWUxMGRhMGU3OWFmMzkzMSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mOTk4YWY1NjA0ZGY0YjUzYTJkMTc4ZGNlOWFmNTE5OCA9ICQoJzxkaXYgaWQ9Imh0bWxfZjk5OGFmNTYwNGRmNGI1M2EyZDE3OGRjZTlhZjUxOTgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxhd3JlbmNlIFBhcmssIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDJhMTFiYzNiMjBkNDMyMzllMTBkYTBlNzlhZjM5MzEuc2V0Q29udGVudChodG1sX2Y5OThhZjU2MDRkZjRiNTNhMmQxNzhkY2U5YWY1MTk4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzFhOTdkNWFmY2IxMzQ1M2JiZWJhNDQ3MmU2ZjkyOWU5LmJpbmRQb3B1cChwb3B1cF8wMmExMWJjM2IyMGQ0MzIzOWUxMGRhMGU3OWFmMzkzMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kOGNlOTYxNTYwYWQ0NDg5YTE4ZDI3MjdhYmE2MTk2NyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxMTY5NDgsLTc5LjQxNjkzNTU5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mNjQ2MmNjNDBlM2I0NWRhOTdmZGZmMDc3OGQyY2NmNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81YmEwZTU1ZTBlYTI0ZDU5YTcyMmU3ZWZhOTg4NjBiOSA9ICQoJzxkaXYgaWQ9Imh0bWxfNWJhMGU1NWUwZWEyNGQ1OWE3MjJlN2VmYTk4ODYwYjkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJvc2VsYXduLCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Y2NDYyY2M0MGUzYjQ1ZGE5N2ZkZmYwNzc4ZDJjY2Y1LnNldENvbnRlbnQoaHRtbF81YmEwZTU1ZTBlYTI0ZDU5YTcyMmU3ZWZhOTg4NjBiOSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kOGNlOTYxNTYwYWQ0NDg5YTE4ZDI3MjdhYmE2MTk2Ny5iaW5kUG9wdXAocG9wdXBfZjY0NjJjYzQwZTNiNDVkYTk3ZmRmZjA3NzhkMmNjZjUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMWRiOTFkMjgwNGE0NDdjZTgxYWM1MDY2MmZlZjc4ODEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTI3NTExLC03OS4zOTAxOTc1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kOGFjMjYzNzRhMzM0MzEzYjQ0NDA5NjY3MjVlMTgzMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lMGY5NTNjZTM5NDQ0NGI5OWJlNjQ0NWU5MTA3OGJiNSA9ICQoJzxkaXYgaWQ9Imh0bWxfZTBmOTUzY2UzOTQ0NDRiOTliZTY0NDVlOTEwNzhiYjUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRhdmlzdmlsbGUgTm9ydGgsIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDhhYzI2Mzc0YTMzNDMxM2I0NDQwOTY2NzI1ZTE4MzIuc2V0Q29udGVudChodG1sX2UwZjk1M2NlMzk0NDQ0Yjk5YmU2NDQ1ZTkxMDc4YmI1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzFkYjkxZDI4MDRhNDQ3Y2U4MWFjNTA2NjJmZWY3ODgxLmJpbmRQb3B1cChwb3B1cF9kOGFjMjYzNzRhMzM0MzEzYjQ0NDA5NjY3MjVlMTgzMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82N2JiYjZhMTcxODg0MWIxODA5MjA0ODljMWNhMDM2ZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY5Njk0NzYsLTc5LjQxMTMwNzIwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wNDEzYjlmYzQwMGU0ZjAzYjc2OWFiZWFjNTU0ZjRkOCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82NDIyNWU3NTIzZWY0YjNiYjFmZmE1NzA2N2M3N2MxZSA9ICQoJzxkaXYgaWQ9Imh0bWxfNjQyMjVlNzUyM2VmNGIzYmIxZmZhNTcwNjdjNzdjMWUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZvcmVzdCBIaWxsIE5vcnRoLCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzA0MTNiOWZjNDAwZTRmMDNiNzY5YWJlYWM1NTRmNGQ4LnNldENvbnRlbnQoaHRtbF82NDIyNWU3NTIzZWY0YjNiYjFmZmE1NzA2N2M3N2MxZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82N2JiYjZhMTcxODg0MWIxODA5MjA0ODljMWNhMDM2ZS5iaW5kUG9wdXAocG9wdXBfMDQxM2I5ZmM0MDBlNGYwM2I3NjlhYmVhYzU1NGY0ZDgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTE1YmRhYjNiYzA4NDVkN2EyYWJkNWIyZDA4ODMxMzYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42OTY5NDc2LC03OS40MTEzMDcyMDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMjE3ODJmMjZkYWExNDk0ODgxYzAzZGI0ZmU5YWFkYzkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfY2IzODA0NDBiOWNlNGQzODhmOGYwNDE1MWVjMjJiOTQgPSAkKCc8ZGl2IGlkPSJodG1sX2NiMzgwNDQwYjljZTRkMzg4ZjhmMDQxNTFlYzIyYjk0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Gb3Jlc3QgSGlsbCBXZXN0LCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzIxNzgyZjI2ZGFhMTQ5NDg4MWMwM2RiNGZlOWFhZGM5LnNldENvbnRlbnQoaHRtbF9jYjM4MDQ0MGI5Y2U0ZDM4OGY4ZjA0MTUxZWMyMmI5NCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85MTViZGFiM2JjMDg0NWQ3YTJhYmQ1YjJkMDg4MzEzNi5iaW5kUG9wdXAocG9wdXBfMjE3ODJmMjZkYWExNDk0ODgxYzAzZGI0ZmU5YWFkYzkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjg5ZDMwZTUzNTg4NDE3ZGIwOGJhOGY3MTcyZTk0MDIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjE2MDgzLC03OS40NjQ3NjMyOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTlmNWVhYjE2Mjc1NDIyN2EwNDQ1MjgxMWIxMzI5ZTEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWZjMDA3YTc0NGMxNDQxY2E5YTRkYTlkNTFkOTY5ZTIgPSAkKCc8ZGl2IGlkPSJodG1sXzVmYzAwN2E3NDRjMTQ0MWNhOWE0ZGE5ZDUxZDk2OWUyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IaWdoIFBhcmssIFdlc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTlmNWVhYjE2Mjc1NDIyN2EwNDQ1MjgxMWIxMzI5ZTEuc2V0Q29udGVudChodG1sXzVmYzAwN2E3NDRjMTQ0MWNhOWE0ZGE5ZDUxZDk2OWUyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzI4OWQzMGU1MzU4ODQxN2RiMDhiYThmNzE3MmU5NDAyLmJpbmRQb3B1cChwb3B1cF85OWY1ZWFiMTYyNzU0MjI3YTA0NDUyODExYjEzMjllMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81NGEyMDBkNWQ3ZjY0YzEzOGUxNTA0MGJhYzEyN2ViNiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MTYwODMsLTc5LjQ2NDc2MzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lNDFlYmM2YzFjNzk0NjI5YTlmYThmMWJlMTAyNDUzNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jMjhlMDYxYmI3YTI0MDk2OWUyMWE4NzdhZjA2ODIxOCA9ICQoJzxkaXYgaWQ9Imh0bWxfYzI4ZTA2MWJiN2EyNDA5NjllMjFhODc3YWYwNjgyMTgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRoZSBKdW5jdGlvbiBTb3V0aCwgV2VzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lNDFlYmM2YzFjNzk0NjI5YTlmYThmMWJlMTAyNDUzNi5zZXRDb250ZW50KGh0bWxfYzI4ZTA2MWJiN2EyNDA5NjllMjFhODc3YWYwNjgyMTgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNTRhMjAwZDVkN2Y2NGMxMzhlMTUwNDBiYWMxMjdlYjYuYmluZFBvcHVwKHBvcHVwX2U0MWViYzZjMWM3OTQ2MjlhOWZhOGYxYmUxMDI0NTM2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzBmZmEwYWQ1OTM4MTQzNDhhNDU3YzE0NGViNWY3NDUyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzE1MzgzNCwtNzkuNDA1Njc4NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzY1MjBjOTM4NzMyZjQyNWJiYzc3ODEwZGRlYjUwNTAzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2VjNmQ5ODA4ZTkyZjRiNDI4OGE5YmRjNjFhOGNkZTYyID0gJCgnPGRpdiBpZD0iaHRtbF9lYzZkOTgwOGU5MmY0YjQyODhhOWJkYzYxYThjZGU2MiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Tm9ydGggVG9yb250byBXZXN0LCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzY1MjBjOTM4NzMyZjQyNWJiYzc3ODEwZGRlYjUwNTAzLnNldENvbnRlbnQoaHRtbF9lYzZkOTgwOGU5MmY0YjQyODhhOWJkYzYxYThjZGU2Mik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wZmZhMGFkNTkzODE0MzQ4YTQ1N2MxNDRlYjVmNzQ1Mi5iaW5kUG9wdXAocG9wdXBfNjUyMGM5Mzg3MzJmNDI1YmJjNzc4MTBkZGViNTA1MDMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYjMzYmY3ZGMzNjMxNDAzZGEyY2YxZDczNWYxMmRmY2EgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NzI3MDk3LC03OS40MDU2Nzg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNjQzM2M2MjBkOTBkNGY0ZmE4NmFlYzk1NzQzYTQ5MDYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTk5NDBhZjIxZTc2NGUxNTk5YzEyOWIxZmIxMzc0YmUgPSAkKCc8ZGl2IGlkPSJodG1sX2U5OTQwYWYyMWU3NjRlMTU5OWMxMjliMWZiMTM3NGJlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgQW5uZXgsIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjQzM2M2MjBkOTBkNGY0ZmE4NmFlYzk1NzQzYTQ5MDYuc2V0Q29udGVudChodG1sX2U5OTQwYWYyMWU3NjRlMTU5OWMxMjliMWZiMTM3NGJlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2IzM2JmN2RjMzYzMTQwM2RhMmNmMWQ3MzVmMTJkZmNhLmJpbmRQb3B1cChwb3B1cF82NDMzYzYyMGQ5MGQ0ZjRmYTg2YWVjOTU3NDNhNDkwNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hODUzNzhjMTc5NmE0MGYwYjkxYjc1YmNhN2JjNWE0MSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3MjcwOTcsLTc5LjQwNTY3ODQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zZjUzOGY1MWZmMzg0ZTFmYWY5YzRmNmJmODFiYzRjOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83ZGU2ZmFlYWQ2OTk0NTY4YmFjZWNhNGIwMGQ5MGE2NyA9ICQoJzxkaXYgaWQ9Imh0bWxfN2RlNmZhZWFkNjk5NDU2OGJhY2VjYTRiMDBkOTBhNjciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5vcnRoIE1pZHRvd24sIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfM2Y1MzhmNTFmZjM4NGUxZmFmOWM0ZjZiZjgxYmM0Yzkuc2V0Q29udGVudChodG1sXzdkZTZmYWVhZDY5OTQ1NjhiYWNlY2E0YjAwZDkwYTY3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2E4NTM3OGMxNzk2YTQwZjBiOTFiNzViY2E3YmM1YTQxLmJpbmRQb3B1cChwb3B1cF8zZjUzOGY1MWZmMzg0ZTFmYWY5YzRmNmJmODFiYzRjOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wMzkxMzhmMDI5YzU0ZGQ2YjQyNmVlM2JkYzBkMjhlMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3MjcwOTcsLTc5LjQwNTY3ODQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82N2I0OTIwNGIyNjQ0MmViYTgxODZmMTc5ODE4YmQ2MyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82NGZlZmU4NGQ4N2Y0OTM5ODZiODQyNTM4Y2VjMDFjOSA9ICQoJzxkaXYgaWQ9Imh0bWxfNjRmZWZlODRkODdmNDkzOTg2Yjg0MjUzOGNlYzAxYzkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPllvcmt2aWxsZSwgQ2VudHJhbCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82N2I0OTIwNGIyNjQ0MmViYTgxODZmMTc5ODE4YmQ2My5zZXRDb250ZW50KGh0bWxfNjRmZWZlODRkODdmNDkzOTg2Yjg0MjUzOGNlYzAxYzkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDM5MTM4ZjAyOWM1NGRkNmI0MjZlZTNiZGMwZDI4ZTEuYmluZFBvcHVwKHBvcHVwXzY3YjQ5MjA0YjI2NDQyZWJhODE4NmYxNzk4MThiZDYzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzA5YzUwOTEzZTc0MjQ1NTVhNWZmZTMwYTQzYWFhMDMyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ4OTU5NywtNzkuNDU2MzI1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yMjQxOWQ2MTUwYTk0Mzc1OTYzOWZhY2Y5Yjk2NTgxYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yZjlhN2E5NGE5YTQ0OWE2OTcyYzgxZGZjMjEyY2Q4OCA9ICQoJzxkaXYgaWQ9Imh0bWxfMmY5YTdhOTRhOWE0NDlhNjk3MmM4MWRmYzIxMmNkODgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBhcmtkYWxlLCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzIyNDE5ZDYxNTBhOTQzNzU5NjM5ZmFjZjliOTY1ODFhLnNldENvbnRlbnQoaHRtbF8yZjlhN2E5NGE5YTQ0OWE2OTcyYzgxZGZjMjEyY2Q4OCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wOWM1MDkxM2U3NDI0NTU1YTVmZmUzMGE0M2FhYTAzMi5iaW5kUG9wdXAocG9wdXBfMjI0MTlkNjE1MGE5NDM3NTk2MzlmYWNmOWI5NjU4MWEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODZmMDE4NzdkMGIyNDE4Njg1YjY0OTlkNTY2NzExNjYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDg5NTk3LC03OS40NTYzMjVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzYyZDIxYTVmMjA2NTQ4NmZiNTU4MjBjMDAwYTMwOTNlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzc5MTQyNzU1Yjk1NjQ2YzI4OTc5ODA1NWQyMGFkNGQ2ID0gJCgnPGRpdiBpZD0iaHRtbF83OTE0Mjc1NWI5NTY0NmMyODk3OTgwNTVkMjBhZDRkNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Um9uY2VzdmFsbGVzLCBXZXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzYyZDIxYTVmMjA2NTQ4NmZiNTU4MjBjMDAwYTMwOTNlLnNldENvbnRlbnQoaHRtbF83OTE0Mjc1NWI5NTY0NmMyODk3OTgwNTVkMjBhZDRkNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84NmYwMTg3N2QwYjI0MTg2ODViNjQ5OWQ1NjY3MTE2Ni5iaW5kUG9wdXAocG9wdXBfNjJkMjFhNWYyMDY1NDg2ZmI1NTgyMGMwMDBhMzA5M2UpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNTcxYjg0YjQ2ZGIxNDlkNWI5ZmFmYzk5NzY3N2Q5NjYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MDQzMjQ0LC03OS4zODg3OTAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iZDVjOGMzNzg4YTY0ZTI3YTJmOWFlMDVjOGI1YzU2MiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zYjliZTU4M2I1OTA0NTNhOTAyZjYzYWJhYWIzOTNmOCA9ICQoJzxkaXYgaWQ9Imh0bWxfM2I5YmU1ODNiNTkwNDUzYTkwMmY2M2FiYWFiMzkzZjgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRhdmlzdmlsbGUsIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYmQ1YzhjMzc4OGE2NGUyN2EyZjlhZTA1YzhiNWM1NjIuc2V0Q29udGVudChodG1sXzNiOWJlNTgzYjU5MDQ1M2E5MDJmNjNhYmFhYjM5M2Y4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzU3MWI4NGI0NmRiMTQ5ZDViOWZhZmM5OTc2NzdkOTY2LmJpbmRQb3B1cChwb3B1cF9iZDVjOGMzNzg4YTY0ZTI3YTJmOWFlMDVjOGI1YzU2Mik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83NTQxM2ZhMzE3ZDg0NWE4YTA1MjdkYTgxNzczYjYwMyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MjY5NTYsLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzNiOGEwOGVhMGNkYjQ4MThhZWU1MGNjNWQ2MGFjNGRkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzdiNDE5ZWRjM2Y4YzRjNDg5YWQ3M2MwN2I2MjQ2YzMyID0gJCgnPGRpdiBpZD0iaHRtbF83YjQxOWVkYzNmOGM0YzQ4OWFkNzNjMDdiNjI0NmMzMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SGFyYm9yZCwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfM2I4YTA4ZWEwY2RiNDgxOGFlZTUwY2M1ZDYwYWM0ZGQuc2V0Q29udGVudChodG1sXzdiNDE5ZWRjM2Y4YzRjNDg5YWQ3M2MwN2I2MjQ2YzMyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzc1NDEzZmEzMTdkODQ1YThhMDUyN2RhODE3NzNiNjAzLmJpbmRQb3B1cChwb3B1cF8zYjhhMDhlYTBjZGI0ODE4YWVlNTBjYzVkNjBhYzRkZCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kYjgyMDllYzgwZWM0MDQyOGU3ZWNiYzhjMzIxYWQxZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MjY5NTYsLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2E3ZmNjYzVhMTAzMjQyMGRhNjIxZGRhMjViNjgyYzkyID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzQ0MGE3OWNkY2FiZTQ0Y2Y4ZjJlOTNlZjRhM2Y5MWJiID0gJCgnPGRpdiBpZD0iaHRtbF80NDBhNzljZGNhYmU0NGNmOGYyZTkzZWY0YTNmOTFiYiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VW5pdmVyc2l0eSBvZiBUb3JvbnRvLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hN2ZjY2M1YTEwMzI0MjBkYTYyMWRkYTI1YjY4MmM5Mi5zZXRDb250ZW50KGh0bWxfNDQwYTc5Y2RjYWJlNDRjZjhmMmU5M2VmNGEzZjkxYmIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZGI4MjA5ZWM4MGVjNDA0MjhlN2VjYmM4YzMyMWFkMWYuYmluZFBvcHVwKHBvcHVwX2E3ZmNjYzVhMTAzMjQyMGRhNjIxZGRhMjViNjgyYzkyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzY1MDZhYTZkYTBhODRlOGU4ODYyMzI0Yzg0NGE4ZTE2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUxNTcwNiwtNzkuNDg0NDQ5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNjgwYTY1ZjE1MjM0NDIxZTljZjBjZDgxNDExY2FjZDEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDdhODZjMjFmMjYxNDIwZjlmMDI5YzM0NmMzMWNkMWEgPSAkKCc8ZGl2IGlkPSJodG1sXzQ3YTg2YzIxZjI2MTQyMGY5ZjAyOWMzNDZjMzFjZDFhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5SdW5ueW1lZGUsIFdlc3QgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjgwYTY1ZjE1MjM0NDIxZTljZjBjZDgxNDExY2FjZDEuc2V0Q29udGVudChodG1sXzQ3YTg2YzIxZjI2MTQyMGY5ZjAyOWMzNDZjMzFjZDFhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzY1MDZhYTZkYTBhODRlOGU4ODYyMzI0Yzg0NGE4ZTE2LmJpbmRQb3B1cChwb3B1cF82ODBhNjVmMTUyMzQ0MjFlOWNmMGNkODE0MTFjYWNkMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iMjM2YjQ4MTViNzc0YmE2YWYwNDY4NWFhMjA0NmU3YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MTU3MDYsLTc5LjQ4NDQ0OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzlmNDdmMjlkYTlmYjRlZjRiZWEwNTI0OGJiOGNkOGQ5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzg0ZGQ1MzA4NjZjNzQxM2FhM2Q0OTNmN2I4MDUwNDk4ID0gJCgnPGRpdiBpZD0iaHRtbF84NGRkNTMwODY2Yzc0MTNhYTNkNDkzZjdiODA1MDQ5OCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U3dhbnNlYSwgV2VzdCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85ZjQ3ZjI5ZGE5ZmI0ZWY0YmVhMDUyNDhiYjhjZDhkOS5zZXRDb250ZW50KGh0bWxfODRkZDUzMDg2NmM3NDEzYWEzZDQ5M2Y3YjgwNTA0OTgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYjIzNmI0ODE1Yjc3NGJhNmFmMDQ2ODVhYTIwNDZlN2MuYmluZFBvcHVwKHBvcHVwXzlmNDdmMjlkYTlmYjRlZjRiZWEwNTI0OGJiOGNkOGQ5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzZmOThjNmI4M2JjZDQ1YThhNjZiOTQyZDViMjVjNjQ0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg5NTc0MywtNzkuMzgzMTU5OTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2U3YzU1ZDg2MGU5YTRiNGRiYmE4NWJjZDY2Y2MzOWFiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Y2ZmIxZjBhMTM5ODRhMmJhMGEzNzE2M2VmZTY1NDE5ID0gJCgnPGRpdiBpZD0iaHRtbF9mNmZiMWYwYTEzOTg0YTJiYTBhMzcxNjNlZmU2NTQxOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TW9vcmUgUGFyaywgQ2VudHJhbCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lN2M1NWQ4NjBlOWE0YjRkYmJhODViY2Q2NmNjMzlhYi5zZXRDb250ZW50KGh0bWxfZjZmYjFmMGExMzk4NGEyYmEwYTM3MTYzZWZlNjU0MTkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNmY5OGM2YjgzYmNkNDVhOGE2NmI5NDJkNWIyNWM2NDQuYmluZFBvcHVwKHBvcHVwX2U3YzU1ZDg2MGU5YTRiNGRiYmE4NWJjZDY2Y2MzOWFiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2IyYmJhOGI5ZTZhMjRkYzNhOTZlMDM1Y2YxZjJjYjBkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg5NTc0MywtNzkuMzgzMTU5OTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzI2YzZjOGRhYTAyMTQxMDhiNTlmNjAxNDhiMTUwNDZlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2YzZGM1MDVmZWFkNDQ0NGM5ZGNiYjYyYTRkMGQ3ZDZiID0gJCgnPGRpdiBpZD0iaHRtbF9mM2RjNTA1ZmVhZDQ0NDRjOWRjYmI2MmE0ZDBkN2Q2YiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U3VtbWVyaGlsbCBFYXN0LCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzI2YzZjOGRhYTAyMTQxMDhiNTlmNjAxNDhiMTUwNDZlLnNldENvbnRlbnQoaHRtbF9mM2RjNTA1ZmVhZDQ0NDRjOWRjYmI2MmE0ZDBkN2Q2Yik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iMmJiYThiOWU2YTI0ZGMzYTk2ZTAzNWNmMWYyY2IwZC5iaW5kUG9wdXAocG9wdXBfMjZjNmM4ZGFhMDIxNDEwOGI1OWY2MDE0OGIxNTA0NmUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNDgyY2E2N2Y5MWUyNGFiZTg2MGMwNDA5OGRjMzUxZjUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTMyMDU3LC03OS40MDAwNDkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lYTRiNTBkZjY3ZGQ0OGZlYjNlOTI5ZGU1NzBjNjkzMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82NDY1NjMxNDE0OGQ0ZmUwYTgxYTAxNzA1NGQ2MTc4OCA9ICQoJzxkaXYgaWQ9Imh0bWxfNjQ2NTYzMTQxNDhkNGZlMGE4MWEwMTcwNTRkNjE3ODgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNoaW5hdG93biwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZWE0YjUwZGY2N2RkNDhmZWIzZTkyOWRlNTcwYzY5MzIuc2V0Q29udGVudChodG1sXzY0NjU2MzE0MTQ4ZDRmZTBhODFhMDE3MDU0ZDYxNzg4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzQ4MmNhNjdmOTFlMjRhYmU4NjBjMDQwOThkYzM1MWY1LmJpbmRQb3B1cChwb3B1cF9lYTRiNTBkZjY3ZGQ0OGZlYjNlOTI5ZGU1NzBjNjkzMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82NTA2YWFiNjI2NmI0NGZhOThiNDU4NjZkYjhlYzZjYSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MzIwNTcsLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzljODY1MDIyNTZkMzQyZGRiOWNlYzJjYzRkZmM2N2RjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2RkMGM5MzcwYmY2OTQxYjJhNTNjZmJlYmY4MmE4MWUwID0gJCgnPGRpdiBpZD0iaHRtbF9kZDBjOTM3MGJmNjk0MWIyYTUzY2ZiZWJmODJhODFlMCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+R3JhbmdlIFBhcmssIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzljODY1MDIyNTZkMzQyZGRiOWNlYzJjYzRkZmM2N2RjLnNldENvbnRlbnQoaHRtbF9kZDBjOTM3MGJmNjk0MWIyYTUzY2ZiZWJmODJhODFlMCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82NTA2YWFiNjI2NmI0NGZhOThiNDU4NjZkYjhlYzZjYS5iaW5kUG9wdXAocG9wdXBfOWM4NjUwMjI1NmQzNDJkZGI5Y2VjMmNjNGRmYzY3ZGMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjAzODg1OGJkOTk4NDJkZDgyNzllZjM5NzdjODUwZjIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTMyMDU3LC03OS40MDAwNDkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ODQ2NTM0YThmOGI0YWUxODhiOWVhNDc4ZGY2YzE0YSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xZDYzMWE1MTRiODc0NmE4YWFlY2EwZjY2ZWIyYmIzNSA9ICQoJzxkaXYgaWQ9Imh0bWxfMWQ2MzFhNTE0Yjg3NDZhOGFhZWNhMGY2NmViMmJiMzUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPktlbnNpbmd0b24gTWFya2V0LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83ODQ2NTM0YThmOGI0YWUxODhiOWVhNDc4ZGY2YzE0YS5zZXRDb250ZW50KGh0bWxfMWQ2MzFhNTE0Yjg3NDZhOGFhZWNhMGY2NmViMmJiMzUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNjAzODg1OGJkOTk4NDJkZDgyNzllZjM5NzdjODUwZjIuYmluZFBvcHVwKHBvcHVwXzc4NDY1MzRhOGY4YjRhZTE4OGI5ZWE0NzhkZjZjMTRhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzIyMmM2M2NiYjQzZDQyYmRhMmNiYmY0NDhkOGZkMDMxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg2NDEyMjk5OTk5OTksLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2NkODAxNzcyMjA0MzRiZDE4NTk2NzczM2NkOTZlMmM5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2FhN2Q0N2M4MWU3NDQ3YWE4YWM2ZjJkNWZhZTMxZWY1ID0gJCgnPGRpdiBpZD0iaHRtbF9hYTdkNDdjODFlNzQ0N2FhOGFjNmYyZDVmYWUzMWVmNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RGVlciBQYXJrLCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2NkODAxNzcyMjA0MzRiZDE4NTk2NzczM2NkOTZlMmM5LnNldENvbnRlbnQoaHRtbF9hYTdkNDdjODFlNzQ0N2FhOGFjNmYyZDVmYWUzMWVmNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yMjJjNjNjYmI0M2Q0MmJkYTJjYmJmNDQ4ZDhmZDAzMS5iaW5kUG9wdXAocG9wdXBfY2Q4MDE3NzIyMDQzNGJkMTg1OTY3NzMzY2Q5NmUyYzkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTFlODM5ZjE0YmE3NDcxZGE0MTg4Mzc0ZjhhYmMyZDIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODY0MTIyOTk5OTk5OSwtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTZmZTUwYzJhZjZlNDRlZWE2YWU1MjZiYWQ4Mjc5OWYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYjJmOTBhZTE0YjE3NGIyZjlmZTUwYzNhMDdkOGE2NmYgPSAkKCc8ZGl2IGlkPSJodG1sX2IyZjkwYWUxNGIxNzRiMmY5ZmU1MGMzYTA3ZDhhNjZmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Gb3Jlc3QgSGlsbCBTRSwgQ2VudHJhbCBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85NmZlNTBjMmFmNmU0NGVlYTZhZTUyNmJhZDgyNzk5Zi5zZXRDb250ZW50KGh0bWxfYjJmOTBhZTE0YjE3NGIyZjlmZTUwYzNhMDdkOGE2NmYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTFlODM5ZjE0YmE3NDcxZGE0MTg4Mzc0ZjhhYmMyZDIuYmluZFBvcHVwKHBvcHVwXzk2ZmU1MGMyYWY2ZTQ0ZWVhNmFlNTI2YmFkODI3OTlmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzk1ZTYzYjg2OWM2YTRmYmViOTIzN2EyYmM2MTg5YzM0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg2NDEyMjk5OTk5OTksLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzEzOWJiNWFkY2EyMzRkYTk5MTk3ZmYxM2I0ZTQwMDM2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2NmOGUxM2NkNGUwNjQ1ZTI5ODUwOGYwNDBiZTBkY2E1ID0gJCgnPGRpdiBpZD0iaHRtbF9jZjhlMTNjZDRlMDY0NWUyOTg1MDhmMDQwYmUwZGNhNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UmF0aG5lbGx5LCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzEzOWJiNWFkY2EyMzRkYTk5MTk3ZmYxM2I0ZTQwMDM2LnNldENvbnRlbnQoaHRtbF9jZjhlMTNjZDRlMDY0NWUyOTg1MDhmMDQwYmUwZGNhNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85NWU2M2I4NjljNmE0ZmJlYjkyMzdhMmJjNjE4OWMzNC5iaW5kUG9wdXAocG9wdXBfMTM5YmI1YWRjYTIzNGRhOTkxOTdmZjEzYjRlNDAwMzYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMWI4NjRhOTk1Y2M3NDlhZjk4NDQzMjAwMTM2MjkzMTQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODY0MTIyOTk5OTk5OSwtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMWNjZjk5YTgxMDI1NDJkNmEwYzk3MDViMDIwODQ1YWMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDFiYjE4ODk4NDZjNGQ2Y2ExYmFkNmQ5MTljOTY5MDYgPSAkKCc8ZGl2IGlkPSJodG1sX2QxYmIxODg5ODQ2YzRkNmNhMWJhZDZkOTE5Yzk2OTA2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Tb3V0aCBIaWxsLCBDZW50cmFsIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzFjY2Y5OWE4MTAyNTQyZDZhMGM5NzA1YjAyMDg0NWFjLnNldENvbnRlbnQoaHRtbF9kMWJiMTg4OTg0NmM0ZDZjYTFiYWQ2ZDkxOWM5NjkwNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xYjg2NGE5OTVjYzc0OWFmOTg0NDMyMDAxMzYyOTMxNC5iaW5kUG9wdXAocG9wdXBfMWNjZjk5YTgxMDI1NDJkNmEwYzk3MDViMDIwODQ1YWMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNTNlY2ZhZDc2MjFhNDRiZGI0NjZiODFkMWQwYjhlZjIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODY0MTIyOTk5OTk5OSwtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYTkwNTgyMTVjNTQ3NDk3ZGJmNzg2NWQ1ODBhZjAwMDEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzk3OTMxNGFhMWI1NGQ5ZDk5YzFjY2E4NjJkN2FjNTIgPSAkKCc8ZGl2IGlkPSJodG1sXzc5NzkzMTRhYTFiNTRkOWQ5OWMxY2NhODYyZDdhYzUyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdW1tZXJoaWxsIFdlc3QsIENlbnRyYWwgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYTkwNTgyMTVjNTQ3NDk3ZGJmNzg2NWQ1ODBhZjAwMDEuc2V0Q29udGVudChodG1sXzc5NzkzMTRhYTFiNTRkOWQ5OWMxY2NhODYyZDdhYzUyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzUzZWNmYWQ3NjIxYTQ0YmRiNDY2YjgxZDFkMGI4ZWYyLmJpbmRQb3B1cChwb3B1cF9hOTA1ODIxNWM1NDc0OTdkYmY3ODY1ZDU4MGFmMDAwMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8xMWM2YjVlNjYyYTY0ZTNhOTdiNzY5MTIxYzAwMTA1YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYyODk0NjcsLTc5LjM5NDQxOTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzlmZWE3MjljYWJiMTRjNDhhOGI1ODMyMzVmMmNiNWVkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzFmNWNjZWNlNGNlODQ1NDk5NjI3NDkyYmU2YzY0MDY4ID0gJCgnPGRpdiBpZD0iaHRtbF8xZjVjY2VjZTRjZTg0NTQ5OTYyNzQ5MmJlNmM2NDA2OCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q04gVG93ZXIsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzlmZWE3MjljYWJiMTRjNDhhOGI1ODMyMzVmMmNiNWVkLnNldENvbnRlbnQoaHRtbF8xZjVjY2VjZTRjZTg0NTQ5OTYyNzQ5MmJlNmM2NDA2OCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xMWM2YjVlNjYyYTY0ZTNhOTdiNzY5MTIxYzAwMTA1Yy5iaW5kUG9wdXAocG9wdXBfOWZlYTcyOWNhYmIxNGM0OGE4YjU4MzIzNWYyY2I1ZWQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMGQzMzlmNDcwZDhjNDI3Njk3YTFkODM5NTQxNjcwNmQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Mjg5NDY3LC03OS4zOTQ0MTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ZDIwY2JhMTdjMTk0YWJiYWQ5YWEwMzc3NmY4NmI0YSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xNTUxOWMyZjJlZWI0MWIyYmU1ZDRkNDk4MWM1M2ZkMiA9ICQoJzxkaXYgaWQ9Imh0bWxfMTU1MTljMmYyZWViNDFiMmJlNWQ0ZDQ5ODFjNTNmZDIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJhdGh1cnN0IFF1YXksIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzdkMjBjYmExN2MxOTRhYmJhZDlhYTAzNzc2Zjg2YjRhLnNldENvbnRlbnQoaHRtbF8xNTUxOWMyZjJlZWI0MWIyYmU1ZDRkNDk4MWM1M2ZkMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wZDMzOWY0NzBkOGM0Mjc2OTdhMWQ4Mzk1NDE2NzA2ZC5iaW5kUG9wdXAocG9wdXBfN2QyMGNiYTE3YzE5NGFiYmFkOWFhMDM3NzZmODZiNGEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMTZiYmFjN2JlYzg0NDVlN2I0OGMzNmM5ZTMzOWE4ZGMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Mjg5NDY3LC03OS4zOTQ0MTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80YjZhOWFmMmRkOTQ0YWEyODRmMTJjOWY2NWRiMWE4ZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wZmQ4ZWMxMWIzMWE0MmNiODg4ZTYzNWJiNmY5NTc3OSA9ICQoJzxkaXYgaWQ9Imh0bWxfMGZkOGVjMTFiMzFhNDJjYjg4OGU2MzViYjZmOTU3NzkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPklzbGFuZCBhaXJwb3J0LCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80YjZhOWFmMmRkOTQ0YWEyODRmMTJjOWY2NWRiMWE4ZS5zZXRDb250ZW50KGh0bWxfMGZkOGVjMTFiMzFhNDJjYjg4OGU2MzViYjZmOTU3NzkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMTZiYmFjN2JlYzg0NDVlN2I0OGMzNmM5ZTMzOWE4ZGMuYmluZFBvcHVwKHBvcHVwXzRiNmE5YWYyZGQ5NDRhYTI4NGYxMmM5ZjY1ZGIxYThlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2M3Y2RkMGM3NmE0OTQwY2RhNmQyNmFhMGZmMDFkMTgxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjI4OTQ2NywtNzkuMzk0NDE5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMWU5YWE5M2I0MTc1NDE0YWExYmM2MGEwMzhhNzU0ZDEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTQzMGRmODkzNDBiNGMwMmJmOWMyNWZhNGJkMjI1NzUgPSAkKCc8ZGl2IGlkPSJodG1sX2U0MzBkZjg5MzQwYjRjMDJiZjljMjVmYTRiZDIyNTc1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IYXJib3VyZnJvbnQgV2VzdCwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMWU5YWE5M2I0MTc1NDE0YWExYmM2MGEwMzhhNzU0ZDEuc2V0Q29udGVudChodG1sX2U0MzBkZjg5MzQwYjRjMDJiZjljMjVmYTRiZDIyNTc1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2M3Y2RkMGM3NmE0OTQwY2RhNmQyNmFhMGZmMDFkMTgxLmJpbmRQb3B1cChwb3B1cF8xZTlhYTkzYjQxNzU0MTRhYTFiYzYwYTAzOGE3NTRkMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82NmZiODRiMmRkZjA0YWJkYTgwYzBhY2MzZTdlZGQzMCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYyODk0NjcsLTc5LjM5NDQxOTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzZhY2UxODBmMDdhMDRhYWRiODRjZGM1MTBhOGU4NTZhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzhhZWVjMzUyZjE2MjQyMWJhZWFkOGQwNjNkMWU2Y2U5ID0gJCgnPGRpdiBpZD0iaHRtbF84YWVlYzM1MmYxNjI0MjFiYWVhZDhkMDYzZDFlNmNlOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+S2luZyBhbmQgU3BhZGluYSwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNmFjZTE4MGYwN2EwNGFhZGI4NGNkYzUxMGE4ZTg1NmEuc2V0Q29udGVudChodG1sXzhhZWVjMzUyZjE2MjQyMWJhZWFkOGQwNjNkMWU2Y2U5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzY2ZmI4NGIyZGRmMDRhYmRhODBjMGFjYzNlN2VkZDMwLmJpbmRQb3B1cChwb3B1cF82YWNlMTgwZjA3YTA0YWFkYjg0Y2RjNTEwYThlODU2YSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81NDMwNjIyOGY5ODM0NTBkYjM0MzAwNDVkNTNjZDcwNyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYyODk0NjcsLTc5LjM5NDQxOTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzI4MmVjNGIwMmJjNTQxYzFiNGY1MDc1ZTNhYjFiM2IzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2RlMmQ0NDRhYjAzMDRkM2RhOTIxMjk0YzY3N2QxNjNjID0gJCgnPGRpdiBpZD0iaHRtbF9kZTJkNDQ0YWIwMzA0ZDNkYTkyMTI5NGM2NzdkMTYzYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UmFpbHdheSBMYW5kcywgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjgyZWM0YjAyYmM1NDFjMWI0ZjUwNzVlM2FiMWIzYjMuc2V0Q29udGVudChodG1sX2RlMmQ0NDRhYjAzMDRkM2RhOTIxMjk0YzY3N2QxNjNjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzU0MzA2MjI4Zjk4MzQ1MGRiMzQzMDA0NWQ1M2NkNzA3LmJpbmRQb3B1cChwb3B1cF8yODJlYzRiMDJiYzU0MWMxYjRmNTA3NWUzYWIxYjNiMyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jZDg3ZDZmNjg0ZGU0ZTU0YjdiNGRlNDEyODkwNTljOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYyODk0NjcsLTc5LjM5NDQxOTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2QxODg5YTkwM2VmZDQ0ZDNhYzM1YWE3NDI1MTAyOTY3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2M1MWMwOGUxZGRmNjRiN2NhZWExOTA2MzdjZDhiNjExID0gJCgnPGRpdiBpZD0iaHRtbF9jNTFjMDhlMWRkZjY0YjdjYWVhMTkwNjM3Y2Q4YjYxMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U291dGggTmlhZ2FyYSwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDE4ODlhOTAzZWZkNDRkM2FjMzVhYTc0MjUxMDI5Njcuc2V0Q29udGVudChodG1sX2M1MWMwOGUxZGRmNjRiN2NhZWExOTA2MzdjZDhiNjExKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2NkODdkNmY2ODRkZTRlNTRiN2I0ZGU0MTI4OTA1OWM4LmJpbmRQb3B1cChwb3B1cF9kMTg4OWE5MDNlZmQ0NGQzYWMzNWFhNzQyNTEwMjk2Nyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zYWExMjI1NzhkYmU0ZTg5YjhhODhmZmM0ODA2MmQzOSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3OTU2MjYsLTc5LjM3NzUyOTQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hOGQwOTViYzQwNDQ0NGI5YmYzNjhiODE3NGZhMmE2MyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lYjBmM2MxYzVkYWU0Mjk1YjNlMDRkY2YyMmIxNDJkNSA9ICQoJzxkaXYgaWQ9Imh0bWxfZWIwZjNjMWM1ZGFlNDI5NWIzZTA0ZGNmMjJiMTQyZDUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJvc2VkYWxlLCBEb3dudG93biBUb3JvbnRvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hOGQwOTViYzQwNDQ0NGI5YmYzNjhiODE3NGZhMmE2My5zZXRDb250ZW50KGh0bWxfZWIwZjNjMWM1ZGFlNDI5NWIzZTA0ZGNmMjJiMTQyZDUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfM2FhMTIyNTc4ZGJlNGU4OWI4YTg4ZmZjNDgwNjJkMzkuYmluZFBvcHVwKHBvcHVwX2E4ZDA5NWJjNDA0NDQ0YjliZjM2OGI4MTc0ZmEyYTYzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzViYjYwNmMzNThjMzRiNTBiMmNkYWVkNzlmNzE5ZjkzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ2NDM1MiwtNzkuMzc0ODQ1OTk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzJiNjdmM2Y1MTA0YzQ2ZjhhN2ZjMjk3M2RlNDhmODQxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzk3YzQ5NjczMjY1YTQ3MDJiNjJiZTdkMzBmYzA0OTg5ID0gJCgnPGRpdiBpZD0iaHRtbF85N2M0OTY3MzI2NWE0NzAyYjYyYmU3ZDMwZmMwNDk4OSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U3RuIEEgUE8gQm94ZXMgMjUgVGhlIEVzcGxhbmFkZSwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMmI2N2YzZjUxMDRjNDZmOGE3ZmMyOTczZGU0OGY4NDEuc2V0Q29udGVudChodG1sXzk3YzQ5NjczMjY1YTQ3MDJiNjJiZTdkMzBmYzA0OTg5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzViYjYwNmMzNThjMzRiNTBiMmNkYWVkNzlmNzE5ZjkzLmJpbmRQb3B1cChwb3B1cF8yYjY3ZjNmNTEwNGM0NmY4YTdmYzI5NzNkZTQ4Zjg0MSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83MjlkOTE5YjQyZmY0ZTEwOTU3ZmU1NjViNTczMzVlZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2Nzk2NywtNzkuMzY3Njc1M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMWIwYzM2Y2I5ZTRjNDc2ODkzMTkzNjVjYTEzOTIwMmUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYzgzZDQ2NWJiZDU4NDcyNDhiYzBiMmZmZjA5ZDI4ZDYgPSAkKCc8ZGl2IGlkPSJodG1sX2M4M2Q0NjViYmQ1ODQ3MjQ4YmMwYjJmZmYwOWQyOGQ2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DYWJiYWdldG93biwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMWIwYzM2Y2I5ZTRjNDc2ODkzMTkzNjVjYTEzOTIwMmUuc2V0Q29udGVudChodG1sX2M4M2Q0NjViYmQ1ODQ3MjQ4YmMwYjJmZmYwOWQyOGQ2KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzcyOWQ5MTliNDJmZjRlMTA5NTdmZTU2NWI1NzMzNWVlLmJpbmRQb3B1cChwb3B1cF8xYjBjMzZjYjllNGM0NzY4OTMxOTM2NWNhMTM5MjAyZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80ODE3Mzk3YjJlMGE0NDc0OGJjM2M4YWM1MmRjZDVhMyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2Nzk2NywtNzkuMzY3Njc1M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzYzMWNjMDM1NDcwNDUwMmI4Nzc4NmUxOTIwYTI0NzcgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzkwNjE3ZTU2YjYxNDdjYzhiYjRjNzE4NDc4ZTQwZWMgPSAkKCc8ZGl2IGlkPSJodG1sXzM5MDYxN2U1NmI2MTQ3Y2M4YmI0YzcxODQ3OGU0MGVjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Eb3dudG93biBUb3JvbnRvLCBTdC4gSmFtZXMgVG93biwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMzYzMWNjMDM1NDcwNDUwMmI4Nzc4NmUxOTIwYTI0Nzcuc2V0Q29udGVudChodG1sXzM5MDYxN2U1NmI2MTQ3Y2M4YmI0YzcxODQ3OGU0MGVjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzQ4MTczOTdiMmUwYTQ0NzQ4YmMzYzhhYzUyZGNkNWEzLmJpbmRQb3B1cChwb3B1cF8zNjMxY2MwMzU0NzA0NTAyYjg3Nzg2ZTE5MjBhMjQ3Nyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yYWNhMzMwOWE2MTY0NjcyYjg2ZWIzYTFhNDNiMGIzNiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODQyOTIsLTc5LjM4MjI4MDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiY3lhbiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsYWNrIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzA3NWFkMDYxZjM2MDQ1NDNiMWI5N2I3NjFiNDA4M2RiKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzE2OTYwN2ZhYzEzYTQzZjhhOGY1NzMwOWM0MzNhM2QyID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2YyMTcxNDEwNGJiZDRkODE5ZDhlZWM2NjY4YmI0YzI2ID0gJCgnPGRpdiBpZD0iaHRtbF9mMjE3MTQxMDRiYmQ0ZDgxOWQ4ZWVjNjY2OGJiNGMyNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Rmlyc3QgQ2FuYWRpYW4gUGxhY2UsIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE2OTYwN2ZhYzEzYTQzZjhhOGY1NzMwOWM0MzNhM2QyLnNldENvbnRlbnQoaHRtbF9mMjE3MTQxMDRiYmQ0ZDgxOWQ4ZWVjNjY2OGJiNGMyNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yYWNhMzMwOWE2MTY0NjcyYjg2ZWIzYTFhNDNiMGIzNi5iaW5kUG9wdXAocG9wdXBfMTY5NjA3ZmFjMTNhNDNmOGE4ZjU3MzA5YzQzM2EzZDIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTIzZjRjMDYyMjFkNDJlNjk2YmIzMmI1OTg4ZDcxODAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDg0MjkyLC03OS4zODIyODAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImN5YW4iLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibGFjayIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8wNzVhZDA2MWYzNjA0NTQzYjFiOTdiNzYxYjQwODNkYik7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85MTNhNjY3ZjdhZTY0NDlmODg0Y2QyOGY0MGYzODk1ZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hNzVkZWNhNGFjNTI0ZTFhOWY0NDUyYjhiZDBjYWRlZiA9ICQoJzxkaXYgaWQ9Imh0bWxfYTc1ZGVjYTRhYzUyNGUxYTlmNDQ1MmI4YmQwY2FkZWYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlVuZGVyZ3JvdW5kIGNpdHksIERvd250b3duIFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkxM2E2NjdmN2FlNjQ0OWY4ODRjZDI4ZjQwZjM4OTVmLnNldENvbnRlbnQoaHRtbF9hNzVkZWNhNGFjNTI0ZTFhOWY0NDUyYjhiZDBjYWRlZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85MjNmNGMwNjIyMWQ0MmU2OTZiYjMyYjU5ODhkNzE4MC5iaW5kUG9wdXAocG9wdXBfOTEzYTY2N2Y3YWU2NDQ5Zjg4NGNkMjhmNDBmMzg5NWYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzI1MDY2MTA1Yzk3NDA2MjllZDcwNjUyNTA5Yjk4YzMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjU4NTk5LC03OS4zODMxNTk5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYmU0YmNhNTUwOTI1NGM2Yzk1ZDZmOGY3NTc0YjBlYjEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDk4YWNjMGZmZTkzNGEwYmEzNzQ1ZmNmZjNkMzE5MTMgPSAkKCc8ZGl2IGlkPSJodG1sX2Q5OGFjYzBmZmU5MzRhMGJhMzc0NWZjZmYzZDMxOTEzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DaHVyY2ggYW5kIFdlbGxlc2xleSwgRG93bnRvd24gVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYmU0YmNhNTUwOTI1NGM2Yzk1ZDZmOGY3NTc0YjBlYjEuc2V0Q29udGVudChodG1sX2Q5OGFjYzBmZmU5MzRhMGJhMzc0NWZjZmYzZDMxOTEzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzcyNTA2NjEwNWM5NzQwNjI5ZWQ3MDY1MjUwOWI5OGMzLmJpbmRQb3B1cChwb3B1cF9iZTRiY2E1NTA5MjU0YzZjOTVkNmY4Zjc1NzRiMGViMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lYmFmN2I4MmFmZmI0NTFlYmM0YmEzZTQ1OGFlYzM1MiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2Mjc0MzksLTc5LjMyMTU1OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJjeWFuIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmxhY2siLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMDc1YWQwNjFmMzYwNDU0M2IxYjk3Yjc2MWI0MDgzZGIpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmJkZjdhNmVhMjIzNDZmY2JjNDdmMzE1M2Q3NjU0N2IgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjZkMjRiNTMzZmMxNDEwYWI2NzRiMjk5YmMxOTZkNzYgPSAkKCc8ZGl2IGlkPSJodG1sXzY2ZDI0YjUzM2ZjMTQxMGFiNjc0YjI5OWJjMTk2ZDc2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CdXNpbmVzcyBSZXBseSBNYWlsIFByb2Nlc3NpbmcgQ2VudHJlIDk2OSBFYXN0ZXJuLCBFYXN0IFRvcm9udG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzZiZGY3YTZlYTIyMzQ2ZmNiYzQ3ZjMxNTNkNzY1NDdiLnNldENvbnRlbnQoaHRtbF82NmQyNGI1MzNmYzE0MTBhYjY3NGIyOTliYzE5NmQ3Nik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lYmFmN2I4MmFmZmI0NTFlYmM0YmEzZTQ1OGFlYzM1Mi5iaW5kUG9wdXAocG9wdXBfNmJkZjdhNmVhMjIzNDZmY2JjNDdmMzE1M2Q3NjU0N2IpOwoKICAgICAgICAgICAgCiAgICAgICAgCjwvc2NyaXB0Pg==" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



## Getting vanues for each Neighborhood from Foursquare


```python
CLIENT_ID = '1LBY0SOLMQWYE0H040VQ2IXISHXAHBRFFFEBHJGUZQCCOY0U' # your Foursquare ID
CLIENT_SECRET = 'KWIT54XNJHB5ANS3UPOLM0H1ILQ1P5LVLNV35BX5VXLNF22O' # your Foursquare Secret
VERSION = '20180605' # Foursquare API version
LIMIT=100
radius = 500
```


```python
def getNearbyVenues(names, latitudes, longitudes, radius=500):
    
    venues_list=[]
    for name, lat, lng in zip(names, latitudes, longitudes):
        print(name)
            
        # create the API request URL
        url = 'https://api.foursquare.com/v2/venues/explore?&client_id={}&client_secret={}&v={}&ll={},{}&radius={}&limit={}'.format(
            CLIENT_ID, 
            CLIENT_SECRET, 
            VERSION, 
            lat, 
            lng, 
            radius, 
            LIMIT)
            
        # make the GET request
        results = requests.get(url).json()["response"]['groups'][0]['items']
        
        # return only relevant information for each nearby venue
        venues_list.append([(
            name, 
            lat, 
            lng, 
            v['venue']['name'], 
            v['venue']['location']['lat'], 
            v['venue']['location']['lng'],  
            v['venue']['categories'][0]['name']) for v in results])

    nearby_venues = pd.DataFrame([item for venue_list in venues_list for item in venue_list])
    nearby_venues.columns = ['Neighborhood', 
                  'Neighborhood Latitude', 
                  'Neighborhood Longitude', 
                  'Venue', 
                  'Venue Latitude', 
                  'Venue Longitude', 
                  'Venue Category']
    
    return(nearby_venues)
```


```python
toronto_venues = getNearbyVenues(names=toronto_df['Neighborhood'],
                                   latitudes=toronto_df['Latitude'],
                                   longitudes=toronto_df['Longitude']
                                  )
print(toronto_venues.shape)
toronto_venues.head()
```

    Harbourfront
    Regent Park
    Ryerson
    Garden District
    St. James Town
    The Beaches
    Berczy Park
    Central Bay Street
    Christie
    Adelaide
    King
    Richmond
    Dovercourt Village
    Dufferin
    Harbourfront East
    Toronto Islands
    Union Station
    Little Portugal
    Trinity
    The Danforth West
    Riverdale
    Design Exchange
    Toronto Dominion Centre
    Brockton
    Exhibition Place
    Parkdale Village
    The Beaches West
    India Bazaar
    Commerce Court
    Victoria Hotel
    Studio District
    Lawrence Park
    Roselawn
    Davisville North
    Forest Hill North
    Forest Hill West
    High Park
    The Junction South
    North Toronto West
    The Annex
    North Midtown
    Yorkville
    Parkdale
    Roncesvalles
    Davisville
    Harbord
    University of Toronto
    Runnymede
    Swansea
    Moore Park
    Summerhill East
    Chinatown
    Grange Park
    Kensington Market
    Deer Park
    Forest Hill SE
    Rathnelly
    South Hill
    Summerhill West
    CN Tower
    Bathurst Quay
    Island airport
    Harbourfront West
    King and Spadina
    Railway Lands
    South Niagara
    Rosedale
    Stn A PO Boxes 25 The Esplanade
    Cabbagetown
    Downtown Toronto, St. James Town
    First Canadian Place
    Underground city
    Church and Wellesley
    Business Reply Mail Processing Centre 969 Eastern
    (3291, 7)





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Neighborhood</th>
      <th>Neighborhood Latitude</th>
      <th>Neighborhood Longitude</th>
      <th>Venue</th>
      <th>Venue Latitude</th>
      <th>Venue Longitude</th>
      <th>Venue Category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Harbourfront</td>
      <td>43.65426</td>
      <td>-79.360636</td>
      <td>Roselle Desserts</td>
      <td>43.653447</td>
      <td>-79.362017</td>
      <td>Bakery</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Harbourfront</td>
      <td>43.65426</td>
      <td>-79.360636</td>
      <td>Tandem Coffee</td>
      <td>43.653559</td>
      <td>-79.361809</td>
      <td>Coffee Shop</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Harbourfront</td>
      <td>43.65426</td>
      <td>-79.360636</td>
      <td>Toronto Cooper Koo Family Cherry St YMCA Centre</td>
      <td>43.653191</td>
      <td>-79.357947</td>
      <td>Gym / Fitness Center</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Harbourfront</td>
      <td>43.65426</td>
      <td>-79.360636</td>
      <td>Body Blitz Spa East</td>
      <td>43.654735</td>
      <td>-79.359874</td>
      <td>Spa</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Harbourfront</td>
      <td>43.65426</td>
      <td>-79.360636</td>
      <td>Morning Glory Cafe</td>
      <td>43.653947</td>
      <td>-79.361149</td>
      <td>Breakfast Spot</td>
    </tr>
  </tbody>
</table>
</div>



### Analyzing the data


```python
# one hot encoding
toronto_onehot = pd.get_dummies(toronto_venues[['Venue Category']], prefix="", prefix_sep="")

# add neighborhood column back to dataframe
toronto_onehot['Neighborhood'] = toronto_venues['Neighborhood'] 

# move neighborhood column to the first column
fixed_columns = [toronto_onehot.columns[-1]] + list(toronto_onehot.columns[:-1])
toronto_onehot = toronto_onehot[fixed_columns]

print(toronto_onehot.shape)
toronto_onehot.head()
```

    (3291, 236)





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Yoga Studio</th>
      <th>Adult Boutique</th>
      <th>Afghan Restaurant</th>
      <th>Airport</th>
      <th>Airport Food Court</th>
      <th>Airport Gate</th>
      <th>Airport Lounge</th>
      <th>Airport Service</th>
      <th>Airport Terminal</th>
      <th>American Restaurant</th>
      <th>...</th>
      <th>Theme Restaurant</th>
      <th>Thrift / Vintage Store</th>
      <th>Toy / Game Store</th>
      <th>Trail</th>
      <th>Train Station</th>
      <th>Vegetarian / Vegan Restaurant</th>
      <th>Video Game Store</th>
      <th>Vietnamese Restaurant</th>
      <th>Wine Bar</th>
      <th>Wings Joint</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 236 columns</p>
</div>




```python
toronto_grouped = toronto_onehot.groupby('Neighborhood').mean().reset_index()
toronto_grouped
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Neighborhood</th>
      <th>Yoga Studio</th>
      <th>Adult Boutique</th>
      <th>Afghan Restaurant</th>
      <th>Airport</th>
      <th>Airport Food Court</th>
      <th>Airport Gate</th>
      <th>Airport Lounge</th>
      <th>Airport Service</th>
      <th>Airport Terminal</th>
      <th>...</th>
      <th>Theme Restaurant</th>
      <th>Thrift / Vintage Store</th>
      <th>Toy / Game Store</th>
      <th>Trail</th>
      <th>Train Station</th>
      <th>Vegetarian / Vegan Restaurant</th>
      <th>Video Game Store</th>
      <th>Vietnamese Restaurant</th>
      <th>Wine Bar</th>
      <th>Wings Joint</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Adelaide</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bathurst Quay</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.066667</td>
      <td>0.066667</td>
      <td>0.066667</td>
      <td>0.133333</td>
      <td>0.2</td>
      <td>0.133333</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Berczy Park</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.017544</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Brockton</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Business Reply Mail Processing Centre 969 Eastern</td>
      <td>0.058824</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>CN Tower</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.066667</td>
      <td>0.066667</td>
      <td>0.066667</td>
      <td>0.133333</td>
      <td>0.2</td>
      <td>0.133333</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabbagetown</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Central Bay Street</td>
      <td>0.011364</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.011364</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.011364</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Chinatown</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.01</td>
      <td>0.01000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.060000</td>
      <td>0.000000</td>
      <td>0.030000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Christie</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Church and Wellesley</td>
      <td>0.011364</td>
      <td>0.011364</td>
      <td>0.011364</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.011364</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.011364</td>
      <td>0.011364</td>
      <td>0.000000</td>
      <td>0.011364</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Commerce Court</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Davisville</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.03125</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Davisville North</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Deer Park</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.071429</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Design Exchange</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.01</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Dovercourt Village</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Downtown Toronto, St. James Town</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Dufferin</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Exhibition Place</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>20</th>
      <td>First Canadian Place</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.01</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Forest Hill North</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.250000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Forest Hill SE</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.071429</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Forest Hill West</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.250000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Garden District</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.01000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.010000</td>
      <td>0.010000</td>
      <td>0.010000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Grange Park</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.01</td>
      <td>0.01000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.060000</td>
      <td>0.000000</td>
      <td>0.030000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Harbord</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.029412</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Harbourfront</td>
      <td>0.021739</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Harbourfront East</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.01</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Harbourfront West</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.066667</td>
      <td>0.066667</td>
      <td>0.066667</td>
      <td>0.133333</td>
      <td>0.2</td>
      <td>0.133333</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Rathnelly</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.071429</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Regent Park</td>
      <td>0.021739</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Richmond</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Riverdale</td>
      <td>0.022727</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.022727</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Roncesvalles</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>49</th>
      <td>Rosedale</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.250000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50</th>
      <td>Roselawn</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>51</th>
      <td>Runnymede</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.025000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>52</th>
      <td>Ryerson</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.01000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.010000</td>
      <td>0.010000</td>
      <td>0.010000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>53</th>
      <td>South Hill</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.071429</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>54</th>
      <td>South Niagara</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.066667</td>
      <td>0.066667</td>
      <td>0.066667</td>
      <td>0.133333</td>
      <td>0.2</td>
      <td>0.133333</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>55</th>
      <td>St. James Town</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>56</th>
      <td>Stn A PO Boxes 25 The Esplanade</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.010526</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>57</th>
      <td>Studio District</td>
      <td>0.027027</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>58</th>
      <td>Summerhill East</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.333333</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>59</th>
      <td>Summerhill West</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.071429</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>60</th>
      <td>Swansea</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.025000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>61</th>
      <td>The Annex</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.041667</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>62</th>
      <td>The Beaches</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>63</th>
      <td>The Beaches West</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>64</th>
      <td>The Danforth West</td>
      <td>0.022727</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.022727</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>65</th>
      <td>The Junction South</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>66</th>
      <td>Toronto Dominion Centre</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.01</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>67</th>
      <td>Toronto Islands</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.01</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>68</th>
      <td>Trinity</td>
      <td>0.015873</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.015873</td>
      <td>0.000000</td>
      <td>0.031746</td>
      <td>0.015873</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>69</th>
      <td>Underground city</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.01</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Union Station</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.01</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>71</th>
      <td>University of Toronto</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.029412</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>72</th>
      <td>Victoria Hotel</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>73</th>
      <td>Yorkville</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.00000</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.041667</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
<p>74 rows × 236 columns</p>
</div>




```python
def return_most_common_venues(row, num_top_venues):
    row_categories = row.iloc[1:]
    row_categories_sorted = row_categories.sort_values(ascending=False)
    
    return row_categories_sorted.index.values[0:num_top_venues]
```


```python
num_top_venues = 5

indicators = ['st', 'nd', 'rd']

# create columns according to number of top venues
columns = ['Neighborhood']
for ind in np.arange(num_top_venues):
    try:
        columns.append('{}{} Most Common Venue'.format(ind+1, indicators[ind]))
    except:
        columns.append('{}th Most Common Venue'.format(ind+1))

# create a new dataframe
neighborhoods_venues_sorted = pd.DataFrame(columns=columns)
neighborhoods_venues_sorted['Neighborhood'] = toronto_grouped['Neighborhood']

for ind in np.arange(toronto_grouped.shape[0]):
    neighborhoods_venues_sorted.iloc[ind, 1:] = return_most_common_venues(toronto_grouped.iloc[ind, :], num_top_venues)

neighborhoods_venues_sorted.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Neighborhood</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Adelaide</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Thai Restaurant</td>
      <td>Steakhouse</td>
      <td>American Restaurant</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bathurst Quay</td>
      <td>Airport Service</td>
      <td>Airport Lounge</td>
      <td>Airport Terminal</td>
      <td>Harbor / Marina</td>
      <td>Boat or Ferry</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Berczy Park</td>
      <td>Coffee Shop</td>
      <td>Cocktail Bar</td>
      <td>Café</td>
      <td>Cheese Shop</td>
      <td>Beer Bar</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Brockton</td>
      <td>Café</td>
      <td>Breakfast Spot</td>
      <td>Coffee Shop</td>
      <td>Grocery Store</td>
      <td>Italian Restaurant</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Business Reply Mail Processing Centre 969 Eastern</td>
      <td>Light Rail Station</td>
      <td>Garden</td>
      <td>Recording Studio</td>
      <td>Auto Workshop</td>
      <td>Skate Park</td>
    </tr>
  </tbody>
</table>
</div>




```python
from sklearn.cluster import KMeans
# set number of clusters
kclusters = 13

toronto_grouped_clustering = toronto_grouped.drop('Neighborhood', 1)

# run k-means clustering
kmeans = KMeans(n_clusters=kclusters, random_state=4).fit(toronto_grouped_clustering)

# check cluster labels generated for each row in the dataframe
kmeans.labels_[0:10] 
```




    array([ 0,  2,  0, 10,  5,  2,  0,  0, 11, 10], dtype=int32)




```python
# add clustering labels
neighborhoods_venues_sorted.insert(0, 'Cluster Labels', kmeans.labels_)

toronto_merged = toronto_df

# merge toronto_grouped with toronto_data to add latitude/longitude for each neighborhood
toronto_merged = toronto_merged.join(neighborhoods_venues_sorted.set_index('Neighborhood'), on='Neighborhood')

toronto_merged.head() # check the last columns!
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PostCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront</td>
      <td>43.654260</td>
      <td>-79.360636</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Park</td>
      <td>Café</td>
      <td>Pub</td>
      <td>Bakery</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park</td>
      <td>43.654260</td>
      <td>-79.360636</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Park</td>
      <td>Café</td>
      <td>Pub</td>
      <td>Bakery</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Ryerson</td>
      <td>43.657162</td>
      <td>-79.378937</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Clothing Store</td>
      <td>Café</td>
      <td>Cosmetics Shop</td>
      <td>Middle Eastern Restaurant</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District</td>
      <td>43.657162</td>
      <td>-79.378937</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Clothing Store</td>
      <td>Café</td>
      <td>Cosmetics Shop</td>
      <td>Middle Eastern Restaurant</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M5C</td>
      <td>Downtown Toronto</td>
      <td>St. James Town</td>
      <td>43.651494</td>
      <td>-79.375418</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Hotel</td>
      <td>Café</td>
      <td>Restaurant</td>
      <td>Cosmetics Shop</td>
    </tr>
  </tbody>
</table>
</div>



### Visualizing the resulting clusters


```python
# create map
map_clusters = folium.Map(location=[latitude, longitude], zoom_start=13)

# set color scheme for the clusters
x = np.arange(kclusters)
ys = [i + x + (i*x)**2 for i in range(kclusters)]
colors_array = cm.rainbow(np.linspace(0, 1, len(ys)))
rainbow = [colors.rgb2hex(i) for i in colors_array]

# add markers to the map
markers_colors = []
for lat, lon, poi, cluster in zip(toronto_merged['Latitude'], toronto_merged['Longitude'], toronto_merged['Neighborhood'], toronto_merged['Cluster Labels']):
    label = folium.Popup(str(poi) + ' Cluster ' + str(cluster), parse_html=True)
    folium.CircleMarker(
        [lat, lon],
        radius=5,
        popup=label,
        color='black',
        fill=True,
        fill_color=rainbow[cluster-1],
        fill_opacity=0.7).add_to(map_clusters)
       
map_clusters
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;base64,PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjIiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3YyA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3YycsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDMuNjUzOTYzLC03OS4zODcyMDddLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgem9vbTogMTMsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBtYXhCb3VuZHM6IGJvdW5kcywKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGxheWVyczogW10sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB3b3JsZENvcHlKdW1wOiBmYWxzZSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGNyczogTC5DUlMuRVBTRzM4NTcKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciB0aWxlX2xheWVyXzUwOGQ4MjY4MjczZTQ0MzhhNWFlY2MwYzc3YTQwNmMwID0gTC50aWxlTGF5ZXIoCiAgICAgICAgICAgICAgICAnaHR0cHM6Ly97c30udGlsZS5vcGVuc3RyZWV0bWFwLm9yZy97en0ve3h9L3t5fS5wbmcnLAogICAgICAgICAgICAgICAgewogICJhdHRyaWJ1dGlvbiI6IG51bGwsCiAgImRldGVjdFJldGluYSI6IGZhbHNlLAogICJtYXhab29tIjogMTgsCiAgIm1pblpvb20iOiAxLAogICJub1dyYXAiOiBmYWxzZSwKICAic3ViZG9tYWlucyI6ICJhYmMiCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84Nzk2ZmU3MWE2MTA0MWQ5YTVhMzZkYTViN2U1ODA0YiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1NDI1OTksLTc5LjM2MDYzNTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzc5NzQ0ODBiZTczOTQzNGZhZmI3ZWRhZDkwNDQ2YzUxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzFiYTE3MDE0NWQ4MzQ5MTI5N2ViMTRmZDI5NmIyOGMxID0gJCgnPGRpdiBpZD0iaHRtbF8xYmExNzAxNDVkODM0OTEyOTdlYjE0ZmQyOTZiMjhjMSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SGFyYm91cmZyb250IENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzk3NDQ4MGJlNzM5NDM0ZmFmYjdlZGFkOTA0NDZjNTEuc2V0Q29udGVudChodG1sXzFiYTE3MDE0NWQ4MzQ5MTI5N2ViMTRmZDI5NmIyOGMxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzg3OTZmZTcxYTYxMDQxZDlhNWEzNmRhNWI3ZTU4MDRiLmJpbmRQb3B1cChwb3B1cF83OTc0NDgwYmU3Mzk0MzRmYWZiN2VkYWQ5MDQ0NmM1MSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84MWQ5MTk4NGNmNTI0M2ZmOTAyZWU4NzM5OTVlMzlhZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1NDI1OTksLTc5LjM2MDYzNTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzI2MmRmZDdhMzMzZTQzN2RiNTE5ZDAzNWMzMjhkOTY0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzE5MzMzN2FlZTI2MTRjNDE5YTVmMmI0ZWM0ZjFjMjQ1ID0gJCgnPGRpdiBpZD0iaHRtbF8xOTMzMzdhZWUyNjE0YzQxOWE1ZjJiNGVjNGYxYzI0NSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UmVnZW50IFBhcmsgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yNjJkZmQ3YTMzM2U0MzdkYjUxOWQwMzVjMzI4ZDk2NC5zZXRDb250ZW50KGh0bWxfMTkzMzM3YWVlMjYxNGM0MTlhNWYyYjRlYzRmMWMyNDUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfODFkOTE5ODRjZjUyNDNmZjkwMmVlODczOTk1ZTM5YWYuYmluZFBvcHVwKHBvcHVwXzI2MmRmZDdhMzMzZTQzN2RiNTE5ZDAzNWMzMjhkOTY0KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzE5OWMyY2FiMmY5ZjQxNGFhMTVjM2ZmZGJjMGUzZDM5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjU3MTYxOCwtNzkuMzc4OTM3MDk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzY4ZTNmYjllNzZlYzQyMjQ4YzFkMWE5OWVkZjQ0MmY5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzZhMWU4Yjc4MThkNjQ3OTA4MTBjNWFkMTVkZGE5NjA0ID0gJCgnPGRpdiBpZD0iaHRtbF82YTFlOGI3ODE4ZDY0NzkwODEwYzVhZDE1ZGRhOTYwNCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UnllcnNvbiBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzY4ZTNmYjllNzZlYzQyMjQ4YzFkMWE5OWVkZjQ0MmY5LnNldENvbnRlbnQoaHRtbF82YTFlOGI3ODE4ZDY0NzkwODEwYzVhZDE1ZGRhOTYwNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xOTljMmNhYjJmOWY0MTRhYTE1YzNmZmRiYzBlM2QzOS5iaW5kUG9wdXAocG9wdXBfNjhlM2ZiOWU3NmVjNDIyNDhjMWQxYTk5ZWRmNDQyZjkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzk0ZWY0YzUzZmI0NGNmYWE3ZGZkM2RhYzNhZmEzZGEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTcxNjE4LC03OS4zNzg5MzcwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMjI4YTY0OTY1YzIxNDVmMmE3NDNlNzU3MTMyNzRmY2YgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODc5ZjRkMWVlYmM0NGFkOTk3NWFjOWY4MjdkZjY0Y2IgPSAkKCc8ZGl2IGlkPSJodG1sXzg3OWY0ZDFlZWJjNDRhZDk5NzVhYzlmODI3ZGY2NGNiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5HYXJkZW4gRGlzdHJpY3QgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yMjhhNjQ5NjVjMjE0NWYyYTc0M2U3NTcxMzI3NGZjZi5zZXRDb250ZW50KGh0bWxfODc5ZjRkMWVlYmM0NGFkOTk3NWFjOWY4MjdkZjY0Y2IpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMzk0ZWY0YzUzZmI0NGNmYWE3ZGZkM2RhYzNhZmEzZGEuYmluZFBvcHVwKHBvcHVwXzIyOGE2NDk2NWMyMTQ1ZjJhNzQzZTc1NzEzMjc0ZmNmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzRjZDM5OWFhMmFhNjQzNjU4OTg3ZTgyZjlkMTkzZDM3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUxNDkzOSwtNzkuMzc1NDE3OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZjBkNDEyYjdmYmU2NGY4MzllY2ZkNGE3MjA1MDZlOGEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYzYzM2IwMmRiNmFlNGYzZTljODA5MDNkZTM2ZmRhMGYgPSAkKCc8ZGl2IGlkPSJodG1sX2M2MzNiMDJkYjZhZTRmM2U5YzgwOTAzZGUzNmZkYTBmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdC4gSmFtZXMgVG93biBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2YwZDQxMmI3ZmJlNjRmODM5ZWNmZDRhNzIwNTA2ZThhLnNldENvbnRlbnQoaHRtbF9jNjMzYjAyZGI2YWU0ZjNlOWM4MDkwM2RlMzZmZGEwZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80Y2QzOTlhYTJhYTY0MzY1ODk4N2U4MmY5ZDE5M2QzNy5iaW5kUG9wdXAocG9wdXBfZjBkNDEyYjdmYmU2NGY4MzllY2ZkNGE3MjA1MDZlOGEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzIzMjNjMDA4NmE4NDA3Y2JjNWZmNzUzOTVjMDhiMGIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NzYzNTczOTk5OTk5OSwtNzkuMjkzMDMxMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM1NGY2Y2IiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzhmMTUxNTgzMzQzNGFkNmJmMmMwZWJiNDNkYzgzN2EgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNTA3MDVkZjg3ODRmNGE4ZGI3NjQ3YmI4ZGQ4ODRjOGEgPSAkKCc8ZGl2IGlkPSJodG1sXzUwNzA1ZGY4Nzg0ZjRhOGRiNzY0N2JiOGRkODg0YzhhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgQmVhY2hlcyBDbHVzdGVyIDY8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2M4ZjE1MTU4MzM0MzRhZDZiZjJjMGViYjQzZGM4MzdhLnNldENvbnRlbnQoaHRtbF81MDcwNWRmODc4NGY0YThkYjc2NDdiYjhkZDg4NGM4YSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83MjMyM2MwMDg2YTg0MDdjYmM1ZmY3NTM5NWMwOGIwYi5iaW5kUG9wdXAocG9wdXBfYzhmMTUxNTgzMzQzNGFkNmJmMmMwZWJiNDNkYzgzN2EpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDNmNzY1ZTc2ZGU4NDRlNWI0NjhiZDEyNjA4N2M0NTIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDQ3NzA3OTk5OTk5OTYsLTc5LjM3MzMwNjRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzc4ODIxYWRjZTlmNTQ2NjE4OGQwZWMwMmExYWYyNjcxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzE0ZjE3Mzc2M2MxZjRlM2U5NGM5NmVkMmE2M2JhN2YyID0gJCgnPGRpdiBpZD0iaHRtbF8xNGYxNzM3NjNjMWY0ZTNlOTRjOTZlZDJhNjNiYTdmMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QmVyY3p5IFBhcmsgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83ODgyMWFkY2U5ZjU0NjYxODhkMGVjMDJhMWFmMjY3MS5zZXRDb250ZW50KGh0bWxfMTRmMTczNzYzYzFmNGUzZTk0Yzk2ZWQyYTYzYmE3ZjIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDNmNzY1ZTc2ZGU4NDRlNWI0NjhiZDEyNjA4N2M0NTIuYmluZFBvcHVwKHBvcHVwXzc4ODIxYWRjZTlmNTQ2NjE4OGQwZWMwMmExYWYyNjcxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU5MDA1Mzg0Y2ExNTQwNDE4NjMyODU3OTk5YWM1YWU0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjU3OTUyNCwtNzkuMzg3MzgyNl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTVkOTEwMjkxZDMwNDI5ZGEzNDcyNWJmY2EyOGMwOGIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYzJlYWMwZDNjYzY2NDM0NTkxMTdkM2FmZDdhZDAxYzggPSAkKCc8ZGl2IGlkPSJodG1sX2MyZWFjMGQzY2M2NjQzNDU5MTE3ZDNhZmQ3YWQwMWM4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DZW50cmFsIEJheSBTdHJlZXQgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xNWQ5MTAyOTFkMzA0MjlkYTM0NzI1YmZjYTI4YzA4Yi5zZXRDb250ZW50KGh0bWxfYzJlYWMwZDNjYzY2NDM0NTkxMTdkM2FmZDdhZDAxYzgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNTkwMDUzODRjYTE1NDA0MTg2MzI4NTc5OTlhYzVhZTQuYmluZFBvcHVwKHBvcHVwXzE1ZDkxMDI5MWQzMDQyOWRhMzQ3MjViZmNhMjhjMDhiKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2JkNTNmMjk0ZGUwNjRmM2E5MTU2ZjQ0YjUyMzkzM2ZkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY5NTQyLC03OS40MjI1NjM3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xYzkzMWUwZDQxMzM0MzM3YTJmMzM4MzkwMjU4NWM5NSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84NjBhMTNhNmI0NjE0NDg3OWY3N2VkZTlmMGE2MDA1MyA9ICQoJzxkaXYgaWQ9Imh0bWxfODYwYTEzYTZiNDYxNDQ4NzlmNzdlZGU5ZjBhNjAwNTMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNocmlzdGllIENsdXN0ZXIgMTA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzFjOTMxZTBkNDEzMzQzMzdhMmYzMzgzOTAyNTg1Yzk1LnNldENvbnRlbnQoaHRtbF84NjBhMTNhNmI0NjE0NDg3OWY3N2VkZTlmMGE2MDA1Myk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iZDUzZjI5NGRlMDY0ZjNhOTE1NmY0NGI1MjM5MzNmZC5iaW5kUG9wdXAocG9wdXBfMWM5MzFlMGQ0MTMzNDMzN2EyZjMzODM5MDI1ODVjOTUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzVmZTMwZjhjNjRjNGMzYzlmMTQ3ZjRlMDhjY2NkYTAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTA1NzEyMDAwMDAwMSwtNzkuMzg0NTY3NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDUyM2ZmZDAzODg1NDdkNTkwZTMzYmY3ZDFhZDUyZmIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTVkNmFjNTAzOTVhNGM4Y2EyOTM5NGU3MGRmODlkZWMgPSAkKCc8ZGl2IGlkPSJodG1sX2E1ZDZhYzUwMzk1YTRjOGNhMjkzOTRlNzBkZjg5ZGVjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BZGVsYWlkZSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQ1MjNmZmQwMzg4NTQ3ZDU5MGUzM2JmN2QxYWQ1MmZiLnNldENvbnRlbnQoaHRtbF9hNWQ2YWM1MDM5NWE0YzhjYTI5Mzk0ZTcwZGY4OWRlYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jNWZlMzBmOGM2NGM0YzNjOWYxNDdmNGUwOGNjY2RhMC5iaW5kUG9wdXAocG9wdXBfNDUyM2ZmZDAzODg1NDdkNTkwZTMzYmY3ZDFhZDUyZmIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZTkxMGUzZjVmYzA1NDBkMTljNjhmOGU5M2U5MTE2NGMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTA1NzEyMDAwMDAwMSwtNzkuMzg0NTY3NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzA3NGQyYThkMGIzNGE0MGExYjdjOWFhOTcwMzRkYjQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTMwMjA3YjQ1OTY3NGE0NmI1OGE0MjE4ODJkM2RmMjggPSAkKCc8ZGl2IGlkPSJodG1sXzEzMDIwN2I0NTk2NzRhNDZiNThhNDIxODgyZDNkZjI4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5LaW5nIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzA3NGQyYThkMGIzNGE0MGExYjdjOWFhOTcwMzRkYjQuc2V0Q29udGVudChodG1sXzEzMDIwN2I0NTk2NzRhNDZiNThhNDIxODgyZDNkZjI4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2U5MTBlM2Y1ZmMwNTQwZDE5YzY4ZjhlOTNlOTExNjRjLmJpbmRQb3B1cChwb3B1cF9jMDc0ZDJhOGQwYjM0YTQwYTFiN2M5YWE5NzAzNGRiNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85ZDgwNzIzZWQwYjM0ZjFhODIyMGUyNTU1OWM4N2E0MyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MDU3MTIwMDAwMDAxLC03OS4zODQ1Njc1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kNWJjYWUyNDBjNTU0YzM1YTI4Y2Q4MDhlZTM2ZGFhYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xOGZmMDZkM2UxMWY0ZTMyOTNhOTNhYjFkOWI4NWQzMiA9ICQoJzxkaXYgaWQ9Imh0bWxfMThmZjA2ZDNlMTFmNGUzMjkzYTkzYWIxZDliODVkMzIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJpY2htb25kIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDViY2FlMjQwYzU1NGMzNWEyOGNkODA4ZWUzNmRhYWMuc2V0Q29udGVudChodG1sXzE4ZmYwNmQzZTExZjRlMzI5M2E5M2FiMWQ5Yjg1ZDMyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzlkODA3MjNlZDBiMzRmMWE4MjIwZTI1NTU5Yzg3YTQzLmJpbmRQb3B1cChwb3B1cF9kNWJjYWUyNDBjNTU0YzM1YTI4Y2Q4MDhlZTM2ZGFhYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jYWYyNDg5Mzg1YzA0ZjUzOWYxYThlY2YzZWY2ZTYyMCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2OTAwNTEwMDAwMDAxLC03OS40NDIyNTkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmN2U0MSIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83MDc3MjE5MGQyMjU0MjdmYmQ2ZDdkODg0NWI4OTRiNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iMWNhY2I0ODZkNDc0OWU2YmMyZDVhODU1OTE1NmY2NCA9ICQoJzxkaXYgaWQ9Imh0bWxfYjFjYWNiNDg2ZDQ3NDllNmJjMmQ1YTg1NTkxNTZmNjQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRvdmVyY291cnQgVmlsbGFnZSBDbHVzdGVyIDExPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83MDc3MjE5MGQyMjU0MjdmYmQ2ZDdkODg0NWI4OTRiNS5zZXRDb250ZW50KGh0bWxfYjFjYWNiNDg2ZDQ3NDllNmJjMmQ1YTg1NTkxNTZmNjQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfY2FmMjQ4OTM4NWMwNGY1MzlmMWE4ZWNmM2VmNmU2MjAuYmluZFBvcHVwKHBvcHVwXzcwNzcyMTkwZDIyNTQyN2ZiZDZkN2Q4ODQ1Yjg5NGI1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzdkNzZiYzhjYTZiMzRkM2I4ZjYwMWQzOWY5MDQ4ODNkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY5MDA1MTAwMDAwMDEsLTc5LjQ0MjI1OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmY3ZTQxIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzI1ZDBjZWFkYmJhMDRiOWY5MzIxZGM2ODZlNGNiMjhiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2YyYzVjOThlY2RlZTRmZDI5NTFkNTZlNDkyMDUxZGRiID0gJCgnPGRpdiBpZD0iaHRtbF9mMmM1Yzk4ZWNkZWU0ZmQyOTUxZDU2ZTQ5MjA1MWRkYiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RHVmZmVyaW4gQ2x1c3RlciAxMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjVkMGNlYWRiYmEwNGI5ZjkzMjFkYzY4NmU0Y2IyOGIuc2V0Q29udGVudChodG1sX2YyYzVjOThlY2RlZTRmZDI5NTFkNTZlNDkyMDUxZGRiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzdkNzZiYzhjYTZiMzRkM2I4ZjYwMWQzOWY5MDQ4ODNkLmJpbmRQb3B1cChwb3B1cF8yNWQwY2VhZGJiYTA0YjlmOTMyMWRjNjg2ZTRjYjI4Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mNGM4MzNkMjAyZDY0MDQ3YjEyZWRiNmRmM2NiYmIyNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0MDgxNTcsLTc5LjM4MTc1MjI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83NWFmYjM3ZjIzYWI0ODhiYmViYjdlZDQ4MzE5ZTU5YSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lYWEzMzY4NWU1MDY0ZDJhYWVlYmU0NjBlNjVlZTNhOSA9ICQoJzxkaXYgaWQ9Imh0bWxfZWFhMzM2ODVlNTA2NGQyYWFlZWJlNDYwZTY1ZWUzYTkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhhcmJvdXJmcm9udCBFYXN0IENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzVhZmIzN2YyM2FiNDg4YmJlYmI3ZWQ0ODMxOWU1OWEuc2V0Q29udGVudChodG1sX2VhYTMzNjg1ZTUwNjRkMmFhZWViZTQ2MGU2NWVlM2E5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Y0YzgzM2QyMDJkNjQwNDdiMTJlZGI2ZGYzY2JiYjI1LmJpbmRQb3B1cChwb3B1cF83NWFmYjM3ZjIzYWI0ODhiYmViYjdlZDQ4MzE5ZTU5YSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84OWIzYWQwYTg0ZmU0NzQ5YTA0ZWVlNWEwYmQxYTU5ZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0MDgxNTcsLTc5LjM4MTc1MjI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85MjViZmIwODNhMzc0MTBkOTBhMjdjYTU5ODE1Mjc2NSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80MDY3NGQ4MTI0OTM0YWU3YTA2YTI3NzljODNiYThkNSA9ICQoJzxkaXYgaWQ9Imh0bWxfNDA2NzRkODEyNDkzNGFlN2EwNmEyNzc5YzgzYmE4ZDUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRvcm9udG8gSXNsYW5kcyBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkyNWJmYjA4M2EzNzQxMGQ5MGEyN2NhNTk4MTUyNzY1LnNldENvbnRlbnQoaHRtbF80MDY3NGQ4MTI0OTM0YWU3YTA2YTI3NzljODNiYThkNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84OWIzYWQwYTg0ZmU0NzQ5YTA0ZWVlNWEwYmQxYTU5ZC5iaW5kUG9wdXAocG9wdXBfOTI1YmZiMDgzYTM3NDEwZDkwYTI3Y2E1OTgxNTI3NjUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNDk2Nzg3NDAwMWYzNDZkZTgxMjZhODNjYWQyN2VmMTQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDA4MTU3LC03OS4zODE3NTIyOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMjlmNTc3MTZjMTEyNDJmOGE3YjE5MDI0NzQ4YThlZjkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWVhYjBhMWIwZmQ4NGVhNDllM2JmNWVkMGQyM2QwNzMgPSAkKCc8ZGl2IGlkPSJodG1sXzVlYWIwYTFiMGZkODRlYTQ5ZTNiZjVlZDBkMjNkMDczIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5VbmlvbiBTdGF0aW9uIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjlmNTc3MTZjMTEyNDJmOGE3YjE5MDI0NzQ4YThlZjkuc2V0Q29udGVudChodG1sXzVlYWIwYTFiMGZkODRlYTQ5ZTNiZjVlZDBkMjNkMDczKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzQ5Njc4NzQwMDFmMzQ2ZGU4MTI2YTgzY2FkMjdlZjE0LmJpbmRQb3B1cChwb3B1cF8yOWY1NzcxNmMxMTI0MmY4YTdiMTkwMjQ3NDhhOGVmOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jMjRkZmY3YmRhZWM0YmIzOWM5ZGUwNmJhODRiZDVlYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NzkyNjcwMDAwMDAwNiwtNzkuNDE5NzQ5N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjdlNDEiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmJjNTJmMjY4ZjFjNGRkMzg0NDAzYjZkYjY3ZWY4OTkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOTNhNjFmNmFkOWY2NGFkMGIwZWY1YTdiNGYwNjcyZjYgPSAkKCc8ZGl2IGlkPSJodG1sXzkzYTYxZjZhZDlmNjRhZDBiMGVmNWE3YjRmMDY3MmY2IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MaXR0bGUgUG9ydHVnYWwgQ2x1c3RlciAxMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNmJjNTJmMjY4ZjFjNGRkMzg0NDAzYjZkYjY3ZWY4OTkuc2V0Q29udGVudChodG1sXzkzYTYxZjZhZDlmNjRhZDBiMGVmNWE3YjRmMDY3MmY2KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2MyNGRmZjdiZGFlYzRiYjM5YzlkZTA2YmE4NGJkNWVjLmJpbmRQb3B1cChwb3B1cF82YmM1MmYyNjhmMWM0ZGQzODQ0MDNiNmRiNjdlZjg5OSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83NGJjZWM2ODFlNzU0MmViYmExOTAyN2EwZTY2ZjgyMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NzkyNjcwMDAwMDAwNiwtNzkuNDE5NzQ5N10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjdlNDEiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfY2NhZmRjNjEzMWNhNDQyZWI5N2JkNjU1MGExMWVhNWEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzdmZWYwMzhiODRmNGFkMjk5ZGI1Y2M2ZmVmY2E1ZjQgPSAkKCc8ZGl2IGlkPSJodG1sXzM3ZmVmMDM4Yjg0ZjRhZDI5OWRiNWNjNmZlZmNhNWY0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UcmluaXR5IENsdXN0ZXIgMTE8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2NjYWZkYzYxMzFjYTQ0MmViOTdiZDY1NTBhMTFlYTVhLnNldENvbnRlbnQoaHRtbF8zN2ZlZjAzOGI4NGY0YWQyOTlkYjVjYzZmZWZjYTVmNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83NGJjZWM2ODFlNzU0MmViYmExOTAyN2EwZTY2ZjgyMS5iaW5kUG9wdXAocG9wdXBfY2NhZmRjNjEzMWNhNDQyZWI5N2JkNjU1MGExMWVhNWEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNWE5MDc2NmVmYzU2NGNiYjlmOTk5YTE1NWRjMGY3MDQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Nzk1NTcxLC03OS4zNTIxODhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzZmMWNkNjZjNDIyNTRhNDhhZTA4OWVkYzY5NmFjZmE5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2IzYWM5MzcxNDljNzQ2ZmQ5ODIzMDRkYjQ2ZDYyN2Q4ID0gJCgnPGRpdiBpZD0iaHRtbF9iM2FjOTM3MTQ5Yzc0NmZkOTgyMzA0ZGI0NmQ2MjdkOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VGhlIERhbmZvcnRoIFdlc3QgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82ZjFjZDY2YzQyMjU0YTQ4YWUwODllZGM2OTZhY2ZhOS5zZXRDb250ZW50KGh0bWxfYjNhYzkzNzE0OWM3NDZmZDk4MjMwNGRiNDZkNjI3ZDgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNWE5MDc2NmVmYzU2NGNiYjlmOTk5YTE1NWRjMGY3MDQuYmluZFBvcHVwKHBvcHVwXzZmMWNkNjZjNDIyNTRhNDhhZTA4OWVkYzY5NmFjZmE5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzE0OTJhMjk0N2NmNTRhNzNiYjkxYzAwODI2YzRhYmRhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjc5NTU3MSwtNzkuMzUyMTg4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hM2MzOWFhMmIxOGI0ZjczODAzNTk3Mjc3YTQzMGNhMSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mMWQxYjdlM2Q1ZTc0OWZiYmRkMDNlYzIzOTEyM2U4YiA9ICQoJzxkaXYgaWQ9Imh0bWxfZjFkMWI3ZTNkNWU3NDlmYmJkZDAzZWMyMzkxMjNlOGIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJpdmVyZGFsZSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2EzYzM5YWEyYjE4YjRmNzM4MDM1OTcyNzdhNDMwY2ExLnNldENvbnRlbnQoaHRtbF9mMWQxYjdlM2Q1ZTc0OWZiYmRkMDNlYzIzOTEyM2U4Yik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xNDkyYTI5NDdjZjU0YTczYmI5MWMwMDgyNmM0YWJkYS5iaW5kUG9wdXAocG9wdXBfYTNjMzlhYTJiMThiNGY3MzgwMzU5NzI3N2E0MzBjYTEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZGVmMTc4MjhlNWQxNGZmZGEyNDA1NDU5MDg0MmFhNTAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDcxNzY4LC03OS4zODE1NzY0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNGFjNTg4Mjg5NTZkNGFhMmFhODg3NjRmNTgwMDEzZTEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYWI3YTI4ZmQzMzE5NDFkMzk4NTQ0MTcyMmVhNWVlM2QgPSAkKCc8ZGl2IGlkPSJodG1sX2FiN2EyOGZkMzMxOTQxZDM5ODU0NDE3MjJlYTVlZTNkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5EZXNpZ24gRXhjaGFuZ2UgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80YWM1ODgyODk1NmQ0YWEyYWE4ODc2NGY1ODAwMTNlMS5zZXRDb250ZW50KGh0bWxfYWI3YTI4ZmQzMzE5NDFkMzk4NTQ0MTcyMmVhNWVlM2QpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZGVmMTc4MjhlNWQxNGZmZGEyNDA1NDU5MDg0MmFhNTAuYmluZFBvcHVwKHBvcHVwXzRhYzU4ODI4OTU2ZDRhYTJhYTg4NzY0ZjU4MDAxM2UxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzdhY2FlNDRlZDA0YTQwYTNhYThiYjZkZThlMmZmZTM1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ3MTc2OCwtNzkuMzgxNTc2NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2NiMGMzMzM0MTFkZjQ1OTY5YmI2MDBiMDg3OTkyNGE4ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2ZiYzMyYzcxYzZlMDQ0YjA4ZDJmZTgwNDdjZjYxZjg1ID0gJCgnPGRpdiBpZD0iaHRtbF9mYmMzMmM3MWM2ZTA0NGIwOGQyZmU4MDQ3Y2Y2MWY4NSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VG9yb250byBEb21pbmlvbiBDZW50cmUgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jYjBjMzMzNDExZGY0NTk2OWJiNjAwYjA4Nzk5MjRhOC5zZXRDb250ZW50KGh0bWxfZmJjMzJjNzFjNmUwNDRiMDhkMmZlODA0N2NmNjFmODUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfN2FjYWU0NGVkMDRhNDBhM2FhOGJiNmRlOGUyZmZlMzUuYmluZFBvcHVwKHBvcHVwX2NiMGMzMzM0MTFkZjQ1OTY5YmI2MDBiMDg3OTkyNGE4KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2RjMGM3ODUwZDZmNjQyZjk5NjU3YjE4ZTgxYTA1MjkwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjM2ODQ3MiwtNzkuNDI4MTkxNDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2U2MTVhMWRmNDBjNzQxNmE5ZDQyNWI3YzNjNDBhYWIzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzgxZDgzZTQxYmVlNjRjZjNhM2NmNDc2ODUzN2JlZjFjID0gJCgnPGRpdiBpZD0iaHRtbF84MWQ4M2U0MWJlZTY0Y2YzYTNjZjQ3Njg1MzdiZWYxYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QnJvY2t0b24gQ2x1c3RlciAxMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTYxNWExZGY0MGM3NDE2YTlkNDI1YjdjM2M0MGFhYjMuc2V0Q29udGVudChodG1sXzgxZDgzZTQxYmVlNjRjZjNhM2NmNDc2ODUzN2JlZjFjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2RjMGM3ODUwZDZmNjQyZjk5NjU3YjE4ZTgxYTA1MjkwLmJpbmRQb3B1cChwb3B1cF9lNjE1YTFkZjQwYzc0MTZhOWQ0MjViN2MzYzQwYWFiMyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84ODFlMGJkZWRjOGE0Y2FmYjVmN2UwZWFjN2RjZDAxNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYzNjg0NzIsLTc5LjQyODE5MTQwMDAwMDAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82NGFjYzdiMTVkOGU0ZmYxOTI3Y2I1ZGU2YjIzMGRmYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85NTIyY2E3MTYyZjI0ZDMyOTgxMzBjOWMxYmY5NTFmOSA9ICQoJzxkaXYgaWQ9Imh0bWxfOTUyMmNhNzE2MmYyNGQzMjk4MTMwYzljMWJmOTUxZjkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkV4aGliaXRpb24gUGxhY2UgQ2x1c3RlciAxMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjRhY2M3YjE1ZDhlNGZmMTkyN2NiNWRlNmIyMzBkZmMuc2V0Q29udGVudChodG1sXzk1MjJjYTcxNjJmMjRkMzI5ODEzMGM5YzFiZjk1MWY5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzg4MWUwYmRlZGM4YTRjYWZiNWY3ZTBlYWM3ZGNkMDE1LmJpbmRQb3B1cChwb3B1cF82NGFjYzdiMTVkOGU0ZmYxOTI3Y2I1ZGU2YjIzMGRmYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85MmFiZTM0ZjNjYmM0ZjdjOWNkY2U0MmMwOTY1MTM3MyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYzNjg0NzIsLTc5LjQyODE5MTQwMDAwMDAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmYjM2MCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84NTk0OGE3NmVhMmM0YmMxYmE2MGYyMDAxMDlhZjFlMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hNzNjM2FiOTlkYTc0ZDEzYjA3ODhkYzIyYWE4M2EyZCA9ICQoJzxkaXYgaWQ9Imh0bWxfYTczYzNhYjk5ZGE3NGQxM2IwNzg4ZGMyMmFhODNhMmQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBhcmtkYWxlIFZpbGxhZ2UgQ2x1c3RlciAxMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODU5NDhhNzZlYTJjNGJjMWJhNjBmMjAwMTA5YWYxZTAuc2V0Q29udGVudChodG1sX2E3M2MzYWI5OWRhNzRkMTNiMDc4OGRjMjJhYTgzYTJkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzkyYWJlMzRmM2NiYzRmN2M5Y2RjZTQyYzA5NjUxMzczLmJpbmRQb3B1cChwb3B1cF84NTk0OGE3NmVhMmM0YmMxYmE2MGYyMDAxMDlhZjFlMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mOGQ5Njg1OTI2MDg0ZWExYWYzZTc1NWJlZTg5N2ZlNiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2ODk5ODUsLTc5LjMxNTU3MTU5OTk5OTk4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzJhZGRkZCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zODlhZjM2YjVkYmY0ZWZmODhiYWY3MmJhMWNmMWI2YyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83ZjcwMGEzZmRiZmM0ZDQzYmIzYWNmYTEzMDU1ZDU1YyA9ICQoJzxkaXYgaWQ9Imh0bWxfN2Y3MDBhM2ZkYmZjNGQ0M2JiM2FjZmExMzA1NWQ1NWMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRoZSBCZWFjaGVzIFdlc3QgQ2x1c3RlciA1PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zODlhZjM2YjVkYmY0ZWZmODhiYWY3MmJhMWNmMWI2Yy5zZXRDb250ZW50KGh0bWxfN2Y3MDBhM2ZkYmZjNGQ0M2JiM2FjZmExMzA1NWQ1NWMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjhkOTY4NTkyNjA4NGVhMWFmM2U3NTViZWU4OTdmZTYuYmluZFBvcHVwKHBvcHVwXzM4OWFmMzZiNWRiZjRlZmY4OGJhZjcyYmExY2YxYjZjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzUzODViYmU4YTAxMzQ1NDZhYTRlNTQxOWE5ZDQwMTVmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY4OTk4NSwtNzkuMzE1NTcxNTk5OTk5OThdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMmFkZGRkIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Q5ZTRhY2I0ZGU5OTRlYWNiMTcxNWFiNmZjYjExNDFlID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzY0YmE1NjBmM2MwNDQzNzM4YzhjYTUwN2FlNzJhYzliID0gJCgnPGRpdiBpZD0iaHRtbF82NGJhNTYwZjNjMDQ0MzczOGM4Y2E1MDdhZTcyYWM5YiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SW5kaWEgQmF6YWFyIENsdXN0ZXIgNTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDllNGFjYjRkZTk5NGVhY2IxNzE1YWI2ZmNiMTE0MWUuc2V0Q29udGVudChodG1sXzY0YmE1NjBmM2MwNDQzNzM4YzhjYTUwN2FlNzJhYzliKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzUzODViYmU4YTAxMzQ1NDZhYTRlNTQxOWE5ZDQwMTVmLmJpbmRQb3B1cChwb3B1cF9kOWU0YWNiNGRlOTk0ZWFjYjE3MTVhYjZmY2IxMTQxZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82NWMwYjc3YTc0NWE0NjY5YTY3MWQ2NzA4MDgwNmM4YiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODE5ODUsLTc5LjM3OTgxNjkwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iOWM0MmY1YzIxYWE0YmI3YjkyOTZiNzdjNTJjNmFmOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wYzc0Mzk2MDJiNjc0MWExOTQ3MWIzY2E4MzU4MDRjOSA9ICQoJzxkaXYgaWQ9Imh0bWxfMGM3NDM5NjAyYjY3NDFhMTk0NzFiM2NhODM1ODA0YzkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNvbW1lcmNlIENvdXJ0IENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjljNDJmNWMyMWFhNGJiN2I5Mjk2Yjc3YzUyYzZhZjkuc2V0Q29udGVudChodG1sXzBjNzQzOTYwMmI2NzQxYTE5NDcxYjNjYTgzNTgwNGM5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzY1YzBiNzdhNzQ1YTQ2NjlhNjcxZDY3MDgwODA2YzhiLmJpbmRQb3B1cChwb3B1cF9iOWM0MmY1YzIxYWE0YmI3YjkyOTZiNzdjNTJjNmFmOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81YzA0NGM2YzU0NmE0MzdiODg0MWQxZGY3NTVmNzg1MSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODE5ODUsLTc5LjM3OTgxNjkwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lNDJlYWMxZWJjYWU0ODQ3YTc3M2JkMTA2NjVlMGZkMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kYTg1MWZjNmIwZTI0OGQwYTk0NDdlMDI4YzdlMTg2MiA9ICQoJzxkaXYgaWQ9Imh0bWxfZGE4NTFmYzZiMGUyNDhkMGE5NDQ3ZTAyOGM3ZTE4NjIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlZpY3RvcmlhIEhvdGVsIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTQyZWFjMWViY2FlNDg0N2E3NzNiZDEwNjY1ZTBmZDAuc2V0Q29udGVudChodG1sX2RhODUxZmM2YjBlMjQ4ZDBhOTQ0N2UwMjhjN2UxODYyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzVjMDQ0YzZjNTQ2YTQzN2I4ODQxZDFkZjc1NWY3ODUxLmJpbmRQb3B1cChwb3B1cF9lNDJlYWMxZWJjYWU0ODQ3YTc3M2JkMTA2NjVlMGZkMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iZGU1NzZiYjQ1NmE0Nzc4YTMxYmM4YmRhNTQ0NTk1ZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1OTUyNTUsLTc5LjM0MDkyM10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDM0YWIxMTIxMmI2NGEyMGIxNWU5YWM2NDQ1ODVhMzkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMDRjZDJkZmM4YzZmNGIyNzljNTMxYWRjMzM1NjJjZDEgPSAkKCc8ZGl2IGlkPSJodG1sXzA0Y2QyZGZjOGM2ZjRiMjc5YzUzMWFkYzMzNTYyY2QxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdHVkaW8gRGlzdHJpY3QgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wMzRhYjExMjEyYjY0YTIwYjE1ZTlhYzY0NDU4NWEzOS5zZXRDb250ZW50KGh0bWxfMDRjZDJkZmM4YzZmNGIyNzljNTMxYWRjMzM1NjJjZDEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYmRlNTc2YmI0NTZhNDc3OGEzMWJjOGJkYTU0NDU5NWYuYmluZFBvcHVwKHBvcHVwXzAzNGFiMTEyMTJiNjRhMjBiMTVlOWFjNjQ0NTg1YTM5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzMzMjFlNzkzNmMzNzQwNzdiM2I2YTQ5ODM4MzI2ZGYxID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzI4MDIwNSwtNzkuMzg4NzkwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNkNGRkODAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzYxNmJkMDA0YWVjNGVmOTkyNWJjYzM0MWZlOWMzNWUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZGU0NTc3MTM5MmVkNDE4MTk0NDJjOTFmNTU1NGM0ZjQgPSAkKCc8ZGl2IGlkPSJodG1sX2RlNDU3NzEzOTJlZDQxODE5NDQyYzkxZjU1NTRjNGY0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MYXdyZW5jZSBQYXJrIENsdXN0ZXIgOTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzYxNmJkMDA0YWVjNGVmOTkyNWJjYzM0MWZlOWMzNWUuc2V0Q29udGVudChodG1sX2RlNDU3NzEzOTJlZDQxODE5NDQyYzkxZjU1NTRjNGY0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzMzMjFlNzkzNmMzNzQwNzdiM2I2YTQ5ODM4MzI2ZGYxLmJpbmRQb3B1cChwb3B1cF83NjE2YmQwMDRhZWM0ZWY5OTI1YmNjMzQxZmU5YzM1ZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lMDE5MTQ0NjgxZmY0ODdmYmU3MzAxNzRiMDIzZjhhZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxMTY5NDgsLTc5LjQxNjkzNTU5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwMDBmZiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iN2YxZTZjZGVjYjQ0MmFhOTMyNjc4MWQ5NWQxNmUzOCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jNDQ3OTE3M2JhN2E0MTFmYmJkMmM2Njc4MDZmNzM3NiA9ICQoJzxkaXYgaWQ9Imh0bWxfYzQ0NzkxNzNiYTdhNDExZmJiZDJjNjY3ODA2ZjczNzYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJvc2VsYXduIENsdXN0ZXIgMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjdmMWU2Y2RlY2I0NDJhYTkzMjY3ODFkOTVkMTZlMzguc2V0Q29udGVudChodG1sX2M0NDc5MTczYmE3YTQxMWZiYmQyYzY2NzgwNmY3Mzc2KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2UwMTkxNDQ2ODFmZjQ4N2ZiZTczMDE3NGIwMjNmOGFmLmJpbmRQb3B1cChwb3B1cF9iN2YxZTZjZGVjYjQ0MmFhOTMyNjc4MWQ5NWQxNmUzOCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80MGFjYzNlZDJkNWM0NTA4YWRlZWZkZjc3ODYwYmQyZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxMjc1MTEsLTc5LjM5MDE5NzVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMmFkZGRkIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2QxMTUxN2JkNGY5NjRmNTFhZDlmNjEwNmQ4YmJjZjA2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2YxMWE5N2M5MTJkNjQwMDc4MGEzNWY2MTZkN2NiN2Q3ID0gJCgnPGRpdiBpZD0iaHRtbF9mMTFhOTdjOTEyZDY0MDA3ODBhMzVmNjE2ZDdjYjdkNyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RGF2aXN2aWxsZSBOb3J0aCBDbHVzdGVyIDU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2QxMTUxN2JkNGY5NjRmNTFhZDlmNjEwNmQ4YmJjZjA2LnNldENvbnRlbnQoaHRtbF9mMTFhOTdjOTEyZDY0MDA3ODBhMzVmNjE2ZDdjYjdkNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80MGFjYzNlZDJkNWM0NTA4YWRlZWZkZjc3ODYwYmQyZC5iaW5kUG9wdXAocG9wdXBfZDExNTE3YmQ0Zjk2NGY1MWFkOWY2MTA2ZDhiYmNmMDYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNmE5YzRlOTc1MDEwNGI5MjhjZTZjZWI4ZWQzMGE4NWMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42OTY5NDc2LC03OS40MTEzMDcyMDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNhYmY2OWIiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfY2RmNTEzNTE0ZDUzNDViN2JiOGRlNTFmMTAzNTNlMjYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZWEzNWNmYzc5Y2FiNDVjMzhlNjQ4M2RiNWRmMjg4MTIgPSAkKCc8ZGl2IGlkPSJodG1sX2VhMzVjZmM3OWNhYjQ1YzM4ZTY0ODNkYjVkZjI4ODEyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Gb3Jlc3QgSGlsbCBOb3J0aCBDbHVzdGVyIDg8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2NkZjUxMzUxNGQ1MzQ1YjdiYjhkZTUxZjEwMzUzZTI2LnNldENvbnRlbnQoaHRtbF9lYTM1Y2ZjNzljYWI0NWMzOGU2NDgzZGI1ZGYyODgxMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82YTljNGU5NzUwMTA0YjkyOGNlNmNlYjhlZDMwYTg1Yy5iaW5kUG9wdXAocG9wdXBfY2RmNTEzNTE0ZDUzNDViN2JiOGRlNTFmMTAzNTNlMjYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNmE4ZGIyNzliODFmNDYzZGFhMjVjZTRhNGJhOWYyYjIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42OTY5NDc2LC03OS40MTEzMDcyMDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNhYmY2OWIiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDc0Yzk4Njc1YmZkNDgwY2FiYzg4YjYzMjQ1NDZiZjkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODdjMmM4ZWM5M2E5NDBlYTgwNDYxNDM3YTllMTczYmQgPSAkKCc8ZGl2IGlkPSJodG1sXzg3YzJjOGVjOTNhOTQwZWE4MDQ2MTQzN2E5ZTE3M2JkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Gb3Jlc3QgSGlsbCBXZXN0IENsdXN0ZXIgODwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDc0Yzk4Njc1YmZkNDgwY2FiYzg4YjYzMjQ1NDZiZjkuc2V0Q29udGVudChodG1sXzg3YzJjOGVjOTNhOTQwZWE4MDQ2MTQzN2E5ZTE3M2JkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzZhOGRiMjc5YjgxZjQ2M2RhYTI1Y2U0YTRiYTlmMmIyLmJpbmRQb3B1cChwb3B1cF8wNzRjOTg2NzViZmQ0ODBjYWJjODhiNjMyNDU0NmJmOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lZjhhMjhlMmQ0MDI0ZDkzOGY4NzRhOWRhYzU3NTUzNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MTYwODMsLTc5LjQ2NDc2MzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmN2U0MSIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81MGZkNDE3N2RlOTU0NmE1OTY5YTY1NWQ3OTY2ZWZlOCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xMmE4MDg2NjFlNmE0NThjOTAzMGY1MzZkYTlkN2RkNiA9ICQoJzxkaXYgaWQ9Imh0bWxfMTJhODA4NjYxZTZhNDU4YzkwMzBmNTM2ZGE5ZDdkZDYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhpZ2ggUGFyayBDbHVzdGVyIDExPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81MGZkNDE3N2RlOTU0NmE1OTY5YTY1NWQ3OTY2ZWZlOC5zZXRDb250ZW50KGh0bWxfMTJhODA4NjYxZTZhNDU4YzkwMzBmNTM2ZGE5ZDdkZDYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZWY4YTI4ZTJkNDAyNGQ5MzhmODc0YTlkYWM1NzU1MzUuYmluZFBvcHVwKHBvcHVwXzUwZmQ0MTc3ZGU5NTQ2YTU5NjlhNjU1ZDc5NjZlZmU4KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU5YjkwZWJlNzgxMDQ1ZjI4ODg1YjllNTE4NGEzNjEwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjYxNjA4MywtNzkuNDY0NzYzMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmY3ZTQxIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzBkODRlNWQ5YzdjNTRmZGNhMTAxNmE1MzU1NWUzNzhmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2MxM2I4NzdmNDVjNjRlMDA5ODczMTkxNTRjZWNlYmIyID0gJCgnPGRpdiBpZD0iaHRtbF9jMTNiODc3ZjQ1YzY0ZTAwOTg3MzE5MTU0Y2VjZWJiMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VGhlIEp1bmN0aW9uIFNvdXRoIENsdXN0ZXIgMTE8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzBkODRlNWQ5YzdjNTRmZGNhMTAxNmE1MzU1NWUzNzhmLnNldENvbnRlbnQoaHRtbF9jMTNiODc3ZjQ1YzY0ZTAwOTg3MzE5MTU0Y2VjZWJiMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81OWI5MGViZTc4MTA0NWYyODg4NWI5ZTUxODRhMzYxMC5iaW5kUG9wdXAocG9wdXBfMGQ4NGU1ZDljN2M1NGZkY2ExMDE2YTUzNTU1ZTM3OGYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzExODMxOTRkMjU1NDc5Y2IwY2UyODliMzQyNzg4YmQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTUzODM0LC03OS40MDU2Nzg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMWE4MzdkYjg1MTk4NDRmZGE0NWM0ZGE1YzY5OWRlZWYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTJlYTNhNWNkMjlhNDFkMjk3YWFiM2RhMzcwYWQzMmEgPSAkKCc8ZGl2IGlkPSJodG1sX2UyZWEzYTVjZDI5YTQxZDI5N2FhYjNkYTM3MGFkMzJhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Ob3J0aCBUb3JvbnRvIFdlc3QgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xYTgzN2RiODUxOTg0NGZkYTQ1YzRkYTVjNjk5ZGVlZi5zZXRDb250ZW50KGh0bWxfZTJlYTNhNWNkMjlhNDFkMjk3YWFiM2RhMzcwYWQzMmEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzExODMxOTRkMjU1NDc5Y2IwY2UyODliMzQyNzg4YmQuYmluZFBvcHVwKHBvcHVwXzFhODM3ZGI4NTE5ODQ0ZmRhNDVjNGRhNWM2OTlkZWVmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2ZlZTY1MjFlYmNhMTQxYmViMzE5YmI1ODgwMzM1MTBlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjcyNzA5NywtNzkuNDA1Njc4NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmY0MTIxIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Q3YzJhOGZiNmUzMTQ4MjE5YzkwYjg0MzA2YTEwMjczID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2U3MzYyMTAzYWViODRjZWQ4YzNkNDg3M2ZlZWM3ZTRkID0gJCgnPGRpdiBpZD0iaHRtbF9lNzM2MjEwM2FlYjg0Y2VkOGMzZDQ4NzNmZWVjN2U0ZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VGhlIEFubmV4IENsdXN0ZXIgMTI8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Q3YzJhOGZiNmUzMTQ4MjE5YzkwYjg0MzA2YTEwMjczLnNldENvbnRlbnQoaHRtbF9lNzM2MjEwM2FlYjg0Y2VkOGMzZDQ4NzNmZWVjN2U0ZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mZWU2NTIxZWJjYTE0MWJlYjMxOWJiNTg4MDMzNTEwZS5iaW5kUG9wdXAocG9wdXBfZDdjMmE4ZmI2ZTMxNDgyMTljOTBiODQzMDZhMTAyNzMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzg1Y2MxY2Q1MGYwNDU0YWIwOTRkMWQxYjdmYjcwOTMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NzI3MDk3LC03OS40MDU2Nzg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjQxMjEiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMmRkNTQyODIyY2E4NGUyMjk0OTQ3OTEwNTU4NzVkMzAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWQwMzljM2JkYmQ1NDA2Mjg2N2JjYTQ5NzI4OGVmODEgPSAkKCc8ZGl2IGlkPSJodG1sXzVkMDM5YzNiZGJkNTQwNjI4NjdiY2E0OTcyODhlZjgxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Ob3J0aCBNaWR0b3duIENsdXN0ZXIgMTI8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzJkZDU0MjgyMmNhODRlMjI5NDk0NzkxMDU1ODc1ZDMwLnNldENvbnRlbnQoaHRtbF81ZDAzOWMzYmRiZDU0MDYyODY3YmNhNDk3Mjg4ZWY4MSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zODVjYzFjZDUwZjA0NTRhYjA5NGQxZDFiN2ZiNzA5My5iaW5kUG9wdXAocG9wdXBfMmRkNTQyODIyY2E4NGUyMjk0OTQ3OTEwNTU4NzVkMzApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNGVkZmE0MzRkN2YyNGRjMTk2MWRiMDEzMmY0MmIyZDcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NzI3MDk3LC03OS40MDU2Nzg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjQxMjEiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmQ4ZDMxNjg2MWQ2NGE2NTlmNjM4ZDBmMDM5YWVlMDAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMWIwY2ZkMDdhYThmNGRkY2IxNTNkZjJmMWU1ZjJlMDkgPSAkKCc8ZGl2IGlkPSJodG1sXzFiMGNmZDA3YWE4ZjRkZGNiMTUzZGYyZjFlNWYyZTA5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Zb3JrdmlsbGUgQ2x1c3RlciAxMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNmQ4ZDMxNjg2MWQ2NGE2NTlmNjM4ZDBmMDM5YWVlMDAuc2V0Q29udGVudChodG1sXzFiMGNmZDA3YWE4ZjRkZGNiMTUzZGYyZjFlNWYyZTA5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzRlZGZhNDM0ZDdmMjRkYzE5NjFkYjAxMzJmNDJiMmQ3LmJpbmRQb3B1cChwb3B1cF82ZDhkMzE2ODYxZDY0YTY1OWY2MzhkMGYwMzlhZWUwMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80YmJmZmJjNDE4NGQ0NTg4OWVlNTQ1YWY3OTU3OGQwNCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODk1OTcsLTc5LjQ1NjMyNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjMzNDU0MTZlZTc0NGI1MGIwNjczYjM5NzFjNDM1MGIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfM2UzY2FlZWIxZDZhNDk3OWI4NTMyOWM4MzFhNDc4ZmMgPSAkKCc8ZGl2IGlkPSJodG1sXzNlM2NhZWViMWQ2YTQ5NzliODUzMjljODMxYTQ3OGZjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QYXJrZGFsZSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2IzMzQ1NDE2ZWU3NDRiNTBiMDY3M2IzOTcxYzQzNTBiLnNldENvbnRlbnQoaHRtbF8zZTNjYWVlYjFkNmE0OTc5Yjg1MzI5YzgzMWE0NzhmYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80YmJmZmJjNDE4NGQ0NTg4OWVlNTQ1YWY3OTU3OGQwNC5iaW5kUG9wdXAocG9wdXBfYjMzNDU0MTZlZTc0NGI1MGIwNjczYjM5NzFjNDM1MGIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOWYwZTYxNWFlYjBkNDNiYjk4NmMyNzNmMWU5NWEwYzAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDg5NTk3LC03OS40NTYzMjVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzI3NmI0MjI2NDM2MTRiZDY4ZGU1YjQyODZhOTBjYzQzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzc1MTRkNTdiYWZjYjQxOGI5ODllNTFmNmI1MWZjM2QzID0gJCgnPGRpdiBpZD0iaHRtbF83NTE0ZDU3YmFmY2I0MThiOTg5ZTUxZjZiNTFmYzNkMyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Um9uY2VzdmFsbGVzIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjc2YjQyMjY0MzYxNGJkNjhkZTViNDI4NmE5MGNjNDMuc2V0Q29udGVudChodG1sXzc1MTRkNTdiYWZjYjQxOGI5ODllNTFmNmI1MWZjM2QzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzlmMGU2MTVhZWIwZDQzYmI5ODZjMjczZjFlOTVhMGMwLmJpbmRQb3B1cChwb3B1cF8yNzZiNDIyNjQzNjE0YmQ2OGRlNWI0Mjg2YTkwY2M0Myk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mNzA0MGNmMTFiNGU0ZDY3YTZjNTY5OTRlOGE2ZThjYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcwNDMyNDQsLTc5LjM4ODc5MDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzlkOGVkYjg2MGUwZjRkMDJhNmRhYzY1ODMzMjU0MDk5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzA0NGI3OTU3OTRjNTRjYWZiMzEwNmZkNjk4NzhjOTViID0gJCgnPGRpdiBpZD0iaHRtbF8wNDRiNzk1Nzk0YzU0Y2FmYjMxMDZmZDY5ODc4Yzk1YiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RGF2aXN2aWxsZSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzlkOGVkYjg2MGUwZjRkMDJhNmRhYzY1ODMzMjU0MDk5LnNldENvbnRlbnQoaHRtbF8wNDRiNzk1Nzk0YzU0Y2FmYjMxMDZmZDY5ODc4Yzk1Yik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mNzA0MGNmMTFiNGU0ZDY3YTZjNTY5OTRlOGE2ZThjYi5iaW5kUG9wdXAocG9wdXBfOWQ4ZWRiODYwZTBmNGQwMmE2ZGFjNjU4MzMyNTQwOTkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMmJjYTNkNjczODNiNGI5ZmIwMDMzYWU2M2ZkZTE1MDAgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjI2OTU2LC03OS40MDAwNDkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmN2U0MSIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mNTBlZjUwMjRmYjM0OWQ3YTIyYmU2OGM4YjNiNjdhZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jMTFjNWI4MjRhZGQ0NDQ1YmQzZDE5ZDNkNzA1OGU5MyA9ICQoJzxkaXYgaWQ9Imh0bWxfYzExYzViODI0YWRkNDQ0NWJkM2QxOWQzZDcwNThlOTMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhhcmJvcmQgQ2x1c3RlciAxMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZjUwZWY1MDI0ZmIzNDlkN2EyMmJlNjhjOGIzYjY3YWYuc2V0Q29udGVudChodG1sX2MxMWM1YjgyNGFkZDQ0NDViZDNkMTlkM2Q3MDU4ZTkzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzJiY2EzZDY3MzgzYjRiOWZiMDAzM2FlNjNmZGUxNTAwLmJpbmRQb3B1cChwb3B1cF9mNTBlZjUwMjRmYjM0OWQ3YTIyYmU2OGM4YjNiNjdhZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lNTQ2Y2Y0OGI5ZTI0MTBjYTA3MTZhM2I0ZTRkYzRhMyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MjY5NTYsLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmY3ZTQxIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzNkN2Y4YmJiYzUwOTQzMmE5ZjMxNzgyZWI5ODBlOGU1ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2ZkMTM2ZGE4YzIzNTRiYzc5MTg5M2VjMDhmZTg4NTA4ID0gJCgnPGRpdiBpZD0iaHRtbF9mZDEzNmRhOGMyMzU0YmM3OTE4OTNlYzA4ZmU4ODUwOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VW5pdmVyc2l0eSBvZiBUb3JvbnRvIENsdXN0ZXIgMTE8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzNkN2Y4YmJiYzUwOTQzMmE5ZjMxNzgyZWI5ODBlOGU1LnNldENvbnRlbnQoaHRtbF9mZDEzNmRhOGMyMzU0YmM3OTE4OTNlYzA4ZmU4ODUwOCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lNTQ2Y2Y0OGI5ZTI0MTBjYTA3MTZhM2I0ZTRkYzRhMy5iaW5kUG9wdXAocG9wdXBfM2Q3ZjhiYmJjNTA5NDMyYTlmMzE3ODJlYjk4MGU4ZTUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZTgyNmQ3MDRhYThmNDBiNzg0ZjgzZTE5OWExNGY3NTYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTE1NzA2LC03OS40ODQ0NDk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jMDU3MjU3ZWVjZWU0NTYwYjcxYWYyYTdiNWQzMzA0MiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85N2M2YjA1OGRjZDE0ZDA4ODg0ZDgxNmI5MTVmNzUwNSA9ICQoJzxkaXYgaWQ9Imh0bWxfOTdjNmIwNThkY2QxNGQwODg4NGQ4MTZiOTE1Zjc1MDUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJ1bm55bWVkZSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2MwNTcyNTdlZWNlZTQ1NjBiNzFhZjJhN2I1ZDMzMDQyLnNldENvbnRlbnQoaHRtbF85N2M2YjA1OGRjZDE0ZDA4ODg0ZDgxNmI5MTVmNzUwNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lODI2ZDcwNGFhOGY0MGI3ODRmODNlMTk5YTE0Zjc1Ni5iaW5kUG9wdXAocG9wdXBfYzA1NzI1N2VlY2VlNDU2MGI3MWFmMmE3YjVkMzMwNDIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZTUxYzhhZTRiMTk3NGZkMmIzZjViY2E5ZTRhNjFiMDMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTE1NzA2LC03OS40ODQ0NDk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82MjUzNGIxYzcwNWU0OGM3YWZhZWUxOTQ4NjJhMmNjNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lMTExN2VmY2ExMjk0YjA5YjBhOGZiYTQ3YTljZmFhOSA9ICQoJzxkaXYgaWQ9Imh0bWxfZTExMTdlZmNhMTI5NGIwOWIwYThmYmE0N2E5Y2ZhYTkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN3YW5zZWEgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82MjUzNGIxYzcwNWU0OGM3YWZhZWUxOTQ4NjJhMmNjNi5zZXRDb250ZW50KGh0bWxfZTExMTdlZmNhMTI5NGIwOWIwYThmYmE0N2E5Y2ZhYTkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZTUxYzhhZTRiMTk3NGZkMmIzZjViY2E5ZTRhNjFiMDMuYmluZFBvcHVwKHBvcHVwXzYyNTM0YjFjNzA1ZTQ4YzdhZmFlZTE5NDg2MmEyY2M2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzc4MWZiY2IyY2QxMzQ5MGU4Yzc1MDc3YWE1NDQ0NTVjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg5NTc0MywtNzkuMzgzMTU5OTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMmM3ZWY3IiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzE0OWNjNDVkMDRmMzQ2N2NhZDUwZjVjMDNhZjE2NDk2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzllMDk2YmE5YTE0MDRjZTlhYTE1ZWNiNTE0M2M5YTJjID0gJCgnPGRpdiBpZD0iaHRtbF85ZTA5NmJhOWExNDA0Y2U5YWExNWVjYjUxNDNjOWEyYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TW9vcmUgUGFyayBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE0OWNjNDVkMDRmMzQ2N2NhZDUwZjVjMDNhZjE2NDk2LnNldENvbnRlbnQoaHRtbF85ZTA5NmJhOWExNDA0Y2U5YWExNWVjYjUxNDNjOWEyYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83ODFmYmNiMmNkMTM0OTBlOGM3NTA3N2FhNTQ0NDU1Yy5iaW5kUG9wdXAocG9wdXBfMTQ5Y2M0NWQwNGYzNDY3Y2FkNTBmNWMwM2FmMTY0OTYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYThlMTMyNGNkMWJhNDNmNWI3MTY3MjM5OThkODljOTMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODk1NzQzLC03OS4zODMxNTk5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMyYzdlZjciLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTFlYjM0ODQ1MTA2NDRlOWEyN2U4MjYzN2FjZWQ5ODUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOTYzNTFiZTE3ODllNDAzOGJhYTEzNDI3NGU5YWQ2OGQgPSAkKCc8ZGl2IGlkPSJodG1sXzk2MzUxYmUxNzg5ZTQwMzhiYWExMzQyNzRlOWFkNjhkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdW1tZXJoaWxsIEVhc3QgQ2x1c3RlciAzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lMWViMzQ4NDUxMDY0NGU5YTI3ZTgyNjM3YWNlZDk4NS5zZXRDb250ZW50KGh0bWxfOTYzNTFiZTE3ODllNDAzOGJhYTEzNDI3NGU5YWQ2OGQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYThlMTMyNGNkMWJhNDNmNWI3MTY3MjM5OThkODljOTMuYmluZFBvcHVwKHBvcHVwX2UxZWIzNDg0NTEwNjQ0ZTlhMjdlODI2MzdhY2VkOTg1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzIyMzI2NTJiZmExYTQwNGFiMjgyM2UxMWMxOTM1NTkyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUzMjA1NywtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjdlNDEiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOTE1MzczM2U5NWYyNGM3NDlmNzE5YzQ3MjBlYTJjZmUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYmY1Y2Y4ZTczNTE2NDkwOGE5YmU3YTA3YWMyOTRmY2EgPSAkKCc8ZGl2IGlkPSJodG1sX2JmNWNmOGU3MzUxNjQ5MDhhOWJlN2EwN2FjMjk0ZmNhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DaGluYXRvd24gQ2x1c3RlciAxMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfOTE1MzczM2U5NWYyNGM3NDlmNzE5YzQ3MjBlYTJjZmUuc2V0Q29udGVudChodG1sX2JmNWNmOGU3MzUxNjQ5MDhhOWJlN2EwN2FjMjk0ZmNhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzIyMzI2NTJiZmExYTQwNGFiMjgyM2UxMWMxOTM1NTkyLmJpbmRQb3B1cChwb3B1cF85MTUzNzMzZTk1ZjI0Yzc0OWY3MTljNDcyMGVhMmNmZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yYzkxMmRkMDc2MDQ0ODJiOWE3MzA3YmY4OWY3ZWMzMyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MzIwNTcsLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmY3ZTQxIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzdiZGQ1ZDczNTcwNDQ0NDM4ZWRlY2Q2NGJjNGFiNzI0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzM4YjA5ZjE4NTExMTQ3NDFhYmRiY2MwZmI4NTE2NTg5ID0gJCgnPGRpdiBpZD0iaHRtbF8zOGIwOWYxODUxMTE0NzQxYWJkYmNjMGZiODUxNjU4OSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+R3JhbmdlIFBhcmsgQ2x1c3RlciAxMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfN2JkZDVkNzM1NzA0NDQ0MzhlZGVjZDY0YmM0YWI3MjQuc2V0Q29udGVudChodG1sXzM4YjA5ZjE4NTExMTQ3NDFhYmRiY2MwZmI4NTE2NTg5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzJjOTEyZGQwNzYwNDQ4MmI5YTczMDdiZjg5ZjdlYzMzLmJpbmRQb3B1cChwb3B1cF83YmRkNWQ3MzU3MDQ0NDQzOGVkZWNkNjRiYzRhYjcyNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kZmNjOWNmYmM4ZmU0MzkwYTMxMjA5NTIxY2I2ODYwMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MzIwNTcsLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmY3ZTQxIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzI2MGRlNWU4NWZiMjQyNTZiOWQ3YzlhNDYwOTIzMWI5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2QzYjZmNmNkMGYyMjRiODRiZTJhNjM5OTFiNzFkZDQ1ID0gJCgnPGRpdiBpZD0iaHRtbF9kM2I2ZjZjZDBmMjI0Yjg0YmUyYTYzOTkxYjcxZGQ0NSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+S2Vuc2luZ3RvbiBNYXJrZXQgQ2x1c3RlciAxMTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjYwZGU1ZTg1ZmIyNDI1NmI5ZDdjOWE0NjA5MjMxYjkuc2V0Q29udGVudChodG1sX2QzYjZmNmNkMGYyMjRiODRiZTJhNjM5OTFiNzFkZDQ1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2RmY2M5Y2ZiYzhmZTQzOTBhMzEyMDk1MjFjYjY4NjAxLmJpbmRQb3B1cChwb3B1cF8yNjBkZTVlODVmYjI0MjU2YjlkN2M5YTQ2MDkyMzFiOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85ZTlhYzRhOGE3NWI0OWM2YWI3MWYzMTQyYmViZmY3ZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY4NjQxMjI5OTk5OTk5LC03OS40MDAwNDkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzAwYjVlYiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yYWQwZGFmYjE2YWM0M2E1OGIxNjUxMzcwMzZiNTJiMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kMjdhMjA2N2M0Yzg0MWI3YTM4ODhkZTM5MWFhMmM5OSA9ICQoJzxkaXYgaWQ9Imh0bWxfZDI3YTIwNjdjNGM4NDFiN2EzODg4ZGUzOTFhYTJjOTkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRlZXIgUGFyayBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzJhZDBkYWZiMTZhYzQzYTU4YjE2NTEzNzAzNmI1MmIwLnNldENvbnRlbnQoaHRtbF9kMjdhMjA2N2M0Yzg0MWI3YTM4ODhkZTM5MWFhMmM5OSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85ZTlhYzRhOGE3NWI0OWM2YWI3MWYzMTQyYmViZmY3Zi5iaW5kUG9wdXAocG9wdXBfMmFkMGRhZmIxNmFjNDNhNThiMTY1MTM3MDM2YjUyYjApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjM5Y2I1NWZlNDMzNGQwYjg2YzQ5ZWRiNDMwNDM3MjIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODY0MTIyOTk5OTk5OSwtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMwMGI1ZWIiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzMyZWMxMGFkMjI4NDkyMjkwZDhlMmFmZTc5MjE3NmQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzk0YjRjMjQ5MGYxNDEyOTgwODVlN2U5NTNiZmUyMTAgPSAkKCc8ZGl2IGlkPSJodG1sXzM5NGI0YzI0OTBmMTQxMjk4MDg1ZTdlOTUzYmZlMjEwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Gb3Jlc3QgSGlsbCBTRSBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzMzMmVjMTBhZDIyODQ5MjI5MGQ4ZTJhZmU3OTIxNzZkLnNldENvbnRlbnQoaHRtbF8zOTRiNGMyNDkwZjE0MTI5ODA4NWU3ZTk1M2JmZTIxMCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yMzljYjU1ZmU0MzM0ZDBiODZjNDllZGI0MzA0MzcyMi5iaW5kUG9wdXAocG9wdXBfMzMyZWMxMGFkMjI4NDkyMjkwZDhlMmFmZTc5MjE3NmQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDM4MmZiYmJmYTYwNDBlZGE2NjhlZjhmNTY4MzE2NGUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODY0MTIyOTk5OTk5OSwtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMwMGI1ZWIiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYmU3ZTJkMjgzNDBiNGJkNDlkN2JlOTI3ODMwNDc5ZTIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNmZmMmY1ZTRmYmNmNDU2M2I0OGRiZjIyOTdmZmI4ZTEgPSAkKCc8ZGl2IGlkPSJodG1sXzZmZjJmNWU0ZmJjZjQ1NjNiNDhkYmYyMjk3ZmZiOGUxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5SYXRobmVsbHkgQ2x1c3RlciA0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iZTdlMmQyODM0MGI0YmQ0OWQ3YmU5Mjc4MzA0NzllMi5zZXRDb250ZW50KGh0bWxfNmZmMmY1ZTRmYmNmNDU2M2I0OGRiZjIyOTdmZmI4ZTEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZDM4MmZiYmJmYTYwNDBlZGE2NjhlZjhmNTY4MzE2NGUuYmluZFBvcHVwKHBvcHVwX2JlN2UyZDI4MzQwYjRiZDQ5ZDdiZTkyNzgzMDQ3OWUyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzNiMzI0N2U1NzE0NjQ1NjA5ZjM4YzUxYmIyNDFlYmM0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg2NDEyMjk5OTk5OTksLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMDBiNWViIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2NiYTg3NTY4ZDhmMzQwNWI4YjdjYmU4MDhjMTMyN2I3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzk5ZTU2MTAyMGFkMjQ1ODE5NzlmZmUzMGE1YTA4NGNkID0gJCgnPGRpdiBpZD0iaHRtbF85OWU1NjEwMjBhZDI0NTgxOTc5ZmZlMzBhNWEwODRjZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U291dGggSGlsbCBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2NiYTg3NTY4ZDhmMzQwNWI4YjdjYmU4MDhjMTMyN2I3LnNldENvbnRlbnQoaHRtbF85OWU1NjEwMjBhZDI0NTgxOTc5ZmZlMzBhNWEwODRjZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zYjMyNDdlNTcxNDY0NTYwOWYzOGM1MWJiMjQxZWJjNC5iaW5kUG9wdXAocG9wdXBfY2JhODc1NjhkOGYzNDA1YjhiN2NiZTgwOGMxMzI3YjcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjdhYTQ2YWZkOTdmNDE2YTkwOGM0YTE3N2U1NjhlYWMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42ODY0MTIyOTk5OTk5OSwtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiMwMGI1ZWIiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMWI0Zjk5ZGY5NDE4NDI5MmJhMmNjN2E5MDZlYzdlOTYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYjY4ZjdhNGQ2N2Y4NDYzYjgyYzRlNjViNTAxZWE0NTQgPSAkKCc8ZGl2IGlkPSJodG1sX2I2OGY3YTRkNjdmODQ2M2I4MmM0ZTY1YjUwMWVhNDU0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdW1tZXJoaWxsIFdlc3QgQ2x1c3RlciA0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xYjRmOTlkZjk0MTg0MjkyYmEyY2M3YTkwNmVjN2U5Ni5zZXRDb250ZW50KGh0bWxfYjY4ZjdhNGQ2N2Y4NDYzYjgyYzRlNjViNTAxZWE0NTQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjdhYTQ2YWZkOTdmNDE2YTkwOGM0YTE3N2U1NjhlYWMuYmluZFBvcHVwKHBvcHVwXzFiNGY5OWRmOTQxODQyOTJiYTJjYzdhOTA2ZWM3ZTk2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzRjMTcwNDg4OTk4YzRkMDk4NmQzYWVjMDRjMzBmZTk1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjI4OTQ2NywtNzkuMzk0NDE5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM1NjQxZmQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDJjNzcyM2QxMWExNDRhNGI2YzM2ZDdiNWEwMWFjY2YgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTk2NDY1OGQ4YTYwNDlkY2FmMDgxZDlkZTE4NzliNWUgPSAkKCc8ZGl2IGlkPSJodG1sXzE5NjQ2NThkOGE2MDQ5ZGNhZjA4MWQ5ZGUxODc5YjVlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DTiBUb3dlciBDbHVzdGVyIDI8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzAyYzc3MjNkMTFhMTQ0YTRiNmMzNmQ3YjVhMDFhY2NmLnNldENvbnRlbnQoaHRtbF8xOTY0NjU4ZDhhNjA0OWRjYWYwODFkOWRlMTg3OWI1ZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80YzE3MDQ4ODk5OGM0ZDA5ODZkM2FlYzA0YzMwZmU5NS5iaW5kUG9wdXAocG9wdXBfMDJjNzcyM2QxMWExNDRhNGI2YzM2ZDdiNWEwMWFjY2YpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZjA1MjU5ZDk3M2FlNDRmOGI3ZDc4YjgzODQzMzhiOTYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Mjg5NDY3LC03OS4zOTQ0MTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzU2NDFmZCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xMTEwNjgzMTgyNTQ0ZGU0YTk5NzdiMDZkOTRiY2Y1NiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mYTczZTdkMDBlZWU0NmU0YWQ1MjNhOTMyYTQ5YjdmZSA9ICQoJzxkaXYgaWQ9Imh0bWxfZmE3M2U3ZDAwZWVlNDZlNGFkNTIzYTkzMmE0OWI3ZmUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJhdGh1cnN0IFF1YXkgQ2x1c3RlciAyPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xMTEwNjgzMTgyNTQ0ZGU0YTk5NzdiMDZkOTRiY2Y1Ni5zZXRDb250ZW50KGh0bWxfZmE3M2U3ZDAwZWVlNDZlNGFkNTIzYTkzMmE0OWI3ZmUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjA1MjU5ZDk3M2FlNDRmOGI3ZDc4YjgzODQzMzhiOTYuYmluZFBvcHVwKHBvcHVwXzExMTA2ODMxODI1NDRkZTRhOTk3N2IwNmQ5NGJjZjU2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzU2YTQ3ZWZmY2MyYjRmODc5ZDA0MzQ2YzQ4Zjc0YTI1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjI4OTQ2NywtNzkuMzk0NDE5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM1NjQxZmQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDQ3MTZjMDMxMmQ3NDc5NTk3ODBlZTZmNWYxMTc0ODEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZThiZDc2MDc0Njk4NGQxM2FmNTMwY2JlODQ1M2E3NmUgPSAkKCc8ZGl2IGlkPSJodG1sX2U4YmQ3NjA3NDY5ODRkMTNhZjUzMGNiZTg0NTNhNzZlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Jc2xhbmQgYWlycG9ydCBDbHVzdGVyIDI8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzA0NzE2YzAzMTJkNzQ3OTU5NzgwZWU2ZjVmMTE3NDgxLnNldENvbnRlbnQoaHRtbF9lOGJkNzYwNzQ2OTg0ZDEzYWY1MzBjYmU4NDUzYTc2ZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81NmE0N2VmZmNjMmI0Zjg3OWQwNDM0NmM0OGY3NGEyNS5iaW5kUG9wdXAocG9wdXBfMDQ3MTZjMDMxMmQ3NDc5NTk3ODBlZTZmNWYxMTc0ODEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfY2U4Y2IzM2Y1YWQ0NGI4ZGIyZmZiNmFjNGRlODVjNzIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Mjg5NDY3LC03OS4zOTQ0MTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzU2NDFmZCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jN2VjMmQ5NzkxNDY0NjRhYmZjNjU0NGYyMzg4ODQ1NSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iNjNiZTI0MDk5Nzc0NGNlYjVjNzg2YjhmYmZkMDRiOCA9ICQoJzxkaXYgaWQ9Imh0bWxfYjYzYmUyNDA5OTc3NDRjZWI1Yzc4NmI4ZmJmZDA0YjgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhhcmJvdXJmcm9udCBXZXN0IENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzdlYzJkOTc5MTQ2NDY0YWJmYzY1NDRmMjM4ODg0NTUuc2V0Q29udGVudChodG1sX2I2M2JlMjQwOTk3NzQ0Y2ViNWM3ODZiOGZiZmQwNGI4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2NlOGNiMzNmNWFkNDRiOGRiMmZmYjZhYzRkZTg1YzcyLmJpbmRQb3B1cChwb3B1cF9jN2VjMmQ5NzkxNDY0NjRhYmZjNjU0NGYyMzg4ODQ1NSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mMTIzNDc0MDBmOGI0YWE3YTkwN2ZiMjRmYjNkN2NkMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYyODk0NjcsLTc5LjM5NDQxOTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjNTY0MWZkIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzA3YTQ4ZmM5NzgxNTRhMzA4N2RjNmJhODMxMDNhOWU4ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2RkNzFkOWVkYTk2NjQ2OTY4Yjk2ZDBmN2Y0NDBkY2E4ID0gJCgnPGRpdiBpZD0iaHRtbF9kZDcxZDllZGE5NjY0Njk2OGI5NmQwZjdmNDQwZGNhOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+S2luZyBhbmQgU3BhZGluYSBDbHVzdGVyIDI8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzA3YTQ4ZmM5NzgxNTRhMzA4N2RjNmJhODMxMDNhOWU4LnNldENvbnRlbnQoaHRtbF9kZDcxZDllZGE5NjY0Njk2OGI5NmQwZjdmNDQwZGNhOCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9mMTIzNDc0MDBmOGI0YWE3YTkwN2ZiMjRmYjNkN2NkMS5iaW5kUG9wdXAocG9wdXBfMDdhNDhmYzk3ODE1NGEzMDg3ZGM2YmE4MzEwM2E5ZTgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOWY4MmMxZDY4YjM5NDBhNGFiMDc1OTY1Zjc0ZGJkNDkgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Mjg5NDY3LC03OS4zOTQ0MTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzU2NDFmZCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85ZWNmYTAxMDMxMDU0NjE0YTdmYTkzZTkxNDUyOTgwOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zNDhiNzc2Mjg3NjQ0ZDhiYmJkNjViYjlhY2E3Y2JkMSA9ICQoJzxkaXYgaWQ9Imh0bWxfMzQ4Yjc3NjI4NzY0NGQ4YmJiZDY1YmI5YWNhN2NiZDEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJhaWx3YXkgTGFuZHMgQ2x1c3RlciAyPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85ZWNmYTAxMDMxMDU0NjE0YTdmYTkzZTkxNDUyOTgwOS5zZXRDb250ZW50KGh0bWxfMzQ4Yjc3NjI4NzY0NGQ4YmJiZDY1YmI5YWNhN2NiZDEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOWY4MmMxZDY4YjM5NDBhNGFiMDc1OTY1Zjc0ZGJkNDkuYmluZFBvcHVwKHBvcHVwXzllY2ZhMDEwMzEwNTQ2MTRhN2ZhOTNlOTE0NTI5ODA5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2FkMjA1MTBjMTIzMzQ2YjM5NmY0M2FjOWM4YWRhYTI4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjI4OTQ2NywtNzkuMzk0NDE5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM1NjQxZmQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODE0MzllMTBiYmFmNGNkMTljYTJkMWQxMzQ4Yzg3NTAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYzc5OWUzZmE0MjBiNDRmZGFhN2U2Y2Y4ODBkNDlkY2EgPSAkKCc8ZGl2IGlkPSJodG1sX2M3OTllM2ZhNDIwYjQ0ZmRhYTdlNmNmODgwZDQ5ZGNhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Tb3V0aCBOaWFnYXJhIENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODE0MzllMTBiYmFmNGNkMTljYTJkMWQxMzQ4Yzg3NTAuc2V0Q29udGVudChodG1sX2M3OTllM2ZhNDIwYjQ0ZmRhYTdlNmNmODgwZDQ5ZGNhKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2FkMjA1MTBjMTIzMzQ2YjM5NmY0M2FjOWM4YWRhYTI4LmJpbmRQb3B1cChwb3B1cF84MTQzOWUxMGJiYWY0Y2QxOWNhMmQxZDEzNDhjODc1MCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80MjA4ZTQzZDEwN2U0YzJlYWU3Yjg5NTY5ZGRhNWM0OSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3OTU2MjYsLTc5LjM3NzUyOTQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzgwZmZiNCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lNzNjZTJkMmI1MmU0MTA0OTBkZWUxMGM5Y2M4Y2Y1MSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xZDNlMmZkMDE4ODg0NDUzYWE1YTBlZjA5N2JhYmUyNSA9ICQoJzxkaXYgaWQ9Imh0bWxfMWQzZTJmZDAxODg4NDQ1M2FhNWEwZWYwOTdiYWJlMjUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJvc2VkYWxlIENsdXN0ZXIgNzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTczY2UyZDJiNTJlNDEwNDkwZGVlMTBjOWNjOGNmNTEuc2V0Q29udGVudChodG1sXzFkM2UyZmQwMTg4ODQ0NTNhYTVhMGVmMDk3YmFiZTI1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzQyMDhlNDNkMTA3ZTRjMmVhZTdiODk1NjlkZGE1YzQ5LmJpbmRQb3B1cChwb3B1cF9lNzNjZTJkMmI1MmU0MTA0OTBkZWUxMGM5Y2M4Y2Y1MSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85ZDI4ZmVlMmZhYWM0MWYyYTlmMWYzM2Y0OTdiMWQ0MCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NjQzNTIsLTc5LjM3NDg0NTk5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84M2M0Njg5YWIxMGQ0MzdkYjQ5NzIyMDM1OWI4MDRkYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mMGNjYTQwZmMyOGM0NWYxYjU4ZTIyYjk0YzdlMTgwYiA9ICQoJzxkaXYgaWQ9Imh0bWxfZjBjY2E0MGZjMjhjNDVmMWI1OGUyMmI5NGM3ZTE4MGIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0biBBIFBPIEJveGVzIDI1IFRoZSBFc3BsYW5hZGUgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF84M2M0Njg5YWIxMGQ0MzdkYjQ5NzIyMDM1OWI4MDRkYy5zZXRDb250ZW50KGh0bWxfZjBjY2E0MGZjMjhjNDVmMWI1OGUyMmI5NGM3ZTE4MGIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOWQyOGZlZTJmYWFjNDFmMmE5ZjFmMzNmNDk3YjFkNDAuYmluZFBvcHVwKHBvcHVwXzgzYzQ2ODlhYjEwZDQzN2RiNDk3MjIwMzU5YjgwNGRjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzMzNGYzZjdlZTJhNTQ5NzM4NTI5MmZmZWVjMDZkYWY4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY3OTY3LC03OS4zNjc2NzUzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wYTVkNzQ1OTQ4OGM0NzExODE3MzM5MDA4NWRkZGJlYiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wNGI4OTBkZGYxMmU0NmI5YTU0NmY0YWE3ZDVkNjQzNSA9ICQoJzxkaXYgaWQ9Imh0bWxfMDRiODkwZGRmMTJlNDZiOWE1NDZmNGFhN2Q1ZDY0MzUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNhYmJhZ2V0b3duIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMGE1ZDc0NTk0ODhjNDcxMTgxNzMzOTAwODVkZGRiZWIuc2V0Q29udGVudChodG1sXzA0Yjg5MGRkZjEyZTQ2YjlhNTQ2ZjRhYTdkNWQ2NDM1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzMzNGYzZjdlZTJhNTQ5NzM4NTI5MmZmZWVjMDZkYWY4LmJpbmRQb3B1cChwb3B1cF8wYTVkNzQ1OTQ4OGM0NzExODE3MzM5MDA4NWRkZGJlYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jYTkyOTJhMzBlYmQ0YmQyOGJmOWJjNzMwZWFmOTBiYSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2Nzk2NywtNzkuMzY3Njc1M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfM2QxMjU3YzkwOTVkNDMyMzg0ZTExNDdmOGRkMGQ2NjIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTBiOWM2NWY3MzM4NDAwMDliMGYzZGI1ZmMyODI0OGMgPSAkKCc8ZGl2IGlkPSJodG1sX2UwYjljNjVmNzMzODQwMDA5YjBmM2RiNWZjMjgyNDhjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Eb3dudG93biBUb3JvbnRvLCBTdC4gSmFtZXMgVG93biBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzNkMTI1N2M5MDk1ZDQzMjM4NGUxMTQ3ZjhkZDBkNjYyLnNldENvbnRlbnQoaHRtbF9lMGI5YzY1ZjczMzg0MDAwOWIwZjNkYjVmYzI4MjQ4Yyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jYTkyOTJhMzBlYmQ0YmQyOGJmOWJjNzMwZWFmOTBiYS5iaW5kUG9wdXAocG9wdXBfM2QxMjU3YzkwOTVkNDMyMzg0ZTExNDdmOGRkMGQ2NjIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODZkNDgxODk0MzllNGQ1ODgwOTFmODYwNzZkZDJmYzQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDg0MjkyLC03OS4zODIyODAyXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsYWNrIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF82OGFiNjQyOGVmZTM0OWNjOTA0YjViNjU3YjllNzU3Yyk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mNWRkOTc5Nzk3Yjc0YjE0YjE3NWM3ZTAzMzQwM2ZhNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mNDdhY2Y4NjE0MjU0ZDQ4YTgyNzI1MTA4M2E4YjAxZSA9ICQoJzxkaXYgaWQ9Imh0bWxfZjQ3YWNmODYxNDI1NGQ0OGE4MjcyNTEwODNhOGIwMWUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZpcnN0IENhbmFkaWFuIFBsYWNlIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZjVkZDk3OTc5N2I3NGIxNGIxNzVjN2UwMzM0MDNmYTUuc2V0Q29udGVudChodG1sX2Y0N2FjZjg2MTQyNTRkNDhhODI3MjUxMDgzYThiMDFlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzg2ZDQ4MTg5NDM5ZTRkNTg4MDkxZjg2MDc2ZGQyZmM0LmJpbmRQb3B1cChwb3B1cF9mNWRkOTc5Nzk3Yjc0YjE0YjE3NWM3ZTAzMzQwM2ZhNSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jYjI1ODMyNDhmZmI0ZWUwYTU5ZDExZjZmMGQwODAyYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODQyOTIsLTc5LjM4MjI4MDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzI2MWVjOTU4OTQxYTQ0MTg4NjM4N2EwZWQyMzZmZGFmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzMyYTQ2OTBkYmUzNTQ5MmY5MGQwNDJiM2Q0YzQyY2NiID0gJCgnPGRpdiBpZD0iaHRtbF8zMmE0NjkwZGJlMzU0OTJmOTBkMDQyYjNkNGM0MmNjYiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VW5kZXJncm91bmQgY2l0eSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzI2MWVjOTU4OTQxYTQ0MTg4NjM4N2EwZWQyMzZmZGFmLnNldENvbnRlbnQoaHRtbF8zMmE0NjkwZGJlMzU0OTJmOTBkMDQyYjNkNGM0MmNjYik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jYjI1ODMyNDhmZmI0ZWUwYTU5ZDExZjZmMGQwODAyYy5iaW5kUG9wdXAocG9wdXBfMjYxZWM5NTg5NDFhNDQxODg2Mzg3YTBlZDIzNmZkYWYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNDBjNzFlMmU3Mjc5NDhmOWE5ODc2YjY3Zjk0NGJhZTYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjU4NTk5LC03OS4zODMxNTk5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibGFjayIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfNjhhYjY0MjhlZmUzNDljYzkwNGI1YjY1N2I5ZTc1N2MpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjkzNWZlMzJmODg0NDgzNjk0YjMzZmJkMWJiNjJhYzMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODczOWI5ZjVlZWRjNGFiMzhmNTZhNDAzNzhlOTY0YjMgPSAkKCc8ZGl2IGlkPSJodG1sXzg3MzliOWY1ZWVkYzRhYjM4ZjU2YTQwMzc4ZTk2NGIzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DaHVyY2ggYW5kIFdlbGxlc2xleSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2I5MzVmZTMyZjg4NDQ4MzY5NGIzM2ZiZDFiYjYyYWMzLnNldENvbnRlbnQoaHRtbF84NzM5YjlmNWVlZGM0YWIzOGY1NmE0MDM3OGU5NjRiMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80MGM3MWUyZTcyNzk0OGY5YTk4NzZiNjdmOTQ0YmFlNi5iaW5kUG9wdXAocG9wdXBfYjkzNWZlMzJmODg0NDgzNjk0YjMzZmJkMWJiNjJhYzMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMmY4MGMyOGViZWU1NDYyMDg4NjI2MmUzNzExYWFmOWUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjI3NDM5LC03OS4zMjE1NThdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmxhY2siLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjMmFkZGRkIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzY4YWI2NDI4ZWZlMzQ5Y2M5MDRiNWI2NTdiOWU3NTdjKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2M0YWY4MmMyNDllNzQ1OTdhOWYzNDVkYWViM2FkMGEyID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzFjOGUyZjAyODY1YTQ1NDRhMDM5YjM5NmRmMGIwNjIyID0gJCgnPGRpdiBpZD0iaHRtbF8xYzhlMmYwMjg2NWE0NTQ0YTAzOWIzOTZkZjBiMDYyMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QnVzaW5lc3MgUmVwbHkgTWFpbCBQcm9jZXNzaW5nIENlbnRyZSA5NjkgRWFzdGVybiBDbHVzdGVyIDU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2M0YWY4MmMyNDllNzQ1OTdhOWYzNDVkYWViM2FkMGEyLnNldENvbnRlbnQoaHRtbF8xYzhlMmYwMjg2NWE0NTQ0YTAzOWIzOTZkZjBiMDYyMik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yZjgwYzI4ZWJlZTU0NjIwODg2MjYyZTM3MTFhYWY5ZS5iaW5kUG9wdXAocG9wdXBfYzRhZjgyYzI0OWU3NDU5N2E5ZjM0NWRhZWIzYWQwYTIpOwoKICAgICAgICAgICAgCiAgICAgICAgCjwvc2NyaXB0Pg==" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



### k=13 in KMeans gives good results. The smaller k join most of center Neighborhoods in one cluster.


```python

```
