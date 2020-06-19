---
title: 使用 Azure CLI 大規模部署模組 - Azure IoT Edge
description: 使用適用於 Azure CLI 的 IoT 延伸模組為 IoT Edge 裝置群組建立自動部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a7bb2cc23374110d447ec7526ada75f7e36a966e
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726157"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>使用 Azure CLI 大規模部署與監視 IoT Edge 模組

使用 Azure 命令列介面建立 **IoT Edge 自動部署**，一次管理許多裝置的持續部署。 IoT Edge 的自動部署是 IoT 中樞[自動裝置管理](/azure/iot-hub/iot-hub-automatic-device-management)功能的一部分。 部署是動態程序，可讓您將多個模組部署到多個裝置、追蹤模組的狀態和健康情況，並視需要進行變更。

如需詳細資訊，請參閱[了解單一裝置或大規模的 IoT Edge 自動部署](module-deployment-monitoring.md)。

在本文中，您會設定 Azure CLI 和 IoT 擴充功能。 接著，您將了解如何使用可用的 CLI 命令，將模組部署到一組 IoT Edge 裝置並監視進度。

## <a name="cli-prerequisites"></a>CLI 先決條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。
* 已安裝 IoT Edge 執行階段的 [IoT Edge 裝置](how-to-register-device.md#prerequisites-for-the-azure-cli)。
* 您環境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 版本至少必須是 2.0.70 或更新版本。 使用 `az --version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。
* [適用於 Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension) \(英文\)。

## <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 如需詳細資訊，請參閱[了解如何在 IoT Edge 中部署模組及建立路由](module-composition.md)。

若要使用 Azure CLI 部署模組，請在本機上將部署資訊清單儲存成 .txt 檔案。 若要執行命令以將設定套用至裝置，您會使用到下一節中的檔案路徑。

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

分層部署是一種自動部署類型，可以堆疊在彼此之上。 如需有關分層部署的詳細資訊，請參閱[了解單一裝置或大規模的 IoT Edge 自動部署](module-deployment-monitoring.md)。

分層部署就像任何自動部署一樣，可以使用 Azure CLI 來建立和管理，只有一些差異。 建立分層部署之後，與任何部署一樣，相同的 Azure CLI 適用於分層部署。 若要建立分層部署，請將 `--layered` 旗標新增至 create 命令。

第二個差異是部署資訊清單的建構。 雖然標準自動部署除了任何使用者模組之外，也必須包含系統執行階段模組，但分層部署只能包含使用者模組。 分層部署也需要在裝置上使用標準自動部署，以提供每個 IoT Edge 裝置的必要元件，例如系統執行階段模組。

下面以具有一個模組的基本分層部署資訊清單為例：

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

上一個範例示範了為模組設定 `properties.desired` 的分層部署。 如果此分層部署的目標是已套用相同模組的裝置，則會覆寫任何所需的現有屬性。 若要更新 (而不是覆寫) 所需的屬性，您可以定義新的子區段。 例如：

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

如需有關在分層部署中設定模組對應項的詳細資訊，請參閱[分層部署](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>使用標記識別裝置

在您可建立部署之前，必須能夠指定您要影響的裝置。 Azure IoT Edge 會使用裝置對應項中的 **tags** 來識別裝置。 每部裝置都可以有多個您利用任何對您解決方案有意義的方式定義的標記。 例如，如果您管理智慧建築的校園，便可以將下列標記新增至裝置：

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

使用 [az iot edge deployment create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/edge/deployment?view=azure-cli-latest#ext-azure-iot-az-iot-edge-deployment-create) 命令建立部署：

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

使用相同的命令搭配 `--layered` 旗標，建立分層部署。

deployment create 命令接受下列參數︰

* **--layered** - 將部署識別為分層部署的選用旗標。
* **--deployment-id** - 將建立在 IoT 中樞內的部署名稱。 為部署指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。 必要參數。
* **--content** - 部署資訊清單 JSON 的檔案名稱。 必要參數。
* **--hub-name** - 將在其中建立部署的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令變更您目前的訂用帳戶。
* **--labels** - 新增標籤，以協助追蹤您的部署。 標籤是成對的「名稱, 值」組合，可描述您的部署。 標籤會採用 JSON 格式的名稱和值。 例如， `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** - 輸入目標條件來判斷這個部署會將哪些裝置設為目標。 條件會以裝置對應項標籤或裝置對應項報告屬性為基礎，且應符合運算式格式。 例如： `tags.environment='test' and properties.reported.devicemodel='4000x'` 。
* **--priority** - 正整數。 如果兩個以上部署的目標為相同的裝置，則將會套用 [優先順序] 數值最高的部署。
* **--metrics** - 建立查詢 edgeHub 報告屬性的計量，以追蹤部署的狀態。 計量會接受 JSON 輸入或 filepath。 例如： `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'` 。

若要使用 Azure CLI 監視部署，請參閱[監視 IoT Edge 部署](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli)。

## <a name="modify-a-deployment"></a>修改部署

當您修改部署時，所做的變更會立即複寫到所有目標裝置。

如果您更新目標條件，就會發生下列更新：

* 如果裝置不符合舊的目標條件，但符合新的目標條件，而且此部署為該裝置的最高優先順序，則會將此部署套用到裝置。
* 如果目前執行此部署的裝置不再符合目標條件，它會解除安裝此部署，並採用下一個最高優先順序的部署。
* 如果目前執行此部署的裝置不再符合目標條件，且不符合任何其他部署的目標條件，則不會在裝置上發生任何變更。 裝置會以模組目前的狀態繼續執行它目前的模組，但不再將其當成此部署的一部分來管理。 一旦它符合任何其他部署的目標條件之後，就會解除安裝此部署，並採用新的部署。

您無法更新部署的內容，包括部署資訊清單中定義的模組和路由。 如果您想要更新部署的內容，請針對具有較高優先順序的相同裝置，建立新的部署。 您可以修改現有模組的某些屬性，包括目標條件、標籤、計量和優先順序。

使用 [az iot edge deployment update](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/edge/deployment?view=azure-cli-latest#ext-azure-iot-az-iot-edge-deployment-update) 命令更新部署：

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

deployment update 命令接受下列參數︰

* **--deployment-id** - 存在於 IoT 中樞的部署名稱。
* **--hub-name** - 部署存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶
* **--set** - 更新部署中的屬性。 您可以更新下列屬性：
  * targetCondition - 例如 `targetCondition=tags.location.state='Oregon'`
  * 標籤
  * priority
* **--add** - 將新的屬性加入至部署，包括目標條件或標籤。
* **--remove** - 移除現有的屬性，包括目標條件或標籤。

## <a name="delete-a-deployment"></a>刪除部署

當您刪除部署時，所有裝置均會採用其下一個最高優先順序的部署。 如果您的裝置不符合任何其他部署的目標條件，則在刪除部署時不會移除模組。

使用 [az iot edge deployment delete](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/edge/deployment?view=azure-cli-latest#ext-azure-iot-az-iot-edge-deployment-delete) 命令刪除部署：

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

deployment delete 命令接受下列參數︰

* **--deployment-id** - 存在於 IoT 中樞的部署名稱。
* **--hub-name** - 部署存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶

## <a name="next-steps"></a>後續步驟

深入了解如何[將模組部署到 IoT Edge 裝置](module-deployment-monitoring.md)。
