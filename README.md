# covid-19-insightsimport numpy as np
import pandas as pd
​
#for getting world map
import folium
​
# read_html() method returns all the tables in the webpage (Internally beautifulSoup is executed)
​
# Retreiving Latitude and Longitude coordinates
info = pdread_html("http://www.quickgs.com/latitudinal-and-longitudinal-extents-of-india-indian-states-and-cities/") 
​
#convering the table data into DataFrame
coordinates = pd.DataFrame(info[0])
​
coordinates.head()
​
# Retreving the LIVE COVID19 Stats from Wikipedia
coronastats = pdread_html('https://psindia.org/covid-19/cases',match='State/UT')
​
#Convert to DataFrame
covid19 = pd.DataFrame(coronastats[0])
covid19.head()
​
# Data cleaning,Transforming Latitude and Longitude into algorithm compatible form
# Removing degree symbol in lattitudes and longitudes and changing their data type
def data_pre(cord):
    return cord[0:5]
coordinates['Latitude']  = coordinates['Latitude'].apply(data_pre).astype('float')
coordinates['Longitude'] = coordinates['Longitude'].apply(data_pre).astype('float')
coordinates.head()
​
# cleaning Covid19 Dataframe
​
# Removing unnecessary rows at the tail and Unnecessary columns
#covid19 = covid19.iloc[:-3,:-4]
​
# Renaming Attribute Names for simplicity
covid19.columns = ['Sr.No.','State','Confirmed','Active','Recoveries','Deaths']
covid19.head()
​
#Merging the tables covid19 and coordinates 
final_data = pd.merge(coordinates, covid19, how ='inner', on ='State')
final_data.head()
final_data.Active= final_data.Active.astype(str)
final_data.Confirmed= final_data.Confirmed.astype(str)
final_data.Recoveries= final_data.Recoveries.astype(str)
final_data.Deaths= final_data.Deaths.astype(str)
final_data.State= final_data.State.astype(str)
# retreiving the data from final table and plotting it on the INDIA map
# creating the map object zooming on INDIA, location here shows the lat and long of INDIA
india = folium.Map(location = [20.5937,78.9629],zoom_start=4.5)
#adding to map
​
for state,lat,long,confirmed_cases,active_cases,recoveries,deaths in zip(list(final_data['State']),list(final_data['Latitude']),list(final_data['Longitude']),list(final_data['Confirmed']),list(final_data['Active']),list(final_data['Recoveries']),list(final_data['Deaths'])):
    
    #for creating circle marker
    folium.CircleMarker(location = [lat,long],
                       radius = 5,
                       color='red',
                       fill = True,
                       fill_color="red").add_to(india)
    #for creating marker
    folium.Marker(location = [lat,long],
                  popup="<strong><font size=3px>State:"+state+"</font></strong><br>"+"<strong>Comfirmed cases: "+confirmed_cases+"</strong><br>"+"<strong>Active cases: "+active_cases+"</strong><br>"+"<strong><font color=green>Recoveries: "+recoveries+"</font></strong><br>"+"<strong><font color=red>Deaths: "+deaths+"</font></strong>").add_to(india)
    
#to show the map
india

