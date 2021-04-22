环境：python3.6 7  spark2.4 scala2.11
# 需要事先将 jar放到spark里面   scala-logging-api_2.10-2.1.2.jar   scala-logging-slf4j_2.10-2.1.2.jar graphframes-0.7.0-spark2.4-s_2.11.jar 
#pip install graphframes


# pyspark_dbscan
An Implementation of DBSCAN on PySpark

```python
import dbscan
from sklearn.datasets import make_blobs
from pyspark.sql import types as T, SparkSession
from scipy.spatial import distance

spark = SparkSession \
        .builder \
        .appName("DBSCAN") \
        .config("spark.jars.packages", "graphframes:graphframes:0.7.0-spark2.3-s_2.11") \
        .config('spark.driver.host', '127.0.0.1') \
        .getOrCreate()
X, labels_true = make_blobs(n_samples=750, centers=centers, cluster_std=0.4, random_state=5)
data = [(i, [float(item) for item in X[i]]) for i in range(X.shape[0])]
schema = T.StructType([T.StructField("id", T.IntegerType(), False),
                               T.StructField("value", T.ArrayType(T.FloatType()), False)])
df = spark.createDataFrame(data, schema=schema)
df_clusters = dbscan.process(spark, df, .2, 10, distance.euclidean, 2, "checkpoint")
```
