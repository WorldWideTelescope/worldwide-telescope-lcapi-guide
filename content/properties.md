+++
title = "Layer and UI Properties"
weight = 300
+++

# Layer Properties

The following table lists the properties that can be get or set on a layer.
The property names and enumeration values are case-sensitive.

| Property | Type | Description | Layer Type | Default |
| :-- | :-- | :-- | :-- | :-- |
| *AltColumn* | Int | Column number containing the altitude/depth data. | Spreadsheet | -1 |
| *AltType* | Enum | One of: `Depth`, `Altitude`, `SeaLevel`, `Terrain`. | Spreadsheet | SeaLevel |
| *AltUnit* | Enum | One of: `Meters`, `Feet`, `Inches`, `Miles`, `Kilometers`, `AstronomicalUnits`, `LightYears`, `Parsecs`, `MegaParsecs`. | Spreadsheet | `Meters` for altitude and `Kilometers` for depth. |
| *Astronomical* | Boolean | True if the data are astronomical rather than terrestrial. If this is set to true the declination will be by default the Latitude column, and the right ascension the Longitude column. | All | False |
| *BeginRange* | DateTime | Date and time of the first data entry in a time series, in one of the formats (MM/DD/YYY): `1/1/2010 11:00:00 PM`, `1/1/2010 11:30 AM`, `1/1/2010 11 am`,`1/1/2000`, `1/2000` | Spreadsheet | Maximum system value |
| *CartesianCustomScale* | Double | Used to divide the Cartesian coordinate values to a custom scale. If this value was set to "0.01" and the scale to Meters, the custom scale would be centimeters. | Spreadsheet | 1 |
| *CartesianScale* | Enum | If the *CoordinatesType* is not `Spherical` then the coordinate system uses X, Y and Z values. One of: `Meters`, `Feet`, `Inches`, `Miles`, `Kilometers`, `AstronomicalUnits`, `LightYears`, `Parsecs`, `MegaParsecs`, `Custom`. | Spreadsheet | `Meters` |
| *ColorMap* | Enum | One of: `Same_For_All`, `Group_by_Values`. | Spreadsheet | `Same_For_All` |
| *ColorMapColumn* | Int | Column number containing the color map data. Columns are numbered from zero; -1 indicates there is no data for this. | Spreadsheet | -1 |
| *ColorValue* | String | String containing ARGB value of the color, in the format: `ARGBColor:255:255:255:255`. | All | White |
| *CoordinatesType* | Enum | One of: `Spherical`, `Rectangular`, `Orbital`. `Spherical` applies when the reference frame is a sphere, and the coordinates will be Lat/Lng or RA/Dec. `Rectangular` applies if there are X, Y and Z coordinates. In this case 0,0,0 is the center of the reference frame. | Spreadsheet | `Spherical` |
| *Decay* | Float | Decay rate of the visualization, in days, in the range 0.00025 to 4096. | Spreadsheet | 16 |
| *Enabled* | Boolean | True if the layer is enabled. | All | True |
| *EndDateColumn* | Int | Column containing the end date/time data. | Spreadsheet | -1 |
| *EndRange* | DateTime | Date and time of the last data entry in a time series, in one of the formats supported by *BeginRange* | Spreadsheet | Minimum system value |
| *EndTime* | DateTime | Date and time to end the visualizations, and return to the start time in the case of an auto loop. This property is visible in the Lifetime dialog for a layer. Formats as in *BeginRange* | All | Maximum system time. |
| *GeometryColumn* | Int | Column containing the geometry data. | Spreadsheet | -1 |
| *Heading* | Double | Heading angle of the model, in radians. | 3D Model | 0 |
| *HyperlinkColumn* | Int | Column containing the hyperlink data. | Spreadsheet | -1 |
| *HyperlinkFormat* | String | ??? | Spreadsheet | "" |
| *FadeSpan* | Time | The time it takes to fade out the graphic. This property is visible in the *Lifetime* dialog for the layer and is shown in days in the dialog. When using the SDK use the format `D.HH:MM:SS`, so for one day use `1.00:00:00`. | All | 0 |
| *FadeType* | Enum | One of: `In`, `Out`, `Both`, `None`. | All | None |
| *FlipV* | Boolean | True if the textures for the 3D model should be flipped. | 3D Model | False |
| *LatColumn* | Int | Column number for the Latitude data. | Spreadsheet | -1 |
| *LngColumn* | Int | Column number for the Longitude data. | Spreadsheet | -1 |
| *MarkerColumn* | Int | Column number for the marker data. | Spreadsheet | -1 |
| *MarkerIndex* | Int | Index number of marker graphic selected, or -1 if no marker has been selected. | Spreadsheet | -1 |
| *MarkerMix* | Enum | One of: `Same_For_All`, `Group_by_Values`. | Spreadsheet | `Same_For_All` |
| *MarkerScale* | Enum | One of: `Screen`, `World`. If this is set to `Screen` the graphic will not change in size as the viewer zooms in and out. If it is set to `World` the graphic (`Pushpin` or `Gaussian`) will scale with the view. | Spreadsheet | World |
| *Name* | String | Name of the layer. | All | "New layer" |
| *NameColumn* | Int | Column containing the event name data. | Spreadsheet | -1 |
| *Opacity* | Float | Opacity of the layer graphics from 0.0 to 1.0. | All | 1 |
| *Pitch* | Double | Pitch angle of the model in radians. | 3d Model | 0 |
| *PlotType* | Enum | One of: `Gaussian`, `Point`, `PushPin`. | Spreadsheet | Gaussian |
| *PointScaleType* | Enum | One of: `Linear`, `Power`, `Log`, `Constant`, `StellarMagnitude`. | Spreadsheet | Power |
| *RaUnits* | Enum | When the longitude column is being used for RA in astronomical data. One of: `Hours`, `Degrees`. | Spreadsheet | Hours |
| *Roll* | Double | Roll angle of the model, in radians. | 3D Model | 0 |
| *Scale* | Vector3d | Scale of the model in x, y and z dimensions. Format: 1,1,1 | 3D Model | 1,1,1 |
| *ScaleFactor* | Float | Scale factor to apply to the magnitude of the event. | Spreadsheet | 1 |
| *ShowFarSide* | Boolean | True if the data points on the invisible side of the body should be shown. | Spreadsheet | False |
| *SizeColumn* | Int | Column containing the size (magnitude) data. | Spreadsheet | -1 |
| *Smooth* | Boolean | True if the 3D Model should have its normals smoothed. | 3D Model | True |
| *StartDateColumn* | Int | Column containing the start date/time data. | Spreadsheet | -1 |
| *StartTime* | DateTime | Date and time to start the visualizations. This property is visible in the Lifetime dialog for the layer. Formats as in *BeginRange* | All | Minimum system time |
| *TimeSeries* | Boolean | True if the layer should be treated as time series data. | Spreadsheet | False |
| *Translate* | Vector3d | Translation (movement offset) of the model in x, y and z dimensions, in units of the model size. Format: 1,1,1 | 3D Model | 0,0,0 |
| *XAxisColumn* | Int | Column number for X data. | Spreadsheet | -1 |
| *YAxisColumn* | Int | Column number for Y data. | Spreadsheet | -1 |
| *ZAxisColumn* | Int | Column number for Z data. | Spreadsheet | -1 |
| *XAxisReverse* | Boolean | True if the direction of the X axis is reversed. | Spreadsheet | False |
| *YAxisReverse* | Boolean | True if the direction of the Y axis is reversed. | Spreadsheet | False |
| *ZAxisReverse* | Boolean | True if the direction of the Z axis is reversed. | Spreadsheet | False |


