Handy Splunk Searches
#####################
:date: 2011-06-29 20:13
:author: Greg Trahair
:category: How To
:slug: handy-splunk-searches
:status: draft

The idea of this post is only to keep track of the Splunk searches that
I use frequently that are generic enough to just work on other systems
without too much tinkering. For example, your sourcetype might be
different.

-  **Check for SUDO activity**

   .. code:: python

       sourcetype="syslog" sudo | rex field=_raw "sudo: (?<user>.*):" | search user!="pam_unix(sudo:auth)" | table user COMMAND
