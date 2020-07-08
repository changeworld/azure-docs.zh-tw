---
title: 管理具有關聯性的對應項圖形
titleSuffix: Azure Digital Twins
description: 請參閱如何使用關聯性連接以管理數位 twins 的圖表。
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bfdf1263ccee78b57ccf79c63efcc01d95dd13c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392245"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>使用關聯性管理數位 twins 的圖表

Azure 數位 Twins 的核心是代表整個環境的對應項[圖形](concepts-twins-graph.md)。 對應項圖形是由透過**關聯**性連線的個別數位 twins 所組成。

當您擁有運作中的[Azure 數位 Twins 實例](how-to-set-up-instance.md)並已設定用戶端應用程式的[驗證](how-to-authenticate-client.md)之後，您就可以使用[**選取 api**](how-to-use-apis-sdks.md)來建立、修改和刪除數位 Twins 及其在 Azure 數位 Twins 實例中的關聯性。 您也可以使用[.net （c #） SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)或[AZURE 數位 Twins CLI](how-to-use-cli.md)。

本文著重于整體管理關聯性和圖形;若要使用個別的數位 twins，請參閱[如何：管理數位 twins](how-to-manage-twin.md)。

## <a name="create-relationships"></a>建立關聯性

關聯性描述不同的數位 twins 如何彼此連接，這會形成對應項圖形的基礎。

關聯性是使用呼叫所建立 `CreateRelationship` 。 

若要建立關聯性，您必須指定：
* 來源對應項識別碼（關聯性的來源對應項）
* 目標對應項識別碼（關聯性到達的對應項）
* 關聯性名稱
* 關聯性識別碼

關聯性識別碼在指定的來源對應項內必須是唯一的。 它不需要是全域唯一的。
例如，針對對應項*foo*，每個特定的關聯性識別碼都必須是唯一的。 不過，另一個對應*項可能會*有與*foo*關聯性相同識別碼的傳出關聯性。 

下列程式碼範例說明如何將關聯性新增至您的 Azure 數位 Twins 實例。

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

如需協助程式類別的詳細資訊 `BasicRelationship` ，請參閱[如何：使用 Azure 數位 Twins Api 和 sdk](how-to-use-apis-sdks.md)。

## <a name="list-relationships"></a>列出關聯性

若要存取圖形中給定對應項的關聯性清單，您可以使用：

```csharp
await client.GetRelationshipsAsync(id);
```

這會傳回 `Azure.Pageable<T>` 或 `Azure.AsyncPageable<T>` ，視您使用的是同步或非同步呼叫版本而定。

以下是可抓取關聯性清單的完整範例：

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

您可以使用抓取的關聯性，流覽至圖形中的其他 twins。 若要這樣做，請 `target` 從傳回的關聯性中讀取欄位，並使用它做為下一個呼叫的識別碼 `GetDigitalTwin` 。 

### <a name="find-relationships-to-a-digital-twin"></a>尋找數位對應項的關聯性

Azure 數位 Twins 也有一個 API，可尋找給定對應項的所有連入關聯性。 這通常適用于反向導覽，或刪除對應項時。

先前的程式碼範例著重于尋找連出關聯性。 下列範例類似，但會尋找連入關聯性。 它也會在找到之後將它們刪除。

請注意，IncomingRelationship 呼叫不會傳回完整的

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

第一個參數指定來源對應項（關聯性的來源對應項）。 另一個參數是關聯性識別碼。 您需要對應項識別碼和關聯性識別碼，因為關聯性識別碼只在對應項的範圍內是唯一的。

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

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>從試算表建立對應項圖形

在實際使用案例中，對應項階層通常會從儲存在不同資料庫中的資料，或在試算表中建立。 本節說明如何剖析試算表。

請考慮下列資料表，其中描述要建立的一組數位 twins 和關聯性。

| 型號    | 識別碼 | 父系 | 關聯性名稱 | 其他資料 |
| --- | --- | --- | --- | --- |
| floor    | Floor01 | | | … |
| 房間    | Room10 | Floor01 | 包含 | … |
| 房間    | Room11 | Floor01 | 包含 | … |
| 房間    | Room12 | Floor01 | 包含 | … |
| floor    | Floor02 | | | … |
| 房間    | Room21 | Floor02 | 包含 | … |
| 房間    | Room22 | Floor02 | 包含 | … |

下列程式碼會使用[MICROSOFT GRAPH API](https://docs.microsoft.com/graph/overview)來讀取試算表，並從結果中建立 Azure 數位 Twins 對應項圖形。

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

您也可以使用 Azure 數位 Twins CLI 來管理 Twins 及其關聯性。 您可以在[如何：使用 Azure 數位 TWINS CLI](how-to-use-cli.md)中找到這些命令。

## <a name="next-steps"></a>後續步驟

深入瞭解查詢 Azure 數位 Twins 對應項圖形：
* [概念：查詢語言](concepts-query-language.md)
* [如何：查詢對應項圖形](how-to-query-graph.md)