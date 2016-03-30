Python string formatting using dictionaries
###########################################
:date: 2012-01-02 20:51
:author: Greg Trahair
:category: Devops, Python
:tags: snippets
:slug: python-string-formatting-using-dictionaries
:status: draft

I've started using the string formatting dictionaries and never looked
back.

As the '%' operator is deprecated in python 3, I've added the future
version too.

::

    Python 3.2 (r32:88445, Feb 21 2011, 21:11:06) 
    [GCC 4.6.0 20110212 (Red Hat 4.6.0-0.7)] on linux2
    Type "help", "copyright", "credits" or "license" for more information.
    >>> fdict = {'name': 'AJ'}
    >>> print('%(name)s is my friend' % fdict)
    AJ is my friend
    >>> '{name} is my friend'.format(**fdict)
    AJ is my friend
