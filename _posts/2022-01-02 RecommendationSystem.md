---
layout: post
read_time: true
show_date: true
title: "Recommendation Systems"
date: 2022-01-02
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

There are two common types of approaches in collaborative filtering, memory based and model based approach.

### Memory based approaches

Often referred to as neighbourhood collaborative filtering. Essentially, ratings of user-item combinations are predicted on the basis of their neighbourhoods. This can be further split into user based collaborative filtering and item based collaborative filtering. User based essentially means that likeminded users are going to yield strong and similar recommendations. Item based collaborative filtering recommends items based on the similarity between items calculated using user ratings of those items.

### Model based approaches

They are predictive models using machine learning. Features associated to the dataset are parameterized as inputs of the model to try to solve an optimization related problem. Model based approaches include using things like decision trees, rule based approaches, latent factor models etc.

### Implementation

- Import data from generate_data function (function provided above) or download the CSV from here. 
- Generate a pivot table with readers on the index and books on the column and values being the ratings
- Calculate similarity between items and users using svds.
- Generate item recommendations based on user_id.

```python
import pandas as pd
import numpy as np

from scipy.sparse import csr_matrix
from scipy.sparse.linalg import svds

def normalize(pred_ratings):
    '''
    This function will normalize the input pred_ratings
    
    params:
        pred_ratings (List -> List) : The prediction ratings 
    '''
    return (pred_ratings - pred_ratings.min()) / (pred_ratings.max() - pred_ratings.min())
  
def generate_prediction_df(mat, pt_df, n_factors):
    '''
    This function will calculate the single value decomposition of the input matrix
    given n_factors. It will then generate and normalize the user rating predictions.
    
    params:
        mat (CSR Matrix) : scipy csr matrix corresponding to the pivot table (pt_df)
        pt_df (DataFrame) : pandas dataframe which is a pivot table
        n_factors (Integer) : Number of singular values and vectors to compute. 
                              Must be 1 <= n_factors < min(mat.shape). 
    '''
    
    if not 1 <= n_factors < min(mat.shape):
        raise ValueError("Must be 1 <= n_factors < min(mat.shape)")
        
    # matrix factorization
    u, s, v = svds(mat, k = n_factors)
    s = np.diag(s)

    # calculate pred ratings
    pred_ratings = np.dot(np.dot(u, s), v) 
    pred_ratings = normalize(pred_ratings)
    
    # convert to df
    pred_df = pd.DataFrame(
        pred_ratings,
        columns = pt_df.columns,
        index = list(pt_df.index)
    ).transpose()
    return pred_df

def recommend_items(pred_df, usr_id, n_recs):
    '''
    Given a usr_id and pred_df this function will recommend
    items to the user.
    
    params:
        pred_df (DataFrame) : generated from `generate_prediction_df` function
        usr_id (Integer) : The user you wish to get item recommendations for
        n_recs (Integer) : The number of recommendations you want for this user
    '''
    
    usr_pred = pred_df[usr_id].sort_values(ascending = False).reset_index().rename(columns = {usr_id : 'sim'})
    rec_df = usr_pred.sort_values(by = 'sim', ascending = False).head(n_recs)
    return rec_df
  
if __name__ == '__main__':
    # constants
    PATH = '../data/data.csv'

    # import data
    df = pd.read_csv(PATH)
    print(df.shape)

    # generate a pivot table with readers on the index and books on the column and values being the ratings
    pt_df = df.pivot_table(
        columns = 'book_id',
        index = 'reader_id',
        values = 'book_rating'
    ).fillna(0)

    # convert to a csr matrix
    mat = pt_df.values
    mat = csr_matrix(mat)
    
    pred_df = generate_prediction_df(mat, pt_df, 10)

    # generate recommendations
    print(recommend_items(pred_df, 5, 5))
```

## Content Based Recommendation System

