---
layout: post
title:  "Dynamic Topic Modelling Methodology"
author: JoeL
categories: [Methodologies]
tags: [Natural Language Processing, Text Data, Topic Model, Machine Learning]
image: assets/images/post-imgs/2024-09-16-TopicModelMethod/library_books.jpg
description: "Our methodology for performing topic modelling on text data that grows over time."
hidden: false
---

*Image Source: Image by <a href="https://unsplash.com/@thkelley?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Thomas Kelley</a> on <a href="https://unsplash.com/photos/books-filed-on-bookshelf-hHL08lF7Ikc?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>*
  
### Overview of topic modelling
It is a challenge for humans to read tens of thousands of text documents and track topics and trends throughout. A commonly used data science solution to this challenge is topic modelling. Topic modelling is a technique within Natural Language Processing that focuses on clustering the words used within and between a document corpus. These clusters of words are assumed to then represent topics in the underlying text. Many topic models however do not make considerations for analysing topics in a growing text corpus instead focussing on static datasets. 

In this methodology paper we present our method for performing topic modelling with a temporal element. The temporal focus of our work enables the use of topic models for ongoing analysis of text data that grows over time. We apply our method to email data, with an email corpus of roughly 40,000 emails that grows by 1,000 emails per month. The result of our method is a model that is able to track topics over time and find receding, emerging and stagnant topics in dynamic text.

### Aims
This paper will focus on our modelling strategy we present as dynamic topic modelling. We will:
<br>
•	illustrate the problems we found when using traditional topic models with text data that grows<br>
•	briefly outline the preprocessing steps we apply to text data before using it to inform a model<br>
•	provide our method for performing dynamic topic modelling<br>
•	discuss the results we have found<br>
•	share the limitations and present opportunities for future work

### Traditional topic models
A traditional topic model for example Latent Dirichlet Allocation (LDA) takes a text dataset as input and returns topics within the text as output. This process has a few things to note:
1.	The LDA process is initialised from a random starting point so there is no guarantee (excluding the use of random seeds) that the same topics will be found when performing LDA on the same dataset twice
2.	The randomness also produces a problem in that topics may not be listed in the same order this makes linking topics from two models a challenge.

These problems are further amplified when the underlying text changes, even when these changes are small. For example, consider performing LDA on a corpus of ten thousand documents, then on a corpus of the same ten thousand plus an extra one thousand documents. Even if we use the same random seed and number of topics as hyperparameters the list of topics returned will tend to differ greatly in order and representation of the topics even when semantically the topics are the same.

**Table 1: The problems of traditional topic models**
<img class="featured-image img-fluid" width="80%" height="auto" src="{{ site.basurl }}/assets/images/post-imgs/2024-09-16-TopicModelMethod/tm-problems.PNG" alt="Table showing the mismatch of topics between two time periods.">

The problem therefore is not with the model itself as it is with the representation of topics from the model. In the example above when performing an analysis of these topics it is difficult to see that topics 1 and 3 are likely to be the same topic. Further, when performing topic modelling at scale with 50 topics this difficulty is amplified, making it almost impossible to track topics as emerging, stagnant or receding through time.

We note that approaches to performing topic modelling with temporal data do exist in the literature, Dynamic LDA being one example. However, these approaches focus on static data with a temporal element attached. As an example, Dynamic LDA may use temporal metadata to analyse topics through the past year. This still relies on having all the data when a model is run. The problem we aim to address is performing topic modelling on one dataset how we then track topics as that dataset grows over time. We point those interest in Dynamic LDA to the following paper.

### Preparing text data for topic modelling
We apply standard text cleaning approaches to the text data used by our model:
<br>
•	tokenisation<br>
•	lemmatisation<br>
•	stop word removal

We then apply a Term Frequency-Inverse Document Frequency (TF-IDF) vectorisation to the text data. TF-IDF vectors represent each document as a series of numbers based on the use of the words within and between documents. Words that are used commonly throughout all documents are weighted with low values, as are words that are rarely used. Words that are used commonly in a subset of documents are weighted highly. This is why TF-IDF is commonly used in topic modelling as words commonly used in a subset of documents likely represent topics. 