# WWT UI Settings

Note that the spelling errors in the names of the properties must be matched.

| Setting Name | Setting Type |
| :-- | :-- |
| *AutoHideContext* | True or False |
| *AutoHideTabs* | True or False |
| *AutoRepeatTour* | True or False |
| *AutoRepeatTourAll* | True or False |
| *ConstellationBoundryColor* | RGB color (default, "MidnightBlue") |
| *ConstellationFigureColor* | RGB color (default, "DarkRed") |
| *ConstellationFiguresFile* | String (a filename) |
| *ConstellationSelectionColor* | RGB color (default, "DarkGoldenrod") |
| *ContextSearchFilter* | String (default "Unfiltered") |
| *DomeTilt* | Double (default "0") |
| *DomeTypeIndex* | Integer (default, "0") |
| *DomeView* | True or False |
| *EclipticColor* | RGB color (default, "CornflowerBlue ") |
| *FollowMouseOnZoom* | True or False |
| *FovCamera* | Integer (default, "0") |
| *FovColor* | RGB color (default, "white") |
| *FovEyepiece* | Integer (default, "0")|
| *FovTelescope* | Integer (default, "0") |
| *FullScreenTours* | True or False |
| *GridColor* | RGB color (default, "64, 64, 64") |
| *ImageQuality* | Integer: 0 to 100 (default, 100)|
| *LargeDomeTextures* | True or False |
| *LastLookAtMode* | Integer (default, "2")|
| *LineSmoothing* | True or False |
| *ListenMode* | True or False |
| *LocalHorizonMode* | True or False |
| *LocationAltitude* | Double (default, "100") |
| *LocationLat* | Double (default, "47.64222") |
| *LocationLng* | Double (default, "-122.142")|
| *LocationName* | String (default, "Microsoft Research Building 99") |
| *MasterController* | True or False |
| *ShowClouds* | True or False |
| *ShowConstellationBoundries* | True or False |
| *ShowConstellationFigures* | True or False |
| *ShowConstellationNames* | True or False |
| *ShowConstellationSelection* | True or False |
| *ShowCrosshairs* | True or False |
| *ShowDatasetNames* | True or False |
| *ShowEarthSky* | True or False |
| *ShowEcliptic* | True or False |
| *ShowElevationModel* | True or False |
| *ShowFieldOfView* | True or False |
| *ShowGrid* | True or False |
| *ShowSolarSystem* | True or False |
| *ShowTouchControls* | True or False |
| *ShowUTCTime* | True or False |
| *SolarSystemCosmos* | True or False |
| *SolarSystemLighting* | True or False |
| *SolarSystemMilkyWay* | True or False |
| *SolarSystemMinorOrbits* | True or False |
| *SolarSystemMinorPlanets* | True or False |
| *SolarSystemMultiRes* | True or False |
| *SolarSystemOrbitColor* | RGB color (default, "64, 64, 64") |
| *SolarSystemOrbits* | True or False |
| *SolarSystemOverlays* | True or False |
| *SolarSystemPlanets* | True or False |
| *SolarSystemScale* | True or False |
| *SolarSystemStars* | True or False |
| *StartUpLookAt** | Integer (default, "4") |
