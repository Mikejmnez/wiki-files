My goal is to create a Javascript API for accessing OpenDAP metadata and
an intuitive, Javascript-based data-request construction form.

**Please note:** This is a proposal for a Google Summer of Code project.
It was not implemented.

## Plan of action

1.  Create API for accessing geographical and time data with Javascript
    by modifying existing BES module or adding a new one
2.  Create Javascript library for easily accessing OpenDAP metadata
    1.  Write asynchronous metadata retrieval interface
    2.  Write metadata parser
    3.  Write JSON emitter
3.  Create data request form constructor using metadata bridge
    1.  Write basic form constructor to build simple but clear HTML
        forms
    2.  Add more advanced form controls (maps, timelines, etc.) for more
        capable browsers

## Execution path

1.  User requests data-request form
2.  Server sends simple HTML page with form-generation Javascript
3.  Javascript requests DDS and DAS for selected data
4.  Javascript parses DDS and DAS to generate HTML form
5.  User requests data through form
6.  Server returns user-selected data

## Application structure

### Form generator

#### Public interface

The public interface gives developers a dead-simple interface to create
data-specific request forms.

<code>

    createDataRequestForm({"url" : "http://test.com/data.gz", "containerID" : "requestform"});

</code>

#### Generator

The heart of the form generator transforms the JSON-encoded DAS and DDS
into an interactive HTML form.

<code>

    var generator = createFormGenerator();
    var form = generator.generateForm(dasData, ddsData);
    document.appendChild(form);

</code>

### DAS/DDS Javascript API

#### Public interface

The public interface allows developers to easily fetch JSON-encoded DAS
and DDS data from a data source.

<code>

    var dasData = fetchDAS("http://test.com/data.gz");
    var ddsData = fetchDDS("http://test.com/data.gz");

</code>

#### HTTP request generator

This critical piece will handle fetching the raw DAS or DDS data from
the server.

<code>

    var rawDASData = fetch({"url" : "http://test.com/data.gz.das", "callback" : onDataReceived});

</code>

#### DAS parser

The DAS parser will convert the raw DAS data into a
Javascript-accessible JSON object.

<code>

    var dasParser = createDASParser();
    var dasData = dasParser.parse(rawDASData);

</code>

#### DDS parser

Like the DAS parser, the DDS parser will translate a raw DDS string into
a JSON object.

<code>

    var ddsParser = createDDSParser();
    var ddsData = ddsParser.parse(rawDDSData);

</code>