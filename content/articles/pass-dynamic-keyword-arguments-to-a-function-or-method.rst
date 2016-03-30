Pass dynamic keyword arguments to a function or method
######################################################
:date: 2011-09-14 07:46
:author: Greg Trahair
:category: Python
:slug: pass-dynamic-keyword-arguments-to-a-function-or-method
:status: draft

I recently had the problem that I needed to pass a set of generated
keyword arguments to a function in python. I took a lot of digging, but
I've now started using it a lot.

Lets take a simple function that takes 5 keywork arguments (kwards):

::

    def function_with_five_kwargs(first='1st', second='2nd', third='3rd',
    fourth='4th', fifth='5th'):
        return '%s, %s, %s, %s, %s' % (first,       second, third, fourth, fifth)

If you run this without kwargs, your output is:

::

    1st, 2nd, 3rd, 4th, 5th

If you now want to change all those args, use a dictionary:

::

    my_dict = {'first': 'first',
                   'second': 'second',
                   'third': 'third',
                   'fourth': 'fourth',
                   'fifth': 'fifth'}

    print function_with_five_kwargs(**my_dict)

Now your output will be:

::

    first, second, third, fourth, fifth
