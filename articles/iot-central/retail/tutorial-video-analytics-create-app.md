---
title: 教學課程 - 在 Azure IoT Central 中建立影片分析 - 物件和動作偵測應用程式
description: 本教學課程說明如何在 IoT Central 中建立影片分析應用程式。 您可以建立及自訂應用程式，並將其連線到其他 Azure 服務。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 07/31/2020
ms.openlocfilehash: c7ddff70d24fe87380c4bf1439811042418ac76a
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369564"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central"></a>教學課程：在 Azure IoT Central 中建立影片分析 - 物件和動作偵測應用程式

以解決方案建立者的身份，了解如何使用 IoT Central 的「影片分析 - 物件和動作偵測」應用程式範本、Azure IoT Edge 裝置和 Azure 媒體服務，來建立影片分析應用程式。 此解決方案使用零售商店來示範如何符合一般商務在監視安全性相機上的需求。 解決方案會在影片摘要中使用自動物件偵測，以快速識別並尋找感興趣的事件。

範例應用程式包含兩個模擬裝置和一個 IoT Edge 閘道。 下列教學課程會示範兩種試驗和了解閘道功能的方法：

* 在 Azure VM 中建立 IoT Edge 閘道，並與模擬相機連線。
* 在真實的裝置 (例如 Intel NUC) 上建立 IoT Edge 閘道，並連接真實的相機。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 使用 Azure IoT Central 的影片分析應用程式範本來建立零售商店應用程式
> * 自訂應用程式設定
> * 為 IoT Edge 閘道裝置建立裝置範本
> * 將閘道裝置新增至 IoT Central 應用程式

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程系列，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，則可以在 [Azure 註冊頁面](https://aka.ms/createazuresubscription)上建立一個。
* 如果您使用的是真實的相機，您需要連接 IoT Edge 裝置與攝影機，而且需要**即時串流通訊協定**通道。

## <a name="initial-setup"></a>初始設定

在這些教學課程中，您會更新及使用數個設定檔。 這些檔案的初始版本可在 [LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) GitHub 存放庫中取得。 此存放庫也包含可供您下載的便籤文字檔，可讓您用來記錄所部署服務的設定值。

在您的本機電腦上建立名為 lva-configuration 的資料夾，以儲存這些檔案的副本。 然後以滑鼠右鍵按一下下列每個連結，然後選擇 [另存新檔]，將檔案儲存至 lva-configuration 資料夾：

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> GitHub 存放庫也包含 **LvaEdgeGatewayModule** 和 **lvaYolov3** IoT Edge 模組的原始程式碼。 如需有關使用原始程式碼的詳細資訊，請參閱[建立 LVA 閘道模組](tutorial-video-analytics-build-module.md)。

## <a name="deploy-and-configure-azure-media-services"></a>部署和設定 Azure 媒體服務

解決方案會使用 Azure 媒體服務帳戶來儲存 IoT Edge 閘道裝置所執行的物件偵測影片剪輯。

當您建立媒體服務帳戶時：

- 您必須提供帳戶名稱、Azure 訂用帳戶、位置、資源群組和儲存體帳戶。 在建立媒體服務帳戶時，使用預設設定來建立新的儲存體帳戶。

- 選擇 [美國東部] 區域作為位置。

- 在 [美國東部] 區域中，為媒體服務和儲存體帳戶建立名為 lva-rg 的新資源群組。 當您完成教學課程時，您可以藉由刪除 lva-rg 資源群組來輕鬆移除所有資源。

[在 Azure 入口網站中建立媒體服務帳戶](https://portal.azure.com/?r=1#create/Microsoft.MediaService)。

> [!TIP]
> 這些教學課程會在所有範例中使用 [美國東部] 區域。 您也可以使用最接近您的區域。

記下 scratchpad.txt 檔案中的**媒體服務**帳戶名稱。

當部署完成時，瀏覽至**媒體服務**帳戶的 [屬性] 頁面。 記下 scratchpad.txt 檔案中的**資源識別碼**，稍後設定 IoT Edge 模組時會用到此值。

接下來，為您的媒體服務資源設定 Azure Active Directory 服務主體。 選取 [API 存取]，然後選取 [服務主體驗證]。 使用與您媒體服務資源相同的名稱建立新的 Azure Active Directory 應用程式，並以「IoT Edge 存取」作為描述來建立祕密。

:::image type="content" source="./media/tutorial-video-analytics-create-app/media-service-authentication.png" alt-text="設定適用於 AMS 的 AAD 應用程式":::

產生秘密時，向下捲動至「**複製您的認證以連結您的服務主體應用程式**」區段。 然後選取 [JSON]。 您可以在這裡一次複製所有認證資訊。 記下 scratchpad.txt 檔案中的此資訊，稍後您設定 IoT Edge 裝置時會用到。

> [!WARNING]
> 這是您唯一可以檢視和儲存秘密的機會。 如果遺失了，您必須產生另一個秘密。

## <a name="create-the-azure-iot-central-application"></a>建立 Azure IoT Central 應用程式

在本節中，您會從範本建立新的 Azure IoT Central 應用程式。 您會在整個教學課程系列中，使用此應用程式來建置完整的解決方案。

若要建立新的 Azure IoT Central 應用程式：

1. 瀏覽至 [Azure IoT Central 的應用程式管理員](https://aka.ms/iotcentral)網站。

1. 以存取您 Azure 訂用帳戶時所用的認證來登入。

1. 若要開始建立新的 Azure IoT Central 應用程式，請在 [建置] 頁面上選取 [新增應用程式]。

1. 選取 [零售]  。 [零售] 頁面會顯示數個零售應用程式範本。

若要建立新的影片分析應用程式：

1. 選取 [影片分析 - 物件和動作偵測] 應用程式範本。 針對本教學課程中使用的裝置，其裝置範本皆包含在此範本中。 此範本包含範例儀表板，可供操作員用來執行監視和管理相機等工作。

1. (選擇性) 選擇易記的 [應用程式名稱]  。 此應用程式會以名為 Northwind Traders 的虛構零售商店為基礎。 本教學課程會使用「Northwind Traders 影片分析」作為**應用程式名稱**。

    > [!NOTE]
    > 如果您使用自訂的**應用程式名稱**，您仍然必須針對應用程式的 **URL** 使用唯一值。

1. 如果您有 Azure 訂用帳戶，請選取您的 [目錄]、[Azure 訂用帳戶]，並以 [美國] 作為 [位置]。 如果您沒有訂用帳戶，則可以啟用 [7 天免費試用]  並填妥必要的連絡人資訊。 本教學課程使用三部裝置 (兩部相機和一部 IoT Edge 裝置)，因此，如果您未使用免費試用版，將會依使用量計費。

    如需關於目錄、訂用帳戶和位置的詳細資訊，請參閱[建立應用程式快速入門](../core/quick-deploy-iot-central.md)。

1. 選取 [建立]。

    :::image type="content" source="./media/tutorial-video-analytics-create-app/new-application.png" alt-text="設定適用於 AMS 的 AAD 應用程式":::

### <a name="retrieve-the-configuration-data"></a>擷取設定資料

稍後在本教學課程中設定 IoT Edge 閘道時，您會需要來自 IoT Central 應用程式的某些設定資料。 IoT Edge 裝置需要此資訊，才能向應用程式註冊及連線到該應用程式。

在 [管理] 區段中選取**您的應用程式**，並記下 scratchpad.txt 檔案中的**應用程式 URL** 和**應用程式識別碼**：

:::image type="content" source="./media/tutorial-video-analytics-create-app/administration.png" alt-text="設定適用於 AMS 的 AAD 應用程式":::

選取 [API 權杖]，然後針對**操作員**角色產生名為 **LVAEdgeToken** 的新權杖：

:::image type="content" source="./media/tutorial-video-analytics-create-app/token.png" alt-text="設定適用於 AMS 的 AAD 應用程式":::

記下 scratchpad.txt 檔案中的權杖，以便稍後使用。 關閉對話方塊之後，您就無法再次檢視權杖。

在 [管理] 區段中選取 [裝置連線]，然後選取 [SAS-IoT-Devices]。

記下 *scratchpad.txt* 檔案中的裝置**主要金鑰**。 稍後設定 IoT Edge 裝置時，您會用到此「主要群組共用存取簽章權杖」。

## <a name="edit-the-deployment-manifest"></a>編輯部署資訊清單

您會使用部署資訊清單來部署 IoT Edge 模組。 在 IoT Central 中，您可以將資訊清單匯入為裝置範本，然後讓 IoT Central 管理模組部署。

若要準備部署資訊清單：

1. 使用文字編輯器，開啟您儲存在 lva-configuration 資料夾中的 deployment.amd64.json 檔案。

1. 尋找 `LvaEdgeGatewayModule` 設定並變更映像名稱，如下列程式碼片段所示：

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. 在 `LvaEdgeGatewayModule` 區段的 `env` 節點中，新增媒體服務帳戶的名稱。 您已從 scratchpad.txt 檔案中記下此帳戶名稱：

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. 範本不會在 IoT Central 中公開這些屬性，因此您需要將媒體服務設定值新增至部署資訊清單。 找出 `lvaEdge` 模組，並將預留位置取代為您在建立媒體服務帳戶時，從 scratchpad.txt 檔案中記下的值。

    `azureMediaServicesArmId` 就是您在建立媒體服務帳戶時，從 *scratchpad.txt* 檔案中記下的**資源識別碼**。

    您已在為媒體服務建立服務主體時，從 *scratchpad.txt* 檔案中記下 `aadTenantId`、`aadServicePrincipalAppId` 和 `aadServicePrincipalSecret`：

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. 儲存變更。

(選擇性) 您可以將 Yolov3 模組取代為 Intel 中已最佳化硬體的 [OpenVINO&trade; Model Server – Edge AI Extension Module](https://github.com/openvinotoolkit/model_server/tree/ams/extras/ams_wrapper)。 您可以下載部署資訊清單範例：[deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json)。 此資訊清單會使用下列設定來取代 `lvaYolov3` 模組的設定：

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="create-the-azure-iot-edge-gateway-device"></a>建立 Azure IoT Edge 閘道裝置

影片分析 - 物件和動作偵測應用程式包含 **LVA Edge 物件偵測器**裝置範本和 **LVA Edge 動作偵測**裝置範本。 在本節中，您會使用部署資訊清單來建立閘道裝置範本，並將閘道裝置新增至您的 IoT Central 應用程式。

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>建立 LVA Edge 閘道的裝置範本

若要匯入部署資訊清單並建立 **LVA Edge 閘道**裝置範本：

1. 在 IoT Central 應用程式中，瀏覽至 [裝置範本]，然後選取 [+ 新增]。

1. 在 [選取範本類型] 頁面上，選取 [Azure IoT Edge] 圖格。 然後，選取 **[下一步：** 自訂]。

1. 在 [上傳 Azure IoT Edge 部署資訊清單] 頁面上，輸入「LVA Edge 閘道」作為範本名稱，然後核取 [具有下游裝置的閘道裝置]。

    請還不要瀏覽部署資訊清單。 如果您這樣做，部署精靈會預期每個模組都要有介面，但您只需要公開 **LvaEdgeGatewayModule**的介面。 您會在稍後的步驟中上傳資訊清單。

    :::image type="content" source="./media/tutorial-video-analytics-create-app/upload-deployment-manifest.png" alt-text="設定適用於 AMS 的 AAD 應用程式":::

    完成時，選取 下一步: **:** 。

1. 在 [檢閱]  頁面上，選取 [建立]  。

### <a name="import-the-device-capability-model"></a>匯入裝置功能模型

裝置範本必須包含裝置功能模型。 在 [LVA Edge 閘道] 頁面上，選取 [匯入功能模型] 磚。 瀏覽至您先前建立的 lva-configuration 資料夾，然後選取 LvaEdgeGatewayDcm.json 檔案。

**LVA Edge 閘道**裝置範本現在包含 **LVA Edge 閘道模組**和三個介面：**裝置資訊**、**LVA Edge 閘道設定**及 **LVA Edge 閘道介面**。

### <a name="replace-the-manifest"></a>取代資訊清單

在 [LVA Edge 閘道] 頁面上，選取 [+ 取代資訊清單]。

:::image type="content" source="./media/tutorial-video-analytics-create-app/replace-manifest.png" alt-text="設定適用於 AMS 的 AAD 應用程式":::

瀏覽至 lva-configuration 資料夾，然後選取您先前編輯的 deployment.amd64.json 資訊清單檔案。 選取 [上傳] 。 驗證完成之後，請選取 [取代]。

### <a name="add-relationships"></a>新增關聯性

在 **LVA Edge 閘道**裝置範本的 [模組/LVA 邊緣閘道模組] 底下，選取 [關聯性]。 選取 [+ 新增關聯性] 並新增下列兩個關聯性：

|顯示名稱               |名稱          |目標 |
|-------------------------- |------------- |------ |
|LVA Edge 動作偵測器   |使用預設值   |LVA Edge 動作偵測器裝置 |
|LVA Edge 物件偵測器   |使用預設值   |LVA Edge 物件偵測器裝置 |

然後選取 [儲存]。

:::image type="content" source="media/tutorial-video-analytics-create-app/relationships.png" alt-text="設定適用於 AMS 的 AAD 應用程式":::

### <a name="add-views"></a>新增檢視

**LVA Edge 閘道**裝置範本不包含任何檢視定義。

若要在裝置範本中新增檢視：

1. 在 **LVA Edge 閘道**裝置範本中，瀏覽至 [檢視]，然後選取 [視覺化裝置] 磚。

1. 輸入「LVA Edge 閘道裝置」作為檢視名稱。

1. 將下列這些磚新增至檢視：

    * 具有**裝置資訊**屬性的磚：**裝置型號**、**製造商**、**作業系統**、**處理器架構**、**軟體版本**、**總記憶體**，以及**總儲存體**。
    * 具有**可用記憶體**和**系統活動訊號**遙測值的折線圖磚。
    * 具有下列事件的事件歷程記錄磚：**建立相機**、**刪除相機**、**模組重新啟動**、**模組已開始**、**模組已停止**。
    * 顯示 **IoT Central 用戶端狀態**遙測的上一個已知值磚 (2x1)。
    * 顯示**模組狀態**遙測的上一個已知值磚 (2x1)。
    * 顯示**系統活動訊號**遙測的上一個已知值磚 (1x1)。
    * 顯示**連結相機**遙測的上一個已知值磚 (1x1)。

    :::image type="content" source="media/tutorial-video-analytics-create-app/gateway-dashboard.png" alt-text="設定適用於 AMS 的 AAD 應用程式":::

1. 選取 [儲存]。

### <a name="publish-the-device-template"></a>發佈裝置範本

您必須先發佈裝置範本，才能將裝置新增至應用程式：

1. 在 **LVA Edge 閘道**裝置範本中，選取 [發佈]。

1. 在 [將此裝置範本發佈至應用程式] 頁面上，選取 [發佈]。

**LVA Edge 閘道**現在可作為裝置類型，以在應用程式中的 [裝置] 頁面上使用。

## <a name="add-a-gateway-device"></a>新增閘道裝置

若要將 **LVA Edge 閘道**裝置新增至應用程式：

1. 瀏覽至 [裝置] 頁面，然後選取 [LVA Edge 閘道] 裝置範本。

1. 選取 [+ 新增]。

1. 在 [建立新裝置] 對話方塊中，將裝置名稱變更為 LVA Gateway 001，並將裝置識別碼變更為 lva-gateway-001。

    > [!NOTE]
    > 裝置識別碼在應用程式中必須是唯一的。

1. 選取 [建立]。

裝置狀態為 [已註冊]。

### <a name="get-the-device-credentials"></a>取得裝置認證

您需要可讓裝置連線到您 IoT Central 應用程式的認證。 若要取得裝置認證：

1. 在 [裝置] 頁面上，選取您建立的 **lva-gateway-001** 裝置。

1. 選取 [連接]。

1. 在 [裝置連線] 頁面上，記下 scratchpad.txt 檔案中的 [識別碼範圍]、[裝置識別碼] 和裝置的 [主要金鑰]。 您稍後會用到這些值。

1. 請確定連線方法已設定為 [共用存取簽章]。

1. 選取 [關閉]。

## <a name="next-steps"></a>後續步驟

您現在已使用**影片分析 - 物件和動作偵測**應用程式範本建立了 IoT Central 應用程式、建立了閘道裝置的裝置範本，以及將閘道裝置新增至應用程式。

如果您想要使用執行雲端 VM 且具有模擬影片串流的 IoT Edge 模組，來試用影片分析 - 物件和動作偵測應用程式：

> [!div class="nextstepaction"]
> [建立適用於影片分析的 IoT Edge 執行個體 (Linux VM)](./tutorial-video-analytics-iot-edge-vm.md)

如果您想要使用執行真實裝置且具有真實 **ONVIF** 相機的 IoT Edge 模組，來試用影片分析 - 物件和動作偵測應用程式：

> [!div class="nextstepaction"]
> [建立適用於影片分析的 IoT Edge 執行個體 (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)
