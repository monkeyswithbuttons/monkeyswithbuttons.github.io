Capacity Planning your Virtualisation Setup
###########################################
:date: 2010-08-12 19:33
:author: geogdog
:category: Devops, System Engineering
:slug: capacity-planning-your-virtualisation-setup
:status: published

| Everybody wants servers now!
|  Now that servers are virtual, everybody knows you can click a button
  and give them what they want!
|  Before you know it, you'll have tripled your server count!
|  |image0|

| How can you make sure that things won't fail in a massive way?
|  Physically plan your virtual setup!

Think about this, you've got sixteen machines to create a pool of
hypervisors. They've all got two, quad-core processors and thirty-two
gigabytes of RAM.

You only have one switch per rack, and you plan to use four racks (four
hypervisors in each rack). This gives you the possibility to recover
from a rack failure (most likely switch failure) as long as you keep the
pool on seventy-five percent loaded on CPU, RAM, Network and Storage.

BUT!!!

You must also think about maximum VM sizing. If you size a single VM
over twenty-five percent of the capacity of one hypervisor, then you
risk not being able to migrate all machines from the four hypervisors
that are allowed to fail.

.. |image0| image:: http://monkeyswithbuttons.files.wordpress.com/2010/08/dilberts-monkey-virtualisation.gif
   :target: http://monkeyswithbuttons.files.wordpress.com/2010/08/dilberts-monkey-virtualisation.gif
