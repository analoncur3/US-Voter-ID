# US-Voter-ID
This repository contains analyses of US Congressional Debates over __voter ID__. The analyses conducted are inductive and bottom up, meaning that the interest lies in recognising and interpreting patterns emerging from the data. 

It is split into 3 files:
- _Descriptive Stats_
    * Pre-processing: Remove punctuation, convert to lower case, remove common stop words, lemmatisation
    * Word Frequencies: ngrams 1 and 2
    * Speech count by party and Congress
    * POS
    * Tf-idf scores (overall corpus and split by Party)
    
- R file
    * 
    * Filter extremes: remove words that occur in less that n=5 documents or in more than 50% of all documents
    * Join common bigrams
 
- _Analyses_
    * Word embedding model (using the Word2vec implementation provided by the gensim package) and visualisation
    * Co-occurrence network
    * Temporal dimension: dynamic word embedding model, word embedding model per Congress

***
