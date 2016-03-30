Using iptables-save
###################
:date: 2010-12-09 11:13
:author: Greg Trahair
:category: Devops, System Engineering
:slug: using-iptables-save
:status: published

| I just lost my netfilter persistance file (/etc/sysconfig/iptables)
  becuase I used /usr/bin/system-config-securitylevel-tui.
|  I see two options now:

#. Edit /etc/sysconfig/iptables and then restart, or
#. Use /sbin/iptables to insert rules then save with /sbin/iptables-save

I'm going for option 2, as I can create a backup of the config at the
same time doing this:

example rule to allow port tcp/80:

.. code:: python

    /sbin/iptables -I RH-Firewall-1-INPUT 10 
        -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
    test -d /var/backup/iptables || mkdir -p /var/backup/iptables
    /sbin/iptables-save | /usr/bin/tee /etc/sysconfig/iptables 
        /var/backup/iptables/iptables-sysconfig-$(date +%s)
