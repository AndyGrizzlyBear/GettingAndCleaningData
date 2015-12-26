GettingAndCleaningData, Course Project

In this project we have the task of turning activity data (sports related)
into a table. We will be implementing reshape2. A full description of this package is here:
description: http://seananderson.ca/2013/10/19/reshape.html. We invoke it in the following way:
library(reshape2)

We get the working directory and set folder with data activity (UCI HAR Dataset) to be the new working directory
Next we load the files "activity_labels.txt" and "features.txt":

Content Of "activity_labels.txt":

1 WALKING
2 WALKING_UPSTAIRS
3 WALKING_DOWNSTAIRS
4 SITTING
5 STANDING
6 LAYING

Content Of "features.txt"

 ```{r}
activityLabels <- read.table("activity_labels.txt")
activityLabels[,2] <- as.character(activityLabels[,2])
features <- read.table("features.txt")
features[,2] <- as.character(features[,2])
```
Later, we extract Only the data related to the mean and the standard deviation

 ```{r}
featuresWanted <- grep(".*mean.*|.*std.*", features[,2])
featuresWanted.names <- features[featuresWanted,2]
featuresWanted.names = gsub('-mean', 'Mean', featuresWanted.names)
featuresWanted.names = gsub('-std', 'Std', featuresWanted.names)
featuresWanted.names <- gsub('[-()]', '', featuresWanted.names)
```

We proceed to loading the datasets of training
```{r}
train <- read.table("train/X_train.txt")[featuresWanted]
trainActivities <- read.table("train/Y_train.txt")
trainSubjects <- read.table("train/subject_train.txt")
```
We merge the training data by columns, 
cbind() function fully explained: http://www.endmemo.com/program/R/cbind.php
```{r}
train <- cbind(trainSubjects, trainActivities, train)
```
We proceed to loading the datasets of tests

```{r}
test <- read.table("test/X_test.txt")[featuresWanted]
testActivities <- read.table("test/Y_test.txt")
testSubjects <- read.table("test/subject_test.txt")
```
We merge the tests data by columns, #cbind() function fully explained:  http://www.endmemo.com/program/R/cbind.php

```{r}
test <- cbind(testSubjects, testActivities, test)
```
We merge the datasets and add labels
rbind() function combines r objects by rows: https://stat.ethz.ch/R-manual/R-devel/library/base/html/cbind.html
```{r}
allData <- rbind(train, test) 
```
c() function combines together a series of 1-length vectors: http://stackoverflow.com/questions/11488820/why-use-c-to-define-vector
```{r}
colnames(allData) <- c("subject", "activity", featuresWanted.names)
```
In the next five lines of R, we turn activities and subjects into factors
```{r}
allData$activity <- factor(allData$activity, levels = activityLabels[,1], labels = activityLabels[,2])
allData$subject <- as.factor(allData$subject)
```
because of this section, library(reshape2) is important.
library(reshape2) fully explained here: http://seananderson.ca/2013/10/19/reshape.html
```{r}
allData.melted <- melt(allData, id = c("subject", "activity"))
allData.mean <- dcast(allData.melted, subject + activity ~ variable, mean)
```
We create our final table
```{r}
write.table(allData.mean, "tidy.txt", row.names = FALSE, quote = FALSE)
```

