:author: Hendrik Heuer
:email: hendrik.heuer@aalto.fi
:institution: Aalto University, Finland

------------------------------------------------------------------------------
Text comparison using word vector representations and dimensionality reduction
------------------------------------------------------------------------------

.. class:: abstract

   This paper describes a technique to compare large text sources using word vector representations (word2vec) and dimensionality reduction (t-SNE) and how it can be implemented using Python. The technique provides a bird’s-eye view of text sources, e.g. text summaries and their source material, and enables users to explore text sources like a geographical map. Word vector representations capture many linguistic properties such as gender, tense, plurality and even semantic concepts like "capital city of". Using dimensionality reduction, a 2D map can be computed where semantically similar words are close to each other. The technique uses the word2vec model from the gensim Python library and t-SNE from scikit-learn.

.. class:: keywords

   Text Comparison, Topic Comparison, word2vec, t-SNE

Introduction
------------

When summarizing a large text, only a subset of the available topics and stories can be taken into account. The decision which topics to cover is largely editorial. This paper introduces a tool that assists this editorial process using word vector representations and dimensionality reduction. It enables a user to visually identify agreement and disagreement between two text sources. 

There are a variety of different ways to approach the problem of visualizing the topics present in a text. The simplest approach is to look at unique words and their occurrences and visualize the words in a list. Topics could also be visualized using word clouds, where the font size of a word is determined by the frequency of the word. Word clouds have a variety of shortcomings: they can only visualize small subsets, they focus on the most frequent words and they do not take synonyms and semantically similar words into account.

This paper describes a human-computer interaction-inspired approach of comparing two text sources. The approach yields a bird’s-eye view of different text sources, including text summaries and their source material, and enables users to explore a text source like a geographical map.
As similar words are close to each other, the user can visually identify clusters of topics that are present in the text.

This paper describes a tool, which can be used to visualize the topics in a single text source as well as to compare different text sources. To compare the topics in source A and source B, three different sets of words can be computed: a set of unique words in source A, a set of unique words in source B as well as the intersection set of words both in source A and B. These three sets are then plotted at the same time. For this, a colour is assigned to each set of words. This enables the user to visually compare the different text sources and makes it possible to see which topics are covered where. The user can explore the word map and zoom in and out. He or she can also toggle the visibility, i.e. show and hide, certain word sets.

The comparison can be used to visualize the difference between a text summary and its source material. It can also help to compare Wikipedia revisions in regards to the topics they cover. Another possible application is the visualization of heterogeneous data sources like a list of search queries and keywords. 

The Github repository of the tool includes an online demo [Heu15]. The tool can be used to explore the precomputed topic sets of the Game of Thrones Wikipedia article revisions from 2013 and 2015. The repository also includes the precomputed topic sets for the Wikipedia article revisions for the articles on World War 2, Facebook, and the United States of America.

Distributional semantic models
------------------------------

The distributional hypothesis by Harris states that words with similar meaning occur in similar contexts [Sah05]. This implies that the meaning of a word can be inferred from its distribution across contexts. The goal of distributional semantics is to find a representation, e.g. a vector, that approximates the meaning of a word [Bru14]. The traditional approach to statistical modeling of language is based on counting frequencies of occurrences of short word sequences of length up to N and did not exploit distributed representations [Cun15].  Distributional semantics takes word co-occurrence in context windows into account.

The general idea behind word space models is to use distributional statistics to generate high-dimensional vector spaces, where a word is represented by a context vector that encodes semantic similarity [Sah05]. The representations are called distributed representations because the features are not mutually exclusive and because their configurations correspond to the variations seen in the observed data [Cun15]. LeCun et al. provide the example of a news story. When the task is to predict the next word in a news story, the learned word vectors for Tuesday and Wednesday will be very similar as they can be easily replaced by each other when used in a sentence [Cun15].

There are a variety of computational models that implement the distributional hypothesis, including word2vec [Che13], GloVe [Pen14], dependency-based word embeddings [Lev14] and Random Indexing [Sah05]. There are a variety of Python implementations of these techniques. word2vec is available in gensim [Řeh10]. For GloVe, the C source code was ported to Python [Gau15, Kul15]. The dependency-based word embeddings by Levy and Goldberg are implemented in spaCy [Hon15]. Random Indexing is available in an implementation by Joseph Turian [Tur10].

