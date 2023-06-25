---
title: "post test"
date: 2019-04-18T15:34:30-04:00
categories:
  - blog
tags:
  - Jekyll
  - update
---


## Perfect Party Playlist

ajsfhjewaflkjaewhlejhkwjalhflje


```python
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import os
import spotipy
import psycopg2 as pg2
from spotipy.oauth2 import SpotifyClientCredentials
from spotipy.oauth2 import SpotifyOAuth
```


```python
os.environ['SPOTIPY_CLIENT_ID'] = 'f9ac0ad027114dcf97963d2c4d2865d5'
os.environ['SPOTIPY_CLIENT_SECRET'] = '0b5d91bd79a24f1886eaa7c62a21fc8e'
os.environ['SPOTIPY_REDIRECT_URI'] = 'https://localhost:8888/callback'
sp = spotipy.Spotify(auth_manager=SpotifyOAuth(client_id='f9ac0ad027114dcf97963d2c4d2865d5',client_secret='0b5d91bd79a24f1886eaa7c62a21fc8e',redirect_uri='https://localhost:8888/callback',scope='user-library-read'))
```


```python
total_tracks = sp.playlist_tracks('https://open.spotify.com/playlist/4I8DT9gh83aBpiJRSEZ9Jb?si=d5aa22b7400b49d9')['total']
```

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
        saved_tracks.append((track['track']['id'],track['track']['name'],track['track']['album']['release_date'], track['track']['artists'][0]['name'], track['track']['artists'][0]['id'], track['track']['popularity']))
        
    remaining_songs-=100
   
    if remaining_songs <=0:
        break
    else:
        offset+=limit
```


```python
df = pd.DataFrame(data=saved_tracks,columns=['track_ID','name','release_date','artist','artist_ID','popularity'])
```


```python
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
      <td>71</td>
    </tr>
  </tbody>
</table>
<p>730 rows × 6 columns</p>
</div>




```python
df.set_index('track_ID')
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
      <th>name</th>
      <th>release_date</th>
      <th>artist</th>
      <th>artist_ID</th>
      <th>popularity</th>
    </tr>
    <tr>
      <th>track_ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4QNpBfC0zvjKqPJcyqBy9W</th>
      <td>Give Me Everything (feat. Ne-Yo, Afrojack &amp; Na...</td>
      <td>2011-06-17</td>
      <td>Pitbull</td>
      <td>0TnOYISbd1XYRBk9myaseg</td>
      <td>86</td>
    </tr>
    <tr>
      <th>2bJvI42r8EF3wxjOuDav4r</th>
      <td>Time of Our Lives</td>
      <td>2014-11-21</td>
      <td>Pitbull</td>
      <td>0TnOYISbd1XYRBk9myaseg</td>
      <td>84</td>
    </tr>
    <tr>
      <th>4kWO6O1BUXcZmaxitpVUwp</th>
      <td>Jackie Chan</td>
      <td>2018-05-18</td>
      <td>Tiësto</td>
      <td>2o5jDhtHVPhrJdv3cEQ99Z</td>
      <td>74</td>
    </tr>
    <tr>
      <th>07nH4ifBxUB4lZcsf44Brn</th>
      <td>Blame (feat. John Newman)</td>
      <td>2014-10-31</td>
      <td>Calvin Harris</td>
      <td>7CajNmpbOovFoOoasH2HaY</td>
      <td>81</td>
    </tr>
    <tr>
      <th>24LS4lQShWyixJ0ZrJXfJ5</th>
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
    </tr>
    <tr>
      <th>0JbSghVDghtFEurrSO8JrC</th>
      <td>Country Girl (Shake It For Me)</td>
      <td>2011-01-01</td>
      <td>Luke Bryan</td>
      <td>0BvkDsjIUla7X0k6CSWh1I</td>
      <td>81</td>
    </tr>
    <tr>
      <th>57DJaoHdeeRrg7MWthNnee</th>
      <td>Body Back (feat. Maia Wright)</td>
      <td>2019-10-24</td>
      <td>Gryffin</td>
      <td>2ZRQcIgzPCVaT9XKhXZIzh</td>
      <td>62</td>
    </tr>
    <tr>
      <th>0zKbDrEXKpnExhGQRe9dxt</th>
      <td>Lay Low</td>
      <td>2023-01-06</td>
      <td>Tiësto</td>
      <td>2o5jDhtHVPhrJdv3cEQ99Z</td>
      <td>87</td>
    </tr>
    <tr>
      <th>3g5OlVimHO0rK6qmRiwokX</th>
      <td>Glad U Came</td>
      <td>2023-04-27</td>
      <td>Jason Derulo</td>
      <td>07YZf4WDAMNwqr4jfgOZ8y</td>
      <td>73</td>
    </tr>
    <tr>
      <th>0qXGuBm0tmBLjC7InLM3EK</th>
      <td>Don't Say Love</td>
      <td>2023-06-16</td>
      <td>Leigh-Anne</td>
      <td>79QUtAVxGAAoiWNlqBz9iy</td>
      <td>71</td>
    </tr>
  </tbody>
</table>
<p>730 rows × 5 columns</p>
</div>




```python
popularity_hist = sns.displot(data=df,x='popularity',color='royalblue')
popularity_hist.fig.suptitle("Track Popularity Distribution")
plt.show()
```


    
![png](output_10_0.png)
    


quite a large number of songs in the playlist are below a popularity score of 5. It's important to consider that popularity scores are derived from a spotify algorithm that considers the number of times a song is played and how recent those plays are. It's also important to consider that every song on spotify is ranked independentaly, so duplicates of the same song can have different scores. One version of the song on Spotify could have a signifcantly different score compared to another version (ex: explicit vs non-explicit).


```python
print("The mean popularity score: " + str(df['popularity'].mean()))
```

    The mean popularity score: 60.86027397260274



```python
print("The total number of songs that have a popular score of 5 or less: " + str(len(df[df['popularity']<=5])))
```

    The total number of songs that have a popular score of 5 or less: 92


In this case, exactly 92 songs in the playlist are ranked 5 or less on the popularity score. For this reason, popularity may not be the single best representation of which songs to choose for our playlist.


```python
print("The total number of songs that have a popular score of 60 or higher: " + str(len(df[df['popularity']>=60])) + " or " + str(len(df[df['popularity']>=60])/len(df)*100) + "%")
```

    The total number of songs that have a popular score of 60 or higher: 509 or 69.72602739726027%


Nearly 70% of the playlist has a popularity score of 60 or higher. The majority of the songs within the playlist are considered popular.

As with any good party playlist, you want to apeal to the largest addience possible. Let's take a look at the distribution of song release dates for this playlist. Let's take a look at the distribution of song release dates for this playlist. To do this, we first must transform our release_date column from yyyy/mm/dd to just the years for easier analysis. Let's first start by creating a function that'll help us extract the year from our release_date column. 


```python
def release_year(date):
    return int(str(date)[:4])
