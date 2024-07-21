# METAR-2-CSV
Convert summertime METAR data into CSV format

### How to use these conversion scripts
0. These are Jedit macros.  you'll need Jedit, and an understanding how to manually install Jedit Macros.  Depending on a lot of things this changes from one setup to another, so install jedit, and read the help files with it to understand macros.

         * https://jedit.org/index.php?page=download

1. Get the data: for METAR copy the result of this URL into the clipboard and paste it into jEdit. Note this URL lists weather station IDs and number of hours.. You can customize those, but 4x168hours is about the max number you can request before the government starts cutting you off. 

         * https://aviationweather.gov/cgi-bin/data/metar.php?ids=KFTW,KDFW,KGPM,KGKY&hours=168&order=id%2C-obs&sep=true

2. If you're tracking longer term data or collecting multiple weather stations, In jedit sort the data with no duplication to eliminate double copies and remove blank lines.  After you do this, you need to remove the blank line at the start of the buffer. 

         * Jedit -> plugins -> TextTools -> sort ... -> [delete identic lines] --> [OK]
         * Jedit (remove the leading blank line that sort may have left.

3. Run the METAR_2_CSV script

         * Jedit -> Macros -> METAR 2 CSV 

4. Select all in Jedit and copy to clipboard. 
5. Open LibreOffice Calc (or Excel, or Google sheets, or possibly other spreadsheets.) Paste the data in, choosing tab separated fields, and if needed 'formulas are in the data.'
I'm encoding formula into tab separated values though, so no promise it works on other (Numbers) programs.  
6. Many columns only have data in row 1.  you'll need to copy-down that column to fill it in. copy row one, and paste it into the rest of the column. 

### Online Data Sources

Website | Provides | Link
--------|----------|------
Aviation Weather | METARs for the last 190ish hours | https://aviationweather.gov
NCEI | METARs since 1901 | https://www.ncei.noaa.gov/pub/data/noaa/
NCEI | Index of stations | [find station ID by call letters](https://www.ncei.noaa.gov/pub/data/noaa/isd-history.csv)
EPA | Pollution Data | https://aqs.epa.gov/aqsweb/airdata/download_files.html
NREL | Solar/Wind/Temp/Pressure/Humidity/Clouds Model Data | https://nsrdb.nrel.gov/data-viewer

### Android Data Sources

App | Provides | Link
----|----------|------
Device Info HW | Sensor information | https://play.google.com/store/apps/details?id=ru.andr7e.deviceinfohw
Physics Toolbox | Realtime tricorder and rapid traces | https://play.google.com/store/apps/details?id=com.chrystianvieyra.physicstoolboxsuite
Physics Toolbox Pro | same as above but supporting them | https://play.google.com/store/apps/details?id=net.vieyrasoftware.physicstoolboxsuitepro
Sensor Logger | Sensor information and long term logs | https://play.google.com/store/apps/details?id=com.kelvin.sensorapp
GPS Logger | Logs GPS Data and outputs KML and GPX without paid account | https://play.google.com/store/apps/details?id=eu.basicairdata.graziano.gpslogger
Govee Home | What I'm using for Air temp and humidity | https://play.google.com/store/search?q=Govee%20Home&c=apps

#### Notes on Apps: 

I'm only focused on Android apps because while in use as a data logger, each device becomes a purpose only device (nothing else runs in the background and no screen swapping.)  Android phones last, and they are priced at levels you can have an extra dozen or so devices laying around (I have 20ish, but I'm on the edge of usual, I know.) I've never run into anyone with more than a couple extra IOS devices and usually those are severely limited in functionality (broken screen, battery life in minutes, been off the appstore for a decade.)  That said **Physics Toolbox** is available on IOS, and will provide all the functionality needed (Except logs get huge fast, since you can't slow them down. Leave it to rocket scientists to decide that 20 points per second is the slowest anyone would ever want to log data... ) I asked for lower frequency logging and received a "it's hard" reply, so there's hope they will allow for smaller files. 

