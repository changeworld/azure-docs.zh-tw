---
title: 管理具有關聯性的對應項圖形
titleSuffix: Azure Digital Twins
description: 瞭解如何藉由與關聯性連接來管理數位 twins 的圖形。
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 947a0c9a0af3c38d6c4d6f66da691d62530a69e7
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93279506"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>使用關聯性管理數位 twins 的圖形

Azure 數位 Twins 的核心是代表整個環境的對應項 [圖形](concepts-twins-graph.md) 。 對應項圖形是由透過 **關聯** 性連接的個別數位 twins 所組成。 

一旦您有運作中的 [Azure 數位 Twins 實例](how-to-set-up-instance-portal.md) ，並已在用戶端應用程式中設定 [驗證](how-to-authenticate-client.md) 程式碼，您就可以使用 [**DigitalTwins api**](/rest/api/digital-twins/dataplane/twins) 來建立、修改和刪除 Azure 數位 Twins 實例中的數位 Twins 及其關聯性。 您也可以使用 [.net (c # ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)或 [AZURE 數位 Twins CLI](how-to-use-cli.md)。

本文著重于管理關聯性和整個圖形;若要使用個別的數位 twins，請參閱 how [*to：管理數位 twins*](how-to-manage-twin.md)。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]
    
[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>建立關聯性

關聯性會說明不同數位 twins 彼此連接的方式，以構成對應項圖形的基礎。

關聯性是使用呼叫建立的 `CreateRelationship()` 。 

若要建立關聯性，您必須指定：
* 下列程式碼範例中的來源對應項識別碼 (`srcId`) ：關聯性來源之對應項的識別碼。
* 下列程式碼範例中的目標對應項識別碼 (`targetId`) ：關聯性抵達之對應項的識別碼。
* 下列程式碼範例中 (的關聯性名稱 `relName`) ：關聯性的泛型型別，類似于 _contains_ 。
* 下列程式碼範例中 (的關聯性識別碼 `relId`) ：此關聯性的特定名稱，類似 _Relationship1_ 。

關聯性識別碼在給定的來源對應項內必須是唯一的。 它不需要是全域唯一的。
例如，針對對應項 *foo* ，每個特定的關聯性識別碼都必須是唯一的。 但是，另一個對應項 *列可以有* 符合 *foo* 關聯性相同識別碼的外寄關聯性。

下列程式碼範例說明如何在您的 Azure 數位 Twins 實例中建立關聯性。

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
在您的 main 方法中，您現在可以呼叫函式 `CreateRelationship()` 來建立 _包含_ 關聯性的關聯性，如下所示： 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
如果您想要建立多個關聯性，您可以重複呼叫相同的方法，將不同的關聯性類型傳遞給引數。 

如需 helper 類別的詳細資訊 `BasicRelationship` ，請參閱作法 [*：使用 Azure 數位 Twins Api 和 sdk*](how-to-use-apis-sdks.md#serialization-helpers)。

### <a name="create-multiple-relationships-between-twins"></a>在 twins 之間建立多個關聯性

關聯性可分類為： 

* 外寄關聯性：屬於這個對應項的關聯性，指向外部，以連接到其他 twins。 `GetRelationshipsAsync()`方法是用來取得對應項的外寄關聯性。
* 連入關聯性：屬於其他 twins 的關聯性，指向此對應項以建立「連入」連結。 `GetIncomingRelationshipsAsync()`方法是用來取得對應項的傳入關聯性。

您可以有兩個 twins 之間的關聯性數目沒有限制，您可以視需要在 twins 之間擁有任意數量的關聯性。 

這表示您可以一次表達兩個 twins 之間的幾種不同類型的關聯性。 例如，對應項 *a* 可以同時具有 *預存* 關聯性和對應項 *B* 的 *製造* 關聯性。

您甚至可以在相同的兩個 twins 之間建立多個相同類型之關聯性的實例（如有需要）。 在此範例中，對應項 *A* 可以有兩個與對應項 *B* 不同的 *預存* 關聯性，只要關聯性具有不同的關聯性識別碼即可。

## <a name="list-relationships"></a>列出關聯性

若要存取圖形中指定對應項的 **外寄** 關聯性清單，您可以使用 `GetRelationships()` 如下所示的方法：

```csharp
await client.GetRelationships()
```

這會傳回 `Azure.Pageable<T>` 或 `Azure.AsyncPageable<T>` ，取決於您使用的是同步或非同步版本的呼叫。

以下是可抓取關聯性清單的範例：

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
您現在可以呼叫這個方法來查看 twins 的傳出關聯性，如下所示：

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
您可以使用已抓取的關聯性來流覽至您圖形中的其他 twins。 若要這樣做，請 `target` 從傳回的關聯性中讀取欄位，然後使用它做為下一個呼叫的識別碼 `GetDigitalTwin()` 。

### <a name="find-incoming-relationships-to-a-digital-twin"></a>尋找數位對應項的連入關聯性

Azure 數位 Twins 也有 API 可尋找指定對應項的 *所有 _ 內* 送 * 關聯性。 這通常適用于反向導覽或刪除對應項。

先前的程式碼範例著重于尋找對應項的傳出關聯性。 下列範例的結構類似，但會改為尋找對應項的連 *入* 關聯性。

請注意， `IncomingRelationship` 呼叫不會傳回關聯性的完整主體。

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

您現在可以呼叫這個方法來查看 twins 的連入關聯性，如下所示：

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>列出所有對應項屬性和關聯性

使用上述方法來列出對應項的外寄和內送關聯性，您可以建立會列印完整對應項資訊的方法，包括對應項的屬性，以及這兩種類型的關聯性。 以下是稱為的範例方法，示範 `FetchAndPrintTwinAsync()` 如何進行這項作業。

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

您現在可以在 main 方法中呼叫此函式，如下所示： 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>刪除關聯性

第一個參數會指定來源對應項 (關聯性源自) 的對應項。 另一個參數是關聯性識別碼。 您需要對應項識別碼和關聯性識別碼，因為關聯性識別碼只有在對應項的範圍內是唯一的。

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

您現在可以呼叫這個方法來刪除關聯性，如下所示：

```csharp
await DeleteRelationship(client, srcId, relId);
```
## <a name="create-a-twin-graph"></a>建立對應項圖形 

下列可執行檔程式碼片段會使用本文中的關聯性作業，從數位 twins 和關聯性建立對應項圖形。

### <a name="set-up-the-runnable-sample"></a>設定可執行檔範例

此程式碼片段會在教學課程中使用模型定義上的 [*Room.json*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) 和 [*Floor.js*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) [*：探索 Azure 數位 Twins 與範例用戶端應用程式*](tutorial-command-line-app.md)。 您可以使用這些連結直接移至檔案，或在 [這裡](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)將其下載為完整的端對端範例專案的一部分。 

執行範例之前，請執行下列動作：
1. 下載模型檔案，將它們放在您的專案中，並取代 `<path-to>` 下列程式碼中的預留位置，告訴您的程式要在哪裡找到它們。
2. 將預留位置取代 `<your-instance-hostname>` 為您的 Azure 數位 Twins 實例的主機名稱。
3. 將這些套件新增至您的專案：
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

如果您想要直接執行範例，您也必須設定本機認證。 下一節將逐步解說這一點。
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>執行範例

完成上述步驟之後，您可以直接執行下列範例程式碼。

```csharp 
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(srcId, floorTwin);
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.Contents = props;
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(targetId, roomTwin);
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");
                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
    }
}
```

以下是上述程式的主控台輸出： 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="顯示對應項詳細資料的主控台輸出、twins 的連入和連出關聯性。" lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> 對應項圖形是在 twins 之間建立關聯性的概念。 如果您想要查看對應項圖形的視覺標記法，請參閱本文的 [_Visualization *](how-to-manage-graph.md#visualization) 一節。 

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>從試算表建立對應項圖表

在實際使用案例中，對應項階層通常會從儲存在不同資料庫中的資料建立，或可能在試算表中建立。 本節說明如何剖析試算表。

請考慮下列資料表，其中描述一組要建立的數位 twins 和關聯性。

| 模型識別碼| 對應項識別碼 (必須是唯一的)  | 關聯性名稱 | 目標對應項識別碼 | 對應項初始化資料 |
| --- | --- | --- | --- | --- |
| dtmi：範例： Floor; 1 | Floor1 |  包含 | Room1 |{"溫度"：80，"濕度"： 60}
| dtmi：範例： Floor; 1 | Floor0 |  has      | Room0 |{"溫度"：70，"濕度"： 30}
| dtmi：範例：房間; 1  | Room1 | 
| dtmi：範例：房間; 1  | Room0 |

下列程式碼會使用 [MICROSOFT GRAPH API](/graph/overview) 來讀取試算表，並從結果中建立 Azure 數位 Twins 對應項圖形。

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string modelId = row[0].GetString();
    string sourceId = row[1].GetString();
    string relName = row[2].GetString();
    string targetId = row[3].GetString();
    string initData = row[4].GetString();
    
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (sourceId != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = sourceId,
            TargetId = targetId,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Contents = initData;
    // Set the type of twin to be created
    twin.Metadata = new DigitalTwinMetadata() { ModelId = modelId };
    
    try
    {
        await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(sourceId, twin);
    }
    catch (RequestFailedException e)
    {
       Console.WriteLine($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            await client.CreateOrReplaceRelationshipAsync(rec.sourceId, Guid.NewGuid().ToString(), rec);
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine($"Error {e.Status}: {e.Message}");
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