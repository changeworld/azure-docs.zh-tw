---
title: 什麼是 Azure IoT Central 中的裝置範本 |Microsoft Docs
description: Azure IoT Central 裝置範本可讓您指定連接至應用程式之裝置的行為。
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7e90a4e9ec88e074ec7b30d78bc99cfa8bf39ba2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87002347"
---
# <a name="what-are-device-templates"></a>什麼是裝置範本？

_本文適用于裝置開發人員和解決方案產生器。_

Azure IoT Central 中的裝置範本是一個藍圖，可定義連線至您應用程式之裝置類型的特性和行為。 例如，裝置範本會定義裝置所傳送的遙測，讓 IoT Central 可以建立使用正確單位和資料類型的視覺效果。

解決方案產生器會將裝置範本新增至 IoT Central 應用程式。 裝置開發人員會撰寫裝置程式碼，以執行在裝置範本中定義的行為。

裝置範本包括下列各節：

- _裝置功能模型（DCM）_。 裝置範本的這個部分會定義裝置與您的應用程式互動的方式。 裝置開發人員會實行 DCM 中定義的行為。
- _雲端屬性_。 裝置範本的這個部分可讓解決方案開發人員指定要儲存的任何裝置中繼資料。 雲端屬性永遠不會與裝置同步處理，而且只存在於應用程式中。 雲端屬性不會影響裝置開發人員為了實行 DCM 而撰寫的程式碼。
- _自訂_。 裝置範本的這個部分可讓解決方案開發人員覆寫 DCM 中的部分定義。 如果解決方案開發人員想要精簡應用程式處理值的方式（例如變更屬性的顯示名稱或用來顯示遙測值的色彩），自訂就很有用。 自訂不會影響裝置開發人員為了實行 DCM 而撰寫的程式碼。
- _Views_。 裝置範本的這個部分可讓解決方案開發人員定義視覺效果，以查看裝置的資料，並使用表單來管理和控制裝置。 這些視圖會使用 DCM、雲端屬性和自訂。 Views 不會影響裝置開發人員撰寫來實行 DCM 的程式碼。

## <a name="device-capability-models"></a>裝置功能模型

DCM 會定義裝置與您的 IoT Central 應用程式互動的方式。 裝置開發人員必須確定裝置會執行 DCM 中定義的行為，讓 IoT Central 可以監視和管理裝置。 DCM 由一或多個_介面_組成，而且每個介面都可以定義_遙測_類型、_裝置屬性_和_命令_的集合。 解決方案開發人員可以將定義 DCM 的 JSON 檔案匯入到裝置範本中，或使用 IoT Central 中的 web UI 來建立或編輯 DCM。 使用 Web UI 所做的 DCM 變更需要設定[裝置範本的版本](./howto-version-device-template.md)。

方案開發人員也可以匯出包含 DCM 的 JSON 檔案。 裝置開發人員可以使用此 JSON 檔來瞭解裝置應該如何與 IoT Central 應用程式通訊。

定義 DCM 的 JSON 檔案會使用數位對應項[定義語言（DTDL） V1](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)。 IoT Central 預期 JSON 檔案包含 DCM，而這些介面是以內嵌方式定義，而不是在不同的檔案中。

典型的 IoT 裝置是由下列各項所組成：

- 自訂群組件，這是讓裝置成為唯一的專案。
- 標準元件，這是所有裝置通用的專案。

這些元件在 DCM 中稱為_介面_。 介面會定義裝置所執行之每個元件的詳細資料。 介面可跨 DCMs 重複使用。

下列範例顯示具有兩個介面之環境感應器裝置的裝置功能模型大綱：

