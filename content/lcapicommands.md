+++
title = "LCAPI Commands"
weight = 200
+++

The following commands can be used to control layers from the application. Note that command names are _not_ case-sensitive, and that the [general parameters](#general-parameters) can be included along with any other command.

| Command |  Description |
| :-- | :-- |
| [**activate**](#activate) | Highlights the selected layer in the layer manager. |
| **[delete](#delete)** | Specifies that a layer should be permanently deleted. |
| [**getprop**](#getprop) | Used to retrieve a value of a single layer property. Remarks include the [Table of Properties](#table-of-properties). |
| [**getprops**](#getprops) | Used to retrieve all the properties for a specified layer. Includes lists for [Spreadsheet](#spreadsheet), [3d Model,](#3d-model) [Shapefile](#shapefile) and [ImageSet](#imageset). |
| **[group](#group)** | Specifies that a layer group should be added. |
| [**layerlist**](#layerlist) | Returns the structure of the layers and layer group names (in an XML document format) that are currently in the layer manager. |
| [**load**](#load) | Specifies a data file, and and some optional parameters, to apply to a new layer. |
| [**mode**](#mode) | Changes the view to one of **Earth**, **Planet**, **Sky**, **Panorama**, **SolarSystem**. |
| **[move](#move)** | Changes the view depending on the supplied parameter. |
| **[new](#new)** | Specifies that a new layer should be created. |
| [**setprop**](#setprop)| Used to specify a value of a single layer property. |
| [**setprops**](#setprops)| Used to specify multiple properties for a layer.|
| [**state**](#state) | Requests some details of the current view.|
| [**uisettings**](#uisettings) | Used to change user interface settings, without altering the layer data. |
| **[update](#update)** | Specifies that the data attached to this command should be added to the layer. |
| [**version**](#version) | Returns the version number of the running version of the LCAPI. |
| [general parameters](#general-parameters) | Parameters that can be applied to any of the commands. |


{% method -%}
### activate

The **activate** layer command will highlight the selected layer in the layer manager.

#### Remarks

| Required Parameters | Description |
| :-- | :-- |
| **&id** | Specifies the id number of the layer. |

{% sample lang="html" -%}
#### Return Value
The following string will be included in the response if the call is successful:

```html
<Status>Success</Status>
```

If the call is not successful the following string may be included in the response, or other errors may be returned:

```html
<Status>Error - Invalid layer ID</Status>
```
{% endmethod %}

{% method -%}
### delete

The **delete** command specifies that a layer should be permanently deleted.

#### Remarks

All sub-components of the layer will also be deleted.

| Required Parameters | Description |
| :-- | :-- |
| **&id** | Specifies the id number of the layer. |

{% sample lang="html" -%}
#### Return Value
The following string will be included in the response if the call is successful:

```html
<Status>Success</Status>
```

If the call is not successful the following string may be included in the response, or other errors may be returned:

```html
<Status>Error - Invalid layer ID</Status>
```

#### Example Code

```js
//
// Send a DELETE command
//
WebClient client = new WebClient();
    string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=delete&id={1}", getIP().ToString(), layerId);
    string response = client.UploadString(url, "");
    XmlDocument doc = new XmlDocument();
    doc.LoadXml(response);
    XmlNode node = doc["LayerApi"];
    string s = node.InnerText;
    //
    // Handle an error situation
    //
    if (s.Contains("Error"))
    {
        throw new Exception(s);
    }
```
{% endmethod %}

{% method -%}
### getprop

The **getprop** command is used to retrieve a value of a single layer property.

#### Remarks

| Required Parameters | Description |
| :-- | :-- |
| **&id** | Specifies the id number of the layer. |
| **&propname** | Property name, one from the table below. |


{% sample lang="html" -%}
#### Return Value

If the call is successful the response will contain the following string:
```html
<Status>Success</Status><Layer _propertyName_="_propertyValue_"</Layer></LayerApi>
```
Where _propertyName_ is the property name requested, and _propertyValue_ is the value returned. For example:

##### Get properties

```html
<LayerApi><Status>Success</Status><Layer AltType="Depth"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer AltColumn="3"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer Astronomical="False"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer BeginRange="1/9/2009 11:44:38 AM"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer EndRange="9/9/2009 11:13:52 PM"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer Decay="16"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer Name="EarthQuakes2009"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer FadeSpan="00:00:00"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer MarkerIndex="-1"</Layer></LayerApi>
```
If the call is not successful the following string may be included in the response, or other errors may be returned:

```html
<Status>Error - Invalid parameter</Status>
```

#### Example Code

**Get altitude units**

```cs
WebClient client = new WebClient();
string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=getprop&id={1}&propname={2}", getIP().ToString(), layerId, "AltUnit");
```

**response**

```xml
<?xml version="1.0" encoding="utf-8"?><LayerApi><Status>Success</Status><Layer AltUnit="Kilometers"</Layer></LayerApi>
```
{% endmethod %}

#### Table of Properties

The following table lists the properties that can be get or set on a layer (note that the property names and Enum values are case-sensitive). Also refer to the Notes below the table.

| Property | Type | Description | Layer Type | Default |
| :-- | :-- | :-- | :-- | :-- |
| **AltColumn** | Int | Column number containing the altitude/depth data. | Spreadsheet | -1 |
| **AltType** | Enum | One of: **Depth, Altitude, SeaLevel, Terrain**. | Spreadsheet | SeaLevel |
| **AltUnit** | Enum | One of: **Meters, Feet, Inches, Miles, Kilometers, AstronomicalUnits, LightYears, Parsecs, MegaParsecs.** | Spreadsheet | Meters for altitude and kilometers for depth. |
| **Astronomical** | Boolean| True if the data is astronomical rather than terrestrial. If this is set to true the declination will be by default the Latitude column, and the right ascension the Longitude column. | All | False |
| **BeginRange** | DateTime | Date and time of the first data entry in a time series, in one the formats (month/day/year): <br> "1/1/2010 11:00:00 PM" <br> "1/1/2010 11:30 AM" <br>  "1/1/2010 11 am" <br> "1/1/2000" <br> "1/2000" | Spreadsheet | Maximum system value |
| **CartesianCustomScale** | Double | Used to divide the Cartesian co-ordinate values to a custom scale. If this value was set to "0.01" and the scale to Meters, the custom scale would be "centimeters". | Spreadsheet | 1 |
| **CartesianScale** | Enum | If the **CoordinatesType** is not **Spherical** then the co-ordinate system uses X,Y and Z values. One of: **Meters, Feet, Inches, Miles, Kilometers, AstronomicalUnits, LightYears, Parsecs, MegaParsecs, Custom**. | Spreadsheet | Meters |
| **ColorMap** | Enum | One of: **Same_For_All, Group_by_Values.** | Spreadsheet | Same_For_All |
| **ColorMapColumn** | Int | Column number containing the color map data. Columns are numbered from zero, -1 indicates there is no data for this. | Spreadsheet | -1 |
| **ColorValue** | String | String containing ARGB value of the color, in the format: "ARGBColor:255:255:255:255". | All | White |
| **CoordinatesType** | Enum | One of: **Spherical, Rectangular, Orbital**. **Spherical** applies when the reference frame is a sphere, and the coordinates will be Lat/Lng or RA/Dec. **Rectangular** applies if there are X,Y and Z coordinates. 0,0,0 in this case is the center of the reference frame. | Spreadsheet | Spherical |
| **Decay** | Float | Decay rate of the visualization, in days, in the range 0.00025 to 4096. | Spreadsheet | 16 |
| **Enabled** | Boolean | True if the layer is enabled. | All | True |
| **EndDateColumn** | Int | Column containing the end date/time data. | Spreadsheet | -1 |
| **EndRange** | DateTime | Date and time of the last data entry in a time series, in one the formats (month/day/year): <br> "1/1/2010 11:00:00 PM" <br> "1/1/2010 11:30 AM" <br>  "1/1/2010 11 am" <br> "1/1/2000" <br> "1/2000"| Spreadsheet | Minimum system value |
| **EndTime** | DateTime | Date and time to end the visualizations, and return to the start time in the case of an auto loop.  This property is visible in the Lifetime dialog for a layer. Format (month/day/year): <br> "1/1/2010 11:00:00 PM" <br> "1/1/2010 11:30 AM" <br>  "1/1/2010 11 am" <br> "1/1/2000" <br> "1/2000"| All | Maximum system time. |
| **GeometryColumn** | Int | Column containing the geometry data. | Spreadsheet | -1 |
| **Heading** | Double | Heading angle  of the model in radians. | 3D Model | 0 |
| **HyperlinkColumn** | Int | Column containing the hyperlink data. | Spreadsheet | -1 |
| **HyperlinkFormat** | String | Spreadsheet | "" |
| **FadeSpan** | Time | The time it takes to fade out the graphic. This property is visible in the **Lifetime** dialog for the layer and is shown in days in the dialog. When using the SDK use the format D.HH:MM:SS, so for one day use **1.00:00:00**. | All | 0 |
| **FadeType** | Enum | One of: **In, Out, Both, None**. | All | None |
| **FlipV** | Boolean | True if the textures for the 3D model should be flipped. | 3D Model | False |
| **LatColumn** | Int | Column number for the Latitude data. | Spreadsheet | -1 |
| **LngColumn** | Int | Column number for the Longitude data. | Spreadsheet | -1 |
| **MarkerColumn** | Int | Column number for the marker data. | Spreadsheet | -1 |
| **MarkerIndex** | Int | Index number of marker graphic selected, or -1 if no marker has been selected. | Spreadsheet| -1 |
| **MarkerMix** | Enum | One of: **Same_For_All, Group_by_Values.** | Spreadsheet | Same_For_All |
| **MarkerScale** | Enum | One of: **Screen, World**. If this is set to **Screen** the graphic will not change in size as the viewer zooms in and out. If it is set to **World** the graphic (**Pushpin** or **Gaussian**) will scale with the view. | Spreadsheet | World |
| **Name** | String | Name of the layer. | All | "New layer" |
| **NameColumn** | Int | Column containing the event name data. | Spreadsheet | -1 |
| **Opacity** | Float | Opacity of the layer graphics from 0.0 to 1.0. | All | 1 |
| **Pitch** | Double | Pitch angle of the model in radians. | 3d Model | 0 |
| **PlotType** | Enum | One of: **Gaussian, Point, PushPin**. | Spreadsheet | Gaussian |
| **PointScaleType** | Enum | One of: **Linear**, **Power, Log, Constant, StellarMagnitude.** | Spreadsheet | Power |
| **RaUnits** | Enum | When the longitude column is being used for RA in astronomical data. One of: **Hours**, **Degrees**. | Spreadsheet | Hours |
| **Roll** | Double | Roll angle of the model in radians. | 3D Model | 0 |
| **Scale** | Vector3d | Scale of the model in x, y and z dimensions. Format: 1,1,1 | 3D Model | 1,1,1|
| **ScaleFactor** | Float | Scale factor to apply to the magnitude of the event. | Spreadsheet | 1 |
| **ShowFarSide** | Boolean | True if the data points on the invisible side of the body should be shown. | Spreadsheet | False |
| **SizeColumn** | Int | Column containing the size (magnitude) data. | Spreadsheet | -1 |
| **Smooth** | Boolean | True if the 3D Model should have its normals smoothed. | 3D Model | True |
| **StartDateColumn** | Int | Column containing the start date/time data. | Spreadsheet | -1 |
| **StartTime** | DateTime | Date and time to start the visualizations. This property is visible in the Lifetime dialog for the layer. Formats (month/day/year): <br> "1/1/2010 11:00:00 PM" <br> "1/1/2010 11:30 AM" <br>  "1/1/2010 11 am" <br> "1/1/2000" <br> "1/2000"| All | Minimum system time |
| **TimeSeries** | Boolean | True if the layer should be treated as time series data. | Spreadsheet | False |
| **Translate** | Vector3d | Translation (movement offset) of the model in x, y and z dimensions, in units of the model size. Format: 1,1,1 | 3D Model | 0,0,0 |
| **XAxisColumn** | Int | Column number for X data. | Spreadsheet | -1 |
| **YAxisColumn** | Int | Column number for Y data. | Spreadsheet | -1 |
| **ZAxisColumn** | Int | Column number for Z data. | Spreadsheet | -1 |
| **XAxisReverse** | Boolean | True if the direction of the X axis is reversed. | Spreadsheet | False |
| **YAxisReverse** | Boolean | True if the direction of the Y axis is reversed. | Spreadsheet | False |
| **ZAxisReverse** | Boolean | True if the direction of the Z axis is reversed. | Spreadsheet | False |

#### Notes

*   Columns are numbered from zero. A value of -1 for any column entry indicates that there is no data of that type.
*   AM and PM in times: The time 1/1/2000 12:00:00 AM is equivalent to midnight on the previous night - so is one second after 12/31/1999 23:59:59\. If the time was set to PM rather than AM then 12:00:00 references noon.


{% method -%}
### getprops

The **getprops** command is used to retrieve all the properties for a specified layer.

#### Remarks

Refer to the [table of properties](#table-of-properties) listed for the [**getprop**](#getprop) command.

| Required Parameters | Description |
| :-- | :-- |
| **&id** | Specifies the id number of the layer. |
{% sample lang="html" -%}
#### Return Value
If the call is successful the response string will contain the following string:

```html
<Status>Success</Status>
```

If the call is not successful the following string may be included in the response, or other errors may be returned:

```html
<Status>Error - Invalid layer ID</Status>
``` 

#### Example Code

The following example gets all the properties from any of the different types of layer. Note that the response string does not include newlines, these have been added for readability.

**get all properties**
```cs
 private void buttonGetProperties()
 {
    try
    {
        WebClient client = new WebClient();
        string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=getprops&id={1}", getIP().ToString(), layerId);
        string response = client.UploadString(url, "");
        XmlDocument doc = new XmlDocument();

        doc.LoadXml(response);
        XmlNode node = doc["LayerApi"];
        if (node.InnerText.Contains("Success"))
        {
            XmlNode node2 = node["Layer"];
            if (node2 != null)
            {
                string propName;
                string propValue;

                for (int i = 0; i < node2.Attributes.Count; i++)
                {
                    propName = node2.Attributes[i].Name;
                    propValue = node2.Attributes[i].Value;
                    // Do something with the name/value pair
                }
            }
            else
            {
                // No properties returned
            }
        } 
        else
        {
           throw new Exception(response);
        }
    }
    catch (Exception ex)
    {
        // Handle exception
    }
}
```


##### Spreadsheet
```xml
<?xml version='1.0' encoding='UTF-8'?>
<LayerApi>
<Status>Success</Status>
<Layer 
   Class=\"SpreadSheetLayer\" 
   TimeSeries=\"True\" 
   BeginRange=\"1/9/2009 11:44:38 AM\" 
   EndRange=\"9/9/2009 11:13:52 PM\" 
   Decay=\"16\" 
   CoordinatesType=\"Spherical\" 
   LatColumn=\"1\" 
   LngColumn=\"2\" 
   GeometryColumn=\"-1\" 
   XAxisColumn=\"-1\" 
   YAxisColumn=\"-1\" 
   ZAxisColumn=\"-1\" 
   XAxisReverse=\"False\" 
   YAxisReverse=\"False\" 
   ZAxisReverse=\"False\" 
   AltType=\"Depth\" 
   RaUnits=\"Hours\" 
   MarkerMix=\"Same_For_All\" 
   MarkerColumn=\"-1\" 
   ColorMapColumn=\"-1\" 
   PlotType=\"Gaussian\" 
   MarkerIndex=\"-1\" 
   ShowFarSide=\"False\" 
   MarkerScale=\"World\" 
   AltUnit=\"Kilometers\" 
   CartesianScale=\"Meters\" 
   CartesianCustomScale=\"1\" 
   AltColumn=\"3\" 
   StartDateColumn=\"0\" 
   EndDateColumn=\"-1\" 
   SizeColumn=\"4\" 
   NameColumn=\"0\" 
   HyperlinkFormat=\"\" 
   HyperlinkColumn=\"-1\" 
   ScaleFactor=\"1\" 
   PointScaleType=\"Power\" 
   Opacity=\"1\" 
   StartTime=\"1/1/0001 12:00:00 AM\" 
   EndTime=\"12/31/9999 11:59:59 PM\" 
   FadeSpan=\"00:00:00\" 
   FadeType=\"None\" 
   Name=\"EarthQuakes2009\" 
   ColorValue=\"ARGBColor:255:255:0:0\" 
   Enabled=\"True\" 
   Astronomical=\"False\" />
</LayerApi>
```

##### 3D Model

```xml
<?xml version='1.0' encoding='UTF-8'?>
<LayerApi>  
<Status>Success</Status>  
<Layer 
   Class=\"Object3dLayer\" 
   FlipV=\"False\" 
   Smooth=\"True\" 
   Heading=\"0\" 
   Pitch=\"0\" 
   Roll=\"0\" 
   Scale=\"1, 1, 1\" 
   Translate=\"0, 0, 0\" 
   Opacity=\"1\" 
   StartTime=\"1/1/0001 12:00:00 AM\" 
   EndTime=\"12/31/9999 11:59:59 PM\" 
   FadeSpan=\"00:00:00\" 
   FadeType=\"None\" 
   Name=\"aurora\" 
   ColorValue=\"ARGBColor:255:255:0:0\" 
   Enabled=\"True\" 
   Astronomical=\"False\" />
</LayerApi>
```

##### Shapefile

```xml
<?xml version='1.0' encoding='UTF-8'?>
<LayerApi>  
<Status>Success</Status>  
<Layer 
   Class=\"ShapeFileRenderer\" 
   Opacity=\"1\" 
   StartTime=\"1/1/0001 12:00:00 AM\" 
   EndTime=\"12/31/9999 11:59:59 PM\" 
   FadeSpan=\"00:00:00\" 
   FadeType=\"None\" 
   Name=\"state_bounds\" 
   ColorValue=\"ARGBColor:255:255:0:0\" 
   Enabled=\"True\" 
   Astronomical=\"False\" />
</LayerApi>
```

##### ImageSet

```xml
<?xml version='1.0' encoding='UTF-8'?>
<LayerApi>  
<Status>Success</Status>  
<Layer 
   Class=\"ImageSetLayer\" 
   Opacity=\"1\" 
   StartTime=\"1/1/0001 12:00:00 AM\" 
   EndTime=\"12/31/9999 11:59:59 PM\" 
   FadeSpan=\"00:00:00\" 
   FadeType=\"None\" 
   Name=\"Bathymetry\" 
   ColorValue=\"ARGBColor:255:255:0:0\" 
   Enabled=\"True\" 
   Astronomical=\"False\" />
</LayerApi>
```
{% endmethod %}

{% method -%}
### group

The **group** command specifies that a layer group should be added.

#### Remarks

Layer groups are just an organizational aid when using the layer manager. The user will be able to collapse and expand groups in the Layer Manager, and have groups that are sub-sets of other groups.

| Required Parameters | Description |
| :-- | :-- |
| **&name** | A unique name for the layer group.|
| **&frame** | The reference frame of the group. This can be a layer group created with the **group** command, or one of: <br> **Earth, Moon, Mercury, Venus, Mars, Jupiter, Saturn, Uranus, Neptune, Pluto, Io, Ganymede, Callisto, Europa, Sun, ISS.** |

{% sample lang="html" -%}
#### Return Value
The following string will be included in the response if the call is successful:

```html
<Status>Success</Status>
```

If the call is not successful the following string may be included in the response, or other errors may be returned:

```html
<Status>Error - Invalid layer ID</Status>
```

#### Example Code

```cs
  private void createLayerGroup(string frame, string name)
  {
      try
      {
          WebClient client = new WebClient();
          string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=group&frame={1}&name={2}", getIP().ToString(), frame, name);
          string response = client.UploadString(url, "");
          XmlDocument doc = new XmlDocument();
          doc.LoadXml(response);
          XmlNode node = doc["LayerApi"];
          string s = node.InnerText;

          //
          // Handle an error situation
          //
          if (s.Contains("Error"))
          {
              throw new Exception(s);
          }
      }
      catch (Exception ex)
      {

      }
  }
```
{% endmethod %}

{% method -%}
### layerlist

The **layerlist** command returns the structure of the layers and layer group names (in an XML document format) that are currently in the layer manager.

#### Remarks

|  Optional Parameter | Description | Default Value |
| :-- | :-- | :-- |
| **&layersonly** | True indicates that only layers, and not reference frames or group names, should be returned. | False |
{% sample lang="html" -%}
#### Return Value
If the call is successful a string will be returned that is an XML document. Refer to the **Example** **Code** for the format and contents of the document.

If the call is not successful the following string may be included in the response, or other errors may be returned:

```html
<Status>Error - Invalid layer ID</Status>
```

#### Example Code

The following code will result in the response given below, though note that one layer (EarthQuakes2009) and one group (New Mercury Group) have been added to the layer manager prior to the **layerlist** call.

##### layerlist

```cs
//
// Recursive scanning of child nodes
//
private void scanNode(XmlNode node)
{
   int i,n;
   //
   // First loop through the attributes of the node
   //

   for (i = 0; i < node.Attributes.Count; i++)
   {
      // Do something with the node.Attributes[i].Name and node.Attributes[i].Value
   }      

   //
   // Next recurse the node tree
   //
   for (n = 0; n < node.ChildNodes.Count; n++)
   {
      scanNode(node.ChildNodes[n])
   }
}
//
// Get the current list of Reference Frames, groups and layers
//
private void getLayerlist(string layersonly)
{
   try
   {
     WebClient client = new WebClient();
     string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=layerlist&layersonly={1}", getIP().ToString(), layersonly);
     string response = client.UploadString(url, "");
     XmlDocument doc = new XmlDocument();

      doc.LoadXml(response);
      XmlNode node = doc["LayerApi"];
      if (node.InnerText.Contains("Success"))
      {

         XmlNode node2 = node["LayerList"];

         if (node2 != null)
         {
            // Top level items returned = node2.ChildNodes.Count
            scanNode(node2);
         }
         else
         {
            // No items returned
         }
      }
      else
      {
         throw new Exception(response);
      }
   }
   catch (Exception ex)
   {
      // Handle exception
   }
}
```

###### response (layersonly = "false")


```xml
<?xml version='1.0' encoding='UTF-8'?>
<LayerApi>  
<Status>Success</Status>  
<LayerList>    
<ReferenceFrame Name=\"Sun\" Enabled=\"True\">      
<ReferenceFrame Name=\"Mercury\" Enabled=\"True\">        
    <LayerGroup Name=\"New Mercury Group\" Enabled=\"True\" />      
</ReferenceFrame>      
<ReferenceFrame Name=\"Venus\" Enabled=\"True\" />      
<ReferenceFrame Name=\"Earth\" Enabled=\"True\">        
    <Layer Name=\"EarthQuakes2009\" ID=\"712cdef2-907f-4fc5-ae2c-c57488be4517\" Type="SpreadSheetLayer" Enabled=\"True\" />        
    <ReferenceFrame Name=\"Moon\" Enabled=\"True\" />        
    <ReferenceFrame Name=\"ISS\" Enabled=\"True\" />      
</ReferenceFrame>      
<ReferenceFrame Name=\"Mars\" Enabled=\"True\" />      
<ReferenceFrame Name=\"Jupiter\" Enabled=\"True\">        
    <ReferenceFrame Name=\"Io\" Enabled=\"True\" />        
    <ReferenceFrame Name=\"Europa\" Enabled=\"True\" />        
    <ReferenceFrame Name=\"Ganymede\" Enabled=\"True\" />
        <ReferenceFrame Name=\"Callisto\" Enabled=\"True\" />      
</ReferenceFrame>      
<ReferenceFrame Name=\"Saturn\" Enabled=\"True\" />      
<ReferenceFrame Name=\"Uranus\" Enabled=\"True\" />      
<ReferenceFrame Name=\"Neptune\" Enabled=\"True\" />
<ReferenceFrame Name=\"Pluto\" Enabled=\"True\" />    
</ReferenceFrame>  
</LayerList>
</LayerApi>
```

###### response (layersonly = "true")

```xml
<?xml version='1.0' encoding='UTF-8'?>
<LayerApi>
  <Status>Success</Status>
  <LayerList>
    <Layer Name=\"EarthQuakes2009\" ID=\"712cdef2-907f-4fc5-ae2c-c57488be4517\" Type="SpreadSheetLayer"  Enabled=\"True\" />
  </LayerList>
</LayerApi>
```
{% endmethod %}

{% method -%}
### load

The **load** command specifies a data file, and and some optional parameters, to apply to a new layer.

#### Remarks

Files that can be loaded using this commend include spreadsheet data (comma or tab delimited), shape files (.shp), 3D model files (.3ds), and WTML files containing **ImageSet** references. In the latter case the first image set found in the file will be loaded. Refer to the [WorldWide Telescope Data Files Reference](http://www.worldwidetelescope.org/Docs/WorldWideTelescopeDataFilesReference.html) for more details on WTML files.

If a spreadsheet of data is loaded, the **load** command will initiate an import wizard of WorldWide Telescope, in order to select the appropriate columns, timing data, and so on.  An import wizard is not automatically invoked by the other data file types.

Note that parameter names are case-sensitive.

| Required Parameters | Description |
| :-- | :-- |
| **&frame** | The reference frame of the group. This can be an existing layer group name, or one of: <br> **Earth, Moon, Mercury, Venus, Mars, Jupiter, Saturn, Uranus, Neptune, Pluto, Io, Ganymede, Callisto, Europa, Sun, ISS.** |
| **&filename** | The full path of the file to load. |

| Optional Parameters | Description | Default Value|
| :-- | :-- | :-- |
| **&name** | A friendly name for the layer. | "New Layer" |
| **&color** | ARBG hex value of the color to be used when rendering the events of the layer. | FFFFFFFF (white) |
| **&startdate** | With time series data, the date and time to start the visualization for this layer. This could for example be slightly earlier than the date of the first event in the actual data. For example: ```"1/1/2000 12:30:30 AM"```. <br> Formats (month/day/year): <br> "1/1/2010 11:00:00 PM" <br> "1/1/2010 11:30 AM" <br>  "1/1/2010 11 am" <br> "1/1/2000" <br> "1/2000"| The System minimum date value |
| **&enddate** | With time series data, the date and time to end the visualization for this layer. | The System maximum date value |
| **&fadetype** | Fades the data visualization. One of: **In**, **Out**, **Both** or **None**. | None |
| **&faderange** | Fade time in days. | Zero |

{% sample lang="html" -%}
#### Return Value
The following string will be included in the response string:

```html
<LayerApi><NewLayerID>_nnnn_</NewLayerID></LayerApi>
```

Where _nnnn_ is the layer id, which will be a GUID in string format.

If the call is not successful the following string may be included in the response, or other errors may be returned:

```html
<Status>Error - Could not Load Layer</Status>
```

#### Example Code

Note that the example code uses **datetime**, one of the [general parameters](#general-parameters), to initiate the visualization of the data immediately.

```js
private void loadDatafile(string name, string frame, string filename, string datetime)
{
    try
    {
        WebClient client = new WebClient();
        string url = string.Format("[http://{0}:5050/layerApi.aspx?cmd=load&frame={1}&filename={2}&name={3}&datetime={4}](http://{0}:5050/layerApi.aspx?cmd=load&frame={1}&filename={2}&name={3}&datetime={4})",
getIP().ToString(), frame, filename, name, datetime);
        string response = client.UploadString(url, "");
        XmlDocument doc = new XmlDocument();
        doc.LoadXml(response);
        XmlNode node = doc["LayerApi"];
        XmlNode child = node.ChildNodes[0];
        layerId = child.InnerText;
        string s = node.InnerText;

        //
        // Handle an error situation
        //
        if (s.Contains("Error"))
        {
            throw new Exception(s);
        }
    }
    catch (Exception ex)
    {

    }
}
```
{% endmethod %}

{% method -%}
### mode

The **mode** command changes the view to one of **Earth**, **Planet**, **Sky**, **Panorama**, **SolarSystem**.

#### Remarks

This command does not take any parameters.
{% sample lang="html" -%}
#### Return Value
The following string will be included in the response if the call is successful:

```html
<Status>Success</Status>
```

#### Example code

##### Change mode

```js
WebClient client = new WebClient();
string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=mode&lookat={1}", getIP().ToString(), "SolarSystem");
string response = client.UploadString(url, "");
```
{% endmethod %}

{% method -%}
### move

The **move** command changes the view depending on the supplied parameter.

#### Remarks

One move parameter must be supplied, **&move=_nnnn_**, where _nnnn_ is one of the following:

| Required Parameters | Description |
| :-- | :-- |
| **ZoomIn** | Zoom in on the current view. |
| **ZoomOut** | Zoom out of the current view. |
| **Up** | Move the current view up. |
| **Down** | Move the current view down. |
| **Left** | Move the current view left. |
| **Right** | Move the current view right. |
| **Clockwise** | Rotate the view clockwise 0.2 of one radian. |
| **CounterClockwise** | Rotate the view counterclockwise 0.2 of one radian. |
| **TiltUp** | Angle the view up 0.2 of one radian. |
| **TiltDown** | Angle the view down 0.2 of one radian. |
| **Finder** | Currently unimplemented. |
{% sample lang="html" -%}
#### Return Value
The following string will be included in the response if the call is successful:

```html
<Status>Success</Status>
```

The following code will be included in the response if the move parameter is invalid:

```html
<Status>Error - Invalid parameter</Status>
```

#### Example code

```js
WebClient client = new WebClient();
string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=move&move={1}", getIP().ToString(), "ZoomIn");
string response = client.UploadString(url, "");
```
{% endmethod %}

{% method -%}
### new

The **new** command specifies that a new layer should be created.

#### Remarks

The **new** command will request that an entirely new layer be created, with the following parameters (note that the parameter names are case-sensitive):

| Required Parameters | Description |
| :-- | :-- |
| **&frame** | The reference frame of the layer. This can be one of: <br> **Earth, Moon, Mercury, Venus, Mars, Jupiter, Saturn, Uranus, Neptune, Pluto, Io, Ganymede, Callisto, Europa, Sun, ISS.** |

| Optional Parameters| Description | Default Value |
| :-- | :-- | :-- |
| **&name** | A friendly name for the layer. | "New Layer" |
| **&color** | ARBG hex value of the color to be used when rendering the events of the layer. | FFFFFFFF (white) |
| **&startdate** | With time series data, the date and time to start the visualization for this layer. This could for example be slightly earlier than the date of the first event in the actual data. For example: ```html"1/1/2000 12:30:30 AM"```. <br> Formats (month/day/year):  <br> "1/1/2010 11:00:00 PM" <br> "1/1/2010 11:30 AM" <br>  "1/1/2010 11 am" <br> "1/1/2000" <br> "1/2000"| The System minimum date value |
| **&enddate** | With time series data, the date and time to end the visualization for this layer. | The System maximum date value |
| **&fadetype** | Fades the data visualization. One of: **In**, **Out**, **Both** or **None**. | None |
| **&faderange** | Fade time in days. | Zero |

The **new** command is embedded in a formatted string (see the **Example Code)**. This formatted string forms the first of two parameters to the **WebClient** **UploadString** method.

The second string parameter to **UploadString** should contain a comma or tab delimited string containing the heading names of the columns in the layer. The supported column names are listed in the following table. The names of the columns in the data file must _contain_ the names listed here (so **"Latitude"** is acceptable for **"Lat"**, or "**dMag**" acceptable for "**Mag**", for example).

| **Column Heading** | Description |
| :-- | :-- |
| **Time** | UTC time, for example: ```html"1/1/2000 12:02:46 AM"``` |
| **Lat** | Latitude in decimal degrees. |
| **Lon** | Longitude in decimal degrees. |
| **RA** | Right ascension in decimal degrees. |
| **Dec** | Declination in decimal degrees. |
| **Depth** | Depth in kilometers. |
| **Altitude** | Altitude in meters. |
| **Mag** | Magnitude as a floating point number. |
| **X** | X co-ordinate, if a Rectangular co-ordinate system is being used. |
| **Y** | Y co-ordinate.|
| **Z** | Z co-ordinate. |

{% sample lang="html" -%}
#### Return Value
The following string will be included in the response string:

```html
<LayerApi><NewLayerID>_nnnn_</NewLayerID></LayerApi>
```

Where _nnnn_ is the layer id number, which will be a GUID in string format. If a GUID is not received, then the call was not successful.

#### Example Code

Note that the example code uses **datetime**, one of the [general parameters](#general-parameters), to initiate the visualization of the data immediately.
Required Parameters

```js
   //
   // Send a NEW command, extracting info from the data file and the UI
   //
   WebClient client = new WebClient();
   string datetime =  ```html"1/1/2009 12:00:00 AM"```;
   string name = ```html"Earthquakes 2009"```;
   string rate = ```html"10000"```;
   string frame = ```html"Earth"```;
   string color = ```html"FFFF0000"```;

   string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=new&datetime={1}&timerate={2}&name={3}&frame={4}&color={5}",  getIP().ToString(), datetime, rate, name, frame, color);

   // field string below is delimited by tabs, not spaces
   string response = client.UploadString(url, "TIME    LAT    LON    DEPTH    MAG");
   XmlDocument doc = new XmlDocument();
   doc.LoadXml(response);
   XmlNode node = doc["LayerApi"];
   XmlNode child = node.ChildNodes[0];
   layerId = child.InnerText;

   //
   // Handle an error situation
   //
   if (layerId.Length != 36)
       throw new Exception("Invalid Layer Id received");

   //
```

For an example of the use of this command in the sample application, see [initWWTLayer](sampleapplication.md#sample-program).
{% endmethod %}

{% method -%}
### setprop

The **setprop** command is used to specify a value for a single layer property.

#### Remarks

| Required Parameters | Description |
| :-- | :-- |
| **&id** | Specifies the id number of the layer. |
| **&propname** | Property name. Refer to the [table of properties](#table-of-properties) listed for the [**getprop**](#getprop) command. |
| **&propvalue** | Property value in string form. |
{% sample lang="html" -%}
#### Return Value
If the call is successful, then the response will contain the string:

```html
<Status>Success</Status>
```

If the call is not successful the following string may be included in the response, or other errors may be returned:

```html
<Status>Error - Invalid parameter</Status>
``` 

#### Example Code

##### Set Opacity property

```cs
 WebClient client = new WebClient();
 string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=setprop&id={1}&propname={2}&propvalue={3}", getIP().ToString(), layerId,"Opacity", "0.5");
 string response = client.UploadString(url, "");
```
{% endmethod %}

{% method -%}
### setprops

The **setprops** command is used to specify multiple properties for a layer.

#### Remarks

| Required Parameters | Description |
| :-- | :-- |
| **&id** | Specifies the id number of the layer. |

Note that the second string parameter to the **UploadString** method contains the xml encoding of the properties. The string should be a correctly formatted XML file, with **xml**, **LayerApi** and **Layer** entries. Each attribute to the **Layer** entry should be a property name/property value pair.

This method can be used to set a single property, though the [**setprop**](#setprop) command is specifically designed for this.

Refer to the [table of properties](#table-of-properties) listed for the [**getprop**](#getprop) command.
{% sample lang="html" -%}
#### Return Value
If the call is successful, then the response will contain the string:

```html
<Status>Success</Status>
```

If the call is not successful the following string may be included in the response, or other errors may be returned:

```html
<Status>Error - Invalid parameter</Status>
```

#### Example Code


##### Set BeginRange and Decay properties

```cs
string propertyXML = "<?xml version='1.0' encoding='UTF-8'?><LayerApi><Layer BeginRange=\"1/9/2008 11:44:38 AM\" Decay=\"5.5\" /> </LayerApi>"; 

WebClient client = new WebClient();
string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=setprops&id={1}", getIP().ToString(), layerId);
string response = client.UploadString(url, propertyXML);
```
{% endmethod %}

{% method -%}
### state

The **state** command requests some details of the current view.

#### Remarks

This command does not take any parameters. The details returned vary slightly depending on the view mode, and include the current view mode, latitude (or declination) in decimal degrees, longitude (or right ascension) in decimal degrees, zoom factor (0 to 360), angle in radians, rotation in radians, current UTC time, time rate, reference frame, a view token, and zoom text.
{% sample lang="html" -%}
#### Return Value
If the call is successful the following string will be returned:

```html
<LayerApi><Status>Success</Status><ViewState> [state information](#State_information) </ViewState></LayerApi>
```

#### Example code

##### Get state

```cs
private void getState()
{
   try
   {
       WebClient client = new WebClient();
       string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=state", getIP().ToString());
       string response = client.UploadString(url, "");
       XmlDocument doc = new XmlDocument();
       doc.LoadXml(response);
       XmlNode node = doc["LayerApi"];
       if (node.InnerText.Contains("Success"))
       {
           XmlNode node2 = node["ViewState"];

           if (node2 != null)
           {
               string propName;
               string propValue;

               for (int i = 0; i < node2.Attributes.Count; i++)
               {
                   propName = node2.Attributes[i].Name;
                   propValue =  node2.Attributes[i].Value;
                   // Do something with the name value pair
               }
           }
           else
           {
              // Success message was a false positive, no state was returned
           }
       }
       else
       {
           throw new Exception(response);
       }
   }
   catch (Exception ex)
   {
      // Handle the exception
   }
}
```

##### State information for each of the five modes:

```cs
<ViewState 
   lookat=\"Earth\" 
   lat=\"86.1800140779305\" 
   lng=\"4.56191579793961\" 
   zoom=\"30.9237645312\" 
   angle=\"-1.52\" 
   rotation=\"0\" 
   time=\"2/23/2011 7:03:31 PM\" 
   timerate=\"1\" 
   ReferenceFrame=\"Earth\" 
   ViewToken=\"SD8834DFA\" 
   zoomText=\"5085 km\">
</ViewState>

<ViewState 
   lookat=\"Planet\" 
   lat=\"86.1800140779305\" 
   lng=\"4.58610934632668\" 
   zoom=\"360\" 
   angle=\"-1.52\" 
   rotation=\"0\" 
   time=\"2/23/2011 7:04:19 PM\" 
   timerate=\"1\" 
   ReferenceFrame=\"Mars\" 
   ViewToken=\"SD8834DFA\" 
   zoomText=\"59200 km\">
</ViewState>

<ViewState 
   lookat=\"Sky\" 
   ra=\"10.2626615902966\" 
   dec=\"46.1799337621283\" 
   zoom=\"30.9237645312\" 
   rotation=\"0\" 
   time=\"2/23/2011 7:04:51 PM\" 
   timerate=\"1\" 
   ReferenceFrame=\"Space\" 
   ViewToken=\"SD8834DFA\" 
   zoomText=\"05:09:14\">
</ViewState>

<ViewState 
   lookat=\"Panorama\" 
   lat=\"-3.07957843596986\" 
   lng=\"-39.9196238171819\" 
   zoom=\"117.9648\" 
   angle=\"-1.52\" 
   rotation=\"0\" 
   time=\"2/23/2011 7:05:35 PM\" 
   timerate=\"1\" 
   ReferenceFrame=\"Panorama\" 
   ViewToken=\"SD8834DFA\" 
   zoomText=\"19:39:39\">
</ViewState>

<ViewState 
   lookat=\"SolarSystem\" 
   lat=\"2.25294244185011\" 
   lng=\"262.382989924732\" 
   zoom=\"0.000578857064151695\" 
   angle=\"-1.52\" 
   rotation=\"0\" 
   time=\"2/23/2011 7:06:13 PM\" 
   timerate=\"1\" 
   ReferenceFrame=\"Sun\" 
   ViewToken=\"SD8834DFA\" 
   zoomText=\"38636 km\">
</ViewState>
```
{% endmethod %}

{% method -%}
### uisettings

The **uisettings** command is used to change user interface settings, without altering the layer data. Note that the spelling errors in the names of the properties must be matched.

| Setting Name | Setting Type |
| :-- | :-- |
| **AutoHideContext** | True or False |
| **AutoHideTabs** | True or False |
| **AutoRepeatTour** | True or False |
| **AutoRepeatTourAll** | True or False |
| **ConstellationBoundryColor** | RGB color (default, "MidnightBlue") |
| **ConstellationFigureColor** | RGB color (default, "DarkRed") |
| **ConstellationFiguresFile** | String (a filename) |
| **ConstellationSelectionColor** | RGB color (default, "DarkGoldenrod") |
| **ContextSearchFilter** | String (default "Unfiltered") |
| **DomeTilt** | Double (default "0") |
| **DomeTypeIndex** | Integer (default, "0") |
| **DomeView** | True or False |
| **EclipticColor** | RGB color (default, "CornflowerBlue ") |
| **FollowMouseOnZoom** | True or False |
| **FovCamera** | Integer (default, "0") |
| **FovColor** | RGB color (default, "white") |
| **FovEyepiece** | Integer (default, "0")|
| **FovTelescope** | Integer (default, "0") |
| **FullScreenTours** | True or False |
| **GridColor** | RGB color (default, "64, 64, 64") |
| **ImageQuality** | Integer: 0 to 100 (default, 100)|
| **LargeDomeTextures** | True or False |
| **LastLookAtMode** | Integer (default, "2")|
| **LineSmoothing** | True or False |
| **ListenMode** | True or False |
| **LocalHorizonMode** | True or False |
| **LocationAltitude** | Double (default, "100") |
| **LocationLat** | Double (default, "47.64222") |
| **LocationLng** | Double (default, "-122.142")|
| **LocationName** | String (default, "Microsoft Research Building 99") |
| **MasterController** | True or False |
| **ShowClouds** | True or False |
| **ShowConstellationBoundries** | True or False |
| **ShowConstellationFigures** | True or False |
| **ShowConstellationNames** | True or False |
| **ShowConstellationSelection** | True or False |
| **ShowCrosshairs** | True or False |
| **ShowDatasetNames** | True or False |
| **ShowEarthSky** | True or False |
| **ShowEcliptic** | True or False |
| **ShowElevationModel** | True or False |
| **ShowFieldOfView** | True or False |
| **ShowGrid** | True or False |
| **ShowSolarSystem** | True or False |
| **ShowTouchControls** | True or False |
| **ShowUTCTime** | True or False |
| **SolarSystemCosmos** | True or False |
| **SolarSystemLighting** | True or False |
| **SolarSystemMilkyWay** | True or False |
| **SolarSystemMinorOrbits** | True or False |
| **SolarSystemMinorPlanets** | True or False |
| **SolarSystemMultiRes** | True or False |
| **SolarSystemOrbitColor** | RGB color (default, "64, 64, 64") |
| **SolarSystemOrbits** | True or False |
| **SolarSystemOverlays** | True or False |
| **SolarSystemPlanets** | True or False |
| **SolarSystemScale** | True or False |
| **SolarSystemStars** | True or False |
| **StartUpLookAt** | Integer (default, "4") |

#### Remarks

This command does not perform any action other than that of changing of the user interface settings.
{% sample lang="html" -%}
#### Return Value
If the call is successful, then the response will contain the string:

```html
<Status>Success</Status>
```

#### Example Code

##### Fly to a location

```cs
//
// Sets the property "ShowConstellationFigures" to true.

WebClient client = new WebClient();
string  url = string.Format("http://{0}:5050/layerApi.aspx?cmd=uisettings&ShowConstellationFigures=True", getIP().ToString());
string response = client.UploadString(url, "");
```
{% endmethod %}

{% method -%}
### update

The **update** command specifies that the data attached to this command should be added to the layer.

#### Remarks

The **update** command will request that data for a layer be updated, with the following parameters (note that the parameter names are case-sensitive):

| Required Parameters | Description |
| :-- | :-- |
| **&id** | Specifies the id number of the layer. |

| Optional Parameters | Description | Default Value |
| :-- | :-- | :-- |
| **&hasheader** | Set to true if the data has a header row. The header should be the first row of the data. | False |
| **&name** | A friendly name to rename the layer. | No change |
| **&nopurge** | The sending of an update command will delete events that occur before the start time of any events in the update and that have already decayed. Set this flag to **true** if the event data should not be deleted. | False |
| **&purgeall** | Purge (delete) all events. | False |
| **&show** | Set to **true** to show the layer, **false** to hide it. True and false are not case-sensitive. | True |

The second string parameter to **UploadString** should contain a comma or tab delimited string containing the data in a form that matches the heading names of the columns in the layer provided in the **[new](#new)** command.
{% sample lang="html" -%}
#### Return Value
The following string will be included in the response if the call is successful:

```html
<Status>Success</Status>
```

If the call is not successful the following string may be included in the response, or other errors may be returned:

```html
<Status>Error - Invalid layer ID</Status>
```

#### Example Code
```cs
  //
  // Send an UPDATE command
  //
  WebClient client = new WebClient();
  string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=update&id={1}", getIP().ToString(), layerId);
  string response = client.UploadString(url, lineBuffer);
  XmlDocument doc = new XmlDocument();
  doc.LoadXml(response);
  XmlNode node = doc["LayerApi"];
  string s = node.InnerText;

  //
  // Handle an error situation
  //
  if (s.Contains("Error"))
  {
      throw new Exception(s);
  }

  //
  // Send an UPDATE command with a flyTo parameter
  //
  string lat, lon, zoom, rotation, angle;
  WebClient client = new WebClient();
  string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=update&id={1}&flyTo={2},{3},{4},{5},{6}&instant=True", getIP().ToString(), layerId, lat, lon, zoom, rotation, angle);
  string response = client.UploadString(url, lineBuffer);
  XmlDocument doc = new XmlDocument();
  doc.LoadXml(response);
  XmlNode node = doc["LayerApi"];
  string s = node.InnerText;

  //
  // Handle an error situation
  //
  if (s.Contains("Error"))
  {
      throw new Exception(s);
  }
```
For an example of the use of this command in the sample application, see [flushBufferToWWT](sampleapplication.md#sample-program).

{% endmethod %}

{% method -%}
### version

The **version** command returns the version number of the running version of the LCAPI.

#### Remarks

The version number should be used in an application to ensure that the LCAPI features used are supported by the version of the LCAPI running on the client computer.
{% sample lang="html" -%}
#### Return Value
The following string will be included in the response if the call is successful (the version number will be more recent than the one shown below) :

```html
<LayerApi><Version>2.8.21.1</Version></LayerApi>
```

There are no specific errors returned if the call is not successful.

#### Example Code

Refer to the [getIP](utilities.md#getip) utility to see how to extract and check a version number.
{% endmethod %}

{% method -%}
### General Parameters

The general parameters can be used with any of the commands.

#### Remarks

| Parameter | Description |
| :-- | :-- |
| **&datetime** | Sets the viewing clock to the given date and time, in UTC format, for example: ```html"1/1/2000 12:02:46 AM"```. This is the date and time that can be set through the **View** menu. <br> Formats (month/day/year): <br> "1/1/2010 11:00:00 PM" <br> "1/1/2010 11:30 AM" <br>  "1/1/2010 11 am" <br> "1/1/2000" <br> "1/2000"|
| **&timerate** | The accelerated time to render the visualization, as a multiple of 10. |
| **&flyto** | Sets the position of the view camera. Requires five floating point numbers separated by commas: latitude, longitude, zoom level, rotation and angle. <br> <ul><li>   Latitude is in decimal degrees, positive to the North. </li> <li> Longitude is in decimal degrees, positive to the East. </li> <li> Zoom level varies from 360 (the most distant view) to 0.00023 (the closest view).</li> <li> Rotation is in radians, positive moves the camera to the left.</li> <li> Angle is in radians, positive moves the camera forward.</li> </ul> <br> Optionally there can be a sixth parameter containing the frame to change the view to, which can be one of: <br> **Earth, Moon, Mercury, Venus, Mars, Jupiter, Saturn, Uranus, Neptune, Pluto, Io, Ganymede, Callisto, Europa, Sun, ISS.** |
| **&instant** | Used with the **&flyto** parameter, set this to **true** to specify that the camera should jump to the location, or **false** that the camera should smoothly pan and zoom to the location. |
| **&autoloop** | True sets the layer manager to auto loop. |

{% sample lang="html" -%}
#### Example Code

##### Fly to a location

```cs
//
// Extract the values from the text boxes of a dialog, assuming there are rich text boxes with the names shown below.
//
string flyString = richLat.Text + "," + richLong.Text + "," + richZoom.Text + "," + richRotation.Text + "," + richAngle.Text;

WebClient client = new WebClient();
string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=state&flyto={1}&instant={2}", getIP().ToString(), flyString, richInstant.Text);
string response = client.UploadString(url, "");
```
{% endmethod %}
