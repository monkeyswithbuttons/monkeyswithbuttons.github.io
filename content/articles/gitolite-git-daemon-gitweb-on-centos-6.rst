gitolite, git-daemon & gitweb on CentOS 6
#########################################
:date: 2013-02-07 21:05
:author: Greg Trahair
:category: Devops, Featured, How To, How To - Linux
:tags: centos, git-daemon, gitolite, gitweb
:slug: gitolite-git-daemon-gitweb-on-centos-6
:status: published

I've found many tutorials on how to accomplish this on Debian/Ubuntu
systems, but not RHEL.

You have to have at least a minimal installation with the
`EPEL <http://fedoraproject.org/wiki/EPEL>`__ repository enabled.

Install packages

.. code-block:: bash

    yum install -y git{,web,-daemon,olite} httpd xinetd

| Setup Gitolite
|  Copy your public ssh-key to /tmp and allow gitolite to read it

.. code-block:: bash

    cp ~/.ssh/id_rsa.pub /tmp/$LOGNAME.pub && chmod 644 /tmp/$LOGNAME.pub

Change to the gitolite user and run gl-setup

.. code-block:: bash

    sudo -u gitolite -i
    gl-setup /tmp/$LOGNAME.pub

| Alter the base permissons of the repos and fix what we already have.
  In /var/lib/gitolite/.gitolite.rc change:
|  $GL\_WILDREPOS to 1; and
|  $REPO\_UMASK to 0027

.. code-block:: bash

    chmod g+r /var/lib/gitolite/projects.list
    chmod g+rx /var/lib/gitolite/repositories

Change the group of the apache user to allow it access to the gitolite
repositories

.. code-block:: bash

    usermod -a -G gitolite apache

| Configure git-daemon to only export repositories with a
  git-daemon-export-ok file inside (created by added R permissions to
  daemon)
|  /etc/xinetd.d/git:

.. code-block:: bash

    # default: off
    # description: The git dæmon allows git repositories to be exported using \
    # the git:// protocol.
    service git {
    disable = no
    socket_type = stream
    wait = no
    user = nobody
    group = gitolite
    server = /usr/libexec/git-core/git-daemon
    server_args = --base-path=/var/lib/gitolite/repositories --syslog --inetd --verbose
    log_on_failure += USERID }

Configure /etc/gitweb.conf to point to the right projectroot and
project\_list (the only 2 lines you actually need are here)

.. code-block:: bash

    our $projectroot = "/var/lib/gitolite/repositories";
    our $projects_list = "/var/lib/gitolite/projects.list";

Make the services persistant (survive a reboot):

.. code-block:: bash

    chkconfig httpd on
    chkconfig xinetd on
    service httpd start
    service xinetd start

To allow access to gitweb or gitdaemon in the config file, do something
like this in your gitolite.conf:

.. code-block:: bash

    repo    webtest
            R       =   daemon gitweb
            RW+     =   admin

If you need to add these perms to a wildcard repo, you can use the
setperm admin command:

.. code-block:: bash

    echo "READERS gitweb daemon" | ssh gitolite@host setperms path/to/wildrepo

Check it with:

.. code-block:: bash

    ssh gitolite@host getperms path/to/wildrepo
