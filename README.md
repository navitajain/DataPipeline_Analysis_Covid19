# Covid19 Data Pipeline on Databricks
Data Pipeline of Covid19 on Databricks  

Creating a Data Pipeline for Covid data from a git repository of JHU CSSE were Covid19 cases are daily reported. The current pipeline is created in Databricks and transformed data is loaded in DBFS file system. The pipeline is build in a community version of Databricks thus 1master and 1worker node. 

Also, used delta lake to write the data in dbfs. This made merging new column/schema super easy, as the data is stored in a time-series fashion (@columns are dates). So, each day a new column is merged into existing delta table. 


<iframe style="width:100%;border-width:0px;height:303px" srcdoc="<html><head><meta charset='utf-8'><meta name='google' content='notranslate'><meta http-equiv='Content-Language' content='en'><link rel='stylesheet' type='text/css' href='https://databricks-prod-cloudfront.cloud.databricks.com/static/e0089706fa18e1220bd5c1770cbbe155e642e8655ec46405ef5e667285215cf0/lib/css/source_code_pro.css'><link rel='stylesheet' type='text/css' href='https://databricks-prod-cloudfront.cloud.databricks.com/static/e0089706fa18e1220bd5c1770cbbe155e642e8655ec46405ef5e667285215cf0/lib/css/bootstrap.min.css'><link rel='stylesheet' type='text/css' href='https://databricks-prod-cloudfront.cloud.databricks.com/static/e0089706fa18e1220bd5c1770cbbe155e642e8655ec46405ef5e667285215cf0/css/main.css'><meta http-equiv='content-type' content='text/html; charset=UTF8'></head><body><div id='databricks-notebook-cell'></div><script>window.settings = {staticNotebookResourceUrl: 'https://databricks-prod-cloudfront.cloud.databricks.com/static/e0089706fa18e1220bd5c1770cbbe155e642e8655ec46405ef5e667285215cf0/', deploymentMode: 'production'};const notebookModelJson = 'eyJjbHVzdGVyTWVtb3J5Ijo2LCJuYW1lIjoiQ292aWQxOSIsInBhdGgiOiIvMzIwMDU3NzMxOTA3Mjg1OS8zMjAwNTc3MzE5MDcyODYxLzI0MzY2ODg4ODMzNTgyNyIsImd1aWQiOiIwZTZlMTZkOC1kYmE2LTRiODMtYWE5Yy02ZDU0NWMwZGMwMjgiLCJqc1J1bkFsbEFjdGlvbiI6Ik5vQWN0aW9uIiwiaVB5dGhvbk1ldGFkYXRhIjpudWxsLCJyZXZpc2lvbkhpc3RvcnlBdXRvU2F2ZUVycm9yTWVzc2FnZSI6IiIsInBhdGhOYW1lIjoiL1VzZXJzL25hdml0YS5qYWluMDFAZ21haWwuY29tL0NvdmlkMTkiLCJydW5TdGF0dXMiOiJpZGxlIiwiY2x1c3RlcklkIjoiMDQxOS0wMTM1NTAtY2VsbHM2OTkiLCJkZXRhY2hSZWFzb24iOm51bGwsImlkIjoyNDM2Njg4ODgzMzU4MjcsImRyaXZlciI6IjU3NjQ0MTgzNTIyNzY4MTU0NDIiLCJsYW5ndWFnZSI6InB5dGhvbiIsImRlbHRhVmVyc2lvbiI6MTU4NzI1OTkwMDUyMjYyOCwidGFyZ2V0Q2x1c3RlcklkIjoiMDQxOS0wMTM1NTAtY2VsbHM2OTkiLCJpbnB1dFdpZGdldHMiOnt9LCJvd25lcklkIjpudWxsLCJsYXRlc3RVc2VySWQiOm51bGwsImRlbHRhRXZlbnQiOiJ1cGRhdGUiLCJ0eXBlIjoic2hlbGwiLCJjbHVzdGVyUmVhZHkiOnRydWUsImNsdXN0ZXJOYW1lIjoiTXlBbmFseXNpcyIsImRvTm90RmlyZUNhbGxiYWNrIjp0cnVlLCJwYXJlbnRJZCI6MzIwMDU3NzMxOTA3Mjg2MSwiZ2xvYmFsVmFycyI6e30sImRlbGV0ZWQiOmZhbHNlLCJjbHVzdGVyTWV0YWRhdGEiOnsibm90ZWJvb2tzSW5mbyI6e30sIndvcmtlcnNVcmwiOltdLCJtYXN0ZXJVcmwiOiJodHRwOi8vMTAuMTcyLjIyNi41OTo0NDkxNCIsImVic1ZvbHVtZVNpemUiOjAsImVic1ZvbHVtZVR5cGUiOiJHRU5FUkFMX1BVUlBPU0VfU1NEIiwidGVybWluYXRpb25QYXJhbWV0ZXJzIjp7fSwiZmlyc3RPbkRlbWFuZCI6MCwic3RhdGUiOiJSdW5uaW5nIiwiZHJpdmVySXNIZWFsdGh5Ijp0cnVlLCJtaW5Xb3JrZXJzIjowLCJ0YXJnZXROdW1Xb3JrZXJzIjowLCJzcG90QmlkUHJpY2VQZXJjZW50IjoxMDAsIm1heFdvcmtlcnMiOjAsImRlZmF1bHRDbHVzdGVyIjpmYWxzZSwic3BhcmtWZXJzaW9uIjoiNi40Lngtc2NhbGEyLjExIiwiZHJpdmVyVWlVcmwiOiJodHRwOi8vMTAuMTcyLjIyNi41OTo0NDkxNCIsInVzZVNwb3RJbnN0YW5jZSI6ZmFsc2UsImlzRHJpdmVyT25TcG90IjpmYWxzZSwiY3JlYXRvciI6IldlYmFwcCIsImVic1ZvbHVtZUNvdW50IjowLCJjbHVzdGVySWQiOiIwNDE5LTAxMzU1MC1jZWxsczY5OSIsInNwYXJrQ29uZiI6eyJzcGFyay5kYXRhYnJpY2tzLmRlbHRhLnByZXZpZXcuZW5hYmxlZCI6InRydWUifSwic3BhcmtDb250ZXh0SWQiOiI1NTc1MDM2MjU3MTI0ODMyNjM5IiwiZmFsbGJhY2tUb09uRGVtYW5kIjpmYWxzZSwiZHJpdmVyRGlzcGxheWFibGVBZGRyZXNzIjoiZWMyLTM0LTIwOS02NS0zNi51cy13ZXN0LTIuY29tcHV0ZS5hbWF6b25hd3MuY29tIiwiaW5zdGFuY2VQcm9maWxlQXJuIjoiIiwibGlicmFyaWVzIjpbXSwiY3VzdG9tVGFncyI6W10sIm51bVdvcmtlcnMiOjAsInNzaFB1YmxpY0tleXMiOltdLCJkcml2ZXJVcmwiOiJodHRwczovLzEwLjE3Mi4yMjYuNTk6NjA2MCIsInVzZXJJZCI6IjYwMjA4MDEzNDk5NzY2MDciLCJzdGF0ZU1lc3NhZ2UiOiIiLCJub2RlVHlwZUlkIjp7ImlkIjoiZGV2LXRpZXItbm9kZSJ9LCJkcml2ZXJOb2RlVHlwZUlkIjp7ImlkIjoiIn0sImNsdXN0ZXJOYW1lIjoiTXlBbmFseXNpcyIsInRlcm1pbmF0aW9uQ29kZSI6bnVsbCwiem9uZUlkIjoidXMtd2VzdC0yYyIsImlzQXV0b3NjYWxpbmciOnRydWUsIm5vdGVib29rcyI6W10sIm1lbW9yeSI6MTAsImRlZmF1bHRUYWdzIjpbXSwibnVtQWN0aXZlU3BvdEV4ZWN1dG9ycyI6MH0sIm5vdGVib29rRmV0Y2hFcnJvciI6bnVsbCwicGVybWlzc2lvbkxldmVsIjoiQ2FuIE1hbmFnZSIsInByZXNlbmNlTWFya3MiOltdfQ==';const notebookModelCommandCollection = 'W3sidHlwZSI6ImNvbW1hbmQiLCJjb21tYW5kIjoiZGlzcGxheShkYnV0aWxzLmZzLmxzKFwiRmlsZTovdG1wL0NvdmlkX2RhdGFzZXRzXCIpKSIsInBvc2l0aW9uIjoxMC41MzUxNTYyNSwiZ3VpZCI6IjQ5YjE3NGE2LTBhZDUtNGU2Zi04ZjFhLTdlYzc1ZDBlZjdmYyIsImxhc3RNb2RpZmllZEJ5IjoiIiwicGFyZW50IjpudWxsLCJkZWx0YVZlcnNpb24iOjE1ODcyNTk5MDA1MjI2MjYsImxvY2FsVmVyc2lvbiI6MTU2LCJydW5uaW5nIjpmYWxzZSwic3RhdGUiOiJmaW5pc2hlZCIsImNvbW1hbmRTdGF0dXMiOnsic3RhdHVzQ29kZSI6IkNPTU1BTkRfQ09NUExFVEVEIiwic3RhcnRUaW1lIjoxNTg3MjYwOTk2MDAyLCJkYXRhIjp7fSwidmVyc2lvbiI6MCwiZGVmYXVsdE1lc3NhZ2UiOiJDb21tYW5kIGV4ZWN1dGlvbiBjb21wbGV0ZWQifSwic2hvdWxkUnVuIjpmYWxzZSwicmVydW5uaW5nIjpmYWxzZSwic3RhZ2VzIjpbXSwiY29tbWFuZFR5cGUiOiJhdXRvIiwiY29tbWFuZFZlcnNpb24iOjM2LCJyZXN1bHRzIjp7Im92ZXJmbG93IjpmYWxzZSwiZGF0YXNldEluZm9zIjpbXSwiZGF0YSI6W1siZmlsZTovdG1wL0NvdmlkX2RhdGFzZXRzLzA0LTE2LTIwMjAuY3N2IiwiMDQtMTYtMjAyMC5jc3YiLDMxNDIyNl0sWyJmaWxlOi90bXAvQ292aWRfZGF0YXNldHMvMDQtMTgtMjAyMC5jc3YiLCIwNC0xOC0yMDIwLmNzdiIsNDE1MDE3XSxbImZpbGU6L3RtcC9Db3ZpZF9kYXRhc2V0cy8wNC0xNC0yMDIwLmNzdiIsIjA0LTE0LTIwMjAuY3N2IiwzMTEwNjhdLFsiZmlsZTovdG1wL0NvdmlkX2RhdGFzZXRzLzA0LTE3LTIwMjAuY3N2IiwiMDQtMTctMjAyMC5jc3YiLDMxNDg0OF0sWyJmaWxlOi90bXAvQ292aWRfZGF0YXNldHMvMDQtMTUtMjAyMC5jc3YiLCIwNC0xNS0yMDIwLmNzdiIsMzEyNTUxXV0sInBsb3RPcHRpb25zIjpudWxsLCJjb2x1bW5DdXN0b21EaXNwbGF5SW5mb3MiOnt9LCJhZ2dUeXBlIjoiIiwiaXNKc29uU2NoZW1hIjp0cnVlLCJyZW1vdmVkV2lkZ2V0cyI6W10sImFnZ1NjaGVtYSI6W10sInNjaGVtYSI6W3sibmFtZSI6InBhdGgiLCJ0eXBlIjoiXCJzdHJpbmdcIiIsIm1ldGFkYXRhIjoie30ifSx7Im5hbWUiOiJuYW1lIiwidHlwZSI6Ilwic3RyaW5nXCIiLCJtZXRhZGF0YSI6Int9In0seyJuYW1lIjoic2l6ZSIsInR5cGUiOiJcImxvbmdcIiIsIm1ldGFkYXRhIjoie30ifV0sImFnZ0Vycm9yIjoiIiwiYWdnRGF0YSI6W10sImFkZGVkV2lkZ2V0cyI6e30sImRiZnNSZXN1bHRQYXRoIjpudWxsLCJ0eXBlIjoidGFibGUiLCJhZ2dPdmVyZmxvdyI6ZmFsc2UsImFnZ1Nlcmllc0xpbWl0UmVhY2hlZCI6ZmFsc2UsImFyZ3VtZW50cyI6e319LCJlcnJvclN1bW1hcnkiOm51bGwsImVycm9yIjpudWxsLCJzdGFydFRpbWUiOjE1ODcyNjA5OTQ4NTYsInN1Ym1pdFRpbWUiOjE1ODcyNjA5OTQ4NDUsImZpbmlzaFRpbWUiOjE1ODcyNjA5OTU5OTAsImNvbGxhcHNlZCI6ZmFsc2UsImJpbmRpbmdzIjp7fSwiZGlzcGxheVR5cGUiOiJ0YWJsZSIsIndpZHRoIjoiYXV0byIsImhlaWdodCI6ImF1dG8iLCJ4Q29sdW1ucyI6bnVsbCwieUNvbHVtbnMiOm51bGwsInBpdm90Q29sdW1ucyI6bnVsbCwiY29tbWVudFRocmVhZCI6W10sImNvbW1lbnRzVmlzaWJsZSI6ZmFsc2UsInBhcmVudEhpZXJhcmNoeSI6W10sImRpZmZJbnNlcnRzIjpbXSwiZGlmZkRlbGV0ZXMiOltdLCJwcmVzZW5jZU1hcmtzIjpbXSwiY29tbWFuZFRpdGxlIjoiIiwic2hvd0NvbW1hbmRUaXRsZSI6ZmFsc2UsImhpZGVDb21tYW5kQ29kZSI6ZmFsc2UsImhpZGVDb21tbWFuZFJlc3VsdCI6ZmFsc2UsImFjY2Vzc2VkQXJncyI6e30sImNvbW1lbnRzIjpudWxsLCJpZCI6MTk4MTM0OTAwOTk2ODUzNywicmVzdWx0VHlwZSI6InRhYmxlIiwiaXNMb2NrZWRJbkV4YW1Nb2RlIjpmYWxzZSwicmVzdWx0RGJmc1N0YXR1cyI6IklOTElORURfSU5fVFJFRSIsInBhdGgiOiIvMzIwMDU3NzMxOTA3Mjg1OS8zMjAwNTc3MzE5MDcyODYxLzI0MzY2ODg4ODMzNTgyNy8xOTgxMzQ5MDA5OTY4NTM3Iiwic3VidHlwZSI6ImNvbW1hbmQiLCJ1c2VDb25zaXN0ZW50Q29sb3JzIjpmYWxzZSwiY3VzdG9tUGxvdE9wdGlvbnMiOnt9LCJ1c2VyU3BlY2lmaWVkTGFuZ3VhZ2VPcHRpb24iOm51bGwsImlQeXRob25NZXRhZGF0YSI6bnVsbCwiZnVsbFJlc3VsdERiZnNMaW5rIjpudWxsLCJkYXRhc2V0UHJldmlld05hbWVUb0NtZElkTWFwIjp7fSwicGF0aE5hbWUiOiIvVXNlcnMvbmF2aXRhLmphaW4wMUBnbWFpbC5jb20vQ292aWQxOS8iLCJwaXZvdEFnZ3JlZ2F0aW9uIjpudWxsLCJyZXN1bHREYmZzSWQiOm51bGwsIm51aWQiOiI5M2RmNjZiYi1mNWM5LTQ2NzEtOWI5Ni1mNjZjMzA4YjQxZWUiLCJzdHJlYW1TdGF0ZXMiOnt9LCJoaWRlQ29tbWFuZFJlc3VsdCI6ZmFsc2UsInN0cmVhbVN0YXR1c2VzIjp7fSwid29ya2Zsb3dzIjpbXSwicmVzdWx0TGVuZ3RoIjo1LCJpbnB1dFdpZGdldHMiOnt9LCJvd25lcklkIjpudWxsLCJsYXRlc3RVc2VySWQiOm51bGwsImRlbHRhRXZlbnQiOiJ1cGRhdGUiLCJsYXRlc3RVc2VyIjoibmF2aXRhLmphaW4wMUBnbWFpbC5jb20iLCJwYXJlbnRJZCI6MjQzNjY4ODg4MzM1ODI3LCJnbG9iYWxWYXJzIjp7fSwiZGVsZXRlZCI6ZmFsc2UsImFkdmljZSI6W10sImFyZ3VtZW50cyI6e30sImNsdXN0ZXJJZCI6IiIsInNwYXJrQ3R4SWQiOiI1NTc1MDM2MjU3MTI0ODMyNjM5IiwiY2x1c3Rlck1lbW9yeSI6MCwiZXJyb3JUaXBzIjpbXSwiY2x1c3Rlck5hbWUiOiIifV0=';</script><script src='https://databricks-prod-cloudfront.cloud.databricks.com/static/e0089706fa18e1220bd5c1770cbbe155e642e8655ec46405ef5e667285215cf0/js/metrics-graphics.js'></script><script src='https://databricks-prod-cloudfront.cloud.databricks.com/static/e0089706fa18e1220bd5c1770cbbe155e642e8655ec46405ef5e667285215cf0/js/cell-main.js'></script></body></html>"></iframe>





### Next steps - 
Is to build a scalable and robust Pipeline using DELTA LAKE's BATCH Streaming. The current challenge with this pipeline is that mergeSchema appends rows to the table instead of extending existing rows. A customized solution is implemented that aggregates the ids and sums the columns. Thus, the plan is to use Delta lake's batch streaming readStream and writeStream API call's to address this.

Finally, will perform some analysis on this dataset.
