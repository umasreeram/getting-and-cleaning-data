## **Get the data**

**The file is downloaded and put in the **** data **** folder**

**if** (!file.exists(&quot;./data&quot;)){dir.create(&quot;./data&quot;)}

fileUrl&lt;-&quot;https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip&quot;

download.file(fileUrl,destfile=&quot;./data/Dataset.zip&quot;,method=&quot;curl&quot;)

**The file is  unzipped**

unzip(zipfile=&quot;./data/Dataset.zip&quot;,exdir=&quot;./data&quot;)

 **Unzipped files are in the folder**** UCI HAR Dataset ****. List of the files is obtained**

path\_rf&lt;-file.path(&quot;./data&quot; , &quot;UCI HAR Dataset&quot;)

files&lt;-list.files(path\_rf, recursive=TRUE)

**Read data from the files into the variables**

Read the Activity files

dataActivityTest  &lt;-read.table(file.path(path\_rf, &quot;test&quot; , &quot;Y\_test.txt&quot;),header=FALSE)

dataActivityTrain&lt;-read.table(file.path(path\_rf, &quot;train&quot;, &quot;Y\_train.txt&quot;),header=FALSE)

Read the Subject files

dataSubjectTrain&lt;-read.table(file.path(path\_rf, &quot;train&quot;, &quot;subject\_train.txt&quot;),header=FALSE)

dataSubjectTest  &lt;-read.table(file.path(path\_rf, &quot;test&quot; , &quot;subject\_test.txt&quot;),header=FALSE)

Read Fearures files

dataFeaturesTest  &lt;-read.table(file.path(path\_rf, &quot;test&quot; , &quot;X\_test.txt&quot;),header=FALSE)

dataFeaturesTrain&lt;-read.table(file.path(path\_rf, &quot;train&quot;, &quot;X\_train.txt&quot;),header=FALSE)

## **Merges the training and the test sets to create one data set**

**Concatenate the data tables by rows**

dataSubject&lt;-rbind(dataSubjectTrain, dataSubjectTest)

dataActivity&lt;-rbind(dataActivityTrain, dataActivityTest)

dataFeatures&lt;-rbind(dataFeaturesTrain, dataFeaturesTest)

**set names to variables**

names(dataSubject)&lt;-c(&quot;subject&quot;)

names(dataActivity)&lt;-c(&quot;activity&quot;)

dataFeaturesNames&lt;-read.table(file.path(path\_rf, &quot;features.txt&quot;),head=FALSE)

names(dataFeatures)&lt;-dataFeaturesNames$V2

**Merge columns to get the data frame **** Data **** for all data**

dataCombine&lt;-cbind(dataSubject, dataActivity)

Data&lt;-cbind(dataFeatures, dataCombine)

## **Extracts only the measurements on the mean and standard deviation for each measurement**

 **Subset Name of Features by measurements on the mean and standard deviation**

i.e taken Names of Features with &quot;mean()&quot; or &quot;std()&quot;

subdataFeaturesNames&lt;-dataFeaturesNames$V2[grep(&quot;mean\\(\\)|std\\(\\)&quot;, dataFeaturesNames$V2)]

 **Subset the data frame **** Data **** by seleted names of Features**

selectedNames&lt;-c(as.character(subdataFeaturesNames), &quot;subject&quot;, &quot;activity&quot;)

Data&lt;-subset(Data,select=selectedNames)

## **Uses descriptive activity names to name the activities in the data set**

 **Read descriptive activity names from &quot;activity\_labels.txt&quot;**

activityLabels&lt;-read.table(file.path(path\_rf, &quot;activity\_labels.txt&quot;),header=FALSE)

**facorize Variale **** activity **** in the data frame **** Data **** using descriptive activity names**
**check**

head(Data$activity,30)

[1] STANDING STANDING STANDING STANDING STANDING STANDING STANDING

[8] STANDING STANDING STANDING STANDING STANDING STANDING STANDING

[15] STANDING STANDING STANDING STANDING STANDING STANDING STANDING

[22] STANDING STANDING STANDING STANDING STANDING STANDING SITTING

[29] SITTING  SITTING

6 Levels: WALKING WALKING\_UPSTAIRS WALKING\_DOWNSTAIRS ... LAYING

**Appropriately labels the data set with descriptive variable names**

In the former part, variables activity and subject and names of the activities have been labelled using descriptive names.In this part, Names of Feteatures will labelled using descriptive variable names.

- prefix t is replaced by time
- Acc is replaced by Accelerometer
- Gyro is replaced by Gyroscope
- prefix f is replaced by frequency
- Mag is replaced by Magnitude
- BodyBody is replaced by Body

names(Data)&lt;-gsub(&quot;^t&quot;, &quot;time&quot;, names(Data))

names(Data)&lt;-gsub(&quot;^f&quot;, &quot;frequency&quot;, names(Data))

names(Data)&lt;-gsub(&quot;Acc&quot;, &quot;Accelerometer&quot;, names(Data))

names(Data)&lt;-gsub(&quot;Gyro&quot;, &quot;Gyroscope&quot;, names(Data))

names(Data)&lt;-gsub(&quot;Mag&quot;, &quot;Magnitude&quot;, names(Data))

names(Data)&lt;-gsub(&quot;BodyBody&quot;, &quot;Body&quot;, names(Data))

## Creating a second,independent tidy data set and ouputing it

In this part,a second, independent tidy data set will be created with the average of each variable for each activity and each subject based on the data set in step 4.

**library** (plyr);

Data2&lt;-aggregate(. ~subject+activity, Data, mean)

Data2&lt;-Data2[order(Data2$subject,Data2$activity),]

write.table(Data2, file=&quot;tidydata.txt&quot;,row.name=FALSE)