---
title: 教學課程：使用 Azure Databricks 對串流資料進行異常情況偵測
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Anomaly Detector API 和 Azure Databricks 來監視資料中的異常狀況。
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: d3f3842265e0c8a36c7eb4b14abca771bd3d38f2
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85918937"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>教學課程：使用 Azure Databricks 對串流資料進行異常情況偵測

[Azure Databricks](https://azure.microsoft.com/services/databricks/) 是快速、簡單且共同作業的 Apache Spark 架構分析服務。 Anomaly Detector API 是 Azure 認知服務的組件，可供監視您的時間序列資料。 在本教學課程中，使用 Azure Databricks 對資料流執行近乎即時的異常偵測。 您將使用 Azure 事件中樞內嵌推文資料，並使用 Spark 事件中樞連接器將其匯入 Azure Databricks 中。 之後，您將使用 API 來偵測串流資料的異常狀況。 

下圖顯示此應用程式流程：

![搭配事件中樞和認知服務的 Azure Databricks](../media/tutorials/databricks-cognitive-services-tutorial.png "搭配事件中樞和認知服務的 Azure Databricks")

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 建立 Azure Databricks 工作區
> * 在 Azure Databricks 中建立 Spark 叢集
> * 建立 Twitter 應用程式來存取串流資料
> * 在 Azure Databricks 中建立 Notebook
> * 連結事件中樞與 Twitter API 的程式庫
> * 建立 Anomaly Detector 資源並擷取存取金鑰
> * 將推文傳送至事件中樞
> * 從事件中樞讀取推文
> * 對推文執行異常偵測

> [!Note]
> * 本教學課程介紹一種方法來對 Anomaly Detector API 實作建議的[解決方案架構](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/)。
> * 您無法使用 Anomaly Detector API 或 Azure Databricks 的免費試用完成教學課程。 

如果您沒有 Azure 訂用帳戶，請建立一個 [Azure 訂用帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

- [Azure 事件中樞命名空間](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)和事件中樞。

- 用來存取事件中樞命名空間的[連接字串](../../../event-hubs/event-hubs-get-connection-string.md)。 連接字串的格式應類似於：

    第 1 課：建立 Windows Azure 儲存體物件`Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`。 

- 事件中樞的共用存取原則名稱和原則金鑰。

如需建立命名空間和事件中樞的相關資訊，請參閱 Azure 事件中樞[快速入門](../../../event-hubs/event-hubs-create.md)。

## <a name="create-an-azure-databricks-workspace"></a>建立 Azure Databricks 工作區

在本節中，您會使用 [Azure 入口網站](https://portal.azure.com/)建立 Azure Databricks 工作區。

1. 在 Azure 入口網站中，選取 [建立資源]   > [分析]   > [Azure Databricks]  。

    ![Azure 入口網站上的 Databricks](../media/tutorials/azure-databricks-on-portal.png "Azure 入口網站上的 Databricks")

3. 在 [Azure Databricks 服務]  下方提供下列值，以建立 Databricks 工作區：


    |屬性  |描述  |
    |---------|---------|
    |**工作區名稱**     | 提供您 Databricks 工作區的名稱        |
    |**訂用帳戶**     | 從下拉式清單中選取您的 Azure 訂用帳戶。        |
    |**資源群組**     | 指定您是要建立新的資源群組，還是使用現有資源群組。 資源群組是存放 Azure 方案相關資源的容器。 如需詳細資訊，請參閱 [Azure 資源群組概觀](../../../azure-resource-manager/management/overview.md)。 |
    |**位置**     | 選取 [美國東部 2]  或其中一個任何其他可用的區域。 如需可用的區域，請參閱[依區域提供的 Azure 服務](https://azure.microsoft.com/regions/services/)。        |
    |定價層      |  選擇 [標準]  或 [進階]  。 請勿選擇 [試用版]  。 如需這些定價層的詳細資訊，請參閱 [Databricks 定價頁面](https://azure.microsoft.com/pricing/details/databricks/)。       |

    選取 [建立]  。

4. 工作區建立需要幾分鐘的時間。 

## <a name="create-a-spark-cluster-in-databricks"></a>在 Databricks 中建立 Spark 叢集

1. 在 Azure 入口網站中，移至您所建立的 Databricks 工作區，然後選取 [啟動工作區]  。

2. 系統會將您重新導向至 Azure Databricks 入口網站。 在入口網站中，選取 [新增叢集]  。

    ![Azure 上的 Databricks](../media/tutorials/databricks-on-azure.png "Azure 上的 Databricks")

3. 在 [新增叢集]  頁面中，提供一些值以建立叢集。

    ![在 Azure 上建立 Databricks Spark 叢集](../media/tutorials/create-databricks-spark-cluster.png "在 Azure 上建立 Databricks Spark 叢集")

    接受下列值以外的所有其他預設值：

   * 輸入叢集的名稱。
   * 針對本文，使用 **5.2** 執行階段建立叢集。 請勿選取 **5.3** 執行階段。
   * 請確定已選取 [在活動\_\_分鐘後終止]  核取方塊。 若未使用叢集，請提供據以終止叢集的持續時間 (以分鐘為單位)。

     選取 [建立叢集]  。 
4. 叢集建立可能需要數分鐘的時間。 叢集在執行後，您就可以將 Notebook 連結至叢集，並執行 Spark 作業。

## <a name="create-a-twitter-application"></a>建立 Twitter 應用程式

若要收到推文的串流，您必須在 Twitter 中建立應用程式。 請依照下列步驟建立 Twitter 應用程式，並記錄要完成本教學課程所需的值。

1. 從網頁瀏覽器移至 [Twitter 應用程式管理](https://apps.twitter.com/) ，然後選取 [建立新的應用程式]  。

    ![建立 Twitter 應用程式](../media/tutorials/databricks-create-twitter-app.png "建立 Twitter 應用程式")

2. 在 [建立應用程式]  頁面上，提供新應用程式的詳細資料，然後選取 [建立 Twitter 應用程式]  。

    ![Twitter 應用程式詳細資料](../media/tutorials/databricks-provide-twitter-app-details.png "Twitter 應用程式詳細資料")

3. 在應用程式頁面上，選取 [金鑰和存取權杖]  索引標籤，並複製 [取用者金鑰]  和 [取用者秘密]  的值。 此外，請選取 [建立我的存取權杖]  來產生存取權杖。 複製 [存取權杖]  和 [存取權杖祕密]  的值。

    ![Twitter 應用程式詳細資料](../media/tutorials/twitter-app-key-secret.png "Twitter 應用程式詳細資料")

儲存您所擷取的 Twitter 應用程式值。 在本教學課程後續的內容中，您會需要這些值。

## <a name="attach-libraries-to-spark-cluster"></a>將程式庫連結至 Spark 叢集

在本教學課程中，您會使用 Twitter API 將推文傳送至事件中樞。 您也會使用 [Apache Spark 事件中樞連接器](https://github.com/Azure/azure-event-hubs-spark)來讀取資料並將資料寫入至 Azure 事件中樞。 若要在叢集中使用這些 API，請將其作為程式庫新增至 Azure Databricks，然後讓這些 API 與您的 Spark 叢集產生關聯。 下列指示說明如何將程式庫新增至工作區中的 [共用]  資料夾。

1. 在 Azure Databricks 工作區中，選取 [工作區]  ，然後以滑鼠右鍵按一下 [共用]  。 從快顯功能表中，選取 [建立]   > [程式庫]  。

   ![新增程式庫對話方塊](../media/tutorials/databricks-add-library-option.png "新增程式庫對話方塊")

2. 在 [新增程式庫] 頁面中，針對 [來源]  選取 [Maven]  。 在 [座標]  中，輸入您要新增之套件的座標。 以下是本教學課程所使用之程式庫的 Maven 座標：

   * Spark 事件中樞連接器 - `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter API - `org.twitter4j:twitter4j-core:4.0.7`

     ![提供 Maven 座標](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "提供 Maven 座標")

3. 選取 [建立]  。

4. 選取程式庫新增所在的資料夾，然後選取程式庫名稱。

    ![選取要新增的程式庫](../media/tutorials/select-library.png "選取要新增的程式庫")

5. 如果程式庫頁面中沒有叢集，請選取 [叢集]  並執行您所建立的叢集。 等到狀態顯示 [執行中]，然後移回程式庫頁面。
在程式庫頁面上，選取要在其中使用程式庫的叢集，然後選取 [安裝]  。 在程式庫成功地與叢集產生關聯後，狀態會立即變更為 [已安裝]  。

    ![將程式庫安裝到叢集](../media/tutorials/databricks-library-attached.png "將程式庫安裝到叢集")

6. 對 Twitter 套件 `twitter4j-core:4.0.7` 重複執行這些步驟。

## <a name="get-a-cognitive-services-access-key"></a>取得認知服務存取金鑰

在本教學課程中，您會使用 [Azure 認知服務 Anomaly Detector API](../overview.md) 以近乎即時的方式對推文串流執行異常偵測。 在您使用此 API 之前，您必須在 Azure 上建立 Anomaly Detector 資源並擷取存取金鑰，才能使用 Anomaly Detector API。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取 [+ 建立資源]  。

3. 在 Azure Marketplace 之下，選取 [AI + 機器學習服務]   > [查看全部]   > [認知服務 - 更多]   > [Anomaly Detector]  。 或者，您可以使用[此連結](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)直接前往 [建立]  對話方塊。

    ![建立 Anomaly Detector 資源](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "建立 Anomaly Detector 資源")

4. 在 [建立]  對話方塊中提供下列值：

    |值 |描述  |
    |---------|---------|
    |名稱     | Anomaly Detector 資源的名稱。        |
    |訂用帳戶     | 將與資源相關聯的 Azure 訂用帳戶。        |
    |Location     | Azure 位置。        |
    |定價層     | 服務的定價層。 如需 Anomaly Detector 定價的詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/)。        |
    |資源群組     | 指定您是要建立新的資源群組，還是選取現有資源群組。        |


     選取 [建立]  。

5. 建立資源之後，從 [概觀]  索引標籤，複製並儲存 [端點]  URL (如螢幕擷取畫面所示)。 然後選取 [顯示存取金鑰]  。

    ![顯示存取金鑰](../media/tutorials/cognitive-services-get-access-keys.png "顯示存取金鑰")

6. 在 [金鑰]  下，針對您要使用的金鑰選取 [複製] 圖示。 儲存存取金鑰。

    ![複製存取金鑰](../media/tutorials/cognitive-services-copy-access-keys.png "複製存取金鑰")

## <a name="create-notebooks-in-databricks"></a>在 Databricks 中建立 Notebook

在本節中，您會在 Databricks 工作區中建立兩個具有下列名稱的 Notebook

- **SendTweetsToEventHub** - 生產者 Notebook，可供用來從 Twitter 取得推文，再將推文串流至事件中樞。
- **AnalyzeTweetsFromEventHub** - 取用者 Notebook，可供用來從事件中樞讀取推文並執行異常偵測。

1. 在 Azure Databricks 工作區中，從左窗格選取 [工作區]  。 從 [工作區]  下拉式清單選取 [建立]  ，然後選取 [Notebook]  。

    ![在 Databricks 中建立筆記本](../media/tutorials/databricks-create-notebook.png "在 Databricks 中建立筆記本")

2. 在 [建立 Notebook]  對話方塊中，輸入 **SendTweetsToEventHub** 作為名稱，選取 [Scala]  作為語言，然後選取您先前建立的 Spark 叢集。

    ![在 Databricks 中建立筆記本](../media/tutorials/databricks-notebook-details.png "在 Databricks 中建立筆記本")

    選取 [建立]  。

3. 重複上述步驟以建立 **AnalyzeTweetsFromEventHub** Notebook。

## <a name="send-tweets-to-event-hubs"></a>將推文傳送至事件中樞

在 **SendTweetsToEventHub** Notebook 中貼上下列程式碼，並將預留位置取代為您稍早所建立之事件中樞命名空間和 Twitter 應用程式的值。 此 Notebook 會從具有關鍵字 "Azure" 的推文擷取建立時間和「喜歡」數目，並即時將其當作事件串流至事件中樞。

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

若要執行 Notebook，請按 **SHIFT + ENTER**。 您會看到如下列程式碼片段所示的輸出。 輸出中的每個事件都是內嵌至事件中樞的時間戳記和「喜歡」數目組合。

```output
    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...
```

## <a name="read-tweets-from-event-hubs"></a>從事件中樞讀取推文

在 **AnalyzeTweetsFromEventHub** Notebook 中，貼上下列程式碼，並將預留位置取代為您稍早所建立 Anomaly Detector 資源的值。 此 Notebook 會讀取您稍早使用 **SendTweetsToEventHub** Notebook 串流至事件中樞的推文。

首先，撰寫用戶端來呼叫 Anomaly Detector。 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

若要執行 Notebook，請按 **SHIFT + ENTER**。 您會看到如下列程式碼片段所示的輸出。

```scala
import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection
defined class Point
defined class Series
defined class AnomalySingleResponse
defined class AnomalyBatchResponse
defined object AnomalyDetector
```

然後準備可供未來使用的彙總函式。
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

若要執行 Notebook，請按 **SHIFT + ENTER**。 您會看到如下列程式碼片段所示的輸出。

```scala
import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap
defined class AnomalyDetectorAggregationFunction
```

接著從事件中樞載入資料，以便進行異常偵測。 將預留位置取代為您稍早所建立 Azure 事件中樞的值。

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

輸出現在會類似下圖。 請注意，您在資料表中的日期可能不同於本教學課程中的日期，因為資料是即時的。
![從事件中樞載入資料](../media/tutorials/load-data-from-eventhub.png "從事件中樞載入資料")

您現在已使用 Apache Spark 的事件中樞連接器，以近乎即時的方式將資料從 Azure 事件中樞串流至 Azure Databricks。 如需如何使用 Spark 事件中樞連接器的詳細資訊，請參閱[連接器文件](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs)。



## <a name="run-anomaly-detection-on-tweets"></a>對推文執行異常偵測

在本節中，您可以使用 Anomaly Detector API 對收到的推文執行異常偵測。 在本節中，您會將程式碼片段新增至相同的 **AnalyzeTweetsFromEventHub** Notebook。

若要執行異常偵測，首先必須依小時彙總計量計數。
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
輸出現在類似下列程式碼片段。
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

然後取得差異的彙總輸出結果。 因為異常偵測需要較長的歷程記錄時段，所以我們使用差異來為您要偵測的資料點保留歷程記錄資料。 以要建立的合格差異資料表名稱 (例如，"tweets") 取代 "[Placeholder: table name]"。 以您每次執行此程式碼時唯一的字串值 (例如，"etl-from-eventhub-20190605") 取代 "[Placeholder: folder name for checkpoints]"。
若要深入了解 Azure Databricks 上的 Delta Lake，請參閱 [Delta Lake 指南](https://docs.azuredatabricks.net/delta/index.html)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

以您在上面選取的相同差異資料表名稱取代 "[Placeholder: table name]"。
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
輸出如下所示： 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

現在，彙總時間序列資料會持續內嵌到差異中。 然後您可以排程每小時作業，以偵測最新資料點的異常狀況。 以您在上面選取的相同差異資料表名稱取代 "[Placeholder: table name]"。

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
結果如下所示： 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

就這麼簡單！ 您已使用 Azure Databricks 成功地以近乎即時的速度將資料串流到 Azure 事件中樞、使用事件中樞連接器取用串流資料，然後對串流資料執行異常偵測。
雖然在本教學課程中，細微性為每小時，您可以隨時變更細微度以符合您的需求。 

## <a name="clean-up-resources"></a>清除資源

在本教學課程執行完後，您可以終止叢集。 若要這樣做，請從 Azure Databricks 工作區的左窗格中選取 [叢集]  。 對於您想要終止的叢集，將游標移到 [動作]  資料行底下的省略符號上，然後選取 [終止]  圖示，然後選取 [確認]  。

![停止 Databricks 叢集](../media/tutorials/terminate-databricks-cluster.png "停止 Databricks 叢集")

如果您不手動終止叢集，叢集將會自動停止，但前提是您已在建立叢集時選取 [在停止活動 \_\_ 分鐘後終止]  核取方塊。 在這種情況下，叢集將會在停止活動達指定時間後自動停止。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Azure Databricks 將資料串流到 Azure 事件中樞，然後從事件中樞即時讀取串流資料。 前進到下一個教學課程，以了解如何使用 Power BI Desktop 呼叫 Anomaly Detector API，並將異常狀況視覺化。 

> [!div class="nextstepaction"]
>[透過 Power BI Desktop 批次處理異常偵測](batch-anomaly-detection-powerbi.md)
