+++
title = "LCAPI Utilities"
weight = 300
+++

The following C# utility functions might be useful in building an application.

* [**getIP**](#getip)
* [**getUTCtime**](#getutctime)

---

# getIP

If the LCAPI application and AAS WorldWide Telescope are running on the same
computer, you can use the following utility to extract the IP address.

The **getIP** function extracts each IP address in turn, then calls the
**CheckForWWTWebServer** function to check that WorldWide Telescope is both
running at the given address and is recent enough that the LCAPI is supported.

```cs
private IPAddress getIP()
{
    IPAddress ipAddress = IPAddress.Loopback;

    // Find IPV4 Address

    foreach (IPAddress ipAdd in Dns.GetHostEntry(Dns.GetHostName()).AddressList)
    {
        if (ipAdd.AddressFamily == AddressFamily.InterNetwork)
        {
            if (CheckForWWTWebServer(ipAdd))
            {
                ipAddress = ipAdd;
                break;
            }
        }
    }

    return ipAddress;
}

private bool CheckForWWTWebServer(IPAddress address)
{
    WebClient client = new WebClient();

    try
    {
        string version = client.DownloadString(
          string.Format(
            "http://{0}:5050/layerapi.aspx?cmd=version", 
            address.ToString()
          )
        );
        XmlDocument doc = new XmlDocument();
        doc.LoadXml(version);
        XmlNode node = doc["LayerApi"];

        if (node.OuterXml.Contains("Version"))
        {
            // As version numbers are in the format xx.xx.xx.xx, an individual
            // comparison of each number is necessary - a simple string comparison
            // will give unreliable results.

            string[] versionNumbers = node.InnerText.Split('.');
            if (versionNumbers[0].CompareTo("2") >= 0 &&
                versionNumbers[1].CompareTo("8") >= 0)
                return true;
        }
    }
    catch (Exception ex)
    {
        // handle exception
    }
    return false;
}
```

---

# getUTCtime

With time series data care should be taken with the timings given in the data
file. To convert local times to the recommended UTC time the following
function could be used. Note that the local time is taken from the computer
System time in the example below, so you may need to add some functionality to
support local times that are not the same as your computer system time.

```cs
//
// Convert a string time to the correct format
//
private string getUTCtime(string time)
{
    DateTime d = DateTime.Parse(time);
    string s = d.ToUniversalTime().ToString();
    return s;
}
```
