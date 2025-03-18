## Backend Code for IPNListener
```
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net;
using System.Web;
using System.Web.Script.Serialization;
using System.Web.UI;
using System.Web.UI.WebControls;
using Microsoft.Ajax.Utilities;

namespace webhook
{
    public partial class IPNListener : Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
            
            // Read the JSON payload
            using (StreamReader reader = new StreamReader(Request.InputStream))
            {
                string jsonPayload = reader.ReadToEnd();
                if(jsonPayload != "")
                {
                    Response.StatusCode = (int)HttpStatusCode.OK;
                    Response.Write("IPN received and processed successfully.\n");

                    Response.Write($"Received IPN: {jsonPayload}");
                    WriteToLogFile(jsonPayload);
                }
                else
                {
                    Response.StatusCode = (int)HttpStatusCode.BadRequest;
                    Response.Write("Invalid request");
                }
            }               
        }

        private void WriteToLogFile(string jsonData)
        {
            var logFilePath = Path.Combine(Server.MapPath("~/logs"), $"bKash-IPNLog-{DateTime.Now:dd.MM.yyyy}.json");

            // Ensure the logs directory exists
            Directory.CreateDirectory(Path.GetDirectoryName(logFilePath));

            // Write the JSON data to the log file
            File.AppendAllText(logFilePath, jsonData + Environment.NewLine);
        }
    }
}
```
### Webhook Endpoint Will be : https://{Your Domain}/IPNListener
### To check if your endpoint is publicly accessible, make a sample POST request from your local machine. For example:
```
curl --location 'https://{Your Domain}/IPNListener' \
--header 'Content-Type: text/plain; charset=UTF-8' \
--data '{
    "x":"y"
}'
```
