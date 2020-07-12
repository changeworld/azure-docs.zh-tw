---
title: 數位分身和對應項圖形
titleSuffix: Azure Digital Twins
description: 瞭解數位對應項的概念，以及它們之間的關聯性如何製作圖表。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 955a3b8d12eb3b93bc9d44c624953cd5c1007318
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258210"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>瞭解數位 twins 及其對應項圖形

在 Azure 數位 Twins 解決方案中，您環境中的實體會由 Azure**數位 Twins**表示。 數位對應項是其中一個自訂[模型](concepts-models.md)的實例。 它可以透過**關聯**性連線到其他數位 twins 來形成對應項**圖形**：此對應項圖形是您整個環境的標記法。

> [!TIP]
> 「Azure 數位 Twins」是指整個 Azure 服務。 「數位對應項 (s) 」或只是「對應項 (s) 」是指服務實例內的個別對應項節點。

## <a name="digital-twins"></a>數位 twins

在您的 Azure 數位 Twins 實例中建立數位對應項之前，您必須先將*模型*上傳至服務。 模型描述特定對應項可以擁有的一組屬性、遙測訊息和關聯性，還有其他專案。 如需模型中所定義的資訊類型，請參閱[概念：自訂模型](concepts-models.md)。

建立和上傳模型之後，您的用戶端應用程式可以建立類型的實例。這是數位對應項。 例如，建立*樓層*的模型之後，您可以建立一或數個使用此類型的數位 twins， (像是名為*GroundFloor*的*Floor*類型對應項、另一個稱為*Floor2*等，) 。 

## <a name="relationships-a-graph-of-digital-twins"></a>關聯性：數位 twins 的圖表

Twins 會依其關聯性連接至對應項圖形。 對應項可以擁有的關聯性會定義為其模型的一部分。  

例如，模型*樓層*可能會定義*contains*關聯性，其目標為*聊天室*類型的 twins。 使用此定義，Azure 數位 Twins 可讓您建立*包含*來自任何*樓層*對應項的關聯性到任何*房間*對應項 (包括) 的*房間*子類型的 Twins。 

此程式的結果是一組節點 (數位 twins) 透過邊緣連線， (它們在圖形中) 關聯性。

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-with-the-apis"></a>使用 Api 建立

本節說明從用戶端應用程式建立數位 twins 和關聯性的樣子。 其中包含的 .NET 程式碼範例會利用[選取 api](how-to-use-apis-sdks.md)，以提供有關每個概念內的其他內容。

### <a name="create-digital-twins"></a>建立數位分身

以下是使用[選取 api](how-to-use-apis-sdks.md)來具現化類型*室*之對應項的用戶端程式代碼程式碼片段。

在目前的 Azure 數位 Twins 預覽中，對應項的所有屬性都必須先初始化，然後才能建立對應項。 這是藉由建立 JSON 檔來提供必要的初始化值來完成。

```csharp
public Task<boolean> CreateRoom(string id, double temperature, double humidity) 
{
    // Define the model for the twin to be created
    Dictionary<string, object> meta = new Dictionary<string, object>()
    {
      { "$model", "dtmi:com:contoso:Room;2" }
    };
    // Initialize the twin properties
    Dictionary<string, object> initData = new Dictionary<string, object>()
    {
      { "$metadata", meta },
      { "Temperature", temperature},
      { "Humidity", humidity},
    };
    try
    {
      await client.DigitalTwins.AddAsync(id, initData);
      return true;
    }
    catch (ErrorResponseException e)
    {
      Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}");
      return false;
    }
}
```

### <a name="create-relationships"></a>建立關聯性

以下是一些範例用戶端程式代碼，它會使用[選取 api](how-to-use-apis-sdks.md) ，在名為*GroundFloor*的*Floor*類型數位對應項和名為*咖啡廳*的*房間*類型數位對應項之間建立關聯性。

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

## <a name="json-representations-of-graph-elements"></a>圖表元素的 JSON 標記法

數位對應項資料和關聯性資料都是以 JSON 格式儲存。 這表示當您在 Azure 數位 Twins 實例中查詢對應項[圖形](how-to-query-graph.md)時，其結果會是您所建立之數位 Twins 和關聯性的 JSON 標記法。

### <a name="digital-twin-json-format"></a>數位對應項 JSON 格式

以 JSON 物件表示時，數位對應項會顯示下欄欄位：

| 欄位名稱 | 描述 |
| --- | --- |
| `$dtId` | 使用者提供的字串，代表數位對應項的識別碼 |
| `$etag` | Web 服務器指派的標準 HTTP 欄位 |
| `$conformance` | 列舉，包含這個數位對應項的一致性狀態 (*一致*、*不一致*、*未知*的)  |
| `{propertyName}` | JSON 中的屬性值 (`string` 、數位類型或物件)  |
| `$relationships` | 關聯性集合的路徑 URL。 如果數位對應項沒有連出關聯性邊緣，則此欄位不存在。 |
| `$metadata.$model` | 選擇性表示此數位對應項之模型介面的識別碼 |
| `$metadata.{propertyName}.desiredValue` | [僅適用于可寫入屬性]所需的指定屬性值 |
| `$metadata.{propertyName}.desiredVersion` | [僅適用于可寫入屬性]所需值的版本 |
| `$metadata.{propertyName}.ackVersion` | 執行數位對應項的裝置應用程式所認可的版本 |
| `$metadata.{propertyName}.ackCode` | [僅適用于可寫入屬性]`ack`執行數位對應項的裝置應用程式所傳回的代碼 |
| `$metadata.{propertyName}.ackDescription` | [僅適用于可寫入屬性]`ack`執行數位對應項的裝置應用程式所傳回的描述 |
| `{componentName}` | JSON 物件，其中包含元件的屬性值和中繼資料，類似于根物件。 即使元件沒有屬性，這個物件仍存在。 |
| `{componentName}.{propertyName}` | 元件在 JSON 中的屬性值 (`string` 、數位類型或物件)  |
| `{componentName}.$metadata` | 元件的中繼資料資訊，類似于根層級`$metadata` |

以下是格式化為 JSON 物件的數位對應項範例：

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
| `{propertyName}` | 選擇性此關聯性的屬性值，以 JSON (`string` 、數位類型或物件)  |

以下是格式化為 JSON 物件之關聯性的範例：

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

請參閱如何使用 Azure 數位對應項 Api 管理圖形元素：
* [如何：管理數位 twins](how-to-manage-twin.md)
* [如何：使用關聯性管理對應項圖形](how-to-manage-graph.md)

或者，深入瞭解查詢 Azure 數位 Twins 對應項圖形以取得資訊：
* [概念：查詢語言](concepts-query-language.md)