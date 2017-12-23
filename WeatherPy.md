

```python
Analysis:
Observed trend 1 - Temperature is higher at lower Latitudes and lower at higher latitudes.
Observed trend 2 - Humidity is above 60% at the ploes and less close to the equator.
Observed trend 3 - Latitude does not seem to effect Wind Speed or Cloudiness.

```


```python
# Dependencies
import pandas as pd
import numpy as np
import requests
import json
from citipy import citipy
from random import uniform
import csv
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime 

```


```python
city_lat = np.random.uniform(-90, 90, size=1000)
city_lng = np.random.uniform(-180, 180, size=1000)

```


```python
city_df = {"lat": city_lat, "lng": city_lng}
city_df = pd.DataFrame(city_df)
city_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>lat</th>
      <th>lng</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>28.881690</td>
      <td>126.188786</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-53.455534</td>
      <td>91.306739</td>
    </tr>
    <tr>
      <th>2</th>
      <td>42.533277</td>
      <td>137.887522</td>
    </tr>
    <tr>
      <th>3</th>
      <td>87.471114</td>
      <td>-133.501980</td>
    </tr>
    <tr>
      <th>4</th>
      <td>40.337183</td>
      <td>149.946731</td>
    </tr>
  </tbody>
</table>
</div>




```python
for index, row in city_df.iterrows():
    city = citipy.nearest_city(row["lat"], row["lng"]).city_name
    country = citipy.nearest_city(row["lat"], row["lng"]).country_code.upper()
    city_df.set_value(index, "City", city)
    city_df.set_value(index, "Country", country)
len(city_df)
```




    1000




```python
city_df.drop_duplicates(subset="City")
len(city_df)
```




    1000




```python
city_sample_df = city_df.sample(n=600)
city_sample_df.reset_index(inplace=True)
city_sample_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>index</th>
      <th>lat</th>
      <th>lng</th>
      <th>City</th>
      <th>Country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>832</td>
      <td>43.232020</td>
      <td>37.654659</td>
      <td>divnomorskoye</td>
      <td>RU</td>
    </tr>
    <tr>
      <th>1</th>
      <td>279</td>
      <td>40.864953</td>
      <td>-64.227315</td>
      <td>shelburne</td>
      <td>CA</td>
    </tr>
    <tr>
      <th>2</th>
      <td>437</td>
      <td>16.091650</td>
      <td>179.332685</td>
      <td>butaritari</td>
      <td>KI</td>
    </tr>
    <tr>
      <th>3</th>
      <td>815</td>
      <td>-9.230500</td>
      <td>138.729362</td>
      <td>merauke</td>
      <td>ID</td>
    </tr>
    <tr>
      <th>4</th>
      <td>116</td>
      <td>-27.815083</td>
      <td>142.835797</td>
      <td>broken hill</td>
      <td>AU</td>
    </tr>
  </tbody>
</table>
</div>




```python
url = "http://api.openweathermap.org/data/2.5/weather?"
api_key = "2e3cdabdb95992e0f95ee985c4c58629"
units = "imperial"
```


