from bs4 import BeautifulSoup 
import requests 
import re
import pandas as pd 
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

headers= {'user-agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36'}


# To find all the states name 
close= requests.get('https://restaurants.kfc.co.in/location/madhya-pradesh', headers= headers).text
like = BeautifulSoup( close , 'html.parser')
states= []
for x in like.find( 'select',  id="OutletState" ).find_all('option'):
    states.append(x.text)
states = states[1:]
new_states=[]
for x  in states:
    new_states.append(x.replace(" ", "-"))


# Creating the data set  of stores 
details= []
session = requests.Session()
retry = Retry(connect=3, backoff_factor=0.5)
adapter = HTTPAdapter(max_retries=retry)
session.mount('http://', adapter)

for states in new_states:
    gates= True
    close= session.get("https://restaurants.kfc.co.in/location/" + states , headers =headers).text
    notclose= BeautifulSoup( close , 'html.parser')
    lati= list(notclose.find_all('input',class_="outlet-latitude"))
    longi=  list(notclose.find_all('input',class_="outlet-longitude"))
    if notclose.find('li', class_ ='page-item last' ) == None:
        gates= False
    else:
        last_link= notclose.find('li', class_ ='page-item last' ).find('a')['href']
    
    for x,y  in enumerate(notclose.find_all("div", class_= "store-info-box")):
        details.append(re.split(r"Open|Opens| \+| Call", re.sub(' +', ' ', y.text)) + [states] + 
                 [re.search('value="(.*)"', str(lati[x])).group(1), 
                  re.search('value="(.*)"',str(longi[x])).group(1) ])
    while(gates):
        link= notclose.find('li', class_ ='next' ).find('a', href= True)['href']
        close= session.get("https://restaurants.kfc.co.in" + link , headers =headers).text
        notclose= BeautifulSoup( close , 'html.parser')
        lati= list(notclose.find_all('input',class_="outlet-latitude"))
        longi=  list(notclose.find_all('input',class_="outlet-longitude"))

        for x,y  in enumerate(notclose.find_all("div", class_= "store-info-box")):
            details.append(re.split(r"Open|Opens| \+| Call", re.sub(' +', ' ', y.text)) + [states] +
                      [re.search('value="(.*)"', str(lati[x])).group(1), 
                       re.search('value="(.*)"',str(longi[x])).group(1) ])
        

        if link == last_link:
            gates = False
        
KFC_STORES  =   pd.DataFrame(details ,  columns =['Address', 'Phone Number','Timings','Not_Required', 'State', 
                                            'Latitude', 'Longitude']) 
                                            
# preparing the data set     

KFC_STORES.loc[KFC_STORES[KFC_STORES['Longitude'].isna()].index, 'Timings':] = KFC_STORES.loc[KFC_STORES[KFC_STORES['Longitude'].isna()].index, 'Timings':].shift(periods=1,axis=1)
KFC_STORES['Timings']=KFC_STORES['Timings'].str.replace("at " , "")
KFC_STORES['Timings']=KFC_STORES['Timings'].str.replace("until " , "")
KFC_STORES['Phone Number']= "+" + KFC_STORES['Phone Number']
KFC_STORES=KFC_STORES.drop('Not_Required', axis=1)
replace_v= KFC_STORES.loc[KFC_STORES[KFC_STORES['Timings'].isna()].index, 'Phone Number'].str.split(" ", n=1 , expand =True).rename(columns= {0 : "Phone Number", 1: 'Timings'} )['Timings']        
KFC_STORES.loc[KFC_STORES[KFC_STORES['Timings'].isna()].index,'Timings'] = replace_v
KFC_STORES['Phone Number']=KFC_STORES['Phone Number'].str.split(" " , n=1, expand= True ).drop(1 , axis=1)


# final data set to be saved as CSV file

KFC_STORES.to_csv("file_name.csv")
                                            
                                            
                                            
                                            
                                            
                                            
                                            
                                            
                                            
                                           
