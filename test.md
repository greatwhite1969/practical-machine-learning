
# Overview
The purpose of this project is to...

The data were available in two separate comma-separated files.

[Training Data] (https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv)     
[Testing Data] (https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv)

Note that hereafter the 'testing' dataset above will be called  'validation.' This is to 
distiguish it from the testing data partitioned from the training data, as noted below.  

# Import Data
The first step is to read in the training data and examine it's structure.  

```
# read in training data 
training <- read.table(file = "./data/pml-training.csv", header = T, sep = ",")
str(training)

```

# Clean Data
Next, we must...



# Random Forest Model

The package randomForest was loaded and fitted to the training dataset.

```

modFit <- randomForest(classe ~ ., data = train_final)
modFit
```

The results show an out-of-bag (OOB) error of 0.21%, confirmed by the strong diagonal elements of 
the confusion matrix.

```
Type of random forest: classification
                     Number of trees: 500
No. of variables tried at each split: 7

        OOB estimate of  error rate: 0.21%
Confusion matrix:
     A    B    C    D    E class.error
A 3348    0    0    0    0 0.000000000
B    2 2276    1    0    0 0.001316367
C    0    4 2049    1    0 0.002434275
D    0    0   11 1918    1 0.006217617
E    0    0    0    5 2160 0.002309469
```

# Apply Random Forest MOdel to the Testing Dataset

The fitted model was applied to the testing data and the resulting predicitons were used to 
construct a confusion matrix.

```
pred_rf <- predict(modFit ,test_final,type = "response") 
table(pred_rf,test_final$classe)

pred_rf    A    B    C    D    E
      A 2232    0    0    0    0
      B    0 1518    5    0    0
      C    0    0 1362    5    0
      D    0    0    1 1280    4
      E    0    0    0    1 1438


```
The results of this matrix confirm the OOB error.  The 16 off-diagonal elements yield an 
out-of-sample error of 0.20%.


# Apply Random Forest Model to Validation Dataset

The good performance of the model in both the training OOB and testing steps demonstrate 
that the model is ready for the validation stage.

```

## read in validation data

valid <- read.table(file = "./data/pml-testing.csv", header = T, sep = ",")
str(valid)
dim(valid)
```
The numeric-only structure of the training data was applied here for simplicity.  Additionally, 
the user name and problem ID were included.
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
The fitted random forest model was applied to the 20 observations of the validataion dataset 
and attached.
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







