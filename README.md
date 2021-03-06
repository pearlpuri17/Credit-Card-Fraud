# Credit-Card-Fraud
#Predicting credit card fraud using XGBoost 

install.packages("caTools")
install.packages("xgboost")
install.packages("party")
install.packages("ROCR")
library(caTools)
library(xgboost)
library(Matrix)
library(party)
library(ROCR)
path = "D:/ccard"
setwd(path)
cdata = read.csv("creditcard.csv", sep = ",", header = TRUE)
summary(cdata)
str(cdata)
nrow(cdata)
ncol(cdata)
table(cdata$Class)
# This is our dependent variable and class imbalance is pretty evident from here
# we will use undersampling to deal with class imbalance as the number of records are high(284807)
#let's split data into test and train
set.seed(101)
sample = sample.split(cdata$Class, SplitRatio = .75)
train = subset(cdata, sample == TRUE)
ncol(train)
test = subset(cdata, sample == FALSE)
#need to remove the predictor here which is class
sparse_matrix <- sparse.model.matrix(Class~ .-1, data = train)
head(ctrain)
output_vector = as.numeric(train[,(31)])
output_vector
bst = xgboost(data = sparse_matrix, label = output_vector, max.depth =4, eta = 1, nthread =2, nround = 26, objective = "binary:logistic")
importance <- xgb.importance(dimnames(train)[[2]], model = bst)
head(importance)
xgb.plot.importance(importance_matrix = importanceRaw)
importanceRaw <- xgb.importance(dimnames(train)[[2]], model = bst, data = sparse_matrix, label = output_vector)
importanceClean <- importanceRaw[,`:=`(Cover=NULL, Frequency=NULL)]
head(importanceClean)
xgb.plot.importance(importance_matrix = importanceRaw)
log_model = glm(Class~V17+V14+V10+V7+V4, data = train, family = "binomial")
summary(log_model)
pred_model = predict(log_model, newdata = test, type = "response")
pr <- prediction(pred_model, test$Class)
prf <- performance(pr, measure = "tpr", x.measure = "fpr")
#plotting the ROC chart
plot(prf)
auc <- performance(pr, measure = "auc")
auc <- auc@y.values[[1]]
auc
