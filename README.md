# KFC_stores
Data of all kfc stores in india
Webscaraped the Kfc india website using the beautiful library  
used string formating method to scrape the tag ( chosse only one tag "store-info") 
other way was to take each tag for address, phone , timing then scrape it


Diffculties faced were on the longitude and lattitude info as it was hidden,
Used string formating here also, other way to use geopy library but the results were not accurate for some data. 

Have to clean the data as mostly it was in string format, so used the pandas extensively.

