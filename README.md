# Tinned-Fish-Analysis

## Overview

Inspired by the recent [trends on TikTok surrounding tinned and canned fish](https://time.com/6250195/tinned-fish-tiktok-shortage/), I decided to study Amazon reviews of tinned and canned fish products. I sourced these reviews from a [database](https://cseweb.ucsd.edu/~jmcauley/datasets/amazon_v2/) compiled by Jianmo Ni of UCSD of Amazon reviews and product descriptions (last updated in 2018). 

The purpose of this study was to identify how specific keywords contribute to the rating of the review either positively or negatively. This could help manufacturers gain insight into what customers value in these products. 

After several iterations I chose to use a deep learning model to conduct Sentiment Analysis. Using a deep learning model proved more appropriate than a more traditional lexicon based approach to Sentiment Analysis for two key reasons:

  1. The lexicon based approach requires a predetermined library of words and their sentiment weights, while I was looking to find the relative sentiment weight within a specific review.
     
  2. Within these dictionaries, descriptive words related to food were given neutral sentiment weights making them useless for this project.

The deep learning model was chosen in order to get around these limitations by having the model find an equivalent to the sentiment weights of the lexicon based approach which takes into account how words are used in each review.

## Data Sourcing and Cleaning

The data was sourced from the previously mentioned database which separates the Amazon reviews and product information into separate files which are then also separated by category. The data on tinned and canned fish fell under the 'Grocery and Gourmet Food' category. The [review](https://datarepo.eng.ucsd.edu/mcauley_group/data/amazon_v2/categoryFiles/Grocery_and_Gourmet_Food.json.gz) and [product](https://datarepo.eng.ucsd.edu/mcauley_group/data/amazon_v2/metaFiles2/meta_Grocery_and_Gourmet_Food.json.gz) data are not stored in this repository as the files are too large, but can be downloaded for your own examination.

The steps for cleaning the data were:

  1. Removing all products and reviews that were not in the  sub-category of 'Tinned and Canned Fish', then filtering the datasets for star rating, date of the review, the review copy, and the type of fish.
  2. Several Natural Language Processing (NLP) techniques were used to process the reviews in preparation for the model.
     - Errant punctuation was removed to standardize the text.
     - Stop words which are words such as  'the', 'in' or 'an' which occur frequently but do not carry much meaning were removed.
  3. Once the reviews were processed, every word in the text was labeled according to its Part of Speech and all words labeled as ‘adjectives’ were added to a list of keywords.
     - Generic adjectives such as ‘good’ or ‘bad’ were filtered out of the list of keywords.
  4. After looking at the total number of reviews for each type of fish, it was decided to focus only on Tuna, Sardines, Anchovies and Salmon because reviews of products made from other fish made up a much smaller portion of the data.


## Deep Learning

The deep learning model that we used was the [amazon-review-sentiment-analysis](https://huggingface.co/LiYuan/amazon-review-sentiment-analysis) by LiYuan which can be found on HuggingFace. This model was chosen because it was trained on Amazon review data and it predicts the sentiment of a review as a number of stars between 1 and 5 to replicate the ratings on Amazon products. This means that when a review is put through the model, it returns a confidence score for each star to indicate how likely it has determined that the review was given that corresponding number of stars. With this model I could determine the sentiment of a keyword within a review along with a corresponding predicted star rating for the entire review.

The first step in the process was finding the confidence scores for the full reviews which would act as a baseline which can be used to measure the effect of each individual keyword in the review. Using these scores we also calculated a predicted rating of the review with a weighted sum to compare to the actual ratings to examine the accuracy of the model.


To determine how each keyword affected the reviews it occurred in, I first iterated through each review again and searched for all of the previously identified keywords in the review. If a review was found to contain one of the keywords, the review was run through the model again but with that word removed. The difference between the confidence scores of the original review and the review without the keyword was recorded as its Sentiment Contribution. The Sentiment Contribution is what I used as the equivalent of the sentiment weights I mentioned earlier when referring to the lexicon based approach for Sentiment Analysis. It is a measure of how much each keyword steers the model’s prediction for the review’s star rating towards or from each number of stars.

Each instance of a keyword in a review was run through the model separately to avoid recording cumulative change. After this process was repeated for each keyword across every instance it appears in the reviews, the values for the Sentiment Contribution were averaged for each word across all reviews.


## Visualizations

All of the following visualizations come from a Looker Studio Report which can be accessed through this [link](https://lookerstudio.google.com/reporting/79d02342-b0f1-459d-a620-afa5ff19ffd8) for closer examination.

### Deep Learning Accuracy

One of the first things we looked at was the accuracy of the deep learning model's predictions on the overall reviews. Below is a graph of the distribution of the absolute value of the difference between the actual and predicted ratings.

![Prediction Error](https://github.com/wawilson810/Tinned-Fish-Analysis/blob/main/Visualizations/Prediction_Error.png)

The distribution is broken down by the actual rating of each review and from it I can see that 5 star reviews were most frequent and the star rating predictions tended to be off by +/- 1 star from the actual value. . The most accurate predictions were those for 4 and 3 star rated reviews whose predictions tend to only have an error of 1 or less.

### Noticeable Findings

For the actual visualizations, I decided to focus on the four fish with the largest number of reviews which are Tuna, Sardines, Salmon and Anchovies. One of the main things I looked at was how the Sentiment Contribution for each word compared between these different fish to see if there were any noticeable differences in how the words were used in those reviews. Here are some significant examples.

#### Omega

![Omega](https://github.com/wawilson810/Tinned-Fish-Analysis/blob/main/Visualizations/omega.png)

One of the words I was interested in the most was 'omega' since the amount of omega fatty acids in fish, specifically species often found in tinned fish, is sought after for health benefits. This graph shows the Average Sentiment Contribution for the four fish we focused on; Sardines, Salmon, Tuna and Anchovies. From this graph we see that for most of the species, this keyword contributes fairly neutrally to the confidence score of each star rating, with the exception of anchovies where it has a higher contribution to 4 and 5 star reviews. This indicates that this is an important factor when people consider different tinned anchovy products.

#### Wild

![Wild](https://github.com/wawilson810/Tinned-Fish-Analysis/blob/main/Visualizations/wild.png)

This is interesting because it shows a negative association with the word ‘wild’ in tuna as opposed to the other fish, showing it contributing to 1 and 2 star reviews and detracting from 4 and 5 star reviews. The exception to this is with Salmon which indicates a high favorability for wild caught salmon and sardines as opposed to captively ranched tuna which could stem from concerns over population depletion.

#### Local

![Local](https://github.com/wawilson810/Tinned-Fish-Analysis/blob/main/Visualizations/local.png)

Continuing with the focus on where the fish were sourced, let us look at the word ‘local.’ Here we can see that the word local contributes positively to higher ratings for these fish except for tuna and salmon at the 5 star rating. This could indicate a bias towards imported products or products from specific regions for these two larger, oftentimes more expensive species of fish.
