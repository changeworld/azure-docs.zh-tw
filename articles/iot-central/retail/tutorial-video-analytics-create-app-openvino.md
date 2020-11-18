---
title: 教學課程 - 在 Azure IoT Central 中建立影片分析 - 物件和動作偵測應用程式 (OpenVINO)
description: 本教學課程說明如何在 IoT Central 中建立影片分析應用程式。 您可以建立及自訂應用程式，並將其連線到其他 Azure 服務。 本教學課程使用 Intel OpenVINO&trade; 工具組進行即時物件偵測。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: af967c58cdeb2c750178141193a711a66af7477c
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426718"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-openvinotrade"></a>教學課程：在 Azure IoT Central 中建立影片分析 - 物件和動作偵測應用程式 (OpenVINO&trade;)

以解決方案建立者的身份，了解如何使用 IoT Central 的「影片分析 - 物件和動作偵測」應用程式範本、Azure IoT Edge 裝置、Azure 媒體服務和用於偵測物件和動作的 Intel 硬體最佳化 OpenVINO&trade;，來建立影片分析應用程式。 此解決方案使用零售商店來示範如何符合一般商務在監視安全性相機上的需求。 解決方案會在影片摘要中使用自動物件偵測，以快速識別並尋找感興趣的事件。

> [!TIP]
> 若要使用 YOLO v3 而非 OpenVINO&trade; 來偵測物件和動作，請參閱[教學課程：在 Azure IoT Central 中建立影片分析 - 物件和動作偵測應用程式 (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md)。

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>編輯部署資訊清單

您會使用部署資訊清單來部署 IoT Edge 模組。 在 IoT Central 中，您可以將資訊清單匯入為裝置範本，然後讓 IoT Central 管理模組部署。

若要準備部署資訊清單：

1. 使用文字編輯器，開啟您儲存在 lva-configuration 資料夾中的 deployment.openvino.amd64.json 檔案。

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

    `azureMediaServicesArmId` 就是您在建立媒體服務帳戶時，從 *scratchpad.txt* 檔案中記下的 **資源識別碼**。

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

本教學課程會將您的解決方案設定為使用 OpenVINO&trade; 模組來偵測物件和動作。 下列程式碼片段顯示模組的設定：

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

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>取代資訊清單

在 [LVA Edge 閘道 v2] 頁面上，選取 [+ 取代資訊清單]。

:::image type="content" source="./media/tutorial-video-analytics-create-app-openvino/replace-manifest.png" alt-text="取代資訊清單":::

瀏覽至 lva-configuration 資料夾，然後選取您先前編輯的 deployment.openvino.amd64.json 資訊清單檔案。 選取 [上傳] 。 驗證完成之後，請選取 [取代]。

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]