```

Now we create a new column using the apply() function on the current release_date column. Once this is added to our dataframe, we can analyze the release_year distrobution.


```python
df['release_year'] = df['release_date'].apply(release_year)
```


```python
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
    <tr>
      <th>...</th>
      <td>...</td>
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
      <td>2011</td>
    </tr>
    <tr>
      <th>726</th>
      <td>57DJaoHdeeRrg7MWthNnee</td>
      <td>Body Back (feat. Maia Wright)</td>
      <td>2019-10-24</td>
      <td>Gryffin</td>
      <td>2ZRQcIgzPCVaT9XKhXZIzh</td>
      <td>62</td>
      <td>2019</td>
    </tr>
    <tr>
      <th>727</th>
      <td>0zKbDrEXKpnExhGQRe9dxt</td>
      <td>Lay Low</td>
      <td>2023-01-06</td>
      <td>Tiësto</td>
      <td>2o5jDhtHVPhrJdv3cEQ99Z</td>
      <td>87</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>728</th>
      <td>3g5OlVimHO0rK6qmRiwokX</td>
      <td>Glad U Came</td>
      <td>2023-04-27</td>
      <td>Jason Derulo</td>
      <td>07YZf4WDAMNwqr4jfgOZ8y</td>
      <td>73</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>729</th>
      <td>0qXGuBm0tmBLjC7InLM3EK</td>
      <td>Don't Say Love</td>
      <td>2023-06-16</td>
      <td>Leigh-Anne</td>
      <td>79QUtAVxGAAoiWNlqBz9iy</td>
      <td>71</td>
      <td>2023</td>
    </tr>
  </tbody>
</table>
<p>730 rows × 7 columns</p>
</div>




```python
year_hist = sns.displot(data=df,x='release_year',bins=20,color='royalblue')
year_hist.fig.suptitle("Track Release Year Distribution")
plt.show()
```


    
![png](output_22_0.png)
    



```python
print("The mean release year: " + str(df['release_year'].mean()))
```

    The mean release year: 2013.1808219178083


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
            feature_tuple = (track['id'],track['acousticness'],track['danceability'],track['duration_ms'],track['energy'],track['instrumentalness'],track['key'],track['liveness'],track['loudness'],track['mode'],track['speechiness'],track['tempo'],track['time_signature'],track['valence'])
            features_list.append(feature_tuple)
            
        except TypeError:
            feature_tuple = (track_subset[index],"DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT")
            features_list.append(feature_tuple)
            

    remaining_songs-=limit
    
    if remaining_songs <=0:
        break
    else:
        offset+=limit
```


```python
features_df = pd.DataFrame(data=features_list,columns=['track_ID','acousticness','danceability','duration_ms','energy','instrumentalness','key','liveness','loudness','mode','speechiness','tempo','time_signature','valence'])
```


```python
features_df.set_index('track_ID')
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
    <tr>
      <th>track_ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
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
      <th>4QNpBfC0zvjKqPJcyqBy9W</th>
      <td>0.19100</td>
      <td>0.671</td>
      <td>252307</td>
      <td>0.939</td>
      <td>0.000000</td>
      <td>8</td>
      <td>0.2980</td>
      <td>-3.206</td>
      <td>1</td>
      <td>0.1610</td>
      <td>129.024</td>
      <td>4</td>
      <td>0.530</td>
    </tr>
    <tr>
      <th>2bJvI42r8EF3wxjOuDav4r</th>
      <td>0.09210</td>
      <td>0.721</td>
      <td>229360</td>
      <td>0.802</td>
      <td>0.000000</td>
      <td>1</td>
      <td>0.6940</td>
      <td>-5.797</td>
      <td>1</td>
      <td>0.0583</td>
      <td>124.022</td>
      <td>4</td>
      <td>0.724</td>
    </tr>
    <tr>
      <th>4kWO6O1BUXcZmaxitpVUwp</th>
      <td>0.37400</td>
      <td>0.747</td>
      <td>215760</td>
      <td>0.834</td>
      <td>0.000000</td>
      <td>3</td>
      <td>0.0586</td>
      <td>-2.867</td>
      <td>0</td>
      <td>0.0450</td>
      <td>128.005</td>
      <td>4</td>
      <td>0.687</td>
    </tr>
    <tr>
      <th>07nH4ifBxUB4lZcsf44Brn</th>
      <td>0.02870</td>
      <td>0.414</td>
      <td>212960</td>
      <td>0.857</td>
      <td>0.005740</td>
      <td>0</td>
      <td>0.3430</td>
      <td>-4.078</td>
      <td>0</td>
      <td>0.0808</td>
      <td>128.024</td>
      <td>4</td>
      <td>0.348</td>
    </tr>
    <tr>
      <th>24LS4lQShWyixJ0ZrJXfJ5</th>
      <td>0.19700</td>
      <td>0.573</td>
      <td>212560</td>
      <td>0.929</td>
      <td>0.000112</td>
      <td>8</td>
      <td>0.0567</td>
      <td>-3.942</td>
      <td>0</td>
      <td>0.1090</td>
      <td>127.934</td>
      <td>4</td>
      <td>0.582</td>
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
    </tr>
    <tr>
      <th>0JbSghVDghtFEurrSO8JrC</th>
      <td>0.02930</td>
      <td>0.645</td>
      <td>225560</td>
      <td>0.904</td>
      <td>0.000000</td>
      <td>2</td>
      <td>0.0834</td>
      <td>-4.532</td>
      <td>1</td>
      <td>0.0462</td>
      <td>105.970</td>
      <td>4</td>
      <td>0.671</td>
    </tr>
    <tr>
      <th>57DJaoHdeeRrg7MWthNnee</th>
      <td>0.09310</td>
      <td>0.687</td>
      <td>214547</td>
      <td>0.832</td>
      <td>0.000001</td>
      <td>8</td>
      <td>0.1830</td>
      <td>-4.434</td>
      <td>1</td>
      <td>0.0366</td>
      <td>123.036</td>
      <td>4</td>
      <td>0.448</td>
    </tr>
    <tr>
      <th>0zKbDrEXKpnExhGQRe9dxt</th>
      <td>0.06070</td>
      <td>0.534</td>
      <td>153443</td>
      <td>0.855</td>
      <td>0.000263</td>
      <td>1</td>
      <td>0.3460</td>
      <td>-4.923</td>
      <td>0</td>
      <td>0.1830</td>
      <td>122.060</td>
      <td>4</td>
      <td>0.420</td>
    </tr>
    <tr>
      <th>3g5OlVimHO0rK6qmRiwokX</th>
      <td>0.00627</td>
      <td>0.675</td>
      <td>178081</td>
      <td>0.801</td>
      <td>0.000000</td>
      <td>8</td>
      <td>0.3710</td>
      <td>-3.707</td>
      <td>1</td>
      <td>0.0579</td>
      <td>112.002</td>
      <td>4</td>
      <td>0.521</td>
    </tr>
    <tr>
      <th>0qXGuBm0tmBLjC7InLM3EK</th>
      <td>0.01170</td>
      <td>0.703</td>
      <td>180000</td>
      <td>0.883</td>
      <td>0.000000</td>
      <td>9</td>
      <td>0.2550</td>
      <td>-2.652</td>
      <td>0</td>
      <td>0.1840</td>
      <td>131.877</td>
      <td>4</td>
      <td>0.812</td>
    </tr>
  </tbody>
</table>
<p>730 rows × 13 columns</p>
</div>




