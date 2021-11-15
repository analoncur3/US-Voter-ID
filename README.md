# US-Voter-ID
This repository contains analyses of US Congressional Debates over __voter ID__. 

It is split into 3 files:
- _Descriptive Stats.ipynb_
    * Pre-processing: Remove punctuation, convert to lower case, remove common stop words, lemmatisation
    * Word Frequencies: ngrams 1 and 2
    * Speech count by party and Congress
    * POS
    * Tf-idf scores (overall corpus and split by Party)
    
- _US-Voter-ID_Nov21.md_
    * Keyness Analysis
    * Target Keyword Analysis ("identification")
    * Dataset preparation for similarity network in Iramuteq
 
- _Word embedding.ipynb_
    * Word embedding model for each Party (using the Word2vec implementation provided by the gensim package) 
    * visualisation of overall model and key word "identification"

***
