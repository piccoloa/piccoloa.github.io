---
permalink: /sentiment_1/
title: "Natural Language Processing: Sentiment Analysis"
excerpt: 'Applying Sentiment analysis to movie review using Kaggle labeled dataset.'
author_profile: false
categories:
- exploring big data
- nlp
- docker
- data science
category: Exploring Big Data
sidebar:
  title: "Big Data"
  nav: Big-Data
layouts_gallery:
  - url: /assets/images/mm-layout-splash.png
    image_path: /assets/images/mm-layout-splash.png
    alt: "splash layout example"
  - url: /assets/images/mm-layout-single-meta.png
    image_path: /assets/images/mm-layout-single-meta.png
    alt: "single layout with comments and related posts"
  - url: /assets/images/mm-layout-archive.png
    image_path: /assets/images/mm-layout-archive.png
    alt: "archive layout example"
last_modified_at: 2019-01-19T12:04:24-04:00
toc: true

---
![jpg](/assets/images/sentimentpost.png)
### **OVERVIEW**

The purpose of this post besides creating and measuring the accuracy of a sentiment model is to practice creating most of necessary steps to implement a working environment within a sharable database environment.  From my experience, many of the cool and useful data mining and analytics I have uncovered are most useful when the results are easily accessed in a database and shared with other systems.  This example uses a Postgres database in a Docker-Compose environment. To create a similar environment clone [this GitHub](https://github.com/piccoloa/priv_jupyter) repo, start docker-compose and follow the notebook links in each part this post. A similar environment was created in the [dedupe using postgres environment](https://github.com/piccoloa/dedupePostgresDocker) that implemented one form of [identity resolution](https://piccoloa.github.io/entityresolution/).  Most of the essential parts of extracting, transforming, and loading (ETL) are performed in Jupyter notebooks that are referenced in this post.

The sentiment model example uses a Kaggle [sample dataset](https://www.kaggle.com/c/sentiment-analysis-on-movie-reviews/data). To follow along my learning path, I gathered the basics of NLP using concepts learned in _Python 3 Text Processing with NLTK 3 Cookbook_ and _pythonprogramming.net_. In practice, I have used these concepts to implement a sentiment system for scoring customer interactions stored in salesforce.com and sharing model results via a Postgres database. Although much of this project could have been done using Pandas, I prefer to use a database in a docker service to make the data persistent and to allow for easily querying the data using [Adminer](https://www.adminer.org/).   


### **Part 1- Write data to Postgres DB** [Go deeper...](https://github.com/piccoloa/piccoloa.github.io/blob/master/assets/notebooks/Part%201-%20Write%20data%20to%20Postgres%20DB.ipynb)

Set up Kaggle dataset to access from a Postgres DB

### **Part 2-Create, Test & Save Sentiment Model** [Go deeper...](https://github.com/piccoloa/piccoloa.github.io/blob/master/assets/notebooks/Part%202-Create%2C%20Test%20%26%20Save%20Sentiment%20Model.ipynb)

Create a sentiment analysis algorithm using labeled Kaggle movie reviews.  Part 1 cleaned and saved reviews to a database. Our model adjusts the Kaggle dataset to comply with a binary classification, in which the target variable only has two classes to be predicted. For this post, the classes are either “negative” or “positive”. Labeled reviews are marked as either a __positive__(> 3) or __negative__(<3) phrases. The Kaggle dataset reviews are labeled as 0 - 4 (neg - pos) based on the provided training set.  
In short, the NLTK library is used to extract POS (parts-of-speech- adjectives) from reviews to use as __features__ for each movie review. These featuresets are then used to train the classification models.  The feature sets associated with the labeled reviews are measured against a bag-of-words to calculate the likelihood of the review being categorized as a positive or negative sentiment.  

Combining the classifier algorithms is a common technique, done by creating a sort of voting system, where each algorithm gets one vote, and the classification that has the most votes is the chosen one.  In NLTK, classifiers are defined using classes that implement the ClassifyI interface. A processing interface for labeling tokens with a single category label (or "class"). Labels are typically strings or integers, but can be any immutable type. The set of labels that the classifier chooses from must be fixed and finite.

### **Part 3 - Process phrases using Sentiment Model** [Go deeper...](https://github.com/piccoloa/piccoloa.github.io/blob/master/assets/notebooks/Part%203-%20Process%20phrases%20using%20Sentiment%20Model.ipynb)

Part 3 is a brief.  The sentiment model saved in part 2 is used to score unclassified phrases. In this case the test phrases provided by Kaggle.    


### **Part 4- Evaluate Model using Kaggle Training Set** [Go deeper...](https://github.com/piccoloa/piccoloa.github.io/blob/master/assets/notebooks/Part%204-%20Evaluate%20Model%20using%20Kaggle%20Training%20Set.ipynb)

The accuracy of our model is the number of times the model correctly predicts the sentiment on new phrases. This is a supervised learning exercise. Given that the Kaggle data has a 0-4 scale, any review labeled 2 would be neither positive nor negative.  For the purpose of our binary classification, the prediction is either positive or negative.  Excluding any neutral reviews, 6,874 labeled phrases are available to measure for our model.  100% of the trained data is used to test the accuracy of the model.  Only the top 5,000 features for all 6,874 phrases were used to train the model. Post the removal of neutral labels, the base positive sentiment prediction is 52.40%. Of course, overfitting our model is a risk given this is supervised learning and we are testing our model against the training set.

### Does the model work?  
Our analysis yielded 68% accuracy on the training set, which is 16.25% more accurate than null accuracy of 52%.  The model has a much higher precision for predicting positive(94%) reviews vs negative(60%) while having a higher F-1 score for negative predictions(75 vs 58). Overall, I am more confident in predicting negative reviews than positive reviews, which I think may reflect the sarcasm that is more likely mis-understood by the analysis.

---

#### **Definitions and Sources**
i. [binary classification](https://en.wikipedia.org/wiki/Binary_classification)  
ii. [confusion matrix](https://en.wikipedia.org/wiki/Confusion_matrix)  
iii. [precision, recall and f measures](https://en.wikipedia.org/wiki/Precision_and_recall)  
iv. [Python 3 Text Processing with NLTK 3 Cookbook](https://www.amazon.com/Python-Text-Processing-NLTK-Cookbook-ebook/dp/B00N2RWMJU/ref=sr_1_2?ie=UTF8&qid=1547992672&sr=8-2&keywords=nltk)  
v.  [Tokenizing Words and Sentences with NLTK](https://pythonprogramming.net/tokenizing-words-sentences-nltk-tutorial/)
