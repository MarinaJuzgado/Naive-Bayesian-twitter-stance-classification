Naive Bayesian data filtering
================
Marina Juzgado
2026-03-06

## Introduction and goal of the case study

In this case study, it will be showed how to apply Bayes Theorem for
data filtering in order to design a detector of opinion (favourable or
against death penalty) in different twits provided in the `twits.csv`
dataset. The dataset is provided by the University Carlos III in the
Bayesian Data Analysis course, the language of said dataset is in
Spanish, working with outputs and twits in this language.

Two different points of view will be compared in the case study: 1.
**Frequentist Naive Bayes Classifier**: Based in the empiric frequence
of the words 2. **Bayesian Naive Bayes Classifier**: With a Laplacian
smoothing to avoid the problem of null probabilities when a word does
not appear in a class during training

## Data loading and preprocessing

``` r
# Load the data
twits = read.csv("Twits.csv", header = TRUE, sep = ";")
twits$Text <- iconv(twits$Text, to = "UTF-8", sub = "byte")

# Transform objective variable as factor
twits$Category = as.factor(twits$Category)

table(twits$Category)
```

    ## 
    ## AGAINST   FAVOR 
    ##     346     384

## Prepare and clean the Corpus

A *corpus* is a collection of documents. We will use `tm` package to
process the texts, convert everything to lowercase and remove numbers,
punctuation and Spanish common words without analytical meaning

``` r
library(tm)
```

    ## Warning: package 'tm' was built under R version 4.4.3

    ## Cargando paquete requerido: NLP

    ## Warning: package 'NLP' was built under R version 4.4.2

``` r
corpus = Corpus(VectorSource(twits$Text))
inspect(corpus[1:3])
```

    ## <<SimpleCorpus>>
    ## Metadata:  corpus specific: 1, document level (indexed): 0
    ## Content:  documents: 3
    ## 
    ## [1] Mientras sigamos con la doctrina Zaffaroni y el ejecutivo no tenga huevos para promover pena de muerte y tolerancia 0 seguir<85>
    ## [2] <93>11 Legisladores han dado luz verde para llevar al Pleno de propuesta de pena de muerte, eso ya es n<fa>mero alto<85>        
    ## [3] <bf><bf><bf><bf>La gente que pide la reinstauraci<f3>n de la pena de muerte tiene alguna noci<f3>n sobre derechos humanos?????

``` r
clean_corpus = tm_map(corpus, content_transformer(tolower))
```

    ## Warning in tm_map.SimpleCorpus(corpus, content_transformer(tolower)):
    ## transformation drops documents

``` r
clean_corpus = tm_map(clean_corpus, removeNumbers)
```

    ## Warning in tm_map.SimpleCorpus(clean_corpus, removeNumbers): transformation
    ## drops documents

``` r
clean_corpus = tm_map(clean_corpus, removePunctuation)
```

    ## Warning in tm_map.SimpleCorpus(clean_corpus, removePunctuation): transformation
    ## drops documents

``` r
clean_corpus = tm_map(clean_corpus, removeWords, stopwords("es"))
```

    ## Warning in tm_map.SimpleCorpus(clean_corpus, removeWords, stopwords("es")):
    ## transformation drops documents

``` r
clean_corpus = tm_map(clean_corpus, stripWhitespace)
```

    ## Warning in tm_map.SimpleCorpus(clean_corpus, stripWhitespace): transformation
    ## drops documents

``` r
inspect(clean_corpus[1])
```

    ## <<SimpleCorpus>>
    ## Metadata:  corpus specific: 1, document level (indexed): 0
    ## Content:  documents: 1
    ## 
    ## [1] mientras sigamos doctrina zaffaroni ejecutivo huevos promover pena muerte tolerancia seguir

## Visualize the Word Clouds

The best way to understand the differences between stances are the word
clouds. They highlight the most frequent terms used by each group.

``` r
library(wordcloud)
```

    ## Warning: package 'wordcloud' was built under R version 4.4.3

    ## Cargando paquete requerido: RColorBrewer

