

```python
import tweepy
import numpy as np
import pandas as pd
from datetime import datetime
import matplotlib.pyplot as plt
import seaborn as sns
from matplotlib import style
style.use('ggplot')

from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
analyzer = SentimentIntensityAnalyzer()

from apikeys import (twitter_consumer_key, 
                    twitter_consumer_secret, 
                    twitter_access_token, 
                    twitter_access_token_secret)
```

# Observable Trends

1. Tweets from Presidents Barack Obama and Donald trump, while not part of the initial HW instructions, have a much more positive mean sentiment (.25) than that of the major Global News Outlets (.08).
2. The scatter plot visualizing each of the 140 tweets from the seven Twitter accounts where data was pulled is not a very useful way of visualizing this data - it is too noisy and no real insights can be made from it. However, this visualization can be made more interesting if compounded with individual scatter plots for each of the seven accounts. 
3. CNN, AKA 'Fake News' - as President Donald Trump calls them, is the only account with a negative compound sentiment for their most recent 140 tweets. 


```python
auth = tweepy.OAuthHandler(twitter_consumer_key, twitter_consumer_secret)
auth.set_access_token(twitter_access_token, twitter_access_token_secret)
api = tweepy.API(auth, parser=tweepy.parsers.JSONParser())
```


```python
target_user = ("@BBC", "@CBS", "@CNN", "@FoxNews", "@nytimes", 
               "@BarackObama", "@realDonaldTrump")
```


```python
results_list = []

sentiments = []
for user in target_user:
    counter = 1
        
    oldest_tweet = None
    
    for x in range(7):
    
        public_tweets = api.user_timeline(user, max_id = oldest_tweet, page = x)
        
        for tweet in public_tweets:
            
            results = analyzer.polarity_scores(tweet["text"])
            compound = results["compound"]
            pos = results["pos"]
            neu = results["neu"]
            neg = results["neg"]
            tweets_ago = counter
            
            oldest_tweet = tweet['id'] - 1
            
            sentiments.append({
            "Date": tweet["created_at"],
            "Username": user,
            "Compound": compound,
            "Positive": pos,
            "Negative": neu,
            "Neutral": neg,
            "Tweets Ago": counter})
            
            oldest_tweet = tweet['id'] - 1
            counter += 1
```


