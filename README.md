# Meteo-data
R function to obtain Finnish Meterological Institute open data

#Description
This script downloads FMI open meteorological data, which is in XML format and parses it to produce a data.frame with the requested information. It is restricted to download one type of weather data (e.g. daily temperature) from a single weatherstation. While this may seem restrictive, the function can be easily implemented to download multiple datasets and concatenate these.   

# Usage
Place the text file in your working directory and use the command
```R
get.met.data<-dget(“get_met_data.txt”)
```
Note that for the function to work, some further steps are needed as described below

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
is a request to download data on daily temperatures (“parameters=tday&”) from station in Kajaani (based on the FMI station ID, “fmisid=101725”), for the time period starting 1 January 1985 and ending 31.12.1985 (starttime and end time are given in the format year-month-day).
You can check the URL works by pasting it in your browser.
Apart from daily temperature, other daily weather parameters are daily rain "rrday", snow "snow", minimal temperature "tmin", maximal temperature "tmax".
Note further that FMI has lots of other data, some browsing on their site is needed to work this out.
For example, URL for wind-speed with 10 minute interval from Hanko during one day.
```wind.hanko<-"http://data.fmi.fi/fmi-apikey/INSERT-YOUR-KEY-HERE/wfs?request=getFeature&storedquery_id=fmi::observations::weather::timevaluepair&fmisid=100917&maxlocations=1&starttime=2013-04-23T00:00:00Z&endtime=2013-04-25T23:50:00Z&parameters=windspeedms&
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