For this paper, word2vec was selected because Mikolov et al. provide 1.4 million pre-trained entity vectors trained on 100 billion words from various news articles in the Google News dataset [Che13]. However, other models might perform equally well for the purpose of text comparison. Moreover, custom word vectors trained on a large domain-specific dataset, e.g. the Wikipedia encyclopedia for the Wikipedia revision comparison, could potentially yield even better results. 

word2vec
~~~~~~~~

word2vec is a tool developed by Mikolov, Sutskever, Chen, Corrado, and Dean at Google. The two model architectures in the C tool were made available under an open-source license [Mik13]. Gensim provides a Python reimplementation of word2vec [Řeh10].

Word vectors encode semantic meaning and capture many different degrees of similarity [Lev14]. word2vec word vectors can capture linguistic properties such as gender, tense, plurality, and even semantic concepts such as "is the capital city of". word2vec captures domain similarity while other more dependency-based approaches capture functional similarity. 

In the word2vec vector space, linear algebra can be used to exploit the encoded dimensions of similarity. Using this, a computer system can complete tasks like the Scholastic Assessment Test (SAT) analogy quizzes, that measure relational similarity. 

.. math::

   king - man + woman \approx queen

It works for the superlative:

.. math::

   fastest - fast + slow \approx slowest

As well as the past participle:

.. math::

   woken - wake + be \approx been

It can infer the Finnish national sport from the German national sport.

.. math::

   football - Germany + Finland \approx hockey

Based on the last name of the current Prime Minister of the United Kingdom, it identifies the last name of the German Bundeskanzlerin:

.. math::

   Cameron - England + Germany \approx Merkel

The analogies can also be applied to the national dish of a country:

.. math::

   haggis - Scotland + Germany \approx Currywurst

Fig. 1 shows the clusters of semantically similar words and how they form semantic units, which can be easily interpreted by humans.

.. figure:: word_clusters.png

   Clusters of semantically similar words emerge when the word2vec vectors are projected down to 2D using t-SNE. :label:`egfig`

Dimensionality reduction with t-SNE
-----------------------------------

t-distributed Stochastic Neighbour Embedding (t-SNE) is a dimensionality reduction technique that retains the local structure of data and that helps to visualize large real-world datasets with limited computational demands [Maa08]. Vectors that are similar in a high-dimensional vector space get represented by two- or three-dimensional vectors that are close to each other in the two- or three-dimensional vector space. Dissimilar high-dimensional vectors are distant in the two- or three-dimensional vector space. Meanwhile, the global structure of the data and the presence of clusters at several scales is revealed. t-SNE is well-suited for high-dimensional data that lies on several different, but related, low-dimensional manifolds [Maa08].

t-SNE achieves this by minimizing the Kullback-Leibler divergence between the joint probabilities of the high-dimensional data and the low-dimensional representation. The Kullback-Leibler divergence measures the faithfulness with which a probability distribution q represents a probability distribution p by a discrete scalar and equals zero if the distributions are the same [Maa08]. The Kullback-Leibler divergence is minimized using the gradient descent method. In contrast to other Stochastic Neighbor Embedding methods that use Gaussian distributions, it uses a Student t-distribution.

Implementation
--------------

The text comparison tool implements a workflow that consists of a Python tool for the back-end and a Javascript tool for the front-end. With the Python tool, a text is converted into a collection of two-dimensional word vectors. These are visualized using the Javascript front-end. With the Javascript front-end, the user can explore the word map and zoom in and out to investigate both the local and the global structure of the text sources. The Javascript front-end can be published online.

The workflow of the tool includes the following four steps: 

Pre-processing
~~~~~~~~~~~~~~

In the pre-processing step, all sentences are tokenized to extract single words. The tokenization is done using the Penn Treebank Tokenizer implemented in the Natural Language Processing Toolkit (NLTK) for Python [Bir09]. Alternatively, this could also be achieved with a regular expression.

Using a hash map, all words are counted. Only unique words, i.e. the keys of the hash map, are taken into account for the dimensionality reduction. The 3000 most frequent English words according to a frequency list collected from Wikipedia are ignored to reduce the amount of data.

Word representations
~~~~~~~~~~~~~~~~~~~~

