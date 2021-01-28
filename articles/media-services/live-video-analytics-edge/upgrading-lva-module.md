---
title: 將 IoT Edge 的即時影片分析從1.0 升級為2。0
description: 本文涵蓋在 Azure IoT Edge 模組上 (LVA) 升級即時影片分析時所要考慮的差異和不同事項。
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 49c17946203bc6c3655b1aaf7b04a1ee3ea67388
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955644"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>將 IoT Edge 的即時影片分析從1.0 升級為2。0

本文涵蓋在 Azure IoT Edge 模組上 (LVA) 升級即時影片分析時所要考慮的差異和不同事項。

## <a name="change-list"></a>變更清單

> [!div class="mx-tdCol4BreakAll"]
> |標題|即時影片分析1。0|即時影片分析2。0|Description|
> |-------------|----------|---------|---------|
> |容器映射|mcr.microsoft.com/media/live-video-analytics:1|mcr.microsoft.com/media/live-video-analytics:2|Microsoft 已發佈 docker 映射，可在 Azure IoT Edge 上進行即時影片分析|
> |**MediaGraph-節點** |    |   |   |
> |來源|:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP 來源 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT 中樞訊息來源 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP 來源 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT 中樞訊息來源 | MediaGraph-節點，作為媒體內嵌和訊息的來源。|
> |處理器|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 動作偵測處理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 畫面播放速率篩選處理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Http 擴充處理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc 擴充處理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 信號閘道處理器 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: 動作偵測處理器 </br>:::image type="icon" source="./././media/upgrading-lva/remove.png":::**畫面播放速率篩選處理器**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Http 擴充處理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc 擴充處理器 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 信號閘道處理器 | MediaGraph 可讓您將媒體格式化，再傳送至 AI 推斷伺服器的節點。|
> |接收器|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 資產接收 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 檔案接收 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT 中樞訊息接收|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 資產接收 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 檔案接收 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT 中樞訊息接收| MediaGraph 可讓您儲存已處理之媒體的節點。|
> |**支援的 MediaGraphs** |    |   |   |
> |拓撲|:::image type="icon" source="./././media/upgrading-lva/check.png"::: 持續錄製影片 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 動作分析和持續錄製影片 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外部分析和持續錄製影片 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 以事件為基礎的動作錄製 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 以事件為基礎的 AI 記錄</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外來事件上以事件為基礎的記錄 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 動作分析 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 動作分析，後面接著 AI 推斷 |:::image type="icon" source="./././media/upgrading-lva/check.png"::: 持續錄製影片 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 動作分析和持續錄製影片 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外部分析和持續錄製影片 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 以事件為基礎的動作錄製 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 以事件為基礎的 AI 記錄</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 外來事件上以事件為基礎的記錄 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 動作分析 </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: 動作分析，後面接著 AI 推斷 </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**AI 組合** </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**音訊和影片錄製** </br>  | MediaGraph 拓撲，可讓您定義圖形的藍圖，並以參數作為值的預留位置。|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>將 IoT Edge 模組上的即時影片分析從1.0 升級為2。0
升級 IoT Edge 模組上的即時影片分析時，請務必更新下列資訊。
### <a name="container-image"></a>容器映射
在您的部署範本中，于節點下的 IoT Edge 模組上尋找您的即時影片分析， `modules` 並將 `image` 欄位更新為：
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
> 如果您尚未在 IoT Edge 模組上修改即時影片分析的名稱，請在 `lvaEdge` 模組節點下尋找。

### <a name="topology-file-changes"></a>拓撲檔案變更
在您的拓撲檔案中，確定 **`apiVersion`** 已設定為2。0

### <a name="mediagraph-node-changes"></a>MediaGraph-節點變更


* 您現在可以將來自相機來源的音訊與影片一起傳遞。 您可以使用任何圖形節點的 [說明]，將 **僅限音訊** 或全 **影片** 或 **音訊和影片** 傳遞給 **`outputSelectors`** 。 例如，如果您只想要從 RTSP 來源選取影片，並將它傳遞至下游，請將下列內容新增至 RTSP 來源節點：
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>**`outputSelectors`** 是選擇性屬性。 如果未使用這項功能，則媒體圖形將會通過音訊 (（如果已啟用）) 和來自 RTSP 攝影機下游的影片。 