```python
features_df.describe().transpose()
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
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>acousticness</th>
      <td>730.0</td>
      <td>0.107897</td>
      <td>0.127984</td>
      <td>0.000018</td>
      <td>0.017900</td>
      <td>0.0578</td>
      <td>0.161500</td>
      <td>0.699</td>
    </tr>
    <tr>
      <th>danceability</th>
      <td>730.0</td>
      <td>0.688834</td>
      <td>0.122045</td>
      <td>0.276000</td>
      <td>0.618000</td>
      <td>0.6960</td>
      <td>0.763750</td>
      <td>0.967</td>
    </tr>
    <tr>
      <th>duration_ms</th>
      <td>730.0</td>
      <td>210495.646575</td>
      <td>43512.586492</td>
      <td>112105.000000</td>
      <td>181543.000000</td>
      <td>206679.0000</td>
      <td>231823.500000</td>
      <td>516893.000</td>
    </tr>
    <tr>
      <th>energy</th>
      <td>730.0</td>
      <td>0.731086</td>
      <td>0.133454</td>
      <td>0.127000</td>
      <td>0.647000</td>
      <td>0.7410</td>
      <td>0.833000</td>
      <td>0.978</td>
    </tr>
    <tr>
      <th>instrumentalness</th>
      <td>730.0</td>
      <td>0.007994</td>
      <td>0.056376</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0000</td>
      <td>0.000032</td>
      <td>0.837</td>
    </tr>
    <tr>
      <th>key</th>
      <td>730.0</td>
      <td>5.260274</td>
      <td>3.623681</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>6.0000</td>
      <td>8.000000</td>
      <td>11.000</td>
    </tr>
    <tr>
      <th>liveness</th>
      <td>730.0</td>
      <td>0.185877</td>
      <td>0.142204</td>
      <td>0.022600</td>
      <td>0.090900</td>
      <td>0.1275</td>
      <td>0.255000</td>
      <td>0.826</td>
    </tr>
    <tr>
      <th>loudness</th>
      <td>730.0</td>
      <td>-5.511659</td>
      <td>1.853605</td>
      <td>-18.064000</td>
      <td>-6.381000</td>
      <td>-5.2795</td>
      <td>-4.305000</td>
      <td>-0.930</td>
    </tr>
    <tr>
      <th>mode</th>
      <td>730.0</td>
      <td>0.671233</td>
      <td>0.470087</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.0000</td>
      <td>1.000000</td>
      <td>1.000</td>
    </tr>
    <tr>
      <th>speechiness</th>
      <td>730.0</td>
      <td>0.087442</td>
      <td>0.078224</td>
      <td>0.025200</td>
      <td>0.040725</td>
      <td>0.0575</td>
      <td>0.101000</td>
      <td>0.592</td>
    </tr>
    <tr>
      <th>tempo</th>
      <td>730.0</td>
      <td>120.076452</td>
      <td>24.658935</td>
      <td>62.538000</td>
      <td>100.029750</td>
      <td>119.9650</td>
      <td>130.997750</td>
      <td>202.015</td>
    </tr>
    <tr>
      <th>time_signature</th>
      <td>730.0</td>
      <td>3.980822</td>
      <td>0.233455</td>
      <td>1.000000</td>
      <td>4.000000</td>
      <td>4.0000</td>
      <td>4.000000</td>
      <td>5.000</td>
    </tr>
    <tr>
      <th>valence</th>
      <td>730.0</td>
      <td>0.588999</td>
      <td>0.207757</td>
      <td>0.057900</td>
      <td>0.446500</td>
      <td>0.5940</td>
      <td>0.754750</td>
      <td>0.979</td>
    </tr>
  </tbody>
</table>
</div>



Many of these audio features are classified on a scale from 0 to 1. We see that on average, the danceability, energy, and valence are higher for this playlist. We also see that on average, acousticness, instrumentalness, liveness, and speechiness are lower. Graphing this out better illustrates the pattern identified. We'll create a new table to create a violin plot by first transforming the current table. 


```python
features_df_transformed = pd.melt(features_df,value_vars=['acousticness','danceability','energy','instrumentalness','liveness','speechiness','valence'])
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
plt.figure(figsize=(5,8))
sns.stripplot(data=features_df_transformed,y='variable',x='value',size=6, alpha=0.3, order=['instrumentalness','speechiness','acousticness','liveness','valence','danceability','energy'],palette='plasma')
plt.title("Classification Score Distribution per Track Feature")
plt.ylabel("Track Features")
plt.xlabel("Classification Score")
plt.show()
```


    
![png](output_32_0.png)
    


Can we use this data to help decide which songs would fit into our playlist? 

We now know:
- Popularity scores matters.
- Songs should have high danceability, energy, and valence.
- Songs should have low instrumentalness, speechiness, acoustics, and liveness.
- Songs should be fairly modern.

For our analysis, we will use the following requirements (based on the calculated mean values):
- a popularity score >60
- an energy score >0.73
- a danceability score >0.69
- a valence score >0.59
- a liveness score <0.19
- an acousticness score <0.11
- a speechiness score <0.09
- an instrumentalness score <0.01
- a year >2013

We'll start by loading in over 6,000 of my liked songs to find candidates to include in my playlist based on these requirements.


```python
remaining_songs = sp.current_user_saved_tracks()['total']
offset=0
limit=50
saved_tracks = []

while True:
    
    spotify_liked_songs = sp.current_user_saved_tracks(limit,offset)
    tracks = spotify_liked_songs['items']
    
    for track in tracks:
        
        saved_tracks.append((track['track']['id'],track['track']['name'],track['track']['album']['release_date'], track['track']['artists'][0]['name'], track['track']['artists'][0]['id'], track['track']['popularity']))
        
    remaining_songs-=limit
   
    if remaining_songs <=0:
        break
    else:
        offset+=limit
```


```python
liked_songs_df = pd.DataFrame(data=saved_tracks,columns=['track_ID','name','release_date','artist','artist_ID','popularity'])
```


```python
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
      <td>3g5OlVimHO0rK6qmRiwokX</td>
      <td>Glad U Came</td>
      <td>2023-04-27</td>
      <td>Jason Derulo</td>
      <td>07YZf4WDAMNwqr4jfgOZ8y</td>
      <td>73</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0zKbDrEXKpnExhGQRe9dxt</td>
      <td>Lay Low</td>
      <td>2023-01-06</td>
      <td>Tiësto</td>
      <td>2o5jDhtHVPhrJdv3cEQ99Z</td>
      <td>87</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0beGbe6JESMEjCDRsxBTAz</td>
      <td>Don't Wanna Wake</td>
      <td>2023-06-16</td>
      <td>Kid Travis</td>
      <td>6UWui6Csqc1ywqnDzjOWnk</td>
      <td>49</td>
    </tr>
    <tr>
      <th>3</th>
      <td>04hxf6171aKFM67gNkOfKt</td>
      <td>Barcelona (feat. Samm Henshaw)</td>
      <td>2023-06-16</td>
      <td>Kota the Friend</td>
      <td>2AfU5LYBVCiCtuCCfM7uVX</td>
      <td>59</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2S2uzei41Epnb1TndFTS9l</td>
      <td>Off My Mind</td>
      <td>2023-06-16</td>
      <td>Coco &amp; Breezy</td>
      <td>0Adbm5kzcPUxFybf9fhjgG</td>
      <td>49</td>
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
      <th>6089</th>
      <td>6FxMzrdk9dmjUMLdHeL5tl</td>
      <td>Miss Atomic Bomb</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>42</td>
    </tr>
    <tr>
      <th>6090</th>
      <td>6HOcAwQAAKaj1GHntfmSoI</td>
      <td>When You Were Young - Calvin Harris Remix</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>45</td>
    </tr>
    <tr>
      <th>6091</th>
      <td>6aKWBLAFHXHUjsJJri2ota</td>
      <td>When You Were Young</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>41</td>
    </tr>
    <tr>
      <th>6092</th>
      <td>75CgwX1tMoPfE8C4ZR14D8</td>
      <td>For Reasons Unknown</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>43</td>
    </tr>
    <tr>
      <th>6093</th>
      <td>6HFbq7cewJ7rPiffV0ciil</td>
      <td>A Sky Full of Stars</td>
      <td>2014-05-02</td>
      <td>Coldplay</td>
      <td>4gzpq5DPGxSnKTe4SA8HAU</td>
      <td>60</td>
    </tr>
  </tbody>
