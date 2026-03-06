# Naive-Bayesian-twitter-stance-classification
This project contains a case study focused on comparing the Frequentist Naive Bayes classifier and the Bayesian Naive Bayes classifier. For it, the comparison was made by classifying a series of tweets provided in the dataset `twits.csv` provided by the University Carlos III de Madrid in the Bayesian Data Analysis course. Here, the classification wa smade by analysing the words in the text of the Spanish tweets and determining if they are in favor or against death penalty.

## Project objective
The main goal is to build a text classifier using Bayes' Theorem and compare two distinct approaches:
1. **Frequentist Naive Bayes:** Based purely on empirical word frequencies.
2. **Bayesian Naive Bayes:** Incorporating Laplace smoothing to handle unseen vocabulary.

## Methodology
This project is built in **RStudio** and follows a rigorous data science pipeline:
* **Text Preprocessing:** Creation of a Corpus, removal of punctuation, numbers, and Spanish stopwords using the `tm` package.
* **Exploratory Data Analysis (EDA):** Generation of word clouds to visualize the most prominent terms used by each stance.
* **Cross-Validation:** Implementation of a 5-Fold Cross-Validation strategy to ensure robust evaluation and prevent data leakage. The frequent word dictionary (features) is dynamically generated only from the training folds in each iteration.
* **Modeling:** Training and testing both Frequentist (`laplace = 0`) and Bayesian (`laplace = 1`) classifiers on categorical Document-Term Matrices.

## Conclusion
Both models yielded similar precision scores, with the Frequentist approach performing slightly better in this specific dataset. 

A deeper analysis reveals that the Bayesian approach (Laplace smoothing = 1) introduces **probability dilution**. Because the Spanish vocabulary across these tweets is extensive, adding a pseudo-count of +1 to every word—including low-information terms—adds artificial noise. In this specific scenario, Laplace smoothing slightly distorts the probability weights of highly significant words rather than solely rescuing zero-probability events.

## Repository Contents
* `Twits.csv`: The original dataset containing the tweets and their categories.
* `Naive_Bayesian_data_filtering.Rmd`: The R Markdown file containing the full annotated code and methodology.
* `Naive_Bayesian_data_filtering.html`: The compiled HTML report showing code outputs and visualizations.
