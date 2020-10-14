---
title: 管理具有關聯性的對應項圖形
titleSuffix: Azure Digital Twins
description: 瞭解如何藉由與關聯性連接來管理數位 twins 的圖形。
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8c698cdf5b26cb1682eec2828922517cf4272275
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048435"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>使用關聯性管理數位 twins 的圖形

Azure 數位 Twins 的核心是代表整個環境的對應項 [圖形](concepts-twins-graph.md) 。 對應項圖形是由透過 **關聯**性連接的個別數位 twins 所組成。

一旦您有運作中的 [Azure 數位 Twins 實例](how-to-set-up-instance-portal.md) ，並已在用戶端應用程式中設定 [驗證](how-to-authenticate-client.md) 程式碼，您就可以使用 [**DigitalTwins api**](how-to-use-apis-sdks.md) 來建立、修改和刪除 Azure 數位 Twins 實例中的數位 Twins 及其關聯性。 您也可以使用 [.net (c # ) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)或 [AZURE 數位 Twins CLI](how-to-use-cli.md)。

本文著重于管理關聯性和整個圖形;若要使用個別的數位 twins，請參閱 how [*to：管理數位 twins*](how-to-manage-twin.md)。

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>建立關聯性

關聯性會說明不同數位 twins 彼此連接的方式，以構成對應項圖形的基礎。

關聯性是使用呼叫建立的 `CreateRelationship` 。 

若要建立關聯性，您必須指定：
* 來源對應項識別碼 (關聯性源自的對應項) 
* 目標對應項識別碼 (關聯性抵達的對應項) 
* 關聯性名稱
* 關聯性識別碼

關聯性識別碼在給定的來源對應項內必須是唯一的。 它不需要是全域唯一的。
例如，針對對應項 *foo*，每個特定的關聯性識別碼都必須是唯一的。 但是，另一個對應項 *列可以有* 符合 *foo* 關聯性相同識別碼的外寄關聯性。 

下列程式碼範例說明如何將關聯性新增至 Azure 數位 Twins 實例。

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

如需 helper 類別的詳細資訊 `BasicRelationship` ，請參閱作法 [*：使用 Azure 數位 Twins Api 和 sdk*](how-to-use-apis-sdks.md)。

### <a name="create-multiple-relationships-between-twins"></a>在 twins 之間建立多個關聯性

您可以有兩個 twins 之間的關聯性數目沒有限制，您可以視需要在 twins 之間擁有任意數量的關聯性。 

這表示您可以一次表達兩個 twins 之間的幾種不同類型的關聯性。 例如，對應項*a*可以同時具有*預存*關聯性和對應項*B*的*製造*關聯性。

您甚至可以在相同的兩個 twins 之間建立多個相同類型之關聯性的實例（如有需要）。 在此範例中，這表示對應項 *a* 可以有兩個不同的 *預存* 關聯性與對應項 *B*。

## <a name="list-relationships"></a>列出關聯性

若要存取來自圖形中指定對應項的 **傳出** 關聯性清單，您可以使用：

```csharp
await client.GetRelationshipsAsync(id);
```

這會傳回 `Azure.Pageable<T>` 或 `Azure.AsyncPageable<T>` ，視您使用的是同步或非同步版本的呼叫而定。

以下是完整的範例，可供您抓取關聯性清單：

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

您可以使用已抓取的關聯性來流覽至您圖形中的其他 twins。 若要這樣做，請 `target` 從傳回的關聯性中讀取欄位，然後使用它做為下一個呼叫的識別碼 `GetDigitalTwin` 。 

### <a name="find-incoming-relationships-to-a-digital-twin"></a>尋找數位對應項的連入關聯性

Azure 數位 Twins 也有 API 可尋找指定對應項的所有連 **入** 關聯性。 這通常適用于反向導覽或刪除對應項。

先前的程式碼範例著重于尋找對應項的傳出關聯性。 下列範例的結構類似，但會改為尋找對應項的連 *入* 關聯性。

請注意， `IncomingRelationship` 呼叫不會傳回關聯性的完整主體。

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>刪除關聯性

您可以使用刪除關聯性 `DeleteRelationship(source, relId);` 。

第一個參數會指定來源對應項 (關聯性源自) 的對應項。 另一個參數是關聯性識別碼。 您需要對應項識別碼和關聯性識別碼，因為關聯性識別碼只有在對應項的範圍內是唯一的。

## <a name="create-a-twin-graph"></a>建立對應項圖形 

下列程式碼片段會使用本文中的關聯性作業，從數位 twins 和關聯性建立對應項圖形。

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>從試算表建立對應項圖表

在實際使用案例中，對應項階層通常會從儲存在不同資料庫中的資料建立，或可能在試算表中建立。 本節說明如何剖析試算表。

請考慮下列資料表，其中描述一組要建立的數位 twins 和關聯性。

| 模型    | 識別碼 | 父系 | 關聯性名稱 | 其他資料 |
| --- | --- | --- | --- | --- |
| floor    | Floor01 | | | … |
| 房間    | Room10 | Floor01 | contains | … |
| 房間    | Room11 | Floor01 | contains | … |
| 房間    | Room12 | Floor01 | contains | … |
| floor    | Floor02 | | | … |
| 房間    | Room21 | Floor02 | contains | … |
| 房間    | Room22 | Floor02 | contains | … |

下列程式碼會使用 [MICROSOFT GRAPH API](/graph/overview) 來讀取試算表，並從結果中建立 Azure 數位 Twins 對應項圖形。

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>使用 CLI 管理關聯性

您也可以使用 Azure 數位 Twins CLI 來管理 Twins 及其關聯性。 您可以在 how [*to：使用 Azure 數位 TWINS CLI*](how-to-use-cli.md)中找到這些命令。

## <a name="next-steps"></a>後續步驟

瞭解如何查詢 Azure 數位 Twins 對應項圖形：
* [*概念：查詢語言*](concepts-query-language.md)
* [*How to：查詢對應項圖形*](how-to-query-graph.md)