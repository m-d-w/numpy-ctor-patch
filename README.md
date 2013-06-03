numpy-ctor-patch
================

Patch files for numpy constructor performance issue.

Problem was discussed on StackOverflow here:

http://stackoverflow.com/questions/16819261/why-is-numpy-array-is-sometimes-very-slow


I'm using the numpy.array() function to create numpy.float64 ndarrays from lists.

I noticed that this is very slow when either the list contains None or a list of lists is provided.

Below are some examples with times. There are obvious workarounds but why is this so slow?

Examples for list of None:

### Very slow to call array() with list of None
In [3]: %timeit numpy.array([None]*100000, dtype=numpy.float64)
1 loops, best of 3: 240 ms per loop

### Problem doesn't exist with array of zeroes
In [4]: %timeit numpy.array([0.0]*100000, dtype=numpy.float64)
100 loops, best of 3: 9.94 ms per loop

### Also fast if we use dtype=object and convert to float64
In [5]: %timeit numpy.array([None]*100000, dtype=numpy.object).astype(numpy.float64)
100 loops, best of 3: 4.92 ms per loop

### Also fast if we use fromiter() insead of array()
In [6]: %timeit numpy.fromiter([None]*100000, dtype=numpy.float64)
100 loops, best of 3: 3.29 ms per loop
Examples for list of lists:

### Very slow to create column matrix
In [7]: %timeit numpy.array([[0.0]]*100000, dtype=numpy.float64)
1 loops, best of 3: 353 ms per loop

### No problem to create column vector and reshape
In [8]: %timeit numpy.array([0.0]*100000, dtype=numpy.float64).reshape((-1,1))
100 loops, best of 3: 10 ms per loop

### Can use itertools to flatten input lists
In [9]: %timeit numpy.fromiter(itertools.chain.from_iterable([[0.0]]*100000),dtype=numpy.float64).reshape((-1,1))
100 loops, best of 3: 9.65 ms per loop
