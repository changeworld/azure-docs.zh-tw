---
title: 偵測動作並發出事件 - Azure
description: 本快速入門說明如何透過程式設計方式呼叫直接方法，在 IoT Edge 上使用 Live Video Analytics 來偵測動作並發出事件。
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 4986ea13bec5382a8e0ef791e75442e4333e4356
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261584"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>快速入門：偵測動作並發出事件

本快速入門會逐步引導您完成在 IoT Edge 上開始使用 Live Video Analytics 的步驟。 這裡使用 Azure VM 做為 IoT Edge 裝置和模擬的即時影片串流。 完成設定步驟之後，您將能夠透過媒體圖表執行模擬的即時影片串流，以偵測並報告該串流中的任何動作。 下圖顯示該媒體圖表的圖表表示。

![以動作偵測為基礎的 Live Video Analytics](./media/analyze-live-video/motion-detection.png) 

本文是以利用 C# 撰寫的[範例程式碼](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp)為基礎。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 您電腦上的 [Visual Studio Code](https://code.visualstudio.com/)，搭配下列延伸模組：
    1. [Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    2. [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) 已安裝在您的系統上

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

    如果指令碼順利完成，您應該會看到您訂用帳戶中提及的所有資源。

1. 在指令碼完成後，按一下大括弧以公開資料夾結構。 您會看到建立在 ~/clouddrive/lva-sample 目錄下的一些檔案。 與本快速入門相關的項目包括：

     * ~/clouddrive/lva-sample/edge-deployment/.env - 包含 Visual Studio Code 用來將模組部署至邊緣裝置的屬性
     * ~/clouddrive/lva-sample/appsetting.json - Visual Studio Code 用來執行範例程式碼
     
在本快速入門稍後，您將需要這些項目，才能在 Visual Studio Code 中更新檔案。 目前，您可以將它們複製到本機檔案。


 ![應用程式設定](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>設定開發環境

1. 從這裡複製存放庫 https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp 。
1. 啟動 Visual Studio Code 並開啟下載存放庫所在的資料夾。
1. 在 Visual Studio Code 中，瀏覽至 "src/cloud-to-device-console-app" 資料夾，並建立名為 "appsettings" 的檔案。 此檔案將包含執行程式所需的設定。
1. 複製上一節 (請參閱步驟 5) 產生的 ~/clouddrive/lva-sample/appsettings.json 檔案中的內容

    文字應會顯示如下：

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. 接下來，瀏覽至 "src/edge" 資料夾，並建立名為 ".env" 的檔案。
1. 複製 "/clouddrive/lva-sample/edge-deployment/.env" 檔案中的內容。 文字應會顯示如下：

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>檢查範例檔案

1. 在 Visual Studio Code 中，瀏覽至 "src/edge"。 您會看到您所建立的 .env 檔案以及一些部署範本檔案。

    部署範本會使用一些預留位置值來參考邊緣裝置的部署資訊清單。 .env 檔案具有這些變數的值。
1. 接下來，瀏覽至 "src/cloud-to-device-console-app" 資料夾。 您在這裡會看到您建立的 appsettings.json 檔案，以及一些其他檔案：

    * c2d-console-app.csproj - Visual Studio Code 的專案檔。
    * operations.json - 此檔案會列出您需要程式執行的不同作業。
    * Program.cs - 執行下列動作的範例程式碼：
    
        * 載入應用程式設定
        * 叫用 IoT Edge 模組上 Live Video Analytics 所公開的直接方法。 您可以使用模組，藉由叫用其[直接方法](direct-methods.md)來分析即時影片串流 
        * 暫停以讓您在終端機視窗中查看程式的輸出，並在 [輸出] 視窗中查看模組所產生的事件
        * 叫用直接方法來清除資源   

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>產生及部署 IoT Edge 部署資訊清單

部署資訊清單會定義要將部署至邊緣裝置的模組，以及這些模組的組態設定。 請遵循下列步驟，從範本檔案產生這類資訊清單，然後將其部署到邊緣裝置。

1. 開啟 Visual Studio Code
1. 按一下左下角 [AZURE IOT 中樞] 窗格旁的 [其他動作] 圖示，以設定 IoT 中樞連接字串。 您可以從 src/cloud-to-device-console-app/appsettings.json 檔案複製字串。 

    ![設定 IOT 連接字串](./media/quickstarts/set-iotconnection-string.png)
1. 接下來，以滑鼠右鍵按一下「src/edge/deployment.template.json」檔案，然後按一下 [產生 IoT Edge 部署資訊清單]。
    ![產生 IoT Edge 部署資訊清單](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    這應該會在 src/edge/config 資料夾中建立名為「deployment.amd64.json」的資訊清單檔。
1. 以滑鼠右鍵按一下「src/edge/config/deployment.amd64.json」，然後按一下 [建立單一裝置的部署]，並選取您的 Edge 裝置的名稱。

    ![建立單一裝置的部署](./media/quickstarts/create-deployment-single-device.png)
1. 接著，系統會要求您「選取 IoT 中樞裝置」。 從下拉式選單中選取 [lva-sample-device]。
1. 在大約 30 秒內，重新整理左下方區段的 Azure IoT 中樞，您應該會看到邊緣裝置已部署下列模組：

    * IoT Edge 上的 Live Video Analytics (名為 "lvaEdge" 的模組)
    * RTSP 模擬器 (模組名稱「rtspsim」)

RTSP 模擬器模組會在您執行 [Live Video Analytics 資源設定指令碼](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)時，使用複製到您邊緣裝置的影片檔案來模擬即時影片串流。 在這個階段，您已部署模組，但沒有任何媒體圖表處於作用中狀態。

## <a name="prepare-for-monitoring-events"></a>準備監視事件

您將會在 IoT Edge 模組上使用 Live Video Analytics，以偵測傳入即時影片串流中的動作，並將事件傳送至 IoT 中樞。 若要查看這些事件，請遵循下列步驟：

1. 在 Visual Studio Code 中開啟 [Explorer] 窗格，然後在左下角尋找 Azure IoT 中樞。
1. 展開 [裝置] 節點。
1. 以滑鼠右鍵按一下 [lva-sample-device]，然後選擇 [開始監視內建事件監視] 選項。

    ![開始監視內建事件端點](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>執行範例程式

遵循下列步驟來執行程式碼範例。
1. 在 Visual Studio Code 中，瀏覽至 "src/cloud-to-device-console-app/operations.json"。
1. 在 GraphTopologySet 節點底下，確保下列各項：

    ` "topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. 接下來，在 GraphInstanceSet 和 GraphTopologyDelete 節點下，確定 topologyName 的值符合上述圖表拓撲中的「name」屬性值：

    `"topologyName" : "MotionDetection"`
    
1. 啟動偵錯工作階段 (按 F5 鍵)。 您會開始看到列印在 [終端機 ] 視窗中某些訊息。
1. operations.json 首先會呼叫 GraphTopologyList 和 GraphInstanceList。 如果您已在先前的快速入門之後清除資源，這會傳回空白清單，然後暫停以讓您按下 Enter 鍵。

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
        "@apiVersion": "1.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
        "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
1. 當您在 [終端機] 視窗中按下 "Enter" 鍵時，將會呼叫下一組直接方法
     
     * 使用上述 topologyUrl 呼叫 GraphTopologySet
     * 使用下列主體呼叫 GraphInstanceSet
     
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph",
       "properties": {
         "topologyName": "MotionDetection",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
           },
           {
             "name": "rtspUserName",
             "value": "testuser"
           },
           {
             "name": "rtspPassword",
             "value": "testpassword"
           }
         ]
       }
     }
     ```
     
     * 呼叫 GraphInstanceActivate 以啟動圖表執行個體，並啟動影片流程。
     * 再次呼叫 GraphInstanceList，以顯示圖表執行個體確實處於執行中狀態。
1. [終端機] 視窗中的輸出現在會在出現「按 Enter 繼續」提示時暫停。 此時不要按 "Enter" 鍵。 您可以向上捲動查看您所叫用直接方法的 JSON 回應承載
1. 如果您現在於 Visual Studio Code 中切換到 [輸出] 視窗，將會看到 IoT Edge 模組上 Live Video Analytics 傳送至 IoT 中樞的訊息。
     * 下列各節將討論這些訊息
1. 媒體圖表會繼續執行，並列印結果 - RTSP 模擬器會持續循環播放來源影片。 若要停止媒體圖表，請回到 [終端機] 視窗，然後按 "Enter" 鍵。 這會進行下一系列的呼叫以清除資源：
     * 呼叫 GraphInstanceDeactivate 以停用圖表執行個體
     * 呼叫 GraphInstanceDelete 以刪除執行個體
     * 呼叫 GraphTopologyDelete 以刪除拓撲
     * 最後一次呼叫 GraphTopologyList 以顯示清單現在是空的

## <a name="interpret-results"></a>解譯結果

當您執行媒體圖表時，來自動作偵測處理器節點的結果會透過 IoT 中樞接收節點傳送至 IoT 中樞。 您在 Visual Studio Code 的 [輸出] 視窗中看到的訊息包含 "body" 區段和 "applicationProperties" 區段。 若要了解這些區段所代表的意思，請參閱[這篇](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)文章 (機器翻譯)。

在下列訊息中，Live Video Analytics 模組會定義應用程式屬性和主體的內容。

## <a name="mediasession-established-event"></a>MediaSessionEstablished 事件

具現化媒體圖表時，RTSP 來源節點會嘗試連線到在 rtspsim-live555 容器中執行的 RTSP 伺服器。 如果成功，則會列印此事件：

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-04-09T16:42:18.1280000Z"  
    }  
}
```

* 此訊息為診斷事件 MediaSessionEstablished，表示 RTSP 來源節點 (主體) 能夠與 RTSP 模擬器建立連線，並開始接收 (模擬的) 即時摘要。
* ApplicationProperties 中的 "subject" 會參考產生訊息的圖形拓撲中的節點。 在此案例中，訊息是來自 RTSP 來源節點。
* applicationProperties 中的 "eventType" 表示這是診斷事件。
* "eventTime" 表示事件發生的時間。
* "body" 包含有關診斷事件的資料，在此案例中為 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 詳細資料。


## <a name="motion-detection-event"></a>動作偵測事件

當偵測到動作時，Live Video Analytics Edge 模組會傳送推斷事件。 類型會設定為 “motion”，表示這是來自動作偵測處理器的結果，而 eventTime 會告訴您發生動作的時間 (UTC)。 以下是範例：

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

* applicationProperties 中的 "subject" 會參考產生訊息之媒體圖表中的節點。 在此情況下，訊息是來自動作偵測處理器節點。
* applicationProperties 中的「eventType」表示這是 Analytics 事件。
* 「eventTime」表示事件發生的時間。
「主體」包含有關分析事件的資料。 在此情況下，事件為推斷事件，因此主體包含「時間戳記」和「推斷」資料。
* "inferences" 資料指出 "type" 為 "motion"，並包含有關 "motion" 事件的其他資料。
* "box" 區段包含移動物件外圍週框方塊的座標。 這些值會依影片的寬度和高度 (以像素為單位) 進行一般化 (例如 寬度 1920 和高度 1080)。

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="cleanup-resources"></a>清除資源

如果您想要嘗試其他快速入門，應該保留所建立的資源。 否則，請移至 Azure 入口網站，瀏覽至您的資源群組並選取您執行本快速入門所用的資源群組，然後刪除所有資源。

## <a name="next-steps"></a>後續步驟

執行其他快速入門，例如，偵測即時影片摘要中的物件。        
