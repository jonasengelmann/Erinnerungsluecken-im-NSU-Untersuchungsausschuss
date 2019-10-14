# Erinnerungslücken im NSU-Untersuchungsausschuss
>Translation: Gaps of memory during the investigation/hearings of the parliamentary enquiry committee into the NSU (national socialist underground)


A parliamentary inquiry committee was set up in 2016 with the aim of comprehensively investigating the significant failures and errors of the security authorities in connection with the right-extremist terrorist organization NSU. Particularly noticeable in these interrogations is the considerable lack of memory, which was always expressed by the summoned witnesses and eventually prevented a comprehensive investigation.

I have automatically captured a plethora of such instances where witnesses expressed their inability to remember. The results are visualized [here](https://erinnerungsluecken-im-nsu-untersuchungsausschuss.de). In this repository I will describe the tools and methods I used.

## 1. Scraping PDFs and parsing relevant information

As I could only find the transcript in PDF format, I had to scrape its content first. The process is straightforward and can be reproduced using [this](https://github.com/jonasengelmann/erinnerungsluecken-im-nsu-untersuchungsausschuss/blob/master/Scraping_and_parsing_of_transcripts.ipynb) Jupyter notebook. [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/jonasengelmann/erinnerungsluecken-im-nsu-untersuchungsausschuss/blob/master/Scraping_and_parsing_of_transcripts.ipynb)

## 2. Semantic matching


### 2.1. With Regular Expressions:

In a first attempt I matched a few commonly used expressions with simple regular expression rules. Sentences like these: 

* Ich erinnere das nicht mehr
* Ich kann mich nicht mehr erinnern
* Das ist mir nicht erinnerlich

can easily be matched with a rule like this:

```
.*?erinnere.*?nicht.*?|.*?nicht.*?erinner.*?
```
(.*? matches any character thus ensuring small variations with the same sentence structure are captured too)


However, this approach can easily misclassify, as there is quite a high chance that the negation matched does not relate to the verb or noun in question. To best avoid this behavior I run those matching rules only on subordinate clauses, i.e. using the start/end of a sentence, a comma, semicolon or colon as demarcations.

By adding a variety of synonyms expression that came to mind I was able to match quite a few instances with this method. Yet, it is quite easy to miss a few, so I was looking for a more generalized approach.

### 2.2 With BERT:

In a second attempt I trained a sentence classifier with [BERT](https://github.com/google-research/bert). BERT is a model that provides language representation based on contextual word embeddings, meaning it encodes words in a multidimensional vector space based on the contexts (the surrounding words) they appear in a sentence. This has the interesting effect that some level of semantic information is encoded in the vector space as well, as similar and synonymous words appear in vicinity to each other.

Using the regular expression from above I label a training dataset which I used to fine-tune the BERT model to this specific classification task, which is analogous to a spam vs not-spam problem, i.e. I-don't-remember vs anything-else.

The Jupyter notebook can be found [here](https://github.com/jonasengelmann/erinnerungsluecken-im-nsu-untersuchungsausschuss/blob/master/Semantic_matching.ipynb). 
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://github.com/jonasengelmann/erinnerungsluecken-im-nsu-untersuchungsausschuss/blob/master/Semantic_matching.ipynb) When opening in Colab, a GPU has to be selected under runtime -> Change runtime type.