</table>
<p>6094 rows × 6 columns</p>
</div>



We'll now pull the track features for each song in the liked songs dataframe.


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
            feature_tuple = (track['id'],track['acousticness'],track['danceability'],track['duration_ms'],track['energy'],track['instrumentalness'],track['key'],track['liveness'],track['loudness'],track['mode'],track['speechiness'],track['tempo'],track['time_signature'],track['valence'])
            features_list.append(feature_tuple)
            
        except TypeError:
            feature_tuple = (track_subset[index],"DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT","DEFAULT")
            features_list.append(feature_tuple)
            

    remaining_songs-=limit
    
    if remaining_songs <=0:
        break
    else:
        offset+=limit
```


```python
liked_songs_features_df = pd.DataFrame(data=features_list,columns=['track_ID','acousticness','danceability','duration_ms','energy','instrumentalness','key','liveness','loudness','mode','speechiness','tempo','time_signature','valence'])
```


```python
liked_songs_features_df.set_index('track_ID')
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
    <tr>
      <th>track_ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
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
      <th>3g5OlVimHO0rK6qmRiwokX</th>
      <td>0.00627</td>
      <td>0.675</td>
      <td>178081</td>
      <td>0.801</td>
      <td>0</td>
      <td>8</td>
      <td>0.371</td>
      <td>-3.707</td>
      <td>1</td>
      <td>0.0579</td>
      <td>112.002</td>
      <td>4</td>
      <td>0.521</td>
    </tr>
    <tr>
      <th>0zKbDrEXKpnExhGQRe9dxt</th>
      <td>0.0607</td>
      <td>0.534</td>
      <td>153443</td>
      <td>0.855</td>
      <td>0.000263</td>
      <td>1</td>
      <td>0.346</td>
      <td>-4.923</td>
      <td>0</td>
      <td>0.183</td>
      <td>122.06</td>
      <td>4</td>
      <td>0.42</td>
    </tr>
    <tr>
      <th>0beGbe6JESMEjCDRsxBTAz</th>
      <td>0.148</td>
      <td>0.73</td>
      <td>189600</td>
      <td>0.556</td>
      <td>0.000004</td>
      <td>9</td>
      <td>0.369</td>
      <td>-7.805</td>
      <td>1</td>
      <td>0.0588</td>
      <td>99.963</td>
      <td>4</td>
      <td>0.425</td>
    </tr>
    <tr>
      <th>04hxf6171aKFM67gNkOfKt</th>
      <td>0.17</td>
      <td>0.816</td>
      <td>217826</td>
      <td>0.466</td>
      <td>0</td>
      <td>4</td>
      <td>0.0924</td>
      <td>-9.024</td>
      <td>0</td>
      <td>0.0578</td>
      <td>115.038</td>
      <td>4</td>
      <td>0.274</td>
    </tr>
    <tr>
      <th>2S2uzei41Epnb1TndFTS9l</th>
      <td>0.407</td>
      <td>0.83</td>
      <td>168197</td>
      <td>0.607</td>
      <td>0.0287</td>
      <td>1</td>
      <td>0.111</td>
      <td>-5.958</td>
      <td>1</td>
      <td>0.266</td>
      <td>122.158</td>
      <td>4</td>
      <td>0.422</td>
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
    </tr>
    <tr>
      <th>6FxMzrdk9dmjUMLdHeL5tl</th>
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
    </tr>
    <tr>
      <th>6HOcAwQAAKaj1GHntfmSoI</th>
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
    </tr>
    <tr>
      <th>6aKWBLAFHXHUjsJJri2ota</th>
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
    </tr>
    <tr>
      <th>75CgwX1tMoPfE8C4ZR14D8</th>
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
    </tr>
    <tr>
      <th>6HFbq7cewJ7rPiffV0ciil</th>
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
    </tr>
  </tbody>
</table>
<p>6094 rows × 13 columns</p>
</div>



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
      <td>3g5OlVimHO0rK6qmRiwokX</td>
      <td>Glad U Came</td>
      <td>2023-04-27</td>
      <td>Jason Derulo</td>
      <td>07YZf4WDAMNwqr4jfgOZ8y</td>
      <td>73</td>
      <td>0.00627</td>
      <td>0.675</td>
      <td>178081</td>
      <td>0.801</td>
      <td>0</td>
      <td>8</td>
      <td>0.371</td>
      <td>-3.707</td>
      <td>1</td>
      <td>0.0579</td>
      <td>112.002</td>
      <td>4</td>
      <td>0.521</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0zKbDrEXKpnExhGQRe9dxt</td>
      <td>Lay Low</td>
      <td>2023-01-06</td>
      <td>Tiësto</td>
      <td>2o5jDhtHVPhrJdv3cEQ99Z</td>
      <td>87</td>
      <td>0.0607</td>
      <td>0.534</td>
      <td>153443</td>
      <td>0.855</td>
      <td>0.000263</td>
      <td>1</td>
      <td>0.346</td>
      <td>-4.923</td>
      <td>0</td>
      <td>0.183</td>
      <td>122.06</td>
      <td>4</td>
      <td>0.42</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0beGbe6JESMEjCDRsxBTAz</td>
      <td>Don't Wanna Wake</td>
      <td>2023-06-16</td>
      <td>Kid Travis</td>
      <td>6UWui6Csqc1ywqnDzjOWnk</td>
      <td>49</td>
      <td>0.148</td>
      <td>0.73</td>
      <td>189600</td>
      <td>0.556</td>
      <td>0.000004</td>
      <td>9</td>
      <td>0.369</td>
      <td>-7.805</td>
      <td>1</td>
      <td>0.0588</td>
      <td>99.963</td>
      <td>4</td>
      <td>0.425</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>3</th>
      <td>04hxf6171aKFM67gNkOfKt</td>
      <td>Barcelona (feat. Samm Henshaw)</td>
      <td>2023-06-16</td>
      <td>Kota the Friend</td>
      <td>2AfU5LYBVCiCtuCCfM7uVX</td>
      <td>59</td>
      <td>0.17</td>
      <td>0.816</td>
      <td>217826</td>
      <td>0.466</td>
      <td>0</td>
      <td>4</td>
      <td>0.0924</td>
      <td>-9.024</td>
      <td>0</td>
      <td>0.0578</td>
      <td>115.038</td>
      <td>4</td>
      <td>0.274</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2S2uzei41Epnb1TndFTS9l</td>
      <td>Off My Mind</td>
      <td>2023-06-16</td>
      <td>Coco &amp; Breezy</td>
      <td>0Adbm5kzcPUxFybf9fhjgG</td>
      <td>49</td>
      <td>0.407</td>
      <td>0.83</td>
      <td>168197</td>
      <td>0.607</td>
      <td>0.0287</td>
      <td>1</td>
      <td>0.111</td>
      <td>-5.958</td>
      <td>1</td>
      <td>0.266</td>
      <td>122.158</td>
      <td>4</td>
      <td>0.422</td>
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
      <th>6089</th>
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
      <th>6090</th>
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
      <th>6091</th>
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
      <th>6092</th>
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
      <th>6093</th>
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
<p>6094 rows × 20 columns</p>
</div>



We are now able to filter our dataframe based on the criteria we previously identified.
- a popularity score >60
- an energy score >0.73
- a danceability score >0.69
- a valence score >0.59
- a liveness score <0.19
- an acousticness score <0.11
- a speechiness score <0.09
- an instrumentalness score <0.01
- a year >2013



