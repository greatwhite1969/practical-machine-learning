
# Overview
The purpose of this project is to predict exercise performance on a five-point scale,
A through E, based on a large number of measurement indices.  A classification of 'A' indicates
perfect performance, with B through E indicating deficiencies.  

The data were available in two separate comma-separated files.

[Training Data] (https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv)     
[Testing Data] (https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv)

This data is part of a larger work previously published:

Ugulino, W.; Cardador, D.; Vega, K.; Velloso, E.; Milidiu, R.; Fuks, H. Wearable Computing: Accelerometers' Data Classification of Body Postures and Movements. Proceedings of 21st Brazilian Symposium on Artificial Intelligence. Advances in Artificial Intelligence - SBIA 2012. In: Lecture Notes in Computer Science. , pp. 52-61. Curitiba, PR: Springer Berlin / Heidelberg, 2012. ISBN 978-3-642-34458-9. DOI: 10.1007/978-3-642-34459-6_6. 



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
The dataset is very large, with 19,622 observations and 160 total columns including the dependent 
variable classe,name of the subject, and observation number.  Many columns were incomplete for all 
observations.  The first step was to remove all variables with missing values.  This removed 67 columns.
```
## remove variables with at least one NA from training data

train_clean <-train[ , colSums(is.na(train)) == 0]
dim(train_clean)

```
User name and observation number were removed from the dataset.  Including user name as a covariate
could result in the model not being useable outside of the six individuals in the study.  Observation
number should have no predictive power in the model.  
```

## take out observation number and user name

table(train_clean$user_name,train_clean$classe)
train_clean <- train_clean[,-(1:2)]

```
To further reduce dimensionality, all factor variables were removed from the dataset.  Many of these
factors had over 20 levels which could possible lead to long run times for the model.  
```

## numeric only with classe

keep <- sapply(train_clean,is.numeric)

```
Classe was added back in, resulting in a dataset with 56 total columns:  55 possible covariates and
the dependent variable classe.
```

classe <- train_clean$classe
train_clean2 <- cbind(classe,train_clean[,keep])
dim(train_clean2)

```


# Split a Testing Set Off from the Training Set
One method to impose cross-validation is to set aside part of the training data as a testing set.
As [Breiman and Cuttler] (http://www.stat.berkeley.edu/~breiman/RandomForests/cc_home.htm#features) note, the out-of-bag sampling by random forest models precludes the need for cross-validation.  However, it is done here as an additional
check on the model.

The clean data was split 60% to train the random forest model, and 40% for model testing.  This resulted
in 11,776 observations available for training and 7,846 for testing.
```

library(caret)

inTrain <- createDataPartition(y = train_clean2$classe,
                               p = 0.60, list = FALSE)

train_final <- train_clean2[inTrain,]
dim(train_final)

test_final <- train_clean2[-inTrain,]
dim(test_final)

```


# Random Forest Model

The package randomForest was loaded and fitted to the 11,776 observations in the training dataset.
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


# Apply Random Forest Model to the Testing Dataset

The fitted model was applied to the 7,846 observations in the testing data and the resulting 
predicitons were used to construct a confusion matrix.
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
The results of this matrix match the OOB error within three decimal places.  The sum of the off-diagonal 
elements yields an out-of-sample error of 0.20% (0.0020 = 16 / 7846).  


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
the user name and problem ID were included for identification purposes.
```

## numeric only with name and problem id

valid_clean <- valid

keep <- sapply(valid_clean,is.numeric)
name <- valid_clean$user_name
problem_id <- valid_clean$problem_id

valid_clean2 <- cbind(problem_id, name,valid_clean[,keep])

dim(valid_clean2)

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







