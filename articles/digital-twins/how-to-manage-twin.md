---
title: 管理 Digital Twins
titleSuffix: Azure Digital Twins
description: 瞭解如何取得、更新和刪除個別的 twins 和關聯性。
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9e00e0e5a34eecd6974e8919ce0d0e16f48757f3
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540962"
---
# <a name="manage-digital-twins"></a>管理 Digital Twins

您環境中的實體會以 [數位 twins](concepts-twins-graph.md)表示。 管理您的數位 twins 可能包括建立、修改和移除。 若要執行這些作業，您可以使用 [**DigitalTwins api**](/rest/api/digital-twins/dataplane/twins)、 [.Net (c # ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)或 [Azure 數位 Twins CLI](how-to-use-cli.md)。

本文著重于管理數位 twins;若要以整體方式使用關聯性和對應項 [圖形](concepts-twins-graph.md) ，請參閱 [*如何：使用關聯性管理*](how-to-manage-graph.md)對應項圖形。

> [!TIP]
> 所有 SDK 函式都有同步和非同步版本。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>管理 twins 的方式

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>建立數位對應項

若要建立對應項，請在 `CreateOrReplaceDigitalTwinAsync()` 服務用戶端上使用方法，如下所示：

```csharp
await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myTwinId", initData);
```

若要建立數位對應項，您需要提供：
* 數位對應項所需的識別碼
* 您要使用的[模型](concepts-models.md)

（選擇性）您可以提供數位對應項之所有屬性的初始值。 屬性會被視為選擇性，並可在稍後設定，但在 **設定之前，它們不會顯示為對應項的一部分。**

>[!NOTE]
>雖然不需要初始化對應項屬性，但對應項上的任何 [元件](concepts-models.md#elements-of-a-model)**都必須在** 建立對應項時設定。 它們可以是空的物件，但是元件本身必須存在。

模型和任何初始屬性值都是透過參數提供的 `initData` ，這是包含相關資料的 JSON 字串。 如需結構化此物件的詳細資訊，請繼續下一節。

> [!TIP]
> 建立或更新對應項之後，最多可能會有10秒的延遲時間，變更才會反映在 [查詢](how-to-query-graph.md)中。 本文 `GetDigitalTwin` 稍後所述的 API () 不會遇到這 [種](#get-data-for-a-digital-twin) 延遲，因此如果您需要立即回應，請使用 api 呼叫而不是查詢來查看您新建立的 twins。 

### <a name="initialize-model-and-properties"></a>初始化模型和屬性

您可以在建立對應項時，初始化對應項的屬性。 

對應項建立 API 會接受序列化為對應項屬性之有效 JSON 描述的物件。 請參閱 [*概念：數位 twins 和*](concepts-twins-graph.md) 對應項圖表，以取得對應項的 JSON 格式描述。 

首先，您可以建立資料物件來代表對應項及其屬性資料。 您可以手動或使用所提供的 helper 類別來建立參數物件。 以下是每個範例。

#### <a name="create-twins-using-manually-created-data"></a>使用手動建立的資料建立 twins

若未使用任何自訂 helper 類別，您可以在中表示對應項的屬性 `Dictionary<string, object>` ，其中 `string` 是屬性的名稱，而 `object` 是代表屬性和其值的物件。

[!INCLUDE [Azure Digital Twins code: create twin](../../includes/digital-twins-code-create-twin.md)]

#### <a name="create-twins-with-the-helper-class"></a>使用 helper 類別建立 twins

的 helper 類別 `BasicDigitalTwin` 可讓您直接將屬性欄位儲存在 "對應項" 物件中。 您仍可能會想要使用建立屬性清單 `Dictionary<string, object>` ，然後直接將其新增至對應項物件 `CustomProperties` 。

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.Contents = props;

client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myRoomId", twin);
Console.WriteLine("The twin is created successfully");
```

>[!NOTE]
> `BasicDigitalTwin` 物件會隨附一個 `Id` 欄位。 您可以將此欄位保留為空白，但如果您新增了識別碼值，它必須符合傳遞至呼叫的 ID 參數 `CreateOrReplaceDigitalTwinAsync()` 。 例如：
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>取得數位對應項的資料

您可以藉由呼叫方法來存取任何數位對應項的詳細資料， `GetDigitalTwin()` 如下所示：

```csharp
object result = await client.GetDigitalTwin(id);
```
這個呼叫會以強型別物件類型（例如）傳回對應項資料 `BasicDigitalTwin` 。 以下是如何使用此方法來查看對應項詳細資料的範例：

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin("myRoomId");
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.Contents.Keys)
{
  if (twin.Contents.TryGetValue(prop, out object value))
  Console.WriteLine($"Property '{prop}': {value}");
}
```
當您使用方法取得對應項時，只會傳回至少已設定一次的屬性 `GetDigitalTwin()` 。

>[!TIP]
>`displayName`對應項的是其模型中繼資料的一部分，因此在取得對應項實例的資料時，不會顯示它。 若要查看此值，您可以 [從模型中取出](how-to-manage-model.md#retrieve-models)。

若要使用單一 API 呼叫來取出多個 twins，請參閱 [*如何：查詢*](how-to-query-graph.md)對應項圖形中的查詢 API 範例。

請考慮下列模型 (以 [數位 Twins 定義語言撰寫 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) 定義 *月亮* ：

```json
{
    "@id": "dtmi:example:Moon;1",
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
在月亮型別對應項上呼叫的結果 `object result = await client.GetDigitalTwinAsync("my-moon");` 可能如下所示： *Moon*

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
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

數位對應項的已定義屬性會傳回為數字對應項上的最上層屬性。 不是 DTDL 定義一部分的中繼資料或系統資訊會以前置詞傳回 `$` 。 中繼資料屬性包括：
* 此 Azure 數位 Twins 實例中數位對應項的識別碼，如下所示 `$dtId` 。
* `$etag`，由 web 伺服器指派的標準 HTTP 欄位。
* 區段中的其他屬性 `$metadata` 。 它們包括：
    - 數位對應項之模型的 DTMI。
    - 每個可寫入屬性的同步處理狀態。 這最適用于裝置，在這種情況下，服務和裝置有可能具有發散狀態 (例如，當裝置離線時) 。 此屬性目前僅適用于連線到 IoT 中樞的實體裝置。 有了中繼資料區段中的資料之後，就可以瞭解屬性的完整狀態，以及上次修改的時間戳記。 如需同步處理狀態的詳細資訊，請參閱關於同步處理裝置狀態的 [IoT 中樞教學](../iot-hub/tutorial-device-twins.md) 課程。
    - 服務特定的中繼資料，例如來自 IoT 中樞或 Azure 數位 Twins。 

您可以使用您選擇的 JSON 剖析程式庫（例如），為對應項剖析傳回的 JSON `System.Text.Json` 。

您也可以使用 SDK 隨附的序列化 helper 類別 `BasicDigitalTwin` ，這會以預先剖析的表單傳回核心對應項中繼資料和屬性。 範例如下：

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_Id);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.Contents.Keys)
{
    if (twin.Contents.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

您可以參閱 [*如何：使用 Azure 數位 Twins api 和 sdk*](how-to-use-apis-sdks.md)，以深入瞭解序列化協助程式類別。

## <a name="view-all-digital-twins"></a>查看所有數位 twins

若要在您的實例中查看所有數位 twins，請使用 [查詢](how-to-query-graph.md)。 您可以使用 [查詢 api](/rest/api/digital-twins/dataplane/query) 或 [CLI 命令](how-to-use-cli.md)來執行查詢。

以下是基本查詢的主體，會傳回實例中所有數位 twins 的清單：

```sql
SELECT *
FROM DIGITALTWINS
``` 

## <a name="update-a-digital-twin"></a>更新數位分身

若要更新數位對應項的屬性，請使用 [JSON 修補程式](http://jsonpatch.com/) 格式來撰寫要取代的資訊。 如此一來，您就可以一次取代多個屬性。 然後，您可以將 JSON 修補檔傳遞給 `UpdateDigitalTwin()` 方法：

```csharp
await client.UpdateDigitalTwin(id, patch);
```

Patch 呼叫可以依您想要的方式，在單一對應項上更新多個屬性， (甚至) 。 如果您需要更新跨多個 twins 的屬性，則每個對應項都需要個別的更新呼叫。

> [!TIP]
> 建立或更新對應項之後，最多可能會有10秒的延遲時間，變更才會反映在 [查詢](how-to-query-graph.md)中。 本文稍 `GetDigitalTwin` [早](#get-data-for-a-digital-twin) 所述的 API () 不會遇到這種延遲，因此，如果您需要立即回應，請使用 api 呼叫而不是查詢來查看您剛更新的 twins。 

以下是 JSON 修補程式程式碼的範例。 這份檔會取代套用的數位對應項的 *大量* 和 *半徑* 屬性值。

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
您可以使用 SDK 中的來建立修補程式 `JsonPatchDocument` 。 [SDK](how-to-use-apis-sdks.md) 範例如下。

```csharp
var updateTwinData = new JsonPatchDocument();
updateTwinData.AppendAddOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(twin_Id, updateTwinData);
```

### <a name="update-properties-in-digital-twin-components"></a>更新數位對應項元件中的屬性

回想一下，模型可能包含元件，使其可由其他模型組成。 

若要修補數位對應項元件中的屬性，您可以使用 JSON 修補程式中的路徑語法：

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

`UpdateDigitalTwin()`函數也可以用來將數位對應項遷移至不同的模型。 

例如，請考慮下列會取代數位對應項之元資料欄位的 JSON 修補檔 `$model` ：

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:example:foo;1"
  }
]
```

只有當修補程式所修改的數位對應項符合新的模型時，這項作業才會成功。 

請考慮下列範例：
1. 想像一個具有 *foo_old* 模型的數位對應項。 *foo_old* 定義必要的屬性 *品質* 。
2. 新模型 *foo_new* 定義屬性品質，並新增必要的屬性 *溫度* 。
3. 在修補之後，數位對應項必須同時有大量和溫度屬性。 

這種情況的修補程式必須更新模型和對應項的溫度屬性，如下所示：

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:example:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

### <a name="handle-conflicting-update-calls"></a>處理衝突的更新呼叫

Azure 數位 Twins 可確保所有連入要求都會在另一個之後處理。 這表示即使有多個函式嘗試同時更新對應項的相同屬性，您也 **不需要** 撰寫明確的鎖定程式碼來處理衝突。

此行為是以每個對應項為基礎。 

例如，假設這三個呼叫同時抵達的案例： 
*   在 *Twin1* 上撰寫屬性 A
*   在 *Twin1* 上寫入屬性 B
*   在 *Twin2* 上撰寫屬性 A

修改 *Twin1* 的兩個呼叫會在另一個之後執行，並針對每個變更產生變更訊息。 修改 *Twin2* 的呼叫可能會在一到達時，同時執行，而不會發生衝突。

## <a name="delete-a-digital-twin"></a>刪除數位對應項

您可以使用方法來刪除 twins `DeleteDigitalTwin()` 。 不過，當對應項沒有其他關聯性時，您只能刪除對應項。 因此，請先刪除對應項的傳入和傳出關聯性。

以下是用來刪除 twins 和其關聯性的程式碼範例：

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
        AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);

        await foreach (BasicRelationship rel in rels)
        {
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```
### <a name="delete-all-digital-twins"></a>刪除所有數位 twins

如需如何一次刪除所有 twins 的範例，請下載 _Tutorial 中使用的範例應用程式 [：探索範例用戶端應用程式的基本概念 *](tutorial-command-line-app.md)。 *CommandLoop.cs* 檔案會在函式中執行此 `CommandDeleteAllTwins()` 工作。

## <a name="runnable-digital-twin-code-sample"></a>可執行檔數位對應項程式碼範例

您可以使用下列可執行檔程式碼範例來建立對應項、更新其詳細資料，以及刪除對應項。 

### <a name="set-up-the-runnable-sample"></a>設定可執行檔範例

程式碼片段會使用教學課程中的模型定義 [Room.js](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) [*：探索 Azure 數位 Twins 與範例用戶端應用程式*](tutorial-command-line-app.md)。 您可以使用此連結直接移至檔案，或將它下載為完整的端對端 [範例專案的](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)一部分。

執行範例之前，請執行下列動作：
1. 下載模型檔案，將它放在您的專案中，並取代 `<path-to>` 下列程式碼中的預留位置，告訴您的程式要在哪裡尋找它。
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
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload a model");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            // Upload the model to the service
            await client.CreateModelsAsync(typeList);

            //Create new digital twin
            BasicDigitalTwin twin = new BasicDigitalTwin();
            string twin_Id = "myRoomId";
            twin.Metadata = new DigitalTwinMetadata();
            twin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            twin.Contents = props;
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twin_Id, twin);
            Console.WriteLine("Twin created successfully");
            Console.WriteLine();

            //Print twin
            Console.WriteLine("--- Printing twin details:");
            twin = FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Update twin data
            var updateTwinData = new JsonPatchDocument();
            updateTwinData.AppendAdd("/Temperature", 25.0);
            await client.UpdateDigitalTwinAsync(twin_Id, updateTwinData);
            Console.WriteLine("Twin properties updated");
            Console.WriteLine();

            //Print twin again
            Console.WriteLine("--- Printing twin details (after update):");
            FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete twin
            await DeleteTwin(client, twin_Id);
        }

        private static BasicDigitalTwin FetchAndPrintTwin(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_Id);
            Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
            foreach (string prop in twin.Contents.Keys)
            {
                if (twin.Contents.TryGetValue(prop, out object value))
                    Console.WriteLine($"Property '{prop}': {value}");
            }

            return twin;
        }
        private static async Task DeleteTwin(DigitalTwinsClient client, string id)
        {
            await FindAndDeleteOutgoingRelationshipsAsync(client, id);
            await FindAndDeleteIncomingRelationshipsAsync(client, id);
            try
            {
                await client.DeleteDigitalTwinAsync(id);
                Console.WriteLine("Twin deleted successfully");
            }
            catch (RequestFailedException exc)
            {
                Console.WriteLine($"*** Error:{exc.Message}");
            }
        }

        private static async Task FindAndDeleteOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);

                await foreach (BasicRelationship rel in rels)
                {
                    await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
                    Console.WriteLine($"Deleted relationship {rel.Id} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
            }
        }

       private static async Task FindAndDeleteIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
                    Console.WriteLine($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
            }
        }

    }
}

```
以下是上述程式的主控台輸出： 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="顯示對應項已建立、更新及刪除的主控台輸出" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>後續步驟

瞭解如何建立和管理數位 twins 之間的關聯性： _ [*如何：管理具有關聯性的* 對應項圖表](how-to-manage-graph.md)