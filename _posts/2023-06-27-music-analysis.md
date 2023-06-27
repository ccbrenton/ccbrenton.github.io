---
title: "Music Analysis"
date: 2019-04-18T15:34:30-04:00
categories:
  - blog
tags:
  - Python
  - Seaborn
  - Matplotlib
  - Pandas
  - NumPy
classes: wide
---

Perfect Party Playlist


```python
import numpy as np
import pandas as pd
import seaborn as sns
from seaborn_qqplot import pplot
import matplotlib.pyplot as plt
import scipy.stats as sci
import os
import spotipy
import psycopg2 as pg2
from spotipy.oauth2 import SpotifyClientCredentials
from spotipy.oauth2 import SpotifyOAuth
```


```python
#This sets the environmental variables during this session
os.environ['SPOTIPY_CLIENT_ID'] = '###'
os.environ['SPOTIPY_CLIENT_SECRET'] = '###'
os.environ['SPOTIPY_REDIRECT_URI'] = '###'

#We create our Spotify object
sp = spotipy.Spotify(auth_manager=SpotifyOAuth(client_id='###',
                                               client_secret='###',
                                               redirect_uri='###',
                                               scope='user-library-read'))
```

Let's find the total number of songs in our playlist. We'll use that as we increment through the data.


```python
total_tracks = sp.playlist_tracks('https://open.spotify.com/playlist/4I8DT9gh83aBpiJRSEZ9Jb?si=d5aa22b7400b49d9')['total']
total_tracks
```




    730



We will increment through the playlist by determining the total number of songs and appending 100 songs at a time. We will then adjust our total_tracks until we are completely through the playlist.


```python
remaining_songs = total_tracks
offset=0
limit=100
saved_tracks = []

while True:
    
    tracks = sp.playlist_tracks('https://open.spotify.com/playlist/4I8DT9gh83aBpiJRSEZ9Jb?si=d5aa22b7400b49d9',offset=offset,limit=limit)['items']

    for track in tracks:
        
        #pulls the id, song name, album release date, first artist, first artist ID, (artist genres?), and popularity
        saved_tracks.append((track['track']['id'],
                             track['track']['name'],
                             track['track']['album']['release_date'], 
                             track['track']['artists'][0]['name'], 
                             track['track']['artists'][0]['id'], 
                             track['track']['popularity']))
        
    remaining_songs-=100
   
    if remaining_songs <=0:
        break
    else:
        offset+=limit

df = pd.DataFrame(data=saved_tracks,columns=['track_ID','name','release_date','artist','artist_ID','popularity'])
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_ID</th>
      <th>name</th>
      <th>release_date</th>
      <th>artist</th>
      <th>artist_ID</th>
      <th>popularity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4QNpBfC0zvjKqPJcyqBy9W</td>
      <td>Give Me Everything (feat. Ne-Yo, Afrojack &amp; Na...</td>
      <td>2011-06-17</td>
      <td>Pitbull</td>
      <td>0TnOYISbd1XYRBk9myaseg</td>
      <td>86</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2bJvI42r8EF3wxjOuDav4r</td>
      <td>Time of Our Lives</td>
      <td>2014-11-21</td>
      <td>Pitbull</td>
      <td>0TnOYISbd1XYRBk9myaseg</td>
      <td>84</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4kWO6O1BUXcZmaxitpVUwp</td>
      <td>Jackie Chan</td>
      <td>2018-05-18</td>
      <td>Tiësto</td>
      <td>2o5jDhtHVPhrJdv3cEQ99Z</td>
      <td>74</td>
    </tr>
    <tr>
      <th>3</th>
      <td>07nH4ifBxUB4lZcsf44Brn</td>
      <td>Blame (feat. John Newman)</td>
      <td>2014-10-31</td>
      <td>Calvin Harris</td>
      <td>7CajNmpbOovFoOoasH2HaY</td>
      <td>81</td>
    </tr>
    <tr>
      <th>4</th>
      <td>24LS4lQShWyixJ0ZrJXfJ5</td>
      <td>Sweet Nothing (feat. Florence Welch)</td>
      <td>2012-10-29</td>
      <td>Calvin Harris</td>
      <td>7CajNmpbOovFoOoasH2HaY</td>
      <td>77</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>725</th>
      <td>0JbSghVDghtFEurrSO8JrC</td>
      <td>Country Girl (Shake It For Me)</td>
      <td>2011-01-01</td>
      <td>Luke Bryan</td>
      <td>0BvkDsjIUla7X0k6CSWh1I</td>
      <td>81</td>
    </tr>
    <tr>
      <th>726</th>
      <td>57DJaoHdeeRrg7MWthNnee</td>
      <td>Body Back (feat. Maia Wright)</td>
      <td>2019-10-24</td>
      <td>Gryffin</td>
      <td>2ZRQcIgzPCVaT9XKhXZIzh</td>
      <td>62</td>
    </tr>
    <tr>
      <th>727</th>
      <td>0zKbDrEXKpnExhGQRe9dxt</td>
      <td>Lay Low</td>
      <td>2023-01-06</td>
      <td>Tiësto</td>
      <td>2o5jDhtHVPhrJdv3cEQ99Z</td>
      <td>87</td>
    </tr>
    <tr>
      <th>728</th>
      <td>3g5OlVimHO0rK6qmRiwokX</td>
      <td>Glad U Came</td>
      <td>2023-04-27</td>
      <td>Jason Derulo</td>
      <td>07YZf4WDAMNwqr4jfgOZ8y</td>
      <td>73</td>
    </tr>
    <tr>
      <th>729</th>
      <td>0qXGuBm0tmBLjC7InLM3EK</td>
      <td>Don't Say Love</td>
      <td>2023-06-16</td>
      <td>Leigh-Anne</td>
      <td>79QUtAVxGAAoiWNlqBz9iy</td>
      <td>73</td>
    </tr>
  </tbody>
</table>
<p>730 rows × 6 columns</p>
</div>



We now have a dataframe with our party playlist songs! Let's use this opportunity to make some quick observations of our data. Firstly, we'll need to make sure we have the data in the format we need. If we look at the release_date column, it's in yyyy/mm/dd format. We are interested in identifying the songs by year, so let's adjust that.

We'll start by defining a function to pull the year from the yyyy/mm/dd format.


```python
def release_year(date):
    return int(str(date)[:4])