* 在 `MediaGraphHttpExtension` 和 `MediaGraphGrpcExtension` 處理器中，請注意下列變更：  
    #### <a name="image-properties"></a>映像屬性
    * `MediaGraphImageFormatEncoded` 不再受支援。 
      * 請改用 **`MediaGraphImageFormatBmp`** 或 **`MediaGraphImageFormatJpeg`** 或 **`MediaGraphImageFormatPng`** 。 例如，
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * 如果您想要使用原始影像，請使用 **`MediaGraphImageFormatRaw`** 。 若要使用這種方式，請將映射節點更新為：
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > 可能的 pixelFormat 值包括： `yuv420p` 、 `rgb565be` 、 `rgb565le` 、 `rgb555be` 、 `rgb555le` 、 `rgb24` 、 `bgr24` `argb` `rgba` `abgr` 、、、、 `bgra`  

    #### <a name="extensionconfiguration-for-grpc-extension-processor"></a>適用于 Grpc 擴充處理器的 extensionConfiguration  
    * 在 `MediaGraphGrpcExtension` 處理器中，有一個稱為的新屬性 **`extensionConfiguration`** ，它是選擇性字串，可作為 gRPC 合約的一部分使用。 這個欄位可以用來將任何資料傳遞至推斷伺服器，而且您可以定義推斷伺服器如何使用該資料。  
    這個屬性的一個使用案例是當您在單一推斷伺服器中封裝多個 AI 模型時。 使用這個屬性，您就不需要為每個 AI 模型公開節點。 相反地，針對圖形實例，您可以使用屬性，以擴充提供者的形式來定義如何選取不同的 AI 模型 **`extensionConfiguration`** ，而 LVA 會將此字串傳遞至推斷伺服器，這可以用來叫用所需的 ai 模型。  

    #### <a name="ai-composition"></a>AI 組合
    * Live Video Analytics 2.0 現在支援在拓撲內使用一個以上的 media graph 擴充處理器。 您可以依照順序，以平行方式或兩者的組合，將媒體框架從 RTSP 攝影機傳遞至不同的 AI 模型。 請參閱範例拓撲，其中顯示兩個依序使用的 AI 模型。

### <a name="disk-space-management-with-sink-nodes"></a>具有接收節點的磁碟空間管理
* 在您的 [檔案 **接收** ] 節點中，您現在可以指定 IoT Edge 模組的即時影片分析可使用多少磁碟空間來儲存已處理的影像。 若要這樣做，請將 **`maximumSizeMiB`** 欄位加入至 FileSink 節點。 範例 File Sink 節點如下所示：
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* 在您的 **資產接收器** 節點中，您可以指定 IoT Edge 模組上的即時影片分析可使用多少磁碟空間來儲存已處理的影像。 若要這樣做，請將 **`localMediaCacheMaximumSizeMiB`** 欄位新增至 [資產接收] 節點。 範例資產接收器節點如下所示：
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  檔案 **接收** 路徑會分割成基底目錄路徑和檔案名模式，而 **資產接收** 路徑則包含基底目錄路徑。  

### <a name="frame-rate-management"></a>畫面播放速率管理
* **`MediaGraphFrameRateFilterProcessor`** 在 **IoT Edge 2.0 模組的即時影片分析** 中已淘汰。
    * 若要取樣傳入影片以進行處理，請將 **`samplingOptions`** 屬性新增至 MediaGraph 延伸處理器 (`MediaGraphHttpExtension` 或 `MediaGraphGrpcExtension`)   
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>使用 Telegraf 的 Prometheus 格式模組計量
在此版本中，您可以使用 Telegraf 將計量傳送至 Azure 監視器。 從該處，計量可能會導向至 Log Analytics 或事件中樞。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="事件的分類法":::

您可以使用 docker 輕鬆地產生具有自訂設定的 Telegraf 映射。 在 [ [監視和記錄](monitoring-logging.md#azure-monitor-collection-via-telegraf) ] 頁面中深入瞭解。

## <a name="next-steps"></a>下一步

[開始使用 IoT Edge 的即時影片分析](get-started-detect-motion-emit-events-quickstart.md)