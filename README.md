# RTView-Splunk-Integration
Splunk RTView Monitor for Tibco BusinessWorks

## Introduction
In version 6.3, Splunk added the HTTP Event Collector (HEC), "a new, robust, token-based JSON API for sending events to Splunk from 
anywhere without requiring a forwarder. It is designed for performance and scale. Using a load balancer in front, it can be
deployed to handle millions of events per second. It is highlyavailable and it is secure. It is easy to configure, easy to use,
and best of all it works out of the box. More information available at http://blogs.splunk.com/2015/10/06/http-event-collector-your-direct-event-pipe-to-splunk-6-3/

SL's RTView dataserver collects monitoring data from a variety of host, application, and middleware components, and has been extended
to forward this data to Splunk via the HEC. For demonstration purposes, this example shows how Tibco BusinessWorks data can be pushed to Splunk and visualized by a new Splunk app developed for this purpose. Given the sample displays and associated Splunk searches, we expect that Splunk users can easily further customize this app for their specific needs.

Given the generic function of the new RTView Splunk connector, it is easy to forward any data collected by RTView to Splunk, opening the doorfor a plethora of opportunities for analysis of data not previously available to Splunk.

##Prerequisites
* Splunk 6.3 or later
* RTView dataserver configured to collect Tibco BusinessWorks metrics 

##Installation
Download and install the RTView BW Monitor App. 
1. In your browser, login to Splunk as an administrator and navigate to Apps -> Manage Apps".
2. Click "Install app from file". 
3. Browse to and select downloaded app file, then click "Upload".

##Splunk Configuration
1. Create a new index for data sent by RTView dataservers.
   a) In your browser, navigate to "Settings -> Indexes".
   b) Click "New Index".
   c) Enter "rtviewdata" for the "Index Name", select "RTView Monitor for Tibco
      BusinessWorks" for the "App", and click "Save".

2. Enable the "HTTP EventCollector".
   a) In your browser, navigate to "Settings -> Data Inputs".
   b) Click "New Token". (SV: Missing - click on 'HTTP Event Collector' and then 'New Token').
   c) Enter "rtviewstream" for the token "Name", and click "Next".
   d) Click on "rtviewdata" in "Select Allowed Indexes" and click "Review".
   e) Click "Submit".
   f) Copy the token for use in "RTView Configuration". (Example: It will be a string similar to the following: 39B11BA2-589C-4BD4-9542-BEA103A31A2C)
   g) Click "Global Settings". Click "Enabled" for "All Tokens" and "Save". (i.e. Settings > Data Inputs > HTTP Event Collector > Global Settings)

##RTView Configuration
Configure your RTView Dataserver to forward data to Splunk.

1. Starting with a dataserver already configured to collect data from a farm of Tibco BusinessWorks engines, install the new "splunkcon" Solution Package. Refer to the instructions in the sample.properties file for splunkcon. You will paste the token from step 2f (above) into the appropriate property in that file and uncomment the appropriate "sender" config file (bw_splunk_sender.rtv).
  
2. Avoid dataserver-to-Splunk connection failures by adding Splunk's certificate to your java JRE's certificate store on the host that will run the RTView dataserver.
	   	   
3. Start the dataserver to begin forwarding data to Splunk.

##View Displays in Splunk App

1. Login to Splunk. Select app > RTView Monitor for Tibco BusinessWorks'. View the displays e.g. BW Servers, BWEngines, BWProcesses tabs. (see screenshots.zip for samples).

2. As the dataserver sends data to Splunk, the various displays in the app will begin to populate.

### Adding New Certificate to your Java Store
1. If the store already contains an older Splunk certificate, then 
delete it as follows:
	 > keytool -delete -alias SplunkServerDefaultCert -keystore "%JAVA_HOME%\jre\lib\security\cacerts"
	   
2. Fetch the new certificate and add it to the store, using the "openssl"
command available in either cygwin or linux (or use browser - see note):
	> openssl s_client -connect <server name or IP>:<port>
	   
3. The output of openssl should include the following, which you should
copy and paste into a file named SplunkServerDefaultCert.pem:
-----BEGIN CERTIFICATE-----
MIICdTCCAd4CCQDlsvzBaZf1RjANBgkqhkiG9w0BAQUFADB/MQswCQYDVQQGEwJV
UzELMAkGA1UECAwCQ0ExFjAUBgNVBAcMDVNhbiBGcmFuY2lzY28xDzANBgNVBAoM
...
-----END CERTIFICATE-----
	   
4. Add the certificate to the store as follows:
	> keytool -import -v -trustcacerts -alias SplunkServerDefaultCert -file SplunkServerDefaultCert.pem -keystore "%JAVA_HOME%\jre\lib\security\cacerts"
	   
Note: The default password for java keystore is "changeit".

### Adding New Certificate to your Browser
As an alternative to openssl, you can obtain the splunk certificate using Firefox. Simply enter the Splunk HEC URL (https://localhost:8088) in your browser, then add an exception to enter the certificate in firefox's certificate store. Then use "Tools -> Options -> Advanced" to view and export the certificate.

### Changing Indexes in Splunk
If you change the index name in Splunk, you will need to update the saved queries for this app.

### Available Ports
TCP port 8088 must be open on the server hosting splunk and not blocked by the host firewall.

### Setting up HTTP Event collector in Splunk
See "http://docs.splunk.com/Documentation/Splunk/6.5.0/Data/UsetheHTTPEventCollector"
for setup and use of Splunk's HTTP Event Collector. 
