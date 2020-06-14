# dtype_pandas_numpy_speed_test
Speed test on Pandas or NumPy 

It looks as thought Pandas Series math operations are always slower (sometime by a factor of 3x) than calling the same operation on the underlying NumPy array. I'd like to discuss this on the Pandas mailing list. 

You are encouraged to run the Python module (below) and to submit your own timings! Giles Weaver has already done so (see the Bugs).

This behaviour is therefore validated on a Dell 9550 Intel i7 laptop and an AMD machine, I'd like to get more. 

This was developed whilst working on my Pandas memory-reduction tool https://github.com/ianozsvald/dtype_diet/

`ser.values` represents the NumPy array, it is expected that Floats are faster than Ints. What's surprising is that the Floats in the Pandas `ser` called directly are significantly slower. The behaviour is consistent with small and large arrays for `min`, `mean`, `std` operations (each of which have slightly different complexity).

# Timing

The following script expands upon:
```
import pandas as pd
import numpy as np
arr = pd.Series(np.ones(shape=1_000_000))
arr.values.dtype                                                                                                                                                         
Out[]: dtype('float64')

# call arr.mean() vs arr.values.mean(), note circa 10* speed difference
# with 4ms vs 0.4ms
%timeit arr.mean()
4.59 ms ± 44.4 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)

%timeit arr.values.mean()
485 µs ± 5.73 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)

# note that arr.values dereference is very cheap (nano seconds)
%timeit arr.values 
456 ns ± 0.828 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)
```

Try `$ python speed_test_combined.py` and post `timings.png` as a bug with some notes on CPU if possible.

Generated with:
```
Pandas: 1.0.4
NumPy: 1.18.5
```

![](timings.png)

This machine: i7:
```
$ inxi -C
CPU:       Topology: Quad Core model: Intel Core i7-6700HQ bits: 64 type: MT MCP L2 cache: 6144 KiB 
           Speed: 1315 MHz min/max: 800/2600 MHz Core speeds (MHz): 1: 1315 2: 1426 3: 1396 4: 1563 5: 1729 6: 2311 7: 1418 
           8: 1660 

$ sudo i7z
i7z DEBUG: i7z version: svn-r93-(27-MAY-2013)
i7z DEBUG: Found Intel Processor
i7z DEBUG:    Stepping 3
i7z DEBUG:    Model e
i7z DEBUG:    Family 6
i7z DEBUG:    Processor Type 0
i7z DEBUG:    Extended Model 5
i7z DEBUG: msr = Model Specific Register
i7z DEBUG: Unknown processor, not exactly based on Nehalem, Sandy bridge or Ivy Bridge
i7z DEBUG: msr device files DO NOT exist, trying out a makedev script
i7z DEBUG: modprobbing for msr

```

# TODO

* Try Pandas dtypes `Int64` `boolean`
* Add slowdown annotations to the graph for intertretability

# Setup on Ian's machine

```
conda create -n dtype_pandas_numpy_speed_test python=3.8 pandas jupyter matplotlib watermark ipython_memory_usage memory_profiler
```
