---
title: 巨量資料認知服務的 Scala 範例
description: 使用認知服務，讓 Azure Databricks 可執行巨量資料的 MMLSpark 管線。
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: c47aa803774343b39efeabe3452f1b256cc64c0d
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363267"
---
# <a name="quick-examples"></a>快速範例

下列程式碼片段已可供執行，將協助您開始使用 Spark 上的認知服務。 下列範例位於 Scala 中。

範例會使用這些認知服務：

- 文字分析 - 取得一組句子的情感 (或情緒)。
- 電腦視覺 - 取得與一組影像相關聯的標記 (單字描述)。
- Bing 影像搜尋 - 在 Web 上搜尋與自然語言查詢相關的影像。
- 語音轉換文字 - 轉譯音訊檔案，以擷取文字記錄。
- 異常偵測器 - 偵測時間序列資料中的異常。

## <a name="prerequisites"></a>必要條件

1. 依照[使用者入門](getting-started.md)中的步驟，設定您的 Azure Databricks 和認知服務環境。 本教學課程將涵蓋如何安裝 MMLSpark，以及如何在 Databricks 中建立 Spark 叢集。
1. 在 Azure Databricks 中建立新的筆記本之後，請複製下面的 **共用程式碼** ，並貼到您筆記本中的新儲存格。
1. 選擇下方的服務範例，並將其貼到您筆記本中的第二個新儲存格。
1. 以您自己的金鑰取代任何服務訂用帳戶金鑰的預留位置。
1. 選擇儲存格右上角的 [執行] 按鈕 (三角形圖示)，然後選取 [執行儲存格]。
1. 在資料格下方的資料表中查看結果。

## <a name="shared-code"></a>共用程式碼
若要開始使用，請將此程式碼新增至您的專案：

```scala
import com.microsoft.ml.spark.cognitive._
import spark.implicits._ 

val serviceKey = "ADD-YOUR-SUBSCRIPTION-KEY"
val location = "eastus"
```

## <a name="text-analytics"></a>文字分析

[文字分析](../text-analytics/index.yml)服務提供數種演算法，可從文字中擷取智慧型深入解析。 例如，我們可以找到指定輸入文字的情感。 服務會傳回介於 `0.0` 和 `1.0` 之間的分數，低分表示負面情感，而高分表示正面情感。  下列範例會使用三個簡單的句子，並傳回每個句子的情感分數。

```scala
import org.apache.spark.sql.functions.col

val df = Seq(
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US")
).toDF("text", "language")

val sentiment = new TextSentiment()
    .setTextCol("text")
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language")

display(sentiment.transform(df).select(col("text"), col("sentiment")(0).getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>預期的結果

| text                                      | 情感                                             |
|:------------------------------------------|:------------------------------------------------------|
| 今天我很高興，天氣真晴朗！(I am so happy today, its sunny!)           | 0.9789592027664185                                    |
| 我因為此尖峰時間的交通感到沮喪 (I am frustrated by this rush hour traffic) | 0.023795604705810547                                  |
| Spark 上的認知服務不佳 (The cognitive services on spark aint bad)  | 0.8888956308364868                                    |

## <a name="computer-vision"></a>電腦視覺

[電腦視覺](../computer-vision/index.yml)會分析影像，以識別臉部、物件和自然語言描述等結構。
在此範例中，我們會標記一系列影像。 標記是影像中事項的單字描述，例如可辨識的物件、人員、景象和動作。

```scala
// Create a dataframe with the image URLs
val df = Seq(
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg")
).toDF("image")

// Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
val analysis = new AnalyzeImage()
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setVisualFeatures(Seq("Categories","Color","Description","Faces","Objects","Tags"))
    .setOutputCol("results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

// Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select(col("image"), col("results").getItem("tags").getItem("name")).alias("results")))

// Uncomment for full results with all visual feature requests
//display(analysis.transform(df).select(col("image"), col("results")))
``` 

### <a name="expected-result"></a>預期的結果

| image | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ['skating' 'person' 'man' 'outdoor' 'riding' 'sport' 'skateboard' 'young' 'board' 'shirt' 'air' 'black' 'park' 'boy' 'side' 'jumping' 'trick' 'ramp' 'doing' 'flying']
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ['dog' 'outdoor' 'fence' 'wooden' 'small' 'brown' 'building' 'sitting' 'front' 'bench' 'standing' 'table' 'walking' 'board' 'beach' 'white' 'holding' 'bridge' 'track']                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ['outdoor' 'grass' 'house' 'building' 'old' 'home' 'front' 'small' 'church' 'stone' 'large' 'grazing' 'yard' 'green' 'sitting' 'leading' 'sheep' 'brick' 'bench' 'street' 'white' 'country' 'clock' 'sign' 'parked' 'field' 'standing' 'garden' 'water' 'red' 'horse' 'man' 'tall' 'fire' 'group'] 

## <a name="bing-image-search"></a>Bing 影像搜尋

[Bing 影像搜尋](../bing-image-search/overview.md)會搜尋 Web，以擷取與使用者的自然語言查詢相關的影像。 在此範例中，我們會使用文字查詢來尋找引號中的影像。 其會傳回影像 URL 清單，其中包含與查詢相關的相片。


```scala
import org.apache.spark.ml.Pipeline

// Number of images Bing will return per query
val imgsPerBatch = 10

// A list of offsets, used to page into the search results
val df = (0 until 100).map(o => Tuple1(o*imgsPerBatch)).toSeq.toDF("offset")

// Run the Bing Image Search service with our text query
val bingSearch = new BingImageSearch()
    .setSubscriptionKey(bingSearchKey)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images")

// Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
val getUrls = BingImageSearch.getUrlTransformer("images", "url")

// This displays the full results returned, uncomment to use
// display(bingSearch.transform(bingParameters))

// Since we have two services, they are put into a pipeline
val pipeline = new Pipeline().setStages(Array(bingSearch, getUrls))

// Show the results of your search: image URLs
display(pipeline.fit(df).transform(df))
```

### <a name="expected-result"></a>預期的結果

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |

## <a name="speech-to-text"></a>語音轉文字

[語音轉換文字](../speech-service/index-speech-to-text.yml)服務會將說話音訊的串流或檔案轉換成文字。 在此範例中，我們會轉譯兩個音訊檔案。 第一個檔案很容易了解，第二個則較具挑戰性。

```scala
import org.apache.spark.sql.functions.col

// Create a dataframe with audio URLs, tied to the column called "url"
val df = Seq(("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav"),
             ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3")).toDF("url")

// Run the Speech-to-text service to translate the audio into text
val speechToText = new SpeechToTextSDK()
    .setSubscriptionKey(serviceKey)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked")

// Show the results of the translation
display(speechToText.transform(df).select(col("url"), col("text").getItem("DisplayText")))
```

### <a name="expected-result"></a>預期的結果

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | 自訂語音提供的工具可讓您比較音訊資料與自訂語音入口網站中的對應辨識結果，透過視覺化方式檢查模型的辨識品質。(Custom speech provides tools that allow you to visually inspect the recognition quality of a model by comparing audio data with the corresponding recognition result from the custom speech portal.) 您可以播放上傳的音訊，並判斷提供的辨識結果是否正確。(You can playback uploaded audio and determine if the provided recognition result is correct.) 此工具可讓您快速檢查 Microsoft 的基準語音轉換文字模型品質或定型的自訂模型，而不需要轉譯任何音訊資料。(This tool allows you to quickly inspect quality of Microsoft's baseline speech to text model or a trained custom model without having to transcribe any audio data.) |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 新增男士思考的視覺效果檢查。(Add a gentleman Sir thinking visual check.)    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 我聽到了。(I hear me.) |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 我喜歡收音機的可靠性，我也可以聽到了。(I like the reassurance for radio that I can hear it as well.) |

## <a name="anomaly-detector"></a>異常偵測器

[異常偵測器](../anomaly-detector/index.yml)非常適合用來偵測時間序列資料中的異常狀況。 在此範例中，我們會使用該服務來尋找整個時間序列中的異常。

```scala
import org.apache.spark.sql.functions.{col, lit}

val anomalyKey = "84a2c303cc7e49f6a44d692c27fb9967"

val df = Seq(
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0),
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0)
  ).toDF("timestamp", "value").withColumn("group", lit("series1"))

// Run the Anomaly Detector service to look for irregular data
val anamolyDetector = new SimpleDetectAnomalies()
    .setSubscriptionKey(anomalyKey)
    .setLocation("eastus")
    .setTimestampCol("timestamp")
    .setValueCol("value")
    .setOutputCol("anomalies")
    .setGroupbyCol("group")
    .setGranularity("monthly")

// Show the full results of the analysis with the anomalies marked as "True"
display(anamolyDetector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
```

### <a name="expected-result"></a>預期的結果

| timestamp            |   value | 是否異常   |
|:---------------------|--------:|:------------|
| 1972-01-01T00:00:00Z |     826 | False       |
| 1972-02-01T00:00:00Z |     799 | False       |
| 1972-03-01T00:00:00Z |     890 | False       |
| 1972-04-01T00:00:00Z |     900 | False       |
| 1972-05-01T00:00:00Z |     766 | False       |
| 1972-06-01T00:00:00Z |     805 | False       |
| 1972-07-01T00:00:00Z |     821 | False       |
| 1972-08-01T00:00:00Z |   20000 | True        |
| 1972-09-01T00:00:00Z |     883 | False       |
| 1972-10-01T00:00:00Z |     898 | False       |
| 1972-11-01T00:00:00Z |     957 | False       |
| 1972-12-01T00:00:00Z |     924 | False       |
| 1973-01-01T00:00:00Z |     881 | False       |
| 1973-02-01T00:00:00Z |     837 | False       |
| 1973-03-01T00:00:00Z |    9000 | True        |