```python
print("Beginning Data Retrieval")
print('-----------------------------')
counter = 0
for index, rows in city_sample_df.iterrows():
    query_url = url + "appid=" + api_key + "&units=" + units + "&q=" + (rows["City"])
    cities_weather = requests.get(query_url).json()
    try:
        city_sample_df.set_value(index, "Max Temp", cities_weather["main"]["temp_max"])
        city_sample_df.set_value(index, "Humidity", cities_weather["main"]["humidity"])
        city_sample_df.set_value(index, "Wind Speed", cities_weather["wind"]["speed"])   
        city_sample_df.set_value(index, "Cloudiness", cities_weather["clouds"]["all"])
        city_sample_df.set_value(index, "Date", cities_weather["dt"])
        counter = counter + 1
        print ("Processing Record: ", counter, " of Set 1 |" , cities_weather["name"])
        print (query_url)  
    except:
        continue
        
        
```

    Beginning Data Retrieval
    -----------------------------
    Processing Record:  1  of Set 1 | Divnomorskoye
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=divnomorskoye
    Processing Record:  2  of Set 1 | Shelburne
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=shelburne
    Processing Record:  3  of Set 1 | Butaritari
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=butaritari
    Processing Record:  4  of Set 1 | Merauke
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=merauke
    Processing Record:  5  of Set 1 | Broken Hill
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=broken hill
    Processing Record:  6  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  7  of Set 1 | Sitka
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=sitka
    Processing Record:  8  of Set 1 | Hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hermanus
    Processing Record:  9  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  10  of Set 1 | Bethel
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bethel
    Processing Record:  11  of Set 1 | Yellowknife
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=yellowknife
    Processing Record:  12  of Set 1 | Castro
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=castro
    Processing Record:  13  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  14  of Set 1 | Bethel
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bethel
    Processing Record:  15  of Set 1 | Iralaya
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=iralaya
    Processing Record:  16  of Set 1 | Nizhniy Kuranakh
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=nizhniy kuranakh
    Processing Record:  17  of Set 1 | Saint George
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=saint george
    Processing Record:  18  of Set 1 | Talara
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=talara
    Processing Record:  19  of Set 1 | Taveta
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=taveta
    Processing Record:  20  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  21  of Set 1 | Sahuaripa
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=sahuaripa
    Processing Record:  22  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  23  of Set 1 | Kavieng
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kavieng
    Processing Record:  24  of Set 1 | San Policarpo
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=san policarpo
    Processing Record:  25  of Set 1 | Puerto Ayora
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=puerto ayora
    Processing Record:  26  of Set 1 | Nanortalik
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=nanortalik
    Processing Record:  27  of Set 1 | Quatre Cocos
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=quatre cocos
    Processing Record:  28  of Set 1 | Ribeira Grande
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ribeira grande
    Processing Record:  29  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  30  of Set 1 | Avarua
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=avarua
    Processing Record:  31  of Set 1 | Busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=busselton
    Processing Record:  32  of Set 1 | Hithadhoo
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hithadhoo
    Processing Record:  33  of Set 1 | Hithadhoo
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hithadhoo
    Processing Record:  34  of Set 1 | Esperance
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=esperance
    Processing Record:  35  of Set 1 | Punta Arenas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=punta arenas
    Processing Record:  36  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  37  of Set 1 | Port Hardy
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=port hardy
    Processing Record:  38  of Set 1 | Uyovu
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=uyovu
    Processing Record:  39  of Set 1 | Hay River
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hay river
    Processing Record:  40  of Set 1 | Puerto Ayora
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=puerto ayora
    Processing Record:  41  of Set 1 | Clyde River
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=clyde river
    Processing Record:  42  of Set 1 | Ingraj Bazar
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ingraj bazar
    Processing Record:  43  of Set 1 | Taltal
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=taltal
    Processing Record:  44  of Set 1 | Ureki
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ureki
    Processing Record:  45  of Set 1 | Harindanga
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=harindanga
    Processing Record:  46  of Set 1 | Puerto Ayora
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=puerto ayora
    Processing Record:  47  of Set 1 | Esperance
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=esperance
    Processing Record:  48  of Set 1 | Taoudenni
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=taoudenni
    Processing Record:  49  of Set 1 | Port-Gentil
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=port-gentil
    Processing Record:  50  of Set 1 | Dovers
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=dovers
    Processing Record:  51  of Set 1 | Castro
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=castro
    Processing Record:  52  of Set 1 | Coihaique
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=coihaique
    Processing Record:  53  of Set 1 | Avarua
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=avarua
    Processing Record:  54  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  55  of Set 1 | Tasiilaq
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tasiilaq
    Processing Record:  56  of Set 1 | Severo-Kurilsk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=severo-kurilsk
    Processing Record:  57  of Set 1 | Hasaki
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hasaki
    Processing Record:  58  of Set 1 | Los Llanos de Aridane
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=los llanos de aridane
    Processing Record:  59  of Set 1 | Nikolskoye
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=nikolskoye
    Processing Record:  60  of Set 1 | Vaini
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=vaini
    Processing Record:  61  of Set 1 | Pontian Kecil
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=pontian kecil
    Processing Record:  62  of Set 1 | Cape Town
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cape town
    Processing Record:  63  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  64  of Set 1 | Yellowknife
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=yellowknife
    Processing Record:  65  of Set 1 | Iqaluit
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=iqaluit
    Processing Record:  66  of Set 1 | Bluff
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bluff
    Processing Record:  67  of Set 1 | Iqaluit
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=iqaluit
    Processing Record:  68  of Set 1 | Key Largo
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=key largo
    Processing Record:  69  of Set 1 | Dikson
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=dikson
    Processing Record:  70  of Set 1 | Bambous Virieux
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bambous virieux
    Processing Record:  71  of Set 1 | Baicheng
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=baicheng
    Processing Record:  72  of Set 1 | Suntar
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=suntar
    Processing Record:  73  of Set 1 | Launceston
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=launceston
    Processing Record:  74  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  75  of Set 1 | New Norfolk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=new norfolk
    Processing Record:  76  of Set 1 | Fortuna
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=fortuna
    Processing Record:  77  of Set 1 | Cidreira
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cidreira
    Processing Record:  78  of Set 1 | Aljezur
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=aljezur
    Processing Record:  79  of Set 1 | Punta Arenas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=punta arenas
    Processing Record:  80  of Set 1 | Atuona
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=atuona
    Processing Record:  81  of Set 1 | Monroe
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=monroe
    Processing Record:  82  of Set 1 | Puerto Ayora
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=puerto ayora
    Processing Record:  83  of Set 1 | Constitucion
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=constitucion
    Processing Record:  84  of Set 1 | Vaini
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=vaini
    Processing Record:  85  of Set 1 | Mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mataura
    Processing Record:  86  of Set 1 | Linjiang
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=linjiang
    Processing Record:  87  of Set 1 | Tasiilaq
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tasiilaq
    Processing Record:  88  of Set 1 | Victoria
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=victoria
    Processing Record:  89  of Set 1 | Portland
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=portland
    Processing Record:  90  of Set 1 | Jamestown
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=jamestown
    Processing Record:  91  of Set 1 | Jamestown
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=jamestown
    Processing Record:  92  of Set 1 | Dikson
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=dikson
    Processing Record:  93  of Set 1 | Yalta
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=yalta
    Processing Record:  94  of Set 1 | Avarua
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=avarua
    Processing Record:  95  of Set 1 | Elizabethtown
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=elizabethtown
    Processing Record:  96  of Set 1 | San Ramon
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=san ramon
    Processing Record:  97  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  98  of Set 1 | Punta Arenas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=punta arenas
    Processing Record:  99  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  100  of Set 1 | Mar del Plata
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mar del plata
    Processing Record:  101  of Set 1 | Vaini
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=vaini
    Processing Record:  102  of Set 1 | Beyneu
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=beyneu
    Processing Record:  103  of Set 1 | Vaini
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=vaini
    Processing Record:  104  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  105  of Set 1 | Wanning
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=wanning
    Processing Record:  106  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  107  of Set 1 | Kahului
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kahului
    Processing Record:  108  of Set 1 | Barrow
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=barrow
    Processing Record:  109  of Set 1 | Norman Wells
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=norman wells
    Processing Record:  110  of Set 1 | Port Alfred
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=port alfred
    Processing Record:  111  of Set 1 | Kapaa
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kapaa
    Processing Record:  112  of Set 1 | Dakar
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=dakar
    Processing Record:  113  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  114  of Set 1 | Laguna
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=laguna
    Processing Record:  115  of Set 1 | Tornio
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tornio
    Processing Record:  116  of Set 1 | Busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=busselton
    Processing Record:  117  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  118  of Set 1 | Mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mataura
    Processing Record:  119  of Set 1 | Miyazu
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=miyazu
    Processing Record:  120  of Set 1 | Tarko-Sale
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tarko-sale
    Processing Record:  121  of Set 1 | Busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=busselton
    Processing Record:  122  of Set 1 | Lavrentiya
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=lavrentiya
    Processing Record:  123  of Set 1 | Ercis
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ercis
    Processing Record:  124  of Set 1 | Lompoc
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=lompoc
    Processing Record:  125  of Set 1 | Ribeira Grande
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ribeira grande
    Processing Record:  126  of Set 1 | Borsa
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=borsa
    Processing Record:  127  of Set 1 | Jiaocheng
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=jiaocheng
    Processing Record:  128  of Set 1 | Hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hermanus
    Processing Record:  129  of Set 1 | Broome
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=broome
    Processing Record:  130  of Set 1 | Nago
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=nago
    Processing Record:  131  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  132  of Set 1 | Hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hermanus
    Processing Record:  133  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  134  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  135  of Set 1 | Cape Town
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cape town
    Processing Record:  136  of Set 1 | Cape Town
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cape town
    Processing Record:  137  of Set 1 | Les Cayes
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=les cayes
    Processing Record:  138  of Set 1 | Butaritari
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=butaritari
    Processing Record:  139  of Set 1 | Mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mataura
    Processing Record:  140  of Set 1 | Busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=busselton
    Processing Record:  141  of Set 1 | Ponta do Sol
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ponta do sol
    Processing Record:  142  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  143  of Set 1 | Rawson
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rawson
    Processing Record:  144  of Set 1 | Fairbanks
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=fairbanks
    Processing Record:  145  of Set 1 | Tuktoyaktuk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tuktoyaktuk
    Processing Record:  146  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  147  of Set 1 | Esperance
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=esperance
    Processing Record:  148  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  149  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  150  of Set 1 | Am Timan
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=am timan
    Processing Record:  151  of Set 1 | Viedma
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=viedma
    Processing Record:  152  of Set 1 | Busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=busselton
    Processing Record:  153  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  154  of Set 1 | Severo-Kurilsk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=severo-kurilsk
    Processing Record:  155  of Set 1 | Oussouye
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=oussouye
    Processing Record:  156  of Set 1 | Tasiilaq
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tasiilaq
    Processing Record:  157  of Set 1 | Esperance
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=esperance
    Processing Record:  158  of Set 1 | Busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=busselton
    Processing Record:  159  of Set 1 | Cherskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cherskiy
    Processing Record:  160  of Set 1 | Adrar
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=adrar
    Processing Record:  161  of Set 1 | San Quintin
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=san quintin
    Processing Record:  162  of Set 1 | Pacifica
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=pacifica
    Processing Record:  163  of Set 1 | Mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mataura
    Processing Record:  164  of Set 1 | Bethel
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bethel
    Processing Record:  165  of Set 1 | Apac
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=apac
    Processing Record:  166  of Set 1 | Sao Joao da Barra
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=sao joao da barra
    Processing Record:  167  of Set 1 | Juneau
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=juneau
    Processing Record:  168  of Set 1 | Ceuta
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ceuta
    Processing Record:  169  of Set 1 | Chuy
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=chuy
    Processing Record:  170  of Set 1 | Atuona
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=atuona
    Processing Record:  171  of Set 1 | Umm Lajj
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=umm lajj
    Processing Record:  172  of Set 1 | Cabo San Lucas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cabo san lucas
    Processing Record:  173  of Set 1 | Keflavik
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=keflavik
    Processing Record:  174  of Set 1 | Zakupne
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=zakupne
    Processing Record:  175  of Set 1 | Punta Arenas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=punta arenas
    Processing Record:  176  of Set 1 | Pacific Grove
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=pacific grove
    Processing Record:  177  of Set 1 | Cape Town
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cape town
    Processing Record:  178  of Set 1 | Zhigansk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=zhigansk
    Processing Record:  179  of Set 1 | Leh
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=leh
    Processing Record:  180  of Set 1 | Brandfort
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=brandfort
    Processing Record:  181  of Set 1 | Qaanaaq
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=qaanaaq
    Processing Record:  182  of Set 1 | Rockland
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rockland
    Processing Record:  183  of Set 1 | New Norfolk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=new norfolk
    Processing Record:  184  of Set 1 | Dikson
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=dikson
    Processing Record:  185  of Set 1 | Sayyan
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=sayyan
    Processing Record:  186  of Set 1 | Mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mataura
    Processing Record:  187  of Set 1 | Nikolskoye
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=nikolskoye
    Processing Record:  188  of Set 1 | Petropavlovsk-Kamchatskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=petropavlovsk-kamchatskiy
    Processing Record:  189  of Set 1 | Catalina
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=catalina
    Processing Record:  190  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  191  of Set 1 | Barra do Garcas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=barra do garcas
    Processing Record:  192  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  193  of Set 1 | Nouadhibou
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=nouadhibou
    Processing Record:  194  of Set 1 | Castro
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=castro
    Processing Record:  195  of Set 1 | Bethel
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bethel
    Processing Record:  196  of Set 1 | Puro
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=puro
    Processing Record:  197  of Set 1 | Mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mataura
    Processing Record:  198  of Set 1 | Atuona
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=atuona
    Processing Record:  199  of Set 1 | Mar del Plata
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mar del plata
    Processing Record:  200  of Set 1 | Orel-Izumrud
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=orel-izumrud
    Processing Record:  201  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  202  of Set 1 | Puerto Ayora
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=puerto ayora
    Processing Record:  203  of Set 1 | Port Alfred
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=port alfred
    Processing Record:  204  of Set 1 | Punta Arenas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=punta arenas
    Processing Record:  205  of Set 1 | Egvekinot
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=egvekinot
    Processing Record:  206  of Set 1 | Achit
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=achit
    Processing Record:  207  of Set 1 | Shahreza
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=shahreza
    Processing Record:  208  of Set 1 | Georgetown
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=georgetown
    Processing Record:  209  of Set 1 | Cooma
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cooma
    Processing Record:  210  of Set 1 | Yumen
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=yumen
    Processing Record:  211  of Set 1 | Severo-Kurilsk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=severo-kurilsk
    Processing Record:  212  of Set 1 | Talnakh
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=talnakh
    Processing Record:  213  of Set 1 | Hobart
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hobart
    Processing Record:  214  of Set 1 | Hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hermanus
    Processing Record:  215  of Set 1 | Mar del Plata
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mar del plata
    Processing Record:  216  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  217  of Set 1 | Garden City
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=garden city
    Processing Record:  218  of Set 1 | Avarua
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=avarua
    Processing Record:  219  of Set 1 | Itarema
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=itarema
    Processing Record:  220  of Set 1 | Mantua
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mantua
    Processing Record:  221  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  222  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  223  of Set 1 | Baherden
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=baherden
    Processing Record:  224  of Set 1 | Hobart
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hobart
    Processing Record:  225  of Set 1 | Mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mataura
    Processing Record:  226  of Set 1 | Port Elizabeth
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=port elizabeth
    Processing Record:  227  of Set 1 | Ugoofaaru
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ugoofaaru
    Processing Record:  228  of Set 1 | Koulikoro
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=koulikoro
    Processing Record:  229  of Set 1 | Yellowknife
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=yellowknife
    Processing Record:  230  of Set 1 | Georgetown
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=georgetown
    Processing Record:  231  of Set 1 | Kapaa
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kapaa
    Processing Record:  232  of Set 1 | Mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mataura
    Processing Record:  233  of Set 1 | Caravelas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=caravelas
    Processing Record:  234  of Set 1 | Hobart
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hobart
    Processing Record:  235  of Set 1 | Taltal
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=taltal
    Processing Record:  236  of Set 1 | Sao Filipe
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=sao filipe
    Processing Record:  237  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  238  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  239  of Set 1 | Portland
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=portland
    Processing Record:  240  of Set 1 | Muisne
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=muisne
    Processing Record:  241  of Set 1 | Provideniya
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=provideniya
    Processing Record:  242  of Set 1 | Nhulunbuy
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=nhulunbuy
    Processing Record:  243  of Set 1 | Omboue
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=omboue
    Processing Record:  244  of Set 1 | Naze
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=naze
    Processing Record:  245  of Set 1 | Saint George
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=saint george
    Processing Record:  246  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  247  of Set 1 | Bredasdorp
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bredasdorp
    Processing Record:  248  of Set 1 | Santa Maria
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=santa maria
    Processing Record:  249  of Set 1 | Chitral
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=chitral
    Processing Record:  250  of Set 1 | Aksarka
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=aksarka
    Processing Record:  251  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  252  of Set 1 | Hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hermanus
    Processing Record:  253  of Set 1 | Kapaa
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kapaa
    Processing Record:  254  of Set 1 | Esperance
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=esperance
    Processing Record:  255  of Set 1 | Katiola
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=katiola
    Processing Record:  256  of Set 1 | Thompson
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=thompson
    Processing Record:  257  of Set 1 | Iqaluit
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=iqaluit
    Processing Record:  258  of Set 1 | Castro
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=castro
    Processing Record:  259  of Set 1 | Mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mataura
    Processing Record:  260  of Set 1 | Lavrentiya
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=lavrentiya
    Processing Record:  261  of Set 1 | Georgetown
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=georgetown
    Processing Record:  262  of Set 1 | Yellowknife
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=yellowknife
    Processing Record:  263  of Set 1 | Busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=busselton
    Processing Record:  264  of Set 1 | Praia da Vitoria
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=praia da vitoria
    Processing Record:  265  of Set 1 | Tasiilaq
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tasiilaq
    Processing Record:  266  of Set 1 | Cape Town
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cape town
    Processing Record:  267  of Set 1 | Sorland
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=sorland
    Processing Record:  268  of Set 1 | Fort Nelson
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=fort nelson
    Processing Record:  269  of Set 1 | East London
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=east london
    Processing Record:  270  of Set 1 | Vaini
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=vaini
    Processing Record:  271  of Set 1 | Provideniya
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=provideniya
    Processing Record:  272  of Set 1 | Barcelos
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=barcelos
    Processing Record:  273  of Set 1 | Gubkinskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=gubkinskiy
    Processing Record:  274  of Set 1 | Saint-Philippe
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=saint-philippe
    Processing Record:  275  of Set 1 | Mahajanga
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mahajanga
    Processing Record:  276  of Set 1 | Naze
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=naze
    Processing Record:  277  of Set 1 | Punta Arenas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=punta arenas
    Processing Record:  278  of Set 1 | Tuatapere
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tuatapere
    Processing Record:  279  of Set 1 | Hovd
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hovd
    Processing Record:  280  of Set 1 | Port Alfred
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=port alfred
    Processing Record:  281  of Set 1 | Buin
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=buin
    Processing Record:  282  of Set 1 | Atar
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=atar
    Processing Record:  283  of Set 1 | New Norfolk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=new norfolk
    Processing Record:  284  of Set 1 | Mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mataura
    Processing Record:  285  of Set 1 | Katsuura
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=katsuura
    Processing Record:  286  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  287  of Set 1 | Bluff
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bluff
    Processing Record:  288  of Set 1 | Maningrida
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=maningrida
    Processing Record:  289  of Set 1 | Genhe
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=genhe
    Processing Record:  290  of Set 1 | Airai
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=airai
    Processing Record:  291  of Set 1 | Middlebury
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=middlebury
    Processing Record:  292  of Set 1 | Airai
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=airai
    Processing Record:  293  of Set 1 | Jalu
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=jalu
    Processing Record:  294  of Set 1 | Yar-Sale
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=yar-sale
    Processing Record:  295  of Set 1 | Grand Gaube
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=grand gaube
    Processing Record:  296  of Set 1 | Hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hermanus
    Processing Record:  297  of Set 1 | Kruisfontein
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kruisfontein
    Processing Record:  298  of Set 1 | Hobart
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hobart
    Processing Record:  299  of Set 1 | Norman Wells
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=norman wells
    Processing Record:  300  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  301  of Set 1 | Souillac
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=souillac
    Processing Record:  302  of Set 1 | Alta Floresta
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=alta floresta
    Processing Record:  303  of Set 1 | Veraval
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=veraval
    Processing Record:  304  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  305  of Set 1 | Torbay
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=torbay
    Processing Record:  306  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  307  of Set 1 | Verkhnyaya Sinyachikha
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=verkhnyaya sinyachikha
    Processing Record:  308  of Set 1 | Hamilton
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hamilton
    Processing Record:  309  of Set 1 | Adrar
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=adrar
    Processing Record:  310  of Set 1 | Mar del Plata
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mar del plata
    Processing Record:  311  of Set 1 | San Patricio
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=san patricio
    Processing Record:  312  of Set 1 | Karratha
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=karratha
    Processing Record:  313  of Set 1 | Cabo San Lucas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cabo san lucas
    Processing Record:  314  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  315  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  316  of Set 1 | Vaini
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=vaini
    Processing Record:  317  of Set 1 | Nome
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=nome
    Processing Record:  318  of Set 1 | Khatanga
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=khatanga
    Processing Record:  319  of Set 1 | Busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=busselton
    Processing Record:  320  of Set 1 | Ostrovnoy
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ostrovnoy
    Processing Record:  321  of Set 1 | Westport
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=westport
    Processing Record:  322  of Set 1 | Cap-aux-Meules
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cap-aux-meules
    Processing Record:  323  of Set 1 | Castro
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=castro
    Processing Record:  324  of Set 1 | Barrow
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=barrow
    Processing Record:  325  of Set 1 | Kenai
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kenai
    Processing Record:  326  of Set 1 | Hasaki
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hasaki
    Processing Record:  327  of Set 1 | Klaksvik
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=klaksvik
    Processing Record:  328  of Set 1 | Kununurra
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kununurra
    Processing Record:  329  of Set 1 | Kaitangata
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kaitangata
    Processing Record:  330  of Set 1 | Avarua
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=avarua
    Processing Record:  331  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  332  of Set 1 | Kapaa
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kapaa
    Processing Record:  333  of Set 1 | Tabat
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tabat
    Processing Record:  334  of Set 1 | Beloha
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=beloha
    Processing Record:  335  of Set 1 | Puerto Ayora
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=puerto ayora
    Processing Record:  336  of Set 1 | Mombetsu
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mombetsu
    Processing Record:  337  of Set 1 | Lensk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=lensk
    Processing Record:  338  of Set 1 | Olenegorsk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=olenegorsk
    Processing Record:  339  of Set 1 | Grindavik
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=grindavik
    Processing Record:  340  of Set 1 | Barrow
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=barrow
    Processing Record:  341  of Set 1 | Torbay
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=torbay
    Processing Record:  342  of Set 1 | Mar del Plata
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mar del plata
    Processing Record:  343  of Set 1 | Hilo
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hilo
    Processing Record:  344  of Set 1 | San Cristobal
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=san cristobal
    Processing Record:  345  of Set 1 | Thompson
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=thompson
    Processing Record:  346  of Set 1 | Dedovichi
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=dedovichi
    Processing Record:  347  of Set 1 | Danville
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=danville
    Processing Record:  348  of Set 1 | Nueva Imperial
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=nueva imperial
    Processing Record:  349  of Set 1 | New Norfolk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=new norfolk
    Processing Record:  350  of Set 1 | Okha
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=okha
    Processing Record:  351  of Set 1 | Thompson
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=thompson
    Processing Record:  352  of Set 1 | Hilo
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hilo
    Processing Record:  353  of Set 1 | Punta Arenas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=punta arenas
    Processing Record:  354  of Set 1 | Mocuba
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mocuba
    Processing Record:  355  of Set 1 | Shakawe
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=shakawe
    Processing Record:  356  of Set 1 | Hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hermanus
    Processing Record:  357  of Set 1 | Yellowknife
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=yellowknife
    Processing Record:  358  of Set 1 | Bonavista
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bonavista
    Processing Record:  359  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  360  of Set 1 | Saskylakh
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=saskylakh
    Processing Record:  361  of Set 1 | Lebu
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=lebu
    Processing Record:  362  of Set 1 | Saint-Philippe
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=saint-philippe
    Processing Record:  363  of Set 1 | Caborca
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=caborca
    Processing Record:  364  of Set 1 | Jamestown
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=jamestown
    Processing Record:  365  of Set 1 | Castro
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=castro
    Processing Record:  366  of Set 1 | Port Alfred
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=port alfred
    Processing Record:  367  of Set 1 | Praia da Vitoria
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=praia da vitoria
    Processing Record:  368  of Set 1 | Aklavik
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=aklavik
    Processing Record:  369  of Set 1 | Busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=busselton
    Processing Record:  370  of Set 1 | Ilulissat
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ilulissat
    Processing Record:  371  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  372  of Set 1 | Kodiak
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kodiak
    Processing Record:  373  of Set 1 | Atuona
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=atuona
    Processing Record:  374  of Set 1 | Severo-Kurilsk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=severo-kurilsk
    Processing Record:  375  of Set 1 | Te Anau
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=te anau
    Processing Record:  376  of Set 1 | Tasiilaq
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tasiilaq
    Processing Record:  377  of Set 1 | Bethel
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bethel
    Processing Record:  378  of Set 1 | Victoria
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=victoria
    Processing Record:  379  of Set 1 | Xifeng
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=xifeng
    Processing Record:  380  of Set 1 | Ariquemes
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ariquemes
    Processing Record:  381  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  382  of Set 1 | Broken Hill
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=broken hill
    Processing Record:  383  of Set 1 | Aksarka
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=aksarka
    Processing Record:  384  of Set 1 | Sitka
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=sitka
    Processing Record:  385  of Set 1 | Esperance
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=esperance
    Processing Record:  386  of Set 1 | Port Shepstone
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=port shepstone
    Processing Record:  387  of Set 1 | Punta Arenas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=punta arenas
    Processing Record:  388  of Set 1 | Punta Arenas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=punta arenas
    Processing Record:  389  of Set 1 | Bara
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bara
    Processing Record:  390  of Set 1 | Oistins
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=oistins
    Processing Record:  391  of Set 1 | Mahebourg
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mahebourg
    Processing Record:  392  of Set 1 | Sao Filipe
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=sao filipe
    Processing Record:  393  of Set 1 | Dali
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=dali
    Processing Record:  394  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  395  of Set 1 | Hobart
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hobart
    Processing Record:  396  of Set 1 | Leningradskiy
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=leningradskiy
    Processing Record:  397  of Set 1 | Arraial do Cabo
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=arraial do cabo
    Processing Record:  398  of Set 1 | San Patricio
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=san patricio
    Processing Record:  399  of Set 1 | Pevek
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=pevek
    Processing Record:  400  of Set 1 | Cap Malheureux
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cap malheureux
    Processing Record:  401  of Set 1 | Kutum
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kutum
    Processing Record:  402  of Set 1 | Port Elizabeth
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=port elizabeth
    Processing Record:  403  of Set 1 | Barrow
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=barrow
    Processing Record:  404  of Set 1 | Gushikawa
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=gushikawa
    Processing Record:  405  of Set 1 | Sitka
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=sitka
    Processing Record:  406  of Set 1 | Nikolskoye
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=nikolskoye
    Processing Record:  407  of Set 1 | Cape Town
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cape town
    Processing Record:  408  of Set 1 | Mitu
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mitu
    Processing Record:  409  of Set 1 | Cayenne
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cayenne
    Processing Record:  410  of Set 1 | Airai
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=airai
    Processing Record:  411  of Set 1 | Kapaa
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kapaa
    Processing Record:  412  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  413  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  414  of Set 1 | Upernavik
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=upernavik
    Processing Record:  415  of Set 1 | Torbay
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=torbay
    Processing Record:  416  of Set 1 | Bluff
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bluff
    Processing Record:  417  of Set 1 | Mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mataura
    Processing Record:  418  of Set 1 | Port Alfred
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=port alfred
    Processing Record:  419  of Set 1 | Mount Isa
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mount isa
    Processing Record:  420  of Set 1 | Cabo San Lucas
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=cabo san lucas
    Processing Record:  421  of Set 1 | Tuktoyaktuk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tuktoyaktuk
    Processing Record:  422  of Set 1 | Bosaso
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bosaso
    Processing Record:  423  of Set 1 | Amahai
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=amahai
    Processing Record:  424  of Set 1 | Hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hermanus
    Processing Record:  425  of Set 1 | Hobart
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hobart
    Processing Record:  426  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  427  of Set 1 | Shirokiy
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=shirokiy
    Processing Record:  428  of Set 1 | Busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=busselton
    Processing Record:  429  of Set 1 | Vaini
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=vaini
    Processing Record:  430  of Set 1 | Antalaha
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=antalaha
    Processing Record:  431  of Set 1 | Lakes Entrance
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=lakes entrance
    Processing Record:  432  of Set 1 | Port Elizabeth
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=port elizabeth
    Processing Record:  433  of Set 1 | Mahebourg
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mahebourg
    Processing Record:  434  of Set 1 | Iqaluit
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=iqaluit
    Processing Record:  435  of Set 1 | Hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hermanus
    Processing Record:  436  of Set 1 | Aklavik
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=aklavik
    Processing Record:  437  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  438  of Set 1 | Kidal
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kidal
    Processing Record:  439  of Set 1 | Kununurra
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kununurra
    Processing Record:  440  of Set 1 | Henties Bay
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=henties bay
    Processing Record:  441  of Set 1 | Torbay
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=torbay
    Processing Record:  442  of Set 1 | Kodiak
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kodiak
    Processing Record:  443  of Set 1 | Jutai
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=jutai
    Processing Record:  444  of Set 1 | Kodiak
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kodiak
    Processing Record:  445  of Set 1 | Bredasdorp
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bredasdorp
    Processing Record:  446  of Set 1 | Ponta do Sol
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ponta do sol
    Processing Record:  447  of Set 1 | Hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hermanus
    Processing Record:  448  of Set 1 | Buta
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=buta
    Processing Record:  449  of Set 1 | Kerugoya
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kerugoya
    Processing Record:  450  of Set 1 | Longyearbyen
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=longyearbyen
    Processing Record:  451  of Set 1 | Yellowknife
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=yellowknife
    Processing Record:  452  of Set 1 | Hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hermanus
    Processing Record:  453  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  454  of Set 1 | Bathsheba
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bathsheba
    Processing Record:  455  of Set 1 | Butaritari
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=butaritari
    Processing Record:  456  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  457  of Set 1 | Kapaa
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kapaa
    Processing Record:  458  of Set 1 | Presidencia Roque Saenz Pena
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=presidencia roque saenz pena
    Processing Record:  459  of Set 1 | Clyde River
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=clyde river
    Processing Record:  460  of Set 1 | Arraial do Cabo
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=arraial do cabo
    Processing Record:  461  of Set 1 | Mar del Plata
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mar del plata
    Processing Record:  462  of Set 1 | Shingu
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=shingu
    Processing Record:  463  of Set 1 | Sao Filipe
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=sao filipe
    Processing Record:  464  of Set 1 | Tingo Maria
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tingo maria
    Processing Record:  465  of Set 1 | Zalegoshch
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=zalegoshch
    Processing Record:  466  of Set 1 | Lebu
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=lebu
    Processing Record:  467  of Set 1 | Sur
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=sur
    Processing Record:  468  of Set 1 | Barrow
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=barrow
    Processing Record:  469  of Set 1 | Butaritari
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=butaritari
    Processing Record:  470  of Set 1 | Aberdeen
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=aberdeen
    Processing Record:  471  of Set 1 | Thompson
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=thompson
    Processing Record:  472  of Set 1 | Hobart
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hobart
    Processing Record:  473  of Set 1 | Tiznit
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tiznit
    Processing Record:  474  of Set 1 | Edd
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=edd
    Processing Record:  475  of Set 1 | Qaanaaq
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=qaanaaq
    Processing Record:  476  of Set 1 | Kishi
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=kishi
    Processing Record:  477  of Set 1 | Bandarbeyla
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bandarbeyla
    Processing Record:  478  of Set 1 | Souillac
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=souillac
    Processing Record:  479  of Set 1 | Puerto Ayora
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=puerto ayora
    Processing Record:  480  of Set 1 | Tuktoyaktuk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=tuktoyaktuk
    Processing Record:  481  of Set 1 | Ballater
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ballater
    Processing Record:  482  of Set 1 | Le Port
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=le port
    Processing Record:  483  of Set 1 | Qaanaaq
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=qaanaaq
    Processing Record:  484  of Set 1 | Alyangula
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=alyangula
    Processing Record:  485  of Set 1 | Flinders
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=flinders
    Processing Record:  486  of Set 1 | Busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=busselton
    Processing Record:  487  of Set 1 | Adrar
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=adrar
    Processing Record:  488  of Set 1 | San Patricio
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=san patricio
    Processing Record:  489  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  490  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  491  of Set 1 | La Rioja
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=la rioja
    Processing Record:  492  of Set 1 | Bluff
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bluff
    Processing Record:  493  of Set 1 | Yangjiang
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=yangjiang
    Processing Record:  494  of Set 1 | Rio Gallegos
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rio gallegos
    Processing Record:  495  of Set 1 | Thunder Bay
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=thunder bay
    Processing Record:  496  of Set 1 | Bud
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bud
    Processing Record:  497  of Set 1 | Severo-Kurilsk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=severo-kurilsk
    Processing Record:  498  of Set 1 | Maku
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=maku
    Processing Record:  499  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  500  of Set 1 | Smoky Lake
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=smoky lake
    Processing Record:  501  of Set 1 | Biloela
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=biloela
    Processing Record:  502  of Set 1 | Grindavik
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=grindavik
    Processing Record:  503  of Set 1 | Puerto Penasco
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=puerto penasco
    Processing Record:  504  of Set 1 | Dingle
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=dingle
    Processing Record:  505  of Set 1 | Verkhnyaya Inta
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=verkhnyaya inta
    Processing Record:  506  of Set 1 | Nikolskoye
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=nikolskoye
    Processing Record:  507  of Set 1 | The Valley
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=the valley
    Processing Record:  508  of Set 1 | Ahuimanu
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ahuimanu
    Processing Record:  509  of Set 1 | Qaanaaq
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=qaanaaq
    Processing Record:  510  of Set 1 | Byron Bay
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=byron bay
    Processing Record:  511  of Set 1 | Quatre Cocos
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=quatre cocos
    Processing Record:  512  of Set 1 | Foz
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=foz
    Processing Record:  513  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  514  of Set 1 | Goulburn
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=goulburn
    Processing Record:  515  of Set 1 | Menongue
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=menongue
    Processing Record:  516  of Set 1 | Barrow
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=barrow
    Processing Record:  517  of Set 1 | Butaritari
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=butaritari
    Processing Record:  518  of Set 1 | Narsaq
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=narsaq
    Processing Record:  519  of Set 1 | Geilo
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=geilo
    Processing Record:  520  of Set 1 | Mount Isa
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mount isa
    Processing Record:  521  of Set 1 | Butaritari
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=butaritari
    Processing Record:  522  of Set 1 | Chokurdakh
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=chokurdakh
    Processing Record:  523  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  524  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  525  of Set 1 | Derzhavinsk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=derzhavinsk
    Processing Record:  526  of Set 1 | Torbay
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=torbay
    Processing Record:  527  of Set 1 | Puerto Ayora
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=puerto ayora
    Processing Record:  528  of Set 1 | Waipawa
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=waipawa
    Processing Record:  529  of Set 1 | Derzhavinsk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=derzhavinsk
    Processing Record:  530  of Set 1 | Vaini
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=vaini
    Processing Record:  531  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  532  of Set 1 | Upernavik
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=upernavik
    Processing Record:  533  of Set 1 | Ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ushuaia
    Processing Record:  534  of Set 1 | Carnarvon
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=carnarvon
    Processing Record:  535  of Set 1 | Nuqui
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=nuqui
    Processing Record:  536  of Set 1 | Mataura
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=mataura
    Processing Record:  537  of Set 1 | Porto Novo
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=porto novo
    Processing Record:  538  of Set 1 | Hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=hermanus
    Processing Record:  539  of Set 1 | Albany
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=albany
    Processing Record:  540  of Set 1 | Busselton
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=busselton
    Processing Record:  541  of Set 1 | Bluff
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=bluff
    Processing Record:  542  of Set 1 | Urusha
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=urusha
    Processing Record:  543  of Set 1 | Morshansk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=morshansk
    Processing Record:  544  of Set 1 | Saint George
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=saint george
    Processing Record:  545  of Set 1 | Ponta do Sol
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=ponta do sol
    Processing Record:  546  of Set 1 | Port Alfred
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=port alfred
    Processing Record:  547  of Set 1 | Usogorsk
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=usogorsk
    Processing Record:  548  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  549  of Set 1 | Rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=rikitea
    Processing Record:  550  of Set 1 | Witbank
    http://api.openweathermap.org/data/2.5/weather?appid=2e3cdabdb95992e0f95ee985c4c58629&units=imperial&q=witbank
    