```python
sentiments_df = pd.DataFrame.from_dict(sentiments)
sentiments_df
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
      <th>Compound</th>
      <th>Date</th>
      <th>Negative</th>
      <th>Neutral</th>
      <th>Positive</th>
      <th>Tweets Ago</th>
      <th>Username</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.5994</td>
      <td>Sun Sep 02 20:11:42 +0000 2018</td>
      <td>0.588</td>
      <td>0.000</td>
      <td>0.412</td>
      <td>1</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.0000</td>
      <td>Sun Sep 02 19:47:44 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>2</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.5719</td>
      <td>Sun Sep 02 18:08:04 +0000 2018</td>
      <td>0.802</td>
      <td>0.000</td>
      <td>0.198</td>
      <td>3</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.0000</td>
      <td>Sun Sep 02 17:06:04 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>4</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.1280</td>
      <td>Sun Sep 02 16:05:04 +0000 2018</td>
      <td>0.797</td>
      <td>0.092</td>
      <td>0.112</td>
      <td>5</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0.0000</td>
      <td>Sun Sep 02 15:24:46 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>6</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>6</th>
      <td>-0.2263</td>
      <td>Sun Sep 02 15:12:01 +0000 2018</td>
      <td>0.840</td>
      <td>0.160</td>
      <td>0.000</td>
      <td>7</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0.5106</td>
      <td>Sun Sep 02 14:06:08 +0000 2018</td>
      <td>0.549</td>
      <td>0.161</td>
      <td>0.290</td>
      <td>8</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>8</th>
      <td>0.0772</td>
      <td>Sun Sep 02 14:03:02 +0000 2018</td>
      <td>0.944</td>
      <td>0.000</td>
      <td>0.056</td>
      <td>9</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>9</th>
      <td>0.0000</td>
      <td>Sun Sep 02 13:03:02 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>10</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>10</th>
      <td>-0.3818</td>
      <td>Sun Sep 02 12:25:11 +0000 2018</td>
      <td>0.825</td>
      <td>0.175</td>
      <td>0.000</td>
      <td>11</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>11</th>
      <td>0.0000</td>
      <td>Sun Sep 02 12:24:18 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>12</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>12</th>
      <td>0.7430</td>
      <td>Sun Sep 02 11:34:00 +0000 2018</td>
      <td>0.730</td>
      <td>0.000</td>
      <td>0.270</td>
      <td>13</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>13</th>
      <td>0.7684</td>
      <td>Sun Sep 02 11:03:19 +0000 2018</td>
      <td>0.712</td>
      <td>0.000</td>
      <td>0.288</td>
      <td>14</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>14</th>
      <td>0.0000</td>
      <td>Sun Sep 02 10:20:05 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>15</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>15</th>
      <td>-0.0516</td>
      <td>Sun Sep 02 10:07:06 +0000 2018</td>
      <td>0.893</td>
      <td>0.107</td>
      <td>0.000</td>
      <td>16</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>16</th>
      <td>0.0000</td>
      <td>Sun Sep 02 09:09:03 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>17</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>17</th>
      <td>-0.2960</td>
      <td>Sun Sep 02 08:11:03 +0000 2018</td>
      <td>0.820</td>
      <td>0.180</td>
      <td>0.000</td>
      <td>18</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>18</th>
      <td>0.0000</td>
      <td>Sun Sep 02 07:05:06 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>19</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>19</th>
      <td>0.8020</td>
      <td>Sat Sep 01 19:55:23 +0000 2018</td>
      <td>0.676</td>
      <td>0.000</td>
      <td>0.324</td>
      <td>20</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>20</th>
      <td>-0.5859</td>
      <td>Sat Sep 01 18:47:18 +0000 2018</td>
      <td>0.798</td>
      <td>0.202</td>
      <td>0.000</td>
      <td>21</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>21</th>
      <td>-0.4588</td>
      <td>Sat Sep 01 18:09:04 +0000 2018</td>
      <td>0.786</td>
      <td>0.214</td>
      <td>0.000</td>
      <td>22</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>22</th>
      <td>0.6258</td>
      <td>Sat Sep 01 17:02:06 +0000 2018</td>
      <td>0.718</td>
      <td>0.000</td>
      <td>0.282</td>
      <td>23</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>23</th>
      <td>0.1280</td>
      <td>Sat Sep 01 16:07:05 +0000 2018</td>
      <td>0.880</td>
      <td>0.000</td>
      <td>0.120</td>
      <td>24</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>24</th>
      <td>0.0000</td>
      <td>Sat Sep 01 15:02:06 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>25</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>25</th>
      <td>0.5262</td>
      <td>Sat Sep 01 14:43:14 +0000 2018</td>
      <td>0.815</td>
      <td>0.000</td>
      <td>0.185</td>
      <td>26</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>26</th>
      <td>0.7184</td>
      <td>Sat Sep 01 14:10:03 +0000 2018</td>
      <td>0.750</td>
      <td>0.000</td>
      <td>0.250</td>
      <td>27</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>27</th>
      <td>0.4019</td>
      <td>Sat Sep 01 13:04:05 +0000 2018</td>
      <td>0.690</td>
      <td>0.000</td>
      <td>0.310</td>
      <td>28</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>28</th>
      <td>0.6369</td>
      <td>Sat Sep 01 12:06:02 +0000 2018</td>
      <td>0.724</td>
      <td>0.000</td>
      <td>0.276</td>
      <td>29</td>
      <td>@BBC</td>
    </tr>
    <tr>
      <th>29</th>
      <td>-0.4767</td>
      <td>Sat Sep 01 11:39:44 +0000 2018</td>
      <td>0.846</td>
      <td>0.154</td>
      <td>0.000</td>
      <td>30</td>
      <td>@BBC</td>
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
      <th>950</th>
      <td>0.0000</td>
      <td>Mon Aug 06 19:13:42 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>111</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>951</th>
      <td>0.3400</td>
      <td>Mon Aug 06 17:43:57 +0000 2018</td>
      <td>0.704</td>
      <td>0.104</td>
      <td>0.193</td>
      <td>112</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>952</th>
      <td>0.8591</td>
      <td>Mon Aug 06 15:48:40 +0000 2018</td>
      <td>0.667</td>
      <td>0.000</td>
      <td>0.333</td>
      <td>113</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>953</th>
      <td>0.7842</td>
      <td>Mon Aug 06 14:52:46 +0000 2018</td>
      <td>0.614</td>
      <td>0.087</td>
      <td>0.300</td>
      <td>114</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>954</th>
      <td>0.0000</td>
      <td>Mon Aug 06 14:25:56 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>115</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>955</th>
      <td>0.0000</td>
      <td>Mon Aug 06 14:13:10 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>116</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>956</th>
      <td>0.0000</td>
      <td>Sun Aug 05 21:53:11 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>117</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>957</th>
      <td>0.9151</td>
      <td>Sun Aug 05 21:52:57 +0000 2018</td>
      <td>0.617</td>
      <td>0.000</td>
      <td>0.383</td>
      <td>118</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>958</th>
      <td>0.9148</td>
      <td>Sun Aug 05 20:01:17 +0000 2018</td>
      <td>0.504</td>
      <td>0.000</td>
      <td>0.496</td>
      <td>119</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>959</th>
      <td>-0.8834</td>
      <td>Sun Aug 05 12:49:21 +0000 2018</td>
      <td>0.627</td>
      <td>0.373</td>
      <td>0.000</td>
      <td>120</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>960</th>
      <td>0.3612</td>
      <td>Mon Jul 30 12:06:39 +0000 2018</td>
      <td>0.872</td>
      <td>0.000</td>
      <td>0.128</td>
      <td>121</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>961</th>
      <td>0.6369</td>
      <td>Mon Jul 30 11:57:34 +0000 2018</td>
      <td>0.785</td>
      <td>0.000</td>
      <td>0.215</td>
      <td>122</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>962</th>
      <td>0.0000</td>
      <td>Sun Jul 29 23:04:20 +0000 2018</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>0.000</td>
      <td>123</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>963</th>
      <td>-0.5106</td>
      <td>Sun Jul 29 20:20:39 +0000 2018</td>
      <td>0.845</td>
      <td>0.155</td>
      <td>0.000</td>
      <td>124</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>964</th>
      <td>0.5423</td>
      <td>Sun Jul 29 20:12:15 +0000 2018</td>
      <td>0.661</td>
      <td>0.101</td>
      <td>0.237</td>
      <td>125</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>965</th>
      <td>-0.7574</td>
      <td>Sun Jul 29 19:35:14 +0000 2018</td>
      <td>0.706</td>
      <td>0.294</td>
      <td>0.000</td>
      <td>126</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>966</th>
      <td>0.8947</td>
      <td>Sun Jul 29 19:09:19 +0000 2018</td>
      <td>0.626</td>
      <td>0.000</td>
      <td>0.374</td>
      <td>127</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>967</th>
      <td>0.2023</td>
      <td>Sun Jul 29 19:09:18 +0000 2018</td>
      <td>0.652</td>
      <td>0.159</td>
      <td>0.188</td>
      <td>128</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>968</th>
      <td>-0.7808</td>
      <td>Sun Jul 29 19:09:18 +0000 2018</td>
      <td>0.687</td>
      <td>0.313</td>
      <td>0.000</td>
      <td>129</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>969</th>
      <td>-0.4019</td>
      <td>Sun Jul 29 19:09:17 +0000 2018</td>
      <td>0.856</td>
      <td>0.144</td>
      <td>0.000</td>
      <td>130</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>970</th>
      <td>0.3400</td>
      <td>Sun Jul 29 13:13:59 +0000 2018</td>
      <td>0.897</td>
      <td>0.000</td>
      <td>0.103</td>
      <td>131</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>971</th>
      <td>0.6597</td>
      <td>Sun Jul 29 12:42:52 +0000 2018</td>
      <td>0.660</td>
      <td>0.094</td>
      <td>0.247</td>
      <td>132</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>972</th>
      <td>0.7316</td>
      <td>Sun Jul 29 12:30:04 +0000 2018</td>
      <td>0.755</td>
      <td>0.000</td>
      <td>0.245</td>
      <td>133</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>973</th>
      <td>0.8707</td>
      <td>Sun Jul 29 12:00:13 +0000 2018</td>
      <td>0.619</td>
      <td>0.000</td>
      <td>0.381</td>
      <td>134</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>974</th>
      <td>0.2732</td>
      <td>Sun Jul 29 11:59:30 +0000 2018</td>
      <td>0.905</td>
      <td>0.000</td>
      <td>0.095</td>
      <td>135</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>975</th>
      <td>-0.7236</td>
      <td>Sun Jul 29 11:59:26 +0000 2018</td>
      <td>0.595</td>
      <td>0.291</td>
      <td>0.114</td>
      <td>136</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>976</th>
      <td>0.3182</td>
      <td>Sun Jul 29 11:58:09 +0000 2018</td>
      <td>0.881</td>
      <td>0.000</td>
      <td>0.119</td>
      <td>137</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>977</th>
      <td>-0.4767</td>
      <td>Sun Jul 29 11:52:49 +0000 2018</td>
      <td>0.829</td>
      <td>0.171</td>
      <td>0.000</td>
      <td>138</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>978</th>
      <td>0.8689</td>
      <td>Sun Jul 29 11:44:00 +0000 2018</td>
      <td>0.634</td>
      <td>0.000</td>
      <td>0.366</td>
      <td>139</td>
      <td>@realDonaldTrump</td>
    </tr>
    <tr>
      <th>979</th>
      <td>0.6666</td>
      <td>Sun Jul 29 11:31:52 +0000 2018</td>
      <td>0.777</td>
      <td>0.000</td>
      <td>0.223</td>
      <td>140</td>
      <td>@realDonaldTrump</td>
    </tr>
  </tbody>
</table>
<p>980 rows × 7 columns</p>
</div>




```python
sentiments_df.to_csv('twitter_sentiment_data.csv', encoding='utf-8')
```


```python
sentiments_BBC_df = sentiments_df.loc[sentiments_df['Username'] == '@BBC']
sentiments_CBS_df = sentiments_df.loc[sentiments_df['Username'] == '@CBS']
sentiments_NYT_df = sentiments_df.loc[sentiments_df['Username'] == '@nytimes']
sentiments_FXN_df = sentiments_df.loc[sentiments_df['Username'] == '@FoxNews']
sentiments_CNN_df = sentiments_df.loc[sentiments_df['Username'] == '@CNN']
sentiments_DJT_df = sentiments_df.loc[sentiments_df['Username'] == '@realDonaldTrump']
sentiments_BHO_df = sentiments_df.loc[sentiments_df['Username'] == '@BarackObama']
```


```python
plt.figure(figsize=(15,10))