Content based systems generates recommendations based on the users preferences and profile. They try to match users to items which they’ve liked previously. The level of similarity between items is generally established based on attributes of items liked by the user. Unlike most collaborative filtering models which leverage ratings between target user and other users, content based models focus on the ratings provided by the target user themselves. In essence, the content based approach leverages different sources of data to generate recommendations.

The simplest forms of content based systems require the following sources of data (these requirements can increase based on the complexity of the system you’re trying to build):

### Item level data source

You need a strong source of data associated to the attributes of the item. For our scenario, we have things like book price, num_pages, published_year, etc. The more information you know regarding the item, the more beneficial it will be for your system.

### User level data source 

You need some sort of user feedback based on the item you’re providing recommendations for. This level of feedback can be either implicit or explicit. In our sample data, we’re working with user ratings of books they’ve read. The more user feedback you can track, the more beneficial it will be for your system.

###Implementation
- Import data from generate_data function (function provided above) or download the CSV from here
- Normalize book_price, book_ratings, num_pages
- One hot encode publish_year, book_genre, text_lang
- Given a book_id input, calculate the cosine similarity and return top n books similar to the input

```python
import pandas as pd
import numpy as np
from numpy import dot
from numpy.linalg import norm 

def normalize(data):
    '''
    This function will normalize the input data to be between 0 and 1
    
    params:
        data (List) : The list of values you want to normalize
    
    returns:
        The input data normalized between 0 and 1
    '''
    min_val = min(data)
    if min_val < 0:
        data = [x + abs(min_val) for x in data]
    max_val = max(data)
    return [x/max_val for x in data]

def ohe(df, enc_col):
    '''
    This function will one hot encode the specified column and add it back
    onto the input dataframe
    
    params:
        df (DataFrame) : The dataframe you wish for the results to be appended to
        enc_col (String) : The column you want to OHE
    
    returns:
        The OHE columns added onto the input dataframe
    '''
    
    ohe_df = pd.get_dummies(df[enc_col])
    ohe_df.reset_index(drop = True, inplace = True)
    return pd.concat([df, ohe_df], axis = 1)

class CBRecommend():
    def __init__(self, df):
        self.df = df
        
    def cosine_sim(self, v1,v2):
        '''
        This function will calculate the cosine similarity between two vectors
        '''
        return sum(dot(v1,v2)/(norm(v1)*norm(v2)))
    
    def recommend(self, book_id, n_rec):
        """
        df (dataframe): The dataframe
        song_id (string): Representing the song name
        n_rec (int): amount of rec user wants
        """
        
        # calculate similarity of input book_id vector w.r.t all other vectors
        inputVec = self.df.loc[book_id].values
        self.df['sim']= self.df.apply(lambda x: self.cosine_sim(inputVec, x.values), axis=1)

        # returns top n user specified books
        return self.df.nlargest(columns='sim',n=n_rec)

if __name__ == '__main__':
    # constants
    PATH = '../data/data.csv'

    # import data
    df = pd.read_csv(PATH)

    # normalize the num_pages, ratings, price columns
    df['num_pages_norm'] = normalize(df['num_pages'].values)
    df['book_rating_norm'] = normalize(df['book_rating'].values)
    df['book_price_norm'] = normalize(df['book_price'].values)
    
    # OHE on publish_year and genre
    df = ohe(df = df, enc_col = 'publish_year')
    df = ohe(df = df, enc_col = 'book_genre')
    df = ohe(df = df, enc_col = 'text_lang')
    
    # drop redundant columns
    cols = ['publish_year', 'book_genre', 'num_pages', 'book_rating', 'book_price', 'text_lang']
    df.drop(columns = cols, inplace = True)
    df.set_index('book_id', inplace = True)
    
    # ran on a sample as an example
    t = df.copy()
    cbr = CBRecommend(df = t)
    print(cbr.recommend(book_id = t.index[0], n_rec = 5))
```

## Hybrid Recommendation System

## Conclusions

## References 

