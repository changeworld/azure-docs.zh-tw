---
title: 將以事件為基礎的影片錄製到雲端並從雲端播放的教學課程 - Azure
description: 在本教學課程中，您將了解如何在 Azure IoT Edge 上使用 Azure Live Video Analytics，將以事件為基礎的影片錄製到雲端並從雲端播放。
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 84f6ef813fb1b2cc425e096212010717d0561aef
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498297"
---
# <a name="tutorial-event-based-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>教學課程：將以事件為基礎的影片錄製到雲端並從雲端播放

在本教學課程中，您將了解如何使用 Azure IoT Edge 上的 Azure Live Video Analytics，選擇性地將即時影片來源的部分錄製到雲端中的 Azure 媒體服務。 此使用案例在本教學課程中稱為[以事件為基礎的影片錄製](event-based-video-recording-concept.md) (EVR)。 為了錄製即時影片的某些部分，您將使用物件偵測 AI 模型在影片中尋找物件，並且只在偵測到特定類型的物件時錄製影片剪輯。 您也將了解如何使用媒體服務播放錄製的影片剪輯。 這項功能適用於需要保留所需影片剪輯的各種案例。 

在本教學課程中，您將：

> [!div class="checklist"]
> * 設定相關資源。
> * 檢查執行 EVR 的程式碼。
> * 執行範例程式碼。
> * 檢查結果並檢視影片。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>建議的閱讀準備事項  

開始之前，請先閱讀下列文章：

