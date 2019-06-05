
Fire prevention systems using IoT
-------------------------------------------------

### Preface

This problem starts with the education of populace.
Prevention of forest fires is first and foremost a human problem, technology is augmentation to humans,
which is a tool and nothing more.
So, like all the problems the best start is to educate.
Education campaign is bigger issue since most of the population is not familiar with how and why fires start
and how they propagate.
There are many campaigns that are proven and tried and are a great starting point(Smoky the bear).
Simply put: educate then augment.

This solution has to have 2 components. A sensing and alert part for professionals, and alert and information
component for population.
As such:
* Using cheap off the shelf components for sensors.
* System for coordinating and supervising the staff and monitoring the sensors(GIS) C&C.
* Alert system that predicts possible fires and areas of fires.
* Civilian alert and advisor system.

### 1.Where to place the sensors?
As always good starting point with any analytical question is to look at the available data.
GIS is a natural choice as a starting point since a lot of the information is of geospatial nature.
National forest organisation has a very good information about the national forests and woods.
It is available to all interested parties and is wealth of information.
Using geospatial algorithms and techniques we can quickly visualize and present some of the questions 
that we are interested in.

Using information aquired from national forest agency(HR Šume) in Croatia we can obtain the geospatial information about the location and clasification of national forests in country.
This information contains the areas, designtation and fire hazard information for particular areas.
We can visualize that information for better understanding of the data and further analysis.


```python
m
```


We are using a small sample data for a particular area in a "Brodsko-posavska" county, specifically the Podcrkavlje region.
As we can see the varition in the hazard areas is diverse and somewhat clustered in different areas. Since the areas are classified and have a designation this could be a basis for our own simple naming convention and help with the firefighting and locating the problematic areas.

Using the information in this dataset fir fire hazard information, we can use the geospatial algorithms to create list of points that could be potential locations for our sensors. The algorithm takes in the account the hazard level of the area, minimum distances between the points and the size of the area that contains the points. The number of points vary as the fire hazard level is higher.


```python
m1
```





We then used the k-means clustering algorithm to cluster the points in a groups that are more managable and equally distant from each other. That provides better grouping for LoRaWAN nodes so we can position them in areas that are closest and find a optimal location for the WAN nodes that will be a relay for our sensors.

Since this is a small area that we chose as a sample, we can see the algorithm generated substantial number of possible location for sensor nodes(383 points), and are randomly generated based on the input paramaters.

This is a expensive and very hard to justify as a possible solution with this set of information that we used so far, but a great starting point for further data layers that we add.



And, so we continue our gathering of information for a more clear picture. We obtained the weather information from:
>European Centre for Medium-Range Weather Forecasts, https://www.ecmwf.int/en/forecasts/datasets

Speciffically a monthly means information for wind direction for our sample location, and incorporated it in our little geospatial analysis.


```python
m2
```







We chose the information for 3 month period at the height of fire season, this data can provide us with optimal placement of particle gathering (2.5pp or similar) and CO2 and similar sensors. From this information we can optimize and or build triangulation data for the placement and direction of gathering sensors that will provide best possible starting point for sensing.


```python
m3
```







We can use this dataset we are using remote sensing data for analysis of potential fire hotspots and further refine the positioning of sensors.
As we can see from this dataset our sample location is not very representative in this case. But this provides us with historical data that can guide us in our mission and help us determine most hazardus locations and areas that are in need of monitoring.

MODIS Active fires

The MODIS sensor, on board the TERRA and ACQUA satellites, identifies areas on the ground that are distinctly hotter than their surroundings and flags them as active fires. The difference in temperature between the areas that are actively burning with respect to neighbouring areas allows active fires to be identified and mapped. The spatial resolution of the active fire detection pixel from MODIS is 1 km.
Additional information on the MODIS active fire product is available at https://earthdata.nasa.gov/what-is-new-collection-6-modis-active-fire-data

VIIRS Active fires

