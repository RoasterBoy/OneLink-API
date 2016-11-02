About this guide
================

This document illustrates a feature of the OneLink*<sup>®</sup>* Platform called the OneLink API. It enables real-time querying of Translation Memory stores to feed other requirements — such as email content, browsers, and other third-party products and data stores that may require translation.

What is the OneLink API?
========================

The OneLink<sup>®</sup> Platform can be configured as a proxy service to provide a translated version of a website, or invoked programmatically as an API to provide translations into several receiving applications, such as email or both. This paper positions how OTX, the API, can be used to return translations into documents, email, and other applications. Most clients of the OneLink<sup>®</sup> Platform use it to serve a proxy version of their website/

The OneLink<sup>®</sup> Platform can also be configured as an API.

For example,

1.  A Spanish page request is made by the browser.

2.  The origin host responds by composing an English page, built using normal processes (i.e., CMS, API, Static Page, etc.).

3.  The composed page is sent to the OneLink API application code.

4.  The OneLink API code makes the appropriate requests from Translation Memory and the Parsing Engine to swap Spanish for English content.

5.  The OneLink<sup>®</sup> Platform composes the Spanish page, and returns it to the origin server.

6.  The origin server renders the Spanish page in the browser.

This model achieves anonymity from the client-server relationship since it is not connected to the exchange between browser and server. The server itself appears in every way as the source of the translated site. When content is requested from the server it is served directly to the client’s browser with no interim contact with a OneLink<sup>®</sup> proxy. Few circumstances mandate implementation of this sort of architecture, but it is available to clients who wish to isolate OneLink<sup>®</sup> completely from that dialog. Those who implement this alternative are required to host the OneLink<sup>®</sup> Platform directly inside their IT architecture, which entails a deeper involvement of internal IT resources than may be desirable for many clients.

# Getting Started


## Why would you use the OneLink API?
-----------------------------------------------------------------------------------------------------------------------------------

Translation needs are very diverse within most companies. Requirements may come from a variety of sources and go to various target applications. Typically, clients’ website content and external applications like email are considered separately, yet both require translated content to function in a global environment. Website content, once translated, seems to span a company’s entire ERP cycle from marketing and sales through to training and support.
### Examples:

-   You can translate selected content that can then be merged with your customers' personal data. The customer data never leaves your site.

-   You can translate internal emails

-   Use case 3

-   Use case 4

##  Solution

In response to these multiple requirements, Translations.com offers a variety of technologies. Translations.com’s OneLink<sup>®</sup> offers both proxy-based website translation and an API for general purpose real-time translation. Translations can be sourced through the API and delivered either to a website or to any number of external applications, if that content has been captured, translated, and resides in Translation Memory. OneLink<sup>®</sup> API is a function of the OneLink<sup>®</sup> Platform which accepts programmatic requests for translation through an API. The OneLink<sup>®</sup> Platform is required to implement the API. There are various flexible ways to set up that platform to fulfill our clients’ needs, requirements, and goals.

The OneLink<sup>®</sup> API leverages the following components of the OneLink<sup>®</sup> Platform:

-   Real-time parsing of web related mime-types (HTML, XML, JS, JSON, etc.)

-   Real-time translation memory (TM) lookups for segment/sentence-level replacements from source to target language

-   Ability to mark-up source content with OneLink® classes to prevent certain content from being translated

-   Ability to capture segments that are missing from the TM and/or backfill with real-time machine translation

-   Sub-second response time

## About the OneLink Proxy

The OneLink Proxy is a special proxy that localizes websites virtually. A traditional localization configuration requires that you maintain your original web content and synchronize changes with the localized content (in multiple languages). The OneLink proxy simplifies this process by applying translation on-the-fly.

The proxy server sits in between the end-user’s browser and the client’s servers.

1.  The user visits es.acme.com. The user’s browser sends a request that will go to the OL proxy server.

2.  The OL proxy server in turn passes a request to the origin server requesting the source file.

3.  The origin server responds with the requested file.

4.  The OL server “localizes” the file.

5.  <img src="./media/image5.png" width="516" height="224" />The OL server responds to the browser with the localized version of the requested file.

Note that steps 3 through 5 are not “seen” by the end-user’s browser. Rather, the browser is requesting a file from the OL server and the OL server is responding with that file just as any other server would. The magic of OneLink is that the localization steps happen with almost zero negative effect on performance for the end-user.

# OneLink API Implementation

## POST Request

Your application code sends POST requests to the OneLink<sup>®</sup> API using parameters that are specified on the next page. Note that you will need to understand where the OneLink API server lives. If the OneLink API server is installed on your LAN, the request may look like this:

**https://10.20.30.40/OneLinkOTX/page/requesting/data.html **

If the OneLink API server is installed in one of our colocation facilities, the request may look like this:

**https://es.acme.com/OneLinkOTX/page/requesting/data.html **

Using your application code, you should create a POST request with the following request headers and post parameters:

