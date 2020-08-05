---
title: 管理 Digital Twins
titleSuffix: Azure Digital Twins
description: 請參閱如何取出、更新和刪除個別的 twins 和關聯性。
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0f4d9811dc288222c0a2190805a8b052cb1ae47b
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87563920"
---
# <a name="manage-digital-twins"></a>管理 Digital Twins

您環境中的實體會以[數位 twins](concepts-twins-graph.md)來表示。 管理您的數位 twins 可能包括建立、修改和移除。 若要執行這些作業，您可以使用[**選取 api**](how-to-use-apis-sdks.md)、 [.Net (c # ) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)或[Azure 數位 Twins CLI](how-to-use-cli.md)。

本文著重于管理數位 twins;若要使用關聯性和對應項[圖形](concepts-twins-graph.md)做為整體，請參閱[*如何：使用關聯性管理*](how-to-manage-graph.md)對應項圖形。

> [!TIP]
> 所有 SDK 函數都是以同步和非同步版本提供。

## <a name="create-a-digital-twin"></a>建立數位對應項

若要建立對應項，您可以在 `CreateDigitalTwin` 服務用戶端上使用方法，如下所示：

```csharp
await client.CreateDigitalTwinAsync("myNewTwinID", initData);
```

若要建立數位對應項，您必須提供：
* 數位對應項的所需識別碼
* 您想要使用的[模型](concepts-models.md) 

（選擇性）您可以為數位對應項的所有屬性提供初始值。 

模型和初始屬性值是透過 `initData` 參數提供，這是包含相關資料的 JSON 字串。

> [!TIP]
> 建立或更新對應項之後，可能會有最多10秒的延遲，變更才會反映在[查詢](how-to-query-graph.md)中。 本文 `GetDigitalTwin` [稍後](#get-data-for-a-digital-twin)所述的 API () 不會遇到這種延遲，因此如果您需要立即回應，請使用 api 呼叫，而不是查詢來查看您新建立的 twins。 

### <a name="initialize-properties"></a>初始化屬性

對應項建立 API 會接受可序列化為對應項屬性之有效 JSON 描述的物件。 如需對應項的 JSON 格式描述，請參閱[*概念：數位 twins 和*](concepts-twins-graph.md)對應項圖形。

您可以手動建立參數物件，或使用提供的 helper 類別。 以下是每個的範例。

#### <a name="create-twins-using-manually-created-data"></a>使用手動建立的資料建立 twins

```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:com:contoso:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

#### <a name="create-twins-with-the-helper-class"></a>使用 helper 類別建立 twins

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

## <a name="get-data-for-a-digital-twin"></a>取得數位對應項的資料

您可以藉由呼叫來存取任何數位對應項的完整資料：

```csharp
object result = await client.GetDigitalTwin(id);
```

此呼叫會以 JSON 字串形式傳回對應項資料。 

> [!TIP]
> 當您使用來抓取對應項時，只會傳回至少已設定一次的屬性 `GetDigitalTwin` 。

若要使用單一 API 呼叫來取出多個 twins，請參閱[*如何：查詢*](how-to-query-graph.md)對應項圖形中的查詢 API 範例。

請考慮下列以[數位 Twins 定義語言](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)撰寫的模型 ( (DTDL) ) ，其中定義了*月球*：

```json
{
    "@id": " dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```

在月亮型別對應項上呼叫的結果可能如下所 `object result = await client.DigitalTwins.GetByIdAsync("my-moon");` 示： *Moon*

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:com:contoso:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

數位對應項的定義屬性會以數位對應項上的最上層屬性傳回。 不屬於 DTDL 定義的中繼資料或系統資訊會以前置詞傳回 `$` 。 中繼資料屬性包括：
* 此 Azure 數位 Twins 實例中數位對應項的識別碼，其為 `$dtId` 。
* `$etag`，這是由 web 伺服器指派的標準 HTTP 欄位
* 區段中的其他屬性 `$metadata` 。 其中包括：
    - 數位對應項的模型 DTMI。
    - 每個可寫入屬性的同步處理狀態。 這對裝置而言最為有用，因為在此情況下，服務和裝置可能會有 (的發散狀態，例如，當裝置離線) 時。 目前，此屬性僅適用于連接到 IoT 中樞的實體裝置。 使用中繼資料區段中的資料，您可以瞭解屬性的完整狀態，以及上次修改的時間戳記。 如需有關同步處理狀態的詳細資訊，請參閱[此 IoT 中樞](../iot-hub/tutorial-device-twins.md)有關同步處理裝置狀態的教學課程。
    - 服務特定的中繼資料，例如 IoT 中樞或 Azure 數位 Twins。 

您可以使用所選的 JSON 剖析程式庫（例如），剖析對應項的傳回 JSON `System.Text.Json` 。

您也可以使用 SDK 隨附的序列化協助 `BasicDigitalTwin` 程式類別，它會以預先剖析的形式傳回核心對應項中繼資料和屬性。 範例如下：

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

如需更多有關序列化協助程式類別的資訊，[*請參閱如何：使用 Azure 數位 Twins api 和 sdk*](how-to-use-apis-sdks.md)。

## <a name="update-a-digital-twin"></a>更新數位分身

若要更新數位對應項的屬性，您可以撰寫要以[JSON 修補程式](http://jsonpatch.com/)格式取代的資訊。 如此一來，您就可以一次取代多個屬性。 接著，您會將 JSON 修補檔傳遞至 `Update` 方法：

```csharp
await client.UpdateDigitalTwin(id, patch);
```

> [!TIP]
> 建立或更新對應項之後，可能會有最多10秒的延遲，變更才會反映在[查詢](how-to-query-graph.md)中。 本文稍 `GetDigitalTwin`) [早](#get-data-for-a-digital-twin)所述的 API (不會遇到這種延遲，因此如果您需要立即回應，請使用 api 呼叫，而不是查詢來查看您剛更新的 twins。 

以下是 JSON Patch 程式碼的範例。 本檔會取代套用的數位對應項的「*大*」和「*半徑*」屬性值。

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```

您可以手動建立修補程式，或使用[SDK](how-to-use-apis-sdks.md)中的序列化 helper 類別。 以下是每個的範例。

#### <a name="create-patches-manually"></a>手動建立修補程式
```csharp
List<object> twinData = new List<object>();
twinData.Add(new Dictionary<string, object>() {
    { "op", "add"},
    { "path", "/Temperature"},
    { "value", 25.0}
});

await client.UpdateDigitalTwinAsync(twinId, JsonConvert.SerializeObject(twinData));
```

#### <a name="create-patches-using-the-helper-class"></a>使用 helper 類別建立修補程式

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>更新數位對應項元件中的屬性

回想一下，模型可能包含元件，讓它可由其他模型組成。 

若要修補數位對應項元件中的屬性，您將使用 JSON 修補程式中的路徑語法：

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>更新數位對應項的模型

`Update`函數也可以用來將數位對應項遷移至不同的模型。 

例如，請考慮下列會取代數位對應項的元資料欄位的 JSON 修補程式檔 `$model` ：

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:contoso:foo;1"
  }
]
```

只有當修補程式修改的數位對應項符合新的模型時，這項作業才會成功。 

請考慮下列範例：
1. 想像一下具有*foo_old*模型的數位對應項。 *foo_old*定義必要的屬性*品質*。
2. 新的模型*foo_new*會定義大量屬性，並新增必要的屬性*溫度*。
3. 在修補程式之後，數位對應項必須同時具有「大量」和「溫度」屬性。 

這種情況的修補程式需要更新模型和對應項的溫度屬性，如下所示：

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:com:contoso:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

## <a name="delete-a-digital-twin"></a>刪除數位對應項

您可以使用刪除 twins `DeleteDigitalTwin(ID)` 。 不過，當對應項沒有其他關聯性時，您只能刪除對應項。 您必須先刪除所有關聯性。 

以下是此程式碼的範例：

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);

        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipssAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

### <a name="delete-all-digital-twins"></a>刪除所有數位 twins

如需如何一次刪除所有 twins 的範例，請下載[*教學課程：使用範例用戶端應用程式探索基本概念*](tutorial-command-line-app.md)中所使用的範例應用程式。 *CommandLoop.cs*檔案會在函式中執行這項 `CommandDeleteAllTwins` 工作。

## <a name="manage-twins-with-cli"></a>使用 CLI 管理 twins

您也可以使用 Azure 數位 Twins CLI 來管理 Twins。 您可以在[*如何：使用 Azure 數位 TWINS CLI*](how-to-use-cli.md)中找到這些命令。

## <a name="next-steps"></a>後續步驟

瞭解如何建立及管理您的數位 twins 之間的關聯性：
* [*如何：使用關聯性管理對應項圖形*](how-to-manage-graph.md)