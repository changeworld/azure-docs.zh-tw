---
title: 監視和記錄-Azure
description: 本文概述 IoT Edge 的即時影片分析中的監視和記錄。
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 6a7251b62421642ad9f5dba4f4c2a15ce74cd5cf
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900870"
---
# <a name="monitoring-and-logging"></a>監視和記錄

在本文中，您將瞭解如何從 IoT Edge 課程模組上的即時影片分析接收遠端監視事件。 

您也將瞭解如何控制模組所產生的記錄。

## <a name="taxonomy-of-events"></a>事件的分類法

IoT Edge 上的即時影片分析會根據下列分類法發出事件或遙測資料：

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="顯示事件分類的圖表。":::

* 操作：使用者的動作所產生的事件，或[媒體圖形](media-graph-concept.md)執行期間所產生的事件
   
   * 磁片區：應為低 (幾次或更少) 
   * 範例：

      - 記錄已開始 (如下列範例所示) 
      - 記錄已停止
      
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
* 診斷：有助於診斷效能問題的事件

   * 磁片區：每分鐘可能會有高 (數次) 
   * 範例：
   
      - RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 資訊 (如下列範例所示)  
      - 內送影片摘要中的間距

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
* 分析：影片分析過程中所產生的事件

   * 磁片區：每分鐘可能會有高 (數次) 
   * 範例：
      
      - 偵測到的動作 (如下列範例所示)  
      - 推斷結果

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

模組發出的事件會傳送至 [IoT Edge 中樞](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)。 您可以從該處將它們路由傳送至其他目的地。 

### <a name="timestamps-in-analytic-events"></a>分析事件中的時間戳記

