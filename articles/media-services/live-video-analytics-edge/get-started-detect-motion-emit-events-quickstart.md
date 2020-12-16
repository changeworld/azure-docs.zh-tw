---
title: 開始使用 IoT Edge 上的 Live Video Analytics - Azure
description: 本快速入門說明如何在 IoT Edge 上開始使用 Live Video Analytics。 了解如何偵測即時影片串流中的動作。
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: db48183e918fcb1096734f912f02c091ddac1d74
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511870"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>快速入門：開始使用 - IoT Edge 上的 Live Video Analytics

本快速入門會逐步引導您完成在 IoT Edge 上開始使用 Live Video Analytics 的步驟。 其使用 Azure VM 作為 IoT Edge 裝置。 其也會使用模擬的即時影片串流。 

完成設定步驟之後，您將能夠透過媒體圖表執行模擬的即時影片串流，以偵測並報告該串流中的任何動作。 下圖以圖形表示該媒體圖表。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="以動作偵測為基礎的 Live Video Analytics":::

您可以觀看下列影片，其中包含如何在 IoT Edge 上開始使用即時影片分析的詳細步驟：

<iframe src="https://www.microsoft.com/en-us/videoplayer/embed/RE4Hcax" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 如果您還未擁有帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 開發電腦上有 [Visual Studio Code](https://code.visualstudio.com/)。 請確定您有 [Azure IoT Tools 延伸模組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
* 確定開發電腦所連線的網路允許透過連接埠 5671 的進階訊息佇列通訊協定 (AMQP)。 此設定可讓 Azure IoT Tools 與 Azure IoT 中樞通訊。

> [!TIP]
> 安裝 Azure IoT Tools 擴充功能時，系統可能會提示您安裝 Docker。 請放心忽略提示。

## <a name="set-up-azure-resources"></a>設定 Azure 資源

本教學課程需要下列 Azure 資源：

* IoT 中樞
* 儲存體帳戶
* Azure 媒體服務帳戶
* Azure 中的 Linux VM，已安裝 [IoT Edge 執行階段](../../iot-edge/how-to-install-iot-edge.md)

在本快速入門中，我們建議您使用 [Live Video Analytics 資源設定指令碼](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)，在您的 Azure 訂用帳戶中部署所需的資源。 若要這樣做，請依照下列步驟執行：

1. 移至 [Azure Cloud Shell](https://shell.azure.com)。
1. 如果您第一次使用 Cloud Shell，系統會提示您選取用來建立儲存體帳戶和 Microsoft Azure 檔案共用的訂用帳戶。 選取 [建立儲存體]，以建立可供儲存 Cloud Shell 工作階段資訊的儲存體帳戶。 此儲存體帳戶與指令碼將建立來搭配您 Azure 媒體服務帳戶使用的帳戶不同。
1. 在 Cloud Shell 視窗左側的下拉式功能表中，選取 [Bash] 作為您的環境。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/env-selector.png" alt-text="環境選取器":::
1. 執行下列命令。

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
如果指令碼成功完成，您應該會在訂用帳戶中看到所有所需資源。 在指令碼輸出中，資源資料表會列出 IoT 中樞名稱。 請尋找 `Microsoft.Devices/IotHubs` 資源類型，並記下該名稱。 在下一個步驟中，您將需要此名稱。 

此指令碼也會在 *~/clouddrive/lva-sample/* 目錄中產生幾個設定檔。 在快速入門稍後的地方會需要這些檔案。

## <a name="deploy-modules-on-your-edge-device"></a>將模組部署到邊緣裝置

從 Cloud Shell 執行下列命令。

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

此命令會將下列模組部署至邊緣裝置，在此案例中為 Linux VM。

* IoT Edge 上的 Live Video Analytics (模組名稱 `lvaEdge`)
* 即時串流通訊協定 (RTSP) 模擬器 (模組名稱 `rtspsim`)

RTSP 模擬器模組會在您執行 [Live Video Analytics 資源設定指令碼](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)時，使用複製到您邊緣裝置的影片檔案來模擬即時影片串流。 

現在模組已完成部署，但沒有任何媒體圖表處於作用中狀態。

## <a name="configure-the-azure-iot-tools-extension"></a>設定 Azure IoT Tools 延伸模組

請遵循下列指示，使用 Azure IoT Tools 延伸模組來連線到您的 IoT 中樞。

1. 在 Visual Studio Code 中，開啟 [延伸模組] 索引標籤 (或按 Ctrl+Shift+X) 並搜尋 Azure IoT 中樞。
1. 按一下滑鼠右鍵，然後選取 [延伸模組設定]。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="延伸模組設定":::
1. 搜尋並啟用「顯示詳細資訊訊息」。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="顯示詳細資訊訊息":::
1. 選取 [檢視] > [Explorer]。 或選取 Ctrl+Shift+E。
1. 在 [總管] 索引標籤的左下角，選取 [Azure IoT 中樞]。
1. 選取 [其他選項] 圖示，以查看捷徑功能表。 然後選取 [設定 IoT 中樞連接字串]。
1. 輸入方塊出現時，請輸入您的 IoT 中樞連接字串。 在 Cloud Shell 中，您可以從 *~/clouddrive/lva-sample/appsettings.json* 取得連接字串。

如果連線成功，便會出現邊緣裝置清單。 您應該至少會看到一個名為 **lva-sample-device** 的裝置。 您現在可以透過捷徑功能表來管理 IoT Edge 裝置，並與 Azure IoT 中樞互動。 若要檢視邊緣裝置上所部署的模組，請在 [lva-sample-device] 底下展開 [模組] 節點。

![lva-sample-device 節點](./media/quickstarts/lva-sample-device-node.png)

> [!TIP]
> 如果您已自行在邊緣裝置 (例如 ARM64 裝置) 上[手動將即時影片分析部署到 IoT Edge](deploy-iot-edge-device.md)，則會看到此模組顯示在 Azure IoT 中樞下的該裝置底下。 您可以選取該模組，並遵循下列其餘步驟。

## <a name="use-direct-method-calls"></a>使用直接方法呼叫

您可以使用模組，藉由叫用直接方法來分析即時影片串流。 如需詳細資訊，請參閱 [IoT Edge 上 Live Video Analytics 的直接方法](direct-methods.md)。 

### <a name="invoke-graphtopologylist"></a>叫用 GraphTopologyList

若要列舉模組中的所有[圖表拓撲](media-graph-concept.md#media-graph-topologies-and-instances)：

1. 在 Visual Studio Code 中，以滑鼠右鍵按一下 [lvaEdge] 模組，然後選取 [叫用模組直接方法]。
1. 在出現的方塊中，輸入 *GraphTopologyList*。
1. 複製下列 JSON 承載，然後將其貼到方塊中。 然後選取 Enter 鍵。

    ```
    {
        "@apiVersion" : "2.0"
    }
    ```

    在幾秒內，[輸出] 視窗便會顯示下列回應。

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    這是符合預期的回應，因為尚未建立任何圖表拓撲。
    

### <a name="invoke-graphtopologyset"></a>叫用 GraphTopologySet

藉由使用可叫用 `GraphTopologyList` 的步驟，您可以叫用 `GraphTopologySet` 來設定[圖表拓撲](media-graph-concept.md#media-graph-topologies-and-instances)。 使用下列 JSON 作為承載。

```
{
    "@apiVersion": "2.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```

此 JSON 承載會建立可定義三個參數的圖表拓撲。 其中的兩個參數有預設值。 拓撲具有一個來源 (RTSP 來源) 節點、一個處理器 (動作偵測處理器) 節點，以及一個接收 (IoT 中樞接收) 節點。

在幾秒內，您會在 [輸出] 視窗中看到下列回應。

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

傳回的狀態為 201。 此狀態表示已建立新的拓撲。 

請嘗試下列後續步驟：

1. 再次叫用 `GraphTopologySet`。 傳回的狀態碼為 200。 此狀態碼表示已成功更新現有的拓撲。
1. 再次叫用 `GraphTopologySet`，但變更描述字串。 傳回的狀態碼是 200，而描述則會更新為新的值。
1. 叫用 `GraphTopologyList`，如上一節所述。 現在您可以在傳回的承載中看到 `MotionDetection` 拓撲。

### <a name="invoke-graphtopologyget"></a>叫用 GraphTopologyGet

使用下列承載來叫用 `GraphTopologyGet`。

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

在幾秒內，您會在 [輸出] 視窗中看到下列回應：

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

在回應承載中，請注意下列詳細資料：

* 狀態碼為 200，表示成功。
* 承載包含 `created` 時間戳記和 `lastModified` 時間戳記。

### <a name="invoke-graphinstanceset"></a>叫用 GraphInstanceSet

建立參考上述圖表拓撲的圖表執行個體。 圖表執行個體可讓您使用相同的圖表拓撲，分析來自許多攝影機的即時影片串流。 如需詳細資訊，請參閱[媒體圖表拓撲和執行個體](media-graph-concept.md#media-graph-topologies-and-instances)。

使用下列承載來叫用 `GraphInstanceSet` 直接方法。

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

請注意，此承載：

* 會指定需要為其建立執行個體的拓撲名稱 (`MotionDetection`)。
* 會包含 `rtspUrl` 的參數值，其在圖表拓撲承載中沒有預設值。

在幾秒內，您會在 [輸出] 視窗中看到下列回應：

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

在回應承載中，請注意：

* 狀態碼為 201，表示已建立新的執行個體。
* 狀態為 `Inactive`，表示圖表執行個體已建立但未啟動。 如需詳細資訊，請參閱[媒體圖表狀態](media-graph-concept.md)。

請嘗試下列後續步驟：

1. 使用相同的承載再次叫用 `GraphInstanceSet`。 請注意，傳回的狀態碼為 200。
1. 再次叫用 `GraphInstanceSet`，但使用不同的描述。 請注意回應承載中的描述已更新，表示您已成功更新圖表執行個體。
1. 叫用 `GraphInstanceSet`，但將名稱變更為 `Sample-Graph-2`。 在回應承載中，請注意新建立的圖表執行個體 (也就是狀態碼 201)。

### <a name="invoke-graphinstanceactivate"></a>叫用 GraphInstanceActivate

現在啟動圖表執行個體以透過模組開始即時影片的流程。 使用下列承載來叫用 `GraphInstanceActivate` 直接方法。

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

在幾秒內，您會在 [輸出] 視窗中看到下列回應。

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

狀態碼 200 表示已成功啟動圖表執行個體。

### <a name="invoke-graphinstanceget"></a>叫用 GraphInstanceGet

現在使用下列承載來叫用 `GraphInstanceGet` 直接方法。

```
 {
     "@apiVersion" : "2.0",
     "name" : "Sample-Graph-1"
 }
 ```

在幾秒內，您會在 [輸出] 視窗中看到下列回應。

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

在回應承載中，請注意下列詳細資料：

* 狀態碼為 200，表示成功。
* 狀態為 `Active`，表示圖表執行個體目前為作用中。

## <a name="observe-results"></a>觀察結果

我們已建立及啟動的圖表執行個體會使用動作偵測處理器節點來偵測傳入即時影片串流中的動作。 其會將事件傳送至 IoT 中樞接收節點。 這些事件會轉送到 IoT Edge 中樞。 

若要觀察結果，請遵循下列步驟。

1. 在 Visual Studio Code 中，開啟 [總管] 窗格。 在左下角尋找 [Azure IoT 中樞]。
2. 展開 [裝置] 節點。
3. 以滑鼠右鍵按一下 [lva-sample-device]，然後選取 [開始監視內建事件監視]。

    ![開始監視 Iot 中樞事件](./media/quickstarts/start-monitoring-iothub-events.png)
    
[輸出] 視窗會顯示下列訊息：

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

請注意下列詳細資料：

* 訊息包含 `body` 區段和 `applicationProperties` 區段。 如需詳細資訊，請參閱[建立及讀取 IoT 中樞訊息](../../iot-hub/iot-hub-devguide-messages-construct.md)。
* 在 `applicationProperties` 中，`subject` 會參考訊息產生來源 `MediaGraph` 中的節點。 在此案例中，訊息來自動作偵測處理器。
* 在 `applicationProperties` 中，`eventType` 表示此事件是分析事件。
* `eventTime` 值是事件的發生時間。
* `body` 包含有關分析事件的資料。 在此情況下，事件是推斷事件，因此主體包含 `timestamp` 和 `inferences` 資料。
* `inferences` 區段表示 `type` 是 `motion`。 其會提供有關 `motion` 事件的其他資料。

如果您讓媒體圖表執行一段時間，就會在 [輸出] 視窗中看到下列訊息。

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

在此訊息中，請注意下列詳細資料：

* 在 `applicationProperties` 中，`subject` 表示訊息是從媒體圖表中的 RTSP 來源節點所產生。
* 在 `applicationProperties` 中，`eventType` 表示這是診斷事件。
* `body` 包含診斷事件的相關資料。 在此案例中，訊息會包含主體，因為事件是 `MediaSessionEstablished`。

## <a name="invoke-additional-direct-methods-to-clean-up"></a>叫用其他直接方法來清除

叫用直接方法以便先停用圖表執行個體，然後再將其刪除。

### <a name="invoke-graphinstancedeactivate"></a>叫用 GraphInstanceDeactivate

使用下列承載來叫用 `GraphInstanceDeactivate` 直接方法。

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

在幾秒內，您會在 [輸出] 視窗中看到下列回應：

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

狀態碼 200 表示已成功停用圖表執行個體。

接下來，請嘗試叫用 `GraphInstanceGet`，如本文先前所述。 觀察 `state` 值。

### <a name="invoke-graphinstancedelete"></a>叫用 GraphInstanceDelete

使用下列承載來叫用 `GraphInstanceDelete` 直接方法。

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

在幾秒內，您會在 [輸出] 視窗中看到下列回應：

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

狀態碼 200 表示已成功刪除圖表執行個體。

### <a name="invoke-graphtopologydelete"></a>叫用 GraphTopologyDelete

使用下列承載來叫用 `GraphTopologyDelete` 直接方法。

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

在幾秒內，您會在 [輸出] 視窗中看到下列回應。

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

狀態碼 200 表示已成功刪除圖表拓撲。

請嘗試下列後續步驟：

1. 叫用 `GraphTopologyList`，並觀察模組並未包含任何圖表拓撲。
1. 使用與 `GraphTopologyList` 相同的承載來叫用 `GraphInstanceList`。 您會觀察到系統不會列舉任何圖表執行個體。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請刪除您在本快速入門中建立的資源。

## <a name="next-steps"></a>後續步驟

* 了解如何[在 IoT Edge 上使用 Live Video Analytics 來錄製影片](continuous-video-recording-tutorial.md)。
* 深入了解[診斷訊息](monitoring-logging.md)。