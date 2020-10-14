---
ms.openlocfilehash: 6732fe364ba67bb2c4ea8fb2543c576166f8a110
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91829197"
---
### <a name="examine-and-edit-the-sample-files"></a>查看和編輯範例檔案

您必須將範例程式碼下載至資料夾，這是必要條件的一部分。 請遵循下列步驟來檢查和編輯範例檔案。

1. 在 Visual Studio Code 中，移至 *src/edge*。 您會看到 *.env* 檔案和一些部署範本檔案。

    部署範本會參考邊緣裝置的部署資訊清單。 其包含一些預留位置值。 *.env* 檔案包含這些變數的值。
1. 移至 *src/cloud-to-device-console-app* 資料夾。 在這裡，您會看到 *appsettings.json* 檔案和其他幾個檔案：

    * ***c2d-console-app.csproj*** - Visual Studio Code 的專案檔。
    * ***operations.json*** - 您想要讓程式執行的作業清單。
    * ***Program.cs*** - 範例程式碼。 此程式碼：

        * 載入應用程式設定。
        * 叫用 IoT Edge 模組上 Live Video Analytics 所公開的直接方法。 您可以使用模組，藉由叫用其[直接方法](../../../direct-methods.md)來分析即時影片串流。
        * 會暫停，讓您可以在 [終端機] 視窗中檢查程式的輸出，並在 [輸出] 視窗中檢查模組所產生的事件。
        * 叫用直接方法來清除資源。
1. 編輯 *operations.json* 檔案：
    * 變更圖表拓撲的連結：

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`

    * 在 `GraphInstanceSet` 底下，編輯圖表拓撲的名稱，使其符合前一個連結中的值：

      `"topologyName" : "InferencingWithHttpExtension"`

    * 在 `GraphTopologyDelete` 底下，編輯名稱：

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>產生和部署 IoT Edge 部署資訊清單

1. 以滑鼠右鍵按一下 *src/edge/ deployment.yolov3.template.json* 檔案，然後選取 [產生 IoT Edge 部署資訊清單]。

    ![產生 IoT Edge 部署資訊清單](../../../media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    *deployment.yolov3.amd64.json* 資訊清單檔案會建立在 *src/edge/config* 資料夾中。
1. 如果您已完成[偵測動作並發出事件](../../../detect-motion-emit-events-quickstart.md)快速入門，請略過此步驟。 

    否則，請在左下角的 [Azure IoT 中樞] 窗格附近，選取 [其他動作] 圖示，然後選取 [設定 IoT 中樞連接字串]。 您可以從 *appsettings.json* 檔案複製字串。 或者，若要確保您已在 Visual Studio Code 中設定適當的 IoT 中樞，請使用[選取 IoT 中樞命令](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)。
    
    ![設定 IoT 中樞連接字串](../../../media/quickstarts/set-iotconnection-string.png)
1. 以滑鼠右鍵按一下 *src/edge/config/ deployment.yolov3.amd64.json*，然後選取 [建立單一裝置的部署]。 

    ![建立單一裝置的部署](../../../media/quickstarts/create-deployment-single-device.png)
1. 當系統提示您選取 IoT 中樞裝置時，請選取 [lva-sample-device]。
1. 大約 30 秒之後，請在視窗左下角重新整理 Azure IoT 中樞。 邊緣裝置現在會顯示下列已部署的模組：

    * 名為 `lvaEdge` 的即時影片分析模組。
    * 名為 `rtspsim` 的模組，其會模擬 RTSP 伺服器並作為即時影片摘要的來源。

        > [!NOTE]
        > 如果您使用自己的邊緣裝置，而不是我們的設定指令碼所佈建的裝置，請移至您的邊緣裝置，並以**管理員權限**執行下列命令，以提取並儲存用於本快速入門的範例影片檔案：  
        
        ```
        mkdir /home/lvaadmin/samples
        mkdir /home/lvaadmin/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
        chown -R lvaadmin /home/lvaadmin/samples/  
        ```
        * `yolov3` 模組，這是 YoloV3 物件偵測模型，會將電腦視覺套用至映像並傳回多個物件類型類別
 
      ![部署在邊緣裝置中的模組](../../../media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>準備監視事件

1. 在 Visual Studio Code 中，開啟 [延伸模組] 索引標籤 (或按 Ctrl+Shift+X) 並搜尋 Azure IoT 中樞。
1. 按一下滑鼠右鍵，然後選取 [延伸模組設定]。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="延伸模組設定":::
1. 搜尋並啟用「顯示詳細資訊訊息」。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="延伸模組設定":::
1. 以滑鼠右鍵按一下 Live Video Analytics 裝置，然後選取 [開始監視內建事件端點]。 您需要執行此步驟，才能在 Visual Studio Code 的 [輸出] 視窗中監視 IoT 中樞事件。 

   ![開始監視](../../../media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>執行範例程式

1. 若要啟動偵錯工作階段，請選取 F5 鍵。 您會在 [終端機] 視窗中看到一些列印的訊息。
1. *operations.json* 程式碼首先會呼叫直接方法 `GraphTopologyList` 和 `GraphInstanceList`。 如果您在完成先前的快速入門之後清除了資源，則此程序會傳回空的清單，然後暫停。 若要繼續，請選取 Enter 鍵。

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

    [終端機] 視窗會顯示下一組直接方法呼叫：

     * 會使用上述 `topologyUrl` 的對 `GraphTopologySet` 呼叫
     * 會使用下列主體的對 `GraphInstanceSet` 呼叫：

         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithHttpExtension",
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
