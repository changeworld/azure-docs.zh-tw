---
title: 使用 Intel 提供的 AI 擴充功能 OpenVINO™ Model Server 來分析即時影片 - Intel 提供的 AI 擴充功能
description: 在本教學課程中，您將使用 Intel 提供的 AI 模型伺服器，從 (模擬) IP 相機分析即時影片摘要。
ms.topic: tutorial
ms.date: 09/08/2020
titleSuffix: Azure
ms.openlocfilehash: d03737f43ee719b72860e7ffeff076e3f156cade
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776335"
---
# <a name="tutorial-analyze-live-video-by-using-openvino-model-server--ai-extension-from-intel"></a>教學課程：使用 Intel 提供的 AI 擴充功能 OpenVINO™ Model Server 來分析即時影片 - Intel 提供的 AI 擴充功能 

本教學課程說明如何使用 Intel 提供的 AI 擴充功能 OpenVINO™ Model Server，從 (模擬) IP 相機分析即時影片摘要。 您會看到此推斷伺服器如何讓您存取用來偵測物件的模型 (人員、車輛或自行車)，以及用來分類車輛的模型。 系統會將即時影片摘要中的框架子集傳送至此推斷伺服器務，並將結果傳送至 IoT Edge 中樞。

本教學課程會使用 Azure VM 作為 IoT Edge 裝置，以及使用模擬的即時影片串流。 其根據的是以 C# 撰寫的範例程式碼，並且會在[偵測動作並發出事件](detect-motion-emit-events-quickstart.md)快速入門的基礎上來建置。

> [!NOTE]
> 本教學課程需要使用 x86-64 電腦作為 Edge 裝置。

## <a name="prerequisites"></a>必要條件

