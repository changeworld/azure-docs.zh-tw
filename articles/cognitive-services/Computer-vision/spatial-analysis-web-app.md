---
title: 部署空間分析 web 應用程式
titleSuffix: Azure Cognitive Services
description: 瞭解如何在 web 應用程式中使用空間分析。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: aahi
ms.openlocfilehash: 24d4dd4d0caa49b9514bf19f707ea87b0b071a79
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357091"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>How to：部署人員計數 web 應用程式

您可以使用本文來瞭解如何將空間分析整合至可瞭解人員移動的 web 應用程式，並監視佔用實體空間的人員人數。 

在本教學課程中，您將了解如何：

* 部署空間分析容器
* 設定作業和攝影機
* 在 Web 應用程式中設定 IoT 中樞連接
* 部署和測試 Web 應用程式

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* Azure IoT Edge 部署設定和[Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub/)的基本瞭解
* 設定的 [主機電腦](spatial-analysis-container.md)。

## <a name="deploy-the-spatial-analysis-container"></a>部署空間分析容器

填寫 [要求應用程式](https://aka.ms/csgate) ，以取得執行容器的存取權。 

遵循 [主機電腦安裝程式](./spatial-analysis-container.md) 來設定主機電腦，並將 IoT Edge 裝置連線到 Azure IoT 中樞。 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>在您的訂用帳戶中部署 Azure IoT 中樞服務

首先，請建立 Azure IoT 中樞服務的實例，其中包含標準定價層 (S1) 或免費層 (S0) 。 請遵循下列指示，使用 Azure CLI 建立此實例。

填寫必要的參數：
* 訂用帳戶： Azure 訂用帳戶的名稱或識別碼
* 資源群組：建立資源群組的名稱
* Iot 中樞名稱：建立 IoT 中樞的名稱
* IoTHub 名稱：您所建立的 IoT 中樞名稱 
* Edge 裝置名稱：建立 Edge 裝置的名稱

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>在主機電腦上 Azure IoT Edge 部署容器

使用 Azure CLI 將空間分析容器部署為主電腦上的 IoT 模組。 部署程式需要部署資訊清單檔案，其中會列出您的部署所需的容器、變數和設定。 您可以在 GitHub 上找到 [Azure Stack Edge 特定部署資訊清單](https://github.com/Azure-Samples/cognitive-services-rest-api-samples/) 以及 [非 Azure Stack Edge 特定部署資訊清單](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) 的範例，其中包含 *空間分析* 容器的基本部署設定。 

或者，您可以使用適用于 Visual Studio Code 的 Azure IoT 擴充功能來執行 IoT 中樞的作業。 若要深入瞭解，請移至 [Visual Studio Code 中的部署 Azure IoT Edge 模組](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-vscode) 。

> [!NOTE] 
> *空間分析-telegraf* 和 *空間分析-診斷* 容器是選擇性的。 您可以決定將它們從 *DeploymentManifest.js* 檔案中移除。 如需詳細資訊，請參閱 [遙測和疑難排解](./spatial-analysis-logging.md) 文章。 您可以在 Github 上的檔案中找到兩個範例 *DeploymentManifest.js* ，適用于 [Azure Stack Edge 裝置](https://go.microsoft.com/fwlink/?linkid=2142179) 或其他 [桌上型電腦](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)

### <a name="set-environment-variables"></a>設定環境變數

IoT Edge 模組的大部分 **環境變數** 都已在上述連結之檔案的範例 *DeploymentManifest.js* 中設定。 在檔案中，搜尋 `BILLING_ENDPOINT` 和 `API_KEY` 環境變數，如下所示。 將這些值取代為您稍早建立的端點 URI 和 API 金鑰。 確認 EULA 值設定為 [接受]。 

```json
"EULA": { 
    "value": "accept"
},

"BILLING_ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"API_KEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>設定作業參數

現在 *空間分析* 容器的初始設定完成後，下一個步驟是設定作業參數，並將其新增至部署。 

第一個步驟是更新上面連結的範例部署資訊清單，並設定的 operationId， `cognitiveservices.vision.spatialanalysis-personcount` 如下所示：


```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

更新部署資訊清單之後，請遵循攝影機製造商的指示來安裝相機、設定相機 url，以及設定使用者名稱和密碼。 

接下來，設定 `VIDEO_URL` 為相機的 RTSP url，以及用來連接到相機的認證。

如果 edge 裝置有一個以上的 GPU，請選取要在其上執行這項操作的 GPU。 確定您負載平衡的作業會在單一 GPU 上執行的作業不超過8個，一次在單一 GPU 上執行。  

接下來，設定您想要在其中計算人員的區域。 若要設定區域多邊形，請先遵循製造商的指示，從相機取出框架。 若要判斷多邊形的每個頂點，請選取框架上的某個點，取得相對於左邊的點（相對於框架左上角）的 x、y 圖元座標，然後依對應的框架尺寸除以。 將結果設定為頂點的 x、y 座標。 您可以在欄位中設定區域多邊形設定 `SPACEANALYTICS_CONFIG` 。

這是範例影片畫面，顯示如何針對大小為1920/1080 的框架計算頂點座標。
![範例影片畫面](./media/spatial-analysis/sample-video-frame.jpg)

您也可以選取當偵測到的人員計數和產生事件時的信賴臨界值。 如果您想要輸出所有事件，請將閾值設定為0。

### <a name="execute-the-deployment"></a>執行部署

現在部署資訊清單已完成，請使用 Azure CLI 中的此命令，將主機電腦上的容器部署為 IoT Edge 模組。

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

填寫必要的參數：

* IoT 中樞名稱：您的 Azure IoT 中樞名稱
* DeploymentManifest.json：部署檔的名稱
* IoT Edge 裝置名稱：主機電腦的 IoT Edge 裝置名稱
* 訂用帳戶：您的訂用帳戶識別碼或名稱

此命令會開始部署，您可以在 Azure 入口網站中查看 Azure IoT 中樞實例的部署狀態。 狀態可能會顯示為 *417-裝置的部署設定* 在裝置完成下載容器映射並開始執行之前未設定。

### <a name="validate-that-the-deployment-was-successful"></a>驗證部署是否成功

在 Azure 入口網站的 IoT 中樞實例中，于空間分析模組的 IoT Edge 模組設定中找出 *執行時間狀態* 。 *執行時間狀態* 所 **需的值** 和 **回報值** 應為 `Running` 。 請參閱以下內容，以瞭解這在 Azure 入口網站上的樣子。

![部署驗證範例](./media/spatial-analysis/deployment-verification.png)

此時，空間分析容器正在執行操作。 它會發出作業的 AI 見解 `cognitiveservices.vision.spatialanalysis-personcount` ，並將這些深入解析作為遙測路由傳送到您的 Azure IoT 中樞實例。 若要設定其他攝影機，可以更新部署資訊清單檔，然後再次執行部署。

## <a name="person-counting-web-application"></a>計算 Web 應用程式的人員

此人計算 web 應用程式可讓您快速設定範例 web 應用程式，並將其裝載在您的 Azure 環境中。

### <a name="get-the-person-counting-app-container"></a>取得計算應用程式容器的人員

此應用程式的容器形式可在 Azure Container Registry 上取得。 使用下列 docker pull 命令下載它。 請聯絡 Microsoft， projectarchon@microsoft.com 以取得存取權杖。

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

將容器推送至 Azure Container Registry (ACR) 。

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

若要安裝容器，請建立新的 Azure 用於容器的 Web App，並填入必要的參數。 然後移至 [ **Docker** ] 索引標籤，選取 [ **單一容器** ]，然後 **Azure Container Registry** ]。 使用您在上推入影像的 Azure Container Registry 實例。

![輸入映射詳細資料](./media/spatial-analysis/solution-app-create-screen.png)

輸入上述參數之後，請按一下 [ **審核] + [建立** ] 並建立應用程式。

### <a name="configure-the-app"></a>設定應用程式 

等待安裝程式完成，然後流覽至您在 Azure 入口網站中的資源。 移至 [設定 **] 區段，** 並新增下列兩個 **應用程式設定** 。

* `EventHubConsumerGroup` –您的 Azure IoT 中樞取用者群組的字串名稱，您可以在 IoT 中樞內建立新的取用者群組，或使用預設群組。 
* `IotHubConnectionString`–您 Azure IoT 中樞的連接字串，可從 Azure IoT 中樞資源設定參數的 [金鑰] 區段中取出。 ![](./media/spatial-analysis/solution-app-config-page.png)

加入這兩個設定之後，請按一下 [ **儲存** ]。 然後按一下左側導覽功能表中的 [ **驗證/授權** ]，並使用所需的驗證層級加以更新。 我們建議使用 Azure Active Director (Azure AD) express。 

### <a name="test-the-app"></a>測試應用程式

移至 Azure Web 應用程式，並確認部署是否成功，以及 Web 應用程式是否正在執行。 流覽至已設定的 url： `<yourapp>.azurewebsites.net` 以查看正在執行的應用程式。

![測試部署](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>取得 PersonCount 來源程式碼
如果您想要查看或修改此應用程式的原始程式碼，您可以 [在 Github 上](https://github.com/Azure-Samples/cognitive-services-spatial-analysis)找到此程式碼。

## <a name="next-steps"></a>後續步驟

* [設定空間分析作業](./spatial-analysis-operations.md)
* [記錄和疑難排解](spatial-analysis-logging.md)
* [攝影機放置指南](spatial-analysis-camera-placement.md)
* [區域和行放置指南](spatial-analysis-zone-line-placement.md)
