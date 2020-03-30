---
title: 使用 Azure CLI - Azure IoT 邊緣大規模部署模組
description: 使用適用於 Azure CLI 的 IoT 延伸模組為 IoT Edge 裝置群組建立自動部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9152b38a0155b610f39f7de239bcc377ad96be5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271469"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>使用 Azure CLI 大規模部署與監視 IoT Edge 模組

使用 Azure 命令列介面創建**IoT Edge 自動部署**，以便一次管理許多設備的持續部署。 IoT Edge 的自動部署是 IoT 中心[自動裝置管理](/azure/iot-hub/iot-hub-automatic-device-management)功能的一部分。 部署是動態過程，使您能夠將多個模組部署到多個設備，跟蹤模組的狀態和運行狀況，並在必要時進行更改。

有關詳細資訊，請參閱[瞭解單個設備或規模上的 IoT 邊緣自動部署](module-deployment-monitoring.md)。

在本文中，您會設定 Azure CLI 和 IoT 擴充功能。 然後，您將瞭解如何將模組部署到一組 IoT Edge 設備，並使用可用的 CLI 命令監視進度。

## <a name="cli-prerequisites"></a>CLI 先決條件

* Azure 訂閱中的[IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。
* 已安裝 IoT Edge 執行階段的 [IoT Edge 裝置](how-to-register-device.md#prerequisites-for-the-azure-cli)。
* 您環境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 至少，Azure CLI 版本必須為 2.0.70 或以上。 使用 `az --version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。
* [Azure CLI 的 IoT 擴展](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 有關詳細資訊，請參閱[瞭解如何在 IoT 邊緣中部署模組和建立路由](module-composition.md)。

若要使用 Azure CLI 部署模組，請在本機上將部署資訊清單儲存成 .txt 檔案。 運行命令時，在下一節中使用檔路徑將配置應用於設備。

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

## <a name="layered-deployment"></a>分層部署

分層部署是一種自動部署，可以堆疊在一起。 有關分層部署的詳細資訊，請參閱[瞭解單個設備或規模上的 IoT Edge 自動部署](module-deployment-monitoring.md)。

可以像任何自動部署一樣使用 Azure CLI 創建和管理分層部署，但僅有幾個差異。 創建分層部署後，相同的 Azure CLI 適用于與任何部署相同的分層部署。 要創建分層部署，請將`--layered`標誌添加到創建命令。

第二個區別是部署清單的構造。 雖然標準自動部署除任何使用者模組外還必須包含系統運行時模組，但分層部署只能包含使用者模組。 相反，分層部署也需要在設備上部署標準自動部署，以提供每個 IoT Edge 設備（如系統運行時模組）所需的元件。

下面是一個基本分層部署清單，其中一個模組作為示例：

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
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

前面的示例顯示了模組的`properties.desired`分層部署設置。 如果此分層部署針對已應用同一模組的設備，它將覆蓋任何現有的所需屬性。 為了更新所需的屬性，而不是覆蓋所需的屬性，可以定義一個新的子節。 例如：

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

有關在分層部署中配置模組孿生的詳細資訊，請參閱[分層部署](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>使用標記識別裝置

在您可建立部署之前，必須能夠指定您要影響的裝置。 Azure IoT Edge 會使用裝置對應項中的 **tags** 來識別裝置。 每個設備可以有多個標記，您以任何對解決方案有意義的方式定義這些標記。 例如，如果您管理智慧建築的校園，便可以將下列標記新增至裝置：

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

如需裝置對應項和標記的詳細資訊，請參閱[了解和使用 IoT 中樞的裝置對應項](../iot-hub/iot-hub-devguide-device-twins.md)。

## <a name="create-a-deployment"></a>建立部署

透過建立一個包含部署資訊清單及其他參數的部署，您就可以將模組部署至您的目標裝置。

使用[az iot 邊緣部署創建](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create)命令創建部署：

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

使用同一命令與`--layered`標誌創建分層部署。

部署創建命令採用以下參數：

* **--分層**- 用於將部署標識為分層部署的可選標誌。
* **--deployment-id** - 將建立在 IoT 中樞內的部署名稱。 為部署指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。 必要參數。
* **--content** - 部署資訊清單 JSON 的檔案名稱。 必要參數。
* **--hub-name** - 將在其中建立部署的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 命令`az account set -s [subscription name]`更改當前訂閱。
* **--labels** - 新增標籤，以協助追蹤您的部署。 標籤是成對的「名稱, 值」組合，可描述您的部署。 標籤會採用 JSON 格式的名稱和值。 例如， `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** - 輸入目標條件來判斷這個部署會將哪些裝置設為目標。條件會以裝置對應項標籤或裝置對應項報告屬性為基礎，且應符合運算式格式。例如： `tags.environment='test' and properties.reported.devicemodel='4000x'` 。
* **--優先順序**- 正整數。 如果兩個以上部署的目標為相同的裝置，則將會套用 [優先順序] 數值最高的部署。
* **--指標**- 創建查詢 edgeHub 報告屬性的指標以跟蹤部署的狀態。 指標採用 JSON 輸入或檔路徑。 例如： `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'` 。

## <a name="monitor-a-deployment"></a>監視部署

使用[az iot 邊緣部署顯示](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show)命令顯示單個部署的詳細資訊：

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

部署顯示命令採用以下參數：

* **--deployment-id** - 存在於 IoT 中樞的部署名稱。 必要參數。
* **--中心名稱**- 部署中存在的 IoT 中心的名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶

在命令視窗中檢查部署。**metrics** 屬性會列出每個中樞所評估每個計量的計數：

* **targetedCount** - 系統計量，指定 IoT 中樞內符合目標條件的裝置對應項數目。
* **appliedCount** - 系統計量，指定已在 IoT 中樞內將部署內容套用到其模組對應項的裝置數目。
* **報告成功計數**- 指定部署中 IoT Edge 設備的數量的設備指標，報告從 IoT Edge 用戶端運行時成功。
* **報告失敗計數**- 指定 IoT Edge 用戶端運行時部署報告失敗中的 IoT Edge 設備數的設備指標。

您可以使用[az iot 邊緣部署顯示度量](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric)命令顯示每個指標的裝置識別碼 或物件清單：

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

部署顯示度量命令採用以下參數：

* **--deployment-id** - 存在於 IoT 中樞的部署名稱。
* **--指標 id** - 要查看裝置識別碼 清單的指標的名稱，例如`reportedFailedCount`。
* **--中心名稱**- 部署中存在的 IoT 中心的名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶。

## <a name="modify-a-deployment"></a>修改部署

當您修改部署時，所做的變更會立即複寫到所有目標裝置。

如果您更新目標條件，就會發生下列更新：

* 如果裝置不符合舊的目標條件，但符合新的目標條件，而且此部署為該裝置的最高優先順序，則會將此部署套用到裝置。
* 如果目前執行此部署的裝置不再符合目標條件，它會解除安裝此部署，並採用下一個最高優先順序的部署。
* 如果目前執行此部署的裝置不再符合目標條件，且不符合任何其他部署的目標條件，則不會在裝置上發生任何變更。 裝置會以模組目前的狀態繼續執行它目前的模組，但不再將其當成此部署的一部分來管理。 一旦它符合任何其他部署的目標條件之後，就會解除安裝此部署，並採用新的部署。

不能更新部署的內容，其中包括在部署清單中定義的模組和路由。 如果要更新部署的內容，可以通過創建以具有更高優先順序的相同設備為目標的新部署來執行此操作。 您可以修改現有模組的某些屬性，包括目標條件、標籤、指標和優先順序。

使用[az iot 邊緣部署更新](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update)命令更新部署：

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

部署更新命令採用以下參數：

* **--deployment-id** - 存在於 IoT 中樞的部署名稱。
* **--中心名稱**- 部署中存在的 IoT 中心的名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶
* **--set** - 更新部署中的屬性。 您可以更新下列屬性：
  * targetCondition - 例如 `targetCondition=tags.location.state='Oregon'`
  * 標籤
  * priority
* **--添加**- 向部署添加新屬性，包括目標條件或標籤。
* **--刪除**- 刪除現有屬性，包括目標條件或標籤。

## <a name="delete-a-deployment"></a>刪除部署

當您刪除部署時，所有裝置均會採用其下一個最高優先順序的部署。 如果您的裝置不符合任何其他部署的目標條件，則在刪除部署時不會移除模組。

使用[az iot 邊緣部署刪除](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete)命令刪除部署：

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

部署刪除命令採用以下參數：

* **--deployment-id** - 存在於 IoT 中樞的部署名稱。
* **--中心名稱**- 部署中存在的 IoT 中心的名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶

## <a name="next-steps"></a>後續步驟

瞭解有關[將模組部署到 IoT 邊緣設備](module-deployment-monitoring.md)方面瞭解有關的介紹。