``` r
favor_indices = which(twits$Category == "FAVOR")
against_indices = which(twits$Category == "AGAINST")
```

``` r
par(mfrow=c(1,2))

wordcloud(clean_corpus[favor_indices], min.freq=3, scale=c(3,.5), 
          colors=brewer.pal(8, "Dark2"), main="In Favor")
```

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## santrichtrinidadteespera could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## narcotraficantes could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## rodriguez could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## secuestradores could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## chffer could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## desgraciados could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## permanente could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## voy could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## corruptos could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## mdaltiples could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## peded could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## arruinan could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## necesitamos could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## perpetua could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## tolerancia could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## quiera could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## legalizar could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## asesinos could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## ejecutivo could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## asesinan could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## cadena could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## podreda could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## sacerdote could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## mejor could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## poner could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## violadores could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## buenmartes could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## pide could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## matar could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## vida could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## promover could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## muerte could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## revisable could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[favor_indices], min.freq = 3, scale = c(3, :
    ## implementar could not be fit on page. It will not be plotted.

``` r
wordcloud(clean_corpus[against_indices], min.freq=3, scale=c(3,.5), 
          colors=brewer.pal(8, "Set1"), main="Against")
```

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : pena could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : muerte could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : explotadores could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : extraordinari could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : dejame could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : permanente could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : asesinan could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : chocobares could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : aprueba could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : libertad could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : golpistas could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : eutanasia could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : existido could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : mes could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : hecho could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : ojale could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : descender could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : derecho could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : bombardeado could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : policedas could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : tambien could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : importancia could not be fit on page. It will not be plotted.

