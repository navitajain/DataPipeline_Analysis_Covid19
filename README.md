# Covid19 Data Pipeline on Databricks
Data Pipeline of Covid19 on Databricks  

Creating a Data Pipeline for Covid data from a git repository of JHU CSSE were Covid19 cases are daily reported. The current pipeline is created in Databricks and transformed data is loaded in DBFS file system. The pipeline is build in a community version of Databricks thus 1master and 1worker node. 

Also, used delta lake to write the data in dbfs. This made merging new column/schema super easy, as the data is stored in a time-series fashion (@columns are dates). So, each day a new column is merged into existing delta table. 


### Next steps - 
Is to build a scalable and robust Pipeline using DELTA LAKE's BATCH Streaming. The current challenge with this pipeline is that mergeSchema appends rows to the table instead of extending existing rows. A customized solution is implemented that aggregates the ids and sums the columns. Thus, the plan is to use Delta lake's batch streaming readStream and writeStream API call's to address this.

Finally, will perform some analysis on this dataset.
