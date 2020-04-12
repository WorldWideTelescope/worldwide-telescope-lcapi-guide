+++
title = "LCAPI Commands"
weight = 200
+++

The following commands can be used to control layers from the application.
Note that command names are not case-sensitive, and that the
[general parameters](#general-parameters) can be included along with any other
command.

| Command |  Description |
| :-- | :-- |
| [**activate**](#activate) | Highlights the selected layer in the layer manager. |
| [**delete**](#delete) | Specifies that a layer should be permanently deleted. |
| [**getprop**](#getprop) | Retrieve a single layer property. |
| [**getprops**](#getprops) | Retrieve all the properties for a specified layer. |
| [**group**](#group) | Specifies that a layer group should be added. |
| [**layerlist**](#layerlist) | Get the structure of the layers and layer group names |
| [**load**](#load) | Specifies a data file, and and some optional parameters, to apply to a new layer. |
| [**mode**](#mode) | Changes the view mode to one of `Earth`, `Planet`, `Sky`, `Panorama`, `SolarSystem`. |
| [**move**](#move) | Changes the view depending on the supplied parameter. |
| [**new**](#new) | Specifies that a new layer should be created. |
| [**setprop**](#setprop)| Specify a value of a single layer property. |
| [**setprops**](#setprops)| Specify multiple values for a layer.|
| [**state**](#state) | Requests some details of the current view. |
| [**uisettings**](#uisettings) | Used to change user interface settings, without altering the layer data. |
| [**update**](#update) | Specifies that the data attached to this command should be added to the layer. |
| [**version**](#version) | Returns the version number of the running version of the LCAPI. |
| [general parameters](#general-parameters) | Parameters that can be applied to any of the commands. |

---

# activate

The **activate** layer command will highlight the selected layer in the layer
manager.

| Required Parameters | Description |
| :-- | :-- |
| *id* | Specifies the id number of the layer. |

The following string will be included in the response if the call is
successful:

```xml
<Status>Success</Status>
```

If the call is not successful the following string may be included in the
response, or other errors may be returned:

```xml
<Status>Error - Invalid layer ID</Status>
```

---

# delete

The **delete** command specifies that a layer should be permanently deleted.

All sub-components of the layer will also be deleted.

| Required Parameters | Description |
| :-- | :-- |
| *id* | Specifies the ID number of the layer. |

The following string will be included in the response if the call is
successful:

```xml
<Status>Success</Status>
```

If the call is not successful the following string may be included in the
response, or other errors may be returned:

```xml
<Status>Error - Invalid layer ID</Status>
```

---

# getprop

The **getprop** command is used to retrieve a value of a single layer
property.

| Required Parameters | Description |
| :-- | :-- |
| *id* | Specifies the id number of the layer. |
| *propname* | Property name |

Layer properties are documented in
[the next section](@/properties.md#layer-properties).

If the call is successful the response will contain the following string:

```xml
<LayerApi><Status>Success</Status><Layer {propertyName}="{propertyValue}"</Layer></LayerApi>
```

Where `{propertyName}` is the property name requested, and `{propertyValue}` is
the value returned. For example:

```xml
<LayerApi><Status>Success</Status><Layer AltType="Depth"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer AltColumn="3"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer Astronomical="False"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer BeginRange="1/9/2009 11:44:38 AM"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer EndRange="9/9/2009 11:13:52 PM"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer Decay="16"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer Name="EarthQuakes2009"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer FadeSpan="00:00:00"</Layer></LayerApi>
<LayerApi><Status>Success</Status><Layer MarkerIndex="-1"</Layer></LayerApi>
```

If the call is not successful the following string may be included in the
response, or other errors may be returned:

```xml
<Status>Error - Invalid parameter</Status>
```

#### Example: Get Altitude Unit of a Layer

The following client code:

```cs
WebClient client = new WebClient();
string url = string.Format(
  "http://{0}:5050/layerApi.aspx?cmd=getprop&id={1}&propname={2}",
  getIP().ToString(), layerId, "AltUnit"
);
```

Might receive the following response:

```xml
<?xml version="1.0" encoding="utf-8"?><LayerApi><Status>Success</Status><Layer AltUnit="Kilometers"</Layer></LayerApi>
```

---

# getprops

The **getprops** command is used to retrieve all the properties for a
specified layer.

Refer to the [table of properties](#table-of-properties) listed for the
[**getprop**](#getprop) command.

| Required Parameters | Description |
| :-- | :-- |
| *id* | Specifies the id number of the layer. |

Layer properties are documented in [the next section](@/properties.md#layer-properties).

If the call is successful the response string will contain the following
string:

```xml
<Status>Success</Status>
```

If the call is not successful the following string may be included in the
response, or other errors may be returned:

```xml
<Status>Error - Invalid layer ID</Status>
```

The following example gets all the properties from any of the different types
of layer. Note that the response string does not include newlines, these have
been added for readability.

#### Example: Get All Properties of Layer

The following code fetches and parses all properties of layer:

```cs
private void buttonGetProperties()
{
    try
    {
        WebClient client = new WebClient();
        string url = string.Format(
          "http://{0}:5050/layerApi.aspx?cmd=getprops&id={1}",
          getIP().ToString(), layerId
        );
        string response = client.UploadString(url, "");
        XmlDocument doc = new XmlDocument();

        doc.LoadXml(response);
        XmlNode node = doc["LayerApi"];
        if (node.InnerText.Contains("Success"))
        {
            XmlNode node2 = node["Layer"];
            if (node2 != null)
            {
                string propName;
                string propValue;

                for (int i = 0; i < node2.Attributes.Count; i++)
                {
                    propName = node2.Attributes[i].Name;
                    propValue = node2.Attributes[i].Value;
                    // Do something with the name/value pair
                }
            }
            else
            {
                // No properties returned
            }
        }
        else
        {
           throw new Exception(response);
        }
    }
    catch (Exception ex)
    {
        // Handle exception
    }
}
```

For a "spreadsheet" (tabular) data layer, the response may take the form:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<LayerApi>
<Status>Success</Status>
<Layer
   Class="SpreadSheetLayer"
   TimeSeries="True"
   BeginRange="1/9/2009 11:44:38 AM"
   EndRange="9/9/2009 11:13:52 PM"
   Decay="16"
   CoordinatesType="Spherical"
   LatColumn="1"
   LngColumn="2"
   GeometryColumn="-1"
   XAxisColumn="-1"
   YAxisColumn="-1"
   ZAxisColumn="-1"
   XAxisReverse="False"
   YAxisReverse="False"
   ZAxisReverse="False"
   AltType="Depth"
   RaUnits="Hours"
   MarkerMix="Same_For_All"
   MarkerColumn="-1"
   ColorMapColumn="-1"
   PlotType="Gaussian"
   MarkerIndex="-1"
   ShowFarSide="False"
   MarkerScale="World"
   AltUnit="Kilometers"
   CartesianScale="Meters"
   CartesianCustomScale="1"
   AltColumn="3"
   StartDateColumn="0"
   EndDateColumn="-1"
   SizeColumn="4"
   NameColumn="0"
   HyperlinkFormat=""
   HyperlinkColumn="-1"
   ScaleFactor="1"
   PointScaleType="Power"
   Opacity="1"
   StartTime="1/1/0001 12:00:00 AM"
   EndTime="12/31/9999 11:59:59 PM"
   FadeSpan="00:00:00"
   FadeType="None"
   Name="EarthQuakes2009"
   ColorValue="ARGBColor:255:255:0:0"
   Enabled="True"
   Astronomical="False" />
</LayerApi>
```

For a 3D Model layer, the response may look like:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<LayerApi>
<Status>Success</Status>
<Layer
   Class="Object3dLayer"
   FlipV="False"
   Smooth="True"
   Heading="0"
   Pitch="0"
   Roll="0"
   Scale="1, 1, 1"
   Translate="0, 0, 0"
   Opacity="1"
   StartTime="1/1/0001 12:00:00 AM"
   EndTime="12/31/9999 11:59:59 PM"
   FadeSpan="00:00:00"
   FadeType="None"
   Name="aurora"
   ColorValue="ARGBColor:255:255:0:0"
   Enabled="True"
   Astronomical="False" />
</LayerApi>
```

For a ShapeFile layer, the response may look like:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<LayerApi>
<Status>Success</Status>
<Layer
   Class="ShapeFileRenderer"
   Opacity="1"
   StartTime="1/1/0001 12:00:00 AM"
   EndTime="12/31/9999 11:59:59 PM"
   FadeSpan="00:00:00"
   FadeType="None"
   Name="state_bounds"
   ColorValue="ARGBColor:255:255:0:0"
   Enabled="True"
   Astronomical="False" />
</LayerApi>
```

For an ImageSet layer, the response may look like:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<LayerApi>
<Status>Success</Status>
<Layer
   Class="ImageSetLayer"
   Opacity="1"
   StartTime="1/1/0001 12:00:00 AM"
   EndTime="12/31/9999 11:59:59 PM"
   FadeSpan="00:00:00"
   FadeType="None"
   Name="Bathymetry"
   ColorValue="ARGBColor:255:255:0:0"
   Enabled="True"
   Astronomical="False" />
</LayerApi>
```

---

# group

The **group** command specifies that a layer group should be added.

Layer groups are just an organizational aid when using the layer manager. The
user will be able to collapse and expand groups in the Layer Manager, and have
groups that are sub-sets of other groups.

| Required Parameters | Description |
| :-- | :-- |
| *name* | A unique name for the layer group. |
| *frame* | The reference frame of the group. |

The reference frame can be a layer group created with the **group** command,
or one of: `Earth`, `Moon`, `Mercury`, `Venus`, `Mars`, `Jupiter`, `Saturn`,
`Uranus`, `Neptune`, `Pluto`, `Io`, `Ganymede`, `Callisto`, `Europa`, `Sun`,
or `ISS`.

The following string will be included in the response if the call is
successful:

```xml
<Status>Success</Status>
```

If the call is not successful the following string may be included in the
response, or other errors may be returned:

```xml
<Status>Error - Invalid layer ID</Status>
```

#### Example: Create a Layer group

```cs
private void createLayerGroup(string frame, string name)
{
    try
    {
        WebClient client = new WebClient();
        string url = string.Format(
          "http://{0}:5050/layerApi.aspx?cmd=group&frame={1}&name={2}",
          getIP().ToString(), frame, name
        );
        string response = client.UploadString(url, "");
        XmlDocument doc = new XmlDocument();
        doc.LoadXml(response);
        XmlNode node = doc["LayerApi"];
        string s = node.InnerText;

        //
        // Handle an error situation
        //
        if (s.Contains("Error"))
        {
            throw new Exception(s);
        }
    }
    catch (Exception ex)
    {
        // Handle exception
    }
}
```

---

# layerlist

The **layerlist** command returns the structure of the layers and layer group
names (in an XML document format) that are currently in the layer manager.

|  Optional Parameter | Description | Default Value |
| :-- | :-- | :-- |
| *layersonly* | True indicates that only layers, and not reference frames or group names, should be returned. | False |

If the call is successful a string will be returned that is an XML document.
The format and contents are shown in the example below If the call is not
successful the following string may be included in the response, or other
errors may be returned:

```xml
<Status>Error - Invalid layer ID</Status>
```

#### Example: Get Layer List

The following code will result in the response given below, in an example
where one layer (`EarthQuakes2009`) and one group (`New Mercury Group`) have
been added to the layer manager prior to the **layerlist** call.

```cs
//
// Recursive scanning of child nodes
//
private void scanNode(XmlNode node)
{
   int i,n;
   //
   // First loop through the attributes of the node
   //

   for (i = 0; i < node.Attributes.Count; i++)
   {
      // Do something with the node.Attributes[i].Name and node.Attributes[i].Value
   }

   //
   // Next recurse the node tree
   //
   for (n = 0; n < node.ChildNodes.Count; n++)
   {
      scanNode(node.ChildNodes[n])
   }
}

//
// Get the current list of Reference Frames, groups and layers
//
private void getLayerlist(string layersonly)
{
   try
   {
       WebClient client = new WebClient();
       string url = string.Format(
         "http://{0}:5050/layerApi.aspx?cmd=layerlist&layersonly={1}",
         getIP().ToString(), layersonly
       );
       string response = client.UploadString(url, "");
       XmlDocument doc = new XmlDocument();

       doc.LoadXml(response);
       XmlNode node = doc["LayerApi"];
       if (node.InnerText.Contains("Success"))
       {
          XmlNode node2 = node["LayerList"];

          if (node2 != null)
          {
             // Top level items returned = node2.ChildNodes.Count
             scanNode(node2);
          }
          else
          {
             // No items returned
          }
       }
       else
       {
          throw new Exception(response);
       }
   }
   catch (Exception ex)
   {
      // Handle exception
   }
}
```

If the *layersonly* parameter is false, the response might be:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<LayerApi>
<Status>Success</Status>
<LayerList>
<ReferenceFrame Name="Sun" Enabled="True">
<ReferenceFrame Name="Mercury" Enabled="True">
    <LayerGroup Name="New Mercury Group" Enabled="True" />
</ReferenceFrame>
<ReferenceFrame Name="Venus" Enabled="True" />
<ReferenceFrame Name="Earth" Enabled="True">
    <Layer Name="EarthQuakes2009" ID="712cdef2-907f-4fc5-ae2c-c57488be4517" Type="SpreadSheetLayer" Enabled="True" />
    <ReferenceFrame Name="Moon" Enabled="True" />
    <ReferenceFrame Name="ISS" Enabled="True" />
</ReferenceFrame>
<ReferenceFrame Name="Mars" Enabled="True" />
<ReferenceFrame Name="Jupiter" Enabled="True">
    <ReferenceFrame Name="Io" Enabled="True" />
    <ReferenceFrame Name="Europa" Enabled="True" />
    <ReferenceFrame Name="Ganymede" Enabled="True" />
    <ReferenceFrame Name="Callisto" Enabled="True" />
</ReferenceFrame>
<ReferenceFrame Name="Saturn" Enabled="True" />
<ReferenceFrame Name="Uranus" Enabled="True" />
<ReferenceFrame Name="Neptune" Enabled="True" />
<ReferenceFrame Name="Pluto" Enabled="True" />
</ReferenceFrame>
</LayerList>
</LayerApi>
```

If the *layersonly* parameter is true, the response might be:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<LayerApi>
  <Status>Success</Status>
  <LayerList>
    <Layer Name="EarthQuakes2009" ID="712cdef2-907f-4fc5-ae2c-c57488be4517" Type="SpreadSheetLayer"  Enabled="True" />
  </LayerList>
</LayerApi>
```

---

# load

The **load** command specifies a data file, and and some optional parameters,
to apply to a new layer.

Files that can be loaded using this commend include spreadsheet data (comma or
tab delimited text), shape files (`.shp`), 3D model files (`.3ds`), and WTML
files containing `ImageSet` references. In the latter case the first image set
found in the file will be loaded. Refer to the
[WorldWide Telescope Data Files Reference](http://www.worldwidetelescope.org/Docs/WorldWideTelescopeDataFilesReference.html)
for more details on WTML files.

If a spreadsheet of data is loaded, the **load** command will initiate an
import wizard of WorldWide Telescope, in order to select the appropriate
columns, timing data, and so on. An import wizard is not automatically invoked
by the other data file types.

Note that parameter names are case-sensitive.

| Required Parameters | Description |
| :-- | :-- |
| *frame* | The reference frame of the group. |
| *filename* | The full path of the file to load. |

| Optional Parameters | Description | Default Value |
| :-- | :-- | :-- |
| *name* | A friendly name for the layer. | "New Layer" |
| *color* | ARGB hex value of the color to be used when rendering the events of the layer. | FFFFFFFF (white) |
| *startdate* | With time series data, the date and time to start the visualization for this layer. This could for example be slightly earlier than the date of the first event in the actual data. | The System minimum date value |
| *enddate* | With time series data, the date and time to end the visualization for this layer. | The System maximum date value |
| *fadetype* | Fades the data visualization. One of: `In`, `Out`, `Both` or `None`. | None |
| *faderange* | Fade time, in days. | Zero |

The reference frame can be a layer group created with the **group** command,
or one of: `Earth`, `Moon`, `Mercury`, `Venus`, `Mars`, `Jupiter`, `Saturn`,
`Uranus`, `Neptune`, `Pluto`, `Io`, `Ganymede`, `Callisto`, `Europa`, `Sun`,
or `ISS`.

The *startdate* and *enddate* values use MM/DD/YYYY structure and may take the
formats `1/1/2000 12:30:30 AM`, `1/1/2010 11:00:00 PM`, `1/1/2010 11:30 AM`,
`1/1/2010 11 am`, `1/1/2000`, `1/2000`.

The following string will be included in the response string:

```xml
<LayerApi><NewLayerID>{NNNN}</NewLayerID></LayerApi>
```

Where `{NNNN}` is the layer id, which will be a GUID in string format.

If the call is not successful the following string may be included in the
response, or other errors may be returned:

```xml
<Status>Error - Could not Load Layer</Status>
```

#### Example: Load Layer Data

Note that the example code uses *datetime*, one of the
[general parameters](#general-parameters), to initiate the visualization of
the data immediately.

```cs
private void loadDatafile(string name, string frame, string filename, string datetime)
{
    try
    {
        WebClient client = new WebClient();
        string url = string.Format(
          "http://{0}:5050/layerApi.aspx?cmd=load&frame={1}&filename={2}&name={3}&datetime={4}",
          getIP().ToString(), frame, filename, name, datetime
        );
        string response = client.UploadString(url, "");
        XmlDocument doc = new XmlDocument();
        doc.LoadXml(response);
        XmlNode node = doc["LayerApi"];
        XmlNode child = node.ChildNodes[0];
        layerId = child.InnerText;
        string s = node.InnerText;

        //
        // Handle an error situation
        //
        if (s.Contains("Error"))
        {
            throw new Exception(s);
        }
    }
    catch (Exception ex)
    {
        // Handle error.
    }
}
```

---

# mode

The **mode** command changes the WWT view mode to one of **Earth**,
**Planet**, **Sky**, **Panorama**, or **SolarSystem**.

| Required Parameters | Description |
| :-- | :-- |
| *lookat* | The view mode to use. |

The following string will be included in the response if the call is successful:

```xml
<Status>Success</Status>
```

#### Example: Change the View Mode

```cs
WebClient client = new WebClient();
string url = string.Format(
  "http://{0}:5050/layerApi.aspx?cmd=mode&lookat={1}", getIP().ToString(), "SolarSystem"
);
string response = client.UploadString(url, "");
```

---

# move

The **move** command changes the view depending on the supplied parameter.

| Required Parameters | Description |
| :-- | :-- |
| *move* | The way to adjust the current view. |

The value of the *move* parameter must be one of the following:

| Mode | Action |
| :-- | :-- |
| **ZoomIn** | Zoom in on the current view. |
| **ZoomOut** | Zoom out of the current view. |
| **Up** | Move the current view up. |
| **Down** | Move the current view down. |
| **Left** | Move the current view left. |
| **Right** | Move the current view right. |
| **Clockwise** | Rotate the view clockwise by 0.2 radians. |
| **CounterClockwise** | Rotate the view counterclockwise by 0.2 radians. |
| **TiltUp** | Angle the view up by 0.2 radians. |
| **TiltDown** | Angle the view down by 0.2 radians. |

The following string will be included in the response if the call is successful:

```xml
<Status>Success</Status>
```

The following code will be included in the response if the move parameter is invalid:

```xml
<Status>Error - Invalid parameter</Status>
```

#### Example code: Zoom the View In

```cs
WebClient client = new WebClient();
string url = string.Format(
  "http://{0}:5050/layerApi.aspx?cmd=move&move={1}",
  getIP().ToString(), "ZoomIn"
);
string response = client.UploadString(url, "");
```

---

# new

The **new** command specifies that a new layer should be created.

The **new** command will request that an entirely new layer be created, with
the following parameters.

| Required Parameters | Description |
| :-- | :-- |
| *frame* | The reference frame of the layer. |

| Optional Parameters | Description | Default Value |
| :-- | :-- | :-- |
| *name* | A friendly name for the layer. | "New Layer" |
| *color* | ARGB hex value of the color to be used when rendering the events of the layer. | FFFFFFFF (white) |
| *startdate* | With time series data, the date and time to start the visualization for this layer. This could for example be slightly earlier than the date of the first event in the actual data. | The System minimum date value |
| *enddate* | With time series data, the date and time to end the visualization for this layer. | The System maximum date value |
| *fadetype* | Fades the data visualization. One of: `In`, `Out`, `Both` or `None`. | None |
| *faderange* | Fade time in days. | Zero |

The reference frame can be a layer group created with the **group** command,
or one of: `Earth`, `Moon`, `Mercury`, `Venus`, `Mars`, `Jupiter`, `Saturn`,
`Uranus`, `Neptune`, `Pluto`, `Io`, `Ganymede`, `Callisto`, `Europa`, `Sun`,
or `ISS`.

The *startdate* and *enddate* values use MM/DD/YYYY structure and may take the
formats `1/1/2000 12:30:30 AM`, `1/1/2010 11:00:00 PM`, `1/1/2010 11:30 AM`,
`1/1/2010 11 am`, `1/1/2000`, `1/2000`.

Along with the URL parameters, data should be uploaded formatted as a comma-
or tab-delimited string containing the names of the columns in the layer. The
supported column names are listed in the following table. The names of the
columns in the data file must _contain_ the names listed here (so `Latitude`
is acceptable for `Lat`, or `dMag` acceptable for `Mag`, for example).

| Column Heading | Description |
| :-- | :-- |
| *Time* | UTC time, for example: `1/1/2000 12:02:46 AM` |
| *Lat* | Latitude in decimal degrees. |
| *Lon* | Longitude in decimal degrees. |
| *RA* | Right ascension in decimal degrees. |
| *Dec* | Declination in decimal degrees. |
| *Depth* | Depth in kilometers. |
| *Altitude* | Altitude in meters. |
| *Mag* | Magnitude as a floating point number. |
| *X* | X co-ordinate, if a Rectangular co-ordinate system is being used. |
| *Y* | Y co-ordinate.|
| *Z* | Z co-ordinate. |

The following string will be included in the response string:

```xml
<LayerApi><NewLayerID>{NNNN}</NewLayerID></LayerApi>
```

Where `{NNNN}` is the layer ID, which will be a GUID in string format. If a
GUID is not received, then the call was not successful.

#### Example: Uploading Earthquake Data

Note that the example code uses **datetime**, one of the
[general parameters](#general-parameters), to initiate the visualization of
the data immediately.

```cs
//
// Send a NEW command, extracting info from the data file and the UI
//
WebClient client = new WebClient();
string datetime =  "1/1/2009 12:00:00 AM";
string name = "Earthquakes 2009";
string rate = "10000";
string frame = "Earth";
string color = "FFFF0000";

string url = string.Format(
  "http://{0}:5050/layerApi.aspx?cmd=new&datetime={1}&timerate={2}&name={3}&frame={4}&color={5}",
  getIP().ToString(), datetime, rate, name, frame, color
);

string response = client.UploadString(url, "TIME\tLAT\tLON\tDEPTH\tMAG");
XmlDocument doc = new XmlDocument();
doc.LoadXml(response);
XmlNode node = doc["LayerApi"];
XmlNode child = node.ChildNodes[0];
layerId = child.InnerText;

//
// Handle an error situation
//
if (layerId.Length != 36)
    throw new Exception("Invalid Layer Id received");
```

For an example of the use of this command in the sample application, see
[initWWTLayer](@/sampleapplication/index.md#sample-program).

---

# setprop

The **setprop** command is used to specify a value for a single layer property.

| Required Parameters | Description |
| :-- | :-- |
| *id* | Specifies the id number of the layer. |
| *propname* | Property name. |
| *propvalue* | Property value in string form. |

Layer properties are documented in
[the next section](@/properties.md#layer-properties).

If the call is successful, then the response will contain the string:

```xml
<Status>Success</Status>
```

If the call is not successful the following string may be included in the
response, or other errors may be returned:

```xml
<Status>Error - Invalid parameter</Status>
```

#### Example Code: Set Layer Opacity

```cs
WebClient client = new WebClient();
string url = string.Format(
  "http://{0}:5050/layerApi.aspx?cmd=setprop&id={1}&propname={2}&propvalue={3}",
  getIP().ToString(), layerId,"Opacity", "0.5"
);
string response = client.UploadString(url, "");
```

---

# setprops

The **setprops** command is used to specify multiple properties for a layer.

| Required Parameters | Description |
| :-- | :-- |
| *id* | Specifies the id number of the layer. |

Along with the URL parameters, the property values should be uploaded as an
XML document containing `LayerApi` and `Layer` entries. Each attribute to the
`Layer` entry should be a property name/property value pair. Layer properties
are documented in [the next section](@/properties.md#layer-properties).

If the call is successful, then the response will contain the string:

```xml
<Status>Success</Status>
```

If the call is not successful the following string may be included in the
response, or other errors may be returned:

```xml
<Status>Error - Invalid parameter</Status>
```

#### Example Code: Set BeginRange and Decay properties

```cs
string propertyXML = "<?xml version='1.0' encoding='UTF-8'?><LayerApi><Layer BeginRange="1/9/2008 11:44:38 AM" Decay="5.5" /></LayerApi>";
WebClient client = new WebClient();
string url = string.Format(
  "http://{0}:5050/layerApi.aspx?cmd=setprops&id={1}",
  getIP().ToString(), layerId
);
string response = client.UploadString(url, propertyXML);
```

---

# state

The **state** command requests some details of the current view.

This command does not take any parameters. The details returned vary slightly
depending on the view mode, and include the current view mode, latitude (or
declination) in decimal degrees, longitude (or right ascension) in decimal
degrees, zoom factor (0 to 360), angle in radians, rotation in radians,
current UTC time, time rate, reference frame, a view token, and zoom text.

If the call is successful the following string will be returned:

```xml
<LayerApi><Status>Success</Status><ViewState> [state information](#State_information) </ViewState></LayerApi>
```

#### Example code: Get View State

```cs
private void getState()
{
   try
   {
       WebClient client = new WebClient();
       string url = string.Format(
         "http://{0}:5050/layerApi.aspx?cmd=state",
         getIP().ToString()
       );
       string response = client.UploadString(url, "");
       XmlDocument doc = new XmlDocument();
       doc.LoadXml(response);
       XmlNode node = doc["LayerApi"];
       if (node.InnerText.Contains("Success"))
       {
           XmlNode node2 = node["ViewState"];

           if (node2 != null)
           {
               string propName;
               string propValue;

               for (int i = 0; i < node2.Attributes.Count; i++)
               {
                   propName = node2.Attributes[i].Name;
                   propValue =  node2.Attributes[i].Value;
                   // Do something with the name value pair
               }
           }
           else
           {
              // Success message was a false positive, no state was returned
           }
       }
       else
       {
           throw new Exception(response);
       }
   }
   catch (Exception ex)
   {
      // Handle the exception
   }
}
```

In `Earth` mode, the state information looks like:

```xml
<ViewState
   lookat="Earth"
   lat="86.1800140779305"
   lng="4.56191579793961"
   zoom="30.9237645312"
   angle="-1.52"
   rotation="0"
   time="2/23/2011 7:03:31 PM"
   timerate="1"
   ReferenceFrame="Earth"
   ViewToken="SD8834DFA"
   zoomText="5085 km">
</ViewState>
```

In `Planet` mode, the state information looks like:

```xml
<ViewState
   lookat="Planet"
   lat="86.1800140779305"
   lng="4.58610934632668"
   zoom="360"
   angle="-1.52"
   rotation="0"
   time="2/23/2011 7:04:19 PM"
   timerate="1"
   ReferenceFrame="Mars"
   ViewToken="SD8834DFA"
   zoomText="59200 km">
</ViewState>
```

In `Sky` mode, the state information looks like:

```xml
<ViewState
   lookat="Sky"
   ra="10.2626615902966"
   dec="46.1799337621283"
   zoom="30.9237645312"
   rotation="0"
   time="2/23/2011 7:04:51 PM"
   timerate="1"
   ReferenceFrame="Space"
   ViewToken="SD8834DFA"
   zoomText="05:09:14">
</ViewState>
```

In `Panorama` mode, the state information looks like:

```xml
<ViewState
   lookat="Panorama"
   lat="-3.07957843596986"
   lng="-39.9196238171819"
   zoom="117.9648"
   angle="-1.52"
   rotation="0"
   time="2/23/2011 7:05:35 PM"
   timerate="1"
   ReferenceFrame="Panorama"
   ViewToken="SD8834DFA"
   zoomText="19:39:39">
</ViewState>
```

In `SolarSystem` (3D) mode, the state information looks like:

```xml
<ViewState
   lookat="SolarSystem"
   lat="2.25294244185011"
   lng="262.382989924732"
   zoom="0.000578857064151695"
   angle="-1.52"
   rotation="0"
   time="2/23/2011 7:06:13 PM"
   timerate="1"
   ReferenceFrame="Sun"
   ViewToken="SD8834DFA"
   zoomText="38636 km">
</ViewState>
```

---

# uisettings

The **uisettings** command is used to change user interface settings. It does
not alter layer data.

This command can take any number of named parameters corresponding to the UI
settings documented in [the next section](@/properties.md#wwt-ui-settings).
Spelling mistakes and capitalization must be emulated precisely.

If the call is successful, then the response will contain the string:

```xml
<Status>Success</Status>
```

#### Example: Show Constellation Figures

```cs
WebClient client = new WebClient();
string  url = string.Format(
  "http://{0}:5050/layerApi.aspx?cmd=uisettings&ShowConstellationFigures=True",
  getIP().ToString()
);
string response = client.UploadString(url, "");
```

---

# update

The **update** command specifies that the data attached to this command should
be added to the layer.

| Required Parameters | Description |
| :-- | :-- |
| *id* | Specifies the ID number of the layer. |

| Optional Parameters | Description | Default Value |
| :-- | :-- | :-- |
| *hasheader* | Set to true if the data has a header row. The header should be the first row of the data. | False |
| *name* | A friendly name to rename the layer. | No change |
| *nopurge* | The sending of an update command will delete rows that (1) occur before the start time of any events in the update and (2) that have already decayed. Set this flag to true if the event data should not be deleted. | False |
| *purgeall* | Delete all events. | False |
| *show* | Set to true to show the layer, false to hide it. "True" and "false" are not case-sensitive. | True |

The parameters should be accompanied with an upload of comma or tab delimited
data in a form that matches the heading names of the columns in the layer
provided in the [**new**](#new) command.

The following string will be included in the response if the call is successful:

```xml
<Status>Success</Status>
```

If the call is not successful the following string may be included in the
response, or other errors may be returned:

```xml
<Status>Error - Invalid layer ID</Status>
```

#### Example Code: Update Data

```cs
//
// Send an UPDATE command
//
string lineBuffer = "... some kind of data ...";

WebClient client = new WebClient();
string url = string.Format(
  "http://{0}:5050/layerApi.aspx?cmd=update&id={1}",
  getIP().ToString(), layerId
);
string response = client.UploadString(url, lineBuffer);
XmlDocument doc = new XmlDocument();
doc.LoadXml(response);
XmlNode node = doc["LayerApi"];
string s = node.InnerText;

//
// Handle an error situation
//
if (s.Contains("Error"))
{
    throw new Exception(s);
}
```

#### Example Code: Update Data with a flyTo Parameter

```cs
//
// Send an UPDATE command with a flyTo parameter
//
string lat = "...", lon, zoom, rotation, angle;

WebClient client = new WebClient();
string url = string.Format(
  "http://{0}:5050/layerApi.aspx?cmd=update&id={1}&flyTo={2},{3},{4},{5},{6}&instant=True",
  getIP().ToString(), layerId, lat, lon, zoom, rotation, angle
);
string response = client.UploadString(url, lineBuffer);
XmlDocument doc = new XmlDocument();
doc.LoadXml(response);
XmlNode node = doc["LayerApi"];
string s = node.InnerText;

//
// Handle an error situation
//
if (s.Contains("Error"))
{
    throw new Exception(s);
}
```

For an example of the use of this command in the sample application, see
[flushBufferToWWT](@/sampleapplication/index.md#sample-program).

---

# version

The **version** command returns the version number of the running version of
the LCAPI.

The version number should be used in an application to ensure that the LCAPI
features used are supported by the version of the LCAPI running on the client
computer.

The following string will be included in the response if the call is
successful (the version number will be more recent than the one shown below):

```xml
<LayerApi><Version>2.8.21.1</Version></LayerApi>
```

There are no specific errors returned if the call is not successful.

Refer to the [getIP](@/utilities.md#getip) utility to see how to extract and check a version number.

---

# General Parameters

The general parameters can be used with any of the commands.

| Parameter | Description |
| :-- | :-- |
| *datetime* | Sets the viewing clock to the given date and time, in UTC format. |
| *timerate* | The accelerated time to render the visualization, as a multiple of 10. |
| *flyto* | Sets the position of the view camera as described below. |
| *instant* | Used with the *flyto* parameter, set this to *true* to specify that the camera should jump to the location, or *false* that the camera should smoothly pan and zoom to the location. |
| *autoloop* | True sets the layer manager to auto loop. |

The *datetime* values uses MM/DD/YYYY structure and may take the formats
`1/1/2000 12:30:30 AM`, `1/1/2010 11:00:00 PM`, `1/1/2010 11:30 AM`, `1/1/2010
11 am`, `1/1/2000`, or `1/2000`.

The *flyTo* parameter sets the position of the view camera. It takes five or
six comma-separated parameters:

1. Latitude is in decimal degrees, positive to the North.
2. Longitude is in decimal degrees, positive to the East.
3. Zoom level varies from 360 (the most distant view) to 0.00023 (the closest
   view).
4. Rotation is in radians, positive moves the camera to the left.
5. Angle is in radians, positive moves the camera forward.
6. Optionally, there can be a sixth parameter containing the frame to change
   the view to, which can be one of: `Earth`, `Moon`, `Mercury`, `Venus`,
   `Mars`, `Jupiter`, `Saturn`, `Uranus`, `Neptune`, `Pluto`, `Io`,
   `Ganymede`, `Callisto`, `Europa`, `Sun`, `ISS`.*

#### Example Code: Fly to a Location

```cs
//
// Extract the values from the text boxes of a dialog, assuming there
// are rich text boxes with the names shown below.
//
string flyString = richLat.Text + "," + richLong.Text + "," + richZoom.Text + "," + richRotation.Text + "," + richAngle.Text;

WebClient client = new WebClient();
string url = string.Format(
  "http://{0}:5050/layerApi.aspx?cmd=state&flyto={1}&instant={2}",
  getIP().ToString(), flyString, richInstant.Text
);
string response = client.UploadString(url, "");
```