plt.plot(sentiments_BBC_df['Tweets Ago'],
         sentiments_BBC_df['Compound'], marker="o", linewidth=0,
         alpha=0.6, color='purple', label='BBC', markeredgecolor = 'black', ms=12)

plt.plot(sentiments_CBS_df['Tweets Ago'],
         sentiments_CBS_df['Compound'], marker="o", linewidth=0,
         alpha=0.6, color='green', label='CBS', markeredgecolor = 'black', ms=12)

plt.plot(sentiments_NYT_df['Tweets Ago'],
         sentiments_NYT_df['Compound'], marker="o", linewidth=0,
         alpha=0.6, color='black', label='NYT', markeredgecolor = 'black', ms=12)

plt.plot(sentiments_FXN_df['Tweets Ago'],
         sentiments_FXN_df['Compound'], marker="o", linewidth=0,
         alpha=0.6, color='red', label='FOX', markeredgecolor = 'black', ms=12)

plt.plot(sentiments_CNN_df['Tweets Ago'],
         sentiments_CNN_df['Compound'], marker="o", linewidth=0,
         alpha=0.6, color='yellow', label='CNN', markeredgecolor = 'black', ms=12)

plt.plot(sentiments_DJT_df['Tweets Ago'],
         sentiments_DJT_df['Compound'], marker="o", linewidth=0,
         alpha=0.6, color='orange', label='Donald', markeredgecolor = 'black', ms=12)

