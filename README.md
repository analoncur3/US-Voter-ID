# US-Voter-ID
This repository contains analyses of US Congressional Debates over __voter ID__. The analyses conducted are inductive and bottom up, meaning that the interest lies in recognising and interpreting patterns emerging from the data. 

Specifically these analyses aim to identify:
1. the topics voter ID is linked to.
2. the ways in which voter ID is framed by Democratic and Republican speakers.
3. how frames/topics around voter ID change over time.

It is split into the sections below:
- _Preprocessing_
    * Clean up: remove punctuation, convert to lower case
    * Remove common stop words
    * lemmatisation
    
- _Descriptive Statistics_
    * Most common words and wordcloud
    * Most common n-grams 
    * Number of speeches per Party and Congress

- Reducing corpus size and noise
    * remove stopwords using TF-IFG scores: to remove words that are not meaningful
    * Filter extremes: remove words that occur in less that n=5 documents or in more than 50% of all documents
    * Join common bigrams
 
- _Analyses_
    * Word embedding model (using the Word2vec implementation provided by the gensim package) and visualisation
    * Co-occurrence network
    * Temporal dimension: dynamic word embedding model, word embedding model per Congress

***