* **Device Info HW** is based in Russia.  I've been using it on everything for about a decade... but times being what they are, use at your own risk. But it provides more about the device than any other app I know, in a straightforward set of tables.  
* **Physics Toolbox** is geared toward education market, but it really transforms my low to mid range androids into lab quality tricorders.  
* **Sensor Logger** is the primary companion to the Bluetooth Hygrometers I'm using. it allows recording all the sensors (but some sensors like tracking WIFI that I'm not interested in require a monthly account.) 
* **GPS Logger** only logs GPS, but exports in KML and GPX which then imports into analyzers (Google Earth) without any excel magic. 
* **Govee Home** provides Temp and humidity. But it's designed on an unsustainable model, requiring me to upload the data to a Govee server which then emails it back to me. (with no paid account, and the sensors were $5 - $10 each with free shipping.) But it's doing its job and a single set of triple A batteries has been powering the first one I bought since April and still reports full battery. So while exporting the log via Govee will probably die at some point, a display temp humidity for rooms will still be useful and replaceable batteries puts end of life at a decade or more. Point is, there are a lot of bluetooth hygrometers on offer.  This one is an _almost_, but probably isn't a long term best buy. 

### Rough Project Plan

I spend a lot of time outside. Being outside in Texas summer in DFW can put you into immediate-risk-to-your-life situations, especially working from an unairconditioned poorly ventilated vehicle. So all this data aggregation is working toward a math equation that tells me when to walk away rather than die. And it's important that it's data driven, because when you've been in the heat for hours, your ability to make that decision on your own gets impaired. 

Because of this, the METAR 2 CSV is only currently designed to handle codes that appear in summertime METAR. I haven't coded in any ice or snow codes... they will appear as remarks and be excluded from the precip number and light factor. At this point, the purpose is to predict and prevent heat related injuries. 

