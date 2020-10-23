---
title: 數位分身和對應項圖形
titleSuffix: Azure Digital Twins
description: 瞭解數位對應項的概念，以及它們的關聯性如何建立圖形。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c41ffcd552cddf981c2ed54d1d78c7cb2e8698c5
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440822"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>瞭解數位 twins 及其對應項圖表

在 Azure 數位 Twins 解決方案中，您環境中的實體會由 Azure **數位 Twins**表示。 數位對應項是您其中一個自訂 [模型](concepts-models.md)的實例。 您可以透過 **關聯** 性連接到其他數位 twins 來形成對應項 **圖形**：此對應項圖形是您整個環境的標記法。

> [!TIP]
> 「Azure 數位 Twins」指的是此一整體的 Azure 服務。 「數位對應項 (s) 」或只是「對應項 (s) 」指的是服務實例內的個別對應項節點。

## <a name="digital-twins"></a>Digital Twins

您必須先將 *模型* 上傳至服務，才可以在 Azure 數位 Twins 實例中建立數位對應項。 模型描述特定對應項可以有的屬性、遙測訊息和關聯性集合，還有其他專案。 針對模型中所定義的資訊類型，請參閱 [*概念：自訂模型*](concepts-models.md)。

建立並上傳模型之後，您的用戶端應用程式可以建立類型的實例;這是數位對應項。 例如，在建立*樓層*的模型之後，您可以建立一或多個使用此 (類型的數位 twins，例如名為*GroundFloor*、另一個稱為 ) *Floor2*等的*樓層*類型對應項。 

## <a name="relationships-a-graph-of-digital-twins"></a>關聯性：數位 twins 的圖形

Twins 會依關聯性連接到對應項圖形。 對應項可以有的關聯性會定義為其模型的一部分。  

例如，模型 *樓層* 可能會定義一個 *包含* twins 型別 *房間*的關聯性。 使用此定義時，Azure 數位 Twins 可讓您建立*包含*任何*樓層*對應項的關聯性*Room* ， (包括) *房間*子類型的 Twins。 

這項程式的結果是一組節點， (數位 twins) 透過邊緣連線， (其在圖形中) 的關聯性。

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-with-the-apis"></a>使用 Api 建立

本節說明從用戶端應用程式建立數位 twins 和關聯性的外觀。 它包含使用 [DigitalTwins api](/rest/api/digital-twins/dataplane/twins)的 .net 程式碼範例，以提供有關每個概念內各項功能的其他內容。

### <a name="create-digital-twins"></a>建立數位分身

以下是使用 [DigitalTwins api](/rest/api/digital-twins/dataplane/twins) 來具現化類型 *空間*對應項的用戶端程式代碼程式碼片段。

在目前的 Azure 數位 Twins 預覽中，對應項的所有屬性都必須先初始化，才能建立對應項。 這是藉由建立提供必要初始化值的 JSON 檔來完成。

[!INCLUDE [Azure Digital Twins code: create twin](../../includes/digital-twins-code-create-twin.md)]

