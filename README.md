# GettingAndCleaningData
Repository holds CodeBook.md, README.md, run_analysis.R script and other supporting files.

## Script review:
* Lines 1-3 identify required libraries.
* Lines 4-6 can be used to download the base files used in this analysis (Remove leading comments - #)
* Lines 7-15 read and combine the Test & Train - observations (xData), subjects (subData) and activities codes (yData) data into unique combined data frames
* Lines 16-19 reads the features names, modifies them to acceptable variable names, adds a ".mean.." and loads them as column names for the combined observation data frame (xData)
* Line 20 selects the observation data frame columns matching the criteria of Mean and Standard Deviation into a new data frame (xsData)*
* Lines 21-22 read activities labels  into an activity label data frame (actTable) and then joins with the activities codes data frame (yData) to create the activities data frame  (yaData) 
* Lines 23-34 Combines the activities data frame (yaData) and the selected observation columns (xsData) producing the (nxsData) adding the activities and naming column 1 of the new data frame to aCtivities
* Line 25 removes unneeded data frames
* Lines 26-27 Combines the subject data frame (subData) and the named observations data frame (nxsData) creating the full data frame (snxsData)  and naming column 1 of the new data frame to sUbject
* Line 28 groups the full data frame by sUbject and aCtivities creating the grouped data frame (gsnxsData)
* Line 29 summaries each of the columns of the grouped data frame (gsnxsData)  on the function mean by the groupings variables (sUbject,aCtivites) creating a grouped data frame (sgsnxsData)
* Line 30 writes the table to a txt file in the working directory  


        
###  run_analysis.R    
    library(httr)  
    library(sqldf)  
    library(dplyr)  
    `# REMOVE TO LINK url TO THE zip FILE # url <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles  %2FUCI%20HAR%20Dataset.zip"   `  
    `# REMOVE TO DOWNLOAD THE ZIP FILE    # GET(url, write_disk(basename(url), overwrite=TRUE))   `   
    `# REMOVE TO UNPACK THE ZIP FILE      # unzip(basename(url))`  
    xteData<-read.table("./UCI HAR Dataset/test/X_test.txt")  
    xtrData<-read.table("./UCI HAR Dataset/train/X_train.txt")  
    xData<-rbind(xteData, xtrData)  
    yteData<-read.table("./UCI HAR Dataset/test/Y_test.txt")  
    ytrData<-read.table("./UCI HAR Dataset/train/Y_train.txt")  
    yData<-rbind(yteData, ytrData)  
    subteData<-read.table("./UCI HAR Dataset/test/Subject_test.txt")  
    subtrData<-read.table("./UCI HAR Dataset/train/Subject_train.txt")  
    subData<-rbind(subteData, subtrData)  
    titleData<-read.table("./UCI HAR Dataset/features.txt")  
    titleData$V3<-paste(gsub("[-$%()]",".",titleData$V2),".mean..", sep="")
    featTitles<-t(subset(titleData, select = 3))  
    colnames(xData) <- c(featTitles)  
    xsData<-xData[,c(unlist(sqldf("select V1 from titleData where V2 like '%mean()%' or V2 like '%std%' or V2 like '%Mean)'")))]  
    actTable<-read.table("./UCI HAR Dataset/activity_labels.txt")  
    yaData <- sqldf("SELECT V2 FROM yData JOIN actTable USING(V1)")  
    nxsData<-cbind(yaData, xsData)  
    colnames(nxsData)[1] <- "aCtivity"  
    rm(xData, xteData, xtrData, yData, yteData, ytrData, subteData, subtrData, titleData, featTitles, actTable)  
    snxsData<-cbind(subData, nxsData)  
    colnames(snxsData)[1] <- "sUbject"  
    gsnxsData<-group_by(snxsData, sUbject, aCtivity)  
    sgsnxsData<-summarise_each(gsnxsData, funs(mean))  
    write.table(sgsnxsData, file="ActivityDataMeansGroupedBySubAct.txt", row.names=FALSE)  
    
    
##Tidy Data

Based on my readings a Tidy Data set has the following characteristics: 

1. Each variable forms a single column
2. Each observation forms a row
3. Each type of observational unit forms a table 
  
versus  
  
Examples of messy data sets:

* Column headers are values, not variables names
* Multiple variables are stored in one column
* Variables are stored in both rows and columns
* Multiple types of observation units are stored in the same table
* A single observational unit is stored in multiple tables

Some further principles provided during my readings include:  

* Focus should be put on a single data set rather than the many connected data sets common in relational databases
* Tidy data should make it easy for an analyst or a computer to extract variables because it provides a standard way of structuring a data set
* One way of organizing variables is by their role
* Tidy data is only worthwhile if it makes analysis easier

Readings I used to understand what makes a Tidy Data set were:  Hadley Wickham's “Tidy Data” excerpt from the Journal of Statistical Software;  the Getting and Sharing videos;  information and direction provided when participating in the discussion forums, especially “David Hood's Project FAQs discussion – https://class.coursera.org/getdata-010/forum/thread?thread_id=49;  the write-up "How to share data with a statistician"; and much searching of the internet.  

##Wide vs Narrow Data Set  Submission

Based on my new found understanding of a Tidy Data set, especially the last principel - Tidy data is only worthwhile if it makes analysis easier -  said to me the best option to meet the project instructions is a Wide Data Set. 

###Viewing the step 5 data set/file -  ActivityDataMeansGroupedBySubAct.txt
I found Excel, OpenOfficeCalc or some other like tool best for viewing the data. Use space as the delimiter.

    > * Criteria for selecting the Mean and Standard Deviation columns is discussed in the accompanying CodeBook.md