Using xargs to speed up XenServer operations
############################################
:date: 2010-08-13 08:17
:author: Greg Trahair
:category: How To - XenServer
:tags: xargs
:slug: using-xargs-to-speed-up-xenserver-operations
:status: draft

Performing many tasks can take some time, and we know that XenServer can
deal with a few of these operations at once. I had some issues, but
finally have a one-liner that is suitable (using xargs again :-))

.. code:: python

    xe vm-list is-control-domain=false power-state=running --minimal | 
        tr -d [:cntrl:] | 
        xargs -d, -n1 -P5 -I '{}' xe vm-param-list uuid='{}'

You notice that I strip all control characters out with tr. This is to
get rid of a strange line break that xargs will process even when
running -r.
