1.  use cases must illustrate that a (say) "mean" may be taken over a
    subset of the coordinate axes for a variable. The mean collapses
    dimensions of the original grid to produce a degenerate grid result.
    E.g. an area mean of an lat-lon-time variable produces a time
    series.
2.  application of this: difference time series of the same area mean
    from two different datasets. Requires these nested operations:
    1.  area-average each of the variables over the same (or different)
        areas
    2.  specify the vertical coordinate point on each in a geo-aware
        manner. (i.e. not by vertical index)
    3.  specify the time range; regrid the time series of one variable
        to match the other
    4.  take a difference between the two time series
3.  this is an equivalent use case but on different dimensions:
    difference XY fields from time-averaged results
    1.  time-average each of the variables over the same (or different)
        time ranges
    2.  specify the vertical coordinate point on each in a geo-aware
        manner. (i.e. not by vertical index)
    3.  specify the lat-lon range; regrid the lat-lon coordinates of one
        field to match the other
    4.  take a difference between the lat-long fields