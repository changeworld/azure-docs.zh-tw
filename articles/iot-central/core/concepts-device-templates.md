---
title: 什麼是 Azure IoT Central 中的裝置範本 |Microsoft Docs
description: Azure IoT Central 裝置範本可讓您指定連接至應用程式之裝置的行為。 裝置範本會指定裝置必須執行的遙測、屬性和命令。 裝置範本也會在 IoT Central 中定義裝置的 UI，例如操作員所使用的表單和儀表板。
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 04c2330ffee396f5fc30b85640e992df77c08263
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97795423"
---
# <a name="what-are-device-templates"></a>什麼是裝置範本？

_本文適用于裝置開發人員和解決方案產生器。_

Azure IoT Central 中的裝置範本是藍圖，可定義連接至應用程式之裝置類型的特性和行為。 例如，裝置範本會定義裝置所傳送的遙測，讓 IoT Central 可以建立使用正確單位和資料類型的視覺效果。

解決方案產生器會將裝置範本新增至 IoT Central 應用程式。 裝置開發人員會撰寫裝置程式碼，以實行裝置範本中定義的行為。

裝置範本包含下列各節：

- _裝置型號_。 裝置範本的這個部分會定義裝置與您應用程式互動的方式。 裝置開發人員會實施模型中所定義的行為。
    - _預設元件_。 每個裝置型號都有預設元件。 預設元件的介面會描述裝置型號特有的功能。
    - _元件_。 裝置型號除了預設元件之外，還可能包含元件來描述裝置功能。 每個元件都有一個描述元件功能的介面。 元件介面可在其他裝置模型中重複使用。 例如，數個電話裝置型號可能會使用相同的相機介面。
    - _繼承的介面_。 裝置模型包含一或多個擴充預設元件功能的介面。
- _雲端屬性_。 裝置範本的這個部分可讓解決方案開發人員指定任何要儲存的裝置中繼資料。 雲端屬性永遠不會與裝置同步，而且只存在於應用程式中。 雲端屬性不會影響裝置開發人員撰寫的程式碼，以實行裝置型號。
- _自訂_。 裝置範本的這個部分可讓解決方案開發人員覆寫裝置模型中的部分定義。 如果解決方案開發人員想要調整應用程式處理值的方式（例如變更屬性的顯示名稱或用來顯示遙測值的色彩），自訂會很有用。 自訂不會影響裝置開發人員撰寫來執行裝置模型的程式碼。
- _Views_。 裝置範本的這個部分可讓解決方案開發人員定義視覺效果來查看裝置的資料，以及用來管理和控制裝置的表單。 這些視圖會使用裝置模型、雲端屬性和自訂專案。 Views 不會影響裝置開發人員所撰寫的程式碼來執行裝置型號。

## <a name="device-models"></a>裝置型號

裝置模型會定義裝置如何與您的 IoT Central 應用程式互動。 裝置開發人員必須確定裝置會執行裝置模型中所定義的行為，讓 IoT Central 可以監視和管理裝置。 裝置型號是由一或多個 _介面_ 所組成，而且每個介面都可以定義 _遙測_ 類型、 _裝置屬性_ 和 _命令_ 的集合。 解決方案開發人員可以將定義裝置型號的 JSON 檔案匯入至裝置範本，或使用 IoT Central 中的 web UI 來建立或編輯裝置型號。 使用 Web UI 所做的裝置型號變更，需要將 [裝置範本設為版本](./howto-version-device-template.md)。

解決方案開發人員也可以匯出包含裝置型號的 JSON 檔案。 裝置開發人員可以使用此 JSON 檔，來瞭解裝置應該如何與 IoT Central 應用程式進行通訊。

