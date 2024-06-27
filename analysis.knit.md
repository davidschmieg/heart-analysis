---
title: "Heart Analysis"
author: "David Schmieg (schmieg2@illinois.edu)"
date: "November 11th, 2020"
output:
  pdf_document:
    toc: yes
  word_document:
    toc: yes
  html_document:
    theme: default
    toc: yes
---







***

## Abstract

Heart disease analysis may be useful for someone who has a family history of heart problems. I used existing data to create a model that uses specific features and may be used to predict the presence of heart disease. I found that this model is somewhat accurate. Ultimately, the model can perhaps say if one should seek a cardiologist and professional assistance, or if that's not necessary while still being vigilant in case the model is not correct.

***

## Introduction

Heart disease analysis could be useful for someone who is transitioning into adulthood. This analysis needs to be done in order to determine what variables are correlated to someone having heart disease. Some variables could be more influential than others, so it's important to figure out what variables could greatly contribute to the presence of heart disease, and which variables one should not worry too much about. The goal of this analysis to use some pre-existing data from four hospitals, and create a dependable model using that data to predict if someone has a any sign of heart disease. 

My mother's side of the family has a history of heart disease. One of my grandfathers died due to climbing up a stairwell with heart problems. For me, this analysis is important because in my opinion, heart disease can be related to whether or not one's family has had a history of heart complications. As I transition to adulthood, knowing what features that may contribute to problems related to heart disease, may help me understand how I should go about my lifestyle in order to keep my heart as healthy as possible.

***

## Methods

First, I read in the given data to test-train split it. Then, I looked over the data to see if any of the numerical vfeatures needed to changed to factors. Some features such as sex (make or female) needed to changed to factor variables.


```
## Warning: package 'tidyverse' was built under R version 4.1.3
```

```
## Warning: package 'tibble' was built under R version 4.1.3
```

```
## Warning: package 'tidyr' was built under R version 4.1.3
```

```
## Warning: package 'readr' was built under R version 4.1.3
```

```
## Warning: package 'purrr' was built under R version 4.1.3
```

```
## Warning: package 'dplyr' was built under R version 4.1.3
```

```
## Warning: package 'forcats' was built under R version 4.1.3
```

```
## Warning: package 'lubridate' was built under R version 4.1.3
```

```
## -- Attaching core tidyverse packages ------------------------ tidyverse 2.0.0 --
## v dplyr     1.1.2     v readr     2.1.4
## v forcats   1.0.0     v stringr   1.5.1
## v ggplot2   3.3.5     v tibble    3.2.1
## v lubridate 1.9.2     v tidyr     1.3.0
## v purrr     1.0.1     
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
## i Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

```
## Warning: package 'caret' was built under R version 4.1.3
```

```
## Loading required package: lattice
## 
## Attaching package: 'caret'
## 
## The following object is masked from 'package:purrr':
## 
##     lift
```

```
## Warning: package 'rpart.plot' was built under R version 4.1.3
```

```
## Rows: 920 Columns: 15
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr  (1): location
## dbl (14): age, sex, cp, trestbps, chol, fbs, restecg, thalach, exang, oldpea...
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

Next, I noticed that some entries for the feature, cholesterol, were zero. It's impossible to have zero cholesterol, so I changed those entries to NA values. Then I checked the proportion of NA values for each feature, and made a new dataset to where no features that had more than 30% of its values being NA, be in the new dataset.


```
##         age         sex          cp    trestbps        chol         fbs 
## 0.000000000 0.000000000 0.000000000 0.061058345 0.229308005 0.100407056 
##     restecg     thalach       exang     oldpeak       slope          ca 
## 0.002713704 0.055630936 0.055630936 0.065128901 0.332428765 0.649932157 
##        thal         num    location 
## 0.527815468 0.000000000 0.000000000
```


Table: Data summary

|                         |       |
|:------------------------|:------|
|Name                     |hd_trn |
|Number of rows           |737    |
|Number of columns        |12     |
|_______________________  |       |
|Column type frequency:   |       |
|factor                   |7      |
|numeric                  |5      |
|________________________ |       |
|Group variables          |None   |


**Variable type: factor**

|skim_variable | n_missing| complete_rate|ordered | n_unique|top_counts                         |
|:-------------|---------:|-------------:|:-------|--------:|:----------------------------------|
|sex           |         0|          1.00|FALSE   |        2|1: 581, 0: 156                     |
|cp            |         0|          1.00|FALSE   |        4|4: 393, 3: 168, 2: 136, 1: 40      |
|fbs           |        74|          0.90|FALSE   |        2|0: 550, 1: 113                     |
|restecg       |         2|          1.00|FALSE   |        3|0: 456, 2: 147, 1: 132             |
|exang         |        41|          0.94|FALSE   |        2|0: 430, 1: 266                     |
|num           |         0|          1.00|FALSE   |        5|0: 330, 1: 211, 2: 88, 3: 87       |
|location      |         0|          1.00|FALSE   |        4|cl: 251, hu: 235, va: 149, ch: 102 |


**Variable type: numeric**