* [IoT Edge 上的 Live Video Analytics 概觀](overview.md)
* [IoT Edge 上的 Live Video Analytics 術語](terminology.md)
* [媒體圖表概念](media-graph-concept.md) 
* [發生事件時錄製影片](event-based-video-recording-concept.md)
* [教學課程：開發 IoT Edge 模組](../../iot-edge/tutorial-develop-for-linux.md)
* [如何編輯 deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* 有關[如何在 IoT Edge 部署資訊清單中宣告路由](../../iot-edge/module-composition.md#declare-routes)的章節

## <a name="prerequisites"></a>必要條件

本教學課程的必要條件：

* 開發電腦上必須具有搭配 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 和 [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 擴充功能的 [Visual Studio Code](https://code.visualstudio.com/)。

    > [!TIP]
    > 系統可能會提示您安裝 Docker。 請忽略此提示。
* 開發電腦上的 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer)。
* 完成 [Live Video Analytics 資源設定指令碼](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)並[設定環境](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

在這些步驟結束時，您會在 Azure 訂用帳戶中部署相關的 Azure 資源：

* Azure IoT 中樞
* Azure 儲存體帳戶
* Azure 媒體服務帳戶
* Azure 中的 Linux VM，已安裝 [IoT Edge 執行階段](../../iot-edge/how-to-install-iot-edge.md)

## <a name="concepts"></a>概念

以事件為基礎的影片錄製是指事件觸發的影片的錄製流程。 產生該事件的來源可以是：
- 影片訊號本身的處理，例如偵測到影片中的移動物件時。
- 獨立來源，例如開門的動作。 

或者，只有在推斷服務偵測到特定事件發生時才會觸發錄製。 在本教學課程中，您將使用在高速公路上移動車輛的影片，並在每次偵測到卡車時錄製影片剪輯。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/overview.svg" alt-text="媒體圖表":::

此圖是以圖片呈現的[媒體圖](media-graph-concept.md)，以及完成所需案例的其他模組。 其中涉及到四個 IoT Edge 模組：

* IoT Edge 模組上的 Live Video Analytics。
* 在 HTTP 端點後方執行 AI 模型的邊緣模組。 此 AI 模組會使用 [YOLO v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) 模型，其可以偵測許多類型的物件。
* 用來計算和篩選物件的自訂模組，在圖表中稱為物件計數器。 您將建立物件計數器，並在本教學課程中加以部署。
* 模擬 RTSP 攝影機的 [RTSP 模擬器模組](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)。
    
如圖所示，您將使用媒體圖中的 [RTSP 來源](media-graph-concept.md#rtsp-source) 節點來擷取模擬的即時影片 (在高速公路上的車流)，並將該影片傳送至兩條路徑：

* 第一條路徑的目的地是[畫面播放速率篩選處理器](media-graph-concept.md#frame-rate-filter-processor)節點，以指定的 (降低的) 畫面播放速率輸出影片畫面。 這些影片畫面會傳送至 HTTP 擴充節點。 節點接著會將畫面 (以影像形式) 轉送至 AI 模組 YOLO v3，也就是物件偵測器。 節點會接收結果，而這些結果就是模型偵測到的物件 (流量中的車輛)。 然後，HTTP 擴充節點會透過 IoT 中樞的訊息接收節點，將結果發佈至 IoT Edge 中樞。
* 物件計數器模組已設定為接收來自 IoT Edge 中樞的訊息，其中包括物件偵測結果 (流量中的車輛)。 此模組會檢查這些訊息，並尋找特定類型的物件 (其已透過設定進行設定)。 找到這類物件時，此模組會將訊息傳送至 IoT Edge 中樞。 接著，這些「找到物件」的訊息會路由傳送至媒體圖的 IoT 中樞來源節點。 收到這類訊息時，媒體圖中的 IoT 中樞來源節點會觸發[訊號閘道處理器](media-graph-concept.md#signal-gate-processor)節點。 訊號閘道處理器節點接著在一段設定的時間內保持開啟。 在該持續時間內，影片會透過閘道進入資產接收節點。 接著，該部分的即時串流會透過[資產接收](media-graph-concept.md#asset-sink)節點錄製到 Azure 媒體服務帳戶中的[資產](terminology.md#asset)。

## <a name="set-up-your-development-environment"></a>設定開發環境

在開始之前，請先確認您已完成 [必要條件](#prerequisites)中的第三個項目。 在資源設定指令碼完成後，選取大括弧以展開資料夾結構。 您會看到建立在 ~/clouddrive/lva-sample 目錄下的一些檔案。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/clouddrive.png" alt-text="應用程式設定":::

本教學課程中的相關檔案如下：

* **~/clouddrive/lva-sample/edge-deployment/.env**：包含 Visual Studio Code 用來將模組部署至邊緣裝置的屬性。
* **~/clouddrive/lva-sample/appsetting.json**：由 Visual Studio Code 用來執行範例程式碼。

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
1. 複製 ~/clouddrive/lva-sample/edge-deployment/.env 檔案中的內容。 文字應會顯示如下：

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

## <a name="examine-the-template-file"></a>檢查範本檔案 

在上一個步驟中，您已啟動 Visual Studio Code 並開啟包含範例程式碼的資料夾。

在 Visual Studio Code 中，瀏覽至 src/edge。 您會看到您所建立的 .env 檔案，以及一些部署範本檔案。 此範本會定義您要部署至邊緣裝置 (Azure Linux VM) 的邊緣模組。 .env 檔案包含這些範本中使用的變數值，例如媒體服務憑證。

開啟 src/edge/deployment.objectCounter.template.json。 [模組] 區段底下有四個項目，與上方 [概念] 區段中所列的項目對應：

* **lvaEdge**：這是 IoT Edge 模組上的 Live Video Analytics。
* **yolov3**：這是使用 YOLO v3 模型所建立的 AI 模組。
* **rtspsim**：這是 RTSP 模擬器。
* **objectCounter**：這是在 yolov3 結果中尋找特定物件的模組。

針對 objectCounter 模組，請參閱用於「映像」值的字串 (${MODULES.objectCounter})。 這會以開發 IoT Edge 模組的[教學課程](../../iot-edge/tutorial-develop-for-linux.md)為基礎。 Visual Studio Code 會自動辨識 objectCounter 模組的程式碼位於 src/edge/modules/objectCounter 之下。 

請閱讀[本節](../../iot-edge/module-composition.md#declare-routes)，以了解如何在 IoT Edge 部署資訊清單中宣告路由。 然後檢查 JSON 檔案範本中的路由。 請注意：

* LVAToObjectCounter 是用來將特定事件傳送至 objectCounter 模組中的特定端點。
* ObjectCounterToLVA 是用來將觸發程式事件傳送至 lvaEdge 模組中的特定端點 (應該是 IoT 中樞的來源節點)。
* objectCounterToIoTHub 是用來做為調試工具，可協助您在執行本教學課程時查看 objectCounter 的輸出。

> [!NOTE]
> 檢查 objectCounter 模組所需的屬性，其設定為尋找標記為「卡車」的物件，且信賴等級至少為50%。

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>產生和部署 IoT Edge 部署資訊清單 

部署資訊清單會定義要將部署至邊緣裝置的模組，以及這些模組的組態設定。 請遵循下列步驟，從範本檔案產生資訊清單，然後將其部署到邊緣裝置。

使用 Visual Studio Code，依照[這些指示](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) 來登入 Docker。 然後選取 [組建和推送 IoT Edge 解決方案]。 在此步驟中使用 src/edge/deployment.objectCounter.template.json。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/build-push.png" alt-text="組建和推送 IoT Edge 解決方案":::

此動作會組建用於物件計數的 objectCounter 模組，並將映像推送至您的 Azure Container Registry。

* 檢查您是否已在 .env 檔案中定義 CONTAINER_REGISTRY_USERNAME_myacr 和 CONTAINER_REGISTRY_PASSWORD_myacr 環境變數。

此步驟會在 src/edge/config/deployment.objectCounter.amd64.json 中建立 IoT Edge 部署資訊清單。 在該檔案上按一下滑鼠右鍵，然後選取 [建立單一裝置的部署]。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/create-deployment-single-device.png" alt-text="建立單一裝置的部署":::

如果這是您在 IoT Edge 上使用 Live Video Analytics 的第一個教學課程，Visual Studio Code 會提示您輸入 IoTHub 連接字串。 您可以從 appsettings.json 檔案複製字串。

接下來，Visual Studio Code 會要求您選取 IoT 中樞裝置。 選取您的 IoT Edge 裝置 (應該是 lva-sample-device)。

在這個階段，已啟動將邊緣模組部署到您的 IoT Edge 裝置。
在大約 30 秒後，重新整理 Visual Studio Code 左下方區段中的 Azure IoT 中樞。 您應該會看到已部署的四個模組，分別名為 lvaEdge、rtspsim、yolov3 和 objectCounter。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/iot-hub.png" alt-text="已部署的四個模組":::

## <a name="prepare-for-monitoring-events"></a>準備監視事件

若要從 objectCounter 模組和 IoT Edge 模組上的 Live Video Analytics 中查看事件，請按照下列步驟操作：

1. 在 Visual Studio Code 中開啟 [檔案總管] 窗格，然後在左下角尋找 **Azure IoT 中樞**。
1. 展開 [裝置] 節點。
1. 以滑鼠右鍵按一下 lva-sample-device 檔案，然後選取 [開始監視內建事件端點]。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/start-monitoring-iothub-events.png" alt-text="開始監視內建事件端點":::
    
## <a name="run-the-program"></a>執行程式

1. 在 Visual Studio Code 中，開啟 [延伸模組] 索引標籤 (或按 Ctrl+Shift+X) 並搜尋 Azure IoT 中樞。
1. 按一下滑鼠右鍵，然後選取 [延伸模組設定]。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="延伸模組設定":::
1. 搜尋並啟用「顯示詳細資訊訊息」。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="顯示詳細資訊訊息":::
1. <!--In Visual Studio Code, go-->移至 src/cloud-to-device-console-app/operations.json。
1. 在 **GraphTopologySet** 節點底下，編輯下列內容：

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. 接下來，在 **GraphInstanceSet** 和 **GraphTopologyDelete** 節點底下，編輯下列內容：

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
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
            "topologyName": "EVRtoAssetsOnObjDetect",
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
1. 圖形執行個體會繼續執行並錄製影片。 RTSP 模擬器會持續循環播放來源影片。 檢閱將在下一節中討論的訊息。 若要停止執行個體，請回到 [終端機] 視窗，然後選取 **Enter**。 接下來會以下列方式執行一系列的呼叫以清除資源：

   * 呼叫 GraphInstanceDeactivate 以停用圖表執行個體。
   * 呼叫 GraphInstanceDelete 以刪除執行個體。
   * 呼叫 GraphTopologyDelete 以刪除拓撲。
   * 最後呼叫 GraphTopologyList 以顯示清單現在是空的。

## <a name="interpret-the-results"></a>解讀結果 

當您執行媒體圖時，IoT Edge 模組上的 Live Video Analytics 會將特定的診斷和操作事件傳送至 IoT Edge 中樞。 這些事件就是您在 Visual Studio Code 的 [輸出] 視窗中看到的訊息。 訊息包含 body 區段和 applicationProperties 區段。 若要瞭解這些區段所表示的內容，請參閱[建立和讀取 IoT 中樞訊息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下列訊息中，Live Video Analytics 模組會定義應用程式屬性和本文內容。

## <a name="diagnostics-events"></a>診斷事件

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件 

當媒體圖表具現化時，RTSP 來源節點會嘗試連接到在 RTSP 模擬器容器上執行的 RTSP 伺服器。 如果成功，則會列印此事件。 事件類型為 Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished。

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* 此訊息為診斷事件 (MediaSessionEstablished)。 這表示 RTSP 來源節點 (主體) 已建立與 RTSP 模擬器的連線，並開始接收 (模擬) 即時摘要。
* ApplicationProperties 中的 subject 區段會參考產生訊息的圖形拓撲中的節點。 在此案例中，訊息來自 RTSP 來源節點。
* applicationProperties 中的 eventType 表示這是診斷事件。
* eventTime 區段表示事件發生的時間。 這是流量影片 (MKV 檔案) 開始以即時串流的形式抵達模組的時間。
* body 區段會包含有關診斷事件的資料，在此案例中為 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 詳細資料。


## <a name="operational-events"></a>運作事件

在媒體圖執行一段時間之後，最後您會從 objectCounter 模組取得事件。 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

applicationProperties 區段包含事件時間。 這是 objectCounter 模組觀察到 yolov3 模組結果包含相關物件 (卡車) 的時間。

當影片中偵測到其他卡車時，您可能會看到更多這些事件。

### <a name="recordingstarted-event"></a>RecordingStarted 事件

緊接在物件計數器傳送事件之後，您會看到類型為 Microsoft.Media.Graph.Operational.RecordingStarted 的事件：

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

applicationProperties 中的 subject 區段會參考圖表中產生此訊息的資產接收節點。 body 區段包含輸出位置的相關資訊。 在此案例中，指的是將影片錄製至其中的 Azure 媒體服務資產名稱。 記下此值。

### <a name="recordingavailable-event"></a>RecordingAvailable 事件

當資產接收節點將影片上傳到資產時，會發出類型為 Microsoft.Media.Graph.Operational.RecordingAvailable 的這個事件：

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

此事件表示已將足夠的資料寫入資產，可讓播放器或用戶端開始播放影片。 ApplicationProperties 中的 subject 區段會參考圖表中產生此訊息的 AssetSink 節點。 body 區段包含輸出位置的相關資訊。 在此案例中，指的是將影片錄製至其中的 Azure 媒體服務資產名稱。

### <a name="recordingstopped-event"></a>RecordingStopped 事件

如果您檢查[拓撲](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)中訊號閘道處理器節點的啟用設定 (maximumActivationTime)，您會看到閘道已設定為在傳送 30 秒的影片之後關閉。 在 RecordingStarted 事件後大約 30 秒，您應該會看到類型為 Microsoft.Media.Graph.Operational.RecordingStopped 的事件。 此事件表示資產接收節點已停止將影片記錄到資產。

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

這個事件表示錄製已停止。 ApplicationProperties 中的 subject 區段會參考圖表中產生此訊息的 AssetSink 節點。 body 區段包含輸出位置的相關資訊。 在此案例中，指的是將影片錄製至其中的 Azure 媒體服務資產名稱。

## <a name="media-services-asset"></a>媒體服務資產  

您可以藉由登入 Azure 入口網站來檢查圖表所建立的媒體服務資產，並觀看影片。

1. 開啟網頁瀏覽器，然後移至 [Azure 入口網站](https://portal.azure.com/)。 輸入您的認證來登入此入口網站。 預設檢視是您的服務儀表板。
1. 在訂用帳戶的資源中找出您的媒體服務帳戶。 開啟帳戶窗格。
1. 選取 [媒體服務] 清單中的 **資產**。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/assets.png" alt-text="連續影片錄製":::
1. 您會發現以 sampleAssetFromEVR-LVAEdge-{DateTime} 名稱列出的資產。 此名稱會在 RecordingStarted 事件的 outputLocation 屬性中提供。 拓撲中的 assetNamePattern 會決定此名稱的產生方式。
1. 選取資產。
1. 在資產詳細資料頁面上，選取 [串流 URL] 文字方塊底下的 [新建]。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/new-asset.png" alt-text="新增資產":::
1. 在開啟的精靈中，接受預設選項，然後選取 [新增]。 如需詳細資訊，請參閱[影片播放](video-playback-concept.md)。

    > [!TIP]
    > 請確定您的[串流端點正在執行](../latest/streaming-endpoint-concept.md)。
1. 播放器應該會載入影片。 選取 [播放] 即可觀看。

> [!NOTE]
> 因為影片的來源是模擬攝影機摘要的容器，所以影片中的時間戳記會與您啟動圖表執行個體以及停用執行個體的時間相關。 如果您使用內建於[播放多日錄影](playback-multi-day-recordings-tutorial.md)教學課程中的播放控制項，可以在螢幕上顯示的影片中看到時間戳記。

## <a name="clean-up-resources"></a>清除資源

如果您想要嘗試其他教學課程，請保留所建立的資源。 否則，請移至 Azure 入口網站，瀏覽至您的資源群組，選取您執行本教學課程所用的資源群組，並刪除資源群組。

## <a name="next-steps"></a>後續步驟

* 使用具有 RTSP 支援的 [IP 攝影機](https://en.wikipedia.org/wiki/IP_camera)，而非使用 RTSP 模擬器。 您可以藉由在[符合 ONVIF 標準產品頁面](https://www.onvif.org/conformant-products/)上尋找符合設定檔 G、S 或 T 的裝置，以搜尋具有 RTSP 支援的 IP 攝影機。
* 使用 AMD64 或 X64 Linux 裝置 (相較於使用 Azure Linux VM)。 此裝置必須與 IP 攝影機位於相同的網路中。 遵循[在 Linux 上安裝 Azure IoT Edge 執行階段](../../iot-edge/how-to-install-iot-edge.md)中的指示。 然後按照[將您的第一個 IoT Edge 模組部署至虛擬 Linux 裝置](../../iot-edge/quickstart-linux.md)快速入門中的指示操作，向 Azure IoT 中樞註冊裝置。