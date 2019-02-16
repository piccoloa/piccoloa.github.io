---
permalink: /sentiment_1/
title: "Sentiment Analysis"
excerpt: 'Relating memories to reality- using NLP as your crutch.'
author: Alex_Piccolo
header:
  overlay_image: /assets/images/chocolate.jpg
  caption: "A. Piccolo: [Chocolate Guadeloupe](https://www.google.com/maps/place/16%C2%B012'28.4%22N+61%C2%B046'48.4%22W/@16.2079,-61.7822887,17z/data=!3m1!4b1!4m5!3m4!1s0x0:0x0!8m2!3d16.2079!4d-61.7801)"
  actions:
    - label: "More Info"
      url: "https://piccoloa.github.io/sentiment_1/"
categories:
- nlp
- docker
- data science
- NLTK
- Postgres
sidebar:
  title: "Big Data"
  nav: Big-Data
last_modified_at: 2019-02-19T12:04:24-04:00
toc: true

---
![jpg](/assets/images/sentimentpost.png)
### **Sentiment Analysis**
#### Using NLP to more efficently analyze unstructured text.

With the likes of Alexa and Siri hitting mainstream adoption, Natural language processing(NLP) has gained much relevance to the average person. That being said, much of the value in unstructured data that is collected, stored and shared in traditional sytems is of little value to most within the organization outside of the original content creator and his or her circle of influence. This is where NLP can potentially shine by using symbols in content as a crutch to relate memories of the the original content creator's intentions.  
In this NLP example, an attempt is made to use sentiment analysis similar to our own recording of episodic memories to consciously recollect memories from prior experienced events to categorize future occurrences. Using parts-of-speech, sentiment analysis uses supervised learning to categorize prior topics to predict new outcomes. Did the person writing the movie review like or not like the movie? Applying prior labeled reviews of movies to predict the sentiment of new movie reviews is just one example of where the concept can be applied. A similar use case may include predicting a customer's future intentions based on learning from prior labeled interactions with like minded customers.

The purpose of this post besides creating and measuring the accuracy of a sentiment model is to practice creating most of the necessary steps to implement a working production environment. In short, the model uses parts-of-speach from prior reviews to score or predict new or un-labeled reviews.  

