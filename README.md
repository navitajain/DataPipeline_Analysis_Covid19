# Covid19 Data Pipeline on Databricks
Data Pipeline of Covid19 on Databricks  

Creating a Data Pipeline for Covid data from a git repository of JHU CSSE were Covid19 cases are daily reported. The current pipeline is created in Databricks and transformed data is loaded in DBFS file system. The pipeline is build in a community version of Databricks thus 1master and 1worker node. 

Also, used delta lake to write the data in dbfs. This made merging new column/schema super easy, as the data is stored in a time-series fashion (@columns are dates). So, each day a new column is merged into existing delta table. 

# The next step - 
Is to build a Robust Pipeline using DELTA LAKE's BATCH Streaming. The problem with current pipeline is that merging schema appends rows to the table instead of extending the row if the id("Country") exists. A customized solution is implemented that groups the ids and sums the columns. But this is not a very robust solution and thus plan is to use Delta lake's batch streaming readStream and writeStream API call's to address above.

Finally, will perform some analysis on this dataset.
