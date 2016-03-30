Disable driver signing - windows 7
##################################
:date: 2010-08-27 09:03
:author: Greg Trahair
:category: How To - Windows 7, Uncategorized
:slug: disable-driver-signing-windows-7
:status: draft

I had a problem installing the tun/tap adapter for openVPN on my windows
7 machines. Windows complains about the driver not being digitally
signed. I don't care about this so I switched it off by doing this:

Hit your windows key and type cmd in the 'Search Programs and Files
Box', now instead of just hitting ENTER, use CTRL+SHIFT ENTER to run as
Administrator. Type the following into the cmd prompt and reboot.

.. code:: python

    bcdedit.exe -set loadoptions DDISABLE_INTEGRITY_CHECKS
    bcdedit.exe -set TESTSIGNING ON

Done!
