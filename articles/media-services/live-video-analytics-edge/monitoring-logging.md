---
title: 監視和記錄-Azure
description: 本文提供 IoT Edge 監視和記錄的即時影片分析總覽。
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: ef00517fc61ac532bdd99c1e887dfd93d56a8c4f
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89567549"
---
# <a name="monitoring-and-logging"></a>監視和記錄

在本文中，您將瞭解如何從 IoT Edge 模組上的即時影片分析接收事件，以進行遠端監視。 

您也將瞭解如何控制模組所產生的記錄。

## <a name="taxonomy-of-events"></a>事件的分類法

IoT Edge 上的即時影片分析會根據下列分類法發出事件或遙測資料。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="事件的分類法":::

* 操作：當使用者採取的動作，或在 [media graph](media-graph-concept.md)執行期間所產生的事件。
   
   * 磁片區：應為低 (幾次，或甚至較低的) 速率。
   * 範例：

      錄製開始 () ，記錄已停止
      
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
* 診斷：有助於診斷問題及/或效能問題的事件。

   * 磁片區：每分鐘可能會有高 (數次) 。
   * 範例：
   
      RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 資訊 () ，或連入影片摘要中的間隙。

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
* 分析：影片分析過程中所產生的事件。

   * 磁片區：可能會有很高的 (數分鐘的時間，或通常) 。
   * 範例：
      
      偵測到 () ，推斷結果中偵測到動作。

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

模組發出的事件會傳送至 [IoT Edge 中樞](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)，並從該處傳送到其他目的地。 

### <a name="timestamps-in-analytic-events"></a>分析事件中的時間戳記