```json
{
  "@id": "urn:contoso:sensor_device:1",
  "@type": "CapabilityModel",
  "displayName": "Environment Sensor Capability Model",
  "implements": [
    {
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": "Device Information",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    },
    {
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:1",
        "@type": "Interface",
        "displayName": "Environmental Sensor",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

功能模型有一些必要的欄位：

- `@id`：簡單的統一資源名稱形式的唯一識別碼。
- `@type`：宣告此物件為功能模型。
- `@context`：指定功能模型所使用的 DTDL 版本。
- `implements`：列出您的裝置所實行的介面。

在 [執行] 區段的介面清單中，每個專案都有：

- `name`：介面的程式設計名稱。
- `schema`：功能模型所執行的介面。

介面有一些必要的欄位：

- `@id`：簡單的統一資源名稱形式的唯一識別碼。
- `@type`：宣告此物件為介面。
- `@context`：指定用於介面的 DTDL 版本。
- `contents`：列出組成您裝置的屬性、遙測和命令。

您可以使用一些選擇性欄位，將更多詳細資料新增至功能模型，例如 [顯示名稱] 和 [描述]。

### <a name="interface"></a>介面

此 DTDL 可讓您描述裝置的功能。 相關功能會分組為介面。 介面會描述您的裝置中所執行的屬性、遙測和命令：

- `Properties`. 屬性是代表裝置狀態的資料欄位。 使用屬性來表示裝置的持久狀態，例如 coolant 泵的關閉狀態。 屬性也可以代表基本裝置屬性，例如裝置的「固件」版本。 您可將屬性宣告為唯讀或可寫入。
- `Telemetry`. 遙測欄位代表來自感應器的測量。 每當您的裝置接受感應器測量時，它應該會傳送包含感應器資料的遙測事件。
- `Commands`. 命令代表裝置的使用者可以在裝置上執行的方法。 例如，可切換或關閉風扇的 reset 命令或命令。

下列範例顯示環境感應器介面定義：

```json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
  "@type": [
    "Telemetry",
    "SemanticType/Temperature"
  ],
  "description": "Current temperature on the device",
  "displayName": "Temperature",
  "name": "temp",
  "schema": "double",
  "unit": "Units/Temperature/fahrenheit"
},
{
  "@type": "Command",
  "name": "turnon",
  "comment": "This Commands will turn-on the LED light on the device.",
  "commandType": "synchronous"
},
{
  "@type": "Command",
  "name": "turnoff",
  "comment": "This Commands will turn-off the LED light on the device.",
  "commandType": "synchronous"
}
```

這個範例會顯示兩個屬性：遙測類型和兩個命令。 最小欄位描述具有：

- `@type`若要指定功能類型： `Telemetry` 、 `Property` 或 `Command` 。  在某些情況下，型別會包含一個語義型別，讓 IoT Central 能夠對如何處理值做出一些假設。
- `name`作為遙測值。
- `schema`指定遙測或屬性的資料類型。 這個值可以是基本類型，例如雙精度浮點數、整數、布林值或字串。 也支援複雜物件類型、陣列和對應。
- `commandType`指定應該如何處理此命令。

選擇性欄位（例如 [顯示名稱] 和 [描述]）可讓您將更多詳細資料新增至介面和功能。

### <a name="properties"></a>[內容]

根據預設，屬性為唯讀。 唯讀屬性表示裝置會向您的 IoT Central 應用程式報告屬性值更新。 您的 IoT Central 應用程式無法設定唯讀屬性的值。

您也可以在介面上將屬性標示為可寫入。 裝置可以從您的 IoT Central 應用程式接收可寫入屬性的更新，以及將屬性值更新報告至您的應用程式。

裝置不需要連線來設定屬性值。 當裝置下一次連線至應用程式時，會傳送更新的值。 這種行為適用于唯讀和可寫入的屬性。

請勿使用屬性從您的裝置傳送遙測。 例如，唯讀屬性（例如） `temperatureSetting=80` 應該表示裝置溫度已設定為80，且裝置正嘗試進入或離開此溫度。

針對可寫入的屬性，裝置應用程式會傳回所需狀態的狀態碼、版本和描述，以指出它是否已接收並套用屬性值。

### <a name="telemetry"></a>遙測

IoT Central 可讓您在儀表板和圖表上查看遙測，並在達到閾值時使用規則來觸發動作。 IoT Central 會使用 DCM 中的資訊（例如資料類型、單位和顯示名稱）來決定如何顯示遙測值。

您可以使用 IoT Central 資料匯出功能，將遙測串流至其他目的地，例如儲存體或事件中樞。

### <a name="commands"></a>命令

命令可能是同步或非同步。 同步命令預設必須在30秒內執行，而且當命令抵達時，裝置必須連接。 如果裝置在時間內回應，或裝置未連線，則命令會失敗。

使用非同步命令進行長時間執行的作業。 裝置會使用遙測訊息傳送進度資訊。 這些進度訊息具有下列標頭屬性：

- `iothub-command-name`：命令名稱，例如 `UpdateFirmware` 。
- `iothub-command-request-id`：在伺服器端產生的要求識別碼，並在初始呼叫中傳送至裝置。
- `iothub-interface-id`：此命令定義所在介面的識別碼，例如 `urn:example:AssetTracker:1` 。
 `iothub-interface-name`：此介面的實例名稱，例如 `myAssetTracker` 。
- `iothub-command-statuscode`：從裝置傳回的狀態碼，例如 `202` 。

## <a name="cloud-properties"></a>雲端屬性

雲端屬性是裝置範本的一部分，但不是 DCM 的一部分。 雲端屬性可讓解決方案開發人員指定要儲存在 IoT Central 應用程式中的任何裝置中繼資料。 雲端屬性不會影響裝置開發人員為了實行 DCM 而撰寫的程式碼。

解決方案開發人員可以將雲端屬性新增至儀表板和裝置屬性的視圖，讓操作員管理連接到應用程式的裝置。 解決方案開發人員也可以使用雲端屬性做為規則定義的一部分，以讓操作員可以編輯臨界值。

## <a name="customizations"></a>自訂

自訂是裝置範本的一部分，但不是 DCM 的一部分。 自訂可讓解決方案開發人員增強或覆寫 DCM 中的部分定義。 例如，解決方案開發人員可以變更遙測類型或屬性的顯示名稱。 方案開發人員也可以使用自訂來新增驗證，例如字串裝置屬性的最小或最大長度。

自訂可能會影響裝置開發人員撰寫來實行 DCM 的程式碼。 例如，自訂可以設定最小和最大字串長度，或遙測的最小值和最大數值。

## <a name="views"></a>檢視

解決方案開發人員會建立可讓操作員監視和管理已連線裝置的視圖。 Views 是裝置範本的一部分，因此，視圖會與特定裝置類型相關聯。 視圖可以包含：

- 繪製遙測的圖表。
- 顯示唯讀裝置屬性的磚。
- 讓運算子編輯可寫入裝置屬性的磚。
- 讓操作員編輯雲端屬性的磚。
- 讓運算子呼叫命令的磚，包括預期承載的命令。
- 顯示標籤、影像或 markdown 文字的磚。

您可以新增至視圖的遙測、屬性和命令，是由 [裝置] 範本中的 DCM、雲端屬性和自訂所決定。

## <a name="next-steps"></a>接下來的步驟

身為裝置開發人員，現在您已瞭解裝置範本，建議的後續步驟是閱讀[遙測、屬性和命令](./concepts-telemetry-properties-commands.md)承載，以深入瞭解裝置與 IoT Central 交換的資料。

身為解決方案開發人員，建議的下一個步驟是閱讀[在 Azure IoT Central 應用程式中定義新的 IoT 裝置類型](./howto-set-up-template.md)，以深入瞭解如何建立裝置範本。