|skim_variable | n_missing| complete_rate|   mean|    sd| p0|    p25|   p50|   p75|  p100|hist                                     |
|:-------------|---------:|-------------:|------:|-----:|--:|------:|-----:|-----:|-----:|:----------------------------------------|
|age           |         0|          1.00|  53.46|  9.25| 29|  47.00|  54.0|  60.0|  77.0|▂▅▇▆▁ |
|trestbps      |        45|          0.94| 131.67| 19.41|  0| 120.00| 130.0| 140.0| 200.0|▁▁▅▇▁ |
|chol          |       169|          0.77| 245.61| 57.72| 85| 208.75| 238.5| 275.0| 564.0|▁▇▂▁▁ |
|thalach       |        41|          0.94| 137.46| 26.09| 60| 120.00| 140.0| 158.0| 202.0|▁▃▇▆▂ |
|oldpeak       |        48|          0.93|   0.88|  1.08| -2|   0.00|   0.5|   1.5|   5.6|▁▇▃▁▁ |



\begin{center}\includegraphics{analysis_files/figure-latex/unnamed-chunk-2-1} \end{center}

Next, I decided to temporarily remove any observations with NA entries to create a new dataset. Then I used the dataset with no NA entries and estimation-validation split it. Finally, I used the rpart function to quickly create a model using the estimation data and all of its features.  


```
## 
##   0   1   2   3   4 
## 219 118  36  37  12
```

```
##       predicted
## actual v0
##      0 54
##      1 29
##      2  8
##      3  9
##      4  2
```

To check the accuracy of the model, I created an actual versus predicted table using the validation data as the actual values and using the model created by the rpart function to predict the num feature, where v0 means no presence of heart disease and any other entry means some presence of heart disease. I used this table to then calculate the accuracy of the model.


```
##       predicted
## actual  0  1  2  3  4
##      0 44  9  1  0  0
##      1  9 15  0  5  0
##      2  1  4  1  2  0
##      3  3  3  0  3  0
##      4  2  0  0  0  0
```

```
## [1] 0.6176471
```

### Data

This data was taken from the UC Irvine Machine Learning Repository The hospitals that provided the data are Hungarian Institute of Cardiology in Budapest, University Hospital in Zurich, Switzerland, University Hospital in Basel, Switzerland and V.A. Medical Center in Long Beach. It should also be noted that this data was recored in 1988. This data contains 76 features, but all published experiments refer to using a subset of 14 of them.

The 14 features are:

1. age - age in years
1. sex - sex (1 = male; 0 = female)
1. cp -  cp: chest pain type -- Value 1: typical angina -- Value 2: atypical angina -- Value 3: non-anginal pain -- Value 4: asymptomatic
1. trestbps - resting blood pressure (in mm Hg on admission to the hospital)
1. chol - serum cholestoral in mg/dl
1. fbs - (fasting blood sugar > 120 mg/dl) (1 = true; 0 = false)
1. restecg - resting electrocardiographic results -- Value 0: normal -- Value 1: having ST-T wave abnormality (T wave inversions   and/or ST elevation or depression of > 0.05 mV)  -- Value 2: showing probable or definite left ventricular hypertrophy by Estes' criteria
1. thalach - maximum heart rate achieved
1. exang - exercise induced angina (1 = yes; 0 = no)
1. oldpeak - ST depression induced by exercise relative to rest
1. slope - slope: the slope of the peak exercise ST segment -- Value 1: upsloping -- Value 2: flat -- Value 3: downsloping
1. ca - number of major vessels (0-3) colored by flourosopy
1. thal - 3 = normal; 6 = fixed defect; 7 = reversable defect
1. num (the predicted attribute) - diagnosis of heart disease (angiographic disease status) -- Value 0: < 50% diameter narrowing -- Value 1: > 50% diameter narrowing

### Modeling

First, I removed any observations with NA entries from the training dataset, then used those observations to cross validate decision tree and k nearest neighbors models.  



***

## Results

I found that the decision tree model had the highest accuracy, so I chose this as my best model with the highest accuracy being 0.627.


```
## CART 
## 
## 524 samples
##  11 predictor
##   5 classes: '0', '1', '2', '3', '4' 
## 
## No pre-processing
## Resampling: Cross-Validated (5 fold) 
## Summary of sample sizes: 418, 419, 421, 418, 420 
## Resampling results across tuning parameters:
## 
##   cp           Accuracy   Kappa    
##   0.001992032  0.5933771  0.3366004
##   0.002656042  0.5953188  0.3402626
##   0.003984064  0.5934320  0.3358350
##   0.006374502  0.6180685  0.3609617
##   0.007968127  0.6199733  0.3644237
##   0.009960159  0.6277772  0.3714739
##   0.011952191  0.6466452  0.3973181
##   0.027888446  0.6240614  0.3619119
##   0.029880478  0.6050836  0.3208117
##   0.195219124  0.5609433  0.1609110
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was cp = 0.01195219.
```

***

## Discussion

62% accuracy does not mean one should trust this model and depend on it to determine if there's a presence of heart disease. The data being used is from 1988, and the medical world seems to have changed very much since then. Anyways, if this model is used to predict any sort of presence of heart disease, there's a chance that the prediction could a false positive or false negative. If someone uses some of the features and the models predicts they do not have heart disease, that person should still be vigilant and suggest a small possibility that the model is not correct. On the other hand, if someone uses the model and it predicts they do have heart disease, then they should seek a cardiologist and professional assistance rather than depending on a prediction model.

***

## Appendix

* Angiography or arteriography - a medical imaging technique used to visualize the inside, or lumen, of blood vessels and organs of the body, with particular interest in the arteries, veins, and the heart chambers.

* fluoroscopy - type of medical imaging that shows a continuous X-ray image on a monitor, much like an X-ray movie. During a fluoroscopy procedure, an X-ray beam is passed through the body.

* electrocardiography - process of producing an electrocardiogram (ECG or EKG). It is a graph of voltage versus time of the electrical activity of the heart using electrodes placed on the skin.
