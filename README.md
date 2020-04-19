# Covid19 Data Pipeline on Databricks
Data Pipeline of Covid19 on Databricks  

Creating a Data Pipeline for Covid data from a git repository of ![https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_daily_reports/]JHU CSSE were Covid19 cases are daily reported. The current pipeline is created in Databricks and transformed data is loaded in DBFS file system. The pipeline is build in a community version of Databricks thus 1master and 1worker node. 

The Git repo from were we str daily records are updated is of form - 
The data is stream from Git repo to a local file system in databricks - 
![](Recent_ExtractedFilesFromGit.png) 


Each daily records file contains data like - 
![](DailyReports_16April_FromGit.png)


The pipeline ETLs loads data in DBFS filesystem in  Delta format, into three category files for Confirmed, Deaths, Recovered cases and a log file that works as a transaction log. Below is a view of DBFS filesystem where data is loaded in different category files- 
![](DBFS_fs.png)


The use of Delta Lake made merging the new column/schema super easy, as the data is stored in a time-series fashion (@columns are dates). So, each day a new column is merged into existing delta table. Below is snapshot of the Covid Confirmed data
![](images/Snapshot_CovidConfirmedFile.png)


We also perform some basic analysis on this dataset.

### Next steps - 
Is to build a scalable and robust Pipeline using DELTA LAKE's BATCH Streaming. The current challenge with this pipeline is that mergeSchema appends rows to the table instead of extending existing rows. A customized solution is implemented that aggregates the ids and sums the columns. Thus, the plan is to use Delta lake's batch streaming readStream and writeStream API call's to address this.


