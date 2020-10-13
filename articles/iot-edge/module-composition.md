---
title: 部署具有部署資訊清單的模組和路由 - Azure IoT Edge
description: 了解部署資訊清單如何宣告要部署哪些模組、如何加以部署，以及如何在其間建立訊息路由。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3f6c12b892e01aafd5beecdff14751481cf7fc96
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963392"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>了解如何在 IoT Edge 中部署模組及建立路由

每個 IoT Edge 裝置會至少執行兩個模組：$edgeAgent 和 $edgeHub；這兩個模組都是 IoT Edge 執行階段的一部分。 IoT Edge 裝置可執行多個其他模組的任意數處理序。 使用部署資訊清單來告知裝置要安裝哪些模組，以及如何設定這些模組共同作業。

部署資訊清單是 JSON 文件，描述：

* **IoT Edge 代理程式**模組對應項，其包含三個元件：
  * 在裝置上執行的每個模組其容器映像。
  * 存取私人容器登錄的認證，其包含模組映像。
  * 如何建立及管理每個模組的指示。
* **IoT Edge 中樞**模組對應項，包括訊息如何在模組之間流動，以及最後如何到達 IoT 中樞。
* 任何其他模組對應項的所需屬性 (選擇性)。

所有 IoT Edge 裝置都必須使用部署資訊清單來設定。 新安裝的 IoT Edge 執行階段會報告錯誤碼，直到使用有效的資訊清單進行設定。

在 Azure IoT Edge 教學課程中，您會藉由完成 Azure IoT Edge 入口網站中的精靈，來建置部署資訊清單。 您也可以程式設計方式使用 REST 或 IoT 中樞服務 SDK，套用部署資訊清單。 如需詳細資訊，請參閱[了解 IoT Edge 部署](module-deployment-monitoring.md)。

## <a name="create-a-deployment-manifest"></a>建立部署資訊清單

概括而言，部署資訊清單是以其所需屬性設定的模組對應項清單。 部署資訊清單會告知 IoT Edge 裝置 (或裝置群組) 要安裝哪些模組，以及如何設定它們。 部署資訊清單包含每個模組對應項的「所需屬性」。 IoT Edge 裝置會回報每個模組的「報告屬性」。

