# Meteo-data
R function to obtain Finnish Meteorological Institute (FMI) open data

#Description
This script downloads FMI open meteorological data, which is in XML format and parses it to produce a data.frame with the requested information. It is restricted to download one type of weather data (e.g. daily temperature) from a single weatherstation. While this may seem restrictive, the function can be easily implemented to download multiple datasets and concatenate these.   

# Usage
```
get.met.data(url.name)
```
# Arguments
url.name - string with the name of the URL with FMI data. Must include valid API key 

# Use
Simply copy the function. Alternatively, place the text file in your working directory and use the command
```R
get.met.data<-dget(“get_met_data.txt”)
```

# Note
For the function to work, some further steps are needed as described below

# Required R packages
Package XML needs to be installed.

# FMI open data
 FMI site for Open data. http://en.ilmatieteenlaitos.fi/open-data-manual and related pages
 The function requires that a station is specified, which you find from http://en.ilmatieteenlaitos.fi/observation-stations?p_p_id=stationlistingportlet_WAR_fmiwwwweatherportlets&p_p_lifecycle=0&p_p_state=normal&p_p_mode=view&p_p_col_id=column-4&p_p_col_count=1&_stationlistingportlet_WAR_fmiwwwweatherportlets_stationGroup=WEATHER
 It works best if you use the FMI Station ID (FIMSID), which can be found from this site.
 
# API key
Get you API key from FMI. The key is a code which you need to insert in the URL instead of the text where it reads INSERT-YOUR-KEY-HERE (below)

# URL with the data you want
The URL looks daunting, but is a fairly simple structure. For example, the following url
```
url.kajaani.85="http://data.fmi.fi/fmi-apikey/INSERT-YOUR-KEY-HERE/wfs?request=getFeature&storedquery_id=fmi::observations::weather::daily::timevaluepair&fmisid=101725&starttime=1985-01-01T12:00:00Z&endtime=1985-12-31T12:00:00Z&parameters=tday&"
```
is a request for data on daily temperatures (“parameters=tday&”) from station in Kajaani (based on the FMI station ID, “fmisid=101725”), for the time period starting 1 January 1985 and ending 31.12.1985 (starttime and end time are given in the format year-month-day).

You should check that the URL works by pasting it in your browser.
Apart from daily temperature, other daily weather parameters are daily rain "rrday", snow "snow", minimal temperature "tmin", maximal temperature "tmax".
Note further that FMI has lots of other data, some browsing on their site is needed to work this out.
For example, URL for wind-speed with 10 minute interval from Hanko during one day.
```
wind.hanko<-"http://data.fmi.fi/fmi-apikey/INSERT-YOUR-KEY-HERE/wfs?request=getFeature&storedquery_id=fmi::observations::weather::timevaluepair&fmisid=100917&maxlocations=1&starttime=2013-04-23T00:00:00Z&endtime=2013-04-25T23:50:00Z&parameters=windspeedms&
```

# Example 1

Extract data on daily temperature and rain from a single station (Tvärminne)
```
get.met.data<-dget(“get_met_data.txt”)
url.tvarminne.temp="http://data.fmi.fi/fmi-apikey/INSERT-YOUR-KEY-HERE/wfs?request=getFeature&storedquery_id=fmi::observations::weather::daily::timevaluepair&fmisid=100953&starttime=2013-04-01T12:00:00Z&endtime=2013-12-31T12:00:00Z&parameters=tday&"
temp.tvarminne<-get_met_data(url.tvarminne.temp)
url.tvarminne.rain="http://data.fmi.fi/fmi-apikey/INSERT-YOUR-KEY-HERE/wfs?request=getFeature&storedquery_id=fmi::observations::weather::daily::timevaluepair&fmisid=100953&starttime=2013-04-01T12:00:00Z&endtime=2013-12-31T12:00:00Z&parameters=rrday&"
rain.tvarminne<-get_met_data(url.tvarminne.rain)
```

# Example 2 : downloading data on multiple stations from multiple days
```
# FMISID
# Hanko Tulludden: 100946: 
# Kimito: 100951
# Jomala: 100917
require(XML)
id.vec<-c(100946,100951,100917)
start.time.string=c("2010-01-01T00:00:00Z","2010-01-02T00:00:00Z")
end.time.string=c("2010-01-01T23:50:00Z","2010-01-02T23:50:00Z")
met.data<-list()
for (i in 1:length(id.vec)) {
	for (y in 1:length(start.time.string)) {
		# create URL address
		url.addr<-
		paste("http://data.fmi.fi/fmi-apikey/INSERT-YOUR-KEY-HERE/wfs?request=getFeature&storedquery_id=fmi::observations::weather::timevaluepair&fmisid="
		, id.vec[i]
		, "&maxlocations=1&starttime="
		, start.time.string[y]
		, "&endtime="
		, end.time.string[y]
		,"&parameters=windspeedms&"
		, sep="")
		#adds the temperature data to the list
		mdat<-get.met.data(url.addr) #data 
		#daily average added to data stored
		met.data<-rbind(met.data,c(id.vec[i],colMeans(mdat)) ) 
	} #for (y
} #for (id
met.data.frame<-data.frame(met.data)
names(met.data.frame)[1]<-"FMI.station.ID"
names(met.data.frame)[5]<-"windspeed"
head(met.data.frame)
```
