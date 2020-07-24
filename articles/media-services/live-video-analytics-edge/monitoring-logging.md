---
title: 監視和記錄-Azure
description: 本文提供有關 IoT Edge 監視和記錄的即時影片分析總覽。
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 82e4a5879e4c88e462edcddb02866ec9b671d7fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87060450"
---
# <a name="monitoring-and-logging"></a>監視和記錄

在本文中，您將瞭解如何從 IoT Edge 模組上的即時影片分析接收事件，以進行遠端監視。 

您也將瞭解如何控制模組所產生的記錄檔。

## <a name="taxonomy-of-events"></a>事件的分類法

IoT Edge 上的即時影片分析會根據下列分類來發出事件或遙測資料。

![IoT Edge 遙測架構上的即時影片分析](./media/telemetry-schema/taxonomy.png)

* 操作：在使用者採取的動作中，或在[媒體圖形](media-graph-concept.md)執行期間產生的事件。
   
   * 磁片區：預期為低（一分鐘幾次，或甚至較低的速率）。
   * 範例：

      記錄已啟動（下方），記錄已停止
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* 診斷：有助於診斷問題和（或）效能問題的事件。

   * 磁片區：可能很高（一分鐘數次）。
   * 範例：
   
      RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol)資訊（下方），或傳入影片摘要中的間距。

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* 分析：在影片分析過程中產生的事件。

   * 磁片區：可能很高（一分鐘或更長的幾次）。
   * 範例：
      
      偵測到的動作（下方），推斷結果。
   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```
模組發出的事件會傳送至[IoT Edge 中樞](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)，然後從該處路由傳送至其他目的地。 

## <a name="controlling-events"></a>控制事件

您可以使用下列模組對應項屬性（如模組對應項[JSON 架構](module-twin-configuration-schema.md)中所述）來控制 IoT Edge 模組上即時影片分析所發佈的操作和診斷事件。

`diagnosticsEventsOutputName`–包含並提供此屬性的（any）值，以便從模組取得診斷事件。 省略它，或將它保留為空白，以停止模組發行診斷事件。
   
`operationalEventsOutputName`–包含並提供此屬性的（any）值，以便從模組取得操作事件。 省略它，或將它保留為空白，以停止模組發佈操作事件。
   
分析事件是由節點（例如，動作偵測處理器或 HTTP 擴充處理器）所產生，而 IoT 中樞接收則是用來將它們傳送至 IoT Edge 中樞。 

您可以透過 $edgeHub 模組對應項的所需屬性來控制[所有上述事件的路由](../../iot-edge/module-composition.md#declare-routes)（在部署資訊清單中）：

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

在上述的中，lvaEdge 是 IoT Edge 模組上即時影片分析的名稱，而路由規則會遵循[declare](../../iot-edge/module-composition.md#declare-routes)route 中定義的架構。

> [!NOTE]
> 為了確保分析事件會到達 IoT Edge 中樞，必須有任何動作偵測處理器節點和/或任何 HTTP 擴充處理器節點下游的 IoT 中樞接收節點。

## <a name="event-schema"></a>結構描述

事件源自于 Edge 裝置，並可在邊緣或雲端中使用。 即時影片分析在 IoT Edge 上所產生的事件，符合由 Azure IoT 中樞所建立的[串流訊息模式](../../iot-hub/iot-hub-devguide-messages-construct.md)，包含系統屬性、應用程式屬性和主體。

### <a name="summary"></a>摘要

透過 IoT 中樞觀察到的每個事件都有一組通用屬性，如下所述。

|屬性   |屬性類型| 資料類型   |描述|
|---|---|---|---|
|message-id |系統 |guid|  唯一的事件識別碼。|
|主題| applicationProperty |字串|    媒體服務帳戶的 Azure Resource Manager 路徑。|
|subject|   applicationProperty |字串|    發出事件之實體的子路徑。|
|eventTime| applicationProperty|    字串| 事件產生的時間。|
|eventType| applicationProperty |字串|    事件種類識別碼（請參閱下文）。|
|body|body  |物件 (object)|    特定事件資料。|
|dataVersion    |applicationProperty|   字串  |{主要}。刻度|

### <a name="properties"></a>[內容]

#### <a name="message-id"></a>message-id

事件全域唯一識別碼（GUID）

#### <a name="topic"></a>主題

代表與圖形相關聯的 Azure 媒體服務帳戶。

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

發出事件的實體：

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

Subject 屬性可讓泛型事件對應到其產生的模組。 例如，如果是不正確 RTSP 使用者名稱或密碼，則產生的事件會 `Microsoft.Media.Graph.Diagnostics.ProtocolError` 在 `/graphInstances/myGraph/sources/myRtspSource` 節點上。

#### <a name="event-types"></a>事件類型

系統會根據下列架構，將事件種類指派給命名空間：

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>事件類別

|類別名稱|描述|
|---|---|
|分析  |在內容分析過程中產生的事件。|
|診斷    |有助於診斷問題和效能的事件。|
|運作    |在資源作業中產生的事件。|

事件種類是每個事件類別特有的。

範例：

* Microsoft. Media. Analytics。
* AuthorizationError 的程式。
* GraphInstanceStarted （操作）

### <a name="event-time"></a>事件時間

事件時間會在 ISO8601 字串中描述，而它是事件發生的時間。

## <a name="logging"></a>記錄

和其他 IoT Edge 模組一樣，您也可以檢查 Edge 裝置上[的容器記錄](../../iot-edge/troubleshoot.md#check-container-logs-for-issues)。 寫入記錄檔的資訊可由[下列模組](module-twin-configuration-schema.md)對應項屬性控制：

* logLevel

   * 允許的值為 Verbose、Information、Warning、Error、None。
   * 預設值為 [資訊] –記錄會包含錯誤、警告和資訊。 訊息.
   * 如果您將值設定為 [警告]，記錄將會包含錯誤和警告訊息
   * 如果您將此值設定為 [錯誤]，則記錄檔只會包含錯誤訊息。
   * 如果您將此值設定為 [無]，則不會產生任何記錄（不建議這樣做）。
   * 如果您需要與 Azure 支援共用記錄以診斷問題，您應該只使用詳細資訊。
* logCategories

   * 下列一或多個以逗號分隔的清單：應用程式、事件、MediaPipeline。
   * 預設值：應用程式、事件。
   * 應用程式–這是模組中的高階資訊，例如模組啟動訊息、環境錯誤和直接方法呼叫。
   * 事件–這些是本文稍早所述的所有事件。
   * MediaPipeline –這些是一些低層級的記錄檔，可在疑難排解問題時提供深入解析，例如建立與支援 RTSP 之攝影機的連線時遇到困難。
   
### <a name="generating-debug-logs"></a>產生調試記錄

在某些情況下，您可能需要產生比上面所述更詳細的記錄，以協助 Azure 支援解決問題。 有兩個步驟可完成此動作。

首先，您可以透過 createOptions 將[模組儲存體連結至裝置存放裝置](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage)。 如果您從快速入門檢查[部署資訊清單範本](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json)，您將會看到：

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

以上可讓 Edge 模組將記錄寫入（裝置）儲存路徑 "/var/local/mediaservices/"。 如果您將下列所需的屬性新增至模組：

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

然後，此模組會將具有二進位格式的 debug 記錄寫入至（裝置）儲存體路徑/var/local/mediaservices/debuglogs/，您可以與 Azure 支援人員共用。

## <a name="faq"></a>常見問題集

[常見問題集](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>接下來的步驟

[連續影片錄製](continuous-video-recording-tutorial.md)
