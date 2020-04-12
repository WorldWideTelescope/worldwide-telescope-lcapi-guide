+++
title = "WWT Layer Control API Guide"
sort_by = "weight"
insert_anchor_links = "right"
+++

This document describes how to write a software program to send data to the
AAS WorldWide Telescope Windows client using its Layer Control API (LCAPI).

{% note(html=1) %}
The LCAPI interface can only control the WWT Windows application, which means
that it is best suited to creating high-end custom experiences for planetarium
or museum environments. For less specialized applications, such as
visualizating research data in WWT, check out the <a
href="https://pywwt.readthedocs.io/">pywwt</a> Python package.
{% end %}

For a large amount of data the program might be a tool that reads data from a
spreadsheet file, and then sends the appropriate fields of the data to WWT in
appropriately sized buffers for visualization. For a smaller amount of data
the entire file could be loaded in one go, or images or 3D models could be
loaded. Time series data can be time-and-location dependent event
data such as:

* Earthquakes
* Volcanic eruptions
* Disease outbreaks
* More general social or natural events and trends

The time-series system does _not_ lend itself to data that varies either its
location or has a complex intensity — such as weather systems, or forest fires
— though it is possible a limited approach to this sort of data may be useful.
The system does lend itself to events that occur over an extended period of
time, given the ability to greatly accelerate simulated time, and also events
that decay very rapidly (lightning) or quite slowly (diseases) given the
ability to control the decay time of the rendered graphic. Although most
examples are of time series events on the Earth, the events can be on any of
the supported Solar System bodies, or simply the Sky.

There are a number of constraints on the use of the LCAPI that should be
understood before commencing with the design of a client program:

* The supported spreadsheet formats are comma or tab delimited files.
* The LCAPI is a feature of the Windows Client version of WorldWide Telescope,
  not the Web Client.
* The LCAPI provides event, image or model data specifically to the Layer
  Manager, not to any other component of WorldWide Telescope.

In addition to using the layer manager interactively, you can use scripting
and programming languages to send commands and data into WWT from your own
code driving the visualization of data in WWT. You can write code that can
read data from a database, or create it mathematically and send it to WWT to
render. In addition almost all of WWT functionality can be controlled through
the LCAPI. This means you can create control mechanisms for automated or
interactive control of WWT through your own hardware and software.
