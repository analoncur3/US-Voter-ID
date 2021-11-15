US Voter ID\_Nov21
================

This is an R Markdown document with the following analyses:

-   Descriptive Statistics: top words/features
-   Keyness Analysis: to identify which words are more frequently used
    by Republicans compared to Democrats.
-   Target Keyword Analyses: to identify key words used when discussing
    voter ID specifically by both parties.
-   Create a corpus of ID context: to use in Iramuteq in order to
    visualise word similarities

First we install the relevant libraries.

``` r
library(readtext)
library(ggplot2)
library(quanteda)
require(quanteda)
require(quanteda.textmodels)
require(quanteda.textstats)
require(quanteda.textplots)
```

Then we load the data. We are loading data that has been pre-processed
and lemmatised in Python. Words with a frequency less than 3 were also
removed from the corpus.

``` r
path_data <- system.file("C:/Users/analo/OneDrive - University of Glasgow/University of Glasgow/Thesis/CHAPTERS/DATA/Voter ID laws debates (US)/R Markdown", package = "readtext")

lemma <- readtext(paste0(path_data,"lemma.xlsx"), text_field = "freq")
```

Then we create the corpus and divide this in two for Democratic and
Republican statements

``` r
corpus <- corpus(lemma)
corp_dem <- corpus_subset(corpus, Party %in% c('Democratic'))
corp_rep<- corpus_subset(corpus, Party %in% c('Republican'))
```

We create a tokens object. tokens() segments texts in a corpus into
tokens (words or sentences) by word boundaries. By default, tokens()
only removes separators (typically whitespaces).

``` r
tokens <- tokens(corpus)%>%
  tokens_compound(pattern = phrase(c('shelby county', 'north carolina', 'john lewis')))%>%
  tokens_select(pattern = c("madam","speaker", "mr"), selection = "remove")%>%
  tokens_replace(c("id"), c("identification"))%>%
  tokens_replace(c("vra"), c("voting_right_act"))%>%
  tokens_group(groups = Party) %>%
  tokens()
```

Then we create a dataframe

``` r
dfm <- dfm(tokens)
```

#### Top words

We plot top features:

``` r
freq <- textstat_frequency(dfm, n = 20)
ggplot(data = freq, aes(x = nrow(freq):1, y = frequency)) +
geom_point() +
facet_wrap(~ group, scales = "free") +
coord_flip() +
scale_x_continuous(breaks = nrow(freq):1,
labels = freq$feature) +
labs(x = NULL, y = "word frequency")
```

<img src="US-Voter-ID_Nov21_files/figure-gfm/unnamed-chunk-6-1.png" style="display: block; margin: auto;" />

``` r
ggsave(
  "wordfreq.jpg",
  plot = last_plot(),
  scale = 1,
  path = "C:/Users/analo/OneDrive - University of Glasgow/University of Glasgow/Thesis/CHAPTERS/DATA/Voter ID laws debates (US)/",
  width = 10,
  height = 8,
  dpi = 1000
)
```

#### Keyness analysis

A keyness analysis is first conducted to identify which words are more
frequently used by Republicans when discussing voter ID compared to
Democrats. Keyness is a statistical index used to evaluate how
significant a word is to a document. The statistical significance of the
frequency difference is reported through Chi-Squared values. Positive
values mean that the keyword appears more often than would be expected
by chance in comparison with the reference corpus (i.e. Democratic
speeches). Likewise, a word which is negatively key occurs less often
than would be expected.

``` r
# Calculate keyness 
result_keyness <- textstat_keyness(dfm, target = 1L, measure = c("chi2"),sort = TRUE)
```

``` r
# Plot estimated word keyness
textplot_keyness(result_keyness, labelsize = 4,labelcolor = "black", color = c("blue", "red"), min_count = 10, n = 25)
```

<img src="US-Voter-ID_Nov21_files/figure-gfm/unnamed-chunk-9-1.png" style="display: block; margin: auto;" />

``` r
library(ggplot2)
ggsave(
  "keyness.jpg",
  plot = last_plot(),
  scale = 1,
  path = "C:/Users/analo/OneDrive - University of Glasgow/University of Glasgow/Thesis/CHAPTERS/DATA/Voter ID laws debates (US)/",
  width = 12,
  height = 10,
  dpi = 1000
)
```

#### Target Keyword Analysis

To get a sense of how voter ID is specifically framed by both parties, I
identified words connected to the key term “identification” based on
their distance in the documents, by comparing frequency of words inside
and outside of their contexts (20 words displayed around the target
keyword, i.e. identification). Below are the top keywords for
Republicans and Democrats. Contrasting the previous keyness analysis,
this analysis does not compare words used by Republicans vs Democrats,
but compares words used when discussing voter ID (nearby words), with
words outside of this contextual window.

