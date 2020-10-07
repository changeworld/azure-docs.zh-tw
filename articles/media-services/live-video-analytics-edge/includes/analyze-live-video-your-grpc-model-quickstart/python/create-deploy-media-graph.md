---
ms.openlocfilehash: f2724a0ea0aa5f609be5847652973cfa03658c24
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89421508"
---
### <a name="examine-and-edit-the-sample-files"></a>查看和編輯範例檔案

您必須將範例程式碼下載至資料夾，這是必要條件的一部分。 請遵循下列步驟來檢查和編輯範例檔案。

1. 在 Visual Studio Code 中，移至 *src/edge*。 您會看到 *.env* 檔案和一些部署範本檔案。

    deployment.grpcyolov3icpu.template.json 會參考邊緣裝置的部署資訊清單。 其包含一些預留位置值。 .env 檔案包含這些變數的值。
1. 移至 *src/cloud-to-device-console-app* 資料夾。 在這裡，您會看到 *appsettings.json* 檔案和其他幾個檔案：
    
    * operations.json - 您想要讓程式執行的作業清單。
    * main.py - 範例程式碼。 此程式碼：

        * 載入應用程式設定。
        * 叫用 IoT Edge 模組上 Live Video Analytics 所公開的直接方法。 您可以使用模組，藉由叫用其直接方法來分析即時影片串流。
        * 會暫停，讓您可以在 [終端機] 視窗中檢查程式的輸出，並在 [輸出] 視窗中檢查模組所產生的事件。
        * 叫用直接方法來清除資源。
1. 編輯 *operations.json* 檔案：
 
    * 變更圖表拓撲的連結：
    * `"topologyUrl"` : `"https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json"`
    * 在 GraphInstanceSet 底下，編輯圖表拓撲的名稱，使其符合上述連結中的值：
    * `"topologyName"` : `"InferencingWithGrpcExtension"`
    * 在 GraphTopologyDelete 底下，編輯名稱：
    * `"name"` : `"InferencingWithGrpcExtension"`

> [!NOTE]
> <p>
> <details>
> <summary>展開此項，並查看拓撲中的 MediaGraphGrpcExtension 節點如何實作</summary>
> <pre><code>
> {
>   "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
>   "name": "grpcExtension",
>   "endpoint": {
>       "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
>       "url": "${grpcExtensionAddress}",
>       "credentials": {
>           "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
>           "username": "${grpcExtensionUserName}",
>           "password": "${grpcExtensionPassword}"
>       }
>   },
>   "dataTransfer": {
>       "mode": "sharedMemory",
>       "SharedMemorySizeMiB": "5"
>   },
>   "image": {
>       "scale": {
>           "mode": "${imageScaleMode}",
>           "width": "${frameWidth}",
>           "height": "${frameHeight}"
>       },
>       "format": {
>           "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
>           "encoding": "${imageEncoding}",
>           "quality": "${imageQuality}"
>       }
>   },
>   "inputs": [
>       {
>           "nodeName": "motionDetection"
>       }
>   ]
> }          
> </code></pre>
> </details>    
> </p>
    
### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>產生和部署 IoT Edge 部署資訊清單

