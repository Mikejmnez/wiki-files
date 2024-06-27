## Thinking out loud

As a site data access manager, can you name the statistics that a site
would be interested in? And of those statistics, which would be good to
maintain a real-time counter of? How this information is acquired could
be from the service or from the logs. I am thinking about how this would
be done and whether Hyrax provide any of this information, or another
tool within the service can scan the logs and obtain a standard of
information.

### Catalog Statistics

------------------------------------------------------------------------

Overall storage of accessible data (GB) Overall number of data files
Overall number of data sets (groups of files from metadata structure,
ideal for aggregations) Overall number of aggregated data sets Overall
number of virtual data sets Overall number of CF compliant data sets ...

### Catalog of data set statistics

------------------------------------------------------------------------

Storage of accessible data (GB) Number of data files Time range of data
Spatial range of data Number of unique variables in data files CF
compliant data set Aggregation of data set ...

### Data service statistics

------------------------------------------------------------------------

Number of unique visitors per day List of unique visitors names, ip
address, host name, and application name Number of data requests per
service (catalog, opendap, wms, ...) Number of data volume delivered per
service List of application name, user name, and data-size delivered for
data request Number of requests exceeding data request size thresholds
List of unique visitors names, ip address, host name, and application
name exceeding threshold Number of data request served asynchronously
Number of data request using server-side processing ...

### Service statistics per data set

------------------------------------------------------------------------

Number of unique visitors per day List of unique visitors names, ip
address, host name, and application name Number of data requests per
service (catalog, opendap, wms, ...) per data set Number of data volume
delivered per service per data set List of application name, user name,
and data-size delivered for data request ...

I’m thinking about this for an implementation plan and data service
management.