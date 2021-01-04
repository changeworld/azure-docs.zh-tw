---
title: 使用 IoT Edge 上的即時影片分析和 Azure 自訂視覺來分析即時影片
description: 了解如何使用 Azure 自訂視覺來建置可偵測玩具卡車的容器化模型，並使用 Azure IoT Edge 上的 Azure 即時影片分析 AI 擴充性功能，在邊緣上部署模型以從即時影片串流中偵測玩具卡車。
ms.topic: tutorial
ms.date: 09/08/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 614c4e401579eda68d8030dc2d2a42b2c4736031
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401690"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>教學課程：使用 IoT Edge 上的即時影片分析和 Azure 自訂視覺來分析即時影片

在本教學課程中，您將了解如何使用 Azure [自訂視覺](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)來建置可偵測玩具卡車的容器化模型，並使用 IoT Edge 上的 Azure 即時影片分析 [AI 擴充性功能](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model)，在邊緣上部署模型以從即時影片串流中偵測玩具卡車。

我們將示範如何將自訂視覺的功能結合在一起，以藉由上傳和標記幾個影像來建置和訓練電腦視覺模型。 您不需要具備資料科學、機器學習或 AI 的任何知識。 您也將了解即時影片分析的功能，輕鬆地將自訂模型部署為邊緣上的容器，並分析模擬的即時影片摘要。

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/custom-vision-tutorial/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/custom-vision-tutorial/python/header.md)]
::: zone-end

本教學課程說明如何：

> [!div class="checklist"]
> * 設定相關資源。
> * 在雲端建置自訂視覺模型，以偵測玩具卡車並將其部署到邊緣上。
> * 使用 HTTP 延伸模組建立媒體圖表並將其部署至自訂視覺模型。
> * 執行範例程式碼。
> * 檢查並解讀結果。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>建議的閱讀準備事項  

開始之前，請先閱讀下列文章：

* [IoT Edge 上的 Live Video Analytics 概觀](overview.md)
* [Azure 自訂視覺概觀](../../cognitive-services/custom-vision-service/overview.md)
* [IoT Edge 上的 Live Video Analytics 術語](terminology.md)
* [媒體圖表概念](media-graph-concept.md)
* [不錄製影片但進行 Live Video Analytics](analyze-live-video-concept.md)
* [使用自己的模型執行即時影片分析](use-your-model-quickstart.md)
* [教學課程：開發 IoT Edge 模組](../../iot-edge/tutorial-develop-for-linux.md)
* [如何編輯 deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>必要條件


::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/python/prerequisites.md)]
::: zone-end
## <a name="review-the-sample-video"></a>檢閱範例影片