```python
merged_df[merged_df['acousticness'] == 'DEFAULT']
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
      <th>2523</th>
      <td>4t9qGGbKPbEc7xTmySiGpG</td>
      <td>92</td>
      <td>2020-10-16</td>
      <td>Fickle Friends</td>
      <td>1nhSLEYdoBHG6cJ8NDwoF1</td>
      <td>0</td>
      <td>DEFAULT</td>
      <td>DEFAULT</td>
      <td>DEFAULT</td>
      <td>DEFAULT</td>
      <td>DEFAULT</td>
      <td>DEFAULT</td>
      <td>DEFAULT</td>
      <td>DEFAULT</td>
      <td>DEFAULT</td>
      <td>DEFAULT</td>
      <td>DEFAULT</td>
      <td>DEFAULT</td>
      <td>DEFAULT</td>
      <td>2020</td>
    </tr>
  </tbody>
</table>
</div>




```python
merged_df.iloc[2523]
```




    track_ID            4t9qGGbKPbEc7xTmySiGpG
    name                                    92
    release_date                    2020-10-16
    artist                      Fickle Friends
    artist_ID           1nhSLEYdoBHG6cJ8NDwoF1
    popularity                               0
    acousticness                       DEFAULT
    danceability                       DEFAULT
    duration_ms                        DEFAULT
    energy                             DEFAULT
    instrumentalness                   DEFAULT
    key                                DEFAULT
    liveness                           DEFAULT
    loudness                           DEFAULT
    mode                               DEFAULT
    speechiness                        DEFAULT
    tempo                              DEFAULT
    time_signature                     DEFAULT
    valence                            DEFAULT
    release_year                          2020
    Name: 2523, dtype: object




```python
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
      <th>level_0</th>
      <th>index</th>
      <th>track_ID</th>
      <th>name</th>
      <th>release_date</th>
      <th>artist</th>
      <th>artist_ID</th>
      <th>popularity</th>
      <th>acousticness</th>
      <th>danceability</th>
      <th>...</th>
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
      <td>0</td>
      <td>0</td>
      <td>3g5OlVimHO0rK6qmRiwokX</td>
      <td>Glad U Came</td>
      <td>2023-04-27</td>
      <td>Jason Derulo</td>
      <td>07YZf4WDAMNwqr4jfgOZ8y</td>
      <td>73</td>
      <td>0.00627</td>
      <td>0.675</td>
      <td>...</td>
      <td>0</td>
      <td>8</td>
      <td>0.371</td>
      <td>-3.707</td>
      <td>1</td>
      <td>0.0579</td>
      <td>112.002</td>
      <td>4</td>
      <td>0.521</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
      <td>0zKbDrEXKpnExhGQRe9dxt</td>
      <td>Lay Low</td>
      <td>2023-01-06</td>
      <td>Tiësto</td>
      <td>2o5jDhtHVPhrJdv3cEQ99Z</td>
      <td>87</td>
      <td>0.0607</td>
      <td>0.534</td>
      <td>...</td>
      <td>0.000263</td>
      <td>1</td>
      <td>0.346</td>
      <td>-4.923</td>
      <td>0</td>
      <td>0.183</td>
      <td>122.06</td>
      <td>4</td>
      <td>0.42</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2</td>
      <td>0beGbe6JESMEjCDRsxBTAz</td>
      <td>Don't Wanna Wake</td>
      <td>2023-06-16</td>
      <td>Kid Travis</td>
      <td>6UWui6Csqc1ywqnDzjOWnk</td>
      <td>49</td>
      <td>0.148</td>
      <td>0.73</td>
      <td>...</td>
      <td>0.000004</td>
      <td>9</td>
      <td>0.369</td>
      <td>-7.805</td>
      <td>1</td>
      <td>0.0588</td>
      <td>99.963</td>
      <td>4</td>
      <td>0.425</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>3</td>
      <td>04hxf6171aKFM67gNkOfKt</td>
      <td>Barcelona (feat. Samm Henshaw)</td>
      <td>2023-06-16</td>
      <td>Kota the Friend</td>
      <td>2AfU5LYBVCiCtuCCfM7uVX</td>
      <td>59</td>
      <td>0.17</td>
      <td>0.816</td>
      <td>...</td>
      <td>0</td>
      <td>4</td>
      <td>0.0924</td>
      <td>-9.024</td>
      <td>0</td>
      <td>0.0578</td>
      <td>115.038</td>
      <td>4</td>
      <td>0.274</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>4</td>
      <td>2S2uzei41Epnb1TndFTS9l</td>
      <td>Off My Mind</td>
      <td>2023-06-16</td>
      <td>Coco &amp; Breezy</td>
      <td>0Adbm5kzcPUxFybf9fhjgG</td>
      <td>49</td>
      <td>0.407</td>
      <td>0.83</td>
      <td>...</td>
      <td>0.0287</td>
      <td>1</td>
      <td>0.111</td>
      <td>-5.958</td>
      <td>1</td>
      <td>0.266</td>
      <td>122.158</td>
      <td>4</td>
      <td>0.422</td>
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
      <td>...</td>
    </tr>
    <tr>
      <th>6089</th>
      <td>6089</td>
      <td>6089</td>
      <td>6FxMzrdk9dmjUMLdHeL5tl</td>
      <td>Miss Atomic Bomb</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>42</td>
      <td>0.0234</td>
      <td>0.56</td>
      <td>...</td>
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
      <th>6090</th>
      <td>6090</td>
      <td>6090</td>
      <td>6HOcAwQAAKaj1GHntfmSoI</td>
      <td>When You Were Young - Calvin Harris Remix</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>45</td>
      <td>0.000162</td>
      <td>0.586</td>
      <td>...</td>
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
      <th>6091</th>
      <td>6091</td>
      <td>6091</td>
      <td>6aKWBLAFHXHUjsJJri2ota</td>
      <td>When You Were Young</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>41</td>
      <td>0.000335</td>
      <td>0.441</td>
      <td>...</td>
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
      <th>6092</th>
      <td>6092</td>
      <td>6092</td>
      <td>75CgwX1tMoPfE8C4ZR14D8</td>
      <td>For Reasons Unknown</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>43</td>
      <td>0.000433</td>
      <td>0.496</td>
      <td>...</td>
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
      <th>6093</th>
      <td>6093</td>
      <td>6093</td>
      <td>6HFbq7cewJ7rPiffV0ciil</td>
      <td>A Sky Full of Stars</td>
      <td>2014-05-02</td>
      <td>Coldplay</td>
      <td>4gzpq5DPGxSnKTe4SA8HAU</td>
      <td>60</td>
      <td>0.00617</td>
      <td>0.545</td>
      <td>...</td>
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
<p>6094 rows × 22 columns</p>
</div>




```python
merged_df = merged_df.drop(2523,axis=0)
```