Things to do: 
1. record the environmental conditions and what's causing them.  I record temp, humidity, pressure, sunlight, I also need airflow locally, but that's not feasible for this project, is it? _(note to self to look for wearable anenometers...)_
  * Temp/humidity in my 3 foot surroundings.
  * Calorie burn rate (steps, and some factors about how much weight I'm moving.)
  * Temp/humidity/pressure/light/
2. Develop models that use local calibrated and public data to get to this same information without or as well as the locally recorded data.  
  * Temp : METAR
  * Humidity : METAR
  * Pressure : METAR (with altitude data from NCEI)
  * Wind : METAR
  * Sunlight : convert sky conditions into percent loss and use model data from NREL
  * Pollution : EPA 
3. Set Limits.
   * Right now, the limit is Temp (F) + Humidity (%) - 2x wind (mph) < 170 is my working model, but it doesn't include sunlight or shade yet. And there are conditions where temp is low (85-95) and the humidity is high (85-95) where its uncomfortable, but not life threatening. So the model needs work. And instead of just a number threshhold, I want it to be a minutes outdoors or a percent away from imminent danger. 
4. Test alternate situations. (probably a 2025 or 26 issue.) That number above (temp + humidity - wind) should translate to anywhere.  

## About METAR data 
 Document | Site 
----------|------
Federal Meteorological Handbook | https://www.icams-portal.gov/resources/ofcm/fmh/FMH1/fmh1_2019.pdf
ASOS User Manual | https://www.weather.gov/media/asos/aum-toc.pdf
METAR Decode Key | https://www.weather.gov/media/wrh/mesowest/metar_decode_key.pdf
ISH Technical Report | https://www.ncei.noaa.gov/pub/data/noaa/ish-tech-report.pdf
ISH File format field List | https://www.github.com
ISD Data Documenation | https://www.ncei.noaa.gov/data/global-hourly/doc/isd-format-document.pdf

## Mikey's Dark Sky Visualizations and percent irradiation drop
 METAR                        | Visualization   | Qty | Meaning              
------------------------------|-----------------|-----|--------------------- 
 FEW[234]/d{2}                | \`              | 0.1 | Light Cirrus Clouds
 SCT[234]/d{2}                | \`\`            | 0.2 | Scattered Cirrus Clouds
 BKN[234]/d{2}                | \`\`\`\`        | 0.4 | Partly Sunny Cirrus Clouds
 OVC[234]/d{2}                | \`\`\`\`\`\`    | 0.6 | Overcast Cirrus Clouds
 FEW([0][6-9]/d\|[1][0-9]{2}) | '               | 0.1 | Light Alto Clouds          
 SCT([0][6-9]/d\|[1][0-9]{2}) | '''             | 0.3 | Scattered Alto Clouds    
 BKN([0][6-9]/d\|[1][0-9]{2}) | ''''''          | 0.6 | Partly Sunny Alto Clouds   
 OVC([0][6-9]/d\|[1][0-9]{2}) | '''''''''       | 0.9 | Overcast Alto Clouds       
 FEW([0][1-5]/d               | .               | 0.1 | Light Tropo Clouds   
 SCT([0][1-5]/d               | ....            | 0.4 | Scattered Tropo Clouds  
 BKN([0][1-5]/d               | .......         | 0.7 | Partly Sunny Tropo Clouds
 OVC([0][1-5]/d               | ..........      | 1   | Overcast Tropo Clouds      
 P00000                       | ;               | 0.1 | Trace Precip         
 P00[1-9]                     | ;;;             | 0.3 | Less than .1" precip 
 P0[1-9]\\d                   | ;;;;;;;;        | 0.8 | Less than 1" precip  
 P[1-9]\\d                    | ;;;;;;;;;;;;;;; | 1.5 | More than 1" precip  
 10SM                         |                 | 0   | Clear                
 [5-9]SM                      | _               | 0.1 | Smog level           
 [2-4]SM                      | __              | 0.2 | Haze Level           
 [/1]\\d{0,1}SM               | _____           | 0.5 | Fog Level     

### Mikey's Pressure Trend visualizations and OCD alphabetizing attempts
 **Figure Code** | **Alpha Code** | **Visual** | **Meaning** | **Term**  | **Change** | **Description (FCM-H1-2019)**                                                      
-----------------|----------------|------------|-------------|-----------|------------|----------------------------------------------------------------------------------- 
 PC0             | V              | /⁀\\       | Peak        | Vertex    | +          | Increasing, then decreasing.                                                       
 PC1             | T              | /⁀⁀        | Plateau     | Table     | +          | Increasing, then steady, or increasing then increasing more slowly.                
 PC2             | R              | ‿/⁀        | Rise        | **Rise**  | +          | Increasing steadily or unsteadily.                                                 
 PC3             | M              | ‿‿/        | Ramp        | Ramp      | +          | Decreasing or steady, then increasing; or increasing then increasing more rapidly. 
 PC4             | L              | \~~~       | Level       | **Level** | +          | Steady.                                                                            
 PC8             | K              | ⁀⁀\\       | Cliff       | Cliff     | \-         | Steady or increasing, then decreasing; or decreasing then decreasing more rapidly. 
 PC7             | F              | ⁀\\‿       | Fall        | **Fall**  | \-         | Decreasing steadily or unsteadily.                                                 
 PC6             | D              | \\‿‿       | Down        | Down      | \-         | Decreasing then steady; or decreasing then decreasing more slowly.                 
 PC5             | B              | \\‿/       | Dip         | Bottom    | \-         | Decreasing, then increasing.                                                            

### METAR.csv Format 
Column| Name                  | Units                        | Column Head     
---- | ---------------------- | ---------------------------- | ----------------
A    | Local Time             | YYYY-MM-DD HH:MM             | Local-Time      
B    | UTC                    | YYYY-MM-DD HH:MM             | UTC-Time        
C    | UTC Year               | YYYY                         | UTC-YYYY        
D    | UTC Month              | MM                           | UTC-MM          
E    | UTC Day                | DD                           | UTC-DD          
F    | UTC Hour               | HH                           | UTC-HH          
G    | Input Time             | Text                         | UTC-HHMM        
H    | Location Name          |                              | Location        
I    | Latitude               | (deg) N of Equator           | GPS-Lat                      
J    | Longitude              | (deg) E of London            | GPS-Lng                      
K    | Accuracy               | (m)                          | GPS-Acc                      
L    | Altitude               | (m) above sea level          | GPS-Alt                      
M    | Speed                  | (m/s)                        | GPS-Speed                    
N    | Bearing                | (deg) E of North             | GPS-Bearing                  
O    | Temp                   | C                            | Temp-Current-(C)             
P    | Temp                   | F                            | Temp-Current-(F)             
Q    | Daily Max              | C                            | Temp-Max-(C)                 
R    | Daily Min              | C                            | Temp-Min-(C)                 
S    | Dewpoint               | C                            | Dewpoint-(C)                 
T    | Dewpoint               | F                            | Dewpoint-(F)                 
U    | Humidity               | (pct)                        | Humidity-(pct)               
V    | Heat Index             | F                            | Heat-Index-(F)               
W    | Heat Warning           | _Warning number_             | Heat-Warning                 
X    | Measured Pressure      | (hPa)                        | Air-Pressure-Measured-(hPa)  
Y    | Alitmeter              | (InHg)                       | Air-Pressure-Altimeter-(inHg)
Z    | Sea Level              | (hPa)                        | Air-Pressure-Sea-Level-(hPa) 
AA   | 3 hr Trend             | (Mikey's Meaning)            | Air-Pressure-Trend           
AB   | 3 hr Change            | (hPa)                        | Air-Pressure-Change-(hPa)    
AC   | Wind Direction         | (Deg)                        | Wind-Direction-(deg)         
AD   | Wind Speed             | (Kts)                        | Wind-Speed-(Kts)             
AE   | Wind Gusts             | (Kts)                        | Wind-Gusts-(Kts)             
AF   | Solar Angle            | Deg from Zenith              | Light-Solar-Angle-(deg)      
AG   | Solar Azimuth          | Deg E of N                   | Light-Solar-Azimuth-(deg)    
AH   | Clearsky DHI           | W/m2                         | Light-Clearsky-DHI-(W/m2)    
AI   | Clearsky DNI           | W/m2                         | Light-Clearsky-DNI-(W/m2)    
AJ   | Clearsky GHI           | W/m2                         | Light-Clearsky-GHI-(W/m2)    
AK   | Light Measured Energy  | (lux)                        | Light-Measured-(lux)         
AL   | Light Measured Angle   | Deg from Zenith              | Light-Measured-Angle-(deg)   
AM   | Light Measured Azimuth | Deg E of N                   | Light-Measured-Azimuth-(deg) 
AN   | Precipitation Hourly   | (in)                         | Precip-Hourly-(in)           
AP   | Precipitation Daily    | (in)                         | Precip-Daily-(in)            
AQ   | Dark Sky Filter        | (pct... Mikey's guess)       | Sky-Dark-Sky-Filter-(pct)              
AR   | Dark Sky List          | (Mikey's Visualization)      | Sky-Dark-Sky-List                    
AS   | Visibility             | (mi)                         | Sky-Visibility-(mi)              
AT-AU| Flags                  |                              | Flag-....                    
AV   | Remarks                | cloud heights and leftovers  | Remarks                      
AW   | empty                  | constant data follows        | required break to avoid sorting constants
AX   | Local Time Zone Offset |                              | Local-Time-Zone-Offset       
AY   | IDX Station ID         | METAR Station ID             | IDX-Location-ID
BC   | IDX Station Name       | Text                         | IDX-Location-Name
BA   | IDX Lat                | Deg N of Equator             | IDX-Location-Latitude-(deg)
BB   | IDX Longitude          | Deg E of London              | IDX-Location-Longitude-(deg)
AZ   | IDX Elevation          | (m)                          | IDX-Location-Elevation-(m)

### About NREL Solar Data 

Placeholder link to source data: https://research-hub.nrel.gov/en/publications/?originalSearch=Solar
(more specific information when I actually pick a model and data source)

### About EPA Pollution Data 

Placeholder link to source data: https://www.epa.gov/outdoor-air-quality-data/air-data-basic-information
(more specific information when I actually pick a model and data source)