![](Naive_Bayesian_data_filtering_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : demostrf could not be fit on page. It will not be plotted.

    ## Warning in wordcloud(clean_corpus[against_indices], min.freq = 3, scale = c(3,
    ## : estafa could not be fit on page. It will not be plotted.

``` r
par(mfrow=c(1,1))
```

We see that the most used words are “pena” and “muerte”. This is normal
as it is precisely the topic we are analysing, the death penalty. There
are other words we can see slightly bigger than other in both classes,
all related to the theme but that can imply connotations that move more
towards one idea or another.

## Model preparation

Now, we need to divide the data into train and test. To divide the data,
we will apply Cross Validation. This is a simple and more effective way
to divide a dataset than a simple 75/25 partition to ensure results do
not depend on the way the dataset is split. We will use a 5-fold Cross
Validation to ensure robust results. We will extract the most frequent
words from the training folds in each iteration.

``` r
library(caret)
```

    ## Warning: package 'caret' was built under R version 4.4.2

    ## Cargando paquete requerido: ggplot2

    ## Warning: package 'ggplot2' was built under R version 4.4.2

    ## 
    ## Adjuntando el paquete: 'ggplot2'

    ## The following object is masked from 'package:NLP':
    ## 
    ##     annotate

    ## Cargando paquete requerido: lattice

``` r
library(e1071)
set.seed(123)
```

``` r
folds = createFolds(twits$Category, k = 5, returnTrain = TRUE)

# Vectors to store accuracies
acc_freq = c()
acc_bayes = c()
```

## Modeling frequentist vs bayesian approach

In the following function we will split, train and test for every of the
five folds we have previously defined. First we need to divide them into
train and test and create a dictionary to store the training data with
the corpus to store just the words that are significant enough to let
the model take a decision to classify.

Then it is important to transform the numeric counts into cathegorical
ones, this is, instead of counting every time a word appears, just
defining it as “Yes” or “No” appeared. This is because Naive Bayes
assumes conditional independencies between features and using numbers
introduces magnitude differences that could distort proability
estimates.

Finally, at the end of the loop we are able to train and test the data
for each fold in the Frequentist and Bayesian approach. - The
Frequentist doe snot have the Laplacian correction, this means that if a
word does not appear in the training, its probability is 0 so all the
prediction could be null. - On the other hand, in the Bayesian approach
the Laplacian correction is used, this means that we would sum 1 to
every counter and avoids probability 0.

``` r
for (i in 1:length(folds)) {
  
  # 1. Divide indexes into training and test
  train_idx = folds[[i]]
  test_idx = setdiff(1:nrow(twits), train_idx)
  
  # 2. Create a dictionary with the training data
  # Create matrix of the corpus
  dtm_train_complete = DocumentTermMatrix(clean_corpus[train_idx])
  # Take only the words that appear at least 5 times (the rest are not relevant enough to be taken into account)
  freq_words = findFreqTerms(dtm_train_complete, lowfreq = 5)
  
  # 3. Filter matrixes using those words
  dtm_train_filtered = DocumentTermMatrix(clean_corpus[train_idx],control = list(dictionary = freq_words))
  dtm_test_filtered = DocumentTermMatrix(clean_corpus[test_idx],control = list(dictionary = freq_words))
  
  # 4. Transform numeric counts into Yes/No as Naive Bayes works better with cathegorical variables
  train_matrix = dtm_train_filtered
  train_yesno = apply(train_matrix, 2, function(x) {
    factor(x > 0, levels = c(FALSE, TRUE), labels = c("No", "Yes"))})
  
  test_matrix = dtm_test_filtered
  test_yesno = apply(test_matrix, 2, function(x) {
    factor(x > 0, levels = c(FALSE, TRUE), labels = c("No", "Yes"))})
  
  # Extract real tags
  tag_train = twits$Category[train_idx]
  tag_test  = twits$Category[test_idx]
  
  # 5. TRAIN FREQUENTIST MODEL
  model_freq = naiveBayes(train_yesno, tag_train, laplace = 0)
  predictions_freq= predict(model_freq, newdata = test_yesno)
  # Compute the precision (% twits correctly classified)
  precision_freq = mean(predictions_freq == tag_test)
  acc_freq = c(acc_freq, precision_freq)
  
  # 6. TRAIN BAYESIAN MODEL
  model_bayes = naiveBayes(train_yesno, tag_train, laplace = 1)
  predictions_bayes = predict(model_bayes, newdata = test_yesno)
  # Compute the precision (% twits correctly classified)
  precision_bayes = mean(predictions_bayes == tag_test)
  acc_bayes = c(acc_bayes, precision_bayes)
  
  cat("Fold", i, "-> Frequentist Precision:", round(precision_freq, 4),
      "| Bayesian Precision:", round(precision_bayes, 4), "\n")
}
```

    ## Fold 1 -> Frequentist Precision: 0.8151 | Bayesian Precision: 0.7877 
    ## Fold 2 -> Frequentist Precision: 0.8356 | Bayesian Precision: 0.8288 
    ## Fold 3 -> Frequentist Precision: 0.8231 | Bayesian Precision: 0.8231 
    ## Fold 4 -> Frequentist Precision: 0.8345 | Bayesian Precision: 0.8345 
    ## Fold 5 -> Frequentist Precision: 0.8082 | Bayesian Precision: 0.8151

``` r
cat("\n FINAL RESULTS \n")
```

    ## 
    ##  FINAL RESULTS

``` r
cat("Mean Frequentist Precision:", round(mean(acc_freq), 4), "\n")
```

    ## Mean Frequentist Precision: 0.8233

``` r
cat("Mean Bayesian Precision:   ", round(mean(acc_bayes), 4), "\n")
```

    ## Mean Bayesian Precision:    0.8178

## Final conclusion

With both tests made both for Frequentist and Bayesian, we are able to
see the precision results for each approach. Whereas both probabilities
are similar changing only by 0.0055, the Frequentist approach has
slightly more precision. This could be explained because with the +1
that the Laplace correction provides is also sum in words that sometimes
do not appear in a category, there are many words in the vocabulary and
even low informative words receive that +1 and change slightly the
precision of the model. In other words, in this case Laplace is
distorting probabilities rather than helping despite the difference
between approaches is not significant.
