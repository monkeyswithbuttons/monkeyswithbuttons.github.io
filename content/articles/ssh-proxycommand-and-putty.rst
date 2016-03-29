ssh ProxyCommand and PuTTY
##########################
:date: 2010-10-01 09:16
:author: geogdog
:category: How To - Linux
:tags: proxy, PuTTY, remote access, security, ssh, ssh-proxy
:slug: ssh-proxycommand-and-putty
:status: published

I've had problems in the past, where I've had so many NAT configurations
to get into various machines in my small, home network. Using the ssh
ProxyCommand, you can use a single exposed machine to forward your ssh
sessions onto any machine in your network.

openSSH CLI
-----------

| Obviously, most (if not all) flavours of Linux come with an ssh client
  included in the basic install, so you can just chuck a little config
  into ~/.ssh/config and your done.
|  In this example, I've exposed sshproxy.example.com to the internet on
  port 22, and I'm going to setup a connection to build.example.local
  (inside my network):

.. code-block:: python

    Host example-build
        User            example
        Hostname        build.example.local
        ForwardAgent    yes
        ProxyCommand    ssh example@sshproxy.example.com nc %h %p

**Host** is an arbitrary string to describe you connection. You'll use
this as the host value when you run ssh:

.. code-block:: python

    ssh example-build

| **User** is the username that you use to connect to the host at
  Hostname
|  **ProxyCommand** is the command you run to create the proxy.

PuTTY
-----

You can configure Putty the same way by doing this:

-  In the session section type this for the host string:
    |image0|
-  In *Connection:Data* enter your username:
    |image1|
-  In *Connection:Proxy*  add the proxy settings:
    |image2|
-  and, Finally allow Agent Forwarding in Connection:SSH:Auth
    |image3|

As long as you have your public key installed on the proxy and the host
that you are connecting to, and your key is loaded into pageant, This
will work.

Please note that it is necessary to have publickey authentication with
the proxyhost using this method. It is possible to send the password
over to the proxy, but I don't recommend that at all.

.. |image0| image:: |filename|/images/host.png
   :target: |filename|/images/host.png
.. |image1| image:: |filename|/images/user.png
   :target: |filename|/images/user.png
.. |image2| image:: |filename|/images/proxy.png
   :target: |filename|/images/proxy.png
.. |image3| image:: |filename|/images/forward-agent.png
   :target: |filename|/images/forward-agent.png
