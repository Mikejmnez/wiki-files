We have provided you with two example BES modules. These modules are the
Hello World module and the CSV data handler module.

Both of these modules are available from the SVN source code repository
and are not included in the source tar ball or the binary distributions.
To get them you will need to 'check out' the bes source code for [this
module](http://scm.opendap.org/svn/trunk/bes/hello_world).

# Hello World

The Hello World module, the SampleModule class, adds a new command to
the BES, the SampleSayXMLCommand. Using the BES command line client you
would have an input file with the following:

      <?xml version="1.0" encoding="UTF-8"?>
      <request reqID="some_unique_value" >
          <say what="hello" to="world" />
      </request>

The XML response document would look like the following:

      <?xml version="1.0" encoding="ISO-8859-1"?>
      <say><response><text>hello world</text></response></say>

It also adds the SampleRequestHandler class, which handles `help` and
`version` requests, the SampleSayResponseHandler class, which handles
building AND populating the informational response, and the SayReporter,
which reports on what is being said to who in a log file configured in
the BES configuration file. You can see the sample input and output in
the bes-testsuite directory under bes/hello_world. Adding this sample
module to your BES configuration file is also easy. It uses the same
mechanism that data handlers use, providing a shell script,
bes-sample-data.sh, to add information to the configuration file.

# CSV Data Handler Module

The CSV Data Handler Module handles requests for data in the CSV format
(comma separated). Here is an example file:

    "Station<String>","latitude<Float32>","longitude<Float32>","temperature_K<Float32>","Notes<String>"
    "CMWM",-34.7,23.7,264.3,
    "BWWJ",-34.2,21.5,262.1,"Foo"
    "CWQK",-32.7,22.3,268.4,
    "CRLM",-33.8,22.1,270.2,"Blah"
    "FOOB",-32.9,23.4,269.69,"FOOBAR"

The types of data allowed are:

- String
- Byte
- Int16
- Int32
- Float32
- Float64

The CSV Data Handler Module class CSVModule adds a request handler
(CSVRequestHandler) to handle populating DAP2 response objects (DAS,
DDS, DataDDS) as well as the standard response for help and version. The
Module also tells the BES that it can handle THREDDS catalog requests.

[Example BES Modules](Category:BES_Modules "wikilink")