* 包含有效訂用帳戶的 Azure 帳戶。 如果您還未擁有帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 具有下列擴充功能的 [Visual Studio Code](https://code.visualstudio.com/)：
    * [Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* 如果您未完成[偵測動作並發出事件](detect-motion-emit-events-quickstart.md)快速入門，則請務必完成[設定 Azure 資源](detect-motion-emit-events-quickstart.md#set-up-azure-resources)的步驟。

> [!TIP]
> 安裝 Azure IoT Tools 時，系統可能會提示您安裝 Docker。 您可以忽略提示。

## <a name="review-the-sample-video"></a>檢閱範例影片

當您設定 Azure 資源時，系統會將停車場的短片複製到 Azure 中您要作為 IoT Edge 裝置的 Linux VM。 本快速入門會使用影片檔案來模擬即時串流。

開啟應用程式，例如 [VLC media player](https://www.videolan.org/vlc/)。 選取 Ctrl+N，然後貼上[影片](https://lvamedia.blob.core.windows.net/public/lots_015.mkv)的連結以開始播放。 您會看到停車場中的車輛的影像，其中大部分車輛都是停住的，而其中一輛正在移動。

在本快速入門中，您將會在 IoT Edge 上使用即時影片分析，並搭配 Intel 提供的 AI 擴充功能 OpenVINO™ Model Server，以偵測車輛之類的物件或進行分類。 您會推斷事件的結果發佈到 IoT Edge 中樞。

## <a name="overview"></a>概觀

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.svg" alt-text="概觀":::

上圖顯示本快速入門中的信號流動方式。 [邊緣模組](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)會模擬裝載了即時串流通訊協定 (RTSP) 伺服器的 IP 攝影機。 [RTSP 來源](media-graph-concept.md#rtsp-source)節點會從這部伺服器提取影片摘要，並將影片畫面傳送到[畫面播放速率篩選處理器](media-graph-concept.md#frame-rate-filter-processor)節點。 此處理器會限制影片串流到達 [HTTP 延伸模組處理器](media-graph-concept.md#http-extension-processor)節點的畫面播放速率。 

HTTP 延伸模組節點扮演 Proxy 的角色。 其會將影片畫面轉換成指定的影像類型。 然後，會透過 REST 將影像轉送至另一個邊緣模組，以在 HTTP 端點後方執行 AI 模型。 在此範例中，該邊緣模組是 Intel 提供的 AI 擴充功能 OpenVINO™ Model Server。 HTTP 延伸模組處理器節點會收集偵測結果，並將事件發佈至 [IoT 中樞接收](media-graph-concept.md#iot-hub-message-sink)節點。 節點接著會將這些事件傳送至 [IoT Edge 中樞](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)。

在本教學課程中，您將：

1. 建立和部署媒體圖表，並加以修改。
1. 解譯解譯。
1. 清除資源。

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>關於 Intel 提供的 AI 擴充功能 OpenVINO™ Model Server

Intel® 發佈的 [OpenVINO™ 工具組](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) (開放式視覺化推斷和類神經網路最佳化) 是免費的軟體套件，可協助開發人員和資料科學家加速電腦視覺工作負載、簡化深度學習推斷和部署，並跨 Intel® 平台輕鬆地從邊緣到雲端執行。 其中包含使用模型最佳化程式和推斷引擎的 Intel® Deep Learning Deployment Toolkit，以及 [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo) 存放庫，其中包含超過 40 個已最佳化的預先定型模型。

為了建立複雜、高效能的即時影片分析解決方案，IoT Edge 模組上的即時影片分析應該與可利用邊緣規模的強大的推斷引擎配對。 在本教學課程中，推斷要求會傳送至 [Intel 提供的 AI 擴充功能 OpenVINO™ Model Server](https://aka.ms/lva-intel-ovms)，這是一個 Edge 模組，其設計訴求是在 IoT Edge 上使用即時影片分析。 此推斷伺服器模組包含 OpenVINO™ Model Server (OVMS)，這是由 OpenVINO™ 工具組所提供的推斷伺服器，已針對電腦視覺工作負載進行高度最佳化，並針對 Intel® 架構進行開發。 擴充功能已新增至 OVMS，可讓您輕鬆地交換影格，以及推斷伺服器與 IoT Edge 模組上即時影片分析之間的推斷結果，並可讓您執行任何支援 OpenVINO™ 工具組的模型 (您可以藉由修改[程式碼](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper)來自訂推斷伺服器模組)。 您可以進一步從 Intel® 硬體提供的各種加速機制中選擇。 包括 CPU (Atom、Core、Xeon)、FPGA、VPU。

在此推斷伺服器的初始版本中，您可以存取下列[模型](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models)：

- 車輛偵測 (推斷 URL： http://{module-name}:4000/vehicleDetection)
- Person/Vehicle/Bike Detection (推斷 URL： http://{module-name}:4000/personVehicleBikeDetection)
- 車輛分類 (推斷 URL： http://{module-name}:4000/vehicleClassification)
- 臉部偵測 (推斷 URL： http://{module-name}:4000/faceDetection)

> [!NOTE]
> 藉由下載並使用 Edge 模組：Intel 提供的 AI 擴充功能 OpenVINO™ Model Server 以及隨附的軟體，您同意[授權合約](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)下的條款及條件。
> Intel 承諾於尊重人權，並避免參與濫用人權的行為。 請參閱 [Intel 的全球人權準則](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html)。 Intel 的產品和軟體僅適用於不會造成或違反國際公認人權的應用程式。

## <a name="create-and-deploy-the-media-graph"></a>建立和部署媒體圖表

### <a name="examine-and-edit-the-sample-files"></a>查看和編輯範例檔案

您必須將範例程式碼下載至資料夾，這是必要條件的一部分。 請遵循下列步驟來檢查和編輯範例檔案。

1. 在 Visual Studio Code 中，移至 *src/edge*。 您會看到 *.env* 檔案和一些部署範本檔案。

    部署範本會參考邊緣裝置的部署資訊清單。 其包含一些預留位置值。 *.env* 檔案包含這些變數的值。

1. 移至 *src/cloud-to-device-console-app* 資料夾。 在這裡，您會看到 *appsettings.json* 檔案和其他幾個檔案：

    * ***c2d-console-app.csproj*** - Visual Studio Code 的專案檔。
    * ***operations.json*** - 您想要讓程式執行的作業清單。
    * ***Program.cs*** - 範例程式碼。 此程式碼：

        * 載入應用程式設定。
        * 叫用 IoT Edge 模組上 Live Video Analytics 所公開的直接方法。 您可以使用模組，藉由叫用其[直接方法](direct-methods.md)來分析即時影片串流。
        * 會暫停，讓您可以在 [終端機] 視窗中檢查程式的輸出，並在 [輸出] 視窗中檢查模組所產生的事件。
        * 叫用直接方法來清除資源。


1. 編輯 *operations.json* 檔案：
    * 變更圖表拓撲的連結：

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json"`

    * 在 `GraphInstanceSet` 底下，編輯圖表拓撲的名稱，使其符合前一個連結中的值：

      `"topologyName" : "InferencingWithOpenVINO"`

    * 在 `GraphTopologyDelete` 底下，編輯名稱：

      `"name": "InferencingWithOpenVINO"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>產生和部署 IoT Edge 部署資訊清單

1. 以滑鼠右鍵按一下 src/edge/deployment.openvino.template.json 檔案，然後選取 [產生 IoT Edge 部署資訊清單]。

    ![產生 IoT Edge 部署資訊清單](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    *deployment.yolov3.amd64.json* 資訊清單檔案會建立在 *src/edge/config* 資料夾中。

1. 如果您已完成[偵測動作並發出事件](detect-motion-emit-events-quickstart.md)快速入門，請略過此步驟。 

    否則，請在左下角的 [Azure IoT 中樞] 窗格附近，選取 [其他動作] 圖示，然後選取 [設定 IoT 中樞連接字串]。 您可以從 *appsettings.json* 檔案複製字串。 或者，若要確保您已在 Visual Studio Code 中設定適當的 IoT 中樞，請使用[選取 IoT 中樞命令](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)。
    
    ![設定 IoT 中樞連接字串](./media/quickstarts/set-iotconnection-string.png)

1. 以滑鼠右鍵按一下 src/edge/config/deployment.openvino.amd64.json，然後選取 [建立單一裝置的部署]。 

    ![建立單一裝置的部署](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. 當系統提示您選取 IoT 中樞裝置時，請選取 [lva-sample-device]。
1. 大約 30 秒之後，請在視窗左下角重新整理 Azure IoT 中樞。 邊緣裝置現在會顯示下列已部署的模組：

    * 名為 **lvaEdge** 的 Live Video Analytics 模組
    * 名為 **rtspsim** 的模組，其會模擬 RTSP 伺服器並作為即時影片摘要的來源
    * **openvino** 模組是 Intel 提供的 AI 擴充功能模組 OpenVINO™ Model Server 

### <a name="prepare-to-monitor-events"></a>準備監視事件

以滑鼠右鍵按一下 Live Video Analytics 裝置，然後選取 [開始監視內建事件端點]。 您需要執行此步驟，才能在 Visual Studio Code 的 [輸出] 視窗中監視 IoT 中樞事件。 

![開始監視](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles"></a>執行範例程式來偵測車輛
如果您在瀏覽器中開啟本教學課程的[圖形拓撲](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json)，會看到 `inferencingUrl` 的值已設定為 `http://openvino:4000/vehicleDetection`，這表示在即時影片中偵測車輛 (如果有) 後，推斷伺服器會傳回結果。

1. 在 Visual Studio Code 中，開啟 [延伸模組] 索引標籤 (或按 Ctrl+Shift+X) 並搜尋 Azure IoT 中樞。
1. 按一下滑鼠右鍵，然後選取 [延伸模組設定]。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="概觀":::
1. 搜尋並啟用「顯示詳細資訊訊息」。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="概觀"
               }
             ]
           }
         }
         ```

     * 會啟動圖表執行個體和影片流程的對 `GraphInstanceActivate` 呼叫
     * 對 `GraphInstanceList` 的第二個呼叫，會顯示處於執行中狀態的圖表執行個體
1. [終端機] 視窗中的輸出會在 `Press Enter to continue` 提示字元處暫停。 還不要選取 Enter 鍵。 請向上捲動，查看您所叫用直接方法的 JSON 回應承載。
1. 切換至 Visual Studio Code 中的 [輸出] 視窗。 您會看到訊息指出 IoT Edge 模組上的 Live Video Analytics 正在傳送到 IoT 中樞。 本快速入門的下一節會討論這些訊息。
1. 媒體圖表會繼續執行並列印結果。 RTSP 模擬器會持續循環播放來源影片。 若要停止媒體圖表，請返回 [終端機] 視窗，然後選取 Enter 鍵。 

    下一系列的呼叫會清除資源：
      * 對 `GraphInstanceDeactivate` 的呼叫會停用圖表執行個體。
      * 對 `GraphInstanceDelete` 的呼叫會刪除執行個體。
      * 對 `GraphTopologyDelete` 的呼叫會刪除拓撲。
      * 最後對 `GraphTopologyList` 的呼叫會顯示清單是空的。

## <a name="interpret-results"></a>解譯結果

當您執行媒體圖表時，來自 HTTP 延伸模組處理器節點的結果會將 IoT 中樞接收節點傳遞至 IoT 中樞。 您在 [輸出] 視窗中看到的訊息包含 `body` 區段和 `applicationProperties` 區段。 如需詳細資訊，請參閱[建立及讀取 IoT 中樞訊息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下列訊息中，Live Video Analytics 模組會定義應用程式屬性和主體內容。 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件

具現化媒體圖表時，RTSP 來源節點會嘗試連線到在 rtspsim-live555 容器中執行的 RTSP 伺服器。 如果連線成功，則會列印下列事件。 事件類型是 `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-07-24T16:42:18.1280000Z"
  }
}
```

在此訊息中，請注意下列詳細資料：

* 此訊息為診斷事件。 `MediaSessionEstablished` 表示 RTSP 來源節點 (主體) 已與 RTSP 模擬器連線，並開始接收 (模擬的) 即時摘要。
* 在 `applicationProperties` 中，`subject` 表示訊息是從媒體圖表中的 RTSP 來源節點所產生。
* 在 `applicationProperties` 中，`eventType` 表示此事件是診斷事件。
* `eventTime` 表示事件的發生時間。
* `body` 包含診斷事件的相關資料。 在此情況下，資料包含[工作階段描述通訊協定 (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 詳細資料。

### <a name="inference-event"></a>推斷事件

HTTP 擴充功能處理器節點會接收 Intel 提供的 AI 擴充功能模組 OpenVINO™ Model Server 的推斷結果。 然後，其會透過 IoT 中樞接收節點，以推斷事件的形式來發出結果。 

在這些事件中，類型會設定為 `entity` 以表示其為實體，例如汽車或卡車。 `eventTime` 值是偵測到物件的 UTC 時間。 

在下列範例中，偵測到兩輛車輛，信賴值在 0.9 以上。

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.9951713681221008
          },
          "box": {
            "l": 0.042635321617126465,
            "t": 0.4004564881324768,
            "w": 0.10961548984050751,
            "h": 0.07942074537277222
          }
        }
      },
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.928486168384552
          },
          "box": {
            "l": 0.2506900727748871,
            "t": 0.07512682676315308,
            "w": 0.05470699071884155,
            "h": 0.07408371567726135
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:43:18.1280000Z"
  }
}
```

在訊息中，請注意下列詳細資料：

* 在 `applicationProperties` 中，`subject` 會參考訊息產生來源圖表拓撲中的節點。 
* 在 `applicationProperties` 中，`eventType` 表示此事件是分析事件。
* `eventTime` 值是事件的發生時間。
* `body` 包含有關分析事件的資料。 在此情況下，事件是推斷事件，因此主體包含 `inferences` 資料。
* `inferences` 區段表示 `type` 是 `entity`。 此區段包含實體的其他相關資料。

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>執行範例程式來偵測人員或車輛或自行車
若要使用不同的模型，您需要修改圖形拓撲，以及 `operations.json` 檔案。

將[圖形拓撲](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json)複製到本機檔案，例如 `C:\TEMP\topology.json`。 開啟該複本，然後將 `inferencingUrl` 的值編輯為 `http://openvino:4000/personVehicleBikeDetection`。

接下來在 Visual Studio Code 中，移至 src/cloud-to-device-console-app 資料夾並開啟 `operations.json` 檔案。 將具有 `topologyUrl` 的程式碼行編輯為：

```
      "topologyFile" : "C:\\TEMP\\topology.json" 
```
您現在可以重複上述步驟，使用新的拓撲再次執行範例程式。 推斷結果會類似 (在結構描述中) 於車輛偵測模型的結果，只是 `subtype` 會設為 `personVehicleBikeDetection`。

## <a name="run-the-sample-program-to-classify-vehicles"></a>執行範例程式來分類車輛
在 Visual Studio Code 中，開啟上一個步驟中 `topology.json` 的本機複本，然後將 `inferencingUrl` 的值編輯為 `http://openvino:4000/vehicleClassification`。 如果您已執行前一個範例來偵測人員或車輛或自行車，則不需要再次修改 `operations.json` 檔案。

您現在可以重複上述步驟，使用新的拓撲再次執行範例程式。 範例分類結果如下所示。

```
[IoTHubMonitor] [9:44:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "classification",
        "subtype": "color",
        "classification": {
          "tag": {
            "value": "black",
            "confidence": 0.9179772138595581
          }
        }
      },
      {
        "type": "classification",
        "subtype": "type",
        "classification": {
          "tag": {
            "value": "truck",
            "confidence": 1
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:44:18.1280000Z"
  }
}
```

## <a name="run-the-sample-program-to-detect-faces"></a>執行範例程式來偵測臉部
在 Visual Studio Code 中，開啟上一個步驟中 `topology.json` 的本機複本，然後將 `inferencingUrl` 的值編輯為 `http://openvino:4000/faceDetection`。 如果您已執行前一個範例來偵測人員或車輛或自行車，則不需要再次修改 `operations.json` 檔案。

您現在可以重複上述步驟，使用新的拓撲再次執行範例程式。 範例偵測結果如下所示 (請注意：上面使用的停車場影片不包含任何可偵測的臉部，您應該使用另一段影片才能嘗試此模型)。

```
[IoTHubMonitor] [9:54:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:54:18.1280000Z"
  }
}
```

## <a name="clean-up-resources"></a>清除資源

如果要嘗試其他快速入門或教學課程，請保留您建立的資源。 否則，請移至 Azure 入口網站、移至您的資源群組、選取您執行本教學課程時所用的資源群組，然後刪除所有資源。

## <a name="next-steps"></a>後續步驟

檢閱進階使用者的其他挑戰：

* 使用具有 RTSP 支援的 [IP 攝影機](https://en.wikipedia.org/wiki/IP_camera)，而非使用 RTSP 模擬器。 您可以在[符合 ONVIF 標準的](https://www.onvif.org/conformant-products/)產品頁面上，搜尋支援 RTSP 的 IP 攝影機。 尋找符合設定檔 G、S 或 T 的裝置。
* 使用 AMD64 或 x64 Linux 裝置 (而非使用 Azure Linux VM)。 此裝置必須與 IP 攝影機位於相同的網路中。 您可以遵循[在 Linux 上安裝 Azure IoT Edge 執行階段](../../iot-edge/how-to-install-iot-edge-linux.md)中的指示。 然後遵循[將您的第一個 IoT Edge 模組部署至虛擬 Linux 裝置](../../iot-edge/quickstart-linux.md)中的指示，向 Azure IoT 中樞註冊裝置。
