# GettingAndCleaningData
Repository holds Codebook, README.MD, run_analysis.r scriptand other supporting files.

## Script review:
* Lines 1-3 identify required libraries.
* Lines 4-6 can be used to download the base files used in this analysis (Remove leading comments - #)
* Lines 7-15 read and combine the Test & Train - observations (xData), subjects (subData) and activities codes (yData) data into separate data frames
* Lines 16-19 reads the features names, modifies them to acceptable variable names and loads them as column names for the combined observation data frame (xData)
* Line 20 selects the observation data frame columns matching the criteria of Mean and Standard Deviation into a new data frame (xsData)*
* Lines 21-22 read activities labels  into an activity label data frame (actTable) and then joins with the activities codes data frame to create the activities data frame  (yaData) 
* Lines 23-34 Combinds the activities data frame (yaData) and the selected observation columns (xsData) producing the (nxsData) and adding the activities and naming column 1 of the new data frame to aCtivities
* Line 25 removes unneeded data frames
* Lines 26-27 Combinds the subject data frame (subData) and the named observations data frame (nxsData) creating the full data frame (snxsData)  and naming column 1 of the new data frame to sUbject
* Line 28 groups the full data frame by sUbject and aCtivities creating the grouped data frame (gsnxsData)
* Line 29 summarises each of the columns of the grouped data frame (gsnxsData)  on the function mean by the groupings variables - while exlcuding the grouping variables from the summary creating a data frame (sgsnxsData)
* Line 30 writes the table to a txt file in the working directory

    
        
###  run_analysis.r    
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
    titleData$V3<-gsub("[-$%()]",".",titleData$V2)  
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
    write.table(sgsnxsData, file="SmartPhoneDataBySubAct.txt", row.names=FALSE)  
    
    
##Tidy Data
    What defines a tidy data set? 