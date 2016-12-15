GettinandCleaningDataCourseProject
================

GitHub Documents
----------------

This is an R Markdown format used for publishing markdown documents to GitHub. When you click the **Knit** button all R code chunks are run and a markdown file (.md) suitable for publishing to GitHub is generated.

Including Code
--------------

You can include R code in the document as follows:

Create one R script called run\_analysis.R that does the following:
-------------------------------------------------------------------

1. Merges the training and the test sets to create one data set.
----------------------------------------------------------------

2. Extracts only the measurements on the mean and standard deviation for each measurement.
------------------------------------------------------------------------------------------

3. Uses descriptive activity names to name the activities in the data set
-------------------------------------------------------------------------

4. Appropriately labels the data set with descriptive activity names.
---------------------------------------------------------------------

5. Creates a second, independent tidy data set with the average of each variable for each activity and each subject.
--------------------------------------------------------------------------------------------------------------------

if (!require("data.table")) { install.packages("data.table") }

if (!require("reshape2")) { install.packages("reshape2") }

require("data.table") require("reshape2")

1. Merges the training and the test sets to create one data set.
----------------------------------------------------------------

read activity labels
====================

activity\_labels &lt;- read.table("activity\_labels.txt")\[, 2\]

read column names
=================

features &lt;- read.table('features.txt')\[, 2\]

remove parentheses in column names
==================================

features &lt;- gsub('\[()\]', '', features)

read test data
==============

test\_x &lt;- read.table('test/X\_test.txt') test\_y &lt;- read.table('test/y\_test.txt') test\_subject &lt;- read.table("test/subject\_test.txt")

read train data
===============

train\_x &lt;- read.table('train/X\_train.txt') train\_y &lt;- read.table('train/y\_train.txt') train\_subject &lt;- read.table("train/subject\_train.txt")

names(test\_x) &lt;- features names(train\_x) &lt;- features

filter only column names with "-mean" or "-std" suffix
======================================================

extract\_features &lt;- grepl("-mean|-std", features)

subset data on those column names
=================================

test\_x &lt;- test\_x\[, features\[extract\_features\]\] train\_x &lt;- train\_x\[, features\[extract\_features\]\]

map activity labels into column next to of activity IDs
=======================================================

test\_y\[, 2\] &lt;- activity\_labels\[test\_y\[, 1\]\] train\_y\[, 2\] &lt;- activity\_labels\[train\_y\[, 1\]\]

names(test\_y) &lt;- c("activity\_id", "activity\_label") names(train\_y) &lt;- c("activity\_id", "activity\_label")

names(test\_subject) &lt;- "subject" names(train\_subject) &lt;- "subject"

put "subject" into 1st column, and Y into 2nd column of our dataset
===================================================================

test\_data &lt;- cbind(as.data.table(test\_subject), test\_y, test\_x) train\_data &lt;- cbind(as.data.table(train\_subject), train\_y, train\_x)

merge test and train data
=========================

data &lt;- rbind(test\_data, train\_data)

define what columns will be ID and what contain variables
=========================================================

id\_labels &lt;- c("subject", "activity\_id", "activity\_label") data\_labels &lt;- setdiff(colnames(data), id\_labels)

melt data
=========

melt\_data &lt;- melt(data, id = id\_labels, measure.vars = data\_labels)

Apply mean function to dataset using dcast function.
====================================================

tidy\_data = dcast(melt\_data, subject + activity\_label ~ variable, mean)

head(tidy\_data)

Store in plain text file.
=========================

write.table(tidy\_data, file = "tidy\_data.txt")

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
