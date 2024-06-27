## DAP2 CE Conversion

### DAP Projection

1.  Each projected variable should appear as one of the selected columns
    in the [SQL SELECT
    statement](http://www.w3schools.com/sql/sql_select.asp).
2.  The name of the Data Source (which is the DAP resource ID) should be
    used in the [FROM section of the SELECT
    statement](http://www.w3schools.com/sql/sql_select.asp).

### DAP Selection

1.  Each clause should be converted (where possible) into a condition in
    the [SQL SELECT statements WHERE
    clause](http://www.w3schools.com/sql/sql_where.asp).
2.  Array sub-setting cannot be encoded in the SQL query and must happen
    once the row set response has been received.

For example, the request:

`http://localhost/opendap/rdh/foo?v,u&lat<26&lat>24&lon<-126&lon>-128`

would be converted in the SQL query:

`SELECT v, u FROM foo WHERE lat<26 AND lat>24 AND lon<-126 AND lon>-128`

Additionally we may wish to add:

1.  The use of a limited regular expression type of matching in the CE
    which could be used to create [SQL "LIKE"
    operators](http://www.w3schools.com/sql/sql_like.asp).
2.  Some kind of a range operation that would map to [SQL "BETWEEN"
    operator](http://www.w3schools.com/sql/sql_between.asp).

### Server Side Functions

Additionally we may wish to add:

1.  A server side function that allows the user to specify that they
    only wish to receive *unique* (aka *distinct*) values in the
    response. This would utilize the [SQL "DISTINCT"
    operator](http://www.w3schools.com/sql/sql_distinct.asp).

## Constraint Expression Processing

In most DAP servers data are read and then the CE is applied to the
result. In the RDH things are a bit more complex. Since it is possible
to express some (but not all) of the DAP2 constraints in an SQL query,
the application of the CE to the data takes place as a multi step
activity. Once the CE has been parsed and the projected variables have
been marked, the instance of the ConstraintEvaluator class should be
holding a collection of Clause objects, one for each clause in the CE.
The next step converts (some, possibly all of) the DAP2 CE to an SQL
query. Each clause in the DAP2 CE that is successfully translated into
an SQL query WHERE clause should be removed from the Clause collection
in the ConstraintEvaluator, as these constraints will be applied by the
RDBMS.

Once the data are read (Which in this case means that the SQL query has
been sent to the RDBMS, the query has been processed, the result
returned, and the data read from the result) the remaining Clauses in
the ConstraintEvaluator instance should be applied to the data values.
This includes any array sub-setting activities.