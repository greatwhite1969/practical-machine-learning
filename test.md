Enter file contents here
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