In practice most useful data mining and analytics are not really helpful to those using the results until they are easily accessed in a database and easily shared with other systems. In this example, I use a Postgres database in a Docker-Compose environment.
To create a similar environment clone [this GitHub](https://github.com/piccoloa/priv_jupyter) repo, start docker-compose and follow the notebook links in each part of this post. A similar environment was created in the [dedupe using postgres environment](https://github.com/piccoloa/dedupePostgresDocker) that implemented one form of [identity resolution](https://piccoloa.github.io/entityresolution/).  Most of the essential parts of extracting, transforming, and loading (ETL) are performed in Jupyter notebooks that are referenced in this post.  In practice, a seperate Docker service is implemented to perform the ETL done in the notebooks.

### Explanation of the Code  
The sentiment model example uses a Kaggle [dataset](https://www.kaggle.com/c/sentiment-analysis-on-movie-reviews/data). Much of the concepts here were learned by following _Python 3 Text Processing with NLTK 3 Cookbook_ and _pythonprogramming.net_. In practice, a similar approach was used for scoring customer interactions recorded as notes in salesforce.com. Although much of this project could have been done using Pandas, I prefer to use a database in a docker service to make the data persistent and to allow for easily querying the data and sharing with other front-end systems. [Adminer](https://www.adminer.org/).   


### **Part 1- Write data to Postgres DB**   
Set up Kaggle dataset to access from a Postgres DB

1. import csv files downloaded from kaggle
2. load data to a Postgres db in docker container
3. clean train and test tables to adapt Kaggle dataset to reflect more likely original data    
[refer to notebook](https://github.com/piccoloa/priv_jupyter/blob/master/notebooks/Part%201-%20Write%20data%20to%20Postgres%20DB.ipynb)



### **Part 2-Create, Test & Save Sentiment Model**  
1. Create two tables, one for negative reviews and one for positive reviews.
2. Use NLTK to tokenize phrases
3. Create featuresets
4. Test Scikit-learn classificaion models   
[refer to notebook](https://github.com/piccoloa/priv_jupyter/blob/master/notebooks/Part%202-Create%2C%20Test%20%26%20Save%20Sentiment%20Model.ipynb)

Create a sentiment analysis algorithm using labeled Kaggle movie reviews.  Part 1 cleaned and saved reviews to a database. Our model adjusts the Kaggle dataset to comply with a binary classification, in which the target variable only has two classes to be predicted. For this post, the classes are either “negative” or “positive”. Labeled reviews are marked as either a __positive__(> 3) or __negative__(<3) phrases. The Kaggle dataset reviews are labeled as 0 - 4 (neg - pos) based on the provided training set.  
In short, the NLTK library is used to extract POS (parts-of-speech- adjectives) from reviews to use as __features__ for each movie review. These featuresets are then used to train the classification models.  The feature sets associated with the labeled reviews are measured against a bag-of-words to calculate the likelihood of the review being categorized as a positive or negative sentiment.  

A custom classifier is used to score reviews.  The classifier includes combining the classifier algorithms and creating a sort of voting system, where each algorithm gets one vote, and the classification that has the most votes is the chosen one.  In NLTK, classifiers are defined using classes that implement the ClassifyI interface. A processing interface for labeling tokens with a single category label (or "class"). Labels are typically strings or integers, but can be any immutable type. The set of labels that the classifier chooses from must be fixed and finite.

### **Part 3 - Process phrases using Sentiment Model**   

1. Create a processed phrase table in Postgres DB.  
2. Incrementally process each phrase using saved sentiment module.    
[refer to notebook](https://github.com/piccoloa/priv_jupyter/blob/master/notebooks/Part%203-%20Process%20phrases%20using%20Sentiment%20Model.ipynb).

The sentiment model saved in part 2 is used to score unclassified phrases. In this case the test phrases provided by Kaggle.    




### **Part 4- Evaluate Model using Kaggle Training Set**  
1. Create a processed phrase table for the training/test set in Postgres DB.
2. Evaluate the model using Sklearn metrics classification and confusion matrix.  
[refer to notebook](https://github.com/piccoloa/priv_jupyter/blob/master/notebooks/Part%204-%20Evaluate%20Model%20using%20Kaggle%20Training%20Set.ipynb)

The accuracy of the model is the number of times the model correctly predicts the sentiment on new phrases. This is a supervised learning exercise. Given that the Kaggle data has a 0-4 scale, any review labeled 2 would be neither positive nor negative.  For the purpose of the binary classification, the prediction is either positive or negative.  Excluding any neutral reviews, 6,874 labeled phrases are available to measure for our model.  100% of the trained data is used to test the accuracy of the model.  Only the top 5,000 features for all 6,874 phrases were used to train the model. Post the removal of neutral labels, the base positive sentiment prediction is 52.40%. Of course, overfitting our model is a risk given this is supervised learning and we are testing our model against the training set.

### Does the model work?  
Our analysis yielded 68% accuracy on the training set, which is 16.25% more accurate than null accuracy of 52%.  The model has a much higher precision for predicting positive(94%) reviews vs negative(60%) while having a higher F-1 score for negative predictions(75 vs 58). Overall, I am more confident in predicting negative reviews than positive reviews, which I think may reflect the sarcasm that is more likely mis-understood by the analysis.

---

#### **Definitions and Sources**
i. [binary classification](https://en.wikipedia.org/wiki/Binary_classification)  
ii. [confusion matrix](https://en.wikipedia.org/wiki/Confusion_matrix)  
iii. [precision, recall and f measures](https://en.wikipedia.org/wiki/Precision_and_recall)  
iv. [Python 3 Text Processing with NLTK 3 Cookbook](https://www.amazon.com/Python-Text-Processing-NLTK-Cookbook-ebook/dp/B00N2RWMJU/ref=sr_1_2?ie=UTF8&qid=1547992672&sr=8-2&keywords=nltk)  
v.  [Tokenizing Words and Sentences with NLTK](https://pythonprogramming.net/tokenizing-words-sentences-nltk-tutorial/)