定義裝置型號的 JSON 檔案會使用數位對應項 [定義語言 (DTDL) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。 IoT Central 預期 JSON 檔案包含具有以內嵌方式定義之介面的裝置模型，而不是在不同的檔案中。

一般的 IoT 裝置是由下列各項所組成：

- 自訂群組件，也就是讓裝置成為唯一的專案。
- 標準元件，也就是所有裝置通用的專案。

這些元件在裝置模型中稱為 _介面_ 。 介面會定義您的裝置所執行之每個元件的詳細資料。 介面可在裝置模型之間重複使用。 在 DTDL 中，元件是指在個別 DTDL 檔中定義的介面。

下列範例會顯示溫度控制器裝置的裝置型號大綱。 預設元件包含 `workingSet` 、和的定義 `serialNumber` `reboot` 。 裝置模型也包含 `thermostat` 和 `deviceInformation` 介面：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry", "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit" : "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat",
      "displayName": "Thermostat",
      "description": "Thermostat One."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

介面有一些必要的欄位：

- `@id`：以簡單統一資源名稱形式提供的唯一識別碼。
- `@type`：宣告此物件為介面。
- `@context`：指定介面所使用的 DTDL 版本。
- `contents`：列出組成您裝置的屬性、遙測及命令。 這些功能可以定義在多個介面中。

您可以使用一些選擇性欄位，將更多詳細資料新增至功能模型，例如顯示名稱和描述。

在 implements 區段的介面清單中，每個專案都有：

- `name`：介面的程式設計名稱。
- `schema`：功能模型所實施的介面。

## <a name="interfaces"></a>介面

DTDL 可讓您描述裝置的功能。 相關的功能會分組為介面。 介面會描述您的裝置所執行的屬性、遙測和命令：

- `Properties`. 屬性是代表裝置狀態的資料欄位。 使用屬性來代表裝置的持久狀態，例如 coolant 泵的關閉狀態。 屬性也可以代表基本裝置屬性，例如裝置的固件版本。 您可將屬性宣告為唯讀或可寫入。 只有裝置可以更新唯讀屬性的值。 操作員可以設定可寫入屬性的值，以傳送至裝置。
- `Telemetry`. 遙測欄位代表來自感應器的度量。 當您的裝置進行感應器測量時，它應該會傳送包含感應器資料的遙測事件。
- `Commands`. 命令代表裝置使用者可在裝置上執行的方法。 例如，重設命令或用來切換風扇的命令。

下列範例顯示控溫器介面定義：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName" : "Temperature",
      "description" : "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit" : "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit" : "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name" : "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name" : "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name" : "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name" : "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

此範例顯示兩個屬性 (一個唯讀和一個可寫入的) 、一個遙測類型和一個命令。 基本欄位描述具有：

- `@type` 若要指定功能的類型： `Telemetry` 、 `Property` 或 `Command` 。  在某些情況下，類型會包含一種語義型別，可讓 IoT Central 針對如何處理值提出一些假設。
- `name` 適用于遙測值。
- `schema` 指定遙測或屬性的資料類型。 這個值可以是基本類型，例如 double、integer、boolean 或 string。 也支援複雜物件類型和對應。

選擇性欄位（例如 [顯示名稱] 和 [描述]）可讓您將更多詳細資料新增至介面和功能。

## <a name="properties"></a>屬性

依預設，屬性為唯讀。 唯讀屬性表示裝置會向您的 IoT Central 應用程式報告屬性值更新。 您的 IoT Central 應用程式無法設定唯讀屬性的值。

您也可以將屬性標記為可在介面上寫入。 裝置可以從您的 IoT Central 應用程式接收可寫入屬性的更新，以及應用程式的報告屬性值更新。

裝置不需要連線來設定屬性值。 當裝置下一次連接至應用程式時，就會傳送更新的值。 此行為適用于唯讀和可寫入屬性。

請勿使用屬性從您的裝置傳送遙測。 例如，readonly 屬性（例如） `temperatureSetting=80` 應該表示裝置溫度已設定為80，而裝置正嘗試達到或保持在此溫度。

針對可寫入的屬性，裝置應用程式會傳回所需的狀態狀態碼、版本和描述，以指出它是否接收並套用屬性值。

## <a name="telemetry"></a>遙測

IoT Central 可讓您在儀表板和圖表上查看遙測，並在達到閾值時使用規則來觸發動作。 IoT Central 會使用裝置模型中的資訊（例如資料類型、單位和顯示名稱）來判斷如何顯示遙測值。

您可以使用 IoT Central 資料匯出功能，將遙測串流至其他目的地，例如儲存體或事件中樞。

## <a name="commands"></a>命令

根據預設，命令必須在30秒內執行，而且裝置必須在命令抵達時連線。 如果裝置在時間內回應，或裝置未連線，則命令會失敗。

命令可以有要求參數，並傳迴響應。

### <a name="offline-commands"></a>離線命令

如果裝置已離線，您可以在裝置範本中針對命令啟用 [ **離線時使用佇列** ] 選項，以選擇佇列命令。

離線命令是從您的解決方案到裝置的單向通知。 離線命令可以有要求參數，但不會傳迴響應。

> [!NOTE]
> 此選項僅適用于 IoT Central web UI。 如果您從裝置範本匯出模型或介面，則不會包含這種設定。

## <a name="cloud-properties"></a>雲端屬性

雲端屬性是裝置範本的一部分，但不是裝置型號的一部分。 雲端屬性可讓解決方案開發人員指定要儲存在 IoT Central 應用程式中的任何裝置中繼資料。 雲端屬性不會影響裝置開發人員撰寫的程式碼，以實行裝置型號。

解決方案開發人員可以將雲端屬性新增至儀表板和 views，以及裝置屬性，讓操作員能夠管理已連線至應用程式的裝置。 解決方案開發人員也可以使用雲端屬性作為規則定義的一部分，以讓操作員可以編輯臨界值。

## <a name="customizations"></a>自訂

自訂是裝置範本的一部分，但不是裝置模型的一部分。 自訂可讓解決方案開發人員增強或覆寫裝置模型中的部分定義。 例如，解決方案開發人員可以變更遙測類型或屬性的顯示名稱。 方案開發人員也可以使用自訂來新增驗證，例如字串裝置屬性的最小或最大長度。

自訂可能會影響裝置開發人員撰寫的程式碼，以實行裝置型號。 例如，自訂可以設定最小和最大字串長度，或遙測的最小值和最大數值。

## <a name="views"></a>檢視

解決方案開發人員建立的視圖可讓操作員監視和管理連線的裝置。 Views 是裝置範本的一部分，因此視圖會與特定裝置類型相關聯。 視圖可以包含：

- 用來繪製遙測資料的圖表。
- 顯示唯讀裝置屬性的磚。
- 讓操作員編輯可寫入裝置屬性的磚。
- 讓操作員編輯雲端屬性的磚。
- 讓操作員呼叫命令的圖格，包括預期承載的命令。
- 顯示標籤、影像或 markdown 文字的磚。

您可以新增至視圖的遙測、屬性和命令，取決於裝置模型、雲端屬性和裝置範本中的自訂專案。

## <a name="next-steps"></a>後續步驟

作為裝置開發人員，您現在已瞭解裝置範本，建議的後續步驟是閱讀 [遙測、屬性和命令](./concepts-telemetry-properties-commands.md) 承載，以深入瞭解裝置與 IoT Central 交換的資料。

作為解決方案開發人員，建議的下一個步驟是 [在您的 Azure IoT Central 應用程式中閱讀定義新的 IoT 裝置類型](./howto-set-up-template.md) ，以深入瞭解如何建立裝置範本。
