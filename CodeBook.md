# Getting-and-Cleaning-Data
###Merges the training and the test sets to create one data set.
The training and test data sets were imported with read.table() and merged with rbind.data.frame and stored in the data frame allData.

```{r}
train <- read.table("UCI HAR Dataset/train/X_train.txt")
test <- read.table("UCI HAR Dataset/test/X_test.txt")
allData <- rbind.data.frame(train, test)
```
###Extracts only the measurements on the mean and standard deviation for each measurement
The names of the feature was imported in the featureList. Search the mean and standard deviation with the grep function. and the index and the names of the desired features were stored in featureNumber and featureName. Subsetting allData with the index.

```{r}
featureList <- read.table("UCI HAR Dataset/features.txt", colClasses = "character")
featureNumber <- grep("mean|std", featureList[,2])
featureName <- grep("mean|std", featureList[,2], value = TRUE)
selectedData <- allData[, featureNumber]
```

###Uses descriptive activity names to name the activities in the data set
The activity labels of the training and test data sets were imported, as well as theclass labels with their activity name.

```{r}
trainLabels <- read.table("UCI HAR Dataset/train/y_train.txt", colClasses = "character")
testLabels <- read.table("UCI HAR Dataset/test/y_test.txt", colClasses = "character")
activity <- read.table("UCI HAR Dataset/activity_labels.txt")
```

Convert the numbers into activity labels

```{r}
allActivity <- rbind.data.frame(trainLabels, testLabels)
activityWithName <- factor(allActivity[,1], levels = activity[, 1], labels = activity[, 2])
```

###Appropriately labels the data set with descriptive variable names
Change the name of the variables and merge the data with subjects, activity, and results
```{r}
names(selectedData) <- featureName

activityWithName <- data.frame(activity = activityWithName)

trainSubject <- read.table("UCI HAR Dataset/train/subject_train.txt")
testSubject <- read.table("UCI HAR Dataset/test/subject_test.txt")

allSubject <- rbind.data.frame(trainSubject, testSubject)
names(allSubject) <- "subject"

allDataWithLabels <- cbind.data.frame(allSubject, activityWithName, selectedData)
```
###From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject
Group the data set by subject and activiti, and get the mean, and export the tidy data set to a text file.

```{r}
Average <- aggregate(allDataWithLabels[, 3:(ncol(allDataWithLabels))], allDataWithLabels[, 1:2], FUN = mean)
write.table(Average, "tidyData.txt", row.name=FALSE)
```

