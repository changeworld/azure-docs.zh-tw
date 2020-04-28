---
title: 如何建立使用者定義函數-在 Azure 數位 Twins 中 |Microsoft Docs
description: 如何在 Azure Digital Twins 中建立使用者定義函式、比對器和角色指派。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 232d85789c25e905873286eba6fda32c327a6e25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76276926"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>如何在 Azure Digital Twins 中建立使用者定義函式

[使用者定義函式](./concepts-user-defined-functions.md)可讓使用者將自訂邏輯設定為從傳入遙測訊息和空間圖表中繼資料中執行。 使用者也可以將事件傳送至預先定義的[端點](./how-to-egress-endpoints.md)。

本指南將在範例中逐步示範如何從所接收的溫度事件中偵測超過特定溫度的任何讀數並發出警示。

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>用戶端程式庫參考

使用者定義函式執行階段中可作為協助程式方法的函式，會列在[用戶端參考](./reference-user-defined-functions-client-library.md)文件中。

## <a name="create-a-matcher"></a>建立比對器

比對器是針對指定的遙測訊息，判斷要使用哪個使用者定義函式的圖表物件。

- 有效比對器條件的比較：

  - `Equals`
  - `NotEquals`
  - `Contains`

- 有效比對器條件的目標：

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

下列比對器範例在資料類型值為 `"Temperature"` 的任何感應器遙測事件上會評估為 True。 您可以藉由提出已驗證的 HTTP POST 要求，在使用者定義函式上建立多個比對器：

```URL
YOUR_MANAGEMENT_API_URL/matchers
```

使用 JSON 主體：

```JSON
{
  "id": "3626464-f39b-46c0-d9b0c-436aysj55",
  "name": "Temperature Matcher",
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "conditions": [
    {
      "id": "ag7gq35cfu3-e15a-4e9c-6437-sj6w68sy44s",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ]
}
```

| 值 | 更換為 |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | 裝載您執行個體的伺服器區域 |

## <a name="create-a-user-defined-function"></a>建立使用者定義的函式

建立使用者定義的函式涉及向 Azure Digital Twins 管理 API 提出多部分 HTTP 要求。

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

建立比對器之後，使用下列已驗證的多部分 HTTP POST 要求來上傳函式程式碼片段：

```URL
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

使用下列主體：

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| 值 | 更換為 |
| --- | --- |
| USER_DEFINED_BOUNDARY | 多部分內容界限名稱 |
| YOUR_SPACE_IDENTIFIER | 空間識別碼  |
| YOUR_MATCHER_IDENTIFIER | 您想要使用之比對器的識別碼 |

1. 確認標頭包含：`Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`。
1. 確認主體是由多個部分組成的：

   - 第一個部分包含必要的使用者定義函式中繼資料。
   - 第二個部分包含 JavaScript 計算邏輯。

1. 在 **USER_DEFINED_BOUNDARY** 區段中，取代 **spaceId** (`YOUR_SPACE_IDENTIFIER`) 和 **matchers**(`YOUR_MATCHER_IDENTIFIER`) 值。
1. 確認將 JavaScript 使用者定義函式作為 `Content-Type: text/javascript` 提供。

### <a name="example-functions"></a>函式範例

直接針對資料類型為 **Temperature** (也就是 `sensor.DataType`) 的感應器設定感應器遙測讀取：

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

**telemetry** 參數會公開 **SensorId** 和 **Message** 屬性 (對應到感應器所傳送的訊息)。 ExecutionContext**** 參數會公開下列屬性：

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

在下一個範例中，如果感應器遙測讀取超出預先定義的閾值，我們就會記錄訊息。 如果 Azure Digital Twins 執行個體上已啟用診斷設定，則也會轉送來自使用者定義函式的記錄：

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

如果溫度高於在預先定義的常數，下列程式碼會觸發通知：

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

如需更複雜的使用者定義函式程式碼範例，請閱讀[佔用量快速入門](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)。

## <a name="create-a-role-assignment"></a>建立角色指派

建立角色指派，讓使用者定義函式可在其下執行。 如果為使用者定義的函式沒有角色指派存在，則不會擁有適當的權限可與管理 API 互動，或者不會擁有可對圖表物件執行動作的存取權。 使用者定義函式可執行的動作是透過 Azure Digital Twins 管理 API 內的角色型存取控制所指定與定義的。 例如，您可以藉由指定特定角色或特定存取控制路徑來限制使用者定義函式的範圍。 如需詳細資訊，請參閱以[角色為基礎的存取控制](./security-role-based-access-control.md)檔。

1. 針對所有角色[查詢系統 API](./security-create-manage-role-assignments.md#retrieve-all-roles)，以取得您想要指派給使用者定義函式的角色識別碼。 提出已驗證的 HTTP GET 要求來達成此目的：

    ```URL
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   保留所需的角色識別碼。 它會以 JSON 主體屬性**roleId** （`YOUR_DESIRED_ROLE_IDENTIFIER`）的形式傳遞。

1. **objectId** （`YOUR_USER_DEFINED_FUNCTION_ID`）將會是稍早建立的使用者定義函數識別碼。
1. 藉由使用`fullpath`查詢您的`YOUR_ACCESS_CONTROL_PATH`空間來尋找**path** （）的值。
1. 複製傳回的 `spacePaths` 值。 您將會在下方用到該值。 提出已驗證的 HTTP GET 要求：

    ```URL
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | 值 | 更換為 |
    | --- | --- |
    | YOUR_SPACE_NAME | 想要使用的空間名稱 |

1. 將傳回的 `spacePaths` 值貼至 **path**，藉由提出已驗證的 HTTP POST 要求來建立使用者定義函式角色指派：

    ```URL
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    使用 JSON 主體：

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | 值 | 更換為 |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | 所需角色的識別碼 |
    | YOUR_USER_DEFINED_FUNCTION_ID | 您所要使用的使用者定義函式其識別碼 |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | 指定使用者定義函數類型的識別碼（`UserDefinedFunctionId`） |
    | YOUR_ACCESS_CONTROL_PATH | 存取控制路徑 |

>[!TIP]
> 請參閱[如何建立和管理角色指派](./security-create-manage-role-assignments.md)一文，以取得使用者定義函式管理 API 作業和端點的相關詳細資訊。

## <a name="send-telemetry-to-be-processed"></a>傳送要處理的遙測

空間智慧圖形中所定義的感應器會傳送遙測。 接著，遙測會觸發已上傳的使用者定義函式執行。 資料處理器取用遙測資料。 然後，系統會針對使用者定義函數的調用建立執行計畫。

1. 針對產生讀數的感應器擷取比對器。
1. 根據評估成功的比對器，擷取相關聯的使用者定義函式。
1. 擷取每個使用者定義函式。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Azure Digital Twins 端點](./how-to-egress-endpoints.md)以傳送事件。

- 如需 Azure Digital Twins 中路由的詳細資訊，請參閱[路由事件和訊息](./concepts-events-routing.md)。

- 檢閱[用戶端程式庫參考文件](./reference-user-defined-functions-client-library.md)。