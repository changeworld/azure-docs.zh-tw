---
title: 開發和部署 gRPC 推斷伺服器-Azure
description: 本文提供如何開發和部署 gRPC 推斷伺服器的指引。
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 6184a369e73c26d3a8a716f9daf1c0420a5239fe
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881647"
---
# <a name="how-to-guide--develop-and-deploy-a-grpc-inference-server"></a>操作指南–開發和部署 gRPC 推斷伺服器

## <a name="overview"></a>概觀

本文說明如何將 AI 模型 () 您在 gRPC 推斷伺服器中選擇的專案，使其可以與即時影片分析整合 (透過圖形擴充功能的 LVA) 。 

## <a name="suggested-pre-reading"></a>建議的閱讀準備事項

* [Media Graph 擴充功能](media-graph-extension-concept.md)
* [gRPC 擴充通訊協定](grpc-extension-protocol.md)
* [推斷中繼資料架構](inference-metadata-schema.md)
* [GRPC 簡介](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [proto3 語言指南](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>先決條件

* 執行其中一個 [受支援 Linux 作業系統](../../iot-edge/support.md#operating-systems) 或 Windows 電腦的 x86-64 或 ARM64 裝置。
* 在您的電腦上[安裝 Docker](https://docs.docker.com/desktop/#download-and-install) 。
* 安裝 [IoT Edge 運行](../../iot-edge/how-to-install-iot-edge.md?tabs=linux)時間。

## <a name="grpc-implementation-steps"></a>gRPC 的執行步驟

若要建立 gRPC 推斷伺服器，並使用即時影片分析將其實作為擴充功能，將會使用下列步驟：

### <a name="setup"></a>安裝程式：

執行必要的步驟，以 [部署即時影片分析模組並在 IoT Edge 裝置上運作](deploy-iot-edge-device.md)。

### <a name="high-level-implementation-steps"></a>高層級的執行步驟：

1. 選擇 gRPC 所支援的多種語言之一： c #、c + +、Dart、Go、JAVA、Node、目標-C、PHP、Python、Ruby。
1. 使用 [proto3](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)檔案來執行 gRPC 伺服器，以與即時影片分析進行通訊。

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/inference-srv-container-process.png" alt-text="將使用 proto3 檔案與即時影片分析通訊的 gRPC 伺服器":::

    在此服務中：
    1. 處理伺服器與用戶端之間的會話描述訊息交換。
    1. 處理 [媒體範例訊息](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) 並傳回結果。

        1. 叫用使用定型模型的推斷引擎，以對內送訊息進行推斷。
        1. 接收引擎的推斷結果，將其封裝回媒體範例，並使用 [推斷](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/inferencing.proto) 的檔案提交回即時影片分析。

            或者，將任何媒體轉換函式叫用至媒體範例。
1. 部署 gRPC 伺服器的執行。 執行這項操作的方法有兩種：

    1. 部署為與即時影片分析並存的 IoT 模組
    1. 以 IoT 模組的形式部署至網路可存取的節點 (內部部署或雲端) ，可與即時影片分析模組交換資料。
1. 使用即時影片分析模組設定即時影片分析圖形拓朴，並將其指向 gRPC 伺服器。

### <a name="recommendation"></a>建議：

在相同節點上共置時，可使用共用記憶體來獲得最佳效能。 這會要求您使用程式設計語言/環境所公開的 Linux 共用記憶體功能。

1. 開啟 Linux 共用記憶體控制碼。
1. 在接收到框架時，存取共用記憶體內的位址位移。
1. 確認畫面格處理完成，以便即時影片分析可回收其記憶體。

## <a name="create-a-grpc-inference-server"></a>建立 gRPC 推斷伺服器

現在您將建立 IoT Edge 的模組 (外部 AI) ，以透過共用記憶體使用 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 訊息，接受來自即時影片分析的影片畫面、將畫面分類為「深色」或「淺色」，並使用 [推斷中繼資料架構](inference-metadata-schema.md)將結果傳回至「即時影片分析」中的「IoT 中樞」訊息接收。

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/external-ai.png" alt-text=" (外部 AI) 建立 IoT Edge 模組 ":::

此 gRPC 推斷伺服器是一種 .NET Core 主控台應用程式，它是用來處理即時影片分析和您自訂 AI 之間傳送的 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 訊息。 以下是即時影片分析和 gRPC 推斷伺服器之間的訊息流程：

1. Live Video Analytics 會傳送媒體資料流程描述項 ([請) ](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) 參閱定義 gRPC 串流會話的 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 訊息，以定義將于影片畫面後面傳送的媒體串流資訊。 
1. 伺服器會驗證並確認資料流程描述元，並設定所需的資料傳輸方法。
1. 即時影片分析接著會開始傳送包含影片畫面的 MediaSample 檔案。
1. 伺服器會在接收時分析影片畫面，並使用您所定義的映射處理器來開始處理它們。
1. 伺服器接著會在 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 訊息可供使用時，傳回推斷結果。 

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/grpc-external-srv.png" alt-text="建立 gRPC 推斷伺服器":::

影片畫面可透過 [共用記憶體](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) 傳送，也可以內嵌在 protobuf 訊息中。 您可以在 LVA 圖形拓撲中設定資料傳輸模式，以決定畫面的傳輸方式。 這可透過設定 MediaGraphGrpcExtension 屬性的 **dataTransfer** 元素來達成，如下所示：

嵌入式：

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

共用記憶體：

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> 透過共用記憶體進行通訊時，IpcMode 的值應該設定為可 **共用** ，而在 gRPC 伺服器模組中，將 IpcMode 的值設定為 **container： {CONTAINER_NAME}**。 這些設定是在用來將模組部署至 Azure IoT 中樞的部署資訊清單檔案中進行。 以下是設定 IoT Edge 模組時要使用的容器選項範例。

即時影片分析課程模組：

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

gRPC 延伸模組：

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:lvaEdge"
    }
}
```

> [!NOTE]
> 確定您可以存取 grpcExtension 內 "container： lvaEdge" 的共用記憶體區域。

## <a name="sample-grpc-server"></a>範例 gRPC 伺服器

為了瞭解如何開發 gRPC 伺服器的詳細資料，讓我們來看看我們的程式碼範例。

1. 從 GitHub 連結複製存放庫 [https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) 。
1. 啟動 VSCode，並流覽至/src/edge/modules/grpcExtension 資料夾。
1. 讓我們快速逐步解說這些檔案：

    1. **Program.cs**：這是應用程式的進入點。 它負責初始化和管理 gRPC 伺服器，這將作為主機。 在我們的範例中，用來接聽來自 gRPC 用戶端之傳入 gRPC 訊息的埠 (例如) 的即時影片分析，是由 AppConfig.js中的 grpcBindings configuration 元素所指定。
    
        ```json    
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\MediaGraphExtensionService.cs**：這個類別負責處理 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 訊息。 它會讀取訊息中的畫面格，叫用 ImageProcessor 並寫入推斷結果。
既然我們已設定並初始化 gRPC 伺服器埠連線，現在讓我們來看看如何處理傳入的 gRPC 訊息。

        * 一旦建立 gRPC 會話，gRPC 伺服器將從用戶端收到的第一則訊息 (即時影片分析) 是在 [擴充的 proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) 檔中定義的 MediaStreamDescriptor。 

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        * 在我們的伺服器執行中，此方法 `ProcessMediaStreamDescriptor` 會驗證 MediaStreamDescriptor 的影片檔案 MediaDescriptor 屬性，然後根據您在拓撲中指定的內容和使用的部署範本檔案，設定資料傳輸模式 (使用共用記憶體或使用內嵌框架傳輸模式) 。 
        * 在接收訊息並成功設定資料傳輸模式之後，gRPC 伺服器會接著將 MediaStreamDescriptor 訊息傳回給用戶端作為確認，並藉此建立伺服器與用戶端之間的連接。    
        * 即時影片分析收到通知之後，就會開始將媒體串流傳送至 gRPC 伺服器。 在我們的伺服器執行中，此方法 `ProcessMediaStream` 將會處理傳入的 MediaStreamMessage。 MediaStreamMessage 也定義于 [extension. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)中。

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.
            
            
              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        * 根據 Appconfig.js中的 batchSize 值而定，我們的伺服器將會繼續接收訊息，並將影片框架儲存在清單中。 一旦達到 batchSize 限制，函式就會呼叫將處理影像的函式或檔案。 在我們的案例中，此方法會呼叫名為 BatchImageProcessor.cs 的檔案。
    1. **Processors\BatchImageProcessor.cs**：這個類別負責處理 (s) 的影像。 在此範例中，我們使用了影像分類模型。 針對將處理的每個影像，使用的演算法如下：

        1. 轉換位元組陣列中的影像以進行處理。 請參閱方法： `GetBytes(Bitmap image)`
        
            我們所使用的範例處理器僅支援 JPG 編碼的影像框架，而不支援以像素格式。 如果您的自訂處理器支援不同的編碼和/或格式，請更新 `IsMediaFormatSupported` processor 類別的方法。
        1. 使用 [ColorMatrix 類別](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1)，將影像轉換成灰色縮放。 請參閱方法： `ToGrayScale(Image source)` 。
        1. 當我們取得灰色縮放影像之後，就會計算灰色尺規位元組的平均值。
        1. 如果平均值 < 127，則會將影像分類為 "深色"，否則我們會將其分類為 "light"，並將信賴價值視為1.0。 請參閱方法： `ProcessImage(List<Image> images)` 。

    您可以藉由修改這個類別，或藉由新增類別並執行方法，來新增自己的處理器邏輯：

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images) 
    ```

    新增類別之後，您必須更新 MediaGraphExtensionService.cs，讓它具現化您的類別，並在其上叫用 ProcessImage 方法，以執行處理邏輯。 

## <a name="connect-with-live-video-analytics-module"></a>使用即時影片分析模組連接

現在您已建立 gRPC 延伸模組，我們現在會建立並部署 media graph 拓撲。

1. 使用 Visual Studio Code，依照[這些指示](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) 來登入 Docker。
1. 在 Visual Studio Code 中，移至 src/edge。 您會看到 .env 檔案和一些部署範本檔案。

    部署範本會參考邊緣裝置的部署資訊清單。 其包含一些預留位置值。 .env 檔案包含這些變數的值。
    
    然後選取 [組建和推送 IoT Edge 解決方案]。 使用 src/edge/deployment.grpc.template.json 進行此步驟。
        
    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/build-push-iot-edge-solution.png" alt-text="使用即時影片分析模組連接":::
    
    此動作會建立 grpc 伺服器模組，並將映射推送至您的 Azure Container Registry。
    檢查您是否已在 .env 檔案中定義 CONTAINER_REGISTRY_USERNAME_myacr 和 CONTAINER_REGISTRY_PASSWORD_myacr 環境變數。
1. 移至 src/cloud-to-device-console-app 資料夾。 在這裡，您會看到 appsettings.json 檔案和其他幾個檔案：

    * c2d-console-app.csproj - Visual Studio Code 的專案檔。
    * operations.json - 您想要讓程式執行的作業清單。
    * Program.cs - 範例程式碼。 此程式碼：

        * 載入應用程式設定。
        * 叫用 IoT Edge 模組上 Live Video Analytics 所公開的直接方法。 您可以使用模組，藉由叫用其[直接方法](direct-methods.md)來分析即時影片串流。
        * 會暫停，讓您可以在 [終端機] 視窗中檢查程式的輸出，並在 [輸出] 視窗中檢查模組所產生的事件。
        * 叫用直接方法來清除資源。
1. 編輯 operations.json 檔案：

    * 變更圖表拓撲的連結：

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json`
        * 在 GraphInstanceSet 底下，編輯圖表拓撲的名稱，使其符合上述連結中的值：<br/>`"topologyName": "InferencingWithGrpcExtension"`
        * 在 GraphTopologyDelete 底下，編輯名稱：<br/>`"name": "InferencingWithGrpcExtension"`

            例如，拓撲 (， `https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtension/topology.json`) 必須定義延伸位址：
    * 擴充位址參數

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC LVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * 組態

        ```
        {
            "@apiVersion": "1.0",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "5"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```
    
## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>產生和部署 IoT Edge 部署資訊清單

部署資訊清單會定義要將部署至邊緣裝置的模組，以及這些模組的組態設定。 請遵循下列步驟，從範本檔案產生資訊清單，然後將其部署到邊緣裝置。
此步驟會在的 src/Edge/config/deployment.grpc.amd64.js建立 IoT Edge 部署資訊清單。 以滑鼠右鍵按一下該檔案，然後選取 [ **建立單一裝置的部署**]。

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/create-deployment-single-device.png" alt-text="產生及部署 IoT Edge 部署資訊清單":::

接下來，Visual Studio Code 會要求您選取 IoT 中樞裝置。 選取您的 IoT Edge 裝置 (應該是 lva-sample-device)。
在這個階段，已啟動將邊緣模組部署到您的 IoT Edge 裝置。 在大約 30 秒後，重新整理 Visual Studio Code 左下方區段中的 Azure IoT 中樞。 您應該會看到新的模組已部署為 lvaExtension。

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/devices.png" alt-text="已部署名為 lvaExtension 的新模組":::

## <a name="next-steps"></a>後續步驟

遵循 [使用模型快速入門分析即時影片](use-your-model-quickstart.md)中所述的「**準備」監視事件** 步驟，以執行範例並解讀結果。 此外，請查看我們的範例 gRPC 拓撲： [gRPCExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtension/topology.json)、 [CVRWithGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json)、[EVRtoAssetsByGrpcExtension 和 [EVROnMotionPlusGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-with-grpcExtension/topology.json)。