1. 以滑鼠右鍵按一下 *src/edge/* *deployment.grpcyolov3icpu.template.json* 檔案，然後選取 [產生 IoT Edge 部署資訊清單]。

    ![產生 IoT Edge 部署資訊清單](../../../media/quickstarts/generate-iot-edge-deployment-manifest-grpc.png)

    *deployment.grpcyolov3icpu.amd64.json* 資訊清單檔案會建立在 *src/edge/config* 資料夾中。
1. 如果您已完成[偵測動作並發出事件](../../../detect-motion-emit-events-quickstart.md)快速入門，請略過此步驟。

    否則，請在左下角的 [Azure IoT 中樞] 窗格附近，選取 [其他動作] 圖示，然後選取 [設定 IoT 中樞連接字串]。 您可以從 *appsettings.json* 檔案複製字串。 或者，若要確保您已在 Visual Studio Code 中設定適當的 IoT 中樞，請使用[選取 IoT 中樞命令](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)。

    ![IoT 中樞連接字串](../../../media/quickstarts/iot-hub-connection-string-grpc.png)
1. 以滑鼠右鍵按一下 *src/edge/config/* *deployment.grpcyolov3icpu.amd64.json*，然後選取 [建立單一裝置的部署]。

    ![建立部署單一裝置](../../../media/quickstarts/create-deployment-single-device-grpc.png)
1. 當系統提示您選取 IoT 中樞裝置時，請選取 [lva-sample-device]。
1. 大約 30 秒之後，請在視窗左下角重新整理 Azure IoT 中樞。 邊緣裝置現在會顯示下列已部署的模組：

    * 名為 **lvaEdge** 的即時影片分析模組。
    * 名為 **rtspsim** 的模組，其會模擬 RTSP 伺服器並作為即時影片摘要的來源。

        > [!NOTE]
        > 如果您使用自己的邊緣裝置，而不是我們的設定指令碼所佈建的裝置，請移至您的邊緣裝置，並以**管理員權限**執行下列命令，以提取並儲存用於本快速入門的範例影片檔案：  

        ```
        mkdir /home/lvaadmin/samples
        mkdir /home/lvaadmin/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
        chown -R lvaadmin /home/lvaadmin/samples/  
        ```
    * **lvaExtension** 模組，這是使用 gRPC 作為通訊方法的 YOLOv3 物件偵測模型，並且會將電腦視覺套用至影像，並傳回多個類別的物件類型。
    
    ![lva 擴充](../../../media/quickstarts/lvaextension-grpc.png)

### <a name="prepare-to-monitor-events"></a>準備監視事件

以滑鼠右鍵按一下 Live Video Analytics 裝置，然後選取 [開始監視內建事件端點]。 您需要執行此步驟，才能在 Visual Studio Code 的 [輸出] 視窗中監視 IoT 中樞事件。

![開始監視](../../../media/quickstarts/start-monitoring-built-event-endpoint-grpc.png)

### <a name="run-the-sample-program"></a>執行範例程式

1. 若要啟動偵錯工作階段，請選取 F5 鍵。 您會在 [終端機] 視窗中看到一些列印的訊息。
1. *operations.json* 程式碼首先會呼叫直接方法 `GraphTopologyList` 和 `GraphInstanceList`。 如果您在完成先前的快速入門之後清除了資源，則此程序會傳回空的清單，然後暫停。 若要繼續，請選取 Enter 鍵。
    
    ```
    -------------------------------Executing operation GraphTopologyList-----------------------  
    Request: GraphTopologyList  --------------------------------------------------
    {
    "@apiVersion": "1.0"
    }
    ---------------  
    Response: GraphTopologyList - Status: 200  ---------------
    {
    "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    
    [終端機] 視窗會顯示下一組直接方法呼叫：
    
    * 呼叫使用上述 topologyUrl 的 `GraphTopologySet`
    * 會使用下列主體的對 `GraphInstanceSet` 呼叫：
    
    ```
    {
      "@apiVersion": "1.0",
      "name": "Sample-Graph-1",
      "properties": {
        "topologyName": "InferencingWithGrpcExtension",
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
    
    * 呼叫 `GraphInstanceActivate` 以啟動圖表執行個體和影片流程。
    * 第二次呼叫 `GraphInstanceList` 以顯示圖表執行個體處於執行中狀態。
1. [終端機] 視窗中的輸出會在出現「按 Enter 繼續」提示時暫停。 還不要選取 Enter 鍵。 請向上捲動，查看您所叫用直接方法的 JSON 回應承載。
1. 切換至 Visual Studio Code 中的 [輸出] 視窗。 您會看到訊息指出 IoT Edge 模組上的 Live Video Analytics 正在傳送到 IoT 中樞。 本快速入門的下一節會討論這些訊息。
1. 媒體圖表會繼續執行並列印結果。 RTSP 模擬器會持續循環播放來源影片。 若要停止媒體圖表，請返回 [終端機] 視窗，然後選取 Enter 鍵。
1. 下一系列的呼叫會清除資源：
    
    * 對 `GraphInstanceDeactivate` 的呼叫會停用圖表執行個體。
    * 對 `GraphInstanceDelete` 的呼叫會刪除執行個體。
    * 對 `GraphTopologyDelete` 的呼叫會刪除拓撲。
    * 最後對 `GraphTopologyList` 的呼叫會顯示清單是空的。

