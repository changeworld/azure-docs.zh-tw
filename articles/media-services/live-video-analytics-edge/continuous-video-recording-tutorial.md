---
title: 錄製到雲端並從雲端播放的連續影片 - Azure
description: 在本教學課程中，您將了解如何在 IoT Edge 上使用 Live Video Analytics，以持續將影片錄製到雲端，並使用 Azure 媒體服務串流該影片的所有部分。
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: a69d3f5db9dd8cbe25bbf79f44921f26258005cc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259984"
---
# <a name="tutorial-continuous-video-recording-to-cloud-and-playback-from-cloud"></a>教學課程：錄製到雲端並從雲端播放的連續影片  

在本教學課程中，您將了解如何在 IoT Edge 上使用 Live Video Analytics，以執行[持續影片錄製](continuous-video-recording-concept.md) (CVR) 到雲端，並使用媒體服務串流該影片的所有部分。 這適用於安全性、合規性及其他等案例，其中需要維護攝影機多日 (或數週) 的畫面封存。

> [!div class="checklist"]
> * 設定相關資源
> * 檢查執行 CVR 的程式碼
> * 執行範例程式碼
> * 檢查結果並檢視影片

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>建議的預先閱讀  

建議您閱讀下列文件頁面

* [IoT Edge 上的 Live Video Analytics 概觀](overview.md)
* [IoT Edge 上的 Live Video Analytics 術語](terminology.md)
* [媒體圖表概念](media-graph-concept.md) 
* [連續影片錄製案例](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>必要條件

本教學課程的必要條件如下所示

* 您開發電腦上的 [Visual Studio Code](https://code.visualstudio.com/)，搭配 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 延伸模組和 [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 延伸模組。

    > [!TIP]
    > 系統可能會提示您安裝 Docker。 您可以忽略此提示。
* 開發電腦上的 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer)。
* 完成 [Live Video Analytics 資源設定指令碼](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)

在上述步驟結束時，您會在 Azure 訂用帳戶中部署特定的 Azure 資源，包括：

* IoT 中樞
* 儲存體帳戶
* Azure 媒體服務帳戶
* Azure 中的 Linux VM，已安裝 [IoT Edge 執行階段](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

## <a name="concepts"></a>概念

如[這裡](media-graph-concept.md)所說明，媒體圖表可讓您定義應該從哪裡擷取媒體、如何處理媒體，以及應該在哪裡傳遞媒體。 若要完成 CVR，您必須從具備 RTSP 功能的攝影機中捕捉影片，並持續將其錄製到 [Azure 媒體服務資產](terminology.md#asset)。 下圖顯示該媒體圖表的圖表表示。

![媒體圖表](./media/continuous-video-recording-tutorial/continuous-video-recording-overview.png)

在本教學課程中，您將使用以 [Live555 媒體伺服器](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) 建置的一個 Edge 模組來模擬 RTSP 攝影機。 在媒體圖表內，您將使用 [RTSP 來源](media-graph-concept.md#rtsp-source)節點來取得即時摘要，並將該影片傳送至[資產接收節點](media-graph-concept.md#asset-sink)，其會將影片錄製到資產中。

## <a name="set-up-your-development-environment"></a>設定開發環境

開始之前，請先確認您已完成[必要條件](#prerequisites)中的第三個項目符號。 當資源設定指令碼完成之後，請按一下大括弧來公開資料夾結構。 您會看到在 ~/clouddrive/lva-sample 目錄下建立的一些檔案。

![應用程式設定](./media/quickstarts/clouddrive.png)

本教學課程中的相關內容為：

     * ~/clouddrive/lva-sample/edge-deployment/.env  - contains properties that Visual Studio Code uses to deploy modules to an edge device
     * ~/clouddrive/lva-sample/appsettings.json - used by Visual Studio Code for running the sample code

您將需要這些檔案以進行下列步驟。

1. 從這裡複製存放庫： https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp 。
1. 啟動 Visual Studio Code 並開啟您下載存放庫的資料夾。
1. 在 Visual Studio Code 中，瀏覽至「src/cloud-to-device-console-app」資料夾，並建立名為「appsettings.json」的檔案。 此檔案將包含執行程式所需的設定。
1. 複製 ~/clouddrive/lva-sample/appsettings.json 檔案中的內容。 文字應會顯示如下：
    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
    IoT 中樞連接字串可讓您使用 Visual Studio Code，透過 Azure IoT 中樞將命令傳送至 Edge 模組。
    
1. 接下來，流覽至「src/edge」資料夾，並建立名為「.env」的檔案。
1. 複製 ~/clouddrive/lva-sample/.env 檔案中的內容。 文字應會顯示如下：

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>檢查範例檔案

在 Visual Studio Code 中，開啟「src/edge/deployment.template.json」。 此範本會定義您要部署至邊緣裝置 (Azure Linux VM) 的 Edge 模組。 請注意，[模組] 區段底下有兩個項目，具有下列名稱：

* lvaEdge – 這是 IoT Edge 模組上的 Live Video Analytics
* rtspsim –這是 RTSP 模擬器

接下來，瀏覽至「src/cloud-to-device-console-app」資料夾。 您在這裡會看到您建立的 appsettings.json 檔案，以及一些其他檔案：

* c2d-console-app.csproj - Visual Studio Code 的專案檔。
* operations.json - 此檔案會列出您要執行的不同作業
* Program.cs - 執行下列動作的範例程式碼：
    * 載入應用程式設定
    * 叫用 IoT Edge 模組上 Live Video Analytics 所公開的直接方法。 您可以使用模組來分析即時影片串流，方法是叫用其[直接方法](direct-methods.md)
    * 暫停可讓您在 [終端機] 視窗中檢查程式的輸出，以及在 [輸出] 視窗中檢查模組所產生的事件
    * 叫用直接方法來清除資源

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>產生及部署 IoT Edge 部署資訊清單 

部署資訊清單會定義要將哪些模組部署至邊緣裝置，以及這些模組的組態設定。 請遵循下列步驟，從範本檔案產生這類資訊清單，然後將其部署到邊緣裝置。

1. 啟動 Visual Studio Code
1. 按一下左下角 [AZURE IOT 中樞] 窗格旁的 [其他動作] 圖示，以設定 IoT 中樞連接字串。 您可以從 src/cloud-to-device-console-app/appsettings.json 檔案複製字串。 

    ![設定 IOT 連接字串](./media/quickstarts/set-iotconnection-string.png)
1. 接下來，以滑鼠右鍵按一下「src/edge/deployment.template.json」檔案，然後按一下 [產生 IoT Edge 部署資訊清單]。 Visual Studio Code 會使用 .env 檔案中的值，來取代部署範本檔案中找到的變數。 這應該會在 src/edge/config 資料夾中建立名為「deployment.amd64.json」的資訊清單檔。

   ![產生 IoT Edge 部署資訊清單](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. 以滑鼠右鍵按一下「src/edge/config/deployment.amd64.json」，然後按一下 [建立單一裝置的部署]。

   ![建立單一裝置的部署](./media/quickstarts/create-deployment-single-device.png)
1. 接著，系統會要求您「選取 IoT 中樞裝置」。 從下拉式選單中選取 [lva-sample-device]。
1. 在大約 30 秒內，重新整理左下方區段的 Azure IoT 中樞，您應該會看到邊緣裝置已部署下列模組：
    * IoT Edge 上的 Live Video Analytics (名為「lvaEdge」的模組)
    * RTSP 模擬器 (模組名稱「rtspsim」)
 
    ![IoT 中樞](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>準備監視模組 

當您在 IoT Edge 模組上使用 Live Video Analytics 來錄製即時影片串流時，其會將事件傳送至 IoT 中樞。 若要查看這些事件，請遵循下列步驟：

1. 在 Visual Studio Code 中開啟 [Explorer] 窗格，然後在左下角尋找 Azure IoT 中樞。
1. 展開 [裝置] 節點。
1. 以滑鼠右鍵按一下 [lva-sample-device]，然後選擇 [開始監視內建事件監視] 選項。

    ![開始監視內建事件端點](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>執行程式 

1. Visual Studio Code，瀏覽至「src/cloud-to-device-console-app/operations.json」
1. 在 GraphTopologySet 節點底下，編輯下列各項：

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. 接下來，在 GraphInstanceSet 和 GraphTopologyDelete 節點下，確定 topologyName 的值符合上述圖表拓撲中的「name」屬性值：

    `"topologyName" : "CVRToAMSAsset"`  
1. 在瀏覽器中開啟[拓撲](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json)，並查看 assetNamePattern。 若要確定您有一個具有唯一名稱的資產，您可能需要變更 operations.json 檔案中的圖表執行個體名稱 (從預設值「Sample-Graph-1」)。

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. 啟動偵錯工作階段 (按 F5)。 您會開始看到列印在 [終端機] 視窗中某些訊息。
1. 在呼叫 GraphTopologyList 和 GraphInstanceList 時，會開始使用 operations.json。 如果您已在先前的快速入門或教學課程之後清除資源，這會傳回空白清單，然後暫停以讓您按下 Enter 鍵，如下所示：

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
1. 當您在 [終端機] 視窗中按下 Enter 鍵時，將會進行下一組直接方法呼叫
     * 使用上述 topologyUrl 呼叫 GraphTopologySet。
     * 使用下列主體呼叫 GraphInstanceSet。
     
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
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
     * 呼叫 GraphInstanceActivate 以啟動圖表執行個體，並啟動影片流程
     * 第二次呼叫 GraphInstanceList，以顯示圖表執行個體確實處於執行中狀態  
1. [終端機] 視窗中的輸出現在會在「按下 Enter 以繼續」提示時暫停。 此時不要按 Enter。 您可以向上捲動查看您所叫用直接方法的 JSON 回應承載
1. 如果您現在在 Visual Studio Code 中切換到 [輸出] 視窗，將會看到 IoT Edge 模組上的 Live Video Analytics 傳送至 IoT 中樞的訊息。

     * 下列各節將討論這些訊息
1. Graph 執行個體會繼續執行，並錄製影片 – RTSP 模擬器會持續迴圈來源影片。 若要停止錄製，請回到 [終端機] 視窗，然後按 Enter。 會進行下一系列的呼叫以清除資源：

     * 呼叫 GraphInstanceDeactivate 以停用 Graph 執行個體
     * 呼叫 GraphInstanceDelete 以刪除執行個體
     * 呼叫 GraphTopologyDelete 以刪除拓撲
     * 最後一次呼叫 GraphTopologyList 以顯示清單現在是空的

## <a name="interpret-the-results"></a>解譯解譯 

當您執行媒體圖表時，IoT Edge 模組上的 Live Video Analytics 會將特定的診斷和操作事件傳送至 IoT Edge 中樞。 這些事件是您在 Visual Studio Code 的 [輸出] 視窗中看到的訊息，其中包含「body」區段和「applicationProperties」區段。 若要了解這些章節所代表的內容，請閱讀[此](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)文章。

在下列訊息中，Live Video Analytics 模組會定義應用程式屬性和主體的內容。

## <a name="diagnostic-events"></a>診斷事件 

### <a name="mediasession-established-event"></a>MediaSession 建立的事件

啟動 Graph 執行個體時，RTSP 來源節點會嘗試連線到在 rtspsim 模組中執行的 RTSP 伺服器。 如果成功，則會列印此事件：

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* 此訊息為診斷事件 MediaSessionEstablished，表示 RTSP 來源節點 (主體) 能夠與 RTSP 模擬器建立連線，並開始接收 (模擬) 即時摘要。
* ApplicationProperties 中的「主體」會參考產生訊息之圖表拓撲中的節點。 在此情況下，訊息是來自 RTSP 來源節點。
* applicationProperties 中的「eventType」表示這是診斷事件。
* 「eventTime」表示事件發生的時間。
* 「主體」包含診斷事件的相關資料，在此案例中為 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 詳細資料。

## <a name="operational-events"></a>運作事件 

### <a name="recordingstarted-event"></a>RecordingStarted 事件

當資產接收節點開始錄製影片時，其會發出類型為 Microsoft.Media.Graph.Operational.RecordingStarted 的這個事件

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

ApplicationProperties 中的「主體」會參考圖表中的「資產接收」節點，這會產生此訊息。

本文包含輸出位置的相關資訊，在此案例中，是用來錄製影片的 Azure 媒體服務資產名稱。 您應該記下此值。

### <a name="recordingavailable-event"></a>RecordingAvailable 事件

如其名所示，RecordingStarted 事件會在錄製開始時傳送，但影片資料可能尚未上傳到資產。 當資產接收節點將影片資料上傳到資產時，其會發出類型為 Microsoft.Media.Graph.Operational.RecordingAvailable 的這個事件

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

此事件表示已將足夠的資料寫入資產，讓播放器/用戶端可起始播放影片。

ApplicationProperties 中的「主體」會參考圖表中的 AssetSink 節點，從而產生此訊息。

本文包含輸出位置的相關資訊，在此案例中，是用來錄製影片的 Azure 媒體服務資產名稱。

### <a name="recordingstopped-event"></a>RecordingStopped 事件

當您停用 Graph 執行個體時，資產接收節點會停止將影片錄製到資產，其會發出類型為 Microsoft.Media.Graph.Operational.RecordingStopped 的這個事件。

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

這個事件表示錄製已停止。

ApplicationProperties 中的「主體」會參考圖表中的 AssetSink 節點，從而產生此訊息。

本文包含輸出位置的相關資訊，在此案例中，是用來錄製影片的 Azure 媒體服務資產名稱。

## <a name="media-services-asset"></a>媒體服務資產  

您可以藉由登入 Azure 入口網站並檢視影片，檢查媒體圖表所建立的媒體服務資產。

1. 開啟網頁瀏覽器，然後移至 [Azure 入口網站](https://portal.azure.com/)。 輸入您的認證來登入此入口網站。 預設檢視是您的服務儀表板。
1. 在您訂用帳戶的資源之間找出您的媒體服務帳戶，然後開啟 [帳戶] 刀鋒視窗
1. 按一下 [媒體服務] 清單中的 [資產]

    ![Assets](./media/continuous-video-recording-tutorial/assets.png)
1. 您會發現以 sampleAsset-CVRToAMSAsset-Sample-Graph-1 名稱列出的資產，這是您在圖表拓撲檔案中選擇的命名模式。
1. 按一下 [資產]。
1. 在 [資產詳細資料] 頁面中，按一下 [串流 URL] 文字方塊下方的 [新建]。

    ![新增資產](./media/continuous-video-recording-tutorial/new-asset.png)

1. 在開啟的精靈中，接受預設選項，然後按 [新增]。 如需詳細資訊，請參閱[影片播放](video-playback-concept.md)。

    > [!TIP]
    > 請確定您的[串流端點正在執行](../latest/streaming-endpoint-concept.md)。
1. 播放器應該會載入影片，且您應該能夠按 [播放]>** 來進行檢視。

> [!NOTE]
> 因為影片的來源是用來模擬攝影機摘要的容器，所以影片中的時間戳記會與您啟動 Graph 執行個體、以及將其停用的時間相關。 您可以看到[此](playback-multi-day-recordings-tutorial.md)教學課程，以了解如何瀏覽多日的錄製，並檢視該封存的部分。 在該教學課程中，您也可以看到螢幕上所顯示影片中的時間戳記。

## <a name="clean-up-resources"></a>清除資源

如果您想要嘗試其他教學課程，應該保留所建立的資源。 否則，請移至 [Azure 入口網站]、瀏覽至您的資源群組、選取您執行本教學課程所用的資源群組，並刪除資源群組。

## <a name="next-steps"></a>後續步驟

* 使用具有 RTSP 支援的 [IP 攝影機](https://en.wikipedia.org/wiki/IP_camera)，而非使用 RTSP 模擬器。 您可以藉由尋找符合設定檔 G、S 或 T 的裝置，在[符合 ONVIF 標準](https://www.onvif.org/conformant-products/)產品頁面上搜尋具有 RTSP 支援的 IP 攝影機。
* 使用 AMD64 或 X64 Linux 裝置 (相較於使用 Azure Linux VM)。 此裝置必須與 IP 攝影機位於相同的網路中。 您可以遵循[在 Linux 上安裝 Azure IoT Edge 執行階段](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)中的指示，然後遵循[將您的第一個 IoT Edge 模組部署至虛擬 Linux 裝置](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)快速入門中的指示，向 Azure IoT 中樞註冊裝置。
