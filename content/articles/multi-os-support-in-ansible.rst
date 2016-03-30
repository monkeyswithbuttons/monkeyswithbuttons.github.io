Multi OS Support in Ansible
###########################
:date: 2014-04-03 18:30
:author: Greg Trahair
:category: Ansible, Patterns
:tags: ansible-patterns
:slug: multi-os-support-in-ansible
:status: published

In the last 6 months I've been diving into Ansible as a part of my daily
work. I have to support RedHat (and CentOS) along with Ubuntu in a
variety of versions. Coming from a puppet background I was surprised
that there was no package management abstraction in Ansible, but after
coming across various roles on GitHub and Ansible Galaxy I quickly
realised that it's not necessary.

Here I will describe a multi-os Ansible pattern. It's all about
including the right set of variables.

In this example I'll be creating a 'ntp' role to simply install and
configure ntp.

Firstly we create our role:

::

    $ ansible-galaxy init ntp

Now we need to add some variables for the OS's we support. This is done
in the **vars/** directory.

Basically we'll create a hierarchical lookup narrowing our variable
scope. My suggestion for this lookup is:

-  {{ ansible\_distribution }}-{{ ansible\_distribution\_version }}
-  {{ ansible\_distribution }}
-  {{ ansible\_os\_family }}
-  defaults

On CentOS-6.3 this would expand to:

-  CentOS-6.3
-  CentOS
-  RedHat
-  defaults

| Create the files.  In my example I── ntp.conf.j2
|  └── vars
|  ├── CentOS.yml
|  ├── defaults.yml
|  ├── main.yml
|  └── Ubuntu.yml

In my example, the ntp package name and service name are different
across Ubuntu and CentOS. Also, the default list of NTP servers are
different. Here are the var files:

::

    ::::::::::::::
    CentOS.yml
    ::::::::::::::
    ---
    ntp_package:
        name: ntp
        state: installed

    ntp_service: ntpd

    ntp_config: /etc/ntp.conf

    ntp_servers:
      - 0.centos.pool.ntp.org iburst
      - 1.centos.pool.ntp.org iburst
      - 2.centos.pool.ntp.org iburst
      - 3.centos.pool.ntp.org iburst

    ::::::::::::::
    Ubuntu.yml
    ::::::::::::::
    ---
    ntp_package:
      name: ntp
      state: installed

    ntp_service: ntp

    ntp_config: /etc/ntp.conf

    ntp_servers:
      - 0.ubuntu.pool.ntp.org
      - 1.ubuntu.pool.ntp.org
      - 2.ubuntu.pool.ntp.org
      - 3.ubuntu.pool.ntp.org

Now I have to add the includes for these variables in tasks/main.yml:

::

    ---
    # tasks file for ntp
    - name: Gather OS Specific Variables
      include_vars: "{{ item }}"
      with_first_found:
        - "../vars/{{ ansible_distribution }}-{{ ansible_distribution_version }}.yml"
        - "../vars/{{ ansible_distribution }}.yml"
        - "../vars/{{ ansible_os_family }}.yml"
        - "../vars/defaults.yml"

    - name: Install NTP Package
      action: >
        {{ ansible_pkg_mgr }} name={{ ntp_package.name }} state={{ ntp_package.state }}

    - name: Configure NTP
     template: src={{ item }} dest={{ ntp_config }}
     with_first_found:
     - "../templates/{{ ansible_distribution }}-{{ ansible_distribution_version }}.ntp.conf.j2"
     - "../templates/{{ ansible_distribution }}.ntp.conf.j2"
     - "../templates/{{ ansible_os_family }}.ntp.conf.j2"
     - "../templates/ntp.conf.j2"
     notify:
     - Restart NTP Service

    - name: Start NTP Service
      action: >
        service name={{ ntp_service}} state=started

A handler for the NTP service in handlers/main.yml:

::

    ---
    # handlers file for ntp
    - name: Restart NTP Service
      action: >
        service name={{ ntp_service }} state=restarted

And the template file for the ntp config in templates/ntp.conf.j2:

::

    # For more information about this file, see the man pages
    # ntp.conf(5), ntp_acc(5), ntp_auth(5), ntp_clock(5), ntp_misc(5), ntp_mon(5).

    driftfile /var/lib/ntp/drift

    # Permit time synchronization with our time source, but do not
    # permit the source to query or modify the service on this system.
    restrict default kod nomodify notrap nopeer noquery
    restrict -6 default kod nomodify notrap nopeer noquery

    # Permit all access over the loopback interface.  This could
    # be tightened as well, but to do so would effect some of
    # the administrative functions.
    restrict 127.0.0.1 
    restrict -6 ::1

    # Hosts on local network are less restricted.
    #restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap

    # Use public servers from the pool.ntp.org project.
    # Please consider joining the pool (http://www.pool.ntp.org/join.html).
    {% for ntp_server in ntp_servers %}
    server {{ ntp_server }}
    {% endfor %}

    #broadcast 192.168.1.255 autokey    # broadcast server
    #broadcastclient            # broadcast client
    #broadcast 224.0.1.1 autokey        # multicast server
    #multicastclient 224.0.1.1      # multicast client
    #manycastserver 239.255.254.254     # manycast server
    #manycastclient 239.255.254.254 autokey # manycast client

    # Enable public key cryptography.
    #crypto

    includefile /etc/ntp/crypto/pw

    # Key file containing the keys and key identifiers used when operating
    # with symmetric key cryptography. 
    keys /etc/ntp/keys

    # Specify the key identifiers which are trusted.
    #trustedkey 4 8 42

    # Specify the key identifier to use with the ntpdc utility.
    #requestkey 8

    # Specify the key identifier to use with the ntpq utility.
    #controlkey 8

    # Enable writing of statistics records.
    #statistics clockstats cryptostats loopstats peerstats

Now it should be easy to add support for other OS's just by adding a
vars/OS.yml file.
