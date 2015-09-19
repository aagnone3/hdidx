# **HDIdx**: Indexing High-Dimensional Data

## What is **HDIdx**?

**HDIdx** is a python package for approximate nearest neighbor (ANN) search. Nearest neighbor (NN) search is very challenging in high-dimensional space because of the [*Curse of Dimensionality*](https://en.wikipedia.org/wiki/Curse_of_dimensionality) problem. The basic idea of **HDIdx** is to compress the original feature vectors into compact binary codes, and perform approximate NN search instead of extract NN search. This can largely reduce the storage requirements and can significantly speed up the search.

## Architecture

![Framework](https://raw.githubusercontent.com/wanji/hdidx/master/doc/framework.png)

**HDIdx** has three main modules: 1) `Encoder` which can compress the original feature vectors into compact binary hash codes, 2) `Indexer` which can index the database items and search approximate nearest neighbor for a given query item, and 3) `Storage` module which encapsulates the underlying data storage, which can be memory or NoSQL database like LMDB, for the `Indexer`.

The current version implements following feature compressing algorithms: 

- `Product Quantization`[1].
- `Spectral Hashing`[2].

To use HDIdx, first you should learn a `Encoder` from some learning vectors.
Then you can map the base vectors into hash codes using the learned `Encoder` and building indexes over these hash codes by an `Indexer`, which will write the indexes to the specified storage medium.
When a query vector comes, it will be mapped to hash codes by the same `Encoder` and the `Indexer` will find the similar items to this query vector.


## Installation

**HDIdx** can be installed via `pip`:

```bash
[sudo] pip install hdidx
```

By default, **HDIdx** use kmeans algorithm provided by [*SciPy*](http://www.scipy.org/). To be more efficient, you can install python extensions of [*OpenCV*](http://opencv.org/), which can be installed via `apt-get` on Ubuntu. For other Linux distributions, e.g. CentOS, you need to compile it from source.

```bash
[sudo] apt-get install python-opencv
```

**HDIdx** will use [*OpenCV*](http://opencv.org/) automatically if it is available.

### Windows Guide

General dependencies:

- [Anaconda](https://store.continuum.io/cshop/anaconda/)
- [Microsoft Visual C++ Compiler for Python](http://www.microsoft.com/en-us/download/details.aspx?id=44266)

After install the above mentioned software, hdidx can be installed via `pip` in the *Anaconda Command Prompt*.

## Example

Here is a simple example. See this [notebook](http://nbviewer.ipython.org/gist/wanji/c08693f06ef744feef50) for more examples.

```python
# import necessary packages

import hdidx
import numpy as np

# generating sample data
ndim = 256     # dimension of features
ndb = 10000    # number of dababase items
nqry = 120     # number of queries

X_db = np.random.random((ndb, ndim)).astype(np.float64)
X_qry = np.random.random((nqry, ndim)).astype(np.float32)

# create Product Quantization Indexer
idx = hdidx.indexer.PQIndexer()
# build indexer
idx.build({'vals': X_db, 'nsubq': 8})
# add database items to the indexer
idx.add(X_db)
# searching in the database, and return top-100 items for each query
idx.search(X_qry, 100)
```

## Reference
```
[1] Jegou, Herve, Matthijs Douze, and Cordelia Schmid.
    "Product quantization for nearest neighbor search."
    Pattern Analysis and Machine Intelligence, IEEE Transactions on 33.1 (2011): 117-128.
[2] Weiss, Yair, Antonio Torralba, and Rob Fergus.
    "Spectral hashing."
    In Advances in neural information processing systems, pp. 1753-1760. 2009.
```