``` r
id <- c("identification") 
```

We create a tokens object for Republican and Democratic speaches

``` r
## Republicans
tokens_rep <- tokens(corp_rep)%>%
  tokens_compound(pattern = phrase(c('shelby county', 'north carolina', 'john lewis', "voting right act")))%>%
  tokens_select(pattern = c("madam","speaker", "mr"), selection = "remove")%>%
  tokens_replace(c("id"), c("identification"))%>%
  tokens_replace(c("vra"), c("voting_right_act"))%>%
  tokens()

## Democrats
tokens_dem <- tokens(corp_dem)%>%
  tokens_compound(pattern = phrase(c('shelby county', 'north carolina', 'john lewis')))%>%
  tokens_select(pattern = c("madam","speaker", "mr"), selection = "remove")%>%
  tokens_replace(c("id"), c("identification"))%>%
  tokens_replace(c("vra"), c("voting_right_act"))%>%
  tokens()
```

We set a window surrounding our key term and compute words’ association
with the keyword using textstat\_keyness().

``` r
## Republicans
toks_inside_rep <- tokens_keep(tokens_rep, pattern = id, window = 20)
toks_inside1_rep <- tokens_remove(tokens_rep, pattern = id) # remove the keywords
toks_outside_rep <- tokens_remove(tokens_rep, pattern = id, window = 20)

dfmat_inside_rep<- dfm(toks_inside_rep)
dfmat_outside_rep <- dfm(toks_outside_rep)

## Democrats
toks_inside_dem <- tokens_keep(tokens_dem, pattern = id, window = 20)
toks_inside1_dem <- tokens_remove(tokens_dem, pattern = id) # remove the keywords
toks_outside_dem <- tokens_remove(tokens_dem, pattern = id, window = 20)

dfmat_inside_dem<- dfm(toks_inside_dem)
dfmat_outside_dem <- dfm(toks_outside_dem)
```

Calculate keyness:

``` r
# Republicans
tstat_key_inside_rep <- textstat_keyness(rbind(dfmat_inside_rep, dfmat_outside_rep),target = seq_len(ndoc(dfmat_inside_rep)))

# Democrats
tstat_key_inside_dem <- textstat_keyness(rbind(dfmat_inside_dem, dfmat_outside_dem),target = seq_len(ndoc(dfmat_inside_dem)))
```

Then we plot using the ggplot library:

``` r
library(dplyr)
library(scales)
library(ggplot2)
library(gapminder)
```

``` r
target_rep <- tstat_key_inside_rep %>% slice_max(order_by = chi2, n = 20)
ggplot(data = target_rep, mapping = aes(x = reorder(feature, chi2), chi2)) + 
  geom_bar(stat = "identity", fill="red") + coord_flip() + labs(x= "keywords", y= "chi2") + ggtitle("Republican") +
  theme(plot.title = element_text(hjust = 0.5))
```

![](US-Voter-ID_Nov21_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

``` r
target_dem <- tstat_key_inside_dem %>% slice_max(order_by = chi2, n = 20)
ggplot(data = target_dem, mapping = aes(x = reorder(feature, chi2), chi2)) + 
  geom_bar(stat = "identity", fill="blue") + coord_flip() + labs(x= "keywords", y= "chi2")+ ggtitle("Democratic") +
  theme(plot.title = element_text(hjust = 0.5))
```

![](US-Voter-ID_Nov21_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

#### Preparing data to visualise in Iramuteq

As I’m interested in plotting these words and their distance in a
network, I create a dataset for each party containing the text
surrounding (with a 20 word window) the key term “identification” by
using the KWIC() function.

``` r
id_dem <- kwic(
  tokens_dem,
  pattern= c( "identification"),
  window = 20,
  valuetype = c("glob", "regex", "fixed"),
)

id_rep <- kwic(
  tokens_dem,
  pattern= c( "identification"),
  window = 20,
  valuetype = c("glob", "regex", "fixed"),
)
```

Then I combine these two datasets and export it. To visualise this in
iramuteq, Democratic and Republican texts are copied into separate .txt
files and loaded into the software.

``` r
library(dplyr)
# Join pre and post keyword into a text column
id_dem$text <- paste(id_dem$pre, id_dem$keyword, id_dem$post)
id_rep$text <- paste(id_rep$pre, id_rep$keyword, id_rep$post)

# Assign column for each party
id_dem$Party <- "Democratic"
id_rep$Party <- "Republican"

# Join two datasets together
id <- rbind(id_dem, id_rep)

# Export
write.csv(id, "id.csv")
```
