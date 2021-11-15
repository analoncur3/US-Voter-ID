# US-Voter-ID
This repository contains analyses of US Congressional Debates over __voter ID__. The analyses conducted are inductive and bottom up, meaning that the interest lies in recognising and interpreting patterns emerging from the data. 

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
    * Word embedding model (using the Word2vec implementation provided by the gensim package) and visualisation
    * Co-occurrence network
    * Temporal dimension: dynamic word embedding model, word embedding model per Congress

***
