# Code book
  This is the code book for tidy data set

## Variables Description
 Data read from project data files. For description of each data files, refer to README in the project folder.

feature_list - features.txt file
activity - activity_labels.txt file
subject_test - subject_test.txt file
x_test - X_test.txt
y_test - Y_test.txt
subject_train - subject_train.txt
x_train - train/X_train.txt
y_train -train/Y_train.txt

Processing variables
y_test_label - match y_test labels with corresponding activities
tidy_test - binding test subject, test activity and test set 
y_train_label - match y_train labels with corresponding activities
tidy_train - binding train subject, train activity and train set
tidy_set - merged test set and train set
tidy_mean_std - data set with only measurements on the mean and standard deviation for each measurement
tidy_avg - independent tidy data set with the average of each variable for each activity and each subject. 
run_analysis.R 

# Submission for Coursera Data Science Specialization  
# Peer Graded Assignment: Getting and Cleaning Data Course Project
# This script requires dplyr package

# Read feature list and activity names
features_list <- read.table("features.txt", col.names = c("no","features"))
activity <- read.table("activity_labels.txt", col.names = c("label", "activity"))


# Read test dataset and combine into one dataframe
subject_test <- read.table("test/subject_test.txt", col.names = "subject")
x_test <- read.table("test/X_test.txt", col.names = features_list$features)
y_test <- read.table("test/Y_test.txt", col.names = "label")
y_test_label <- left_join(y_test, activity, by = "label")

tidy_test <- cbind(subject_test, y_test_label, x_test)
tidy_test <- select(tidy_test, -label)


# Read train dataset
subject_train <- read.table("train/subject_train.txt", col.names = "subject")
x_train <- read.table("train/X_train.txt", col.names = features_list$features)
y_train <- read.table("train/Y_train.txt", col.names = "label")
y_train_label <- left_join(y_train, activity, by = "label")

tidy_train <- cbind(subject_train, y_train_label, x_train)
tidy_train <- select(tidy_train, -label)

# combine test and train data set
tidy_set <- rbind(tidy_test, tidy_train)

# Extract mean and standard deviation
tidy_mean_std <- select(tidy_set, contains("mean"), contains("std"))

# Averanging all variable by each subject each activity
tidy_mean_std$subject <- as.factor(tidy_set$subject)
tidy_mean_std$activity <- as.factor(tidy_set$activity)

tidy_avg <- tidy_mean_std %>%
  group_by(subject, activity) %>%
  summarise_each(funs(mean))