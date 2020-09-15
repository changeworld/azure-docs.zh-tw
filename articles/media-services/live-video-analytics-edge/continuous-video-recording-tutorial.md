---
title: 將連續影片錄製到雲端並從雲端播放的教學課程 - Azure
description: 在本教學課程中，您將了解如何在Azure IoT Edge 上使用 Azure Live Video Analytics，以持續將影片錄製到雲端，並使用 Azure 媒體服務串流該影片的任何部分。
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: c94f87068d003fc260d861cb99c60326d4a53258
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566786"
---
# <a name="tutorial-continuous-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>教學課程：將連續影片錄製移到雲端並從雲端播放

在本教學課程中，您將了解如何在 Azure IoT Edge 上使用 Azure Live Video Analytics，執行以雲端為目的地的[連續影片錄製](continuous-video-recording-concept.md) (CVR)，並使用 Azure 媒體服務串流該影片的任何部分。 此功能適用於安全性和合規性等案例，其中需要維護攝影機多日或數週的畫面封存。 

在本教學課程中，您將：

> [!div class="checklist"]
> * 設定相關資源。
> * 檢查執行 CVR 的程式碼。
> * 執行範例程式碼。
> * 檢查結果並檢視影片。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>建議的閱讀準備事項  

開始之前，請先閱讀下列文章：

