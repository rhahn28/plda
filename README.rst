Parallelized Python/Cython implementation of Latent Dirichlet allocation
Final project for CS205 at Harvard University
Written by Charles Liu, Nicolas Drizard, and Virgile Audi

# System Requirements:

This package was tested on OSX. We ran experiments on Python 2.7 with needed packages:

- Numpy
- Threading
- Cython

The execution of the Cython scripts require a C compiler.

# Installation:

To install the package, download the zip folder from the git repository. We are working to have a pip install link soon.

# Documentation:

This Python package can be used to perform efficient topic modeling using Latent Dirichlet Allocation. More details on LDA can be found in the IPython notebook below. 

The organisation of the package is as follow:

 - Two classes: 
    
    * The *oviLDA* class to perform Online Variational Inference and the *cgsLDA* class to perform Collapsed Gibbs Sampling
 
    * These 2 classes have identical methods, and only a few specific attributes change for inference purposes:
 
        - Common attributes include:
        
       |    Attribute    |                        Type                       |                                                        Details                                                        |
       |:---------------:|:-------------------------------------------------:|:---------------------------------------------------------------------------------------------------------------------:|
       |    num_topics   |                        Int                        |                                                Number of topics desired                                               |
       |   num_threads   |                        Int                        |                                      Number of threads needed for parallelisation                                     |
       |      topics     | Array of dimensions: num_topics x len(vocabulary) | Each row representing a particular topic, after normalisation these can be treated as multinomial over the vocabulary |
       |      gamma      |   Array of dimensions: len(corpus) x num_topics   |                               Each row representing the topic assignment for a document                               |
       | _log_likelihood |                       Float                       |                                       Perplexity evaluated on the training data                                       |
        
        - OVI specific attributes:
        
        |    Attribute   |      Type      |                                Details                                |
        |:--------------:|:--------------:|:---------------------------------------------------------------------:|
        |   batch_size   |       Int      |             Number of document to consider in every batch             |
        |       tau      |       Int      |    Parameter used to weight the first iterations of the algorithm     |
        |      kappa     | Float: (0.5,1] | Parameter controlling the rate at which we forget previous iterations |
        | max_iterations |       Int      |        Maximum number of iterations on one particular document        |
        
        - CGS specific attributes:
        
        |   Attribute   |   Type  |                                       Details                                        |
        |:-------------:|:-------:|:------------------------------------------------------------------------------------:|
        |   iterations  |   Int   |                          Number of sampling iterations                               |
        |    damping    |   Int   | Likelihood full number of occurrences will be sampled. See notebook for more details |
        | sync_interval |   Int   |      Parameter controlling how often threads aggregate topic distributions           |
        |     alpha     |  Float  |                     Dirichlet prior parameter for document/topics                    |
        |      beta     |  Float  |                      Dirichlet prior parameter for topics/words                      |
        |  split_words  | Boolean |            Parallelization method used. See notebook for more details                |
        
        - Methods:
        
            * fit(dtm): fits the model for a particular corpus
            
            | Parameters |                    Type                   |        Details       |
            |------------|:-----------------------------------------:|----------------------|
            |     dtm    | array of dimensions: len(docs) x len(voc) | document term matrix |
           
            * transform(dtm): Transform new documents into a topic assignment matrix according to a previously trained model
            
            | Parameters |                    Type                   |                        Details                        |
            |------------|:-----------------------------------------:|-------------------------------------------------------|
            |     dtm    | array of dimensions: len(docs) x len(voc) | document term matrix (NO ZERO COLUMNS FOR CGS METHOD) |
            
            |   Return  |                     Type                    |        Details       |
            |-----------|:-------------------------------------------:|----------------------|
            |   gamma   | array of dimensions: len(docs) x num_topics |  Topic assignments   |
        
    
 -  Useful functions related to the LDA model in the LDAutil folder:
    
    * print_topic(model,vocabulary,num_top_words): prints the topics for a fitted LDA model
    
    |   Parameters  |                   Type                   |                              Details                              |
    |:-------------:|:----------------------------------------:|:-----------------------------------------------------------------:|
    |     model     |             cgsLDA or oviLDA             |                   A previously fitted LDA model                   |
    |   vocabulary  | array of dimensions: 1 x len(vocabulary) | An array of strings ordered in the same way as the columns of DTM |
    | num_top_words |                    Int                   |                  Number of wanted words per topic                 |
    
    * perplexity(model,dtm_test): computes the log-likelihood of the documents in dtm_test based on the
    topic distribution already learned by the model
    
    | Parameters |                       Type                       |                                          Details                                         |
    |:----------:|:------------------------------------------------:|:----------------------------------------------------------------------------------------:|
    |    model   |                 cgsLDA or oviLDA                 |                               A previously fitted LDA model                              |
    |   dtm_new  | array of dimensions: len(docs) x len(vocabulary) | A new DTM corresponding to the new documents on which we want to evaluate the perplexity |

    |   Return   |  Type |                Details                |
    |:----------:|:-----:|:-------------------------------------:| 
    | perplexity | float | Perplexity evaluated on new documents |

More details on these functions and what they actually evaluate are present in the Ipython notebook.
    
 - A subset of the Reuters news dataset in the form of a document term matrix and the associated vocabulary.
    

# Test to run:

For you to test if your system is up to the requirements and to showcase the package in action, we included a Python test.py file.

You can run both versions of LDA by commenting and uncommenting respectively lines 36 and 39.

# References:

- The OVI code is based on Hoffman's 2010 paper ["Online Learning for Latent Dirichlet Allocation"](https://www.cs.princeton.edu/~blei/papers/HoffmanBleiBach2010b.pdf)
- The CGS code relies on:
    * [Efficient Collapsed Gibbs Sampling For Latent Dirichlet Allocation](http://jmlr.csail.mit.edu/proceedings/papers/v13/xiao10a/xiao10a.pdf) by Han Xiao and Thomas Stibor
    * [Parallel Inference for Latent Dirichlet Allocation on Graphics Processing Units](https://www.cs.purdue.edu/homes/alanqi/papers/Parallel-Inf-LDA-GPU-NIPS.pdf) by Feng Yan, Ningyi Xu and Yuan (Alan) Qi