每個部署資訊清單中都必須要有兩個模組：`$edgeAgent` 及 `$edgeHub`。 這些模組是 IoT Edge 執行階段的一部分，負責管理 IoT Edge 裝置與其上執行的模組。 如需這些模組的詳細資訊，請參閱[了解 IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

除了這兩個執行時間模組之外，您還可以新增您自己的50模組，以在 IoT Edge 裝置上執行。

僅包含 IoT Edge 執行階段 (edgeAgent 與 edgeHub) 的部署資訊清單為有效。

部署資訊清單遵循此結構：

```json
{
  "modulesContent": {
    "$edgeAgent": { // required
      "properties.desired": {
        // desired properties of the IoT Edge agent
        // includes the image URIs of all deployed modules
        // includes container registry credentials
      }
    },
    "$edgeHub": { //required
      "properties.desired": {
        // desired properties of the IoT Edge hub
        // includes the routing information between modules, and to IoT Hub
      }
    },
    "module1": {  // optional
      "properties.desired": {
        // desired properties of module1
      }
    },
    "module2": {  // optional
      "properties.desired": {
        // desired properties of module2
      }
    }
  }
}
```

## <a name="configure-modules"></a>設定模組

定義 IoT Edge 執行階段在您部署中安裝模組的方式。 IoT Edge 代理程式是一項執行階段元件，負責管理 IoT Edge 裝置的安裝、更新及狀態回報。 因此，$edgeAgent 模組對應項包含所有模組的設定及管理資訊。 這項資訊也包括 IoT Edge 代理程式本身的設定參數。

$EdgeAgent 屬性遵循此結構：

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "settings":{
            "registryCredentials":{
              // give the IoT Edge agent access to container images that aren't public
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            // configuration and management details
          },
          "edgeHub": {
            // configuration and management details
          }
        },
        "modules": {
          "module1": {
            // configuration and management details
          },
          "module2": {
            // configuration and management details
          }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

IoT Edge 代理程式架構1.1 版與 IoT Edge 1.0.10 版本一起發行，並啟用模組啟動順序。 針對執行1.0.10 版或更新版本的任何 IoT Edge 部署，建議使用架構版本1.1。

### <a name="module-configuration-and-management"></a>模組設定和管理

您可以使用 [IoT Edge 代理程式所需屬性] 清單，定義哪些模組要部署到 IoT Edge 裝置，以及如何設定和管理它們。

如需可以或必須包含之所需屬性的完整清單，請參閱 [IoT Edge 代理程式和 IoT Edge 中樞的屬性](module-edgeagent-edgehub.md)。

例如：

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": { ... },
        "systemModules": {
          "edgeAgent": { ... },
          "edgeHub": { ... }
        },
        "modules": {
          "module1": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "myacr.azurecr.io/module1:latest",
              "createOptions": "{}"
            }
          },
          "module2": { ... }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

每個模組都有一個 **設定** 屬性，其中包含模組 **映射**、容器登錄中容器映射的位址，以及在啟動時設定映射的任何 **createOptions** 。 如需詳細資訊，請參閱 [如何設定 IoT Edge 模組的容器建立選項](how-to-use-create-options.md)。

EdgeHub 模組和自訂模組也有三個屬性，可告訴 IoT Edge 代理程式如何管理它們：

* **狀態**：第一次部署時，模組是否應執行或停止。 必要。
* **RestartPolicy**：當 IoT Edge 代理程式停止時，應重新開機該模組。 必要。
* **StartupOrder**： *在 IoT Edge 版本1.0.10 中引進。* IoT Edge 代理程式在第一次部署時應該啟動模組的順序。 順序是以整數宣告，其中會先啟動具有0啟動值的模組，然後再遵循較高的數位。 EdgeAgent 模組沒有啟動值，因為它一律會先啟動。 選擇性。

  IoT Edge 代理程式會依啟動值的順序起始模組，但不會等待每個模組完成啟動，再進入下一個模組。

  如果某些模組相依于其他模組，啟動順序會很有説明。 例如，您可能會想要先啟動 edgeHub 模組，讓它準備好在其他模組啟動時路由傳送訊息。 或者，您可能想要在將資料傳送至其中的模組之前啟動儲存體模組。 不過，您應該一律設計模組來處理其他模組的失敗。 這是容器的本質，它們可能會在任何時間停止並重新啟動，以及任意次數。

## <a name="declare-routes"></a>宣告路由

IoT Edge 中樞會管理模組、IoT 中樞和任何分葉裝置間的通訊。 因此，$edgeHub 模組對應項包含稱為 *routes* 的所需屬性，其會宣告訊息在部署中的傳遞方式。 您可以在相同部署內具有多個路由。

路由會以下列語法在 **$edgeHub** 所需屬性中宣告：

```json
{
  "modulesContent": {
    "$edgeAgent": { ... },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "route1": "FROM <source> WHERE <condition> INTO <sink>",
          "route2": {
            "route": "FROM <source> WHERE <condition> INTO <sink>",
            "priority": 0,
            "timeToLiveSecs": 86400
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    },
    "module1": { ... },
    "module2": { ... }
  }
}
```

IoT Edge 中樞架構1.1 版與 IoT Edge 1.0.10 版本一起發行，並啟用路由排定優先順序和存留時間。 針對執行1.0.10 版或更新版本的任何 IoT Edge 部署，建議使用架構版本1.1。

每個路由都需要 *訊息來源，* 以及訊息到達的 *接收* 。 *條件*是選擇性的部分，您可以用來篩選訊息。

您可以將 *優先順序* 指派給您想要確保先處理其訊息的路由。 這項功能在上游連線很弱或受限的情況下很有用，而且您有重要的資料應優先于標準遙測訊息。

### <a name="source"></a>來源

來源會指定訊息來自於何處。 IoT Edge 可路由來自模組或分葉裝置的訊息。

使用 IoT SDK，模組可使用 ModuleClient 類別來宣告其訊息的特定輸出佇列。 輸出佇列非必要，但對管理多個路由很有幫助。 分葉裝置可使用 IoT SDK 的 DeviceClient 類別，以將訊息傳送至 IoT 中樞的相同方式將訊息傳送到 IoT Edge 閘道裝置。 如需詳細資訊，請參閱[了解和使用 Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md)。

來源屬性可以是下列其中任何一個值：

| 來源 | 描述 |
| ------ | ----------- |
| `/*` | 所有來自任何模組或分葉裝置的裝置到雲端訊息或對應項變更通知 |
| `/twinChangeNotifications` | 任何來自任何模組或分葉裝置的對應項變更 (報告屬性) |
| `/messages/*` | 任何由模組或分葉裝置透過部分或無輸出傳送的裝置到雲端訊息 |
| `/messages/modules/*` | 由模組透過部分或無輸出傳送的任何裝置到雲端訊息 |
| `/messages/modules/<moduleId>/*` | 任何由特定模組透過部分或無輸出傳送的裝置到雲端訊息 |
| `/messages/modules/<moduleId>/outputs/*` | 任何由特定模組透過部分輸出傳送的裝置到雲端訊息 |
| `/messages/modules/<moduleId>/outputs/<output>` | 任何由特定模組透過特定輸出傳送的裝置到雲端訊息 |

### <a name="condition"></a>條件

條件在路由宣告中是選擇性項目。 如果想要將所有訊息從來源傳遞至接收器，請直接省略整個 **WHERE** 子句。 您可以使用 [IoT 中樞查詢語言](../iot-hub/iot-hub-devguide-routing-query-syntax.md)來篩選特定訊息或符合條件的訊息類型。 IoT Edge 路由不支援根據對應項標籤或屬性來篩選訊息。

在 IoT Edge 的模組之間傳遞的訊息所用的格式，和您的裝置與 Azure IoT 中樞傳遞訊息時所用的格式相同。 所有訊息均採用 JSON 格式，且含 **systemProperties**、**appProperties** 和 **body** 參數。

您可以使用下列語法，對三個參數中的任何一個參數建立查詢：

* 系統屬性：`$<propertyName>` 或 `{$<propertyName>}`
* 應用程式屬性：`<propertyName>`
* 主體屬性：`$body.<propertyName>`

如需如何針對訊息屬性建立查詢的範例，請參閱[裝置到雲端訊息路由查詢運算式](../iot-hub/iot-hub-devguide-routing-query-syntax.md)。

有個 IoT Edge 特有的範例，是想要篩選從分葉裝置送達閘道裝置的訊息。 來自模組的訊息包含稱為 **connectionModuleId** 的系統屬性。 因此，如果您將訊息直接從分葉裝置路由至 IoT 中樞，請使用下列路由來排除模組訊息：

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>接收

接收會定義要將訊息傳往何處。 只有模組和 IoT 中樞能接收訊息。 訊息無法路由到其他裝置。 接收屬性中沒有任何萬用字元選項。

接收屬性可以是下列其中任何一個值：

| 接收 | 描述 |
| ---- | ----------- |
| `$upstream` | 將訊息傳送到 IoT 中樞 |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | 將訊息傳送到特定模組的特定輸入 |

IoT Edge 提供至少一次的保證。 IoT Edge 中樞會將訊息儲存在本機，以備路由無法將訊息傳遞至其接收器時使用。 例如，如果 IoT Edge 中樞無法連線至 IoT 中樞，或目標模組未連線。

[IoT Edge 中樞所需屬性](module-edgeagent-edgehub.md)的 `storeAndForwardConfiguration.timeToLiveSecs` 屬性會指定訊息能在 IoT Edge 中樞內儲存多久。

### <a name="priority-and-time-to-live"></a>優先順序和存留時間

您可以使用定義路由的字串，或使用路由字串、優先順序整數和存留時間整數的物件來宣告路由。

選項 1：

   ```json
   "route1": "FROM <source> WHERE <condition> INTO <sink>",
   ```

選項2：使用 IoT Edge 中樞架構1.1 版的 IoT Edge 版本1.0.10 引進：

   ```json
   "route2": {
     "route": "FROM <source> WHERE <condition> INTO <sink>",
     "priority": 0,
     "timeToLiveSecs": 86400
   }
   ```

**優先權** 值可以是0-9 （含），其中0是最高優先順序。 訊息會根據其端點排入佇列。 系統會先處理以特定端點為目標的所有優先順序0訊息，然後再處理任何優先順序1的訊息（以相同的端點為目標），並向下行。 如果相同端點的多個路由具有相同的優先順序，則會以先出先服務的方式來處理它們的訊息。 如果未指定任何優先權，則會將路由指派給最低優先順序。

除非明確設定，否則 **timeToLiveSecs** 屬性會從 IoT Edge 中樞的 **storeAndForwardConfiguration** 繼承值。 值可以是任何正整數。

如需如何管理優先順序佇列的詳細資訊，請參閱 [路由優先順序和存留時間](https://github.com/Azure/iotedge/blob/master/doc/Route_priority_and_TTL.md)的參考頁面。

## <a name="define-or-update-desired-properties"></a>定義或更新所需屬性

部署資訊清單可為部署到 IoT Edge 裝置的每個模組指定所需屬性。 部署資訊清單中所需屬性會覆寫目前在模組對應項中的任何所需屬性。

如果您未在部署資訊清單中指定模組對應項的所需屬性，IoT 中樞就不會修改模組對應項。 您可以改為以程式設計方式來設定所需屬性。

您可以使用與修改裝置對應項相同的機制來修改模組對應項。 如需詳細資訊，請參閱[模組對應項開發人員指南](../iot-hub/iot-hub-devguide-module-twins.md)。

## <a name="deployment-manifest-example"></a>部署資訊清單範例

下列範例顯示有效部署資訊清單文件的可能外觀。

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 0,
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
            "startupOrder": 2,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 1,
            "env": {
              "tempLimit": {"value": "100"}
            },
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "sensorToFilter": {
            "route": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "priority": 0,
            "timeToLiveSecs": 1800
          },
          "filterToIoTHub": {
            "route": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream",
            "priority": 1,
            "timeToLiveSecs": 1800
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 100
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>後續步驟

* 如需 $edgeAgent 和 $edgeHub 中可包含或必須包含的屬性完整清單，請參閱 [IoT Edge 代理程式和 IoT Edge 中樞的屬性](module-edgeagent-edgehub.md)。

* 您現在知道如何使用 IoT Edge 模組，[了解開發 IoT Edge 模組的需求和工具](module-development.md)。
