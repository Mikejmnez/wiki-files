    <?xml version="1.0"?>

    <!-- For the packageId attribute I took the URL to the described resource
         and appended .eml -->
    <!--  -->
    <!-- xsi:schemaLocation="eml://ecoinformatics.org/eml-2.0.1/eml.xsd eml/eml.xsd" -->
    <eml:eml xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="eml://ecoinformatics.org/eml-2.0.1 file:/home/jimg/REAP_cataloging/eml-2.0.1/eml.xsd"
         xmlns:eml="eml://ecoinformatics.org/eml-2.0.1"
         xmlns:res="eml://ecoinformatics.org/resource-2.0.1"
         xmlns:doc="eml://ecoinformatics.org/documentation-2.0.1"
         xmlns:xs="http://www.w3.org/2001/XMLSchema"
         xmlns:cit="eml://ecoinformatics.org/literature-2.0.1"
         xmlns:prot="eml://ecoinformatics.org/protocol-2.0.1"
         xmlns:stmml="http://www.xml-cml.org/schema/stmml"
         xmlns:sw="eml://ecoinformatics.org/software-2.0.1"
         xmlns:ds="eml://ecoinformatics.org/dataset-2.0.1"
         packageId="http://www.marine.csiro.au/dods/nph-dods/dods-data/ocean_colour/czcs/global/8_day/netcdf/czcs_1978_8D_chlor.nc.eml"
         system="DAP2.0">

         <!-- This is an EML document describing a DAP data source used for the
         REAP SST use-case. The data source URL is:

         http://www.marine.csiro.au/dods/nph-dods/dods-data/ocean_colour/czcs/
             global/8_day/netcdf/ -->

         <!-- required. Here's a picture of what goes in this element:
         http://knb.ecoinformatics.org/software/eml/eml-2.0.1/eml-dataset.png -->

         <dataset>

              <!-- required. From the DAP Global attribute 'Title' -->
              <title>CZCS global 8 day mean Chlorophyll a concentration</title>

              <!-- optional. Searched by Kepler if present -->
              <!-- abstract>
                   <para></para>
              </abstract -->

              <!-- required -->
              <creator>
                   <organizationName>Australian Commonwealth Scientific and Research Organization
                        (CSIRO)</organizationName>
              </creator>

              <!-- optional but important for search functionality -->
              <keywordSet>
                   <!-- required; one of an ORed set of elements -->
                   <keyword>remote sensing</keyword>
                   <keyword>imagery</keyword>
                   <keyword>spatial raster</keyword>
                   <keyword>earth observation</keyword>
                   <keyword>satellite data</keyword>

                   <keyword>CZCS</keyword>
                   <keyword>Chlorophyll</keyword>
              </keywordSet>

              <!-- required -->
              <contact>
                   <organizationName>Australian Commonwealth Scientific and Research Organization
                        (CSIRO)</organizationName>
              </contact>

              <spatialRaster>
                   <!-- I made this the pathname of the file on the server -->
                   <entityName>/ocean_colour/czcs/global/8_day/netcdf/czcs_1978_8D_chlor.nc</entityName>

                   <physical>
                        <!-- Change this to teh DAP variable to access??? -->
                        <objectName>chlor</objectName>
                        <dataFormat>
                             <externallyDefinedFormat>
                                  <formatName>DAP</formatName>
                             </externallyDefinedFormat>
                        </dataFormat>
                        <distribution>
                             <online>
                                  <url>http://www.marine.csiro.au/dods/nph-dods/dods-data/ocean_colour/czcs/global/8_day/netcdf/czcs_1978_8D_chlor.nc</url>
                             </online>
                        </distribution>
                   </physical>

                   <!-- It would seem like this is needed, or at least it did at
                        one point while I was writing this, but it's not according
                        to the schema. jhrg 1/6/09 -->
                   <coverage>
                        <geographicCoverage>
                             <geographicDescription/>
                             <boundingCoordinates>
                                  <westBoundingCoordinate>-179.95833333333334</westBoundingCoordinate>
                                  <eastBoundingCoordinate>179.95833333333331</eastBoundingCoordinate>
                                  <northBoundingCoordinate>89.958333333333329</northBoundingCoordinate>
                                  <southBoundingCoordinate>-89.958333333333329</southBoundingCoordinate>
                             </boundingCoordinates>
                        </geographicCoverage>
                        <temporalCoverage>
                             <singleDateTime>
                                  <calendarDate>2001-05-21</calendarDate>
                             </singleDateTime>
                        </temporalCoverage>
                   </coverage>

                   <attributeList>
                        <!-- I think an attribute in EML is roughly a variable in
                             DAP -->
                        <attribute>
                             <attributeName>chlor</attributeName>
                             <attributeDefinition>chlor is level of chlorophyll a in mg m^-3 as measured
                                  by the satellite-borne CZCS sensor</attributeDefinition>
                             <measurementScale>
                                  <ratio>
                                       <unit>
                                            <standardUnit>milligramsPerCubicMeter</standardUnit>
                                       </unit>
                                       <numericDomain>
                                            <numberType>real</numberType>
                                       </numericDomain>
                                  </ratio>
                             </measurementScale>
                        </attribute>
                   </attributeList>

                   <spatialReference>
                        <horizCoordSysDef name="Sphere_Equidistant_Cylindrical">
                             <geogCoordSys>
                                  <datum/>
                                  <spheroid/>
                                  <primeMeridian longitude="0"/>
                                  <unit name="degree"/>
                             </geogCoordSys>
                        </horizCoordSysDef>
                        <!-- horizCoordSysName>Sphere_Equidistant_Cylindrical</horizCoordSysName -->
                   </spatialReference>

                   <horizontalAccuracy>
                        <accuracyReport>These data have very little variation in the horzontal
                             dimensionality between pixels.</accuracyReport>
                   </horizontalAccuracy>
                   <verticalAccuracy>
                        <accuracyReport>These data have very little variation in the vertical
                             dimensionality between pixels.</accuracyReport>
                   </verticalAccuracy>
                   <!-- I used the name of a know coordinate system so the units
                   come from that and are Meters -->

                   <cellSizeXDirection>0.083333333333333329</cellSizeXDirection>
                   <cellSizeYDirection>0.083333333333333329</cellSizeYDirection>

                   <numberOfBands>1</numberOfBands>
                   <rasterOrigin>Upper Left</rasterOrigin>
                   <rows>2160</rows>
                   <columns>4320</columns>
                   <verticals>1</verticals>
                   <cellGeometry>pixel</cellGeometry>

              </spatialRaster>

         </dataset>

    </eml:eml>