本教學課程使用[玩具車推斷影片](https://lvamedia.blob.core.windows.net/public/t2.mkv)檔案來模擬即時串流。 您可以透過 [VLC 媒體播放器](https://www.videolan.org/vlc/)之類的應用程式來檢查影片。 選取 **Ctrl+N**，然後貼上 [玩具車推斷影片](https://lvamedia.blob.core.windows.net/public/t2.mkv)的連結以開始播放。 在觀看影片時請注意，在 36 秒的標記處，玩具卡車會出現在影片中。 自訂模型已完成定型而可偵測到此特定玩具卡車。 在本教學課程中，您將在 IoT Edge 上使用即時影片分析來偵測這類玩具卡車，並將相關聯的推斷事件發佈到 IoT Edge 中樞。

## <a name="overview"></a>概觀

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="顯示自訂視覺概觀的圖表。":::

上圖顯示本教學課程中的信號流動方式。 [邊緣模組](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)會模擬裝載了即時串流通訊協定 (RTSP) 伺服器的 IP 攝影機。 [RTSP 來源](media-graph-concept.md#rtsp-source) 節點會從這部伺服器提取影片摘要，然後將影片畫面傳送至 [HTTP 擴充處理器](media-graph-concept.md#http-extension-processor) 節點。

HTTP 延伸模組節點扮演 Proxy 的角色。  其會對使用 `samplingOptions` 欄位所設定的傳入影片畫面進行取樣，並將影片畫面轉換為指定的影像類型。 然後，會透過 REST 將影像轉送至另一個邊緣模組，以在 HTTP 端點後方執行 AI 模型。 在此範例中，該邊緣模組是使用自訂視覺所建置的玩具卡車偵測器模型。 HTTP 延伸模組處理器節點會收集偵測結果，並將事件發佈至 [Azure IoT 中樞接收](media-graph-concept.md#iot-hub-message-sink)節點。 節點接著會將這些事件傳送至 [IoT Edge 中樞](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)。

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>建置及部署自訂視覺玩具偵測模型 

如自訂視覺所建議的名稱，您可以使用此名稱在雲端中建置自己的自訂物件偵測器或分類器。 其提供了簡單、容易使用且直覺的介面，供您建置可透過容器部署在雲端或邊緣的自訂視覺模型。

若要建置玩具卡車偵測器，請逐步遵循[快速入門：使用自訂視覺網站建置物件偵測器](../../cognitive-services/custom-vision-service/get-started-build-detector.md)。

其他注意事項：
 
* 在本教學課程中，請勿使用快速入門文章中[必要條件一節](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites)所提供的範例影像。 相反地，我們使用了特定的影像集來建置玩具偵測器自訂視覺模型。 在快速入門中，當系統要求您[選擇訓練影像](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images)時，請使用[這些影像](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip)。
* 在快速入門的標記影像一節中，請確實使用「貨車」標籤來標記圖片中的玩具卡車。

完成之後，您可以使用 [效能] 索引標籤上的 [匯出] 按鈕，將模型匯出至 Docker 容器。請務必選擇 Linux 作為容器平台類型。 這是將用來執行容器的平台。 容器下載所在的電腦可以是 Windows 或 Linux。 下列指示根據的是下載至 Windows 電腦的容器檔案。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="顯示已選取 Dockerfile 的畫面。":::
 
1. 您應該已將 ZIP 檔案下載到名為 `<projectname>.DockerFile.Linux.zip` 的本機電腦上。 
1. 檢查是否已安裝 Docker。 如果未安裝，請為您的 Windows 桌面安裝 [Docker](https://docs.docker.com/get-docker/)。
1. 將下載的檔案解壓縮到您選擇的位置。 使用命令列移至已解壓縮的資料夾目錄。
    
    執行下列命令：
    
    1. `docker build -t cvtruck` 
    
        此命令會下載許多套件、建置 Docker 映像，並將其標記為 `cvtruck:latest`。
    
        > [!NOTE]
        > 如果成功，您應該會看到下列訊息：`Successfully built <docker image id>` 和 `Successfully tagged cvtruck:latest`。 如果建置命令失敗，請再試一次。 有時候，相依性套件不會在第一次執行命令時下載。
    1. `docker  image ls`

        此命令會檢查新影像是否位於本機登錄中。
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        此命令應該將 Dockers 公開的連接埠 (80) 發佈到本機電腦的連接埠 (80)。
    1. `docker container ls`
    
        此命令會檢查連接埠對應，以及 Docker 容器是否已在電腦上成功執行。 輸出應該類似如下：

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            此命令會測試本機電腦上的容器。 如果影像中的貨車與我們用來訓練模型的貨車相同，輸出應該會類似下列範例。 其指出已偵測到貨車，且機率為 90.12%。
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>檢查範例檔案


::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>產生和部署部署資訊清單

1. 在 Visual Studio Code 中，移至 src/cloud-to-device-console-app/operations.json。

1. 在 `GraphTopologySet` 底下，確定符合下列條件：<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. 在 `GraphInstanceSet` 底下，確定：
    1. `"topologyName" : "InferencingWithHttpExtension"`
    1. 將下列項目新增至參數陣列頂端：`{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. 將 `rtspUrl` 參數值變更為 `"rtsp://rtspsim:554/media/t2.mkv"`。
1. 在 `GraphTopologyDelete` 底下，確定 `"name": "InferencingWithHttpExtension"`。
1. 以滑鼠右鍵按一下 src/edge/ deployment.customvision.template.json 檔案，然後選取 [產生 IoT Edge 部署資訊清單]。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="顯示 [產生 IoT Edge 部署資訊清單] 的螢幕擷取畫面。":::
  
    此動作應該會在 src/edge/config 資料夾中建立名為 deployment.customvision.amd64.json 的資訊清單檔。
1. 開啟 src/edge/ deployment.customvision.template.json 檔案，然後尋找 `registryCredentials` JSON 區塊。 在此區塊中，您會找到 Azure 容器登錄的位址以及其使用者名稱和密碼。
1. 藉由在命令列上遵循下列步驟，將本機自訂視覺容器推送至 Azure Container Registry 執行個體：

    1. 藉由執行下列命令來登入登入：
    
        `docker login <address>`
    
        當系統要求驗證時，請輸入使用者名稱和密碼。
        
        > [!NOTE]
        > 命令列上不會顯示密碼。
    1. 使用此命令標記您的影像： <br/>`docker tag cvtruck   <address>/cvtruck`.
    1. 使用此命令推送您的影像： <br/>`docker push <address>/cvtruck`.

        如果成功，您應該會在命令列上看到 `Pushed`，以及影像的 SHA。
    1. 您也可以藉由檢查 Azure 入口網站上的 Azure Container Registry 執行個體來進行確認。 在這裡，您會看到存放庫的名稱以及標籤。
1. 選取左下角 [AZURE IOT 中樞] 窗格旁的 [更多動作] 圖示，以設定 IoT 中樞連接字串。 您可以從 appsettings.json 檔案複製字串。 (以下是另一個建議的方法，可確保您在 Visual Studio Code 內透過[選取 IoT 中樞命令](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub) 設定適當的 IoT 中樞)。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="顯示設定 IoT 中樞連接字串的螢幕擷取畫面。":::
1. 接下來，以滑鼠右鍵按一下 src/edge/config/ deployment.customvision.amd64.json，然後選取 [建立單一裝置的部署]。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="顯示 [建立單一裝置的部署] 的螢幕擷取畫面。":::
1. 接著，系統會要求您選取 IoT 中樞裝置。 從下拉式清單中選取 [lva-sample-device]。
1. 在大約 30 秒後，重新整理左下方區段中的 Azure IoT 中樞。 您的邊緣裝置應已部署下列模組：

    * IoT Edge 模組 (名稱為 `lvaEdge`) 上的即時影片分析。
    * 名為 `rtspsim` 的模組，其會模擬 RTSP 伺服器，以作為即時影片摘要的來源。
    * 名為 `cv` 的模組，如名稱所示，其為自訂視覺玩具卡車偵測模型，會將自訂視覺套用至影像，並傳回多個標籤類型。 (我們的模型已在單一個標籤「貨車」上訓練)。

## <a name="prepare-for-monitoring-events"></a>準備監視事件

以滑鼠右鍵按一下 Live Video Analytics 裝置，然後選取 [開始監視內建事件端點]。 您需要執行此步驟，才能在 Visual Studio Code 的 [輸出] 視窗中監視 IoT 中樞事件。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="顯示 [開始監視內建事件端點] 的螢幕擷取畫面。":::

## <a name="run-the-sample-program"></a>執行範例程式

如果您在瀏覽器中開啟本教學課程的圖表拓撲，您會看到 `inferencingUrl` 的值已設定為 `http://cv:80/image`。 這項設定表示在即時影片中偵測到玩具卡車 (如果有的話) 之後，推斷伺服器會傳回結果。

1. 在 Visual Studio Code 中，開啟 [延伸模組] 索引標籤 (或選取 **Ctrl+Shift+X**) 並搜尋 Azure IoT 中樞。
1. 按一下滑鼠右鍵，然後選取 [延伸模組設定]。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="顯示延伸模組設定的螢幕擷取畫面。":::
1. 搜尋並啟用「**顯示詳細資訊訊息**」。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="顯示 [顯示詳細資訊訊息] 的螢幕擷取畫面。":::
1. 若要啟動偵錯工作階段，請選取 **F5** 鍵。 您會在 [終端機] 視窗中看到一些列印的訊息。
1. operations.json 程式碼首先會呼叫直接方法 `GraphTopologyList` 和 `GraphInstanceList`。 如果您在完成先前的快速入門之後清除了資源，則此程序會傳回空的清單，然後暫停。 若要繼續，請選取 **Enter** 鍵。
    
   [終端機] 視窗會顯示下一組直接方法呼叫：
    
   * 使用上述 `topologyUrl` 來呼叫 `GraphTopologySet`。
   * 會使用下列主體的對 `GraphInstanceSet` 呼叫：
        
   ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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
    
   * 呼叫 `GraphInstanceActivate` 以啟動圖表執行個體和影片流程。
   * 第二次呼叫 `GraphInstanceList` 以顯示圖表執行個體處於執行中狀態。
    
1. [終端] 視窗中的輸出會在出現「按 Enter 繼續」提示時暫停。 還不要選取 **Enter** 鍵。 請向上捲動，查看您所叫用直接方法的 JSON 回應承載。
1. 切換至 Visual Studio Code 中的 [輸出] 視窗。 您會看到訊息指出 IoT Edge 模組上的 Live Video Analytics 正在傳送到 IoT 中樞。 本教學課程的下一節會討論這些訊息。
1. 媒體圖表會繼續執行並列印結果。 RTSP 模擬器會持續循環播放來源影片。 若要停止媒體圖表，請返回 [終端] 視窗，然後選取 **Enter** 鍵。
下一系列的呼叫會清除資源：
    
   * 對 `GraphInstanceDeactivate` 的呼叫會停用圖表執行個體。
   * 對 `GraphInstanceDelete` 的呼叫會刪除執行個體。
   * 對 `GraphTopologyDelete` 的呼叫會刪除拓撲。
   * 最後對 `GraphTopologyList` 的呼叫會顯示清單是空的。
    
## <a name="interpret-the-results"></a>解讀結果

當您執行媒體圖表時，來自 HTTP 延伸模組處理器節點的結果會將 IoT 中樞接收節點傳遞至 IoT 中樞。 您在 [輸出] 視窗中看到的訊息包含主體區段和 `applicationProperties` 區段。 如需詳細資訊，請參閱[建立及讀取 IoT 中樞訊息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下列訊息中，Live Video Analytics 模組會定義應用程式屬性和主體內容。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件

具現化媒體圖表時，RTSP 來源節點會嘗試連線到在 rtspsim-live555 容器中執行的 RTSP 伺服器。 如果連線成功，則會列印下列事件。 事件類型是 `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`。

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

在此訊息中，請注意下列詳細資料：

* 此訊息為診斷事件。 `MediaSessionEstablished` 表示 RTSP 來源節點 (主體) 已與 RTSP 模擬器連線，並開始接收模擬的即時摘要。
* 在 `applicationProperties` 中，`subject` 表示訊息是從媒體圖表中的 RTSP 來源節點所產生。
* 在 `applicationProperties` 中，事件類型表示此事件是診斷事件。
* 事件時間 (event time) 表示事件發生的時間。
* 主體 (body) 包含診斷事件的相關資料。 在此情況下，資料包含[工作階段描述通訊協定 (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 詳細資料。

### <a name="inference-event"></a>推斷事件

HTTP 擴充功能處理器節點會接收來自自訂視覺容器的推斷結果，並透過 IoT 中樞接收節點以推斷事件的形式發出結果。

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

請注意上述訊息中的下列資訊：

* `applicationProperties` 中的主體會參考訊息產生來源媒體圖表中的節點。 在此情況下，訊息是來自 HTTP 延伸模組處理器。
* `applicationProperties` 中的事件類型 (event type) 表示這是分析推斷事件。
* 事件時間 (event time) 表示事件發生的時間。
* 主體包含有關分析事件的資料。 在此情況下，事件是推斷事件，因此主體包含稱為預測的推斷陣列。
* [預測] 區段包含在畫面中找到玩具貨車 (標籤為「貨車」) 的預測清單。 您應該還記得，「貨車」是您提供給玩具卡車自訂訓練模型的自訂標記。 模型會在輸入影片中推斷並識別玩具卡車，並具有不同的機率信賴分數。

## <a name="clean-up-resources"></a>清除資源

如果您想要嘗試其他教學課程或快速入門，請保留已建立的資源。 否則，請移至 Azure 入口網站，瀏覽至您的資源群組，選取您執行本教學課程所用的資源群組，並刪除所有資源。

## <a name="next-steps"></a>後續步驟

檢閱進階使用者的其他挑戰：

* 使用具有 RTSP 支援的 [IP 攝影機](https://en.wikipedia.org/wiki/IP_camera)，而非使用 RTSP 模擬器。 您可以在[符合 ONVIF 標準的](https://www.onvif.org/conformant-products/)產品頁面上，搜尋支援 RTSP 的 IP 攝影機。 尋找符合設定檔 G、S 或 T 的裝置。
* 使用 AMD64 或 x64 Linux 裝置 (而非使用 Azure Linux VM)。 此裝置必須與 IP 攝影機位於相同的網路中。 您可以遵循[在 Linux 上安裝 Azure IoT Edge 執行階段](../../iot-edge/how-to-install-iot-edge.md)中的指示。

然後遵循[將您的第一個 IoT Edge 模組部署至虛擬 Linux 裝置](../../iot-edge/quickstart-linux.md)中的指示，向 Azure IoT 中樞註冊裝置。