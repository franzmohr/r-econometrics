---
title: "William Shakespeare's Work in a Word Cloud"
author: "Franz X. Mohr"
date: '2018-12-30'
tags:
  - r
  - wordcloud
  - text-mining
---

Word or tag clouds seem to be quite popular at the moment. Although their analytical power might be limited, they do serve an aesthetic purpose and, for example, could be put on the cover page of a thesis or a presentation using the content of your work or the literature you went through. This post uses text data from the Gutenberg project to give a step-by-step introduction on how to create a wordcould in R.

<!--more-->

Inspired by <a href="https://www.r-bloggers.com/constructing-a-word-cloud-for-icml-2015/" target="_blank" rel="noopener">Andrew Collier's blog post on creating word clouds from multiple PDF-sources</a> I played around with some texts from the Gutenberg project to visualize some of the most prominent pieces of classic literature. Then I remembered my old English teacher, who said that Shakespeare's work basically hinged on the motives of "love", "death" and "aristocracy". So, I became curious about how this might be reflected in a word cloud that is drawn from the complete works of William Shakespeare. Fortunately, a complete version of the master's work is available on the <a href="http://www.gutenberg.org/ebooks/100" target="_blank" rel="noopener">Project Gutenberg</a>, where you can also find a myriad of other free literature to download and read (legally!!!). But let's postpone the reading and concentrate on how to make a word cloud from such data. I will go through this step-by-step as in Andrew Collier' post.

Download the raw text file into R and read its lines.

```r
rawc <- readLines(file("http://www.gutenberg.org/files/100/100-0.txt"))
```

Both at the beginning and the end of the file there is a lot of information on how to use the file and copyright issues. Since such modern text might bias our results, we omit those lines by looking only at the part between them using `rawc[142:151140]`. Then we put the relevant lines together into a single character string:


```r
text <- paste(rawc[142:151140], collapse = " ")
```

Convert all letters into lower cases. After that you will need the `tm` package for **t**ext **m**ining, which contains functions to remove numbers and punctuation.

```r
text <- tolower(text) # Convert all letters into lower cases

library(tm) # install.packages("tm")
text <- removeNumbers(text) # Remove all numbers
text <- removePunctuation(text) # Remove punctuation
```

The `tm` package also includes a function that allows to remove words from the sample, which are very frequently used, but add no content to the analysis. First, there is a prespecified list of words available that contains such words. It can be easily accessed by `stopwords("english")`.

When it is used in `removeWords`, a bunch of unnecessary words will be removed from the data. A little warning here: This takes some time...


```r
text <- removeWords(text, stopwords("english"))
```

Secondly, I removed some additional words, which I collected by iteratively running the code of this post and did not want to have in the sample. Again, this takes some time.


```r
exclude <- c("shall", "thee", "thy", "thus", "will", "come",
             "know", "may", "upon", "hath", "now", "well", "make",
             "let", "see", "tell", "yet", "like", "put", "speak",
             "give", "speak", "can", "comes", "makes", "sees", "tells",
             "likes", "puts", "speaks", "gives", "speaks", "knows",
             "say", "says", "take", "takes", "exeunt", "though", "hear", 
             "think", "hears", "thinks", "listen", "listens", "hear", 
             "hears", "follow" ,"commercially" ,"commercial" , "readable",
             "personal", "doth", "membership", "stand", "therefore", 
             "complete", "tis", "electronic", "prohibited", "must",
             "look", "looks", "call", "calls", "done", "prove", "whose",
             "enter", "one", "words", "thou", "came", "much", "never",
             "wit", "leave", "even", "ever", "distributed" , "keep",
             "stay", "made", "scene", "many", "away", "exit", "shalt")

text <- removeWords(text, exclude)
```

Split the single character string into a list of words, get rid of the empty entries and keep all entries with more than two letters.


```r
text <- strsplit(text, split = " ")[[1]]
text <- text[text != ""]
text <- text[nchar(text) > 2]
```

The next step requires the `dplyr` package. Create a *tibble* from a data frame of the word list and count the number of instances of a word in that table. It is useful to let the count function sort the result according to the number of instances.


```r
library(dplyr)

text_tbl <- as_tibble(as.data.frame(text)) # Create table
text_tbl <- count(text_tbl, sort = TRUE, vars = text) # Count and sort words
```

Basically, you could already make a word cloud from this data. However, since the amount of words is quite high (27,642), I would like to limit the set of plotted words to 300, which I deem to be a good size for word clouds, because you are still impressed by the number of words in the cloud without losing the ability to get a basic impression of the content from it.


```r
text_tbl <- text_tbl[1:300,] # Extract the first 300 most frequent words
```

Finally, you can plot the word cloud using the function `tagcloud` from the `tagcloud` package, where you have to specify the words and their weights, i.e. counts. Additionally, I used `fvert = .3` so that 30 percent of the words in the could are aligned vertically.


```r
library(tagcloud)
tagcloud(text_tbl$vars, weights = text_tbl$n, fvert = .3)
```

<img src="/post/2018-12-30-shakespeare-wordcloud_files/figure-html/shakespeare-1.png" width="576" style="display: block; margin: auto;" />

Look at the result. As we can see, my English teacher was quite right, although I did not expect that high concentration of words with masculine aristocratic connotation. However, since we excluded words like "thy", "shall", "will", "thou" etc., much content from the sonnets is lost, where a lot of motives like "love" and "desire" are not explicitly mentioned, but at least implicitly present. This might cause a bias towards motives from the dramatic pieces.