The VIIRS (Visible Infrared Imaging Radiomer Suite) on board the NASA/NOAA Suomi National Polar-orbiting Partnership (SNPP) uses similar algorithms to those used by MODIS to detect active fires. The VIIRS active fire products complements the MODIS active fire detection and provides an improved spatial resolution, as compared to MODIS. The spatial resolution of the active fire detection pixel for VIIRS is 375 m. Additionally, VIIRS is able to detect smaller fires and can help delinate perimeters of ongoing large fires.
Additional information on VIIRS active fire products can be found at https://earthdata.nasa.gov/earth-observation-data/near-real-time/firms/viirs-i-band-active-fire-data

The mapping of active fires is performed to provide a synoptic view of current fires in Europe and as a means to help the subsequent mapping of burnt fire perimeters. Information on active fires is normally updated 6 times daily and made available in EFFIS within 2-3 hours of the acquisition of the MODIS/VIIRS images.

When interpreting the hotspots displayed in the map, the following must be considered:

    Hotspot location on the map is only accurate within the spatial accuracy of the sensor
    Some fires may be small or obscured by smoke or cloud and remain undetected
    The satellites also detect other heat sources (not all hotspots are fires)

To minimize false alarms and filter out active fires not qualified as wildfires (e.g. agricultural burnings), the system only displays a filtered subset of the hotspots detected by FIRMS. To this end a knowledge based algorithm is applied that takes into account the extent of surrounding land cover categories, the distance to urban areas and artificial surfaces, the confidence level of the hotspot.

With the identify feature tool, key information attached to each active fire is provided, such as geographic coordinates, administrative district (commune and province) and the main land cover category affected.

> source:
    http://effis.jrc.ec.europa.eu/about-effis/technical-background/active-fire-detection/
    



```python

```

forecast:
    https://www.ecmwf.int/en/forecasts/datasets


```python
import folium

m = folium.Map(location=[45.23, 18], zoom_start=13, tiles='Openstreetmap')



folium.raster_layers.WmsTileLayer(
    url='http://173.82.94.104:8080/geoserver/IOT/wms?',
    layers='IOT:Opasnost_od_pozara',
    name='Fire hazard',
    fmt='image/png',
    overlay=True,
    control=True,
    transparent=True,
).add_to(m)
legend_html =   '''
                <div style="position: fixed; 
                            bottom: 50px; left: 50px; width: 100px; height: 150px; 
                            background: white;
                            border:2px solid black; z-index:9999; font-size:14px;
                            ">&nbsp; Fire hazard<br>
                            &nbsp; <img src="http://173.82.94.104:8080/geoserver/IOT/wms?REQUEST=GetLegendGraphic&VERSION=1.0.0&FORMAT=image/png&WIDTH=20&HEIGHT=20&LAYER=IOT:Opasnost_od_pozara">
                              
                </div>
                ''' 

m.get_root().html.add_child(folium.Element(legend_html))

folium.LayerControl(position="bottomright",collapsed=False).add_to(m)
```




    <folium.map.LayerControl at 0x2acbd09da20>




```python
m1 = folium.Map(location=[45.23, 18], zoom_start=13, tiles='Openstreetmap')



folium.raster_layers.WmsTileLayer(
    url='http://173.82.94.104:8080/geoserver/IOT/wms?',
    layers='IOT:Opasnost_od_pozara',
    name='Fire hazard',
    fmt='image/png',
    overlay=True,
    control=True,
    transparent=True,
).add_to(m1)
folium.raster_layers.WmsTileLayer(
    url='http://173.82.94.104:8080/geoserver/IOT/wms?',
    layers='IOT:Sensori',
    name='Sensor location',
    fmt='image/png',
    overlay=True,
    control=True,
    transparent=True,
).add_to(m1)
legend_html =   '''
                <div style="position: fixed; 
                            bottom: 50px; left: 50px; width: 150px; height: 50px; 
                            background: white;
                            border:2px solid black; z-index:9999; font-size:14px;
                            ">&nbsp; Sensor locations <br>
                             &nbsp; &nbsp;Sensors <i class="fa fa-circle" style="color:black"></i>
                              
                </div>
                ''' 

m1.get_root().html.add_child(folium.Element(legend_html))

folium.LayerControl(position="bottomright",collapsed=False).add_to(m1)
```




    <folium.map.LayerControl at 0x2acbad42cf8>