```python
city_sample_df = city_sample_df.dropna(how='any')
```


```python
city_sample_df.count()
```




    index         550
    lat           550
    lng           550
    City          550
    Country       550
    Max Temp      550
    Humidity      550
    Wind Speed    550
    Cloudiness    550
    Date          550
    dtype: int64




```python
city_final_df = pd.DataFrame(city_sample_df["City"])
city_final_df['Cloudiness'] = city_sample_df['Cloudiness']
city_final_df['Country'] = city_sample_df[('Country')]
city_final_df['Date'] = city_sample_df['Date']
city_final_df['Humidity'] = city_sample_df['Humidity'].map("{:0.0f}".format)
city_final_df['Lat'] = city_sample_df['lat'].map("{:.2f}".format)
city_final_df['Lng'] = city_sample_df['lng'].map("{:.2f}".format)
city_final_df['Max Temp'] = city_sample_df['Max Temp'].map("{:.1f}".format)
city_final_df['Wind Speed'] = city_sample_df['Wind Speed']
```


```python
city_final_df.head()

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>City</th>
      <th>Cloudiness</th>
      <th>Country</th>
      <th>Date</th>
      <th>Humidity</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>divnomorskoye</td>
      <td>64.0</td>
      <td>RU</td>
      <td>1.514053e+09</td>
      <td>100</td>
      <td>43.23</td>
      <td>37.65</td>
      <td>34.0</td>
      <td>8.75</td>
    </tr>
    <tr>
      <th>1</th>
      <td>shelburne</td>
      <td>90.0</td>
      <td>CA</td>
      <td>1.514052e+09</td>
      <td>100</td>
      <td>40.86</td>
      <td>-64.23</td>
      <td>30.2</td>
      <td>9.37</td>
    </tr>
    <tr>
      <th>2</th>
      <td>butaritari</td>
      <td>88.0</td>
      <td>KI</td>
      <td>1.514053e+09</td>
      <td>100</td>
      <td>16.09</td>
      <td>179.33</td>
      <td>82.4</td>
      <td>18.43</td>
    </tr>
    <tr>
      <th>3</th>
      <td>merauke</td>
      <td>64.0</td>
      <td>ID</td>
      <td>1.514053e+09</td>
      <td>91</td>
      <td>-9.23</td>
      <td>138.73</td>
      <td>81.0</td>
      <td>8.03</td>
    </tr>
    <tr>
      <th>4</th>
      <td>broken hill</td>
      <td>24.0</td>
      <td>AU</td>
      <td>1.514053e+09</td>
      <td>32</td>
      <td>-27.82</td>
      <td>142.84</td>
      <td>67.8</td>
      <td>6.29</td>
    </tr>
  </tbody>
</table>
</div>




```python
city_final_df.to_csv("City_Weather.csv")
```


```python
current_date = datetime.now().strftime("%m-%d-%Y")
print('\033[1m' + "Latitude vs Temperature Plot")
plt.figure(figsize=(15,10))
plt.scatter(x=city_final_df['Lat'].apply(float).apply(int),
            y=city_final_df['Max Temp'].apply(float).apply(int),
            c="blue",
            marker ='o',
            edgecolors='black',
            alpha=0.75)
