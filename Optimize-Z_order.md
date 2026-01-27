https://docs.delta.io/optimizations-oss/#language-sql







Note



While using Databricks Runtime, to control the output file size, set the Spark configuration

spark.databricks.delta.optimize.maxFileSize The default value is 1073741824, which sets the size to 1 GB.

Specifying the value 104857600 sets the file size to 100 MB.



Example: you want to optimize your sales\_order table in 64MB files:



spark.conf.set("spark.databricks.delta.optimize.maxFileSize ",64\*1024\*8)



%sql

OPTIMIZE sales\_order ZORDER BY(Invoice\_num)





If you run the below query, what happens in the background is: Delta has got all the invoice numbers arranged in sequence number and 

then distributed in different part files.

&nbsp;

%sql



select min(invoiceno) mn, max(invoiceno) mx, metadata.file\_name f\_n --show the parquet files name 

from sales\_delta

group by metadata.file\_name

order by min(invoiceno)



Result: 



mn      mx      f\_n

-----------------------------------------------------------------------------

536365  539259  part-00000-410bc7f1-2563-4a5c-ae69-a78e3e07de41-c000.snappy.parquet

539259  541784  part-00001-54d9343c-6d45-4472-850b-7efdc712106d-c000.snappy.parqurt



If you select \* from sales\_delta where invoice\_num = 539259



go to spark UI > open the latest query > dag: Scan parquet spark\_catalog.default.sales\_delta\_partitioned (1)> 

SQL/Dataframe > you will see number of files read = 1



Also you can use OPTIMIZE command on two columns : Invoice\_num \& country



%sq1



OPTIMIZE sales\_delta ZORDER BY (Country, InvoiceNo)



If you see the result, metric column



**numFilesAdded**: 11

numFilesRemoved: 16

> filesAdded: ("min": 514192, "max": 621087, "avg": 545421.2727272727, "totalFiles": 11, "totalSize": 5999634)

> filesRemoved: ("min": 357726, "max": 499297, "avg": 387604.9375, "totalFiles": 16, "totalSize": 6201679)

partitionsOptimized: 0



If you run the select country, min(invoice), max(invoice), metadata.file\_name



you will see : for each of the country the min and max invoice is separated in different part files.



--------------------------------------------------------------------------------------

This command show partition data in files distribution : 

display(dbutils.fs.ls("/data/output/sales\_delta\_partitioned/")





path 								name 

---------------------------------------------------------------------------------------------

dbfs:/data/output/sales\_delta\_partitioned/Country=Australia/	Australia

dbfs:/data/output/sales\_delta\_partitioned/Country=Austria/	Austria

dbfs:/data/output/sales\_delta\_partitioned/Country=Bahrain/	Bahrain





display(dbutils.fs.ls("/data/output/sales\_delta\_partitioned/Country=Australia")


path 																	name

--------------------------------------------------------------------------------------------------------------------------------------------

dbfs:/data/output/sales\_delta\_partitioned/Country=Australia/part-00000-c60956fa-a5d7-4d67-9f4b-acebcf042b2f.c000.snappy.parquet 	part-00000-c60956fa-a5d7-4d67-9f4b-acebc

dbfs:/data/output/sales\_delta\_partitioned/Country=Australia/part-00001-6656eab6-f5a1-4da4-be51-ccd4f6c5aeb6.c000.snappy.parquet		part-00001-6656eab6-f5a1-4da4-be51-ccd4f6c5aeb6.c000.snappy.parquet





-------------------------------------------------------------------------------------

Selective Z-Ordering with Partition filters

Only Optimizes and Z-orders the data for the specific partition



consider we only wand to z\_order the data for Australia



%sql

OPTIMIZE sales\_delta\_partitioned where country ='Australia' ZORDER BY (InvoiceNo)







