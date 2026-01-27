**Coalesce vs Repartitions**

Repartition involves data shuffling, whereas Coalesce doesn't.

Repartition can increase or decrease partition numbers, but Coalesce can only decrease, not increase.

Repartition allows uniform data distribution, but Coalesce can't guarantee it.



\# Find the partition info for partitions and repartition

from pyspark.sql.functions import spark\_partition\_id

emp\_1 = emp.repartition(4, "department\_id").withColumn("partition\_num", spark\_partition\_id())





**Shuffling** basically happens whenever you do a wide transformation, and those transformations can be anything

like a group by or a join, so the idea behind shuffling is that Spark tries to bring together all of the data that

is related, but it resides across different Nodes  in the cluster,  

