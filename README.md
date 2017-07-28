# WorldWide Telescope Layer Control API (LCAPI)

This document describes how to write a software program to send data to WorldWide Telescope, using the Layer Control API (LCAPI). For a large amount of data the program might be a tool that reads data from a spreadsheet file, and then sends the appropriate fields of the data to WorldWide Telescope in appropriately sized buffers for visualization. For a smaller amount of data the entire file could be loaded in one go, or images or 3D models could be loaded. Time series data would typically be time-and-location dependent event data such as:

*   Earthquakes
*   Volcanic eruptions
*   Disease outbreaks
*   More general social or natural events and trends

The time-series system does _not_ lend itself to data that varies either its location or has a complex intensity - such as weather systems, or forest fires - though it is possible a limited approach to this sort of data may be useful. The system does lend itself to events that occur over an extended period of time, given the ability to greatly accelerate simulated time, and also events that decay very rapidly (lightning) or quite slowly (diseases) given the ability to control the decay time of the rendered graphic. Although most examples are of time series events on the Earth, the events can be on any of the supported Solar System bodies, or simply the Sky.

There are a number of constraints on the use of the LCAPI that should be understood before commencing with the design of the tool:

*   The supported spreadsheet formats are comma or tab delimited files.
*   The LCAPI is a feature of the Windows Client version of WorldWide Telescope, not the Web Client.
*   The LCAPI provides event, image or model data specifically to the Layer Manager, not to any other component of WorldWide Telescope.
