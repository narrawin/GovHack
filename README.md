**GIS Analysis Process Undertaken in the Creation of the Pilot Innovative Land Index (ILI) Model as part of GOVHACK 2019.**

The goal of the ILI was to create a raster layer dataset which maps the relative importance of agricultural land from the perspective of soil characteristics generally beneficial to agricultural yield, rainfall abundance and distance to the closest major regional centre (for the purposes of calculating minimum food miles). The opensource GIS desktop program QGIS 3.8 (IOS) was used for this process outlined. Justification of the choice of soil and other spatial attributes can be found outlined at our website at [www.ili.best](http://www.ili.best). The purpose of the ILI model at this stage is to demonstrate a proof on concept of how soil and other data could better guide future land-use planning. It is envisaged that this model would be further expanded to include a range of other important soil (and other) variables guided by experts in the fields of agriculture, climate (including future predictions), soil science, ecology and urban planning including a multidisciplinary peer-review process to agree upon the relative contribution of the different variables that make up the underlying equation upon which the ILI is generated. Under no circumstances should the current ILI be used to make any planning decisions.

**Defining the Study Area**

THE VIC AG API is only available as a service to call up soil attributes for a coordinate points. To transform this into useable spatial maps we defined a study area:

The study area was defined as area approximately 12200km2 around Ballarat. This area was chosen because:

1. Our team is from Ballarat
2. The area encompassed a diverse range of soil types, and current agricultural land uses.
3. Ballarat is one the major urban areas in Victoria and thus draws on a food from surrounding region.
4. Ballarat is currently forecast as one of the fastest growing urban centres in Victoria with increasing urban sprawl into farmland areas.

![Figure 1](https://github.com/DanielFerguson/GovHack/readme_resources/fig-1.png)

Figure 1: The study area defined (marked with red boundary)

#### A requirement of the GovHack challenge we entered was the use of the Agriculture Victoria Soils API ([https://developer.vic.gov.au/index.php?option=com\_apiportal&amp;view=apitester&amp;usage=api&amp;tab=tests&amp;apiName=Agriculture%20Victoria%20Soils%20API&amp;apiId=208f8e40-aba4-4fd3-b61b-af461170cee9&amp;menuId=187](https://developer.vic.gov.au/index.php?option=com_apiportal&amp;view=apitester&amp;usage=api&amp;tab=tests&amp;apiName=Agriculture%20Victoria%20Soils%20API&amp;apiId=208f8e40-aba4-4fd3-b61b-af461170cee9&amp;menuId=187))

#### This was only available as a query on a given GSP coordinate with the CRS 4326, rather than being provided as a WMS/WFS or any other raster format. The creation of the ILI model required raster datasets therefore we had to generate this ourselves from the API. Note we understand that a limitation of our model has therefore been created by the format of this available dataset as in the process of creating a raster from what was probably originally a raster datasets the true resolution and accuracy of the datasets have been downgraded. In an ideal world we would have used the raw dataset itself as a WMS/WFS/GeoTIFF to ensure the highest resolution and accuracy of the modelling.

#### To create raster format digital soil map layers of the soil attributes provided by the Agriculture Victoria Soils API (AgVIC Soils API) we generated a grid of points (as a shapefile) over the study area at 1km intervals with a CRS of 4326 and appended to their attribute table their x and y coordinates. Using the scripts provided elsewhere in the GitHub documentation for this project we then queried the AgVIC Soils API for each of the gps points in the grid all the soil attributes. This effectively attached to the point grid shapefile the various soil attributes.

####
![Figure 2](https://github.com/DanielFerguson/GovHack/readme_resources/fig-2.png)

Figure 2: The 1km grid generated in QGIS for the creation of digital soil maps from the AgVIC Soils API data

Digital soil maps were generated using IDW interpolation (IDW tool in QGIS). This used a search radius of 100 and a cell size of 1000m with no distance weighting.

This generated Raster Layers for the AgVIC API for the following soil attributes that we chose as relevant for this study:
![Figure 3](https://github.com/DanielFerguson/GovHack/readme_resources/fig-3.png)

Figure 3: Digital Soil Map Created of Organic Carbon (top)

![Figure 4](https://github.com/DanielFerguson/GovHack/readme_resources/fig-4.png)
Figure 4 Digital Soil Map Created of Electrical Conductivity (Top)

For the pH attribute based on a review of the soil literature in relation to agriculture1 we determined that a pH of 6.5 was considered generally ideal for most agricultural farming land uses. Therefore or the pH (top) dataset we calculated the variance of the values from the ideal 6.5 at each datapoint in the grid. Eg. the pH ideal variance value for a location with a pH of 7.5 was calculated as 1 as would a location with a pH of 5.5. Using the same IDW method of interpolation used for the other soil attributes a raster of pH value variance from ideal was created.

![Figure 5](https://github.com/DanielFerguson/GovHack/readme_resources/fig-5.png)
Figure 5: Digital Soil map of Variance of pH from 6.5 pH ideal

Additional rasters were created by simply clipping the available raster layers to the study area.

These datasets were average annual rainfall (BOM data [http://www.bom.gov.au/jsp/ncc/climate\_averages/rainfall/index.jsp?period=an&amp;area=vc#maps](http://www.bom.gov.au/jsp/ncc/climate_averages/rainfall/index.jsp?period=an&amp;area=vc#maps) )

And Available Water Capacity (CSIRO/TERN [https://data.csiro.au/collections/#collection/CIcsiro:11016v4](https://data.csiro.au/collections/#collection/CIcsiro:11016v4))

The final raster layer created to contribute to the ILI model was the distance of any location to the major urban centres of Victoria (Ballarat, Melbourne, Bendigo and Geelong). To create this layer the official locations of these urban centres was extracted from the FOI Locality open dataset ([http://services.land.vic.gov.au/catalogue/metadata?anzlicId=ANZVI0803003600&amp;publicId=guest&amp;extractionProviderId=1](http://services.land.vic.gov.au/catalogue/metadata?anzlicId=ANZVI0803003600&amp;publicId=guest&amp;extractionProviderId=1)
)

The distance of each point in our 1km grid was calculated to the nearest urban centre point using the Distance to Nearest Hub tool in QGIS. The distance value generated and appended to the attribute table of the 1km grid was then interpolated using the same IDW tool to create a &#39;food miles&#39; raster.

![Figure 6](https://github.com/DanielFerguson/GovHack/readme_resources/fig-6.png)
Figure 6: Distance to Nearest Urban Centre Raster (Minimum Food Miles)

The ILI combines the rasters created of all these variables:

- --Variance of pH from the idea
- --Organic Carbon
- --Electrical Conductivity
- --Available Water Capacity
- --Annual Average Rainfall
- --Distance from Nearest Urban Centre

Because each of these attribute datasets are measured in different units in order to not give undue weighting to those that measure things in larger numbers than smaller numbers it was necessary to create normalised versions of each dataset. This was done using the normalise raster tool in QGIS for each dataset.

The final ILI model was created by using the raster calculator tool in QGIS using the following equation:

ILI = (normalised carbon + normalised available water capacity + normalised rainfall) – (normalised electrical conductivity + normalised distance layer)

This equation was used to give equal weighting of each of the variables. This may not be ideal – some variables may be more important than others. However determining this would require collaboration from domain experts (soil science, agriculture, climate scientist, urban planners etc) and a peer review process to determine.

The first 3 variables (carbon, available water capacity and rainfall) were given positive weighting in the equation as they are variables that are generally (based on the literature) associated with benefits for agricultural yield. Electrical conductivity (associated with salinity levels) and the distance variable were given negative weighting as increasing values negatively influence a location both in terms of potential agricultural yield and increasing food miles to major urban centres.

The following is the ILI layer created in QGIS:

![Figure 7](https://github.com/DanielFerguson/GovHack/readme_resources/fig-7.png)
Figure 7: The Innovative Land Index Created for this Project. Values that are towards the blue green spectrum rank higher than those towards the red end of the spectrum. Higher values are locations considered of greater importance for agriculture both in terms of soil and climate properties and their relative distance to urban centres for food distribution.

Additional analysis involved overlaying the Planning zone layer from Victoria

[http://services.land.vic.gov.au/catalogue/metadata?anzlicId=ANZVI0803002909&amp;publicId=guest&amp;extractionProviderId=1](http://services.land.vic.gov.au/catalogue/metadata?anzlicId=ANZVI0803002909&amp;publicId=guest&amp;extractionProviderId=1)

and reclassifying the more detailed planning zones into the categories:

Agriculture Zones, Rural Living Zones, Conservation Zones and Built Environment (which represented everything else and includes all urban areas, roads etc).

This was overlayed over the ILI in our mapping portal [www.ili.best](http://www.ili.best) to demonstrate to urban planners, farmers and the public how current landuse zoning relates to the ranking of farm land. This considers how farm land may have been subdivided into smaller parcels in the rural living zone which generally makes them no longer available for profitable food production as well the valuableness of the  land surrounding the major urban centre of Ballarat for food production which could better direct urban planning to target less important areas for agricultural production.

#
[https://www.dpi.nsw.gov.au/\_\_data/assets/pdf\_file/0003/167187/soil-ph.pdf](https://www.dpi.nsw.gov.au/__data/assets/pdf_file/0003/167187/soil-ph.pdf)