plt.title("City Latitude vs. Max Temperature (" + str(current_date) + ")")
plt.xlabel('Latitude')
plt.ylabel('Max Temperature(F)')
plt.yticks(np.arange(-100, 150, 50))
plt.xticks(np.arange(-80, 100, 20))
plt.grid(True)
sns.set_style('darkgrid')
plt.savefig("LatitudevsTemperature.png")
plt.show()
```

    [1mLatitude vs Temperature Plot
    


![png](output_14_1.png)



```python
print('\033[1m' + "Latitude vs. Humidity Plot")
plt.figure(figsize=(15,10))
plt.scatter(x=city_final_df['Lat'].apply(float).apply(int),
            y=city_final_df['Humidity'].apply(float).apply(int),
            c="blue",
            marker ='o',
            edgecolors='black',
            alpha=0.75)
plt.title("City Latitude vs. Humidity (" + str(current_date) + ")")
plt.xlabel('Latitude')
plt.ylabel('Humidity(%)')
plt.yticks(np.arange(-20, 120, 20))
plt.xticks(np.arange(-80, 120, 20))
plt.grid(True)
sns.set_style('darkgrid')
plt.savefig("LatitudevsHumidity.png")
plt.show()

```

    [1mLatitude vs. Humidity Plot
    


![png](output_15_1.png)



```python
print('\033[1m' + 'Latitude vs. Cloudiness Plot') 
plt.figure(figsize=(15,10))
plt.scatter(x=city_final_df['Lat'].apply(float).apply(int),
            y=city_final_df['Cloudiness'].apply(float).apply(int),
            c="blue",
            marker ='o',
            edgecolors='black',
            alpha=0.75)
