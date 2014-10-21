
# Overview
The purpose of this project is to...

The data were available in two separate comma-separated files.

[Training Data] (https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv)     
[Testing Data] (https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv)

# Import Data
The first step is to

```

# read in training data 
training <- read.table(file = "./data/pml-training.csv", header = T, sep = ",")
str(training)

# read in testing data
testing <- read.table(file = "./data/pml-testing.csv", header = T, sep = ",")
str(testing)

```


# Clean Data
Next, we must...



# Apply Random Forest Model to Validation Dataset


```

## read in validation data

valid <- read.table(file = "./data/pml-testing.csv", header = T, sep = ",")
str(valid)
dim(valid)
```
The numeric-only structure of the training data was applied here for simplicity.
Additionally, the name and problem ID were included.
```

## numeric only with name and problem id


valid_clean <- valid

keep <- sapply(valid_clean,is.numeric)
name <- valid_clean$user_name
problem_id <- valid_clean$problem_id

valid_clean2 <- cbind(problem_id, name,valid_clean[,keep])

dim(valid_clean2)
str(valid_clean2)
fix(valid_clean2)
```
The fitted random forest model was applied to the 20 observations of the validataion
dataset and attached.

```
## use model to predict classe for validation data set & attach

pred_valid <- predict(modFit,valid_clean2)
pred_valid

valid_final <- cbind(pred_valid,valid_clean2)
```
The predictions were written to individual text files using the script provided by the 
course administrators.
```
## write answers to file

setwd("C://.Coursera/machine learning/project")

pml_write_files = function(x){
	n = length(x)
	for(i in 1:n){
	filename = paste0("./predictions/problem_id_",i,".txt")
 	write.table(x[i],file=filename,quote=FALSE,row.names=FALSE,col.names=FALSE)
	}}

pml_write_files(pred_valid)

```
These twenty files were individually submitted to the online grader per the instructions.
The resulting accuracy of 100% correct suggests a good model fit.







