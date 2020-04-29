---
title: 從 Azure IoT 解決方案與 IoT 隨插即用預覽版裝置互動 | Microsoft Docs
description: 身為解決方案開發人員，請了解如何使用服務 SDK 與 IoT 隨插即用裝置互動。
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e349aadfd629202b1c8cdb5c53a88e0a6c2e06de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80159212"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>連線至 IoT 隨插即用預覽版裝置並與其互動

本操作指南說明如何使用 Node 服務 SDK 中的範例，了解 IoT 解決方案如何與 IoT 隨插即用預覽版裝置互動。

如果您尚未完成[將 IoT 隨插即用裝置連線至您的解決方案](quickstart-connect-pnp-device-solution-node.md)快速入門，請於此時完成。 本快速入門說明如何下載和安裝 SDK，並執行一些範例。

執行服務範例之前，請先開啟新的終端機，移至您複製之存放庫的根資料夾，瀏覽至 **digitaltwins/quickstarts/service** 資料夾，然後執行下列命令以安裝相依性：

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>執行服務範例

請使用下列範例來探索 Node.js 服務 SDK 的功能。 請確定已在您使用的殼層中設定 `IOTHUB_CONNECTION_STRING` 環境變數：

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>擷取數位對應項並列出介面

**get_digital_twin.js** 會取得與您的裝置相關聯的數位對應項，並在命令列中列印其元件。 此作業不需要執行中的裝置範例即可完成。

**get_digital_twin_interface_instance.js** 會取得與您的裝置相關聯之數位對應項的單一介面執行個體，並將其列印在命令列中。 此作業不需要裝置範例即可執行。

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>使用 Node 服務 SDK 取得和設定屬性

**update_digital_twin.js** 會使用完整的修補程式，在您的裝置數位對應項上更新可寫入的屬性。 您可以視需要更新多個介面上的多個屬性。 若要順利完成，必須同時執行裝置範例。 作業成功時，裝置範例會列印關於更新屬性的內容，而服務範例會在終端機中列印更新的數位對應項。

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>使用 Node 服務 SDK 傳送命令並擷取回應

**invoke_command.js** 會對您的裝置數位對應項叫用同步命令。 若要順利完成，必須同時執行裝置範例。 作業成功時，裝置範例會列印有關認可命令的內容，而服務用戶端則會在終端機中列印命令的結果。

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>連線至公用存放庫，並使用 Node 服務 SDK 擷取模型定義

依照與服務和裝置範例相同的指示，您必須設定下列環境變數：

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

您可以使用 [Azure IoT 認證入口網站](https://preview.catalog.azureiotsolutions.com)，在**公司存放庫**的 [連接字串]**** 索引標籤上中找到此連接字串。

連接字串會如下列範例所示：

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

在設定這四個環境變數之後，請以執行其他範例的相同方式來執行此範例：

```cmd/sh
node model_repo.js
```

此範例會下載 **ModelDiscovery** 介面，並在終端機中列印此模型。

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>根據功能模型和介面在 IoT 中樞執行查詢

IoT 中樞查詢語言支援 `HAS_INTERFACE` 和 `HAS_CAPABILITYMODEL`，如下列範例所示：

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>建立數位對應項路由

您的解決方案可以接收數位對應項變更事件的通知。 若要訂閱這些通知，請使用 [IoT 中樞路由功能](../iot-hub/iot-hub-devguide-endpoints.md)將通知傳送至端點，例如 Blob 儲存體、事件中樞或服務匯流排佇列。

若要建立數位對應項路由：

1. 在 Azure 入口網站中，移至您的 IoT 中樞資源。
1. 選取 [訊息路由]****。
1. 在 [路由]**** 索引標籤上，選取 [新增]****。
1. 在 [名稱]**** 欄位中輸入值，然後選擇 [端點]****。 如果您尚未設定端點，請選取 [新增端點]****。
1. 在 [資料來源]**** 下拉式清單中，選取 [數位對應項變更事件]****。
1. 選取 [儲存]  。

下列 JSON 顯示數位對應項變更事件的範例：

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>後續步驟

您已了解與 IoT 隨插即用裝置互動的服務解決方案，建議執行的下一個步驟是了解[模型探索](concepts-model-discovery.md)。
