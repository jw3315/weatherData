import requests
from pprint import pprint
import json
from pandas.io.json import json_normalize
import pandas as pd

## This is an example to pull 2001-2017 daily weather for 18 cities in Alabama state.
## Register an account on https://www.wunderground.com/weather/api, get your API key: "your_key"
## try api
#r = requests.get("http://api.wunderground.com/api/your_key/history_20180801/q/CA/San_Francisco.json")
#data = r.json()
#pprint (data['history']['dailysummary'])
## well, for wunderground api, it can be called 500 times per day, 10 times per min in free account.
## there's only daily pull for historical data in the api
## it means 365 calls for 1 year data for one airport address, not efficient!

## try using the json file from the back end, we would be able to get custom daily data directly
## weather stations you may be interested in : https://www.wunderground.com/about/faq/US_cities.asp
station = pd.read_csv("weatherStation.csv")
city = station.Station
stationid = station.ID

#url = ["https://api-ak.wunderground.com/api/your_key/history_2001010120171231/lang:EN/units:english/bestfct:1/v:2.0/q/" + stationid + ".json?showObs=0&ttl=120" for date in date]
#data = [requests.get(u).json() for u in url]
#df = [json_normalize(dat['history']['days']) for dat in data]
#df = [d.rename(columns=lambda x: x.replace('summary.','')) for d in df]
### after trying pulling 2001-2017 weather all together, it only responses 20010101-20020202, seems about one year most in one call

year = range(2001,2018)
date = [str(y)+'0101'+str(y)+'1231'for y in year]

### try each city each year, it's 18*17=306 calls
### not necessarily to stop because of the 10 call/min limit. With one year data pull, it doesn't run that fast.
def weather(date,stationid):
    url = "https://api-ak.wunderground.com/api/your_key/history_"+ date +"/lang:EN/units:english/bestfct:1/v:2.0/q/" + stationid + ".json?showObs=0&ttl=120"
    ## after trying pulling 2001-2017 weather all together, it only responses 20010101-20020202, seems about one year most
    ## try each city each year, it's 18*17=306 calls
    data = requests.get(url).json()   
    df = json_normalize(data['history']['days']).rename(columns=lambda x: x.replace('summary.',''))
    return df

def weather_city(stationid):
    return pd.concat([weather(dat,stationid) for dat in date])

df = [weather_city(stat) for stat in stationid]
    
writer = pd.ExcelWriter('weather.xlsx')
for i, d in enumerate(df):
    d.to_excel(writer,sheet_name="{0}".format(city[i]))
writer.save()
