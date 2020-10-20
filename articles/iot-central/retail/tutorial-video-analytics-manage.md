---
title: 教學課程 - 使用 Azure IoT Central 影片分析 - 物件和動作偵測應用程式範本來監視影片
description: 本教學課程說明如何使用 [影片分析 - 物件和動作偵測] 應用程式範本中的儀表板來管理相機並監視影片。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 1c1ddeb8cafd8aa7584da48a715139c5d12eeb10
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874793"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>教學課程：監視和管理 [影片分析 - 物件和動作偵測] 應用程式

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 將物件和動作偵測相機新增至您的 IoT Central 應用程式。
> * 管理您的影片串流，並在偵測到感興趣的事件時加以播放。

## <a name="prerequisites"></a>先決條件

開始之前，您應該先完成：

* 先前的[在 Azure IoT Central 中建立即時影片分析應用程式](./tutorial-video-analytics-create-app-yolo-v3.md)或[在 Azure IoT Central 中建立影片分析 (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) 教學課程。
* 其中一個先前的教學課程：[建立適用於即時影片分析的 IoT Edge 執行個體 (Linux VM)](tutorial-video-analytics-iot-edge-vm.md) 或[建立適用於即時影片分析的 IoT Edge 執行個體 (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md)。

您應該已在本機電腦上安裝 [Docker](https://www.docker.com/products/docker-desktop)，以執行影片檢視器應用程式。

## <a name="add-an-object-detection-camera"></a>新增物件偵測相機

在您的 IoT Central 應用程式中，瀏覽至您先前建立的 **LVA Gateway 001** 裝置。 然後選取 [命令] 索引標籤。

使用下表中的值作為 **Add Camera Request**命令的參數值。 資料表中顯示的值是假設您使用的是 Azure VM 中的模擬相機，如果您使用真正的相機，請適當地調整這些值：

| 欄位| 描述| 範例值|
|---------|---------|---------|
| 相機識別碼      | 用於佈建的裝置識別碼 | camera-003 |
| 相機名稱    | 易記名稱           | 物件偵測相機 |
| RTSP Url       | 串流的位址   | RTSP://10.0.0.4:554/media/camera-300s.mkv|
| RTSP 使用者名稱  |                         | user    |
| RTSP 密碼  |                         | 密碼    |
| 偵測類型 | 下拉式清單                | 物件偵測       |

選取 [執行] 以新增相機裝置：

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="新增相機":::

> [!NOTE]
> 應用程式中已有 **LVA Edge 物件偵測器**裝置範本。

## <a name="add-a-motion-detection-camera-optional"></a>新增動作偵測相機 (選擇性)

如果您將兩個相機連線到 IoT Edge 閘道裝置，請重複上述步驟，將動作偵測相機新增至應用程式。 請在**相機識別碼**、**相機名稱**及 **RTSP Url** 參數中使用不同的值。

## <a name="view-the-downstream-devices"></a>檢視下游裝置

選取 **LVA Gateway 001** 裝置的 [下游裝置] 索引標籤，以查看您剛新增的相機裝置：

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="新增相機":::

在應用程式的 [裝置] 頁面上，相機裝置也會出現在其中的清單中。

## <a name="configure-and-manage-the-camera"></a>設定和管理相機

瀏覽至 **camera-003** 裝置，然後選取 [管理] 索引標籤。

請使用預設值，或如果您需要自訂裝置屬性，請加以修改：

**照相機設定**

| 屬性 | 說明 | 建議的值 |
|-|-|-|
| 影片播放主機 | 「Azure 媒體播放器」檢視器的主機 | http://localhost:8094 |

**LVA 作業和診斷**

| 屬性 | 說明 | 建議的值 |
|-|-|-|
| 自動啟動 | 在 LVA 閘道重新啟動時起始物件偵測 | 已核取 |
| 遙測偵錯 | 事件追蹤 | 選用 |
|推斷超時 (秒)| 用來判斷推斷已停止的時間量 | 20 |

**AI 物件偵測**

| 屬性 | 說明 | 建議的值 |
|-|-|-|
| 推斷擷取時間上限 (秒) | 擷取時間上限 | 15 |
| 偵測類別 | 包含偵測標記的字串 (以逗號分隔)。 如需詳細資訊，請參閱[支援的標記清單](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt) | truck, bus |
| 信賴閾值 | 判斷物件偵測是否有效的限定百分比 | 70 |
| 推斷畫面採樣速率 (fps) | [在此描述] | 2 |

選取 [儲存]。

幾秒鐘後，您會看到每個設定的 [已接受] 確認訊息：

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="新增相機":::

## <a name="start-lva-processing"></a>開始 LVA 處理

瀏覽至 **camera-003** 裝置，然後選取 [命令] 索引標籤。

執行**開始 LVA 處理**命令。

當命令完成時，請檢視命令歷程記錄來確定沒有任何錯誤：

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="新增相機":::

## <a name="monitor-the-cameras"></a>監視相機

瀏覽至 **camera-003** 裝置，然後選取 [儀表板] 索引標籤：

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="新增相機":::

[偵測計數] 磚會顯示在一秒的偵測間隔中，每個所選偵測類別物件的平均偵測計數。

[偵測類別] 圓形圖會顯示每個類別類型的偵測項目百分比。

**推斷事件影片**是 Azure 媒體服務中包含偵測的資產連結清單。 此連結會使用下一節中所述的主機播放機。

## <a name="start-the-streaming-endpoint"></a>啟動串流端點

啟動媒體服務端點，讓您的用戶端媒體播放器應用程式串流錄製的影片：

* 在 Azure 入口網站中，瀏覽至 **lva-rg** 資源群組。
* 開啟**串流端點**資源。
* 在 [串流端點詳細資料] 頁面上，選取 [開始]。 您會看到一則警告，指出端點啟動時就會開始計費。

## <a name="view-stored-video"></a>觀看儲存的影片

監看相機和回應可疑影像的天數都已結束。 透過自動事件標記及已儲存影片 (具推斷偵測) 的直接連結，安全性操作員可以在清單中尋找感興趣的事件，然後依照連結來觀看影片。

您可以使用 [AMP 影片播放器](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer)來觀看儲存在 Azure 媒體服務帳戶中的影片。

IoT Central 應用程式會將影片儲存在 Azure 媒體服務中，讓您可以從中進行串流。 您需要影片播放器來播放儲存在 Azure 媒體服務中的影片。

請確定 **Docker** 正在本機電腦上執行。

開啟命令提示字元，並使用下列命令在本機電腦上的 Docker 容器中執行影片播放器。 將命令中的預留位置取代為您先前在 scratchpad.txt 檔案中所記下的值。 在為媒體服務帳戶建立服務主體時，您已記下 `amsAadClientId`、`amsAadSecret`、`amsAadTenantId`、`amsSubscriptionId` 和 `amsAccountName`：

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Docker 參數 | AMS API 存取 (JSON)|
|-|-|
|amsAadClientId| AadClientId|
|amsAadSecret| AadSecret |
|amsAadTenantId| AadTenantId |
|amsSubscriptionId| SubscriptionId|
|amsResourceGroup| ResourceGroup |
|amsAccountName| AccountName|

瀏覽至 **camera-003** 裝置，然後選取 [儀表板] 索引標籤。然後在 [推斷事件影片] 磚上，按一下其中一個已擷取的物件偵測超連結。 影片會出現在本機影片播放器所顯示的頁面上：

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="新增相機":::

## <a name="change-the-simulated-devices-in-application-dashboards"></a>變更應用程式儀表板中的模擬裝置

應用程式儀表板最初會填入從 IoT Central 模擬裝置中產生的遙測和屬性。 若要將磚設定為來自真實相機或 Live555 模擬器的遙測，請遵循下列步驟：

1. 瀏覽至 [(範例) 真實相機監視器] 應用程式儀表板。
1. 選取 [編輯]  。
1. 選取 [便箋] 圖格並加以刪除。
1. 將儀表板標題變更為*真實相機監視器*。
1. 在 [推斷計數] 磚上，選取 [設定] 圖示。
1. 在 [設定圖表] 區段中，選取 **LVA Edge 物件偵測器**裝置群組中的一個或多個真實相機。
1. 選取 `AI Inference Interface/Inference Count` 遙測欄位。
1. 選取 [更新]。

1. 對下列幾個磚重複前述步驟：
    1. **偵測**圓形圖會使用 `AI Inference Interface/Inference/entity/tag/value` 遙測類型。
    1. **推斷**會使用 `AI Inference Interface/Inference/entity/tag/value` 上一個已知值。
    1. **信賴度 %** 會使用 `AI Inference Interface/Inference/entity/tag/confidence` 上一個已知值。
    1. **快照集**會使用顯示為影像的 `AI Inference Interface/Inference Image`。
    1. **推斷事件影片**會使用顯示為連結的 `AI Inference Interface/Inference Event Video`。
1. 選取 [儲存]。

**真實相機監視器**儀表板現在會顯示真實相機裝置的值：

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="新增相機":::

## <a name="pause-processing"></a>暫停處理

您可以在應用程式中暫停即時影片分析處理：

1. 在您的應用程式中，瀏覽至物件偵測相機的 [命令] 頁面。 然後執行 **Stop LVA Processing**命令。

1. 停止媒體服務帳戶的串流端點：
    * 在 Azure 入口網站中，瀏覽至 **lva-rg** 資源群組。
    * 按一下 [串流端點] 資源。
    * 在 [串流端點詳細資料] 頁面上，選取 [停止]。

## <a name="tidy-up"></a>整理一下

如果您已完成應用程式，您可以移除您建立的所有資源，如下所示：

1. 在 IoT Central 應用程式中，劉覽至 [管理] 區段中的 [您的應用程式] 頁面。 然後選取 [刪除]。
1. 在 Azure 入口網站中，刪除 **lva-rg** 資源群組。
1. 在您的本機電腦上，停止**amp-viewer** Docker 容器。

## <a name="next-steps"></a>後續步驟

您現在已了解如何將相機新增至 IoT Central 應用程式，並針對物件和動作偵測來進行設定。

若要了解如何自訂 IoT Edge 模組的原始程式碼：

> [!div class="nextstepaction"]
> [修改並建置即時影片分析閘道模組](./tutorial-video-analytics-build-module.md)
