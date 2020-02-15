---
title: 從 Visual Studio Code Azure IoT Edge 部署模組
description: 搭配 Azure IoT Tools 使用 Visual Studio Code，將 IoT Edge 模組從您的 IoT 中樞推送至您的 IoT Edge 裝置，如部署資訊清單所設定。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/8/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e1b2e2a80670cf0409f8f8477563b9a209cc8706
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209200"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>從 Visual Studio Code 部署 Azure IoT Edge 模組

只要您使用商務邏輯建立 IoT Edge 模組，便需要將這些模組部署到裝置以在邊緣操作。 如果您有多個模組共同運作以收集及處理資料，您可以一次部署所有模組，並宣告這些模組的路由規則。

本文說明如何建立 JSON 部署資訊清單，然後使用該檔案將部署發送到 IoT Edge 裝置。 如需有關根據其共用標記來建立以多個裝置為目標的部署的詳細資訊，請參閱[使用 Visual Studio Code 大規模部署 IoT Edge 模組](how-to-deploy-monitor-vscode.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* 已安裝 IoT Edge 執行階段的 [IoT Edge 裝置](how-to-register-device.md#register-with-visual-studio-code)。
* [Visual Studio Code](https://code.visualstudio.com/) \(英文\)。
* 適用於 Visual Studio Code 的 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview)。

## <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 如需部署資訊清單的功能，以及如何建立此類清單的詳細資訊，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md) (英文)。

若要使用 Visual Studio Code 部署模組，請以 .JSON 格式將部署資訊清單儲存到本機上。 若要執行命令以將設定套用至裝置，您會使用到下一節中的檔案路徑。

下面以具有一個模組的基本部署資訊清單為例：

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>登入以存取 IoT 中樞

您可以使用適用於 Visual Studio Code 的 Azure IoT 擴充功能來透過 IoT 中樞執行各種操作。 為了讓這些作業發揮作用，您需要登入 Azure 帳戶，並選取現用的 IoT 中樞。

1. 在 Visual Studio Code 中，開啟 [總管] 檢視。

1. 在 [Explorer] 底部，展開 [ **Azure IoT 中樞**] 區段。

   ![展開 Azure IoT 中樞區段](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. 按一下 [ **Azure IoT 中樞**] 區段標頭中的 [ **...** ]。 若未看到省略符號，請將滑鼠暫留在標題上方。

1. 選擇 [選取 IoT 中樞]。

1. 如果您未登入 Azure 帳戶，請依照提示執行。

1. 選取 Azure 訂用帳戶。

1. 選取您的 IoT 中樞。

## <a name="deploy-to-your-device"></a>部署至裝置

您可以透過套用您運用模組資訊設定的部署資訊清單，將模組部署到裝置上。

1. 在 [Visual Studio Code explorer] 視圖中，展開 [ **Azure IoT 中樞**] 區段，然後展開 [**裝置**] 節點。

1. 以滑鼠右鍵按一下您想要利用部署資訊清單設定的 IoT Edge 裝置。

    > [!TIP]
    > 若要確認您所選擇的裝置是 IoT Edge 裝置，請選取該裝置以展開模組清單，並確認 **$edgeHub** 和 **$edgeAgent** 是否存在。 每個 IoT Edge 都裝置包含這兩個模組。

1. 選取 [建立單一裝置的部署]。

1. 導覽至您想要使用的部署資訊清單 JSON 檔案，，然後按一下 [選取 Edge 部署資訊清單]。

   ![選取 Edge 部署資訊清單](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

您部署的結果會列印在 VS Code 輸出中。 如果目標裝置已執行並連線到網際網路，系統便會在幾分鐘內套用成功的部署。

## <a name="view-modules-on-your-device"></a>在裝置上檢視模組

將模組部署至裝置之後，您就可以在 [ **Azure IoT 中樞**] 區段中加以查看。 選取您的 IoT Edge 裝置旁的箭號可展開。 這會顯示所有目前正在執行的模組。

如果您最近有在裝置上部署新模組，請將滑鼠暫留在 [Azure IoT 中樞裝置] 區段標題上方，然後選取重新整理圖示以更新檢視。

以滑鼠右鍵按一下模組名稱，以檢視並編輯模組對應項。

## <a name="next-steps"></a>後續步驟

瞭解如何[使用 Visual Studio Code 大規模部署和監視 IoT Edge 模組](how-to-deploy-monitor.md)
