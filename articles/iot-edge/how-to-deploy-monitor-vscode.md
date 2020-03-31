---
title: 使用視覺化工作室代碼大規模部署模組 - Azure IoT 邊緣
description: 使用視覺化工作室代碼的 IoT 擴展為 IoT 邊緣設備組創建自動部署。
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 08299a589dc6e8f768cba7ef976e109ef1fb69d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774129"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>使用視覺化工作室代碼大規模部署 IoT 邊緣模組

您可以使用 Visual Studio Code 創建**IoT Edge 自動部署**，以便一次管理許多設備的持續部署。 IoT Edge 的自動部署是 IoT 中心[自動裝置管理](/azure/iot-hub/iot-hub-automatic-device-management)功能的一部分。 部署是動態過程，使您能夠將多個模組部署到多個設備。 您還可以跟蹤模組的狀態和運行狀況，並在必要時進行更改。

有關詳細資訊，請參閱[瞭解單個設備或規模上的 IoT 邊緣自動部署](module-deployment-monitoring.md)。

在本文中，您可以設置視覺化工作室代碼和 IoT 擴展。 然後，您將瞭解如何將模組部署到一組 IoT Edge 設備。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂閱中的[IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 已安裝 IoT Edge 執行階段的 [IoT Edge 裝置](how-to-register-device.md#register-with-visual-studio-code)。
* [視覺工作室代碼](https://code.visualstudio.com/)。
* 用於視覺化工作室代碼的[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview)。

## <a name="sign-in-to-access-your-iot-hub"></a>登入以存取 IoT 中樞

您可以使用 Visual Studio 代碼的 Azure IoT 擴展對中心執行操作。 要使這些操作正常工作，您需要登錄到 Azure 帳戶並選擇正在處理的 IoT 中心。

1. 在 Visual Studio Code 中，開啟 [總管]**** 檢視。

1. 在資源管理器的底部，展開**Azure IoT 中心**部分。

1. 按一下**Azure IoT 中心**部分標題中的 **...** 若未看到省略符號，請將滑鼠暫留在標題上方。

1. 選擇 [選取 IoT 中樞]****。

1. 如果未登錄到 Azure 帳戶，請按照提示執行此操作。

1. 選取 Azure 訂用帳戶。

1. 選取您的 IoT 中樞。

## <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署清單是 JSON 文檔，用於描述要部署的模組。 它還描述了資料如何在模組之間流動，以及模組孿生所需的屬性。 有關詳細資訊，請參閱[瞭解如何在 IoT 邊緣中部署模組和建立路由](module-composition.md)。

若要使用 Visual Studio Code 部署模組，請以 .JSON 格式將部署資訊清單儲存到本機上。 運行命令時，您需要提供其位置，以便將配置應用於設備。

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

如果需要確定當前可以配置的 IoT Edge 設備，請運行**IoT 邊緣：獲取設備資訊**命令。

## <a name="identify-devices-with-target-conditions"></a>識別具有目標條件的設備

要標識要接收部署的 IoT Edge 設備，必須指定目標條件。 當指定的條件由設備 Id、標記值或報告的屬性值匹配時，將滿足目標條件。

在設備孿生中配置標記。 下面是具有標記的設備孿生示例：

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

如果部署的目標條件包含與標記的值之一（如`tag.location.building = '20'`）匹配的運算式，則此設備將收到部署。

如果要定位特定設備，而不考慮其標記或其他值，只需`deviceId`指定目標條件。

下面是一些更多示例：

* deviceId ='linuxprod1'
* 設備 Id = "linuxprod1" 或設備 Id = "linuxprod2" 或設備 Id = "linuxprod3"
* tags.environment ='prod'
* 標籤.環境 = "prod"和標記.位置 = "westus2"
* 標籤.環境 = "prod"OR 標記.位置 = "westus2"
* 標籤.運算子 = "John"和標籤.環境 = "prod"，而不是設備 Id = "linuxprod1"

有關詳細資訊，請參閱[目標條件](module-deployment-monitoring.md#target-condition)。 如需裝置對應項和標記的詳細資訊，請參閱[了解和使用 IoT 中樞的裝置對應項](../iot-hub/iot-hub-devguide-device-twins.md)。

### <a name="edit-the-device-twin"></a>編輯設備孿生

您可以在 Visual Studio 代碼中編輯設備孿生以配置標記。 在 **"查看"** 功能表中，選擇**命令調色板**並運行 **"IoT 邊緣：編輯設備孿生"** 命令。 選擇 IoT 邊緣設備，將顯示裝置孿生。

在此示例中，未定義任何標記。 將當前空節`"tags": {}`替換為您自己的標記定義。

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

保存本地檔後，運行**IoT 邊緣：更新設備孿生**命令。

## <a name="create-deployment-at-scale"></a>大規模創建部署

在設備孿生中配置了部署清單和配置的標記後，即可進行部署。

1. 在 **"查看"** 功能表中，選擇**命令調色板**並選擇**Azure IoT 邊緣：在"縮放"中創建部署**命令。

1. 導覽至您想要使用的部署資訊清單 JSON 檔案，，然後按一下 [選取 Edge 部署資訊清單]****。

1. 以**部署 ID**開頭，以提示提供值。

   ![指定部署 ID](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   指定這些參數的值：

  | 參數 | 描述 |
  | --- | --- |
  | 部署 ID | 將在 IoT 中心中創建的部署的名稱。 為部署指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。 |
  | 目標條件 | 輸入目標條件，以確定此部署將針對哪些設備。條件會以裝置對應項標籤或裝置對應項報告屬性為基礎，且應符合運算式格式。例如， `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | 優先順序 |  正整數。 如果兩個或多個部署針對同一設備，則應用具有"優先順序"最高數值的部署。 |

  指定優先順序後，終端應顯示類似于以下描述的輸出：

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>監視和修改部署

使用[Azure CLI](how-to-deploy-monitor-cli.md#monitor-a-deployment)或[Azure 門戶](how-to-deploy-monitor.md#monitor-a-deployment)監視、修改和刪除部署。 兩者都提供部署指標。

## <a name="next-steps"></a>後續步驟

瞭解有關[將模組部署到 IoT 邊緣設備](module-deployment-monitoring.md)方面瞭解有關的介紹。