### Dynamic topic modelling
To present our method we consider a dataset with three time periods, each containing one thousand documents. We begin by building a traditional LDA model with the first time period of data. We then extract the word-topic vector of this model. The word-topic vector is of dimensions: (N. words in corpus, k topics used) and represents how much a given word contributes to a topic. 

We now consider the second time period. We apply a sliding window approach; in the new time period we use both time period one and time period two to inform an LDA model. This sliding window enables us to consider a large volume of text over time, and further grounds the topics in period two with those in period one as they are still in the data. We then apply the word-topic vector extracted above to the second model as word priors. These priors effectively bias the model toward finding the same topic at the same indices. Returning to Figure 1 above, this addresses the data linkage problem and ensures that the period two model considers the outputs of model one. We again extract the word-topic vector from this model.

Finally, we consider period three. In this period, we build a third model informed by period two and period three. We apply the word-topic vector from period two as a word prior to this model. We are now biasing based on a model that was previously biased by period one but was then optimised on data in period one and two. The idea is that these biases fade over time for topics that disappear from the data (recede) allowing others to emerge or they remain for the stagnant topics.

**Figure 1: Overview of dynamic topic model, three time periods**
<img class="featured-image img-fluid" width="75%" height="auto" src="{{ site.basurl }}/assets/images/post-imgs/2024-09-16-TopicModelMethod/tm-process.png" alt="Outline of process described above.">

### Results
For evaluation we have used both an internal sensitive dataset which has not been shared, and a publicly available dataset from [Kaggle](https://www.kaggle.com/datasets/rmisra/news-category-dataset). The table below shows the results of the dynamic process from the Kaggle dataset.

**Table 2: Topic examples from Kaggle data**

<img class="featured-image img-fluid" src="{{ site.basurl }}/assets/images/post-imgs/2024-09-16-TopicModelMethod/topicsOverTime.png" alt="Table showing the mismatch of topics between two time periods.">

The metrics we report are the coherence metric commonly used in topic modelling. The coherence metric reported is the CV Coherence score ranging 0-1. We also report on metrics we have developed and found useful to assess the quality of a topic set found by a model:
<br>
•	Topic Fit: Apply a word embedding to the top k words in a topic and and find the average distances between each of the embedded words. This metric gives an indication of how well the words fit together in a wider context, outside of the corpus. High fit indicates one would expect to see these words together without knowledge of the underlying corpus, low fit indicates either the topic is not meaningful, or that the words are used uniquely in the corpus.<br>

•	Topic Specificity: Measure the topic residual across documents (how spread out a topic is across the documents). Found by subtracting the document-topic weights from the TF-IDF matrix, then finding the normalised dot-product between the resulting matrix and the model topic weights. This vector is then averaged by topic. Low specificity indicates a general topic, high specificity suggests it has been mentioned in a very specific context.<br>



### Limitations and future work
The code used for this project along with the example applied to Kaggle data is available on our GitHub. The limitations of the modelling approach are:
<br>
•	we have not performed benchmarking or analysis of the optimal sliding window size due to the scope of our project having a set sliding window to apply, it is recommended that this window size is optimised based on the number of documents per time period<br>
•	experimentation is required between datasets to find the optimal bias vector size, for example applying the entire word-topic vector as biases ensures every word in the period 1 corpus is accounted for but using just 50 words may be preferred to reduce the bias or increase speed<br>
•	for our problem an LDA model was sufficient and whilst we experimented with Non-Negative Matrix Factorisation (NMF) we applied the LDA model predominantly due to its ability to handle biases<br>
•	applying biases has a risk that words that appear in period 2 were not present in the bias from period 1, in this case these words are placed randomly in space in the second model, so in some cases may not be placed optimally immediately in period 2. This is not a problem when words are not present between period 1 and 2<br>
•	when deploying as a process it is recommended that after many biased time periods a new unbiased model is built for comparison or use.

We have presented our work on dynamic topic models for others to use and extend the method. There is room for further experimentation and exploration with modelling topics through time. Due to the scope of our work for example we were unable to test, more advanced models for labelling topics beyond word lists. It is possible an additional step by a more advanced language model would be able to label the topics and that this could be used in later data linkage and analysis.

