## Overview

One important technique to diagnose issues is to look at trace files. Kudu service as well as its application write traces to the /Logfiles folder (see [[File structure on Azure]]). The logstream endpoint (/logstream in the [[Kudu service|Accessing-the-kudu-service]]) enables developers to view real time traces as they are being written. 

## Using /logstream

By simply connecting to `<kudu-service-url>/logstream` (preferably using a tool like curl), you will be able to get the live streaming of traces.  The /logstream watches (FileSystemWatcher) all the log/txt files changes under /LogFiles and its sub folders.   

To scope the live traces to certain providers/folders, you may additional specify the path.

* `/logstream/kudu/trace` => kudu related traces intended for diagnostic kudu issues.
* `/logstream/kudu/deployment` => kudu deployment traces intended for application developers/operators.
* `/logstream/application` => application traces.
* `/logstream/http` => iis logs.

etc.

## trace_level knobs

Most trace providers allow users to adjust the verbosity of the traces.  Kudu trace provider uses Diagnostic's TraceLevel; Off=0 (default), Error=1, Warning=2, Info=3, Verbose=4.  

Its trace level can be adjusted via settings.

* set kudu trace_level to Verbose.

  `$ curl <kudu-service-url>/settings -X POST -H "Content-Type: Application/json" -d "{'trace_level':'4'}"` 

* get kudu trace_level.

  `$ curl <kudu-service-url>/settings/trace_level` 
 
TBD: setting and getting application trace_level.

## filtering and last N

TBD.

## Lifetime of /logstream requests

As long as there exists active traces, the client request is kept alive.  After 10 mins of idle without traces, the client connection is terminated to optimize server resources.  

## Connecting the /logstream using a web browser

If you request /logstream from a Web browser (e.g. Chrome or IE), you may find that you initially get a blank page, even though your site is producing some trace.

The reason is that browsers do buffering of the response, until either the response is complete (which never happens in this case), or it gets a large amount of data.

For that reason, using a browser is not recommended, and using a simple tool like curl is preferable.

## log samples

For .NET application, 

       System.Diagnostics.Trace.TraceInformation("Info statement");
       System.Diagnostics.Trace.TraceError("Error statement");
       System.Diagnostics.Trace.TraceWarning("Warning statement");

Codes above produce traces below in log file.

        2013-10-04T22:17:06  PID[2432] Information Info statement
        2013-10-04T22:17:07  PID[2432] Error       Error statement
        2013-10-04T22:17:07  PID[2432] Warning     Warning statement

For NodeJS application, `console.log("Hello World " + req.url);` produces below in log file.

        Hello World /
        Hello World /favicon.ico