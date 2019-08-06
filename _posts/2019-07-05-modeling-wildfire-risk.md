---
layout: post
title:  "Modeling Wildfire Risk"
date:   2019-07-05
excerpt: "The impact of wildfires continues to escalate as utility companies struggle to manage risk."
image: "http://www.wildfirerisktool.com/images/modeling.jpg"
author: "Nicholas Conidas"
---

_Image credit: []()_

One of the foundational components of our project was a machine learning model that would output the predicted wildfire risk at each S2 cell every day. 

## Framing the Problem
The first step in the model development process was to properly frame the problem. Of key importance, we had binary labels (0 = No Fire, 1 = Fire) for every data point and we knew we wanted our model to output a floating point risk score in the range 0-1. These specifications align nicely with the supervised binary classification framework.

## Challenges
### Class Imbalance
Fortunately, wildfires are relatively rare. This does however, pose a challenge from a modelling perspective in that wildfire datasets suffer from massive class imbalance. The negative class consisted of 99.97% of all data points, which makes it very challenging for a classifier to learn a decision boundary that would outperform always predicting “no fire.” 

|                  | Train             | Validation        |
|                  | (2016-2017)       | (2018)            |
|-----------------:|:-----------------:|:-----------------:|
| Row Count        | 7.7 Million       | 3.9 Million       |
| Fires            | 2431              | 1242              |
| Fire Percent     | 0.03%             | 0.03%             |

### Ignition Randomness
A second challenge we had to deal with was the inherent randomness in wildfires. An area may have a very high risk of fire on a given day, but without an ignition source, a fire will not occur. Furthermore many fires start due to things like lightning strikes and human behavior, both of which are very difficult to accurately predict. 

## Development Process
Due to the previously mentioned challenges we chose a flexible framework that allowed us to rapidly experiment with many techniques. 

The model development process consisted of:
1. Segment the data into separate training and validation sets
2. Train a classifier
3. Conduct hyperparameter tuning
4. Assess feature importance and model performance
5. Repeat steps 2-4 with new models and techniques

By adopting an iterative approach we were able to test a variety of techniques in a short period of time. 

## Baseline Model
The first model we developed was used both to establish a baseline performance as well as gain preliminary insights into dataset feature importance. As interpretability and simplicity were of paramount importance for the baseline we chose logistic regression, implemented using LogisticRegressionClassifier in scikit-learn.

## Technique Experimentation
Due to the heavy class imbalance in our dataset we borrowed a few techniques from outlier detection, including Up/Down Sampling and Isolation Forests. With 11.7 million data points we felt we had a sufficient amount of data to train a neural network. Ultimately we tried a feedforward neural network and a convolutional neural network. For structuring the convolutional neural network we had two options, to convolve about a geospatial dimension or about a temporal dimension. Ultimately, either option would require a substantial restructuring of our data and decided to leave the CNN for future work. The feedforward neural network was more straightforward and could be used with our data as is, this left us free to experiment with layer counts, dimensions and activation functions. 

## Final Model
Ultimately, we chose a model that had the best balance of performance and simplicity and that was a feedforward neural network with minority class upsampling and majority class downsampling. When it came time to optimize our model we chose to focus on recall as high recall would correspond to few wildfires going unpredicted. As can be seen in the table below the final model does substantially improve recall as compared to the baseline. At first glance the precision achieved by the models seems quite low because of the large number of false positives. However, it is important to note that an area might have had a high risk of fire but was simply missing an ignition source. For the reason precision is not a wholly dependable metric, and future work should be done to establish a better system for assessing fire risk models.

|                  | Logistic Regression | Feed Forward Neural Network |
|                  | (Baseline)          | (Final)                     |
|-----------------:|:-------------------:|:---------------------------:|
| Precision        | 0.0007              | .00045                      |
| Recall           | 0.13                | .69                         |


