#How to run Achilles Heel only: 

Execution of all analyses computations is not necessary if all you want to do is to run new data quality measures in a revised version of Heel. Instead of 10+ hours, you can be done in few minutes with running just heel
```
#initialize your connectionDetails as usual
#set your schema names for where is data and where to store results
  myCdm='cdm5_inst';resDb='results'

#run heel only like this
  heel<-achillesHeel(connectionDetails,cdmDatabaseSchema = myCdm, resultsDatabaseSchema = resDb,cdmVersion = "5")

#optionally - get heel errors and warnings as a small CSV file
  heelRes<-fetchAchillesHeelResults(connectionDetails,resDb)
  write.csv(heelRes,paste0(myCdm,'-01-heel-res.csv'),row.names = F,na = '')

```

#How to save time on running full Achilles - make it finish much earlier
If you are willing to skip cost analyses (not used very often), this smaller set of analyses will finish much earlier
```
#get all possible analyses first
allAnalyses=getAnalysisDetails()$ANALYSIS_ID

#cost analyses may take 15+ hours and may not be always necessary
longAnalyses1=c(1500:1699)

#exclude them
subSet1=setdiff(allAnalyses,longAnalyses1)


#create connection details (modify for your server)
connectionDetails <- createConnectionDetails(dbms="redshift", server="server.com", user="secret",
                            password='secret', schema="cdm5_inst", port="5439")

#run Achilles (and Heel)  with this smaller subSet1 only (this will save several hours (or days) of your execution time)
achillesResults <- achilles(connectionDetails, cdmDatabaseSchema="cdm5_inst", 
                            resultsDatabaseSchema="results", sourceName="My Source Name", 
                            vocabDatabaseSchema="vocabulary",cdmVersion = "5",analysisIds = subSet1)
```

#Execute only few new analyses
Achilles can take a long time to execute. To see new analyses, it is possible to only execute those new analyses. E.g., newly integrated Iris analyses.
Use the following code that specifies a set of analysis_id's.
The key is to specify which anlayses to run, and to specify createTables to FALSE so that this execution will preserve results previously executed.
```R
cdmDatabaseSchema='ccae_v5'    #change to yours
resultsDatabaseSchema='nih'    #change to yours
vocabDatabaseSchema='ccae_v5'  #change to yours
achillesResults <- achilles(connectionDetails,cdmDatabaseSchema=cdmDatabaseSchema,
                            resultsDatabaseSchema=resultsDatabaseSchema,
                            sourceName="My Source Name", 
                            vocabDatabaseSchema=vocabDatabaseSchema,
                            cdmVersion = "5",
                            createTable = F,analysisIds = c(2000,2001))
                            
```


#Data Quality CDM 
These notes relate Achilles and Achilles Heel to Data Quality CDM (DQ CDM)

DQM terminology is slightly different

**Achilles term = DQM term**  
analysis = measure  
stratum = dimension  
rule = check

##Classification of measures
###by PURPOSE	
- general purpose measure (% of males)
- measure specific for DQ (count of rows with invalid provider_id)
###by OUTPUT	
- single row measure  (count of providers)
- multiple rows measure  (medium, large, very large) (depends on stratification)
###by TERMINOLOGY/MODEL	
- terminology dependent measure/rule  (hysterectomy (using SNOMED (SCT0013513) (Athena CIDs)) (ICD9CM, 10PCS, CPT)
- terminology independent measure/rule (eg, at least 1 numerical lab result value in 1000 person sample)
- model independent measure/rule (eg, zombie events, prior conception events)




#overview of analyses (analysisDetail.csv)
This overview gets updated via insert statements in the Achilles main SQL file.
The R code below updates the CSV file. (used by some other parts of code)
```R
#internal function called here but it simply executes the sql passed to it
analyses_overview<-fetchAnySql(connectionDetails,resultsDatabaseSchema,'select * from achilles_analysis')
write.csv(analyses_overview,file='c:/d/Achilles/inst/csv/analysisDetails.csv',row.names=F,na='')
```

#Types of analyses

##By outputed results
###Stratified analyses

use table ACHILLES_results

###distributions 
use table ACHILLES_results_dist
e.g., 103,104,105,106,107,203,206,211,403,406,506,511,512,513,514,515,603,606,704,706,715,716,717,803,806,815

##By nature

###general
Some analyses are checking data size (and useful in general)  
###conformance to data model
Other analyses have only while others are s
###data quality specific analyses
e.g., analysis_id 7,8,9,207





#Analyzing Heel Results
###Simple rules: 
There are  simple rules that generate a single error or warning.

###Complex rules
However, some rules (e.g., rule_id 6) can generate multiple rows. The true primary key for output is combination of rule_id and analysis_id


#Possible outputs 
##Heel
All errors encoutered

##Full Data characterization
Full listing of AchillesResults and AchillesResultsDist tables

##Describe
We propose a new output table for Achilles that we refer to as AchillesDescribe 
This can be a subset of full output tables that removes some elements (e.g., frequency data that are sensitive to a data partner within a consortium/study).




