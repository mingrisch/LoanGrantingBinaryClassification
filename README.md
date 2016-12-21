# LoanGrantingBinaryClassification

In December 2016, I signed up for a Data Science Professional Project at https://courses.edx.org/courses/course-v1:Microsoft+DAT102x+1T2017/info. This course is the capstone project of the  Microsoft Professional Program for data science, and it takes the form of a Cortana intelligence competition. The goal of the competition is to predict (as a binary classification problem), whether a loan will be fully repaid. 

(expand a little on the design: public and private data and leaderboard, etc)

This document is a step-by-step documentation of my attempts at this binary classification problem.

# Public training data

I quickly explored teh public training set, entirely with the tools available in the azure ML studio. The public training data consists of approximately 77k datasets. Two variables are laond and customer ID, which probably have little predictive value. Outcome variable is 'Loan status', with the two levels  'Fully repaid' and xx. The remainng columns are categorical/string and numerical variables, with unknown predictive power. I identified two columns with 14k missing values, these columns were 'Credit Score' and 'Annual income'. This exploration took approximately five minutes and identified the following action items:

* Immediate: deal with missing values in 'Annual income' and 'Credit score'
* Possibly: drop loan and customer ID (maybe - it may also be that single customers ahve several entries)
* Possibly: deal with string and categorical values
* Possibly: scale variables (depending on the ML model)

# Initial Model

Initially, I tried to establish a very simple and non-optimized model as quickly as possible, in order to get accustomed to the whole competition pipeline. I worked entirely in the AzureML studio, as provided by the free plan. I replaced missing values in the two columns using the 'MICE' algorithms and the default value of 5 iterations. I used a simple train/test split, stratified on the 'Loan status' column, and I trained a two-class boosted decision tree model with default setting. After training, I set up a predictive web service, inserted an 'Apply sql transformation' step and submitted the competition entry.

(SQL: select "Loan ID" , "Scored Labels" as Status_Pred from t1;)
This very simple initial model took me on place 12 in the public leaderboard with an accurac of 68.673% (admittedly, there were only 28 submissions at the time of entry). However, and more importantly, this simple model provides a fully functional base on which I can perform subsequent feature engineering, model tuning and general improvements of prediction.

# LGBCD 1-missing-values-fixed
After an exploratory data analysis (to be posted separately), I iterated the initial two-class boosted decision tree model experiment. Briefly, I inserted an 'Execute R script' module, which replaced Current Loan Amount values of 99999999 with NAs. In the following clean-missing-data module, the such flagged values were imputed using MICE with five iterations - just as Credit score and Annual income.

In a further preprocessing step, we should deal with the Credit Score issue.

The model with fixed missing values achieved an accuracy on the private test set of 70.893508, PL 36/144

# LGBCD 1- missing-values-fixed-tuned
The previous model was trained with default settings of the two-class boosted decision tree model

CV accuracy: 0.872, AUC 0.835
PL: 71.687587

# LGBCD-2-missing-values-fixed-tuned-CSfix

CV accuracy: 0.843, AUC: 0.742
PL 71.694336 (32/278 at time of submission)

I note that all our models appear to slightly overfit on the training data - CV accuracy is higher than accuracy on the test set. This indicates a need for more regularization, possibly.

# Update: LGBCD-7- fully trained

I started working on the categorical features, and handled Years in Job, and Purpose (cut down to three indicator variables).
Train/Test Acc 0.764, AUC 0.74
PL 
as of now, untuned.

submitting competition entries is apparently not possible atm

# To do: XGBoost

ON a parallel track, I am working with encouraging results with xgboost.
handling pre-trained models in azure is a pain, though.


# LGBC 11, azure only.

## Todo:

* deal with duplicate loan IDs: should we average?? https://miteshgadgil.github.io/assets/Loan-Prediction-project/Data_Cleaning.html
* fix monthly debt: remove leading \\$
* fix maximum open credit
