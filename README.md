# Slangvolution
Git repo for the paper [**Slangvolution: A Causal Analysis of Semantic Change and Frequency Dynamics in Slang**](https://arxiv.org/), published as a main conference paper at ACL 2022. This README will walk you through the code and how to reproduce our results. 

Start by installing all libraries:

`pip install -r requirements.txt`

## Data Preparation

All the words used in this study are listed under `all_words.csv`.

To retrieve tweets, add your bearer token to `twitter_api.py`. Then run:

`get_tweets.py --type slang --year 2020 --num-dates 40 --hour-gap 24 --max-results 50`

To approximate word frequency, run: 

`get_word_frequencies.py --type slang --year 2020 --num-dates 40`

What each parameter means: 
- type: string corresponding to word type, can be slang, nonslang, or both
- year: 2020 or 2010
- num-dates: integer, number of random time points to sample per year 
- hour-gap: integer, number of hours to consider for tweet collection, after each random time point 
- max-results: integer, maximum number of tweets to get per request 

The Urban Dictionary data used for fine-tuning is taken from [**Urban Dictionary Embeddings for Slang NLP Applications**](https://aclanthology.org/2020.lrec-1.586/). Please reach out to the original authors for access. Then run:

`python UD_data_preprocessing.py --path PATH-TO-DATA/all_definitions.dat`

This will provide you with a filtered UD csv file, used for fine-tuning.

## Representation Retrieval

With the data from the previous step, run `python MLM_fine_tuning.py` to retrieve four models (with different learning rates). We recommend doing this remotely on a GPU (it takes a couple of days), but if you just wanna make sure that the code runs &mdash; add `--small True` and set the number of epochs to be small `--num-epochs 1`.

Then, apply RoBERTa to the tweets to get the representations: `representations_retrieval.py --type slang`. By default this will give you the summed representations across layers. Provide the data path to the old/new slang/nonslang/hybrid tweets with `--data-path data/tweets_new/slang_word_tweets`. The script will write two files, one for the representations and one for the tweet texts.

You may also get the representations for the SemEval 2020 Task 1 data with the same script, by adding `--sem-eval True`. Download the data from [here](https://www.ims.uni-stuttgart.de/en/research/resources/corpora/sem-eval-ulscd-eng/).

## Semantic Change and Frequency Shift Scores

To get the APD scores on representations reduced to 100 dimensions with PCA run:

`get_semantic_change_scores.py --method apd --reduction pca`

Once the frequencies are saved, run 

`frequency_change_analysis.py`

To save dataframes with the frequency change statistics for each word type 
