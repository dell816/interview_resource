

Deletion vector



In a normal scenario, when we modify data in a particular parquet file, the whole parquet file will be rewritten. Consider we have millions rows in a file,

and we just want to modify one record. This is an optimization issue. with deletion vector, a flag would be added to your row, and the file will not be rewritten

and will be flagged as deleted. When you read a file, only rows will be read which marked flag not deleted. next time when you run maintenance which is

Optimized, or predictive optimize command, the file will be rewritten. 



&nbsp;

Liquid Clustering

helps improve performance for data with high Cardinality



When you enable L.C in a particular column in a delta table, you do not need to rewrite data; the incremental will be automatically get adjusted as per

of the cluster column that has already mentioned.

&nbsp; 

What is liquid clustering used for?

The following are examples of scenarios that benefit from clustering:



. Tables often filtered by high cardinality columns.

. Tables with significant skew in data distribution.

. Tables that grow quickly and require maintenance and tuning effort.

. Tables with access patterns that change over time.

. Tables where a typical partition column could leave the table with too many or too few partitions.



You can do clustering in an existing or a new table :



ALTER TABLE dev.bronze.sales CLUSTER BY (InvoiceNo);



New Table:



CREATE.TABLE dev.bronze.sales\_ct CLUSTER.BY (InvoiceNo)

AS



select .\* from

read\_files(

.'dbfs:/databricks-datasets/online\_retail/data-001/data.csv',

header .= > true,

.format .= >.'csv'

)



>> describe extended dev.bronze.sales 



Table Properties



\[**clusteringColumns**=

\[\["**InvoiceNo**"]],delta.checkpointPolicy=v2,delta.enableDeletionVectors=true,delta.enableRowTracking=true,delta.feature.clus

ported,delta.feature.deletionVectors=supported,delta.feature.domainMetadata=supported,delta.feature.rowTracking=supp

ature.v2Checkpoint=supported,delta.minReaderVersion=3,delta.minWriterVersion=7,delta.rowTracking.materializedRowCom

ColumnName =\_ row-commit-version-col-512e2e40-8dd1-4d1b-b3d4-

bca11d38a0c0,delta.rowTracking.materializedRowldColumnName =\_ row-id-col-e7b99c12-9594-4918-9f36-3721d0434765]

