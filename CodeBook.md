---
title: "CODE BOOK"
subtitle: "Human Activity Recognition Using Smartphones"
author: "Saad Mahmood"

output: html_document
---
The purpose of this document is to describe all variables, data, and transformations made in order to get the final tidy data set found in this repository.

## Introduction

Data was sourced from the [UCI Machine Learning Repository](http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones), and downloaded from Coursera in a zip file. The Human Activity Recognition database is a collection of inertial sensor recordings from the Galaxy S smartphone. Observations were made of 30 test subjects, who wore the smartphone on their waist while performing day-to-day activities (such as walking or standing). 

The data in its original form comprises of 8 files that are of relevance for this course project:

* __activity_labels.txt__ activity names metadata
* __features.txt__ feature names metadata
* __subject_train.txt (training)__ subject identifiers for the training set
* __X_train.txt (training)__ sensor measurements for the training set
* __Y_train.txt (training)__ activity labels for the training set
* __subject_test.txt (testing)__ test subject identifiers for the testing set
* __X_test.txt (testing)__ sensor measurements for the testing set
* __Y_test.txt (testing)__ activity labels for the testing set

The data in its original form is organized for the purpose of machine learning - hence the separate files for training and testing. For the purpose of this project, it was required to clean up and consolidate the data into tidy data. This data set can be found in the project repository as __tidyData.txt__, and is generated by the script __run_analysis.R__. The rest of this document will walk through the workings of this script.

## run_analysis.R

### Assigns each data file to a variable
From the extracted folder ```UCI HAR Dataset```, the original data is read into the following variables:

* ```featureNames``` is a [561x2] data frame read from ```UCI HAR Dataset/features.txt```
* ```labelNames``` is a [6x2] data frame read from ```UCI HAR Dataset/activity_labels.txt```
* ```trainSubjects``` is a [7352x1] data frame read from ```UCI HAR Dataset/train/subject_train.txt```
* ```trainMeasurements``` is a [7352x561] data frame read from ```UCI HAR Dataset/train/X_train.txt```
* ```trainLabels``` is a [7352x1] data frame read from ```UCI HAR Dataset/train/Y_train.txt```
* ```testSubjects``` is a [2947x1] data frame read from ```UCI HAR Dataset/test/subject_test.txt```
* ```testMeasurements``` is a [2947x561] data frame read from ```UCI HAR Dataset/test/X_test.txt```
* ```testLabels``` is a [2947x1] data frame read from ```UCI HAR Dataset/train/Y_test.txt```

### Merges testing and training sets
* ```rbind``` is called to merge ```trainSubjects``` and ```testSubjects``` into ```subjects``` (a [10299x1] data frame)
* ```rbind``` is called to merge ```trainLabels``` and ```testLabels``` into ```activityLabels``` (a [10299x1] data frame)
* ```rbind``` is called to merge ```trainMeasurements``` and ```testMeasurements``` into ```measurements``` (a [10299x561] data frame)
* Following this, the script calls the ```rm``` function to clear up the environment of training and testing specific data frames. 

### Names columns, renames activites based on metadata
* ```colnames``` is used to give every column of the data frames an appropriate name
* in particular, the metadata from ```featureNames``` is passed onto ```colnames``` for ```measurements``` in the form of a matrix
* the rows in ```activityLabels``` are replaced with the corresponding labels found in the metadata from ```labelNames```

### Consolidates all data
* ```cbind``` is called to merge all the data together into ```allData``` (a [10299x563] data frame)
* ```select``` is called to only leave appropriate columns in ```allData``` (Subject, Activities, and only the mean and standard deviation for each measurement)
* the final dimensions of the ```allData``` data frame is [10299x88]

### Feature names are edited with ```gsub``` for readability
* ```Acc``` is replaced with ```Accelerometer```
* ```Gyro``` is replaced with ```Magnitude```
* ```Mag``` is replaced with ```Body```
* ```^t``` is replaced with ```Time```
* ```^f``` is replaced with ```Frequency```
* ```tBody``` is replaced with ```TimeBody```
* ```-mean()``` is replaced with ```Mean```
* ```-std()``` is replaced with ```STD```
* ```-freq()``` is replaced with ```Frequency```
* ```angle``` is replaced with ```Angle```
* ```gravity``` is replaced with ```Gravity```

### Second independent tidy data set is created
* As per project requirements, ```tidyData``` (a [180x88] data frame)  is created through a two-step pipeline
* the data is grouped by Subject and Activity using ```group_by```
* the mean of each variable is taken using ```summarise_all(list(mean))```

### ```tidyData``` is exported as ```tidyData.txt```
* the file is saved to the immediate directory

