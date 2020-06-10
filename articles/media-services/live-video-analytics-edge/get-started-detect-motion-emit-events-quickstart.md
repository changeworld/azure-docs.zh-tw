---
title: 開始使用 IoT Edge 上的 Live Video Analytics - Azure
description: 本快速入門說明如何在 IoT Edge 上開始使用 Live Video Analytics，並偵測即時影片串流中的動作。
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 307a81938be3e25b8a6a07bb3696ca3b7647c0aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261562"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>快速入門：開始使用 - IoT Edge 上的 Live Video Analytics

本快速入門會逐步引導您完成在 IoT Edge 上開始使用 Live Video Analytics 的步驟。 這裡使用 Azure VM 做為 IoT Edge 裝置和模擬的即時影片串流。 完成設定步驟之後，您將能夠透過媒體圖表執行模擬的即時影片串流，以偵測並報告該串流中的任何動作。 下圖顯示該媒體圖表的圖表表示。

![以動作偵測為基礎的 Live Video Analytics](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 您開發電腦上的 [Visual Studio Code](https://code.visualstudio.com/)，搭配 [Azure IoT Tools 延伸模組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
* 您的開發電腦所連線的網路，應允許透過連接埠 5671 的 AMQP 通訊協定 (讓 Azure IoT Tools 可與 Azure IoT 中樞通訊)。

> [!TIP]
> 安裝 Azure IoT Tools 延伸模組時，系統可能會提示您安裝 Docker。 您可以視需要予以略過。

## <a name="set-up-azure-resources"></a>設定 Azure 資源

本教學課程需要下列 Azure 資源。

* IoT 中樞
* 儲存體帳戶
* Azure 媒體服務帳戶
* Azure 中的 Linux VM，已安裝 [IoT Edge 執行階段](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

在本快速入門中，我們建議您使用 [Live Video Analytics 資源設定指令碼](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)，在您的 Azure 訂用帳戶中部署前述的 Azure 資源。 若要這樣做，請依照下列步驟執行：

1. 瀏覽至 https://shell.azure.com 。
1. 如果這是您第一次使用 Cloud Shell，系統會提示您選取用來建立儲存體帳戶和 Microsoft Azure 檔案共用的訂用帳戶。 選取 [建立儲存體]，以建立用來儲存您 Cloud Shell 工作階段資訊的儲存體帳戶。 此儲存體帳戶與指令碼將建立來搭配您 Azure 媒體服務帳戶使用的帳戶不同。
1. 在 Shell 視窗左側的下拉式選單中，選取 [Bash] 做為您的環境。

    ![環境選取器](./media/quickstarts/env-selector.png)

1. 執行下列命令

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
如果指令碼順利完成，您應該會看到您訂用帳戶中提及的所有資源。 在指令碼輸出中，將會產生一份資源資料表，當中會列出 IoT 中樞名稱。 尋找「Microsoft.Devices/IotHubs」的資源類型，並記下該名稱。 在下一個步驟中，您將需要此項目。 此指令碼也會在 ~/clouddrive/lva-sample/ 目錄中產生幾個組態檔，您稍後會在快速入門中需要這些檔案。

## <a name="deploy-modules-on-your-edge-device"></a>將模組部署到邊緣裝置

從 Cloud Shell 執行下列命令

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

上述命令會將下列模組部署至邊緣裝置 (Linux VM)：

* IoT Edge 上的 Live Video Analytics (名為「lvaEdge」的模組)
* RTSP 模擬器 (模組名稱「rtspsim」)

RTSP 模擬器模組會在您執行 [Live Video Analytics 資源設定指令碼](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)時，使用複製到您邊緣裝置的影片檔案來模擬即時影片串流。 在這個階段，您已部署模組，但沒有任何媒體圖表處於作用中狀態。

## <a name="configure-azure-iot-tools-extension-in-visual-studio-code"></a>在 Visual Studio Code 中設定 Azure IoT Tools 延伸模組

啟動 Visual Studio Code，並遵循下列指示，使用 Azure IoT Tools 延伸模組連線到您的 Azure IoT 中樞。

1. 透過 [檢視] > [Explorer] 或直接按 (Ctrl+Shift+E)，瀏覽至 Visual Studio Code 中的 [Explorer] 索引標籤。
1. 在 [Explorer] 索引標籤中，按一下左下角的 [Azure IoT 中樞]。
1. 按一下 [更多選項] 圖示以查看操作功能表，然後選取 [設定 IoT 中樞連接字串] 選項。
1. 隨即會快顯輸入方塊，然後輸入您的 IoT 中樞連接字串。 您可以從 Cloud Shell 中的 ~/clouddrive/lva-sample/appsettings.json 取得 IoT 中樞的連接字串。
1. 如果連線成功，將會顯示邊緣裝置的清單。 應該至少有一個裝置，名為「lva-sample-device」。
1. 您現在可以透過操作功能表來管理 IoT Edge 裝置，並與 Azure IoT 中樞互動。
1. 您可以展開 [lva-sample-device] 底下的 [模組] 節點，來檢視邊緣裝置上部署的模組。

    ![lva-sample-device 節點](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-methods"></a>使用直接方法

您可以藉由叫用直接方法，使用模組來分析即時影片串流。 閱讀 [IoT Edge 上 Live Video Analytics 的直接方法](direct-methods.md)，以了解模組所提供的所有直接方法。 

### <a name="invoke-graphtopologylist"></a>叫用 GraphTopologyList
這會列舉模組中的所有[圖表拓撲](media-graph-concept.md#media-graph-topologies-and-instances)。

1. 以滑鼠右鍵按一下 [lvaEdge] 模組，然後從操作功能表中選取 [叫用模組直接方法]。
1. 您會在 Visual Studio Code 視窗的頂端中央看到快顯編輯方塊。 在編輯方塊中輸入「GraphTopologyList」，然後按 Enter。
1. 接下來，在編輯方塊中複製並貼上下列 JSON 承載，然後按 Enter 鍵。

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    在幾秒內，您會在 Visual Studio Code 快顯中看到 [輸出] 視窗，其中包含下列回應

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
    
    預期會有上述回應，因為尚未建立任何圖表拓撲。
    

### <a name="invoke-graphtopologyset"></a>叫用 GraphTopologySet

使用與叫用 GraphTopologyList 所概述的相同步驟，您可以使用下列 JSON 做為承載，來叫用 GraphTopologySet 以設定[圖表拓撲](media-graph-concept.md#media-graph-topologies-and-instances)。

```
{
    "@apiVersion": "1.0",
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


上述 JSON 承載會造成建立圖表拓撲，以定義三個參數 (其中兩個具有預設值)。 拓撲具有一個來源 (RTSP 來源) 節點、一個處理器 (動作偵測處理器) 節點，以及一個接收 (IoT 中樞接收) 節點。

在幾秒內，您會在 [輸出] 視窗中看到下列回應：

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

傳回的狀態為 201，表示已建立新的拓撲。 在後續步驟中，請嘗試下列項目：

* 再次叫用 GraphTopologySet，並注意傳回的狀態碼為 200。 狀態碼 200 表示已成功更新現有的拓撲。
* 再次叫用 GraphTopologySet，但變更描述字串。 請注意，回應中的狀態碼是 200，而描述會更新為新的值。
* 如前一節中所概述叫用 GraphTopologyList，並請注意，現在您可以在傳回的承載中看到「MotionDetection」拓撲。

### <a name="invoke-graphtopologyget"></a>叫用 GraphTopologyGet

現在使用下列承載來叫用 GraphTopologyGet

```

{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

在幾秒內，您應該會在 [輸出] 視窗中看到下列回應：

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

請注意下列回應承載中的事項：

* 狀態碼為 200，表示成功。
* 承載具有「created」和「lastModified」時間戳記。

### <a name="invoke-graphinstanceset"></a>叫用 GraphInstanceSet

接下來，建立參考上述圖表拓撲的圖表執行個體。 如[這裡](media-graph-concept.md#media-graph-topologies-and-instances)所述，圖表執行個體可讓您使用相同的圖表拓撲，分析來自許多攝影機的即時影片串流。

叫用具有下列承載的直接方法 GraphInstanceSet。

```
{
    "@apiVersion" : "1.0",
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

請注意：

* 上述承載會指定需要建立執行個體的拓撲名稱 (MotionDetection)。
* 承載包含「rtspUrl」的參數值，在圖表拓撲承載中沒有預設值。

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

請注意下列回應承載中的事項：

* 狀態碼為 201，表示已建立新的執行個體。
* 狀態為「非作用中」，表示圖表執行個體已建立但未啟動。 如需詳細資訊，請參閱[媒體圖表狀態](media-graph-concept.md)。

在後續步驟中，請嘗試下列項目：

* 使用相同的承載再次叫用 GraphInstanceSet，並請注意，傳回的狀態碼現在是 200。
* 再次叫用 GraphInstanceSet，但使用不同的描述，並請注意回應承載中的更新描述，表示已成功更新圖表執行個體。
* 叫用 GraphInstanceSet，但將名稱變更為「Sample-Graph-2」，然後觀察回應承載。 請注意，會建立新的圖表執行個體 (亦即狀態碼為 201)。

### <a name="invoke-graphinstanceactivate"></a>叫用 GraphInstanceActivate

現在啟動圖表執行個體 - 這會透過模組開始即時影片的流程。 叫用具有下列承載的直接方法 GraphInstanceActivate。

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

在幾秒內，您應該會在 [輸出] 視窗中看到下列回應：

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

回應承載中的狀態碼 200 表示已成功啟動圖表執行個體。

### <a name="invoke-graphinstanceget"></a>叫用 GraphInstanceGet

現在叫用具有下列承載的直接方法 GraphInstanceGet：

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

在幾秒內，您應該會在 [輸出] 視窗中看到下列回應：

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

請注意下列回應承載中的事項：

* 狀態碼為 200，表示成功。
* 狀態為「作用中」，表示圖表執行個體現在處於「作用中」狀態。

## <a name="observe-results"></a>觀察結果

我們在上面建立及啟動的圖表執行個體會使用 [動作偵測處理器] 節點來偵測傳入即時影片串流中的動作，並將事件傳送至 [IoT 中樞接收] 節點。 接著，這些事件會轉送到您的 IoT Edge 中樞，現在可以觀察到。 若要這樣做，請遵循下列步驟。

1. 在 Visual Studio Code 中開啟 [Explorer] 窗格，然後在左下角尋找 Azure IoT 中樞。
2. 展開 [裝置] 節點。
3. 以滑鼠右鍵按一下 [lva-sample-device]，然後選擇 [開始監視內建事件監視] 選項。

![開始監視 Iot 中樞事件](./media/quickstarts/start-monitoring-iothub-events.png)

您應該會看到下列訊息出現在 [輸出] 視窗中：

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

請注意上述訊息中的下列事項

* 訊息包含「body」區段和「applicationProperties」區段。 若要了解這些章節所代表的內容，請閱讀[建立及讀取 IoT 中樞訊息](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)一文。
* applicationProperties 中的「主體」會參考產生訊息之來源 MediaGraph 中的節點。 在此情況下，訊息是來自動作偵測處理器。
* applicationProperties 中的「eventType」表示這是分析事件。
* 「eventTime」表示事件發生的時間。
* 「主體」包含有關分析事件的資料。 在此情況下，事件為推斷事件，因此主體包含「時間戳記」和「推斷」資料。
* 「推斷」區段表示「類型」為「動作」，且有關於「動作」事件的其他資料。

如果您讓 MediaGraph 執行一段時間，則也會在 [輸出] 視窗中看到下列訊息：

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

請注意上述訊息中的下列事項

* applicationProperties 中的「主體」表示訊息是從媒體圖表中的 RTSP 來源節點所產生。
* applicationProperties 中的「eventType」表示這是診斷事件。
* 「主體」包含診斷事件的相關資料。 在此情況下，事件為 MediaSessionEstablished，因此是主體。

## <a name="invoke-additional-direct-methods-to-clean-up"></a>叫用其他直接方法來清除

現在，叫用直接方法來停用及刪除圖表執行個體 (依該順序)。

### <a name="invoke-graphinstancedeactivate"></a>叫用 GraphInstanceDeactivate

叫用具有下列承載的直接方法 GraphInstanceDeactivate。

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

在幾秒內，您應該會在 [輸出] 視窗中看到下列回應：

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

狀態碼 200 表示已成功停用圖表執行個體。

在後續步驟中，請嘗試下列項目。

* 如先前章節所示叫用 GraphInstanceGet，並觀察「狀態」值。

### <a name="invoke-graphinstancedelete"></a>叫用 GraphInstanceDelete

叫用具有下列承載的直接方法 GraphInstanceDelete

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

在幾秒內，您應該會在 [輸出] 視窗中看到下列回應：

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

回應中的狀態碼 200 表示已成功刪除圖表執行個體。

### <a name="invoke-graphtopologydelete"></a>叫用 GraphTopologyDelete

叫用具有下列承載的直接方法 GraphTopologyDelete：

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

在幾秒內，您應該會在 [輸出] 視窗中看到下列回應：

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

狀態碼 200 表示已成功刪除圖表拓撲。

在後續步驟中，請嘗試下列項目。

* 叫用 GraphTopologyList，並觀察模組中沒有任何圖表拓撲。
* 使用與 GraphTopologyList 相同的承載來叫用 GraphInstanceList，並觀察不會列舉任何圖表執行個體。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請刪除本快速入門中建立的資源。

## <a name="next-steps"></a>後續步驟

* 了解如何使用 IoT Edge 上的 Live Video Analytics 來錄製影片
* 深入了解診斷訊息。