您也可以使用稱為的 helper 類別 `BasicDigitalTwin` ，更直接將屬性欄位儲存在「對應項」物件中，做為使用字典的替代方法。 如需協助程式類別及其用法範例的詳細資訊，請參閱*如何：管理數位 twins*的[*建立數位*](how-to-manage-twin.md#create-a-digital-twin)對應項一節。

### <a name="create-relationships"></a>建立關聯性

以下是使用[DigitalTwins api](/rest/api/digital-twins/dataplane/twins)的一些範例用戶端程式代碼，以建立名為*GroundFloor*的*樓層*型數位對應項和名為*咖啡廳*的*房間*型數位對應項之間的關聯性。

```csharp
// Create Twins, using functions similar to the previous sample
await CreateRoom("Cafe", 70, 66);
await CreateFloor("GroundFloor", averageTemperature=70);
// Create relationships
Dictionary<string, object> targetrec = new Dictionary<string, object>()
{
    { "$targetId", "Cafe" }
};
try
{
    await client.DigitalTwins.AddEdgeAsync("GroundFloor", "contains", "GF-to-Cafe", targetrec);
} catch(ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating relationship: {e.Response.StatusCode}");
}
```

## <a name="json-representations-of-graph-elements"></a>圖形元素的 JSON 標記法

數位對應項資料和關聯性資料都是以 JSON 格式儲存。 這表示當您在 Azure 數位 Twins 實例中查詢對應項 [圖形](how-to-query-graph.md) 時，結果會是數位 TWINS 的 JSON 標記法和您已建立的關聯性。

### <a name="digital-twin-json-format"></a>數位對應項 JSON 格式

以 JSON 物件表示時，數位對應項會顯示下欄欄位：

| 欄位名稱 | 描述 |
| --- | --- |
| `$dtId` | 使用者提供的字串，代表數位對應項的識別碼 |
| `$etag` | Web 服務器指派的標準 HTTP 欄位 |
| `$conformance` | 列舉，其中包含此數位對應項的符合性狀態， (*符合*規範且 *不符合*規範的 *未知*)  |
| `{propertyName}` | JSON (`string` 、數位類型或物件中的屬性值)  |
| `$relationships` | 關聯性集合路徑的 URL。 如果數位對應項沒有外寄關聯性邊緣，則此欄位不存在。 |
| `$metadata.$model` | 參數此數位對應項之特性的模型介面識別碼 |
| `$metadata.{propertyName}.desiredValue` | [僅適用于可寫入的屬性]指定之屬性的所需值 |
| `$metadata.{propertyName}.desiredVersion` | [僅適用于可寫入的屬性]所需值的版本 |
| `$metadata.{propertyName}.ackVersion` | 執行數位對應項的裝置應用程式所認可的版本 |
| `$metadata.{propertyName}.ackCode` | [僅適用于可寫入的屬性] `ack` 執行數位對應項的裝置應用程式所傳回的程式碼 |
| `$metadata.{propertyName}.ackDescription` | [僅適用于可寫入的屬性] `ack` 執行數位對應項的裝置應用程式所傳回的描述 |
| `{componentName}` | JSON 物件，其中包含元件的屬性值和中繼資料，類似于根物件的值。 即使元件沒有屬性，此物件仍然存在。 |
| `{componentName}.{propertyName}` | 元件在 JSON (`string` 、數位類型或物件中的屬性值)  |
| `{componentName}.$metadata` | 元件的中繼資料資訊，類似于根層級 `$metadata` |

以下是數位對應項格式化為 JSON 物件的範例：

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>關聯性 JSON 格式

以 JSON 物件表示時，數位對應項的關聯性會顯示下欄欄位：

| 欄位名稱 | 描述 |
| --- | --- |
| `$relationshipId` | 使用者提供的字串，代表此關聯性的識別碼。 此字串在來源數位對應項的內容中是唯一的，這也表示在 `sourceId`  +  `relationshipId` Azure 數位 Twins 實例的內容中是唯一的。 |
| `$etag` | Web 服務器指派的標準 HTTP 欄位 |
| `$sourceId` | 來源數位對應項的識別碼 |
| `$targetId` | 目標數位對應項的識別碼 |
| `$relationshipName` | 關聯性的名稱 |
| `{propertyName}` | 參數此關聯性的屬性值，以 JSON (`string` 、數位類型或物件)  |

以下是格式化為 JSON 物件的關聯性範例：

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>後續步驟

瞭解如何使用 Azure 數位對應項 Api 管理圖形元素：
* [*How to：管理數位 twins*](how-to-manage-twin.md)
* [*How to：使用關聯性管理對應項圖表*](how-to-manage-graph.md)

或者，您也可瞭解如何查詢 Azure 數位 Twins 對應項圖形以取得相關資訊：
* [*概念：查詢語言*](concepts-query-language.md)