For all unique non-frequent words, the word representation vectors are collected from the word2vec model from the gensim Python library [Řeh10]. Each word is represented by an N-dimensional vector (N=300, informed by the best accuracy in [Mik13] and following the default in [Che13]). 

.. code-block:: python

   from gensim.models import Word2Vec

   model = Word2Vec.load_word2vec_format(
    word_vectors_filename, binary=True 
   )

   for word in words:
     if word in model:
       print model[word]


Dimensionality Reduction
~~~~~~~~~~~~~~~~~~~~~~~~

The resulting N-dimensional word2vec vectors are projected down to 2D using the t-SNE Python implementation in scikit-learn [Ped11].

In the dimensionality reduction step, the N-dimensional word vectors are projected down to a two-dimensional space so that they can be easily visualized in a 2D coordinate system (see Fig. 2).

.. figure:: tsne_dimensionality_reduction.png

   In the dimensionality reduction step, the word vectors are projected down to 2D. :label:`egfig`

For the implementation, the t-SNE implementation in scikit-learn is used:

.. code-block:: python

   from sklearn.manifold import TSNE

   tsne = TSNE(n_components=2)
   tsne.fit_transform(word_vectors)

Visualization
~~~~~~~~~~~~~

After the dimensionality reduction, the vectors are exported to a JSON file. The vectors are visualized using the D3.js JavaScript data visualization library [Bos12]. Using D3.js, an interactive map was developed. With this map, the user can move around and zoom in and out. The colour coding helps to judge the ratio of dissimilar and similar words. At the global scale, the map can be used to assess how similar two text sources are to each other. At the local scale, clusters of similar words can be explored.

Results
--------------

As with many unsupervised methods, the evaluation can be difficult and the quality of the visualization is hard to quantify. The goal of this section is, therefore, to introduce relevant use cases and illustrate how the technique can be applied. 

The flow described in the previous section can be applied to different revisions of Wikipedia articles. For this, a convenience sample of the most popular articles in 2013 from the English Wikipedia was used.  For each article, the last revision from the 31st of December 2013 and the most recent revision on the 26th of May 2015 were collected. The assumption was that popular articles will attract sufficient changes to be interesting to compare. The list of the most popular Wikipedia articles includes Facebook, Game of Thrones, the United States, and World War 2.

The article on Game of Thrones was deemed especially illustrative for the task of comparing the topics in a text, as the storyline of the TV show developed between the two different snapshot dates as new characters were introduced. Other characters became less relevant and were removed from the article. The article on World War 2 was especially interesting as one of the motivations for the topic tool is to find subtle changes in data.

Fig. 3 shows how different the global cluster, i.e. the full group of words on the minimum zoom setting, of the Wikipedia articles on the United States, Game of Thrones and World War 2 are.

.. figure:: global_clusters.png

   Global clusters of the Wikipedia articles on the United States (left), Game of Thrones (middle), and World War 2 (right). :label:`egfig`

Fig. 4 shows four screenshots of the visualization of the Wikipedia articles on the United States including an overview and detail views that only show the intersection set of words, words only present in the 2013 revision of the article, and words only present in the 2015 revision of the article. 

.. figure:: topic_comparison_usa.png

   Topic comparison of the Wikipedia article on the United States. In the top left, all words in both texts are plotted. On the top right, only the intersection set of words is shown. In the bottom left, only the words present in the 2013 revision are displayed. In the bottom right, only the words present in the 2015 revision are shown. :label:`egfig`

When applied to Game of Thrones, it is e.g. easy to visually compare names that were removed since 2013 and that were added in 2015 (Fig. 5). Using the online demo available [Heu15], this technique can be applied to the Wikipedia articles on the United States and World War 2. 

The technique can also be applied to visualize the Google search history of an individual. Similar words are represented by similar vectors. Thus, terms related to different topics, e.g. technology, philosophy or music, will end up in separate clusters.

.. figure:: game_of_thrones.png

   Names present in the Wikipedia article on Game of Thrones. Red names were added to the 2015 revision, orange names removed. White names are present in both revisions.

Conclusion
----------

Word2vec word vector representations and t-SNE dimensionality reduction can be used to provide a bird’s-eye view of different text sources, including text summaries and their source material. This enables users to explore a text source like a geographical map. 

The paper gives an overview of an ongoing investigation of the usefulness of word vector representations and dimensionality reduction in the text and topic comparison context. The major flaw of this paper is that the introduced text visualization and text comparison approach is not validated empirically.

