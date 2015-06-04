# Meteo-data
R function to obtain Finnish Meterological Institute open data

#Description
This script downloads FMI open meteorological data. It is restricted to download 1 type of weather data (e.g. daily temperature) from a single weatherstation. It returns the data in a data.frame

# Usage
Place the text file in your working directory and use the command
get.met.data<-dget(“get_met_data.txt”)
Note that for the function to work, some further steps are needed as described below

# Required R packages
Package XML is needed. When it is not installed, run the command
install.packages("XML")

# FMI open data
 FMI site for Open data. http://en.ilmatieteenlaitos.fi/open-data-manual and related pages
 
# API key
Get you API key from FMI. The key is a code which you need to insert in the URL instead of the text where it reads INSERT-YOUR-KEY-HERE (below)

# URL with the data you want
The URL looks daunting, but is a fairly simple structure. For example, the following url
url.kajaani.85="http://data.fmi.fi/fmi-apikey/INSERT-YOUR-KEY-HERE/wfs?request=getFeature&storedquery_id=fmi::observations::weather::daily::timevaluepair&fmisid=101725&starttime=1985-01-01T12:00:00Z&endtime=1985-12-31T12:00:00Z&parameters=tday&"
is a request to download data on daily temperatures (“parameters=tday&”) from station in Kajaani (based on the FMI station ID, “fmisid=101725”), for the time period starting 1 January 1985 and ending 31.12.1985 (starttime and end time are given in the format year-month-day).

