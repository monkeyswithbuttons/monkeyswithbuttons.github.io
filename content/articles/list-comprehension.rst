List Comprehension
##################
:date: 2012-02-21 22:23
:author: geogdog
:category: Devops, Featured, Python
:tags: lists, python
:slug: list-comprehension
:status: published

Why is it better to use list comprehension instead of *map + lambda*?

Take this bit of example code. It generates a random mac address:

*random\_mac.py*

::

    import random

    def random_mac(style=3000):
        '''Create a random MAC address'''
        mac = [ random.randint(0x00, 0xff),
                random.randint(0x00, 0xff),
                random.randint(0x00, 0xff),
                random.randint(0x00, 0xff),
                random.randint(0x00, 0xff),
                random.randint(0x00, 0xff) ]
        if style == 2000:
            # old style - decrecated
            result = ':'.join(map(lambda x: "%02x" % x, mac))
        else:
            # using list comprehension
            result = ':'.join(['%02x' % m for m in mac])
        return result

Now, I'll check the execution timings using *timeit*:

::

    $ python -m timeit -n10000 -r50 'from random_mac import random_mac' 'random_mac()'
    10000 loops, best of 50: 45.7 usec per loop
    $ python -m timeit -n10000 -r50 'from random_mac import random_mac' 'random_mac(style=2000)'
    10000 loops, best of 50: 49.1 usec per loop

The conclusion is that list comprehension (in this case) is a massive
**6.9%** faster!