plt.plot(sentiments_BHO_df['Tweets Ago'],
         sentiments_BHO_df['Compound'], marker="o", linewidth=0,
         alpha=0.6, color='blue', label='Barack', markeredgecolor = 'black', ms=12)

now = datetime.now()
now = now.strftime("%Y-%m-%d %H:%M")
plt.title("Media and Presidential Sentiment Analysis of Tweets on 140 Most Recent Tweets")
plt.ylabel("Tweet Polarity")
plt.xlabel("Tweets Ago")
ax = plt.subplot(111)
ax.legend(loc='center left', bbox_to_anchor=(1, 0.5))
plt.savefig("140MostRecentTweetsSentiment.png")
plt.show()

```

    /anaconda3/envs/PythonData/lib/python3.6/site-packages/matplotlib/cbook/deprecation.py:106: MatplotlibDeprecationWarning: Adding an axes using the same arguments as a previous axes currently reuses the earlier instance.  In a future version, a new instance will always be created and returned.  Meanwhile, this warning can be suppressed, and the future behavior ensured, by passing a unique label to each axes instance.
      warnings.warn(message, mplDeprecation, stacklevel=1)



![png](output_9_1.png)



```python
sentiments_compound_df = sentiments_df.groupby('Username').mean()
sentiments_compound_df
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
      <th>Compound</th>
      <th>Negative</th>
      <th>Neutral</th>
      <th>Positive</th>
      <th>Tweets Ago</th>
    </tr>
    <tr>
      <th>Username</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>@BBC</th>
      <td>0.142629</td>
      <td>0.863264</td>
      <td>0.037200</td>
      <td>0.099571</td>
      <td>70.5</td>
    </tr>
    <tr>
      <th>@BarackObama</th>
      <td>0.295181</td>
      <td>0.782257</td>
      <td>0.046079</td>
      <td>0.171664</td>
      <td>70.5</td>
    </tr>
    <tr>
      <th>@CBS</th>
      <td>0.277949</td>
      <td>0.841743</td>
      <td>0.025586</td>
      <td>0.132664</td>
      <td>70.5</td>
    </tr>
    <tr>
      <th>@CNN</th>
      <td>-0.054028</td>
      <td>0.841207</td>
      <td>0.093221</td>
      <td>0.065579</td>
      <td>70.5</td>
    </tr>
    <tr>
      <th>@FoxNews</th>
      <td>0.002624</td>
      <td>0.833979</td>
      <td>0.083064</td>
      <td>0.082971</td>
      <td>70.5</td>
    </tr>
    <tr>
      <th>@nytimes</th>
      <td>0.033346</td>
      <td>0.865643</td>
      <td>0.059150</td>
      <td>0.075186</td>
      <td>70.5</td>
    </tr>
    <tr>
      <th>@realDonaldTrump</th>
      <td>0.201181</td>
      <td>0.781907</td>
      <td>0.066514</td>
      <td>0.151607</td>
      <td>70.5</td>
    </tr>
  </tbody>