* [IoT Edge 上的 Live Video Analytics 概觀](overview.md)
* [IoT Edge 上的 Live Video Analytics 術語](terminology.md)
* [媒體圖表概念](media-graph-concept.md) 
* [連續影片錄製案例](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>必要條件

本教學課程的必要條件：

* 開發電腦上必須具有搭配 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 和 [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 擴充功能的 [Visual Studio Code](https://code.visualstudio.com/)。

    > [!TIP]
    > 系統可能會提示您安裝 Docker。 請忽略此提示。
* 開發電腦上的 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer)。
* 完成 [Live Video Analytics 資源設定指令碼](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)。

在這些步驟結束時，您會在 Azure 訂用帳戶中部署相關的 Azure 資源：

* Azure IoT 中樞
* Azure 儲存體帳戶
* Azure 媒體服務帳戶
* Azure 中的 Linux VM，已安裝 [IoT Edge 執行階段](../../iot-edge/how-to-install-iot-edge-linux.md)

## <a name="concepts"></a>概念

如[媒體圖表概念](media-graph-concept.md)文章中所述，媒體圖表可讓您定義：

- 應該從中擷取媒體的位置。
- 處理媒體的方式。
- 應該傳遞結果的位置。 
 
 若要完成 CVR，您必須從具備 RTSP 功能的攝影機中捕捉影片，並持續將其錄製到 [Azure 媒體服務資產](terminology.md#asset)。 下圖以圖表表示該媒體圖表。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg" alt-text="媒體圖表":::

在本教學課程中，您將使用以 [Live555 媒體伺服器](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)建置的一個邊緣模組來模擬 RTSP 攝影機。 在媒體圖表內，您將使用 [RTSP 來源](media-graph-concept.md#rtsp-source)節點來取得即時摘要，並將該影片傳送至[資產接收節點](media-graph-concept.md#asset-sink)，其會將影片錄製到資產中。

## <a name="set-up-your-development-environment"></a>設定開發環境

在開始之前，請先確認您已完成 [必要條件](#prerequisites)中的第三個項目。 在資源設定指令碼完成後，選取大括弧以展開資料夾結構。 您會看到建立在 ~/clouddrive/lva-sample 目錄下的一些檔案。

![應用程式設定](./media/quickstarts/clouddrive.png)

本教學課程中的相關檔案如下：

* **~/clouddrive/lva-sample/edge-deployment/.env**：包含 Visual Studio Code 用來將模組部署至邊緣裝置的屬性。
* **~/clouddrive/lva-sample/appsettings.json**：由 Visual Studio Code 用來執行範例程式碼。

您將需要這些檔案來進行以下步驟。

1. 從 GitHub 連結 https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp 複製存放庫。
1. 啟動 Visual Studio Code 並開啟您用來下載存放庫的資料夾。
1. 在 Visual Studio Code 中，瀏覽至 src/cloud-to-device-console-app 資料夾，並建立名為 **appsettings.json** 的檔案。 此檔案包含執行程式所需的設定。
1. 複製 ~/clouddrive/lva-sample/appsettings.json 檔案中的內容。 文字應會顯示如下：
    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
    IoT 中樞連接字串可讓您使用 Visual Studio Code 透過 Azure IoT 中樞將命令傳送至邊緣模組。
    
1. 接下來，瀏覽至 src/edge 資料夾，並建立名為 **.env** 的檔案。
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

在 Visual Studio Code 中，開啟 src/edge/deployment.template.json。 此範本會定義您要部署至邊緣裝置 (Azure Linux VM) 的邊緣模組。 [模組] 區段底下有兩個項目，名稱如下：

* **lvaEdge**：這是 IoT Edge 模組上的 Live Video Analytics。
* **rtspsim**：這是 RTSP 模擬器。

接下來，瀏覽至 src/cloud-to-device-console-app 資料夾。 您在這裡會看到您所建立的 appsettings.json 檔案，以及一些其他檔案：

* **c2d-console-app.csproj**：Visual Studio Code 的專案檔。
* **operations.json**：此檔案會列出您要執行的不同作業。
* **Program.cs**：範例程式碼，其功用為：
    * 載入應用程式設定。
    * 叫用 IoT Edge 模組上 Live Video Analytics 所公開的直接方法。 您可以使用模組，藉由叫用其[直接方法](direct-methods.md)來分析即時影片串流。
    * 暫停以讓您在 [終端機] 視窗中查看程式的輸出，並在 [輸出] 視窗中查看模組所產生的事件。
    * 叫用直接方法來清除資源。

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>產生和部署 IoT Edge 部署資訊清單 

部署資訊清單會定義要將部署至邊緣裝置的模組，以及這些模組的組態設定。 請遵循下列步驟，從範本檔案產生資訊清單，然後將其部署到邊緣裝置。

1. 啟動 Visual Studio Code。
1. 選取左下角 [AZURE IOT 中樞] 窗格旁的 [更多動作] 圖示，以設定 IoT 中樞連接字串。 從 src/cloud-to-device-console-app/appsettings.json 檔案複製字串。 

    ![設定 IoT 中樞連接字串](./media/quickstarts/set-iotconnection-string.png)
1. 以滑鼠右鍵按一下 src/edge/deployment.template.json 檔案，然後選取 [產生 IoT Edge 部署資訊清單]。 Visual Studio Code 會使用 .env 檔案中的值，來取代部署範本檔案中找到的變數。 此動作應該會在 src/edge/config 資料夾中建立名為 **deployment.amd64.json** 的資訊清單檔。

   ![產生 IoT Edge 部署資訊清單](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. 以滑鼠右鍵按一下 src/edge/config/deployment.amd64.json，然後選取 [建立單一裝置的部署]。

   ![建立單一裝置的部署](./media/quickstarts/create-deployment-single-device.png)
1. 接著，系統會要求您**選取 IoT 中樞裝置**。 從下拉式清單中選取 [lva-sample-device]。
1. 在大約 30 秒後，重新整理左下方區段中的 Azure IoT 中樞。 您應該會看到邊緣裝置已部署下列模組：
    * IoT Edge 上的 Live Video Analytics (模組名稱為 **lvaEdge**)
    * RTSP 模擬器 (模組名稱為 **rtspsim**)
 
    ![IoT 中樞](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>準備監視模組 

當您在 IoT Edge 模組上使用 Live Video Analytics 來錄製即時影片串流時，其會將事件傳送至 IoT 中樞。 若要查看這些事件，請遵循下列步驟：

1. 在 Visual Studio Code 中開啟 [檔案總管] 窗格，然後在左下角尋找 **Azure IoT 中樞**。
1. 展開 [裝置] 節點。
1. 以滑鼠右鍵按一下 lva-sample-device 檔案，然後選取 [開始監視內建事件端點]。

    ![開始監視內建事件端點](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>執行程式 

1. 在 Visual Studio Code 中，移至 src/cloud-to-device-console-app/operations.json。
1. 在 **GraphTopologySet** 節點底下，編輯下列內容：

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. 接下來，在 **GraphInstanceSet** 和 **GraphTopologyDelete** 節點下，確定 **topologyName** 的值符合上述圖表拓撲中的 **name** 屬性值：

    `"topologyName" : "CVRToAMSAsset"`  
1. 在瀏覽器中開啟[拓撲](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json)，並查看 assetNamePattern。 若要確定您有一個具有唯一名稱的資產，您可能需要變更 operations.json 檔案中的圖表執行個體名稱 (從預設值 Sample-Graph-1 變更)。

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. 選取 F5 來啟動偵錯工作階段。 您會在 [終端機] 視窗中看到一些列印的訊息。
1. operations.json 檔案首先會呼叫 GraphTopologyList 和 GraphInstanceList。 如果您已在先前的快速入門或教學課程之後清除資源，此動作會傳回空白清單並暫停以讓您選取 **Enter**，如下所示：

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

1. 當您在 [終端機] 視窗中選取 **Enter** 後，將會進行下一組直接方法的呼叫：
   * 使用先前的 topologyUrl 呼叫 GraphTopologySet
   * 使用下列主體呼叫 GraphInstanceSet
     
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
   * 再次呼叫 GraphInstanceList，以顯示圖表執行個體處於執行中狀態 
1. [終端機] 視窗中的輸出現在會在出現「按 Enter 繼續」提示時暫停。 此時請不要選取 **Enter**。 請向上捲動，查看您所叫用直接方法的 JSON 回應承載。
1. 如果您現在於 Visual Studio Code 中切換到 [輸出] 視窗，將會看到 IoT Edge 模組上 Live Video Analytics 傳送至 IoT 中樞的訊息。

   這些訊息將在下一節中討論。
1. 圖形執行個體會繼續執行並錄製影片。 RTSP 模擬器會持續循環播放來源影片。 若要停止錄製，請回到 [終端機] 視窗，然後選取 **Enter**。 接下來會以下列方式執行一系列的呼叫以清除資源：

   * 呼叫 GraphInstanceDeactivate 以停用圖表執行個體。
   * 呼叫 GraphInstanceDelete 以刪除執行個體。
   * 呼叫 GraphTopologyDelete 以刪除拓撲。
   * 最後呼叫 GraphTopologyList 以顯示清單現在是空的。

## <a name="interpret-the-results"></a>解讀結果 

當您執行媒體圖時，IoT Edge 模組上的 Live Video Analytics 會將特定的診斷和操作事件傳送至 IoT Edge 中樞。 這些事件就是您在 Visual Studio Code 的 [輸出] 視窗中看到的訊息。 訊息包含 body 區段和 applicationProperties 區段。 若要瞭解這些區段所表示的內容，請參閱[建立和讀取 IoT 中樞訊息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下列訊息中，Live Video Analytics 模組會定義應用程式屬性和本文內容。

## <a name="diagnostics-events"></a>診斷事件 

### <a name="mediasession-established-event"></a>MediaSessionEstablished 事件

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

* 此訊息為診斷事件 (MediaSessionEstablished)。 這表示 RTSP 來源節點 (主體) 已建立與 RTSP 模擬器的連線，並開始接收 (模擬) 即時摘要。
* ApplicationProperties 中的 subject 區段會參考產生訊息的圖形拓撲中的節點。 在此案例中，訊息來自 RTSP 來源節點。
* applicationProperties 中的 eventType 表示這是診斷事件。
* eventTime 區段表示事件發生的時間。
* body 區段會包含有關診斷事件的資料，在此案例中為 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 詳細資料。

## <a name="operational-events"></a>運作事件 

### <a name="recordingstarted-event"></a>RecordingStarted 事件

當資產接收節點開始錄製影片時，其會發出類型為 Microsoft.Media.Graph.Operational.RecordingStarted 的這個事件：

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

applicationProperties 中的 subject 區段會參考圖表中產生此訊息的資產接收節點。

body 區段包含輸出位置的相關資訊。 在此案例中，指的是將影片錄製至其中的 Azure 媒體服務資產名稱。 記下此值。

### <a name="recordingavailable-event"></a>RecordingAvailable 事件

如其名所示，RecordingStarted 事件會在錄製開始時傳送，但影片資料可能尚未上傳到資產。 當資產接收節點將影片資料上傳到資產時，其會發出類型為 Microsoft.Media.Graph.Operational.RecordingAvailable 的這個事件：

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

此事件表示已將足夠的資料寫入資產，可讓播放器或用戶端開始播放影片。

ApplicationProperties 中的 subject 區段會參考圖表中產生此訊息的 AssetSink 節點。

body 區段包含輸出位置的相關資訊。 在此案例中，指的是將影片錄製至其中的 Azure 媒體服務資產名稱。

### <a name="recordingstopped-event"></a>RecordingStopped 事件

當您停用圖表執行個體時，資產接收節點會停止將影片錄製到資產。 其會發出 Microsoft.Media.Graph.Operational.RecordingStopped 類型的這個事件：

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

ApplicationProperties 中的 subject 區段會參考圖表中產生此訊息的 AssetSink 節點。

body 區段包含有關輸出位置的資訊，在此案例中，是用來錄製影片的 Azure 媒體服務資產的名稱。

## <a name="media-services-asset"></a>媒體服務資產  

您可以藉由登入 Azure 入口網站並檢視影片，檢查媒體圖表所建立的媒體服務資產。

1. 開啟網頁瀏覽器，然後移至 [Azure 入口網站](https://portal.azure.com/)。 輸入您的認證來登入此入口網站。 預設檢視是您的服務儀表板。
1. 在訂用帳戶的資源中找出您的媒體服務帳戶，然後開啟 [帳戶] 窗格。
1. 選取 [媒體服務] 清單中的**資產**。

    ![媒體服務資產](./media/continuous-video-recording-tutorial/assets.png)
1. 您會發現以 sampleAsset-CVRToAMSAsset-Sample-Graph-1 作為名稱列出的資產。 這是在您的圖表拓撲檔案中選擇的命名模式。
1. 選取資產。
1. 在資產詳細資料頁面上，選取 [串流 URL] 文字方塊底下的 [新建]。

    ![新增資產](./media/continuous-video-recording-tutorial/new-asset.png)

1. 在開啟的精靈中，接受預設選項，然後選取 [新增]。 如需詳細資訊，請參閱[影片播放](video-playback-concept.md)。

    > [!TIP]
    > 請確定您的[串流端點正在執行](../latest/streaming-endpoint-concept.md)。
1. 播放器應該會載入影片。 選取 [播放] 即可觀看。

> [!NOTE]
> 因為影片的來源是模擬攝影機摘要的容器，所以影片中的時間戳記會與您啟動圖表執行個體以及停用執行個體的時間相關。 若要了解如何瀏覽多日的錄影並查看該封存的各部分，請參閱[播放多日錄影](playback-multi-day-recordings-tutorial.md)教學課程。 在該教學課程中，您也可以看到螢幕上顯示影片中的時間戳記。

## <a name="clean-up-resources"></a>清除資源

如果您想要嘗試其他教學課程，請保留所建立的資源。 否則，請移至 Azure 入口網站，瀏覽至您的資源群組，選取您執行本教學課程所用的資源群組，並刪除資源群組。

## <a name="next-steps"></a>後續步驟

* 使用具有 RTSP 支援的 [IP 攝影機](https://en.wikipedia.org/wiki/IP_camera)，而非使用 RTSP 模擬器。 您可以藉由在[符合 ONVIF 標準產品頁面](https://www.onvif.org/conformant-products/)上尋找符合設定檔 G、S 或 T 的裝置，以搜尋具有 RTSP 支援的 IP 攝影機。
* 使用 AMD64 或 X64 Linux 裝置 (相較於使用 Azure Linux VM)。 此裝置必須與 IP 攝影機位於相同的網路中。 遵循[在 Linux 上安裝 Azure IoT Edge 執行階段](../../iot-edge/how-to-install-iot-edge-linux.md)中的指示。 然後按照[將您的第一個 IoT Edge 模組部署至虛擬 Linux 裝置](../../iot-edge/quickstart-linux.md)快速入門中的指示操作，向 Azure IoT 中樞註冊裝置。