如上所示，在影片分析過程中產生的事件會有相關聯的時間戳記。 如果您將 [即時影片錄製](video-recording-concept.md) 為圖形拓撲的一部分，此時間戳記可協助您找出已錄製影片中發生特定事件的位置。 下列指導方針說明如何將分析事件中的時間戳記對應到錄製到 [Azure 媒體服務資產](terminology.md#asset)的影片時間軸。

首先，將 `eventTime` 值解壓縮。 在 [時間範圍篩選器](playback-recordings-how-to.md#time-range-filters) 中使用此值，以抓取錄製的適當部分。 例如，您可能會想要提取的影片將于30秒後開始 `eventTime` ，並在30秒後結束。 在上述範例中，如果 `eventTime` 是 2020-05-12T23：33： 09.381 z，則適用于 +/30 秒視窗之 HLS 資訊清單的要求如下所示：

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

上述 URL 會傳回所謂的 [主要播放清單](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming)，其中包含媒體播放清單的 url。 Media 播放清單中會包含如下的專案：

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
在上述的專案中，此專案會報告從的時間戳記值開始可用的影片片段 `143039375031270` 。 `timestamp`分析事件中的值會使用與媒體播放清單相同的時間範圍，而且可以用來識別相關的影片片段，並搜尋正確的畫面格。

如需詳細資訊，您可以閱讀 HLS 中有關框架精確搜尋的眾多 [文章](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) 之一。

## <a name="controlling-events"></a>控制事件

您可以使用下列模組對應項屬性（如模組對應項 [JSON 架構](module-twin-configuration-schema.md)中所述），來控制 IoT Edge 模組上的即時影片分析所發佈的操作和診斷事件。

`diagnosticsEventsOutputName` –針對此屬性包含並提供 (任何) 值，以便從模組取得診斷事件。 省略它，或將其保留為空白，以防止模組發行診斷事件。
   
`operationalEventsOutputName` –針對此屬性包含並提供 (任何) 值，以便從模組取得操作事件。 省略它，或將其保留為空白，以防止模組發行操作事件。
   
分析事件是由節點（例如動作偵測處理器或 HTTP 擴充功能處理器）所產生，而 IoT 中樞接收則是用來將它們傳送至 IoT Edge 中樞。 

您可以透過部署資訊清單) 中 $edgeHub 模組對應項 (所需的屬性，來控制 [所有上述事件的路由](../../iot-edge/module-composition.md#declare-routes) ：

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

在上述 lvaEdge 中，是即時影片分析在 IoT Edge 模組上的名稱，而路由規則會遵循宣告 [路由](../../iot-edge/module-composition.md#declare-routes)中定義的架構。

> [!NOTE]
> 為了確保分析事件會觸達 IoT Edge 中樞，任何動作偵測處理器節點和/或任何 HTTP 擴充處理器節點都必須有 IoT 中樞接收節點下游。

## <a name="event-schema"></a>結構描述

事件源自 Edge 裝置，並可在邊緣或雲端中使用。 IoT Edge 上的即時影片分析所產生的事件符合 Azure IoT 中樞所建立的 [串流訊息模式](../../iot-hub/iot-hub-devguide-messages-construct.md) ，並具有系統屬性、應用程式屬性和主體。

### <a name="summary"></a>摘要

透過 IoT 中樞觀察到的每個事件都會有一組通用屬性，如下所述。

|屬性   |屬性類型| 資料類型   |描述|
|---|---|---|---|
|message-id |系統 |guid|  唯一的事件識別碼。|
|主題| applicationProperty |字串|    媒體服務帳戶的 Azure Resource Manager 路徑。|
|subject|   applicationProperty |字串|    發出事件之實體的子路徑。|
|eventTime| applicationProperty|    字串| 產生事件的時間。|
|eventType| applicationProperty |字串|    事件種類識別碼 (請參閱以下) 。|
|body|body  |物件 (object)|    特定的事件資料。|
|dataVersion    |applicationProperty|   字串  |{主要}。微小|

### <a name="properties"></a>屬性

#### <a name="message-id"></a>message-id

事件全域唯一識別碼 (GUID) 

#### <a name="topic"></a>主題

代表與圖形相關聯的 Azure 媒體服務帳戶。

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

發出事件的實體：

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

Subject 屬性可讓泛型事件對應至其產生的模組。 比方說，如果是不正確 RTSP 使用者名稱或密碼，則產生的事件會 `Microsoft.Media.Graph.Diagnostics.ProtocolError` 在 `/graphInstances/myGraph/sources/myRtspSource` 節點上。

#### <a name="event-types"></a>事件類型

事件種類會根據下列架構指派給命名空間：

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>事件類別

|類別名稱|描述|
|---|---|
|分析  |作為內容分析一部分產生的事件。|
|診斷    |有助於診斷問題和效能的事件。|
|運作    |在資源作業中產生的事件。|

事件種類是每個事件類別特有的。

範例：

* Microsoft. Graph。
* AuthorizationError （.）
* GraphInstanceStarted 的工作。

### <a name="event-time"></a>事件時間

事件時間會在 ISO8601 字串中描述，而這是事件發生的時間。

## <a name="logging"></a>記錄

如同其他 IoT Edge 模組，您也可以檢查 Edge 裝置上 [的容器記錄](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) 。 寫入至記錄檔的資訊可由 [下列模組](module-twin-configuration-schema.md) 對應項屬性控制：

* logLevel

   * 允許的值為詳細資訊、資訊、警告、錯誤、無。
   * 預設值為 [資訊] –記錄檔將包含錯誤、警告和資訊。 消息。
   * 如果您將值設定為 [警告]，記錄檔將會包含錯誤和警告訊息。
   * 如果您將值設為 [錯誤]，記錄檔中只會包含錯誤訊息。
   * 如果您將值設定為 [無]，則不會產生任何記錄 () 不建議這樣做。
   * 如果您需要與 Azure 支援共用記錄以診斷問題，您應該只使用 [詳細資訊]。
* logCategories

   * 下列其中一項或多項的逗號分隔清單： Application、Events、MediaPipeline。
   * 預設值：應用程式、事件。
   * 應用程式–這是模組中的高階資訊，例如模組啟動訊息、環境錯誤和直接方法呼叫。
   * 事件-這些都是本文稍早所述的所有事件。
   * MediaPipeline –這些是一些低層級的記錄檔，可在對問題進行疑難排解時提供深入解析，例如建立與支援 RTSP 之攝影機的連接時遇到問題。
   
### <a name="generating-debug-logs"></a>產生 debug 記錄

在某些情況下，您可能需要產生比上面所述更詳細的記錄，以協助 Azure 支援解決問題。 有兩個步驟可以完成此操作。

首先，透過 createOptions 將 [模組儲存體連結到裝置儲存體](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) 。 如果您從快速入門檢查 [部署資訊清單範本](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) ，您將會看到：

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

以上可讓 Edge 模組將記錄寫入 (裝置) 儲存體路徑 "/var/local/mediaservices/"。 如果您將下列所需屬性新增至模組：

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

然後，模組會以二進位格式將 debug 記錄寫入 (的裝置) 儲存體路徑/var/local/mediaservices/debuglogs/，您可以與 Azure 支援共用。

## <a name="faq"></a>常見問題集

[常見問題集](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>接下來的步驟

[連續影片錄製](continuous-video-recording-tutorial.md)
