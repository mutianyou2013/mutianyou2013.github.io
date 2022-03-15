---
layout: post
read_time: true
show_date: true
title: "Recommendation Systems"
date: 2021-11-02
img: posts/20211102/CE.png
tags: [Machine Learning, Cluster Expansion, Academia]
author: Tianyou Mou
description: "Machine learning for computational catalysis."
mathjax: yes
---

## What is a recommendation system

Recommendation engines are a subclass of machine learning which generally deal with ranking or rating products/users. Recommender systems are algorithms aim at suggesting relevant items to users. They have been widely applied by many big name companies like Youtube, Amazon, Netflix and others to increase the engagement with users and the platform. Recommendation systems are employed since they can generate more profit when companies use efficient systems and help them stand out from competitors. There are many real life examples, YouTube uses recommendations to suggest you other videos similar to the ones you liked or repeatedly watched. Amazon also uses them to suggest products to users when they are browsing items. Here, I will go through different paradigms of recommendation systems with implementation of them.  

## Various recommendation systems and how to define a good system

There are three major paradigms of recommendation systems: collaborative filtering methods, content based methods, and a combination of them called hybrid methods. The collaborative filtering recommendation systems are methods that are based solely on the past interactions between users and items in order to predict new recommendations. While for the content based approaches additional information about users and items can be used. For example of a movies recommendation system, additional information can be the age, the job or any other personal information for users as well as the actors, the category of the movies or items. The hybrid system make a combination of both systems.

The evaluation of recommendation systems is necessary since we have to decide which algorithm fits the best in our situation. It's normal to use metrics based evaluation like error measurement metric like mean absolute error. This means you train the model with only a part of the available data and test with the remaining. This method of evaluation is solely dependent on the dataset and approach used to generate the recommendation. So common statistical accuracy measures to evaluate accuracy of the recommender can be employed, for example, root mean squared deviation (RMSD), mean absolute error (MAE), and k fold cross validation. 

## Data preparation

I will present different methods of creating recommendation engines and the associated implementations in Python. Here I provide a script to synthesize a dataset associated to books. This dataset will be used for applications of recommendation systems in the following sections. 
```python
import pandas as pd
from random import randint

def generate_data(n_books = 3000, n_genres = 10, n_authors = 450, n_publishers = 50, n_readers = 30000, dataset_size = 100000):
    '''
    This function will generate a dataset with features associated to
    book data set. The dataset will have the following columns : 
        - book_id (String) : Unique identified for the book
        - book_rating (Integer) : A value between 0 and 10
        - reader_id (String) : Unique identifier for the user
        - book_genre (Integer) : An integer representing a genre for the book, 
                                 value is between 1 and 15, indicating that 
                                 there are 15 unique genres. Each book can only
                                 have 1 genre
        - author_id (String) : Unique identifier for the author of the book
        - num_pages (Integer) : Random value between 70 and 500
        - publisher_id (String) : A unique identifier for the publisher of the book
        - publish_year (Integer) : The year of book publishing
        - book_price (Integer) : The sale price of the book
        - text_lang (Integer) : The language of the book - returns an integer which 
                                is mapped to some language
        
    params:
        n_books (Integer) : The number of books you want the dataset to have
        n_genres (Integer) : Number of genres to be chosen from
        n_authors (Integer) : Number of authors to be generated
        n_publishers (Integer) : Number of publishers for the dataset
        n_readers (Integer) : Number of readers for the dataset
        dataset_size (Integer) : The number of rows to be generated 
        
    example:
        data = generate_data()
    '''
    
    d = pd.DataFrame(
        {
            'book_id' : [randint(1, n_books) for _ in range(dataset_size)],
            'author_id' : [randint(1, n_authors) for _ in range(dataset_size)],
            'book_genre' : [randint(1, n_genres) for _ in range(dataset_size)],
            'reader_id' : [randint(1, n_readers) for _ in range(dataset_size)],
            'num_pages' : [randint(75, 700) for _ in range(dataset_size)],
            'book_rating' : [randint(1, 10) for _ in range(dataset_size)],
            'publisher_id' : [randint(1, n_publishers) for _ in range(dataset_size)],
            'publish_year' : [randint(2000, 2021) for _ in range(dataset_size)],
            'book_price' : [randint(1, 200) for _ in range(dataset_size)],
            'text_lang' : [randint(1,7) for _ in range(dataset_size)]
        }
    ).drop_duplicates()
    return d
  
d = generate_data(dataset_size = 100000)
d.to_csv('data.csv', index = False)
```
## Collaborative Filtering Recommendation System

Collaborative filtering is the process of predicting the interests of a user by identifying preferences and information from many users. This is done by filtering data for information or patterns using techniques involving collaboration among multiple agents, data sources, etc. The idea behind the collaborative filtering is that if user A and B have similar taste in a product, then A and B are likely to have similar taste in other products as well. 

## Content Based Recommendation System

## Hybrid Recommendation System

## Conclusions

## References 