```

Now we create a new column using the apply() function on the current release_date column. Once this is added to our dataframe, we can analyze the release_year distribution.


```python
df['release_year'] = df['release_date'].apply(release_year)
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_ID</th>
      <th>name</th>
      <th>release_date</th>
      <th>artist</th>
      <th>artist_ID</th>
      <th>popularity</th>
      <th>release_year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4QNpBfC0zvjKqPJcyqBy9W</td>
      <td>Give Me Everything (feat. Ne-Yo, Afrojack &amp; Na...</td>
      <td>2011-06-17</td>
      <td>Pitbull</td>
      <td>0TnOYISbd1XYRBk9myaseg</td>
      <td>86</td>
      <td>2011</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2bJvI42r8EF3wxjOuDav4r</td>
      <td>Time of Our Lives</td>
      <td>2014-11-21</td>
      <td>Pitbull</td>
      <td>0TnOYISbd1XYRBk9myaseg</td>
      <td>84</td>
      <td>2014</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4kWO6O1BUXcZmaxitpVUwp</td>
      <td>Jackie Chan</td>
      <td>2018-05-18</td>
      <td>Tiësto</td>
      <td>2o5jDhtHVPhrJdv3cEQ99Z</td>
      <td>74</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>3</th>
      <td>07nH4ifBxUB4lZcsf44Brn</td>
      <td>Blame (feat. John Newman)</td>
      <td>2014-10-31</td>
      <td>Calvin Harris</td>
      <td>7CajNmpbOovFoOoasH2HaY</td>
      <td>81</td>
      <td>2014</td>
    </tr>
    <tr>
      <th>4</th>
      <td>24LS4lQShWyixJ0ZrJXfJ5</td>
      <td>Sweet Nothing (feat. Florence Welch)</td>
      <td>2012-10-29</td>
      <td>Calvin Harris</td>
      <td>7CajNmpbOovFoOoasH2HaY</td>
      <td>77</td>
      <td>2012</td>
    </tr>
  </tbody>
</table>
</div>



Nice! Now we can start our analysis we'll start by quickly describing the data in python.


```python
df.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>popularity</th>
      <th>release_year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>730.000000</td>
      <td>730.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>60.958904</td>
      <td>2013.180822</td>
    </tr>
    <tr>
      <th>std</th>
      <td>27.521553</td>
      <td>10.224512</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>1968.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>54.000000</td>
      <td>2009.250000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>72.000000</td>
      <td>2017.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>80.000000</td>
      <td>2020.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>97.000000</td>
      <td>2023.000000</td>
    </tr>
  </tbody>
</table>
</div>



We can see that our playlist has a mean popularity of around 61% with a standard deviation of 27.5. We also see that our mean year is around 2013 with a standard deviation around 10 years. What about the general distribution of our data? let's plot some histograms to find out.


```python
fig,ax = plt.subplots(1,2,figsize=(14,6))

sns.histplot(data=df,x='popularity',color='royalblue',ax=ax[0])
sns.histplot(data=df,x='release_year',bins=20,color='royalblue',ax=ax[1])

plt.suptitle("Playlist Track Popularity and Year Distribution",fontsize=16)
ax[0].set_title("Track Popularity Distribution")
ax[1].set_title("Track Release Year Distribution")
ax[0].set_xlabel("Popularity")
ax[1].set_xlabel("Year")

fig.subplots_adjust(left=None,
    bottom=None,
    right=None,
    top=None,
    wspace=0.3,
    hspace=0.1,)

plt.show()
```


    
![png](/assets/images/output_15_0.png)
    


quite a large number of songs in the playlist are below a popularity score of 5. It's important to consider that popularity scores are derived from a spotify algorithm that considers the number of times a song is played and how recent those plays are. It's also important to consider that every song on spotify is ranked independentaly, so duplicates of the same song can have different scores. One version of the song on Spotify could have a signifcantly different score compared to another version (ex: explicit vs non-explicit).


```python
print("The total number of songs that have a popular score of 5 or less: " + str(len(df[df['popularity']<=5])))
```

    The total number of songs that have a popular score of 5 or less: 92


In this case, exactly 92 songs in the playlist are ranked 5 or less on the popularity score. For this reason, popularity may not be the single best representation of which songs to choose for our playlist.

We see an exponential increase of songs as the year increases to the current date. This is ideal as we focus on the most recent songs while still including classic staples from previous generations.

Let's take a deeper dive into the characterstics of the songs within the playlist to learn more about what type of music we want to focus on as we add new songs. To do this, we need to pull detailed data on a variety of attributes that are associated with each song. Doing so many be able to help us identify patterns in the playlist which can help us predict which songs may fit in the future. Let's start by pulling the attributes for each song and adding them to a new table.


```python
remaining_songs = total_tracks
track_ids = list(df['track_ID'])
track_subset = []
features_list = []
offset = 0
limit = 100

while True:
    
    track_subset = track_ids[offset:offset+limit]
    
    audio_features_results = sp.audio_features(track_subset)
    
    for index,track in enumerate(audio_features_results):
        
        try:
            feature_tuple = (track['id'],
                             track['acousticness'],
                             track['danceability'],
                             track['duration_ms'],
                             track['energy'],
                             track['instrumentalness'],
                             track['key'],
                             track['liveness'],
                             track['loudness'],
                             track['mode'],
                             track['speechiness'],
                             track['tempo'],
                             track['time_signature'],
                             track['valence'])
            
            features_list.append(feature_tuple)
            
        except TypeError:
            feature_tuple = (track_subset[index],
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT")
            
            features_list.append(feature_tuple)
            

    remaining_songs-=limit
    
    if remaining_songs <=0:
        break
    else:
        offset+=limit

features_df = pd.DataFrame(data=features_list,columns=['track_ID',
                                                       'acousticness',
                                                       'danceability',
                                                       'duration_ms',
                                                       'energy',
                                                       'instrumentalness',
                                                       'key',
                                                       'liveness',
                                                       'loudness',
                                                       'mode',
                                                       'speechiness',
                                                       'tempo',
                                                       'time_signature',
                                                       'valence'])

features_df.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>acousticness</th>
      <th>danceability</th>
      <th>duration_ms</th>
      <th>energy</th>
      <th>instrumentalness</th>
      <th>key</th>
      <th>liveness</th>
      <th>loudness</th>
      <th>mode</th>
      <th>speechiness</th>
      <th>tempo</th>
      <th>time_signature</th>
      <th>valence</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>730.000000</td>
      <td>730.000000</td>
      <td>730.000000</td>
      <td>730.000000</td>
      <td>730.000000</td>
      <td>730.000000</td>
      <td>730.000000</td>
      <td>730.000000</td>
      <td>730.000000</td>
      <td>730.000000</td>
      <td>730.000000</td>
      <td>730.000000</td>
      <td>730.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>0.107897</td>
      <td>0.688834</td>
      <td>210495.646575</td>
      <td>0.731086</td>
      <td>0.007994</td>
      <td>5.260274</td>
      <td>0.185877</td>
      <td>-5.511659</td>
      <td>0.671233</td>
      <td>0.087442</td>
      <td>120.076452</td>
      <td>3.980822</td>
      <td>0.588999</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.127984</td>
      <td>0.122045</td>
      <td>43512.586492</td>
      <td>0.133454</td>
      <td>0.056376</td>
      <td>3.623681</td>
      <td>0.142204</td>
      <td>1.853605</td>
      <td>0.470087</td>
      <td>0.078224</td>
      <td>24.658935</td>
      <td>0.233455</td>
      <td>0.207757</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000018</td>
      <td>0.276000</td>
      <td>112105.000000</td>
      <td>0.127000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.022600</td>
      <td>-18.064000</td>
      <td>0.000000</td>
      <td>0.025200</td>
      <td>62.538000</td>
      <td>1.000000</td>
      <td>0.057900</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>0.017900</td>
      <td>0.618000</td>
      <td>181543.000000</td>
      <td>0.647000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.090900</td>
      <td>-6.381000</td>
      <td>0.000000</td>
      <td>0.040725</td>
      <td>100.029750</td>
      <td>4.000000</td>
      <td>0.446500</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>0.057800</td>
      <td>0.696000</td>
      <td>206679.000000</td>
      <td>0.741000</td>
      <td>0.000000</td>
      <td>6.000000</td>
      <td>0.127500</td>
      <td>-5.279500</td>
      <td>1.000000</td>
      <td>0.057500</td>
      <td>119.965000</td>
      <td>4.000000</td>
      <td>0.594000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>0.161500</td>
      <td>0.763750</td>
      <td>231823.500000</td>
      <td>0.833000</td>
      <td>0.000032</td>
      <td>8.000000</td>
      <td>0.255000</td>
      <td>-4.305000</td>
      <td>1.000000</td>
      <td>0.101000</td>
      <td>130.997750</td>
      <td>4.000000</td>
      <td>0.754750</td>
    </tr>
    <tr>
      <th>max</th>
      <td>0.699000</td>
      <td>0.967000</td>
      <td>516893.000000</td>
      <td>0.978000</td>
      <td>0.837000</td>
      <td>11.000000</td>
      <td>0.826000</td>
      <td>-0.930000</td>
      <td>1.000000</td>
      <td>0.592000</td>
      <td>202.015000</td>
      <td>5.000000</td>
      <td>0.979000</td>
    </tr>
  </tbody>
</table>
</div>



Many of these audio features are classified on a scale from 0 to 1. We see that on average, the danceability, energy, and valence are higher for this playlist. We also see that on average, acousticness, instrumentalness, liveness, and speechiness are lower. Graphing this out better illustrates the pattern identified. We'll create a new table to create a violin plot by first transforming the current table. 


```python
features_df_transformed = pd.melt(features_df,value_vars=['acousticness',
                                                          'danceability',
                                                          'energy',
                                                          'instrumentalness',
                                                          'liveness',
                                                          'speechiness',
                                                          'valence'])
features_df_transformed
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>variable</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>acousticness</td>
      <td>0.1910</td>
    </tr>
    <tr>
      <th>1</th>
      <td>acousticness</td>
      <td>0.0921</td>
    </tr>
    <tr>
      <th>2</th>
      <td>acousticness</td>
      <td>0.3740</td>
    </tr>
    <tr>
      <th>3</th>
      <td>acousticness</td>
      <td>0.0287</td>
    </tr>
    <tr>
      <th>4</th>
      <td>acousticness</td>
      <td>0.1970</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>5105</th>
      <td>valence</td>
      <td>0.6710</td>
    </tr>
    <tr>
      <th>5106</th>
      <td>valence</td>
      <td>0.4480</td>
    </tr>
    <tr>
      <th>5107</th>
      <td>valence</td>
      <td>0.4200</td>
    </tr>
    <tr>
      <th>5108</th>
      <td>valence</td>
      <td>0.5210</td>
    </tr>
    <tr>
      <th>5109</th>
      <td>valence</td>
      <td>0.8120</td>
    </tr>
  </tbody>
</table>
<p>5110 rows × 2 columns</p>
</div>




```python
#sns.catplot(data=features_df_transformed,x='variable',y='value',kind='violin',scale='count',color='0.8',height=7,aspect=2,inner='quartile')
fig,ax = plt.subplots(1,2,figsize=(14,8))
sns.boxenplot(data=features_df_transformed, 
              y='variable', 
              x='value', 
              palette='plasma',
              ax=ax[1],
              order=['instrumentalness',
                     'speechiness',
                     'acousticness',
                     'liveness',
                     'valence',
                     'danceability',
                     'energy'])

sns.stripplot(data=features_df_transformed,
              y='variable',
              x='value',
              size=6, 
              alpha=0.3, 
              order=['instrumentalness',
                     'speechiness',
                     'acousticness',
                     'liveness',
                     'valence',
                     'danceability',
                     'energy'],
              palette='plasma',
              ax=ax[0])

plt.suptitle("Classification Score Distribution per Track Feature",fontsize=16)
ax[0].set_title("Strip Plot")
ax[1].set_title("Boxen Plot (Letter-Value Plot)")
ax[0].set_ylabel("Track Features")
ax[1].set_ylabel("Track Features")
ax[0].set_xlabel("Classification Score")
ax[1].set_xlabel("Classification Score")

fig.subplots_adjust(left=None,
    bottom=None,
    right=None,
    top=None,
    wspace=0.5,
    hspace=0.1,)

plt.show()
```


    
![png](/assets/images/output_24_0.png)
    


What does do these two graphs tell us about our data? The left figure plots all data points for each track feature using a alpha value (transperancy) of 0.3 to help illustrate the density of overlapping points. The right figure plots boxen plots (or letter-value plots) of the data to give us more information than a traditional box plot by helping us better illustrate the overall distribution. 

Based on our analysis so far we now know:
- Popularity scores matters.
- Songs should have high danceability, energy, and valence.
- Songs should have low instrumentalness, speechiness, acoustics, and liveness.
- Songs should be fairly modern.

How can we use this data to help us decide which songs would be a good or bad fit for our playlist? Ideally, we want to take a mathematical approach to guarantee the best possible accuracy in our predictions. The first observation we want to make in our quest for picking the best mathematical solution is to determine if we are dealing with a normal distribution or not. Normal distributions can help make our lives a lot easier, but unfortunately our boxen plot helps us visualize that our data may not fit a normal distribution too well. Let's double check by utilizing a more specific tool called a QQ plot. While this won't tell us what the distribution is, it can tell us what it's NOT. Let's plot our data on the QQ plot to determine if we have a normal distribution.


```python
pplot(features_df_transformed,y=sci.norm,x='value',hue='variable',kind='qq',height=6,aspect=2,display_kws={"identity":True})

plt.title("QQ Plot")
plt.ylabel("Normal Distribution")
plt.xlabel("Classification Score")
plt.annotate('The closer points follow the x=y line, \n the closer the data is to a normal distribution. ', 
             xy=(1.1, 1.1), 
             xycoords='data',
            xytext=(1, -0.55), 
             bbox=dict(boxstyle="round", fc="w"),
             va="center", 
             ha="center", 
             size=12,
            arrowprops=dict(arrowstyle="->,head_width=0.4,head_length=0.6",facecolor='black',connectionstyle="arc3,rad=0.1",fc="w",relpos=(0.75, 0.)))

plt.show()
```


    
![png](/assets/images/output_26_0.png)
    


What we are looking for with a QQ plot is whether the points closely follow the x=y line for the given distribution, but unfortunately it seems none of our track features are well defined by a normal distribution. We could use this opportunity to determine the best possible distribution model for each individual track feature with tools such as the fitter python library, but for simplicities sake, there is an alternative option we can employ. We can use Chebyshev's inequality.

Chebyshev's inequality (also called Bienaymé–Chebyshev inequality) states that:
For a wide class of probability distributions, no more than a certain fraction of values can be more than a certain distance from the mean. Specifically, no more than 1/k2 of the distribution's values can be k or more standard deviations away from the mean.

```math
Pr(|X-\mu|\geq k \sigma) \leq \frac{1}{k^2}
```

In a normal distribution, we could confidently say that 95% of the data would fit within 2 standard deviations from the mean. Using Chebyshev's inequality, we can conclude that 75% of the data will fit within 2 standard deviations of the mean for a wide range of different distributions. Using these bounds does not give us the same level of accuracy we can expect with a normal distribution, but it does allow us to better identify values in our data which would correlate to a song better fitting our playlist.

Now that we've identified how to pick values in our data that better represent the entire population, we can use this information to help us predict which songs may fit better into our playlist. We will use a range of values 2 standard deviations from the mean for each track feature to help us develop a filter for the type of songs we want. We will upload a new dataframe of over 6,000 songs to identify which would best fit our new criteria.

For our analysis, we will start by calculating the upper and lower bounds of our target values based on 2 standard deviations from the mean.


```python
#['acousticness','danceability','energy','instrumentalness','liveness','speechiness','valence']

pop_min = df['popularity'].mean() - 2*df['popularity'].std()
pop_max = df['popularity'].mean() + 2*df['popularity'].std()
year_min = df['release_year'].mean() - 2*df['release_year'].std()
year_max = df['release_year'].mean() + 2*df['release_year'].std()
acoust_min = features_df['acousticness'].mean() - 2*features_df['acousticness'].std()
acoust_max = features_df['acousticness'].mean() + 2*features_df['acousticness'].std()
dance_min = features_df['danceability'].mean() - 2*features_df['danceability'].std()
dance_max = features_df['danceability'].mean() + 2*features_df['danceability'].std()
en_min = features_df['energy'].mean() - 2*features_df['energy'].std()
en_max = features_df['energy'].mean() + 2*features_df['energy'].std()
instru_min = features_df['instrumentalness'].mean() - 2*features_df['instrumentalness'].std()
instru_max = features_df['instrumentalness'].mean() + 2*features_df['instrumentalness'].std()
live_min = features_df['liveness'].mean() - 2*features_df['liveness'].std()
live_max = features_df['liveness'].mean() + 2*features_df['liveness'].std()
speech_min = features_df['speechiness'].mean() - 2*features_df['speechiness'].std()
speech_max = features_df['speechiness'].mean() + 2*features_df['speechiness'].std()
val_min = features_df['valence'].mean() - 2*features_df['valence'].std()
val_max = features_df['valence'].mean() + 2*features_df['valence'].std()
```

We will now full the full dataset of over 6000 songs into a new dataframe.


```python
remaining_songs = sp.current_user_saved_tracks()['total']
offset=0
limit=50
saved_tracks = []

while True:
    
    spotify_liked_songs = sp.current_user_saved_tracks(limit,offset)
    tracks = spotify_liked_songs['items']
    
    for track in tracks:
        
        saved_tracks.append((track['track']['id'],
                             track['track']['name'],
                             track['track']['album']['release_date'], 
                             track['track']['artists'][0]['name'], 
                             track['track']['artists'][0]['id'], 
                             track['track']['popularity']))
        
    remaining_songs-=limit
   
    if remaining_songs <=0:
        break
    else:
        offset+=limit
        
liked_songs_df = pd.DataFrame(data=saved_tracks,columns=['track_ID','name','release_date','artist','artist_ID','popularity'])
liked_songs_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_ID</th>
      <th>name</th>
      <th>release_date</th>
      <th>artist</th>
      <th>artist_ID</th>
      <th>popularity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4W7bfpXBXrH79dnWyJMkEX</td>
      <td>If Only I (feat. Bebe Rexha)</td>
      <td>2023-06-23</td>
      <td>Loud Luxury</td>
      <td>6t1gpxYbY8OlLA7D2RiikQ</td>
      <td>64</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0H4AI6urtSKuCLuWy8J1UO</td>
      <td>Suburban Blues</td>
      <td>2023-06-02</td>
      <td>The Aces</td>
      <td>2AmfMGi3WZMxqFDHissIAe</td>
      <td>50</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5NMl4DvH391bWzv5GXPUf5</td>
      <td>i need you (in my life)</td>
      <td>2023-06-09</td>
      <td>Jeremy Zucker</td>
      <td>3gIRvgZssIb9aiirIg0nI3</td>
      <td>59</td>
    </tr>
    <tr>
      <th>3</th>
      <td>7tNHLrbkBhU6xh0GL0gTtQ</td>
      <td>Tuesday</td>
      <td>2023-06-23</td>
      <td>Nic D</td>
      <td>1XlLhtgJjC4ROQZilBZAvw</td>
      <td>58</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5DU3wahsCBJ6kkyDBYpIRN</td>
      <td>I Know (feat. Tenille Townes &amp; Bryce Vine)</td>
      <td>2023-06-23</td>
      <td>Train</td>
      <td>3FUY2gzHeIiaesXtOAdB7A</td>
      <td>53</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>6106</th>
      <td>6FxMzrdk9dmjUMLdHeL5tl</td>
      <td>Miss Atomic Bomb</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>42</td>
    </tr>
    <tr>
      <th>6107</th>
      <td>6HOcAwQAAKaj1GHntfmSoI</td>
      <td>When You Were Young - Calvin Harris Remix</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>45</td>
    </tr>
    <tr>
      <th>6108</th>
      <td>6aKWBLAFHXHUjsJJri2ota</td>
      <td>When You Were Young</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>41</td>
    </tr>
    <tr>
      <th>6109</th>
      <td>75CgwX1tMoPfE8C4ZR14D8</td>
      <td>For Reasons Unknown</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>43</td>
    </tr>
    <tr>
      <th>6110</th>
      <td>6HFbq7cewJ7rPiffV0ciil</td>
      <td>A Sky Full of Stars</td>
      <td>2014-05-02</td>
      <td>Coldplay</td>
      <td>4gzpq5DPGxSnKTe4SA8HAU</td>
      <td>60</td>
    </tr>
  </tbody>
</table>
<p>6111 rows × 6 columns</p>
</div>



We'll now pull the track features for each song in the the new dataframe we just created.


```python
remaining_songs = sp.current_user_saved_tracks()['total']
track_ids = list(liked_songs_df['track_ID'])
track_subset = []
features_list = []
offset = 0
limit = 50

while True:
    
    track_subset = track_ids[offset:offset+limit]
    
    audio_features_results = sp.audio_features(track_subset)
    
    for index,track in enumerate(audio_features_results):
        
        try:
            feature_tuple = (track['id'],
                             track['acousticness'],
                             track['danceability'],
                             track['duration_ms'],
                             track['energy'],
                             track['instrumentalness'],
                             track['key'],
                             track['liveness'],
                             track['loudness'],
                             track['mode'],
                             track['speechiness'],
                             track['tempo'],
                             track['time_signature'],
                             track['valence'])
            
            features_list.append(feature_tuple)
            
        except TypeError:
            feature_tuple = (track_subset[index],
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT",
                             "DEFAULT")
            
            features_list.append(feature_tuple)
            

    remaining_songs-=limit
    
    if remaining_songs <=0:
        break
    else:
        offset+=limit

liked_songs_features_df = pd.DataFrame(data=features_list,columns=['track_ID',
                                                                   'acousticness',
                                                                   'danceability',
                                                                   'duration_ms',
                                                                   'energy',
                                                                   'instrumentalness',
                                                                   'key',
                                                                   'liveness',
                                                                   'loudness',
                                                                   'mode',
                                                                   'speechiness',
                                                                   'tempo',
                                                                   'time_signature',
                                                                   'valence'])
```

We need to merge our two new dataframes in order to have access to all the data we need before filtering. In this case, we will conduct an inner join where we have equivalent track ID's located in each dataframe.


```python
merged_df = pd.merge(liked_songs_df,liked_songs_features_df,how='inner',on='track_ID')
merged_df['release_year'] = merged_df['release_date'].apply(release_year)
merged_df.set_index('track_ID')
merged_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_ID</th>
      <th>name</th>
      <th>release_date</th>
      <th>artist</th>
      <th>artist_ID</th>
      <th>popularity</th>
      <th>acousticness</th>
      <th>danceability</th>
      <th>duration_ms</th>
      <th>energy</th>
      <th>instrumentalness</th>
      <th>key</th>
      <th>liveness</th>
      <th>loudness</th>
      <th>mode</th>
      <th>speechiness</th>
      <th>tempo</th>
      <th>time_signature</th>
      <th>valence</th>
      <th>release_year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4W7bfpXBXrH79dnWyJMkEX</td>
      <td>If Only I (feat. Bebe Rexha)</td>
      <td>2023-06-23</td>
      <td>Loud Luxury</td>
      <td>6t1gpxYbY8OlLA7D2RiikQ</td>
      <td>64</td>
      <td>0.161</td>
      <td>0.693</td>
      <td>142756</td>
      <td>0.73</td>
      <td>0.000041</td>
      <td>0</td>
      <td>0.109</td>
      <td>-5.06</td>
      <td>0</td>
      <td>0.0356</td>
      <td>120.018</td>
      <td>4</td>
      <td>0.693</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0H4AI6urtSKuCLuWy8J1UO</td>
      <td>Suburban Blues</td>
      <td>2023-06-02</td>
      <td>The Aces</td>
      <td>2AmfMGi3WZMxqFDHissIAe</td>
      <td>50</td>
      <td>0.000172</td>
      <td>0.403</td>
      <td>180096</td>
      <td>0.923</td>
      <td>0.00101</td>
      <td>9</td>
      <td>0.406</td>
      <td>-5.159</td>
      <td>1</td>
      <td>0.059</td>
      <td>76.738</td>
      <td>4</td>
      <td>0.403</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5NMl4DvH391bWzv5GXPUf5</td>
      <td>i need you (in my life)</td>
      <td>2023-06-09</td>
      <td>Jeremy Zucker</td>
      <td>3gIRvgZssIb9aiirIg0nI3</td>
      <td>59</td>
      <td>0.101</td>
      <td>0.704</td>
      <td>189107</td>
      <td>0.521</td>
      <td>0.000062</td>
      <td>8</td>
      <td>0.116</td>
      <td>-7.65</td>
      <td>1</td>
      <td>0.0307</td>
      <td>130.015</td>
      <td>4</td>
      <td>0.625</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>3</th>
      <td>7tNHLrbkBhU6xh0GL0gTtQ</td>
      <td>Tuesday</td>
      <td>2023-06-23</td>
      <td>Nic D</td>
      <td>1XlLhtgJjC4ROQZilBZAvw</td>
      <td>58</td>
      <td>0.103</td>
      <td>0.594</td>
      <td>128464</td>
      <td>0.77</td>
      <td>0.000004</td>
      <td>10</td>
      <td>0.25</td>
      <td>-5.132</td>
      <td>1</td>
      <td>0.0513</td>
      <td>168.124</td>
      <td>4</td>
      <td>0.729</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5DU3wahsCBJ6kkyDBYpIRN</td>
      <td>I Know (feat. Tenille Townes &amp; Bryce Vine)</td>
      <td>2023-06-23</td>
      <td>Train</td>
      <td>3FUY2gzHeIiaesXtOAdB7A</td>
      <td>53</td>
      <td>0.0902</td>
      <td>0.682</td>
      <td>183578</td>
      <td>0.789</td>
      <td>0</td>
      <td>1</td>
      <td>0.379</td>
      <td>-5.801</td>
      <td>1</td>
      <td>0.0411</td>
      <td>151.974</td>
      <td>4</td>
      <td>0.842</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>6106</th>
      <td>6FxMzrdk9dmjUMLdHeL5tl</td>
      <td>Miss Atomic Bomb</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>42</td>
      <td>0.0234</td>
      <td>0.56</td>
      <td>294760</td>
      <td>0.722</td>
      <td>0.000027</td>
      <td>10</td>
      <td>0.257</td>
      <td>-7.083</td>
      <td>1</td>
      <td>0.0315</td>
      <td>132.971</td>
      <td>4</td>
      <td>0.348</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>6107</th>
      <td>6HOcAwQAAKaj1GHntfmSoI</td>
      <td>When You Were Young - Calvin Harris Remix</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>45</td>
      <td>0.000162</td>
      <td>0.586</td>
      <td>372800</td>
      <td>0.918</td>
      <td>0.0543</td>
      <td>11</td>
      <td>0.199</td>
      <td>-4.399</td>
      <td>1</td>
      <td>0.0942</td>
      <td>130.013</td>
      <td>4</td>
      <td>0.459</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>6108</th>
      <td>6aKWBLAFHXHUjsJJri2ota</td>
      <td>When You Were Young</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>41</td>
      <td>0.000335</td>
      <td>0.441</td>
      <td>220413</td>
      <td>0.976</td>
      <td>0.0475</td>
      <td>8</td>
      <td>0.298</td>
      <td>-3.09</td>
      <td>0</td>
      <td>0.147</td>
      <td>130.411</td>
      <td>4</td>
      <td>0.248</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>6109</th>
      <td>75CgwX1tMoPfE8C4ZR14D8</td>
      <td>For Reasons Unknown</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>43</td>
      <td>0.000433</td>
      <td>0.496</td>
      <td>213280</td>
      <td>0.889</td>
      <td>0.0415</td>
      <td>3</td>
      <td>0.122</td>
      <td>-3.855</td>
      <td>1</td>
      <td>0.0372</td>
      <td>140.438</td>
      <td>4</td>
      <td>0.519</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>6110</th>
      <td>6HFbq7cewJ7rPiffV0ciil</td>
      <td>A Sky Full of Stars</td>
      <td>2014-05-02</td>
      <td>Coldplay</td>
      <td>4gzpq5DPGxSnKTe4SA8HAU</td>
      <td>60</td>
      <td>0.00617</td>
      <td>0.545</td>
      <td>267867</td>
      <td>0.675</td>
      <td>0.00197</td>
      <td>6</td>
      <td>0.209</td>
      <td>-6.474</td>
      <td>1</td>
      <td>0.0279</td>
      <td>124.97</td>
      <td>4</td>
      <td>0.162</td>
      <td>2014</td>
    </tr>
  </tbody>
</table>
<p>6111 rows × 20 columns</p>
</div>



Before we continue, we need to clean our data to make sure that there are no values present that we do not expect. We previously set up our script to pull all songs available. If there were songs that did not have track feature data available, we would fill in the data with 'DEFAULT'. We will look to find if any of these values exist and pull them from the data.



```python
index = []
empty_tracks_df = merged_df[merged_df['acousticness'] == 'DEFAULT']

for val in empty_tracks_df.index:
    merged_df = merged_df.drop(val,axis=0)

merged_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_ID</th>
      <th>name</th>
      <th>release_date</th>
      <th>artist</th>
      <th>artist_ID</th>
      <th>popularity</th>
      <th>acousticness</th>
      <th>danceability</th>
      <th>duration_ms</th>
      <th>energy</th>
      <th>instrumentalness</th>
      <th>key</th>
      <th>liveness</th>
      <th>loudness</th>
      <th>mode</th>
      <th>speechiness</th>
      <th>tempo</th>
      <th>time_signature</th>
      <th>valence</th>
      <th>release_year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4W7bfpXBXrH79dnWyJMkEX</td>
      <td>If Only I (feat. Bebe Rexha)</td>
      <td>2023-06-23</td>
      <td>Loud Luxury</td>
      <td>6t1gpxYbY8OlLA7D2RiikQ</td>
      <td>64</td>
      <td>0.161</td>
      <td>0.693</td>
      <td>142756</td>
      <td>0.73</td>
      <td>0.000041</td>
      <td>0</td>
      <td>0.109</td>
      <td>-5.06</td>
      <td>0</td>
      <td>0.0356</td>
      <td>120.018</td>
      <td>4</td>
      <td>0.693</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0H4AI6urtSKuCLuWy8J1UO</td>
      <td>Suburban Blues</td>
      <td>2023-06-02</td>
      <td>The Aces</td>
      <td>2AmfMGi3WZMxqFDHissIAe</td>
      <td>50</td>
      <td>0.000172</td>
      <td>0.403</td>
      <td>180096</td>
      <td>0.923</td>
      <td>0.00101</td>
      <td>9</td>
      <td>0.406</td>
      <td>-5.159</td>
      <td>1</td>
      <td>0.059</td>
      <td>76.738</td>
      <td>4</td>
      <td>0.403</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5NMl4DvH391bWzv5GXPUf5</td>
      <td>i need you (in my life)</td>
      <td>2023-06-09</td>
      <td>Jeremy Zucker</td>
      <td>3gIRvgZssIb9aiirIg0nI3</td>
      <td>59</td>
      <td>0.101</td>
      <td>0.704</td>
      <td>189107</td>
      <td>0.521</td>
      <td>0.000062</td>
      <td>8</td>
      <td>0.116</td>
      <td>-7.65</td>
      <td>1</td>
      <td>0.0307</td>
      <td>130.015</td>
      <td>4</td>
      <td>0.625</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>3</th>
      <td>7tNHLrbkBhU6xh0GL0gTtQ</td>
      <td>Tuesday</td>
      <td>2023-06-23</td>
      <td>Nic D</td>
      <td>1XlLhtgJjC4ROQZilBZAvw</td>
      <td>58</td>
      <td>0.103</td>
      <td>0.594</td>
      <td>128464</td>
      <td>0.77</td>
      <td>0.000004</td>
      <td>10</td>
      <td>0.25</td>
      <td>-5.132</td>
      <td>1</td>
      <td>0.0513</td>
      <td>168.124</td>
      <td>4</td>
      <td>0.729</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5DU3wahsCBJ6kkyDBYpIRN</td>
      <td>I Know (feat. Tenille Townes &amp; Bryce Vine)</td>
      <td>2023-06-23</td>
      <td>Train</td>
      <td>3FUY2gzHeIiaesXtOAdB7A</td>
      <td>53</td>
      <td>0.0902</td>
      <td>0.682</td>
      <td>183578</td>
      <td>0.789</td>
      <td>0</td>
      <td>1</td>
      <td>0.379</td>
      <td>-5.801</td>
      <td>1</td>
      <td>0.0411</td>
      <td>151.974</td>
      <td>4</td>
      <td>0.842</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>6106</th>
      <td>6FxMzrdk9dmjUMLdHeL5tl</td>
      <td>Miss Atomic Bomb</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>42</td>
      <td>0.0234</td>
      <td>0.56</td>
      <td>294760</td>
      <td>0.722</td>
      <td>0.000027</td>
      <td>10</td>
      <td>0.257</td>
      <td>-7.083</td>
      <td>1</td>
      <td>0.0315</td>
      <td>132.971</td>
      <td>4</td>
      <td>0.348</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>6107</th>
      <td>6HOcAwQAAKaj1GHntfmSoI</td>
      <td>When You Were Young - Calvin Harris Remix</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>45</td>
      <td>0.000162</td>
      <td>0.586</td>
      <td>372800</td>
      <td>0.918</td>
      <td>0.0543</td>
      <td>11</td>
      <td>0.199</td>
      <td>-4.399</td>
      <td>1</td>
      <td>0.0942</td>
      <td>130.013</td>
      <td>4</td>
      <td>0.459</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>6108</th>
      <td>6aKWBLAFHXHUjsJJri2ota</td>
      <td>When You Were Young</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>41</td>
      <td>0.000335</td>
      <td>0.441</td>
      <td>220413</td>
      <td>0.976</td>
      <td>0.0475</td>
      <td>8</td>
      <td>0.298</td>
      <td>-3.09</td>
      <td>0</td>
      <td>0.147</td>
      <td>130.411</td>
      <td>4</td>
      <td>0.248</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>6109</th>
      <td>75CgwX1tMoPfE8C4ZR14D8</td>
      <td>For Reasons Unknown</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>43</td>
      <td>0.000433</td>
      <td>0.496</td>
      <td>213280</td>
      <td>0.889</td>
      <td>0.0415</td>
      <td>3</td>
      <td>0.122</td>
      <td>-3.855</td>
      <td>1</td>
      <td>0.0372</td>
      <td>140.438</td>
      <td>4</td>
      <td>0.519</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>6110</th>
      <td>6HFbq7cewJ7rPiffV0ciil</td>
      <td>A Sky Full of Stars</td>
      <td>2014-05-02</td>
      <td>Coldplay</td>
      <td>4gzpq5DPGxSnKTe4SA8HAU</td>
      <td>60</td>
      <td>0.00617</td>
      <td>0.545</td>
      <td>267867</td>
      <td>0.675</td>
      <td>0.00197</td>
      <td>6</td>
      <td>0.209</td>
      <td>-6.474</td>
      <td>1</td>
      <td>0.0279</td>
      <td>124.97</td>
      <td>4</td>
      <td>0.162</td>
      <td>2014</td>
    </tr>
  </tbody>
</table>
<p>6110 rows × 20 columns</p>
</div>



We will finally update the type of our columns and apply a filter to find tracks that meet our criteria.


```python
#change all columns from objects to numeric values
merged_df['acousticness'] = pd.to_numeric(merged_df['acousticness'])
merged_df['energy'] = pd.to_numeric(merged_df['energy'])
merged_df['danceability'] = pd.to_numeric(merged_df['danceability'])
merged_df['valence'] = pd.to_numeric(merged_df['valence'])
merged_df['liveness'] = pd.to_numeric(merged_df['liveness'])
merged_df['speechiness'] = pd.to_numeric(merged_df['speechiness'])
merged_df['instrumentalness'] = pd.to_numeric(merged_df['instrumentalness'])

#generate a new dataframe based on our filter targets
new_tracks_df = merged_df[(merged_df['popularity']>pop_min) & (merged_df['popularity']<pop_max) &
                  (merged_df['energy']>en_min) & (merged_df['energy']<en_max) &
                  (merged_df['danceability']>dance_min) & (merged_df['danceability']<dance_max) &
                  (merged_df['valence']>val_min) & (merged_df['valence']<val_max) &
                  (merged_df['liveness']>live_min) & (merged_df['liveness']<live_max) &
                  (merged_df['acousticness']>acoust_min) & (merged_df['acousticness']<acoust_max) &
                  (merged_df['speechiness']>speech_min) & (merged_df['speechiness']<speech_max) &
                  (merged_df['instrumentalness']>instru_min) & (merged_df['instrumentalness']<instru_max) &
                  (merged_df['release_year']>year_min) & (merged_df['release_year']<year_max)]

new_tracks_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_ID</th>
      <th>name</th>
      <th>release_date</th>
      <th>artist</th>
      <th>artist_ID</th>
      <th>popularity</th>
      <th>acousticness</th>
      <th>danceability</th>
      <th>duration_ms</th>
      <th>energy</th>
      <th>instrumentalness</th>
      <th>key</th>
      <th>liveness</th>
      <th>loudness</th>
      <th>mode</th>
      <th>speechiness</th>
      <th>tempo</th>
      <th>time_signature</th>
      <th>valence</th>
      <th>release_year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4W7bfpXBXrH79dnWyJMkEX</td>
      <td>If Only I (feat. Bebe Rexha)</td>
      <td>2023-06-23</td>
      <td>Loud Luxury</td>
      <td>6t1gpxYbY8OlLA7D2RiikQ</td>
      <td>64</td>
      <td>0.161000</td>
      <td>0.693</td>
      <td>142756</td>
      <td>0.730</td>
      <td>0.000041</td>
      <td>0</td>
      <td>0.1090</td>
      <td>-5.06</td>
      <td>0</td>
      <td>0.0356</td>
      <td>120.018</td>
      <td>4</td>
      <td>0.693</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5NMl4DvH391bWzv5GXPUf5</td>
      <td>i need you (in my life)</td>
      <td>2023-06-09</td>
      <td>Jeremy Zucker</td>
      <td>3gIRvgZssIb9aiirIg0nI3</td>
      <td>59</td>
      <td>0.101000</td>
      <td>0.704</td>
      <td>189107</td>
      <td>0.521</td>
      <td>0.000062</td>
      <td>8</td>
      <td>0.1160</td>
      <td>-7.65</td>
      <td>1</td>
      <td>0.0307</td>
      <td>130.015</td>
      <td>4</td>
      <td>0.625</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>3</th>
      <td>7tNHLrbkBhU6xh0GL0gTtQ</td>
      <td>Tuesday</td>
      <td>2023-06-23</td>
      <td>Nic D</td>
      <td>1XlLhtgJjC4ROQZilBZAvw</td>
      <td>58</td>
      <td>0.103000</td>
      <td>0.594</td>
      <td>128464</td>
      <td>0.770</td>
      <td>0.000004</td>
      <td>10</td>
      <td>0.2500</td>
      <td>-5.132</td>
      <td>1</td>
      <td>0.0513</td>
      <td>168.124</td>
      <td>4</td>
      <td>0.729</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5DU3wahsCBJ6kkyDBYpIRN</td>
      <td>I Know (feat. Tenille Townes &amp; Bryce Vine)</td>
      <td>2023-06-23</td>
      <td>Train</td>
      <td>3FUY2gzHeIiaesXtOAdB7A</td>
      <td>53</td>
      <td>0.090200</td>
      <td>0.682</td>
      <td>183578</td>
      <td>0.789</td>
      <td>0.000000</td>
      <td>1</td>
      <td>0.3790</td>
      <td>-5.801</td>
      <td>1</td>
      <td>0.0411</td>
      <td>151.974</td>
      <td>4</td>
      <td>0.842</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1wYTR3Hg5rBQcdfxGEdsx0</td>
      <td>See You Again</td>
      <td>2023-06-23</td>
      <td>The Chainsmokers</td>
      <td>69GGBxA162lTqCwzJG5jLp</td>
      <td>69</td>
      <td>0.084400</td>
      <td>0.478</td>
      <td>163452</td>
      <td>0.497</td>
      <td>0.001180</td>
      <td>4</td>
      <td>0.1780</td>
      <td>-4.992</td>
      <td>1</td>
      <td>0.0304</td>
      <td>144.046</td>
      <td>4</td>
      <td>0.209</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>6104</th>
      <td>55fb0yygjCXdrCY5eJdmD1</td>
      <td>Human</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>43</td>
      <td>0.001290</td>
      <td>0.552</td>
      <td>245320</td>
      <td>0.851</td>
      <td>0.000796</td>
      <td>10</td>
      <td>0.0989</td>
      <td>-6.682</td>
      <td>1</td>
      <td>0.0627</td>
      <td>135.481</td>
      <td>4</td>
      <td>0.553</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>6105</th>
      <td>5Fs9DJEuCAJxPePDJ0qmRS</td>
      <td>The Way It Was</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>41</td>
      <td>0.010200</td>
      <td>0.666</td>
      <td>232307</td>
      <td>0.809</td>
      <td>0.000022</td>
      <td>1</td>
      <td>0.0717</td>
      <td>-4.969</td>
      <td>0</td>
      <td>0.0250</td>
      <td>105.993</td>
      <td>4</td>
      <td>0.508</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>6106</th>
      <td>6FxMzrdk9dmjUMLdHeL5tl</td>
      <td>Miss Atomic Bomb</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>42</td>
      <td>0.023400</td>
      <td>0.560</td>
      <td>294760</td>
      <td>0.722</td>
      <td>0.000027</td>
      <td>10</td>
      <td>0.2570</td>
      <td>-7.083</td>
      <td>1</td>
      <td>0.0315</td>
      <td>132.971</td>
      <td>4</td>
      <td>0.348</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>6107</th>
      <td>6HOcAwQAAKaj1GHntfmSoI</td>
      <td>When You Were Young - Calvin Harris Remix</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>45</td>
      <td>0.000162</td>
      <td>0.586</td>
      <td>372800</td>
      <td>0.918</td>
      <td>0.054300</td>
      <td>11</td>
      <td>0.1990</td>
      <td>-4.399</td>
      <td>1</td>
      <td>0.0942</td>
      <td>130.013</td>
      <td>4</td>
      <td>0.459</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>6109</th>
      <td>75CgwX1tMoPfE8C4ZR14D8</td>
      <td>For Reasons Unknown</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>43</td>
      <td>0.000433</td>
      <td>0.496</td>
      <td>213280</td>
      <td>0.889</td>
      <td>0.041500</td>
      <td>3</td>
      <td>0.1220</td>
      <td>-3.855</td>
      <td>1</td>
      <td>0.0372</td>
      <td>140.438</td>
      <td>4</td>
      <td>0.519</td>
      <td>2013</td>
    </tr>
  </tbody>
</table>
<p>2512 rows × 20 columns</p>
</div>



Now lets find unique songs in this dataframe that don't already exist in our playlist. We'll do this by merging our playlist dataframe to our new tracks dataframe and use an indicator to find when a vaue exists in both dataframes. We'll build a new dataframe to only include values that are not present in both the previous dataframes.


```python
df_all = pd.merge(new_tracks_df, df, on=['name','name'], how='left',indicator=True)
unique_tracks_df = df_all[df_all['_merge'] == 'left_only']
unique_tracks_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_ID_x</th>
      <th>name</th>
      <th>release_date_x</th>
      <th>artist_x</th>
      <th>artist_ID_x</th>
      <th>popularity_x</th>
      <th>acousticness</th>
      <th>danceability</th>
      <th>duration_ms</th>
      <th>energy</th>
      <th>...</th>
      <th>time_signature</th>
      <th>valence</th>
      <th>release_year_x</th>
      <th>track_ID_y</th>
      <th>release_date_y</th>
      <th>artist_y</th>
      <th>artist_ID_y</th>
      <th>popularity_y</th>
      <th>release_year_y</th>
      <th>_merge</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4W7bfpXBXrH79dnWyJMkEX</td>
      <td>If Only I (feat. Bebe Rexha)</td>
      <td>2023-06-23</td>
      <td>Loud Luxury</td>
      <td>6t1gpxYbY8OlLA7D2RiikQ</td>
      <td>64</td>
      <td>0.161000</td>
      <td>0.693</td>
      <td>142756</td>
      <td>0.730</td>
      <td>...</td>
      <td>4</td>
      <td>0.693</td>
      <td>2023</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>left_only</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5NMl4DvH391bWzv5GXPUf5</td>
      <td>i need you (in my life)</td>
      <td>2023-06-09</td>
      <td>Jeremy Zucker</td>
      <td>3gIRvgZssIb9aiirIg0nI3</td>
      <td>59</td>
      <td>0.101000</td>
      <td>0.704</td>
      <td>189107</td>
      <td>0.521</td>
      <td>...</td>
      <td>4</td>
      <td>0.625</td>
      <td>2023</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>left_only</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7tNHLrbkBhU6xh0GL0gTtQ</td>
      <td>Tuesday</td>
      <td>2023-06-23</td>
      <td>Nic D</td>
      <td>1XlLhtgJjC4ROQZilBZAvw</td>
      <td>58</td>
      <td>0.103000</td>
      <td>0.594</td>
      <td>128464</td>
      <td>0.770</td>
      <td>...</td>
      <td>4</td>
      <td>0.729</td>
      <td>2023</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>left_only</td>
    </tr>
    <tr>
      <th>3</th>
      <td>5DU3wahsCBJ6kkyDBYpIRN</td>
      <td>I Know (feat. Tenille Townes &amp; Bryce Vine)</td>
      <td>2023-06-23</td>
      <td>Train</td>
      <td>3FUY2gzHeIiaesXtOAdB7A</td>
      <td>53</td>
      <td>0.090200</td>
      <td>0.682</td>
      <td>183578</td>
      <td>0.789</td>
      <td>...</td>
      <td>4</td>
      <td>0.842</td>
      <td>2023</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>left_only</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1wYTR3Hg5rBQcdfxGEdsx0</td>
      <td>See You Again</td>
      <td>2023-06-23</td>
      <td>The Chainsmokers</td>
      <td>69GGBxA162lTqCwzJG5jLp</td>
      <td>69</td>
      <td>0.084400</td>
      <td>0.478</td>
      <td>163452</td>
      <td>0.497</td>
      <td>...</td>
      <td>4</td>
      <td>0.209</td>
      <td>2023</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>left_only</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2527</th>
      <td>55fb0yygjCXdrCY5eJdmD1</td>
      <td>Human</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>43</td>
      <td>0.001290</td>
      <td>0.552</td>
      <td>245320</td>
      <td>0.851</td>
      <td>...</td>
      <td>4</td>
      <td>0.553</td>
      <td>2013</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>left_only</td>
    </tr>
    <tr>
      <th>2528</th>
      <td>5Fs9DJEuCAJxPePDJ0qmRS</td>
      <td>The Way It Was</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>41</td>
      <td>0.010200</td>
      <td>0.666</td>
      <td>232307</td>
      <td>0.809</td>
      <td>...</td>
      <td>4</td>
      <td>0.508</td>
      <td>2013</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>left_only</td>
    </tr>
    <tr>
      <th>2529</th>
      <td>6FxMzrdk9dmjUMLdHeL5tl</td>
      <td>Miss Atomic Bomb</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>42</td>
      <td>0.023400</td>
      <td>0.560</td>
      <td>294760</td>
      <td>0.722</td>
      <td>...</td>
      <td>4</td>
      <td>0.348</td>
      <td>2013</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>left_only</td>
    </tr>
    <tr>
      <th>2530</th>
      <td>6HOcAwQAAKaj1GHntfmSoI</td>
      <td>When You Were Young - Calvin Harris Remix</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>45</td>
      <td>0.000162</td>
      <td>0.586</td>
      <td>372800</td>
      <td>0.918</td>
      <td>...</td>
      <td>4</td>
      <td>0.459</td>
      <td>2013</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>left_only</td>
    </tr>
    <tr>
      <th>2531</th>
      <td>75CgwX1tMoPfE8C4ZR14D8</td>
      <td>For Reasons Unknown</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>43</td>
      <td>0.000433</td>
      <td>0.496</td>
      <td>213280</td>
      <td>0.889</td>
      <td>...</td>
      <td>4</td>
      <td>0.519</td>
      <td>2013</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>left_only</td>
    </tr>
  </tbody>
</table>
<p>2243 rows × 27 columns</p>
</div>



Out of 6097 available tracks, we found 2233 that could potentially fit into our playlist. We reduced the total number of songs by about 63.4%! 

Future steps:
- Train and deploy a machine learning model for advanced recommendations on songs
- Compare track features between my party playlist and that of a different type of playlist (ex: studying/relaxation playlist)
- Identify trends in type of genres that fit with my playlist's track features

The last thing we'll do is export the potential songs out to an excel sheet and we're done!


```python
unique_tracks_df.to_excel('new_tracks.xlsx',index=False)
```
