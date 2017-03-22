---
layout: post
title: "SQL operations in pandas"
description: ""
category: 
tags: ['pandas', 'python']
---
{% include JB/setup %}


This blog is meant to provide examples to perform query using Pandas like what I usually do in SQL. I have used pandas for a while but I have not tried to dig deeper so I got stuck to some simple SQL-like query when I was using methods of dataframe. More specifically when I am using a groupby object. Therefore I decided to sit down and follow some tutorial and play with Pandas in order to be familiar with dataframe manipulations. In this post I am using MovieLens dataset [here](https://github.com/nickccm1122/data-science-coursework/tree/master/data/ml-100k). Following are some tutorial I went through

- [Intro to pandas data structure](http://www.gregreda.com/2013/10/26/intro-to-pandas-data-structures/)
- [Pandas cookbook](https://github.com/jvns/pandas-cookbook)
- [Pandas official doc](http://pandas.pydata.org/pandas-docs/version/0.18.0/comparison_with_sql.html)

## Read the data


```python
import pandas as pd
import numpy as np
import warnings
warnings.filterwarnings('ignore')
```


```python
dataset_path = "../data/ml-100k/"

u_cols = ['user_id', 'age', 'sex', 'occupation', 'zip_code']
users = pd.read_csv(dataset_path+'u.user', sep='|', names=u_cols)

r_cols = ['user_id', 'movie_id', 'rating', 'unix_timestamp']
ratings = pd.read_csv(dataset_path+'u.data', sep='\t', names=r_cols)

g_cols = ['genre', 'genre_id']
genres = pd.read_csv(dataset_path+'u.genre', sep='|', names=g_cols)

m_cols = ['movie_id', 'title', 'release_date', 'video_release_date', 'imdb_url'] + genres.genre.values.tolist()
movies = pd.read_csv(dataset_path+'u.item', sep='|', names=m_cols)
```

## Joining the table

We need to join two tables together to perform queries with more information we desire. we can do that using dataframe's merge method. By default the merge function will perform a inner join, it is no surprise that more options we can perform like, left join, right join, outer join.  If we want to perform a left join to users and ratings table, we may use:

```
pd.merge(users, ratings, on='user_id', how='left')
```

As I have decided the coloum names myself so we don't need to specify the key to be joined. let say we want to join the users, rating, movies together.


```python
full_records = ratings.merge(users).merge(movies)
full_records.head(3)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>movie_id</th>
      <th>rating</th>
      <th>unix_timestamp</th>
      <th>age</th>
      <th>sex</th>
      <th>occupation</th>
      <th>zip_code</th>
      <th>title</th>
      <th>release_date</th>
      <th>...</th>
      <th>Fantasy</th>
      <th>Film-Noir</th>
      <th>Horror</th>
      <th>Musical</th>
      <th>Mystery</th>
      <th>Romance</th>
      <th>Sci-Fi</th>
      <th>Thriller</th>
      <th>War</th>
      <th>Western</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>196</td>
      <td>242</td>
      <td>3</td>
      <td>881250949</td>
      <td>49</td>
      <td>M</td>
      <td>writer</td>
      <td>55105</td>
      <td>Kolya (1996)</td>
      <td>24-Jan-1997</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>305</td>
      <td>242</td>
      <td>5</td>
      <td>886307828</td>
      <td>23</td>
      <td>M</td>
      <td>programmer</td>
      <td>94086</td>
      <td>Kolya (1996)</td>
      <td>24-Jan-1997</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6</td>
      <td>242</td>
      <td>4</td>
      <td>883268170</td>
      <td>42</td>
      <td>M</td>
      <td>executive</td>
      <td>98101</td>
      <td>Kolya (1996)</td>
      <td>24-Jan-1997</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>3 rows × 31 columns</p>
</div>



As we are not going to use that much columns, we may constrcut a dataframe with less columns.


```python
selected_cols = np.arange(12)
reduced_records = full_records[selected_cols]
reduced_records.head(3)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>movie_id</th>
      <th>rating</th>
      <th>unix_timestamp</th>
      <th>age</th>
      <th>sex</th>
      <th>occupation</th>
      <th>zip_code</th>
      <th>title</th>
      <th>release_date</th>
      <th>video_release_date</th>
      <th>imdb_url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>196</td>
      <td>242</td>
      <td>3</td>
      <td>881250949</td>
      <td>49</td>
      <td>M</td>
      <td>writer</td>
      <td>55105</td>
      <td>Kolya (1996)</td>
      <td>24-Jan-1997</td>
      <td>NaN</td>
      <td>http://us.imdb.com/M/title-exact?Kolya%20(1996)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>305</td>
      <td>242</td>
      <td>5</td>
      <td>886307828</td>
      <td>23</td>
      <td>M</td>
      <td>programmer</td>
      <td>94086</td>
      <td>Kolya (1996)</td>
      <td>24-Jan-1997</td>
      <td>NaN</td>
      <td>http://us.imdb.com/M/title-exact?Kolya%20(1996)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6</td>
      <td>242</td>
      <td>4</td>
      <td>883268170</td>
      <td>42</td>
      <td>M</td>
      <td>executive</td>
      <td>98101</td>
      <td>Kolya (1996)</td>
      <td>24-Jan-1997</td>
      <td>NaN</td>
      <td>http://us.imdb.com/M/title-exact?Kolya%20(1996)</td>
    </tr>
  </tbody>
</table>
</div>



## Groupby

### Which 10 movies got the most ratings?


```python
by_title = reduced_records.groupby('title')
by_title.size().sort_values(ascending=False).head(10)
```




    title
    Star Wars (1977)                 583
    Contact (1997)                   509
    Fargo (1996)                     508
    Return of the Jedi (1983)        507
    Liar Liar (1997)                 485
    English Patient, The (1996)      481
    Scream (1996)                    478
    Toy Story (1995)                 452
    Air Force One (1997)             431
    Independence Day (ID4) (1996)    429
    dtype: int64



### Which is the top ten rated movies?

The query I used here comdine few concepts in order to get the result I want. Let's break it down.

I used **`by_title.agg`** to find the size and the mean of rating of each movies. **`np.size`** and **`np.mean`** are Numpy funtions that I want to pass and get the result from the rating column.

```
top_rated = by_title.agg({
        'rating': [np.size, np.mean]
    })
```

As there are many moives had only gotten few ratings so I would like to exclude movies with less than 50 ratings so that the sample size could be large enough. **`top_rated.rating['size'] > 50`** actually returns a new array of only True/False values and the consept here is to make a logical operation between **`top_rated`** and **`top_rated.rating['size'] > 50`**. The **`top_rated[top_rated.rating['size'] > 50]`** simply return a new dataframe object with size>50. And then we sort the values by the mean


```python
top_rated = by_title.agg({
        'rating': [np.size, np.mean]
    })
top_rated[top_rated.rating['size'] > 50].sort_values(by=('rating', 'mean'), ascending=False).head(10)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="2" halign="left">rating</th>
    </tr>
    <tr>
      <th></th>
      <th>size</th>
      <th>mean</th>
    </tr>
    <tr>
      <th>title</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Close Shave, A (1995)</th>
      <td>112</td>
      <td>4.491071</td>
    </tr>
    <tr>
      <th>Schindler's List (1993)</th>
      <td>298</td>
      <td>4.466443</td>
    </tr>
    <tr>
      <th>Wrong Trousers, The (1993)</th>
      <td>118</td>
      <td>4.466102</td>
    </tr>
    <tr>
      <th>Casablanca (1942)</th>
      <td>243</td>
      <td>4.456790</td>
    </tr>
    <tr>
      <th>Wallace &amp; Gromit: The Best of Aardman Animation (1996)</th>
      <td>67</td>
      <td>4.447761</td>
    </tr>
    <tr>
      <th>Shawshank Redemption, The (1994)</th>
      <td>283</td>
      <td>4.445230</td>
    </tr>
    <tr>
      <th>Rear Window (1954)</th>
      <td>209</td>
      <td>4.387560</td>
    </tr>
    <tr>
      <th>Usual Suspects, The (1995)</th>
      <td>267</td>
      <td>4.385768</td>
    </tr>
    <tr>
      <th>Star Wars (1977)</th>
      <td>583</td>
      <td>4.358491</td>
    </tr>
    <tr>
      <th>12 Angry Men (1957)</th>
      <td>125</td>
      <td>4.344000</td>
    </tr>
  </tbody>
</table>
</div>



### How Star War was rated by each age group?


```python
age_group_labels = ['0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79']
age_group = pd.cut(reduced_records['age'], bins=range(0, 81, 10), right=False, labels=age_group_labels)
reduced_records['age_group'] = age_group
```


```python
star_war_by_age = reduced_records[reduced_records.title == 'Star Wars (1977)'].groupby('age_group')
star_war_by_age.agg({
        'rating': [np.size, np.mean]
    })
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="2" halign="left">rating</th>
    </tr>
    <tr>
      <th></th>
      <th>size</th>
      <th>mean</th>
    </tr>
    <tr>
      <th>age_group</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0-9</th>
      <td>1</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>10-19</th>
      <td>46</td>
      <td>4.630435</td>
    </tr>
    <tr>
      <th>20-29</th>
      <td>230</td>
      <td>4.413043</td>
    </tr>
    <tr>
      <th>30-39</th>
      <td>157</td>
      <td>4.305732</td>
    </tr>
    <tr>
      <th>40-49</th>
      <td>90</td>
      <td>4.166667</td>
    </tr>
    <tr>
      <th>50-59</th>
      <td>49</td>
      <td>4.469388</td>
    </tr>
    <tr>
      <th>60-69</th>
      <td>9</td>
      <td>4.000000</td>
    </tr>
    <tr>
      <th>70-79</th>
      <td>1</td>
      <td>4.000000</td>
    </tr>
  </tbody>
</table>
</div>



### Which 10 movies got the highest rating for age group 20-29?


```python
reduced_records[reduced_records.age_group=='20-29'].groupby('title').agg({
        'rating': [np.size, np.mean]
    })[top_rated.rating['size'] > 50].sort_values(by=('rating', 'mean'), ascending=False).head(10)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="2" halign="left">rating</th>
    </tr>
    <tr>
      <th></th>
      <th>size</th>
      <th>mean</th>
    </tr>
    <tr>
      <th>title</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Shawshank Redemption, The (1994)</th>
      <td>123</td>
      <td>4.585366</td>
    </tr>
    <tr>
      <th>Wallace &amp; Gromit: The Best of Aardman Animation (1996)</th>
      <td>34</td>
      <td>4.558824</td>
    </tr>
    <tr>
      <th>Schindler's List (1993)</th>
      <td>109</td>
      <td>4.467890</td>
    </tr>
    <tr>
      <th>Casablanca (1942)</th>
      <td>76</td>
      <td>4.447368</td>
    </tr>
    <tr>
      <th>Silence of the Lambs, The (1991)</th>
      <td>152</td>
      <td>4.434211</td>
    </tr>
    <tr>
      <th>Usual Suspects, The (1995)</th>
      <td>120</td>
      <td>4.416667</td>
    </tr>
    <tr>
      <th>Star Wars (1977)</th>
      <td>230</td>
      <td>4.413043</td>
    </tr>
    <tr>
      <th>Wrong Trousers, The (1993)</th>
      <td>56</td>
      <td>4.410714</td>
    </tr>
    <tr>
      <th>Close Shave, A (1995)</th>
      <td>51</td>
      <td>4.392157</td>
    </tr>
    <tr>
      <th>Good Will Hunting (1997)</th>
      <td>63</td>
      <td>4.365079</td>
    </tr>
  </tbody>
</table>
</div>



How many movies have you watched in this list??? For me? Star Wars (1977), Good Will Hunting (1997) haha

There is also another way to have the view of mean rating for each age group of movies.


```python
new_view = reduced_records.groupby(['title','age_group']).rating.mean().unstack().fillna(0)
new_view.head(10)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>age_group</th>
      <th>0-9</th>
      <th>10-19</th>
      <th>20-29</th>
      <th>30-39</th>
      <th>40-49</th>
      <th>50-59</th>
      <th>60-69</th>
      <th>70-79</th>
    </tr>
    <tr>
      <th>title</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>'Til There Was You (1997)</th>
      <td>0</td>
      <td>1.000000</td>
      <td>2.285714</td>
      <td>4.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1-900 (1994)</th>
      <td>0</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>3.666667</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>101 Dalmatians (1996)</th>
      <td>0</td>
      <td>3.545455</td>
      <td>2.648649</td>
      <td>3.088235</td>
      <td>2.950000</td>
      <td>2.333333</td>
      <td>2.000000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>12 Angry Men (1957)</th>
      <td>0</td>
      <td>4.500000</td>
      <td>4.230769</td>
      <td>4.382353</td>
      <td>4.500000</td>
      <td>4.235294</td>
      <td>4.400000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>187 (1997)</th>
      <td>0</td>
      <td>3.333333</td>
      <td>3.222222</td>
      <td>2.200000</td>
      <td>2.500000</td>
      <td>3.333333</td>
      <td>0.000000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2 Days in the Valley (1996)</th>
      <td>0</td>
      <td>3.000000</td>
      <td>3.276596</td>
      <td>3.000000</td>
      <td>3.538462</td>
      <td>3.250000</td>
      <td>3.000000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>20,000 Leagues Under the Sea (1954)</th>
      <td>0</td>
      <td>3.250000</td>
      <td>3.217391</td>
      <td>3.578947</td>
      <td>3.615385</td>
      <td>3.750000</td>
      <td>4.000000</td>
      <td>4</td>
    </tr>
    <tr>
      <th>2001: A Space Odyssey (1968)</th>
      <td>5</td>
      <td>4.100000</td>
      <td>3.924731</td>
      <td>3.887500</td>
      <td>4.093023</td>
      <td>4.000000</td>
      <td>4.285714</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3 Ninjas: High Noon At Mega Mountain (1998)</th>
      <td>0</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>39 Steps, The (1935)</th>
      <td>0</td>
      <td>4.500000</td>
      <td>3.700000</td>
      <td>3.888889</td>
      <td>4.230769</td>
      <td>4.272727</td>
      <td>4.000000</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>



## Closing

This is a short practice and I hope I would be more confitable to use pandas next time. I followed the tutorials provided at the begining and please feel free to go through all those as they provide more details and usage in Pandas.