</table>
</div>




```python
sentiments_compound_df = sentiments_compound_df.drop(['Negative', 'Neutral', 'Positive', 
                                                      'Tweets Ago'], axis=1)
sentiments_compound_df
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
      <th>Compound</th>
    </tr>
    <tr>
      <th>Username</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>@BBC</th>
      <td>0.142629</td>
    </tr>
    <tr>
      <th>@BarackObama</th>
      <td>0.295181</td>
    </tr>
    <tr>
      <th>@CBS</th>
      <td>0.277949</td>
    </tr>
    <tr>
      <th>@CNN</th>
      <td>-0.054028</td>
    </tr>
    <tr>
      <th>@FoxNews</th>
      <td>0.002624</td>
    </tr>
    <tr>
      <th>@nytimes</th>
      <td>0.033346</td>
    </tr>
    <tr>
      <th>@realDonaldTrump</th>
      <td>0.201181</td>
    </tr>
  </tbody>
</table>
</div>




```python
sentiments_compound_df.plot(kind='bar', 
                            title='Overall Media and Presidential Sentiment Based on Twitter',
                            legend=False)
plt.savefig("OverallTwitterSentiments.png")
plt.ylabel("Tweet Polarity")
```




    Text(12.5,0.5,'Tweet Polarity')




![png](output_12_1.png)

