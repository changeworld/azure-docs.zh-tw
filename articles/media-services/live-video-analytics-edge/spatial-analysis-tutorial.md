---
title: 使用適用於空間分析的電腦視覺來分析即時影片 - Azure
description: 本教學課程說明如何使用即時影片分析搭配 Azure 認知服務的電腦視覺空間分析 AI 功能，分析來自 (模擬) IP 攝影機的即時影片摘要。
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: cad96847d6fbf682f1d694b0c8c255b3725e96d1
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824138"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>使用適用於空間分析 (預覽) 的電腦視覺來分析即時影片

本教學課程說明如何使用即時影片分析搭配 [Azure 認知服務的空間分析 AI 服務電腦視覺](https://azure.microsoft.com/services/cognitive-services/computer-vision/)，分析來自 (模擬) IP 攝影機的即時影片摘要。 您將看到此推斷伺服器如何讓您分析串流影片，以了解實體空間內人員和移動之間的空間關聯性。  影片摘要中的畫面子集會傳送到此推斷伺服器，結果則會傳送到 IoT Edge 中樞，而且當某些條件符合時，便會記錄影片剪輯並將其儲存為 Azure 媒體服務資產。

在本教學課程中，您將：

> [!div class="checklist"]
> * 設定資源。
> * 檢查程式碼。
> * 執行範例程式碼。
> * 監視事件。
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>建議的閱讀準備事項

開始之前，請先閱讀下列文章：

* [IoT Edge 上的 Live Video Analytics 概觀](overview.md)
* [IoT Edge 上的 Live Video Analytics 術語](terminology.md)
* [媒體圖表概念](media-graph-concept.md)
* [發生事件時錄製影片](event-based-video-recording-concept.md)
* [教學課程：開發 IoT Edge 模組](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [在 Azure Stack Edge 上部署即時影片分析](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Prerequisites

以下是要將空間分析模組連接到即時影片分析模組所需符合的必要條件。

* 開發電腦上有 [Visual Studio Code](https://code.visualstudio.com/)。 請確定您有 [Azure IoT Tools 延伸模組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
    * 確定開發電腦所連線的網路允許透過連接埠 5671 的進階訊息佇列通訊協定。 此設定可讓 Azure IoT Tools 與 Azure IoT 中樞通訊。
* 具有 GPU 加速的 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)。  
    建議您使用具有 GPU 加速的 Azure Stack Edge，不過，容器可在任何其他具有 [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/) 的裝置上執行。 
* [Azure 認知服務電腦視覺容器](https://azure.microsoft.com/services/cognitive-services/computer-vision/)，用來進行空間分析。  
    若要使用此容器，您必須擁有電腦視覺資源，才能取得相關聯的 **API 金鑰**及**端點 URI**。 在 Azure 入口網站的 [電腦視覺概觀] 和 [金鑰] 頁面上可取得此 API 金鑰。 需要有金鑰和端點才能啟動容器。

## <a name="overview"></a>概觀

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="空間分析概觀":::
 
上圖顯示本教學課程中的信號流動方式。 [邊緣模組](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)會模擬裝載了即時串流通訊協定 (RTSP) 伺服器的 IP 攝影機。 [RTSP 來源](media-graph-concept.md#rtsp-source)節點會從這部伺服器提取影片摘要，並將影片畫面傳送到[畫面播放速率篩選處理器](media-graph-concept.md#frame-rate-filter-processor)節點。 此處理器會限制到達 MediaGraphCognitiveServicesVisionExtension 處理器節點的影片串流畫面播放速率。

MediaGraphCognitiveServicesVisionExtension 節點扮演 Proxy 的角色。 其會將影片畫面轉換成指定的影像類型。 然後會透過**共用記憶體**將影像轉送至另一個邊緣模組，以在 gRPC 端點後方執行 AI 作業。 在此範例中，該邊緣模組是空間分析模組。 MediaGraphCognitiveServicesVisionExtension 處理器節點會做兩件事：

* 其會收集結果，並將事件發佈至 [IoT 中樞接收](media-graph-concept.md#iot-hub-message-sink)節點。 節點接著會將這些事件傳送至 [IoT Edge 中樞](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub)。 
* 其也會使用[信號閘道處理器](media-graph-concept.md#signal-gate-processor)從 RTSP 來源中擷取 30 秒的影片剪輯，並將其儲存為媒體服務資產。

## <a name="create-the-computer-vision-resource"></a>建立電腦視覺資源

您必須在 [Azure 入口網站](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)中或透過 Azure CLI 建立電腦視覺類型的 Azure 資源。 一旦您存取容器的要求通過核准，且已註冊 Azure 訂用帳戶識別碼，您就能夠建立資源。 移至 https://aka.ms/csgate 以提交您的使用案例和您的 Azure 訂用帳戶識別碼。  您必須使用已在 [存取要求] 表單上提供的相同 Azure 訂用帳戶來建立 Azure 資源。

### <a name="gathering-required-parameters"></a>收集必要參數

包括空間分析容器在內，所有必要的認知服務容器都有三個主要參數。 使用者授權合約 (EULA) 必須存在，且其值為 [接受]。 此外，也需要端點 URI 和 API 金鑰。

### <a name="keys-and-endpoint-uri"></a>金鑰和端點 URI

此金鑰可用來啟動空間分析容器，並可在相對應認知服務資源的 Azure 入口網站 [`Keys and Endpoint`] 頁面上取得。 瀏覽至該頁面，然後尋找金鑰和端點 URI。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="空間分析概觀":::

## <a name="set-up-azure-stack-edge"></a>設定 Azure Stack Edge

遵循[這些步驟](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep)來設定 Azure Stack Edge，並繼續遵循下列步驟以部署即時影片分析和空間分析模組。

## <a name="set-up-your-development-environment"></a>設定開發環境

1. 從這個位置複製存放庫： https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp 。
1. 在 Visual Studio Code 中，開啟存放庫下載所在的資料夾。
1. 在 Visual Studio Code 中，移至 src/cloud-to-device-console-app 資料夾。 在其中建立一個檔案，並將其命名為 appsettings.json。 此檔案將包含執行程式所需的設定。
1. 遵循下列步驟，從 Azure Stack Edge 取得 IotHubConnectionString：

    * 移至 Azure 入口網站中的 IoT 中樞，然後按一下左側瀏覽窗格中的 [`Shared access policies`]。
    * 按一下 [`iothubowner`] 以取得共用存取金鑰。
    * 複製 `Connection String – primary key`，並將其貼入 VSCode 的輸入方塊中。
    
        連接字串看起來會像這樣： <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. 將下列內容複製到檔案中。 請務必取代變數。
    
    ```json
    {
        "IoThubConnectionString" : "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. 移至 src/edge 資料夾，並建立名為 .env 的檔案。
1. 複製 /clouddrive/lva-sample/edge-deployment/.env 檔案的內容。 文字看起來應該會像下列程式碼。

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>設定部署範本  

在 /src/edge/deployment.spatialAnalysis.template.json 中尋找部署檔案。 範本中有 lvaEdge 模組、rtspsim 模組和我們的空間分析模組。

在部署範本檔案中，您需要注意幾件事：

1. 設定連接埠繫結。
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. LvaEdge 中的 `IpcMode` 和空間分析模組 createOptions 應該相同，而且設定為 [主機]。
1. 若要讓 RTSP 模擬器正常運作，請確定您已設定磁碟區界限。 如需詳細資訊，請參閱[設定 Docker 磁碟區掛接](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts)。

    1. [連線至 SMB 共用](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-add-shares#connect-to-an-smb-share)，並將[範例 bulldozer 影片檔案](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv)複製到本機共用。
    1. 您會看到 rtspsim 模組具有下列設定：
        
        ```json
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
## <a name="generate-and-deploy-the-deployment-manifest"></a>產生和部署部署資訊清單

部署資訊清單會定義要將哪些模組部署至邊緣裝置。 其也會定義這些模組的組態設定。

請遵循下列步驟，從範本檔案產生資訊清單，然後將其部署到邊緣裝置。

1. 開啟 Visual Studio Code。
1. 在 [Azure IoT 中樞] 窗格旁，選取 [其他動作] 圖示，以設定 IoT 中樞的連接字串。 您可以從 src/cloud-to-device-console-app/appsettings.json 檔案複製字串。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="空間分析概觀":::
1. 以滑鼠右鍵按一下 `src/edge/deployment.spatialAnalysis.template.json`，然後選取 [產生 IoT Edge 部署資訊清單]。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="空間分析概觀":::
    
    此動作應該會在 src/edge/config 資料夾中，建立名為 deployment.amd64.json 的資訊清單檔。
1. 以滑鼠右鍵按一下 `src/edge/config/deployment.spatialAnalysis.amd64.json`，選取 [建立單一裝置的部署]，然後選取邊緣裝置的名稱。
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="空間分析概觀":::   
1. 當系統提示您選取 IoT 中樞裝置時，請從下拉式功能表中選擇 [Azure Stack Edge]。
1. 大約 30 秒之後，請在視窗左下角重新整理 Azure IoT 中樞。 邊緣裝置現在會顯示下列已部署的模組：
    
    * IoT Edge 上的 Live Video Analytics (模組名稱為 lvaEdge)。
    * 即時串流通訊協定 (RTSP) 模擬器 (模組名稱為 rtspsim)。
    * 空間分析 (模組名稱為 spatialAnalysis)。
    
如果您成功完成部署，輸出中將會顯示如下訊息：

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

接著，您可以在 [裝置/模組] 下找到 `lvaEdge`、`rtspsim`、`spatialAnalysis` 和 `rtspsim` 模組，且這些模組的狀態應為「執行中」。

## <a name="prepare-to-monitor-events"></a>準備監視事件

若要查看這些事件，請遵循下列步驟：

1. 在 Visual Studio Code 中，開啟 [延伸模組] 索引標籤 (或按 Ctrl+Shift+X) 並搜尋 Azure IoT 中樞。
1. 按一下滑鼠右鍵，然後選取 [延伸模組設定]。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="空間分析概觀":::
1. 搜尋並啟用「顯示詳細資訊訊息」。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="空間分析概觀":::
1. 開啟 [檔案總管] 窗格，然後在左下角尋找 Azure IoT 中樞。
1. 展開 [裝置] 節點。
1. 以滑鼠右鍵按一下您的 Azure Stack Edge，然後選取 [開始監視內建事件端點]。
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="空間分析概觀":::
     
## <a name="run-the-program"></a>執行程式

會有 program.cs 在 src/cloud-to-device-console-app/operations.json 中叫用直接方法。 我們需要設定 operations.json，並提供用於媒體圖表的拓撲。  

在 operations.json 中：

* 設定如下的拓撲 (topologyFile 用於本機拓撲，topologyUrl 用於線上拓撲)：

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyFile": "../edge/spatialAnalysisTopology.json"
    }
},
```

* 建立如下的圖表執行個體，在這裡設定拓撲中的參數：

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
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
},
```
* 變更圖表拓撲的連結：

`topologyUrl` : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/lva-spatial-analysis/topology.json"

在 **GraphInstanceSet** 底下，編輯圖表拓撲的名稱，使其符合上述連結中的值：

`topologyName`：InferencingWithCVExtension

在 **GraphTopologyDelete** 底下，編輯名稱：

`name`：InferencingWithCVExtension

>[!Note]
查看如何使用 MediaGraphRealTimeComputerVisionExtension 來與空間分析模組連接。 將 ${grpcUrl} 設定為 **tcp://spatialAnalysis:<PORT_NUMBER>** ，例如 tcp://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcUrl}",
    "credentials": {
        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
        "username": "${spatialanalysisusername}",
        "password": "${spatialanalysispassword}"
    }
    },
    "image": {
    "scale": {
        "mode": "pad",
        "width": "1408",
        "height": "786"
    },
    "format": {
        "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
        "pixelFormat": "bgr24"
    }
    },
    "inputs": [
    {
        "nodeName": "frameRateFilter"
    }
    ]
}
```

執行偵錯工作階段並遵循終端機指示，其會設定拓撲、設定圖表執行個體、啟動圖表執行個體，最後刪除資源。

## <a name="interpret-results"></a>解譯結果

當媒體圖表具現化時，您應該會看到「MediaSessionEstablished」事件，在這裡是[範例 MediaSessionEstablished 事件](detect-motion-emit-events-quickstart.md#mediasessionestablished-event)。

空間分析模組也會將 AI 深入解析事件傳送至即時影片分析，然後再傳送至 IoTHub，其也會顯示在輸出中。 實體是偵測物件，事件則是 spaceanalytics 事件。 此輸出將會傳遞至即時影片分析。

personZoneEvent 的範例輸出 (來自 cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics 作業)：

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>後續步驟

在部署資訊清單檔案的 [圖表] 節點中切換 [已啟用] 旗標，以嘗試 `spatialAnalysis` 模組所提供的不同作業，例如 **personCount** 和 **personDistance**。
>[!Tip]
> 使用在畫面中有多人的[範例影片檔案](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv)。

> [!NOTE]
> 您一次只能執行一項作業。 因此，請確定只有一個旗標設定為 **true**，其他旗標則設定為 **false**。