```python
m2 = folium.Map(location=[45.23, 18], zoom_start=13, tiles='Openstreetmap')



folium.raster_layers.WmsTileLayer(
    url='http://173.82.94.104:8080/geoserver/IOT/wms?',
    layers='IOT:Opasnost_od_pozara',
    name='Fire danger',
    fmt='image/png',
    overlay=True,
    control=True,
    transparent=True,
).add_to(m2)
folium.raster_layers.WmsTileLayer(
    url='http://173.82.94.104:8080/geoserver/IOT/wms?',
    layers='IOT:vjetar_lipanj',
    name='Wind direction June/2014',
    fmt='image/png',
    overlay=True,
    control=True,
    transparent=True,
).add_to(m2)
folium.raster_layers.WmsTileLayer(
    url='http://173.82.94.104:8080/geoserver/IOT/wms?',
    layers='IOT:vjetar_srpanj',
    name='Wind direction July/2014',
    fmt='image/png',
    overlay=True,
    control=True,
    transparent=True,
).add_to(m2)
folium.raster_layers.WmsTileLayer(
    url='http://173.82.94.104:8080/geoserver/IOT/wms?',
    layers='IOT:vjetar_kolovoz',
    name='Wind direction August/2014',
    fmt='image/png',
    overlay=True,
    control=True,
    transparent=True,
).add_to(m2)
legend_html =   '''
                <div style="position: fixed; 
                            bottom: 50px; left: 50px; width: 130px; height: 65px; 
                            background: white;
                            border:2px solid black; z-index:9999; font-size:14px;
                            ">&nbsp; Wind direction <br>
                            &nbsp;&nbsp;<img src="http://173.82.94.104:8080/geoserver/IOT/wms?REQUEST=GetLegendGraphic&VERSION=1.0.0&FORMAT=image/png&WIDTH=60&HEIGHT=40&LAYER=IOT:vjetar_kolovoz">
                              
                </div>
                ''' 

m2.get_root().html.add_child(folium.Element(legend_html))

folium.LayerControl(position="bottomright",collapsed=False).add_to(m2)
```




    <folium.map.LayerControl at 0x2acbdb655c0>




```python
import folium

m3 = folium.Map(location=[45.23, 18], zoom_start=11, tiles='Openstreetmap')




folium.raster_layers.WmsTileLayer(
    url='http://173.82.94.104:8080/geoserver/IOT/wms?',
    layers='IOT:fire_heatmap',
    name='VIIRS Active fires',
    fmt='image/png',
    overlay=True,
    control=True,
    transparent=True,
).add_to(m3)

folium.raster_layers.WmsTileLayer(
    url='http://173.82.94.104:8080/geoserver/IOT/wms?',
    layers='IOT:Opasnost_od_pozara',
    name='Fire hazard',
    fmt='image/png',
    overlay=True,
    control=True,
    transparent=True,
).add_to(m3)

folium.raster_layers.WmsTileLayer(
    url='http://173.82.94.104:8080/geoserver/IOT/wms?',
    layers='IOT:fire_archive',
    name='VIIRS Active fires/2014',
    fmt='image/png',
    overlay=True,
    control=True,
    transparent=True,
).add_to(m3)


legend_html =   '''
                <div style="position: fixed; 
                            bottom: 50px; left: 50px; width: 150px; height: 55px; 
                            background: white;
                            border:2px solid black; z-index:9999; font-size:14px;
                            ">&nbsp; VIIRS Active fires <br>
                            &nbsp; for fireseason 2014.
                           
                              
                </div>
                ''' 

m3.get_root().html.add_child(folium.Element(legend_html))

folium.LayerControl(position="bottomright",collapsed=False).add_to(m3)
```




    <folium.map.LayerControl at 0x2acbdb659b0>




```python

```


```python

```
