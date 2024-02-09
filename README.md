# Tinned-Fish-Analysis

## Overview

Inspired by the recent trends on TikTok surrounding tinned and canned fish, we decided to study Amazon reviews of tinned and canned fish products. We sourced these reviews from a [database](https://cseweb.ucsd.edu/~jmcauley/datasets/amazon_v2/) compiled by Jianmo Ni of UCSD of amazon reviews and product descriptions which was last updated in 2018. The purpose of this study was to identify keywords used in the reviews and identify how they contribute to the rating of the review either positively or negatively to identify what customers value in these products. To do this, several different methods were explored and in the end, a Sentiment Analysis approach using a deep learning model was chosen. A reason we chose this method instead of a more established lexicon based Sentiment Analysis method like Vader is because the lexicon based approach requires a predetermined dictionary of words and sentiment weights, the latter of which being what we were aiming to find. Another issue is that after examination of these dictionaries, many of the descriptive words that pertain specifically to food were given neutral sentiment weights which would not reveal anything useful. Using the deep learning model, we aimed to examine how words affected the overall sentiment of each review in context.

## Data Sourcing and Cleaning

The data was sourced from the previously mentioned database which separates the Amazon reviews and product information into separate files which are then also separated by category. The data on tinned and canned fish fell under the 'Grocery and Gourmet Food' category. The [review](https://datarepo.eng.ucsd.edu/mcauley_group/data/amazon_v2/categoryFiles/Grocery_and_Gourmet_Food.json.gz) and [product](https://datarepo.eng.ucsd.edu/mcauley_group/data/amazon_v2/metaFiles2/meta_Grocery_and_Gourmet_Food.json.gz) data is not stored in this repository as the files are too large but can be downloaded for your own examination.

The first step for cleaning the data was removing all products and reviews for products that were not in the sub-category of 'Tinned and Canned Fish' and combining the datasets with only the information needed for the analysis which included the star rating, the date of the review, the text of the review itself and finally which type of fish the product was made with. Then Natural Language Processing techniques were used to clean the review text of errant punctuation and remove stop words which are words which are common in the language but carry much meaning such as 'the', 'in' or 'an.' Then each word was labelled by Part of Speech and all words which were identified to be acting as adjectives or descriptive words were added to the list categorized by what fish the product was made from. The star ratings of the reviews each word were used in and the total number of uses was also counted. For the sake of computational time, it was decided to focus on words which occurred in reviews with average ratings of 2.5 or higher.

## Deep Learning

The deep learning model that we used was the [amazon-review-sentiment-analysis](https://huggingface.co/LiYuan/amazon-review-sentiment-analysis) by Liyuan which can be found on HuggingFace. This model was chosen because it was trained on Amazon review data and it predicts the sentiment of a review as a number of stars between 1 and 5 to replicate the ratings on Amazon products. This means that when a review is put through the model, it returns a confidence score for each star to indicate how strongly it feels that the review was given that number of stars. This is helpful to us as it not only lets us see if a word is used in a positive or negative way but also if it pushes the review towards a specific number of stars.

In order to get the sentiment of specific words we first got the scores for the entire reviews. Using these scores we also calculated a predicted rating of the review with a weighted sum to compare to the actual ratings. Then we iterated through each review again, searching to see if any of the list of descriptive words we found previously were used. If a review was found to contain one of the words, the review was run through the model again but with that word removed and the difference between the confidence scores of the overall review and the review without the word was recorded as its Sentiment Contribution. The Sentiment Contribution is how much a selected keyword added or took away from the confidence scores for a review. Each instance of a word in a review was run separately since we didn't want to record the cumulative change, only the contribution of individual uses, and after this was done for every review and every keyword the values for the Sentiment Contribution were averaged.

## Visualizations

All of the following visualizations come from a Looker Studio Report which can be accessed through this [link](https://lookerstudio.google.com/reporting/79d02342-b0f1-459d-a620-afa5ff19ffd8) for closer examination.

### Deep Learning Accuracy

One of the first things we looked at was the accuracy of the deep learning model's predictions on the overall reviews. Below is a graph of the distribution of the absolute value of the difference between the actual and predicted ratings.

![Prediction Error](https://github.com/wawilson810/Tinned-Fish-Analysis/blob/main/Visualizations/Prediction_Error.png)

The distribution is broken down by the actual rating of each review and from it we can see that 5 star reviews made up the majority of the reviews we looked at and the predictions tended to be 1 off from the actual value. The most accurate predictions were those for 4 and 3 star rated reviews whose predictions tend to only have an error of 1 or less.

### Noticeable Findings
