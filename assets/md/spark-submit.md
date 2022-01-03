```python
import sys
from random import random
from operator import add
from pyspark.sql import SparkSession
```


```python
spark = SparkSession\
        .builder\
        .appName("PythonPi")\
        .getOrCreate()

partitions = 1000
n = 100000 * partitions
```


```python
def f(_):
    x = random() * 2 - 1
    y = random() * 2 - 1
    return 1 if x ** 2 + y ** 2 <= 1 else 0
```


```python
count = spark.sparkContext.parallelize(range(1, n + 1), partitions).map(f).reduce(add)
```

                                                                                    


```python
print("Pi is roughly %f" % (4.0 * count / n))
```

    Pi is roughly 3.141040



```python
spark.stop()
```