```python
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
      <th>level_0</th>
      <th>index</th>
      <th>track_ID</th>
      <th>name</th>
      <th>release_date</th>
      <th>artist</th>
      <th>artist_ID</th>
      <th>popularity</th>
      <th>acousticness</th>
      <th>danceability</th>
      <th>...</th>
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
      <td>0</td>
      <td>0</td>
      <td>3g5OlVimHO0rK6qmRiwokX</td>
      <td>Glad U Came</td>
      <td>2023-04-27</td>
      <td>Jason Derulo</td>
      <td>07YZf4WDAMNwqr4jfgOZ8y</td>
      <td>73</td>
      <td>0.00627</td>
      <td>0.675</td>
      <td>...</td>
      <td>0</td>
      <td>8</td>
      <td>0.371</td>
      <td>-3.707</td>
      <td>1</td>
      <td>0.0579</td>
      <td>112.002</td>
      <td>4</td>
      <td>0.521</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
      <td>0zKbDrEXKpnExhGQRe9dxt</td>
      <td>Lay Low</td>
      <td>2023-01-06</td>
      <td>Tiësto</td>
      <td>2o5jDhtHVPhrJdv3cEQ99Z</td>
      <td>87</td>
      <td>0.0607</td>
      <td>0.534</td>
      <td>...</td>
      <td>0.000263</td>
      <td>1</td>
      <td>0.346</td>
      <td>-4.923</td>
      <td>0</td>
      <td>0.183</td>
      <td>122.06</td>
      <td>4</td>
      <td>0.42</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2</td>
      <td>0beGbe6JESMEjCDRsxBTAz</td>
      <td>Don't Wanna Wake</td>
      <td>2023-06-16</td>
      <td>Kid Travis</td>
      <td>6UWui6Csqc1ywqnDzjOWnk</td>
      <td>49</td>
      <td>0.148</td>
      <td>0.73</td>
      <td>...</td>
      <td>0.000004</td>
      <td>9</td>
      <td>0.369</td>
      <td>-7.805</td>
      <td>1</td>
      <td>0.0588</td>
      <td>99.963</td>
      <td>4</td>
      <td>0.425</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>3</td>
      <td>04hxf6171aKFM67gNkOfKt</td>
      <td>Barcelona (feat. Samm Henshaw)</td>
      <td>2023-06-16</td>
      <td>Kota the Friend</td>
      <td>2AfU5LYBVCiCtuCCfM7uVX</td>
      <td>59</td>
      <td>0.17</td>
      <td>0.816</td>
      <td>...</td>
      <td>0</td>
      <td>4</td>
      <td>0.0924</td>
      <td>-9.024</td>
      <td>0</td>
      <td>0.0578</td>
      <td>115.038</td>
      <td>4</td>
      <td>0.274</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>4</td>
      <td>2S2uzei41Epnb1TndFTS9l</td>
      <td>Off My Mind</td>
      <td>2023-06-16</td>
      <td>Coco &amp; Breezy</td>
      <td>0Adbm5kzcPUxFybf9fhjgG</td>
      <td>49</td>
      <td>0.407</td>
      <td>0.83</td>
      <td>...</td>
      <td>0.0287</td>
      <td>1</td>
      <td>0.111</td>
      <td>-5.958</td>
      <td>1</td>
      <td>0.266</td>
      <td>122.158</td>
      <td>4</td>
      <td>0.422</td>
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
      <td>...</td>
    </tr>
    <tr>
      <th>6089</th>
      <td>6089</td>
      <td>6089</td>
      <td>6FxMzrdk9dmjUMLdHeL5tl</td>
      <td>Miss Atomic Bomb</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>42</td>
      <td>0.0234</td>
      <td>0.56</td>
      <td>...</td>
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
      <th>6090</th>
      <td>6090</td>
      <td>6090</td>
      <td>6HOcAwQAAKaj1GHntfmSoI</td>
      <td>When You Were Young - Calvin Harris Remix</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>45</td>
      <td>0.000162</td>
      <td>0.586</td>
      <td>...</td>
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
      <th>6091</th>
      <td>6091</td>
      <td>6091</td>
      <td>6aKWBLAFHXHUjsJJri2ota</td>
      <td>When You Were Young</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>41</td>
      <td>0.000335</td>
      <td>0.441</td>
      <td>...</td>
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
      <th>6092</th>
      <td>6092</td>
      <td>6092</td>
      <td>75CgwX1tMoPfE8C4ZR14D8</td>
      <td>For Reasons Unknown</td>
      <td>2013-01-01</td>
      <td>The Killers</td>
      <td>0C0XlULifJtAgn6ZNCW2eu</td>
      <td>43</td>
      <td>0.000433</td>
      <td>0.496</td>
      <td>...</td>
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
      <th>6093</th>
      <td>6093</td>
      <td>6093</td>
      <td>6HFbq7cewJ7rPiffV0ciil</td>
      <td>A Sky Full of Stars</td>
      <td>2014-05-02</td>
      <td>Coldplay</td>
      <td>4gzpq5DPGxSnKTe4SA8HAU</td>
      <td>60</td>
      <td>0.00617</td>
      <td>0.545</td>
      <td>...</td>
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
<p>6093 rows × 22 columns</p>
</div>




```python
merged_df['acousticness'] = pd.to_numeric(merged_df['acousticness'])
merged_df['energy'] = pd.to_numeric(merged_df['energy'])
merged_df['danceability'] = pd.to_numeric(merged_df['danceability'])
merged_df['valence'] = pd.to_numeric(merged_df['valence'])
merged_df['liveness'] = pd.to_numeric(merged_df['liveness'])
merged_df['speechiness'] = pd.to_numeric(merged_df['speechiness'])
merged_df['instrumentalness'] = pd.to_numeric(merged_df['instrumentalness'])
```


```python
merged_df[(merged_df['popularity']>60) & (merged_df['energy']>0.73) & (merged_df['danceability']>0.69) & (merged_df['valence']>0.59) & (merged_df['liveness']<0.19) & (merged_df['acousticness']<0.11) & (merged_df['speechiness']<0.09) & (merged_df['instrumentalness']<0.01) & (merged_df['release_year']>2013)]
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
      <th>level_0</th>
      <th>index</th>
      <th>track_ID</th>
      <th>name</th>
      <th>release_date</th>
      <th>artist</th>
      <th>artist_ID</th>
      <th>popularity</th>
      <th>acousticness</th>
      <th>danceability</th>
      <th>...</th>
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
      <th>131</th>
      <td>131</td>
      <td>131</td>
      <td>7LbhIjYNfCguxjAw43Mkff</td>
      <td>Ghost</td>
      <td>2023-01-27</td>
      <td>Ava Max</td>
      <td>4npEfmQ6YuiwW1GpUmaq3F</td>
      <td>70</td>
      <td>0.00573</td>
      <td>0.715</td>
      <td>...</td>
      <td>0.001850</td>
      <td>11</td>
      <td>0.0549</td>
      <td>-3.548</td>
      <td>0</td>
      <td>0.0426</td>
      <td>119.971</td>
      <td>4</td>
      <td>0.615</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>144</th>
      <td>144</td>
      <td>144</td>
      <td>1JUtrCqYzJ80tcAzMbCvir</td>
      <td>Lottery (feat. LU KALA)</td>
      <td>2023-02-17</td>
      <td>Latto</td>
      <td>3MdXrJWsbVzdn6fe5JYkSQ</td>
      <td>79</td>
      <td>0.01580</td>
      <td>0.792</td>
      <td>...</td>
      <td>0.000365</td>
      <td>1</td>
      <td>0.0710</td>
      <td>-2.966</td>
      <td>1</td>
      <td>0.0852</td>
      <td>113.969</td>
      <td>4</td>
      <td>0.934</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>235</th>
      <td>235</td>
      <td>235</td>
      <td>6p8uRRGziFLcA3QDvWxEPM</td>
      <td>Heaven</td>
      <td>2022-04-29</td>
      <td>Dimitri Vegas &amp; Like Mike</td>
      <td>73jBynjsVtofjRpdpRAJGk</td>
      <td>68</td>
      <td>0.00812</td>
      <td>0.716</td>
      <td>...</td>
      <td>0.000268</td>
      <td>9</td>
      <td>0.1540</td>
      <td>-3.254</td>
      <td>0</td>
      <td>0.0300</td>
      <td>124.014</td>
      <td>4</td>
      <td>0.593</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>426</th>
      <td>426</td>
      <td>426</td>
      <td>6Uj1ctrBOjOas8xZXGqKk4</td>
      <td>Woman</td>
      <td>2021-06-25</td>
      <td>Doja Cat</td>
      <td>5cj0lLjcoR7YOSnhnX0Po5</td>
      <td>88</td>
      <td>0.08880</td>
      <td>0.824</td>
      <td>...</td>
      <td>0.002940</td>
      <td>5</td>
      <td>0.1170</td>
      <td>-4.175</td>
      <td>0</td>
      <td>0.0854</td>
      <td>107.998</td>
      <td>4</td>
      <td>0.881</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>651</th>
      <td>651</td>
      <td>651</td>
      <td>1l4iQsOZ5sOXZPMQLvouaB</td>
      <td>Coast (feat. Anderson .Paak)</td>
      <td>2022-07-29</td>
      <td>Hailee Steinfeld</td>
      <td>5p7f24Rk5HkUZsaS3BLG5F</td>
      <td>77</td>
      <td>0.02620</td>
      <td>0.701</td>
      <td>...</td>
      <td>0.000007</td>
      <td>11</td>
      <td>0.1370</td>
      <td>-4.1</td>
      <td>1</td>
      <td>0.0757</td>
      <td>111.976</td>
      <td>4</td>
      <td>0.840</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>727</th>
      <td>727</td>
      <td>727</td>
      <td>4h9wh7iOZ0GGn8QVp4RAOB</td>
      <td>I Ain't Worried</td>
      <td>2022-05-13</td>
      <td>OneRepublic</td>
      <td>5Pwc4xIPtQLFEnJriah9YJ</td>
      <td>94</td>
      <td>0.08260</td>
      <td>0.704</td>
      <td>...</td>
      <td>0.000745</td>
      <td>0</td>
      <td>0.0546</td>
      <td>-5.927</td>
      <td>1</td>
      <td>0.0475</td>
      <td>139.994</td>
      <td>4</td>
      <td>0.825</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>800</th>
      <td>800</td>
      <td>800</td>
      <td>5THQw2QDTWwOP1Hj543Rb2</td>
      <td>Fading Like A Flower</td>
      <td>2022-04-22</td>
      <td>Roxette</td>
      <td>2SHhfs4BiDxGQ3oxqf0UHY</td>
      <td>61</td>
      <td>0.01800</td>
      <td>0.701</td>
      <td>...</td>
      <td>0.000001</td>
      <td>5</td>
      <td>0.0665</td>
      <td>-4.34</td>
      <td>1</td>
      <td>0.0460</td>
      <td>121.022</td>
      <td>4</td>
      <td>0.908</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>833</th>
      <td>833</td>
      <td>833</td>
      <td>21AziTMaZwcLflGMAj85o5</td>
      <td>All 4 Nothing (I'm So In Love)</td>
      <td>2022-04-08</td>
      <td>Lauv</td>
      <td>5JZ7CnR6gTvEMKX4g70Amv</td>
      <td>68</td>
      <td>0.04110</td>
      <td>0.720</td>
      <td>...</td>
      <td>0.000000</td>
      <td>1</td>
      <td>0.0606</td>
      <td>-4.588</td>
      <td>1</td>
      <td>0.0362</td>
      <td>131.99</td>
      <td>4</td>
      <td>0.898</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>880</th>
      <td>880</td>
      <td>880</td>
      <td>6Ql7rNuoP90aIdgwQkPYBP</td>
      <td>Synchronize</td>
      <td>2022-03-09</td>
      <td>Milky Chance</td>
      <td>1hzfo8twXdOegF3xireCYs</td>
      <td>67</td>
      <td>0.00898</td>
      <td>0.728</td>
      <td>...</td>
      <td>0.000216</td>
      <td>11</td>
      <td>0.1760</td>
      <td>-6.198</td>
      <td>0</td>
      <td>0.0310</td>
      <td>124.001</td>
      <td>4</td>
      <td>0.652</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>953</th>
      <td>953</td>
      <td>953</td>
      <td>0ePX6rVjeql7Bt1KurTbAG</td>
      <td>What, Me Worry?</td>
      <td>2022-02-09</td>
      <td>Portugal. The Man</td>
      <td>4kI8Ie27vjvonwaB2ePh8T</td>
      <td>61</td>
      <td>0.06820</td>
      <td>0.722</td>
      <td>...</td>
      <td>0.000005</td>
      <td>3</td>
      <td>0.1240</td>
      <td>-2.564</td>
      <td>0</td>
      <td>0.0416</td>
      <td>139.965</td>
      <td>4</td>
      <td>0.743</td>
      <td>2022</td>
    </tr>
    <tr>
      <th>978</th>
      <td>978</td>
      <td>978</td>
      <td>7rglLriMNBPAyuJOMGwi39</td>
      <td>Cold Heart - PNAU Remix</td>
      <td>2021-10-22</td>
      <td>Elton John</td>
      <td>3PhoLpVuITZKcymswpck5b</td>
      <td>73</td>
      <td>0.03540</td>
      <td>0.795</td>
      <td>...</td>
      <td>0.000073</td>
      <td>1</td>
      <td>0.0915</td>
      <td>-6.32</td>
      <td>1</td>
      <td>0.0309</td>
      <td>116.032</td>
      <td>4</td>
      <td>0.934</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>1206</th>
      <td>1206</td>
      <td>1206</td>
      <td>3lUQpvfWFcxZC3RYAVGE7F</td>
      <td>I Feel Good</td>
      <td>2021-10-15</td>
      <td>Pink Sweat$</td>
      <td>1W7FNibLa0O0b572tB2w7t</td>
      <td>68</td>
      <td>0.02330</td>
      <td>0.803</td>
      <td>...</td>
      <td>0.000039</td>
      <td>11</td>
      <td>0.0450</td>
      <td>-4.67</td>
      <td>1</td>
      <td>0.0551</td>
      <td>106.0</td>
      <td>4</td>
      <td>0.628</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>1345</th>
      <td>1345</td>
      <td>1345</td>
      <td>6Dy1jexKYriXAVG6evyUTJ</td>
      <td>OUT OUT (feat. Charli XCX &amp; Saweetie)</td>
      <td>2021-08-13</td>
      <td>Joel Corry</td>
      <td>6DgP9otnZw5z6daOntINxp</td>
      <td>81</td>
      <td>0.01800</td>
      <td>0.787</td>
      <td>...</td>
      <td>0.007470</td>
      <td>8</td>
      <td>0.0374</td>
      <td>-4.403</td>
      <td>1</td>
      <td>0.0478</td>
      <td>123.97</td>
      <td>4</td>
      <td>0.796</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>1350</th>
      <td>1350</td>
      <td>1350</td>
      <td>6zSpb8dQRaw0M1dK8PBwQz</td>
      <td>Cold Heart - PNAU Remix</td>
      <td>2021-08-13</td>
      <td>Elton John</td>
      <td>3PhoLpVuITZKcymswpck5b</td>
      <td>88</td>
      <td>0.03400</td>
      <td>0.796</td>
      <td>...</td>
      <td>0.000042</td>
      <td>1</td>
      <td>0.0952</td>
      <td>-6.312</td>
      <td>1</td>
      <td>0.0317</td>
      <td>116.032</td>
      <td>4</td>
      <td>0.942</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>1370</th>
      <td>1370</td>
      <td>1370</td>
      <td>7iUB3byx9Rcks8zW10JVkP</td>
      <td>Stupid Feelings</td>
      <td>2021-08-06</td>
      <td>220 KID</td>
      <td>4Euia7UzdRshy1DJOSMTcs</td>
      <td>64</td>
      <td>0.01520</td>
      <td>0.715</td>
      <td>...</td>
      <td>0.000000</td>
      <td>6</td>
      <td>0.1210</td>
      <td>-6.546</td>
      <td>0</td>
      <td>0.0365</td>
      <td>123.954</td>
      <td>4</td>
      <td>0.756</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>1603</th>
      <td>1603</td>
      <td>1603</td>
      <td>4qu63nuBpdn0qHUHuObEj1</td>
      <td>Leave Before You Love Me (with Jonas Brothers)</td>
      <td>2021-05-21</td>
      <td>Marshmello</td>
      <td>64KEffDW9EtZ1y2vBYgq8T</td>
      <td>85</td>
      <td>0.00226</td>
      <td>0.721</td>
      <td>...</td>
      <td>0.000004</td>
      <td>7</td>
      <td>0.1180</td>
      <td>-4.77</td>
      <td>1</td>
      <td>0.0403</td>
      <td>119.976</td>
      <td>4</td>
      <td>0.637</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>2444</th>
      <td>2444</td>
      <td>2444</td>
      <td>463CkQjx2Zk1yXoBuierM9</td>
      <td>Levitating (feat. DaBaby)</td>
      <td>2020-10-01</td>
      <td>Dua Lipa</td>
      <td>6M2wZ9GZgrQXHCFfjv46we</td>
      <td>77</td>
      <td>0.00883</td>
      <td>0.702</td>
      <td>...</td>
      <td>0.000000</td>
      <td>6</td>
      <td>0.0674</td>
      <td>-3.787</td>
      <td>0</td>
      <td>0.0601</td>
      <td>102.977</td>
      <td>4</td>
      <td>0.915</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>2491</th>
      <td>2491</td>
      <td>2491</td>
      <td>0110a0AM2nOV8yYa9u6kjQ</td>
      <td>Love Me Like a Friend</td>
      <td>2019-11-22</td>
      <td>Fly By Midnight</td>
      <td>4rQTEdG6hDVOlDUFKs9EjZ</td>
      <td>61</td>
      <td>0.07180</td>
      <td>0.713</td>
      <td>...</td>
      <td>0.000000</td>
      <td>6</td>
      <td>0.0708</td>
      <td>-5.57</td>
      <td>0</td>
      <td>0.0294</td>
      <td>125.052</td>
      <td>4</td>
      <td>0.962</td>
      <td>2019</td>
    </tr>
    <tr>
      <th>3505</th>
      <td>3505</td>
      <td>3505</td>
      <td>6WrI0LAC5M1Rw2MnX2ZvEg</td>
      <td>Don't Start Now</td>
      <td>2019-10-31</td>
      <td>Dua Lipa</td>
      <td>6M2wZ9GZgrQXHCFfjv46we</td>
      <td>80</td>
      <td>0.01250</td>
      <td>0.794</td>
      <td>...</td>
      <td>0.000000</td>
      <td>11</td>
      <td>0.0952</td>
      <td>-4.521</td>
      <td>0</td>
      <td>0.0842</td>
      <td>123.941</td>
      <td>4</td>
      <td>0.677</td>
      <td>2019</td>
    </tr>
    <tr>
      <th>3683</th>
      <td>3683</td>
      <td>3683</td>
      <td>2NjO87HyT80fsgejd3PLYW</td>
      <td>Dance To This (feat. Ariana Grande)</td>
      <td>2018-08-31</td>
      <td>Troye Sivan</td>
      <td>3WGpXCj9YhhfX11TToZcXP</td>
      <td>68</td>
      <td>0.00632</td>
      <td>0.736</td>
      <td>...</td>
      <td>0.005770</td>
      <td>11</td>
      <td>0.1060</td>
      <td>-7.099</td>
      <td>1</td>
      <td>0.0470</td>
      <td>117.016</td>
      <td>4</td>
      <td>0.712</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>4494</th>
      <td>4494</td>
      <td>4494</td>
      <td>5xzCzOAOfRi4DOttSzvznR</td>
      <td>Lullaby</td>
      <td>2018-02-23</td>
      <td>Sigala</td>
      <td>1IueXOQyABrMOprrzwQJWN</td>
      <td>61</td>
      <td>0.07840</td>
      <td>0.725</td>
      <td>...</td>
      <td>0.000013</td>
      <td>5</td>
      <td>0.0834</td>
      <td>-3.913</td>
      <td>0</td>
      <td>0.0370</td>
      <td>120.026</td>
      <td>4</td>
      <td>0.859</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>4557</th>
      <td>4557</td>
      <td>4557</td>
      <td>0KsB4TwgATg88aXCMBoO3Y</td>
      <td>Breathe</td>
      <td>2017-12-01</td>
      <td>Jax Jones</td>
      <td>4Q6nIcaBED8qUel8bBx6Cr</td>
      <td>62</td>
      <td>0.02340</td>
      <td>0.722</td>
      <td>...</td>
      <td>0.000157</td>
      <td>11</td>
      <td>0.1430</td>
      <td>-5.52</td>
      <td>0</td>
      <td>0.0363</td>
      <td>125.985</td>
      <td>4</td>
      <td>0.686</td>
      <td>2017</td>
    </tr>
    <tr>
      <th>4994</th>
      <td>4994</td>
      <td>4994</td>
      <td>5bcTCxgc7xVfSaMV3RuVke</td>
      <td>Feels (feat. Pharrell Williams, Katy Perry &amp; B...</td>
      <td>2017-06-30</td>
      <td>Calvin Harris</td>
      <td>7CajNmpbOovFoOoasH2HaY</td>
      <td>80</td>
      <td>0.06420</td>
      <td>0.893</td>
      <td>...</td>
      <td>0.000000</td>
      <td>11</td>
      <td>0.0943</td>
      <td>-3.105</td>
      <td>0</td>
      <td>0.0571</td>
      <td>101.018</td>
      <td>4</td>
      <td>0.872</td>
      <td>2017</td>
    </tr>
    <tr>
      <th>5672</th>
      <td>5672</td>
      <td>5672</td>
      <td>3zkWCteF82vJwv0hRLba76</td>
      <td>Stitches</td>
      <td>2015-04-14</td>
      <td>Shawn Mendes</td>
      <td>7n2wHs1TKAczGzO7Dd2rGr</td>
      <td>83</td>
      <td>0.01520</td>
      <td>0.746</td>
      <td>...</td>
      <td>0.000000</td>
      <td>1</td>
      <td>0.0486</td>
      <td>-6.684</td>
      <td>1</td>
      <td>0.0676</td>
      <td>149.882</td>
      <td>4</td>
      <td>0.746</td>
      <td>2015</td>
    </tr>
  </tbody>
</table>
<p>24 rows × 22 columns</p>
</div>



24 songs were identified as potential candidates to be added to my party playlist based on our filter criteria.

Future steps:
- Train and deploy a machine learning model for advanced recommendations on songs
- Compare track features between my party playlist and that of a different type of playlist (ex: studying/relaxation playlist)
- Identify trends in type of genres that fit with my playlist's track features


```python

```
