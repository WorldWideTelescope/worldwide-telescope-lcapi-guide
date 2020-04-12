+++
title = "LCAPI Design Overview"
weight = 100
+++

The Windows client version of AAS WorldWide Telescope listens continuously,
and without any setup process, for a certain format of communication on a
particular port. The format of this communication is in the form of strings,
that themselves are formatted in a specific way. An application may be quite
short and only need a [**load**](@/lcapicommands.md#load) call, or perhaps
just need the [**new**](@/lcapicommands.md#new) and
[**update**](@/lcapicommands.md#update) commands. Typically a simple
application will go through the following steps, _after_ AAS WorldWide
Telescope has been started:

1. Determine the IP address of the computer running AAS WorldWide Telescope.
   If this is the same computer as the application use the
   [**getIP**](@/utilities.md#getip) utility, if not a utility will have to be
   written or acquired that establishes the remote IP address.
2. Initialize and open a data file, or files, from a selected source.
3. Initialize the connection with AAS WorldWide Telescope with a
   [**new**](@/lcapicommands.md#new) or [**load**](@/lcapicommands.md#load)
   call, which will create a new layer within WorldWide Telescope, and return
   a layer ID number.
4. If the [**new**](@/lcapicommands.md#new) command is used:
   1. Fill a string buffer with data from the opened files.
   2. Transmit the data to WorldWide Telescope with an
      [**update**](@/lcapicommands.md#update) call, using the layer ID number
      for reference.
   3. Repeat this step until all the data has been transmitted.
5. Use the [**getprop**](@/lcapicommands.md#getprop),
   [**setprop**](@/lcapicommands.md#setprop),
   [**uisettings**](@/lcapicommands.md#uisettings) commands and parameters to
   control the view in WWT from the LCAPI application.
6. Close down.

A more complex application may initialize several layers, or a layer group,
within WorldWide Telescope, or may transmit the data under user control —
enabling the user to have some choice over which data is rendered, the speed
and time of the simulation, and other similar controls.

# Development Environment

The samples and examples described in this document have been developed using
Microsoft Visual Studio and the .NET framework, and written in C#.

The suggested development approach is first to get the sample application
working, then to modify it as required using the code examples for each
command. Note that the code examples themselves are not runnable code, but
just guidelines on how the calls can be made.