***Required Request Headers:***

**Host:** *your-virtual-host* (see below)

**Content-Type:** application/x-www-form-url-encoded (all POST requests have this header)

***Required Post Parameters:***

**otx\_account**=account number, account password

**otx\_mimetype**=(see below)

**otx\_service**=(see below)

**otx\_content**=(see below)

POST Parameters
---------------

**otx\_account:** This contains the account number and password for the OneLink API.

**otx\_service:** *Optional.* *Defaults to “tx”*. Values are as follows:

> **tx:** Performs normal translation as defined for the virtual host.
>
> **smt:** Performs SMT (simple machine translation).
>
> **wmt:** Performs WebMT (WorldLingo machine translation).
>
> **tx+smt:** Performs normal translation, but any segments not found in the TM are translated using simple machine translation
>
> **tx+wmt:** Performs normal translation, but any segments not found in the TM are translated using WorldLingo machine translation
>
> **parse:** Parses and outputs segmentation and token logic (used primarily for debugging)

**otx\_mimetype:** The mime-type of the content being uploaded. This tells the OneLink<sup>®</sup> Proxy how to interpret the incoming information. The only supported types as of right now are:

> **text/html**: This page will be parsed as a normal HTML page by the rules defined for the virtual host.
>
> **text/xml**: This data will be parsed normally as XML content following the logic defined for the virtual host.
>
> **text/javascript**: Incoming data will have our custom JavaScript parser applied.
>
> **text/json** or **text/plain**: The JSON data will be filtered through our custom JSON parser logic.
>
> **text/segment** or **application/json**: This refers to a single segment of text that will not be parsed with rules, it will only be translated.
>
> **Note:** any foreign mimetype not listed above will cause the OneLink API to respond with HTTP-205 and the original document untouched. The same thing will happen if the mimetype is not set.

**otx\_content:** the content to be translated. Note that in the event that the content contains non-ASCII characters, the base encoding must be UTF-8 (not Windows-1252, nor ISO-8859). In order to be passed as POST data, the content must also be “form-url-encoding.” Most application frameworks will automatically form-encode data if they know you are sending a POST request.

## OneLink API Responses

The response is a standard HTTP response and the status code is any one of the following:

> **HTTP-200:** Success
>
> **HTTP-205:** The mime type not supported, original document is returned untouched
>
> **HTTP-401:** Protocol error: missing or malformed required header
>
> **HTTP-402:** Feature is not enabled for specified host
>
> **HTTP-403:** Invalid account number or invalid account password
>
> **HTTP-404:** Translation Memory (TM) is not started or service is not available
>
> **HTTP-405:** Attempted to use HTTP instead of HTTPS from a public IP address

Following an HTTP-200 response, the rest of the response header will look like the following:

> **Content-Type:** The mime type of the response (usually the same as the mime type that came in)
>
> **Content-Length:** Number of bytes in the translated document
>
> **Encoding:** gzip, etc. (based on site configuration rules)

The following response headers are included whenever the request includes **X-OneLink-Headers** and “**translation”** is in the comma-delimited list of flags:

> **X-OneLinkSegments:** *nn* (the number of translatable text segments in the content)
>
> **X-OneLinkTranslated:** *nn* (the number of text segments translated)
>
> **X-OneLinkTxPercent:** *nn* (percentage of text segments translated)

**Sent to the OneLink API:**

Host: es.acme.com

Content-Type: application/x-www-form-urlencoded

Content-Length: 123

**Received from the OneLink API:**

HTTP/1.1 200 OK

Content-Type: text/plain

Content-Length: 12

Encoding: utf8

Hola Mundial

For example:
```
% curl -k --header "host: es.acme.com" --request post
'https://es.acme.com/OneLinkOTX/wfonline/' --data "otx\_mimetype=text/html&otx\_account=acme&otx\_service=smt&otx\_content=&lt;html&gt;&lt;head&gt;&lt;/head&gt;&lt;body&gt;Hello World&lt;/body&gt;&lt;/html&gt;"
```
# Appendix: Code Samples

The following examples show you some basic examples of the OneLink API in Python, Java, and PHP.

Python example:
---------------
```
\#!/usr/bin/env python
\# -\*- coding: utf-8 -\*-
\# Import extensible library for opening URLs
import urllib2, urllib
\# Segment for translation
contentForTx = "Acme Corp has the largest selection of dynamite and anvils on the market."
\# The hostname of the project that contains the TM and API account
virtualHostName = "es-otx.onelink-poc.com"
\# The hostname of the server the project is contained on
physicalHostName = "es-otx.onelink-poc.com"
\# Set the parameters of the request, encode with url encoding.
params = urllib.urlencode({'otx\_mimetype': 'text/html',
'otx\_account' : 'otx,otxpass',
'otx\_service' : 'tx',
'otx\_content' : contentForTx})
\# Set the host header
headers = {"Host": virtualHostName}
\# Make the request object passing in the parameters and headers
req = urllib2.Request("https://"+physicalHostName+"/OneLinkOTX/", params, headers)
\# Execute the request
response = urllib2.urlopen(req)
\# Read the response
htmlData = response.read()
print ":: Content for translation : "+contentForTx
print ":: Received translated content: "+htmlData
```
The following is the output from this example:
```
python ./onelink.api.example.py

:: Content for translation : Acme Corp has the largest selection of dynamite and anvils on the market.
:: Received translated content: Acme Corp tiene la selección más grande de la dinamita y de los yunques en el mercado.
```
## Java Example:

**Java example goes here**

## PHP example:
------------
```
&lt;?php

if($argc&lt;2){

                echo "\\n usage: php getOTX.php content service\\n";

               exit;

}

// Setup Defaults

$otx\_account = 'otx,otxpass';

$otx\_mimetype = "text/html";

// Fill from cli

$otx\_content =                 $argv\[1\];

$otx\_service = ( isset($argv\[2\])) ? $argv\[2\] : "smt";

// Setup data for http query

$url = 'https://es-otx.onelink-poc.com/OneLinkOTX/';

$data = array(

                                'otx\_account' =&gt; $otx\_account

                                ,'otx\_service' =&gt; $otx\_service

                                ,'otx\_mimetype' =&gt; $otx\_mimetype

                                ,'otx\_content' =&gt; $otx\_content

                                                               

                );

// Setup stream options

$options = array(

    'http' =&gt; array(

        'header'  =&gt; "Content-type: application/x-www-form-urlencoded\\r\\n",

        'method'  =&gt; 'POST',

        'content' =&gt; http\_build\_query($data),

    ),

);

// Create stream , get contents

$context  = stream\_context\_create($options);

$result = file\_get\_contents($url, false, $context);

// Results

echo "\\n    content:" . $otx\_content;

echo "\\n translated:" . $result ."\\n";

?&gt;
```
The following example shows the output from the PHP sample:
```
php getOTX.php "&lt;p&gt;I am going for a long walk&lt;/p&gt;" tx
```
content:&lt;p&gt;I am going for a long walk&lt;/p&gt;
translated:&lt;p&gt;Voy para una caminata larga&lt;/p&gt;

# Appendix: OneLink API Testing

We have a OneLink API service you can test with Spanish. It uses Machine Translation so be cautioned that the translation is not reliable and is used only for testing purposes.

The domain is: [es-otx.onelink-poc.com](es-otx.onelink-poc.com)

The username is: otx
and password is: otxpass

Rather than make separate calls for each segment you want to translate you can send the server JSON or XML.

A simple Curl test to the OneLink API Service:

```
curl -k --header 'Host: es-otx.onelink-poc.com' --request POST 'https://es-otx.onelink-poc.com/OneLinkOTX/' --data 'otx\_mimetype=text/html&otx\_account=otx,otxpass&otx\_service=tx&otx\_content=&lt;p&gt;I am going for a long walk&lt;/p&gt;' ; echo
```
This is expected to return the translated segment:
```
&lt;p&gt;Yo soy va para un largo caminar&lt;/p&gt;
```
You can also send several segments to the service for translation by using JSON or HTML

In this case the server must be configured to translate specific data. This is done because most servers use JSON and XML to transmit not only translatable segments but code and tag information.

For testing we have added a global ‘otxtest’ tag to the OneLink translation stack. Anytime it finds this in the tag stack, it will translate the content inside it.

For example, the command passes JSON with 3 elements to the server. Because these are contained within an otxtest element it will translate:

> curl -k --header 'Host:es-otx.onelink-poc.com' --request POST 'https://es-otx.onelink-poc.com/OneLinkOTX/' --data 'otx\_mimetype=text/json&otx\_account=otx,otxpass&otx\_service=tx&otx\_content={ otxtest: { "data1":"i see the cat","data2":"chasing the dog", data3:"in the yard" }}' ; echo

This is the expected response:

> { "otxtest": { "data1": "yo ver la gato", "data2": "persiguiendo la perro", "data3": "en la patio" } }

The same is true for XML (Note modified mime type)

> curl -k --header 'Host:es-otx.onelink-poc.com' --request POST 'https://es-otx.onelink-poc.com /OneLinkOTX/' --data 'otx\_mimetype=text/xml&otx\_account=otx,otxpass&otx\_service=tx&otx\_content=&lt;otxtest&gt;&lt;foo&gt;You have to learn the rules of the game.&lt;/foo&gt;&lt;bar&gt;And then you have to play better than anyone else.&lt;/bar&gt;&lt;/otxtest&gt;' ; echo

This is the expected result:

> &lt;otxtest&gt;&lt;foo&gt;Usted tener a aprender la normas de la juego.&lt;/foo&gt;
> &lt;bar&gt;Y entonces usted tener a jugar mejor de nadie mÃ¡s.&lt;/bar&gt;
> &lt;/otxtest&gt;