Get the data
1.Download the file and put the file in the data folder
if(!file.exists("./data")){dir.create("./data")}
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl,destfile="./data/Dataset.zip",method="curl")
2.Unzip the file
unzip(zipfile="./data/Dataset.zip",exdir="./data")
3.unzipped files are in the folderUCI HAR Dataset. Get the list of the files
path_rf <- file.path("./data" , "UCI HAR Dataset")
files<-list.files(path_rf, recursive=TRUE)

Read data from the files into the variables
Read the Activity files
dataActivityTest  <- read.table(file.path(path_rf, "test" , "Y_test.txt" ),header = FALSE)
dataActivityTrain <- read.table(file.path(path_rf, "train", "Y_train.txt"),header = FALSE)
Read the Subject files
dataSubjectTrain <- read.table(file.path(path_rf, "train", "subject_train.txt"),header = FALSE)
dataSubjectTest  <- read.table(file.path(path_rf, "test" , "subject_test.txt"),header = FALSE)
Read Fearures files
dataFeaturesTest  <- read.table(file.path(path_rf, "test" , "X_test.txt" ),header = FALSE)
dataFeaturesTrain <- read.table(file.path(path_rf, "train", "X_train.txt"),header = FALSE)
Merges the training and the test sets to create one data set
1.Concatenate the data tables by rows
dataSubject <- rbind(dataSubjectTrain, dataSubjectTest)
dataActivity<- rbind(dataActivityTrain, dataActivityTest)
dataFeatures<- rbind(dataFeaturesTrain, dataFeaturesTest)
2.set names to variables
names(dataSubject)<-c("subject")
names(dataActivity)<- c("activity")
dataFeaturesNames <- read.table(file.path(path_rf, "features.txt"),head=FALSE)
names(dataFeatures)<- dataFeaturesNames$V2
3.Merge columns to get the data frame Data for all data
dataCombine <- cbind(dataSubject, dataActivity)
Data <- cbind(dataFeatures, dataCombine)
Extracts only the measurements on the mean and standard deviation for each measurement
1.	Subset Name of Features by measurements on the mean and standard deviation
i.e taken Names of Features with �mean()� or �std()�
subdataFeaturesNames<-dataFeaturesNames$V2[grep("mean\\(\\)|std\\(\\)", dataFeaturesNames$V2)]
2.	Subset the data frame Data by seleted names of Features
selectedNames<-c(as.character(subdataFeaturesNames), "subject", "activity" )
Data<-subset(Data,select=selectedNames)
Uses descriptive activity names to name the activities in the data set
1.Read descriptive activity names from �activity_labels.txt�
activityLabels <- read.table(file.path(path_rf, "activity_labels.txt"),header = FALSE)
2.	facorize Variale activity in the data frame Data using descriptive activity names
3.	check
head(Data$activity,30)
##  [1] STANDING STANDING STANDING STANDING STANDING STANDING STANDING
##  [8] STANDING STANDING STANDING STANDING STANDING STANDING STANDING
## [15] STANDING STANDING STANDING STANDING STANDING STANDING STANDING
## [22] STANDING STANDING STANDING STANDING STANDING STANDING SITTING 
## [29] SITTING  SITTING 
## 6 Levels: WALKING WALKING_UPSTAIRS WALKING_DOWNSTAIRS ... LAYING
Appropriately labels the data set with descriptive variable names
In the former part, variables activity and subject and names of the activities have been labelled using descriptive names.In this part, Names of Feteatures will labelled using descriptive variable names.
�	prefix t is replaced by time
�	Acc is replaced by Accelerometer
�	Gyro is replaced by Gyroscope
�	prefix f is replaced by frequency
�	Mag is replaced by Magnitude
�	BodyBody is replaced by Body
names(Data)<-gsub("^t", "time", names(Data))
names(Data)<-gsub("^f", "frequency", names(Data))
names(Data)<-gsub("Acc", "Accelerometer", names(Data))
names(Data)<-gsub("Gyro", "Gyroscope", names(Data))
names(Data)<-gsub("Mag", "Magnitude", names(Data))
names(Data)<-gsub("BodyBody", "Body", names(Data))
Creates a second,independent tidy data set and ouput it
In this part,a second, independent tidy data set will be created with the average of each variable for each activity and each subject based on the data set in step 4.
library(plyr);
Data2<-aggregate(. ~subject + activity, Data, mean)
Data2<-Data2[order(Data2$subject,Data2$activity),]
write.table(Data2, file = "tidydata.txt",row.name=FALSE)