如先前所述，在影片分析中產生的事件會有相關聯的時間戳記。 如果您將 [即時影片錄製](video-recording-concept.md) 為圖形拓撲的一部分，這些時間戳記可協助您找出發生特定事件的錄製影片中的位置。 以下是如何將分析事件中的時間戳記對應到錄製至 [Azure 媒體服務資產](terminology.md#asset)的影片時間軸的指導方針。

首先，將 `eventTime` 值解壓縮。 在 [時間範圍篩選器](playback-recordings-how-to.md#time-range-filters) 中使用此值，以抓取錄製的適當部分。 例如，您可能想要取出在30秒之前的影片， `eventTime` 並在30秒後結束。 在先前的範例中，如果 `eventTime` 是 2020-05-12T23：33： 09.381 z，則在之前和之後30秒的 HLS 資訊清單要求 `eventTime` 看起來會像此要求：

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

上述 URL 會傳回包含媒體播放清單 Url 的 [主要播放清單](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) 。 Media 播放清單中會包含如下的專案：

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
上述專案會報告從的值開始可用的影片片段 `timestamp` `143039375031270` 。 `timestamp`分析事件中的值會使用與媒體播放清單相同的時間刻度。 可以用來識別相關的影片片段，並搜尋正確的框架。

如需詳細資訊，請參閱這些有關 HLS 中的 [框架精確搜尋的文章](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) 。

## <a name="controlling-events"></a>控制事件

您可以使用下列模組對應項屬性，來控制 IoT Edge 模組上的即時影片分析所發佈的操作和診斷事件。 這些屬性記載于模組對應項 [JSON 架構](module-twin-configuration-schema.md)中。

- `diagnosticsEventsOutputName`：若要從模組取得診斷事件，請包含此屬性並為其提供任何值。 省略它或將其保留為空白，以防止模組發行診斷事件。
   
- `operationalEventsOutputName`：若要從模組取得作業事件，請包含此屬性並為其提供任何值。 省略它或將其保留為空白，以防止模組發行操作事件。
   
分析事件是由像是動作偵測處理器或 HTTP 擴充處理器等節點所產生。 IoT 中樞接收會用來將它們傳送至 IoT Edge 中樞。 

您可以[](../../iot-edge/module-composition.md#declare-routes) `desired` `$edgeHub` 在部署資訊清單中使用模組對應項的屬性來控制所有先前事件的路由：

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

在上述 JSON 中， `lvaEdge` 是 IoT Edge 模組上的即時影片分析名稱。 路由規則會遵循宣告 [路由](../../iot-edge/module-composition.md#declare-routes)中所定義的架構。

> [!NOTE]
> 若要確保分析事件會觸達 IoT Edge hub，您必須讓 IoT 中樞接收節點具有任何動作偵測處理器節點和/或任何 HTTP 擴充處理器節點的下游。

## <a name="event-schema"></a>結構描述

事件是源自 edge 裝置，而且可以在邊緣或雲端中使用。 IoT Edge 上的即時影片分析所產生的事件符合 Azure IoT 中樞所建立的 [串流訊息模式](../../iot-hub/iot-hub-devguide-messages-construct.md) 。 此模式包含系統屬性、應用程式屬性和主體。

### <a name="summary"></a>摘要

透過 IoT 中樞觀察到的每個事件都有一組通用屬性：

|屬性   |屬性類型| 資料類型   |描述|
|---|---|---|---|
|`message-id`   |系統 |guid|  唯一的事件識別碼。|
|`topic`|   applicationProperty |字串|    Azure 媒體服務帳戶的 Azure Resource Manager 路徑。|
|`subject`| applicationProperty |字串|    發出事件之實體的子路徑。|
|`eventTime`|   applicationProperty|    字串| 產生事件的時間。|
|`eventType`|   applicationProperty |字串|    事件種類識別碼。  (請參閱下一節。 ) |
|`body`|body    |物件 (object)|    特定的事件資料。|
|`dataVersion`  |applicationProperty|   字串  |{主要}。微小|

### <a name="properties"></a>Properties

#### <a name="message-id"></a>message-id

事件的全域唯一識別碼 (GUID) 。

#### <a name="topic"></a>主題

表示與圖形相關聯的 Azure 媒體服務帳戶。

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

發出事件的實體：

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

`subject`屬性可讓您將泛型事件對應至產生的模組。 例如，如果是不正確 RTSP 使用者名稱或密碼，則產生的事件會 `Microsoft.Media.Graph.Diagnostics.ProtocolError` 在 `/graphInstances/myGraph/sources/myRtspSource` 節點上。

#### <a name="event-types"></a>事件類型

事件種類會根據此架構指派給命名空間：

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>事件類別

|類別名稱|描述|
|---|---|
|分析  |作為內容分析一部分產生的事件。|
|診斷    |有助於診斷問題和效能的事件。|
|運作    |在資源作業中產生的事件。|

事件種類是每個事件類別特有的。

範例：

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>事件時間

事件時間的格式為 ISO 8601 字串。 它代表事件發生的時間。

### <a name="azure-monitor-collection-via-telegraf"></a>透過 Telegraf Azure 監視器集合

這些計量將會從 IoT Edge 課程模組上的即時影片分析報告：  

|度量名稱|類型|標籤|描述|
|-----------|----|-----|-----------|
|lva_active_graph_instances|量測計|iothub、edge_device、module_name graph_topology|每個拓撲的使用中圖表總數。|
|lva_received_bytes_total|計數器|iothub、edge_device、module_name、graph_topology、graph_instance、graph_node|節點接收的位元組總數。 僅支援 RTSP 來源。|
|lva_data_dropped_total|計數器|iothub、edge_device、module_name、graph_topology、graph_instance、graph_node、data_kind|任何已卸載資料 (事件、媒體等等) 的計數器。|

> [!NOTE]
> [Prometheus 端點](https://prometheus.io/docs/practices/naming/)會在容器的埠9600公開。 如果您將即時影片分析命名于 IoT Edge 模組 "lvaEdge" 上，他們將能夠藉由傳送 GET 要求至來存取計量 http://lvaEdge:9600/metrics 。   

依照下列步驟，在 IoT Edge 課程模組上啟用即時影片分析中的計量收集：

1. 在您的開發電腦上建立資料夾，然後移至該資料夾。

1. 在資料夾中，建立包含下列設定的檔案 `telegraf.toml` ：
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = ""
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > 請務必取代 >gopkg.toml 檔案中的變數。 變數是以大括弧 (`{}`) 表示。

1. 在相同的資料夾中，建立 `.dockerfile` 包含下列命令的：
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. 使用 Docker CLI 命令來建立 Docker 檔案，並將映射發佈至您的 Azure container registry。
    
   如需使用 Docker CLI 推送至容器登錄的詳細資訊，請參閱 [推送和提取 Docker 映射](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)。 如需 Azure Container Registry 的其他資訊，請參閱 [檔](https://docs.microsoft.com/azure/container-registry/)。


1. 在推送至 Azure Container Registry 完成之後，將下列節點新增至您的部署資訊清單檔案：
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > 請務必取代資訊清單檔中的變數。 變數是以大括弧 (`{}`) 表示。


   Azure 監視器可以透過 [服務主體進行驗證](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)。
        
   Azure 監視器的 Telegraf 外掛程式會公開數 [種驗證方法](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)。 

  1. 若要使用服務主體驗證，請設定下列環境變數：  
     `AZURE_TENANT_ID`：指定要對其進行驗證的租使用者。  
     `AZURE_CLIENT_ID`：指定要使用的應用程式用戶端識別碼。  
     `AZURE_CLIENT_SECRET`：指定要使用的應用程式密碼。  
     
     >[!TIP]
     > 您可以將 **監視計量發行者** 角色授與服務主體。

1. 部署模組之後，計量會出現在 Azure 監視器的單一命名空間底下。 計量名稱會符合 Prometheus 發出的名稱。 

   在此情況下，請移至 [Azure 入口網站中的 IoT 中樞，然後選取左窗格中的 [ **計量** ]。 您應該會在該處看到度量。

## <a name="logging"></a>記錄

如同其他 IoT Edge 模組，您也可以檢查 Edge 裝置上 [的容器記錄](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) 。 您可以使用 [下列模組](module-twin-configuration-schema.md) 對應項屬性，設定寫入記錄的資訊：

* `logLevel`

   * 允許的值為 `Verbose`、`Information`、`Warning`、`Error` 和 `None`。
   * 預設值是 `Information`。 記錄檔將包含錯誤、警告和資訊訊息。
   * 如果您將值設為 `Warning` ，記錄檔將會包含錯誤和警告訊息。
   * 如果您將值設定為 `Error` ，則記錄只會包含錯誤訊息。
   * 如果您將值設為 `None` ，則不會產生任何記錄。  (不建議進行這種設定。 ) 
   * `Verbose`只有當您需要與 Azure 支援共用記錄以診斷問題時，才使用。

* `logCategories`

   * 下列其中一個或多個值的逗號分隔清單： `Application` 、 `Events` 、 `MediaPipeline` 。
   * 預設值是 `Application, Events`。
   * `Application`：模組的高層級資訊，例如模組啟動訊息、環境錯誤和直接方法呼叫。
   * `Events`：本文稍早所述的所有事件。
   * `MediaPipeline`：當您針對問題進行疑難排解時，可能會提供深入解析的低層級記錄，例如建立與支援 RTSP 之攝影機的連接時遇到問題。
   
### <a name="generating-debug-logs"></a>產生 debug 記錄

在某些情況下，為了協助 Azure 支援解決問題，您可能需要產生比先前所述更詳細的記錄。 若要產生這些記錄：

1. 透過將[模組儲存體連結至裝置存放裝置](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) `createOptions` 。 如果您從快速入門查看 [部署資訊清單範本](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) ，您會看到此程式碼：

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   此程式碼可讓 Edge 模組將記錄寫入裝置儲存路徑 `/var/local/mediaservices/` 。 

 1. 將下列 `desired` 屬性新增至模組：

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

模組現在會以二進位格式將 debug 記錄寫入裝置儲存路徑 `/var/local/mediaservices/debuglogs/` 。 您可以透過 Azure 支援共用這些記錄。

## <a name="faq"></a>常見問題集

如果您有任何問題，請參閱 [監視和計量常見問題](faq.md#monitoring-and-metrics)。

## <a name="next-steps"></a>後續步驟

[連續影片錄製](continuous-video-recording-tutorial.md)