As many researchers publish their source code under open source licenses and as the Python community embraces and supports these publications, it was possible to integrate the findings from the literature review of my Master's thesis into a useable tool. Distributed representations are an active field of research. New findings on word, sentence or paragraph vectors can be easily integrated into the workflow of the tool.

Both the front-end and the back-end of the implementation were made available on GitHub under GNU General Public License 3 [Heu15]. The repository includes the necessary Python code to collect the word2vec representations using Gensim, to project them down to 2D using t-SNE and to output them as JSON. The repository also includes the front-end code to explore the JSON file as a geographical map.

The tool can be used in addition to topic modeling techniques like LDA. It enables the comparison of large text sources at a glance and is aimed at similar text sources with subtle differences.

References
----------

.. [Bir09] S. Bird, E. Klein, and E. Loper, Natural Language Processing with Python, 1st ed. O’Reilly Media, Inc., 2009.

.. [Bos12] M. Bostock, D3.js - Data-Driven Documents. 2012.

.. [Bru14] E. Bruni, N. K. Tran, and M. Baroni, “Multimodal Distributional Semantics,” J. Artif. Int. Res., vol. 49, no. 1, pp. 1–47, Jan. 2014.

.. [Che13] T. Mikolov, K. Chen, G. Corrado, and J. Dean, word2vec. 2013. Available: https://code.google.com/p/word2vec/. [Accessed 18-Aug-2015].

.. [Cun15] Y. LeCun, Y. Bengio, and G. Hinton, “Deep learning,” Nature, vol. 521, no. 7553, pp. 436–444, May 2015.

.. [Gau15] J. Gauthier, glove.py. GitHub, 2015. Available: https://github.com/hans/glove.py. [Accessed: 06-Aug-2015].

.. [Heu15] H. Heuer, Topic Comparison Tool. GitHub, 2015. Available: https://github.com/h10r/topic_comparison_tool. [Accessed: 06-Aug-2015].

.. [Hon15] M. Honnibal, spaCy. 2015. Available: https://honnibal.github.io/spaCy/. [Accessed: 06-Aug-2015].

.. [Kul15] M. Kula, glove-python. GitHub, 2015. Available: https://github.com/maciejkula/glove-python. [Accessed: 06-Aug-2015].

.. [Lev14] O. Levy and Y. Goldberg, “Dependency-Based Word Embeddings,” in Proceedings of the 52nd Annual Meeting of the Association for Computational Linguistics (Volume 2: Short Papers), Baltimore, Maryland, 2014, pp. 302–308.

.. [Maa08] L. Van der Maaten and G. Hinton, “Visualizing data using t-SNE,” Journal of Machine Learning Research, vol. 9, no. 2579–2605, p. 85, 2008.

.. [Mik13] T. Mikolov, K. Chen, G. Corrado, and J. Dean, “Efficient Estimation of Word Representations in Vector Space,” CoRR, vol. abs/1301.3781, 2013.

.. [Ped11] F. Pedregosa, G. Varoquaux, A. Gramfort, V. Michel, B. Thirion, O. Grisel, M. Blondel, P. Prettenhofer, R. Weiss, V. Dubourg, J. Vanderplas, A. Passos, D. Cournapeau, M. Brucher, and E. Duchesnay, “Scikit-learn: Machine Learning in Python,” Journal of Machine Learning Research, vol. 12, pp. 2825–2830, 2011.

.. [Pen14] J. Pennington, R. Socher, and C. D. Manning, “GloVe: Global Vectors for Word Representation,” in Proceedings of EMNLP, 2014.

.. [Sah05] M. Sahlgren, “An introduction to random indexing,” in Methods and applications of semantic indexing workshop at the 7th international conference on terminology and knowledge engineering, TKE, 2005, vol. 5.

.. [Tur10] J. Turian, Random Indexing Word Representations. Github, 2010. [Online]. Available: https://github.com/turian/random-indexing-wordrepresentations. [Accessed: 06-Aug-2015].

.. [Řeh10] R. Řehůřek and P. Sojka, “Software Framework for Topic Modelling with Large Corpora,” in Proceedings of the LREC 2010 Workshop on New Challenges for NLP Frameworks, Valletta, Malta, 2010, pp. 45–50.
