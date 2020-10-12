---
title: 使用 Visual Studio Code Azure IoT Edge 大規模部署模組
description: 使用 Visual Studio Code 的 IoT 擴充功能，為 IoT Edge 裝置群組建立自動部署。
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 92540c57179ae0198f78b588681167fe48097362
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82134360"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>使用 Visual Studio Code 大規模部署 IoT Edge 模組

您可以使用 Visual Studio Code 來建立 **IoT Edge 的自動部署** ，以同時管理許多裝置的進行中部署。 IoT Edge 的自動部署是 IoT 中樞[自動裝置管理](/azure/iot-hub/iot-hub-automatic-device-management)功能的一部分。 部署是動態程式，可讓您將多個模組部署至多個裝置。 您也可以追蹤模組的狀態和健全狀況，並在必要時進行變更。

如需詳細資訊，請參閱[了解單一裝置或大規模的 IoT Edge 自動部署](module-deployment-monitoring.md)。

在本文中，您會設定 Visual Studio Code 和 IoT 擴充功能。 接著，您將瞭解如何將模組部署到一組 IoT Edge 裝置。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* 已安裝 IoT Edge 執行階段的 [IoT Edge 裝置](how-to-register-device.md#register-with-visual-studio-code)。
* [Visual Studio Code](https://code.visualstudio.com/) \(英文\)。
* 適用於 Visual Studio Code 的 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview)。

## <a name="sign-in-to-access-your-iot-hub"></a>登入以存取 IoT 中樞

您可以使用適用于 Visual Studio Code 的 Azure IoT 擴充功能，對您的中樞進行作業。 若要讓這些作業正常運作，您必須登入您的 Azure 帳戶，並選取您正在處理的 IoT 中樞。

1. 在 Visual Studio Code 中，開啟 [總管] 檢視。

1. 在 Explorer 底部，展開 [Azure IoT 中樞] 區段。

1. 按一下 [Azure IoT 中樞] 區段標題中的 **...** 。 若未看到省略符號，請將滑鼠暫留在標題上方。

1. 選擇 [選取 IoT 中樞]。

1. 如果您未登入 Azure 帳戶，請遵循提示來進行。

1. 選取 Azure 訂用帳戶。

1. 選取您的 IoT 中樞。

## <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單是描述要部署哪些模組的 JSON 檔。 它也會描述模組之間的資料流程，以及模組 twins 的所需屬性。 如需詳細資訊，請參閱[了解如何在 IoT Edge 中部署模組及建立路由](module-composition.md)。

若要使用 Visual Studio Code 部署模組，請以 .JSON 格式將部署資訊清單儲存到本機上。 當您執行命令以將設定套用至裝置時，您將需要提供其位置。

下面以具有一個模組的基本部署資訊清單為例：

```json
{
  "content": {
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
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

如果您需要判斷目前可以設定的 IoT Edge 裝置，請執行 [ **IoT Edge：取得裝置資訊** ] 命令。

## <a name="identify-devices-with-target-conditions"></a>識別具有目標條件的裝置

若要識別要接收部署的 IoT Edge 裝置，您必須指定目標條件。 當指定的準則符合 deviceId、標記值或回報的屬性值時，就會符合目標條件。

您可以在裝置對應項中設定標記。 以下是具有標記的裝置對應項範例：

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

如果部署的目標條件包含符合其中一個標記值的運算式（例如），則此裝置會接收部署 `tag.location.building = '20'` 。

如果您想要將目標設為特定的裝置，而不論其標記或其他值為何，只要指定 `deviceId` 目標條件的。

以下是一些範例：

* deviceId ='linuxprod1'
* deviceId = ' linuxprod1 ' 或 deviceId = ' linuxprod2 ' 或 deviceId = ' linuxprod3 '
* tags.environment ='prod'
* 標記. 環境 = ' 生產 ' 和標記。位置 = ' westus2 '
* 標記. 環境 = ' 生產 ' 或標記。位置 = ' westus2 '
* 標記。 operator = ' John ' 和標記。環境 = ' 生產 '，而不是 deviceId = ' linuxprod1 '

請參閱 [目標條件](module-deployment-monitoring.md#target-condition) 以取得詳細資料。 如需裝置對應項和標記的詳細資訊，請參閱[了解和使用 IoT 中樞的裝置對應項](../iot-hub/iot-hub-devguide-device-twins.md)。

### <a name="edit-the-device-twin"></a>編輯裝置對應項

您可以在 Visual Studio Code 中編輯裝置對應項，以設定標記。 從 [ **View** ] 功能表選取 [ **命令** 選擇區]，然後執行 [ **IoT Edge：編輯裝置** 對應項] 命令。 選取您的 IoT Edge 裝置，裝置對應項隨即出現。

在此範例中，未定義任何標記。 將目前的空白區段取代 `"tags": {}` 為您自己的標記定義。

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

儲存本機檔案之後，請執行 **IoT Edge：更新裝置** 對應項命令。

## <a name="create-deployment-at-scale"></a>大規模建立部署

在裝置對應項中設定部署資訊清單和已設定的標籤之後，您就可以開始部署。

1. 從 [ **View** ] 功能表選取 [ **命令** 選擇區]，然後選取 [ **Azure IoT Edge：建立大規模部署** ] 命令。

1. 導覽至您想要使用的部署資訊清單 JSON 檔案，，然後按一下 [選取 Edge 部署資訊清單]。

1. 從 **部署識別碼**開始，以提示的形式提供值。

   ![指定部署識別碼](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   指定這些參數的值：

  | 參數 | 說明 |
  | --- | --- |
  | 部署 ID | 將在 IoT 中樞中建立的部署名稱。 為部署指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。 |
  | 目標條件 | 輸入目標條件，以決定要將哪些裝置設為此部署的目標。 條件會以裝置對應項標籤或裝置對應項報告屬性為基礎，且應符合運算式格式。例如， `tags.environment='test' and properties.reported.devicemodel='4000x'` 。 |
  | 優先順序 |  正整數。 如果有兩個或多個部署以相同裝置為目標，則會套用具有最高優先順序數值的部署。 |

  指定優先順序之後，終端機應該會顯示類似下列描繪的輸出：

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>監視和修改部署

使用 [Azure 入口網站](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal) 或 [Azure CLI](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli) 來監視、修改及刪除部署。 兩者都提供您部署的度量。

## <a name="next-steps"></a>後續步驟

深入了解如何[將模組部署到 IoT Edge 裝置](module-deployment-monitoring.md)。