plt.title("City Latitude vs. Cloudiness (" + str(current_date) + ")")
plt.xlabel('Latitude')
plt.ylabel('Cloudiness(%)')
plt.yticks(np.arange(-20, 120, 20))
plt.xticks(np.arange(-80, 120, 20))
plt.grid(True)
sns.set_style('darkgrid')
plt.savefig("LatitudevsCloudiness.png")
plt.show()
```

    [1mLatitude vs. Cloudiness Plot
    


![png](output_16_1.png)



```python
print('\033[1m' + 'Latitude vs. Wind Speed Plot') 
plt.figure(figsize=(15,10))
plt.scatter(x=city_final_df['Lat'].apply(float).apply(int),
            y=city_final_df['Wind Speed'].apply(float).apply(int),
            c="blue",
            marker ='o',
            edgecolors='black',
            alpha=0.75)
plt.title("City Latitude vs. Wind Speed (" + str(current_date) + ")")

plt.xlabel('Latitude')
plt.ylabel('Wind Speed(mph)')
plt.yticks(np.arange(-5, 50, 5))
plt.xticks(np.arange(-80, 120, 20))
plt.grid(True)
sns.set_style('darkgrid')
plt.savefig("LatitudevsWindSpeed.png")
plt.show()
```

    [1mLatitude vs. Wind Speed Plot
    


![png](output_17_1.png)

