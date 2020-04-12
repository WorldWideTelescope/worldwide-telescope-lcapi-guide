+++
title = "Sample LCAPI Application"
weight = 400
+++

The following sample application loads earthquake data from a CSV file, sends it in buffers of up to 100 events, and displays it as a time series in WorldWide Telescope.

![](//wwtweb.blob.core.windows.net/docs/images/LCAPI_sample.jpg)
![](//wwtweb.blob.core.windows.net/docs/images/LCAPI_quakes.jpg)


### Sample program

```cs
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;

//
// Added References
//
using System.IO;
using System.Net;
using System.Net.Sockets;
using System.Xml;

//
//  Quakes is a sample of the WorldWide Telescope LCAPI
//

namespace Quakes
{
    public partial class Quakes : Form
    {
        public Quakes()
        {
            InitializeComponent();
            //
            // Set defaults
            //
            listBox1.SelectedItem = "1000000";
            listBox2.SelectedItem = "Earth";
        }

        private void buttonExit_Click(object sender, EventArgs e)
        {
            Dispose(true);
        }

        private void buttonBrowse_Click(object sender, EventArgs e)
        {
            if (openFileDialog1.ShowDialog() == DialogResult.OK)
            {
                richFilename.Text = openFileDialog1.FileName;
            }
        }

        //
        // Add messages to the list box
        //
        private void addMessage(string m)
        {
            int i = richMessages.Items.Add(m);
            richMessages.SelectedIndex = i;
        }

        //
        // Go!
        //
        private void buttonGo_Click(object sender, EventArgs e)
        {
            string line;
            string w;

            richMessages.Items.Clear();

            try
            {
                initWWTLayer();                             

                StreamReader sr = new StreamReader(richFilename.Text);

                //
                // Ignore first line of data (the headings)
                //
                w = sr.ReadLine();

                //
                // Read lines of data until there are none left
                //
                while ((w = sr.ReadLine()) != null)
                {
                    line = parseLine(w,false);
                    flushBufferToWWT(line);
                }

                sr.Close();

                //
                // Flush buffer for last time - empty string indicates this is the last flush
                //
                flushBufferToWWT("");

                //
                // Record the end
                //
                addMessage("Data transmission ended: " + totalEvents + " events");
            }
            catch (Exception ex)
            {
                addMessage(ex.Message);
            }

        }

        //
        // Change the event color
        //
        private void buttonColor_Click(object sender, EventArgs e)
        {
            if (colorDialog.ShowDialog() == DialogResult.OK)
            {
                buttonColor.BackColor = colorDialog.Color;
            }
        }

        int timeSlot;                   // Data column number for time
        int latSlot;                    // Data column number for latitude
        int lonSlot;                    // Data column number for longitude
        int depSlot;                    // Data column number for depth
        int magSlot;                    // Data column number for magnitude
        string layerId;                 // string GUID for the WWT Layer
        string lineBuffer;              // Buffer to hold events until they are sent
        int lineCountInBuffer;          // Number of events in the buffer
        int totalEvents;                // Count of total events transmitted
        DateTime lastTimeBufferFlushed; // Time of last transmission, used to check for time-out

        //
        // Locate the columns of the required data, they must be present but can be in any
        // order in the data file
        //

        private string initCSVData(string filename)
        {
            string w;           // line of data from data file

            timeSlot = -1;
            latSlot = -1;
            lonSlot = -1;
            depSlot = -1;
            magSlot = -1;

            //
            // First line (line 0) of the data file must contain the column headings
            //
            StreamReader sr = new StreamReader(filename);
            w = sr.ReadLine();
            string[] words = w.Split(new char[] { ',' });

            for (int i = 0; i < words.Length; i++)
            {
                if (words[i].IndexOf("Time", StringComparison.OrdinalIgnoreCase) != -1)
                    timeSlot = i;
                else
                    if (words[i].IndexOf("Lat", StringComparison.OrdinalIgnoreCase) != -1)
                        latSlot = i;
                    else
                        if (words[i].IndexOf("Lon", StringComparison.OrdinalIgnoreCase) != -1)
                            lonSlot = i;
                        else
                            if (words[i].IndexOf("Depth", StringComparison.OrdinalIgnoreCase) != -1)
                                depSlot = i;
                            else
                                if (words[i].IndexOf("Mag", StringComparison.OrdinalIgnoreCase) != -1)
                                    magSlot = i;
            }

            //
            // One of more heading is missing....
            //
            if (timeSlot == -1 || latSlot == -1 || lonSlot == -1 || depSlot == -1 || magSlot == -1)
                throw new Exception("Source file does not contain one or more of: time, lat, lon, depth, mag");

            //
            // Record a successful reading of the headings
            //
            addMessage("Time = " + timeSlot.ToString() + " Lat = " + latSlot.ToString() + " Lon = " + lonSlot.ToString() + " Depth = " + depSlot.ToString() + " Mag = " + magSlot.ToString());

            //
            // Extract the start time from the data file by reading line 1
            //

            w = sr.ReadLine();
            string startDate = parseLine(w, true);
            sr.Close();

            //
            // Record a successful reading of the start
            //
            addMessage("Start time = " + startDate);     

            return startDate;
        }

        //
        // Convert a string time to the correct format
        //
        private string getUTCtime(string time)
        {
            DateTime d = DateTime.Parse(time);
            string s = d.ToUniversalTime().ToString();
            return s;
        }

        //
        // Parses a line from the data file
        //
        private string parseLine(string w, bool timeOnly)
        {
            string[] words = w.Split(new char[] { ',' });
            string line = "";

            string time = getUTCtime(words[timeSlot]);
            string lat = words[latSlot];
            string lon = words[lonSlot];
            string depth = words[depSlot];
            string mag = words[magSlot];

            if (timeOnly)
            {
                return time;
            }
            line = time + "\t" + lat + "\t" + lon + "\t" + depth + "\t" + mag + "\t";

            return line;
        }

        //
        // Create a new WWT layer
        //
        private void <a name="initWWTLayer">initWWTLayer()
        {
            //
            // Send a NEW command, extracting info from the data file and the UI
            //
            WebClient client = new WebClient();
            string startDate = initCSVData(richFilename.Text);
            string name = Path.GetFileNameWithoutExtension(richFilename.Text);
            string rate = listBox1.SelectedItem.ToString();
            string frame = listBox2.SelectedItem.ToString();
            string color = buttonColor.BackColor.ToArgb().ToString("X8"<font size="2">);
            string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=new&datetime={1}&timerate={2}&name={3}&frame={4}&color={5}",  getIP().ToString(), startDate, rate, name, frame, color);
            // field string below is delimited by tabs, not spaces
            string response = client.UploadString(url, "TIME    LAT    LON    DEPTH    MAG");
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

            //
            // Record a successful creation of a new layer
            //
            addMessage("New layer Id = " + layerId);

            //
            // Clear the buffer
            //
            lineBuffer = string.Empty;
            lineCountInBuffer = 0;
            totalEvents = 0;
            lastTimeBufferFlushed = DateTime.Now;
        }

        //
        // Utility to extract IP address
        //
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
                string version = client.DownloadString(string.Format("http://{0}:5050/layerapi.aspx?cmd=version", address.ToString()));
                XmlDocument doc = new XmlDocument();
                doc.LoadXml(version);
                XmlNode node = doc["LayerApi"];

                if (node.OuterXml.Contains("Version"))
                {
                    // As version numbers are in the format xx.xx.xx.xx, an individual comparison of each
                    // number is necessary - a simple string comparison will give unreliable results

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


        //
        // Buffer the data and send it to WWT every N events or every T time units
        //
        private void <a name="flushBufferToWWT">flushBufferToWWT(string line)
        {
            const int FlushThresholdInEventCount = 100;     // Flush the buffer when this number of events is stored
            const int FlushThresholdInSeconds = 2;          // Flush the buffer after this number of seconds has elapsed
            //
            // If line length is zero, this is the last transmission
            //
            if (line.Length > 0)
            {
                lineBuffer = lineBuffer + line + Environment.NewLine;
                lineCountInBuffer++;
            }
            DateTime now = DateTime.Now;

            //
            // Send the buffer if flushing for the last time OR the buffer is full OR the timeout has been reached
            //
            if ((line.Length == 0  lineCountInBuffer > 0) ||
                lineCountInBuffer == FlushThresholdInEventCount ||
                now.Subtract(lastTimeBufferFlushed) > TimeSpan.FromSeconds(FlushThresholdInSeconds))
            {
                //
                // Send an UPDATE command
                //
                WebClient client = new WebClient();

                string url = string.Format("http://{0}:5050/layerApi.aspx?cmd=update&id={1}", getIP().ToString(), layerId);
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
                //
                // Record a successful transmission
                //
                addMessage(now.ToString() + ": " + lineCountInBuffer.ToString() + " events sent");
                totalEvents += lineCountInBuffer;

                //
                // Clear the buffer
                //
                lineBuffer = string.Empty;
                lastTimeBufferFlushed = now;
                lineCountInBuffer = 0;
            }
        }
    }
}
//
// End
//
```
