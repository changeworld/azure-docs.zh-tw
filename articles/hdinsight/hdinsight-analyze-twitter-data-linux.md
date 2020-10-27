---
title: 使用 Apache Hive 分析 Twitter 資料 - Azure HDInsight
description: 了解如何使用 HDInsight 上的 Apache Hive 與 Apache Hadoop，將原始 Twitter 資料轉換成可搜尋的 Hive 資料表。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: fe511ed2d6b724c1215f9986c9d6c50aae076935
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533286"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>在 HDInsight 上使用 Apache Hive 與 Apache Hadoop 分析 Twitter 資料

瞭解如何使用 [Apache Hive](https://hive.apache.org/) 來處理 Twitter 資料。 結果是一份傳送了最多包含特定文字之推文的 Twitter 使用者清單。

> [!IMPORTANT]  
> 本文件中的步驟已在 HDInsight 3.6 上進行過測試。

## <a name="get-the-data"></a>取得資料

Twitter 可讓您透過 REST API 抓取每則推文資料，作為 JavaScript 物件標記法 (JSON)。 [OAuth](https://oauth.net) (英文)。

### <a name="create-a-twitter-application"></a>建立 Twitter 應用程式

1. 從網頁瀏覽器登入 [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/) 。 如果您沒有 Twitter 帳戶，請選取 [ **立即註冊** ] 連結。

2. 選取 [ **建立新的應用程式** ]。

3. 輸入 [名稱]  、[說明]  、[網站]  。 您可以在 [網站]  欄位中自行設定 URL。 下表列出部分要使用的範例值：

   | 欄位 | 值 |
   |--- |--- |
   | 名稱 |MyHDInsightApp |
   | 描述 |MyHDInsightApp |
   | 網站 |`https://www.myhdinsightapp.com` |

4. 選取 **[是，我同意]** ，然後選取 [ **建立您的 Twitter 應用程式** ]。

5. 選取 [ **許可權** ] 索引標籤。預設許可權是 **唯讀** 的。

6. 選取 [ **金鑰和存取權杖** ] 索引標籤。

7. 選取 [ **建立我的存取權杖** ]。

8. 選取頁面右上角的 [ **測試 OAuth** ]。

9. 記下 **消費者金鑰** 、 **消費者祕密** 、 **存取權杖** 和 **存取權杖祕密** 。

### <a name="download-tweets"></a>下載的推文

下列 Python 程式碼會從 Twitter 下載 10,000 則推文，並儲存到名為 **tweets.txt** 的檔案。

> [!NOTE]  
> 由於已安裝 Python，下列步驟會在 HDInsight 叢集上執行。

1. 使用 [ssh 命令](./hdinsight-hadoop-linux-use-ssh-unix.md)來連線到您的叢集。 編輯以下命令並將 CLUSTERNAME 取代為您叢集的名稱，然後輸入命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 使用下列命令來安裝 [Tweepy](https://www.tweepy.org/)、 [進度](https://pypi.python.org/pypi/progressbar/2.2)列和其他必要套件：

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. 使用以下命令建立名為 **gettweets.py** 的檔案：

   ```bash
   nano gettweets.py
   ```

1. `Your consumer secret` `Your consumer key` `Your access token` `Your access token secret` 以您的 twitter 應用程式中的相關資訊取代、、和，以編輯下列程式碼。 然後，將編輯過的程式碼貼入 **gettweets.py** 檔案的內容。

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=100

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!TIP]  
    > 調整最後一行上的主題篩選條件，以追蹤熱門關鍵字。 使用您執行指令碼當時熱門的關鍵字，可以更快擷取資料。

1. 依序按 **Ctrl + X** ，然後 **Y** 儲存檔案。

1. 使用以下命令執行檔案，並下載推文：

    ```bash
    python gettweets.py
    ```

    進度指示器隨即出現。 隨著推文的下載，其進度會推進到 100%。

   > [!NOTE]  
   > 如果需要花費很長的時間來讓進度列往前移動，則您應該變更篩選來追蹤趨勢主題。 當您篩選中的主題有許多推文時，您可以快速取得所需的100推文。

### <a name="upload-the-data"></a>上傳資料

若要將資料下載到 HDInsight 儲存體，請使用下列命令：

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

這些命令會將資料儲存在叢集中的所有節點都能存取的位置。

## <a name="run-the-hiveql-job"></a>執行 HiveQL 工作

1. 使用下列命令來建立包含 [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) 語句的檔案：

   ```bash
   nano twitter.hql
   ```

    使用下列文字做為檔案的內容：

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

1. 依序按 **Ctrl + X** ，然後 **Y** 儲存檔案。

1. 使用以下命令執行包含於檔案中的 HiveQL：

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    此命令會執行 **twitter.hql** 檔案。 當查詢完成時，您會看到 `jdbc:hive2//localhost:10001/>` 提示字元。

1. 從 beeline 提示字元中，使用下列查詢來確認資料已匯入︰

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    這個查詢會傳回最多 10 則訊息內容中包含文字 **Azure** 的推文。

    > [!NOTE]  
    > 如果您變更了 `gettweets.py` 指令碼中的篩選條件，請將 **Azure** 取代為您使用的其中一個篩選條件。

## <a name="next-steps"></a>下一步

您已瞭解如何將非結構化的 JSON 資料集轉換成結構化的 [Apache Hive](https://hive.apache.org/) 資料表。 若要深入了解 HDInsight 上的 Hive，請參閱下列文件：

* [開始使用 HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [使用 HDInsight 分析航班延誤資料](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)