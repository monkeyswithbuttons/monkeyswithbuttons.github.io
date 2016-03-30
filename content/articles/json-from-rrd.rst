JSON from RRD
#############
:date: 2012-03-30 13:51
:author: Greg Trahair
:category: Devops
:tags: rrdtool
:slug: json-from-rrd
:status: published

Towards the end of January 2012, Tobi Oetiker released rrdtool 1.4.6.
This release contained a community submitted patch to allow export of
rrd in json.

Here's an example (based on a collectd rrd file):

::

    $ /opt/rrdtool-1.4.7/bin/rrdtool xport --json -s $(date -d "10 mins ago" +%s) -e $(date +%s) \
        --step 10 DEF:load_1min_avg=/var/lib/collectd/$(hostname -f)/load/load.rrd:shortterm:AVERAGE \
        XPORT:load_1min_avg
    { about: 'RRDtool xport JSON output',
      meta: {
        start: 1333111500,
        step: 10,
        end: 1333111500,
        legend: [
          ''
              ]
         },
      data: [
        [ 8.7720000000e+00 ],
        [ 9.0620000000e+00 ],
        [ 9.0540000000e+00 ],
        [ 8.9620000000e+00 ],
        [ 8.8840000000e+00 ],
        [ 9.0520000000e+00 ],
        [ 8.9760000000e+00 ],
        [ 8.0920000000e+00 ],
        [ 7.8240000000e+00 ],
        [ 7.8620000000e+00 ],
        [ 8.0440000000e+00 ],
        [ 8.4500000000e+00 ],
        [ 8.5720000000e+00 ],
        [ 8.6540000000e+00 ],
        [ 8.9960000000e+00 ],
        [ 9.2200000000e+00 ],
        [ 9.3700000000e+00 ],
        [ null ],
        [ null  ]
      ]
    }

Now all you need to do is get this data into jqplot or flot and you can
make beautiful dashboards.
