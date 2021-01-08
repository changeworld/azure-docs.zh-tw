---
title: 教學課程：撰寫用戶端應用程式的程式碼
titleSuffix: Azure Digital Twins
description: 使用 .NET (C#) SDK 撰寫用戶端應用程式基礎程式碼的教學課程。
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 96533c1742e8ad5fde9796ea2073f6e402ac8dcf
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862377"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>教學課程：使用 Azure Digital Twins API 撰寫程式碼

開發人員使用 Azure Digital Twins 撰寫用戶端應用程式，與其 Azure Digital Twins 服務執行個體互動，是很常見的情況。 這項以開發人員為主的教學課程，會介紹使用[適用於 .NET 的 Azure Digital Twins SDK (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) (英文) 設計 Azure Digital Twins 服務的程式。 逐步引導您從頭開始撰寫 C# 主控台用戶端應用程式。

> [!div class="checklist"]
> * 設定專案
> * 開始使用專案程式碼   
> * 完整的程式碼範例
> * 清除資源
> * 後續步驟

## <a name="prerequisites"></a>必要條件

本教學課程使用命令列進行設定及專案工作。 因此，您可以使用任何程式碼編輯器來逐步完成練習。

開始前的準備：
* 任何程式碼編輯器
* 開發電腦上的 **.NET Core 3.1**。 您可以從[下載 .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (英文) 下載此適用於多個平台的 .NET Core SDK 版本。

### <a name="prepare-an-azure-digital-twins-instance"></a>準備 Azure Digital Twins 執行個體

[!INCLUDE [Azure Digital Twins: instance prereq](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

## <a name="set-up-project"></a>設定專案

準備好使用您的 Azure Digital Twins 執行個體之後，請開始設定用戶端應用程式專案。 

在您的電腦上開啟命令提示字元或其他主控台視窗，建立儲存本教學課程工作的空白專案目錄。 將目錄命名為任何您想要的名稱 (例如 *DigitalTwinsCodeTutorial*)。

導覽到新目錄。

進入專案目錄後，**建立空白的 .NET 主控台應用程式專案**。 在命令視窗中執行下列命令，以建立主控台的基礎 C# 專案：

```cmd/sh
dotnet new console
```

這會在您的目錄中建立數個檔案，包括一個名為 *Program.cs* 的檔案，您將在此撰寫大部分的程式碼。

讓命令視窗保持開啟，因為整個教學課程都要繼續使用。

接下來，**將兩個相依性新增至您的專案**，以使用 Azure Digital Twins。 您可以使用下列連結瀏覽至 NuGet 上的套件，在其中找到主控台命令 (包括 .NET CLI 可用的命令)，將每個套件新增至您的專案。
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core)。 這是適用於[適用於 .NET 的 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 的套件。 新增最新版本。
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). 此程式庫會提供協助驗證 Azure 的工具。 新增 1.2.2 版。

>[!NOTE]
> 目前有[已知問題](troubleshoot-known-issues.md#issue-with-default-azure-credential-authentication-on-azureidentity-130)會影響在本教學課程中使用 Azure.Identity 1.3.0 版的能力。 若此問題持續發生，請使用 1.2.2 版。

## <a name="get-started-with-project-code"></a>開始使用專案程式碼

在本節中，您要開始撰寫新應用程式專案的程式碼，以使用 Azure Digital Twins。 涵蓋的動作包括：
* 驗證服務
* 上傳模型
* 攔截錯誤
* 建立數位分身
* 建立關聯性
* 查詢數位分身

本教學課程結束時，會另有一節顯示完整的程式碼。 您可以此為參考，隨時檢查您的程式。

開始前，請使用任何程式碼編輯器開啟 *Program.cs* 檔案。 您會看到如下所示的基礎程式碼範本：

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

首先，在程式碼頂端新增一些 `using` 行，以提取必要的相依性。

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

接下來，您要在此檔案新增程式碼，以填滿一些功能。 

### <a name="authenticate-against-the-service"></a>驗證服務

應用程式必須執行的第一項工作，就是驗證 Azure Digital Twins 服務。 接著，您就可以建立服務用戶端類別以存取 SDK 函式。

為了進行驗證，您需要 Azure Digital Twins 執行個體的 *hostName*。

在 *Program.cs* 中，將下列程式碼貼在 `Main` 方法中 "Hello, World!" 列印行下方。 將 `adtInstanceUrl` 的值設定為您的 Azure Digital Twins 執行個體 *hostName*。

```csharp
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>"; 
var credential = new DefaultAzureCredential();
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
Console.WriteLine($"Service client created – ready to go");
```

儲存檔案。 

在命令視窗中，使用此命令執行程式碼： 

```cmd/sh
dotnet run
```

第一次執行時，這會還原相依性，然後執行程式。 
* 如未發生錯誤，程式會列印「服務用戶端已建立 - 準備就緒」。
* 此專案中還沒有任何錯誤處理機制，因此如果發生問題，您將會看到程式碼擲回的例外狀況。

### <a name="upload-a-model"></a>上傳模型

Azure Digital Twins 沒有內建的網域詞彙。 您環境中可以在 Azure Digital Twins 表示的元素類型，是由您使用 **模型** 定義。 [模型](concepts-models.md)類似物件導向程式設計語言中的類別，為[數位分身](concepts-twins-graph.md)提供日後可遵循並具現化的使用者定義範本。 以類似 JSON，稱為 **數位分身定義語言 (DTDL)** 的語言所撰寫。

建立 Azure Digital Twins 解決方案的第一個步驟，是在 DTDL 檔案中至少定義一個模型。

在您建立專案的目錄中，建立新的 *.json* 檔案，名為 *SampleModel.json*。 貼入下列檔案主體： 

```json
{
  "@id": "dtmi:example:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> 如果在本教學課程中使用 Visual Studio，建議您選取新建立的 JSON 檔案，並將屬性偵測器的「複製到輸出目錄」屬性設定為「有更新時才複製」或「永遠複製」。 當您在本教學課程的其餘部分，使用 **F5** 執行程式時，這可讓 Visual Studio 找到具有預設路徑的 JSON 檔案。

> [!TIP] 
> 有一種適用各種語言的 [DTDL 驗證程式範例](/samples/azure-samples/dtdl-validator/dtdl-validator)，您可用來檢查模型文件，以確定 DTDL 有效。 其建置基礎為 DTDL 剖析器程式庫，詳細資訊請參閱[操作說明：剖析與驗證模型](how-to-parse-models.md)。

接下來，在 *Program.cs* 中新增更多程式碼，以將您剛才建立的模型上傳至您的 Azure Digital Twins 執行個體。

首先，在檔案頂端新增一些 `using` 陳述式：

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
```

接下來，將 `Main` 方法簽章變更為允許非同步執行，準備使用 C# 服務 SDK 中的非同步方法。 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> 因為 SDK 也提供所有呼叫的同步版本，所以不一定非使用 `async` 不可。 本教學課程的練習使用 `async`。

接下來是與 Azure Digital Twins 服務互動的第一段程式碼。 此程式碼會載入您從磁碟建立的 DTDL 檔案，然後上傳至您的 Azure Digital Twins 服務執行個體。 

在您稍早新增的授權碼底下貼上下列程式碼。

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

在命令視窗中，使用下列命令執行程式： 

```cmd/sh
dotnet run
```
「上傳模型」將會列印在輸出中，指出已連線到此程式碼，但還沒有輸出指出上傳是否成功。

若要新增列印陳述式以顯示所有已成功上傳至執行個體的所有模型，請在上一段程式碼後面緊接著新增下列程式碼：

```csharp
// Read a list of models back from the service
Console.WriteLine("Models uploaded to the instance:");
AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
await foreach (DigitalTwinsModelData md in modelDataList)
{
    Console.WriteLine($"{md.Id}");
}
```

**再次執行程式以測試這段新的程式碼之前**，請記得您在上次執行程式時已上傳模型。 Azure Digital Twins 不會讓您將相同的模型上傳兩次，因此，如果您再次嘗試上傳相同的模型，程式應該會擲回例外狀況。

據此，在您的命令視窗中，再次使用此命令執行程式：

```cmd/sh
dotnet run
```

程式應該會擲回例外狀況。 當您嘗試上傳已上傳過的模型時，服務會透過 REST API 傳回「不正確的要求」錯誤。 最後，Azure Digital Twins 用戶端 SDK 就會針對不是傳回成功代碼的每項服務，擲回例外狀況。 

下一節將討論類似這樣的例外狀況，以及該如何在您的程式碼中處理。

### <a name="catch-errors"></a>攔截錯誤

為使程式不損毀，您可以在模型上傳程式碼的周圍新增例外狀況程式碼。 在 try/catch 處理常式中包裝現有的用戶端呼叫 `await client.CreateModelsAsync(typeList)`，如下所示：

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

現在，如果您正在命令視窗中使用 `dotnet run` 執行程式，就會看到傳回的錯誤碼。 模型建立程式碼的輸出會顯示下列錯誤：

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="程式輸出，顯示一則訊息指出「409：服務要求失敗。狀態：409 (衝突)。」，後面接著一段錯誤輸出，指出 dtmi:example:SampleModel;1 已存在":::

從這裡開始，本教學課程會將所有對服務方法的呼叫包裝在 try/catch 處理常式中。

### <a name="create-digital-twins"></a>建立數位分身

將模型上傳至 Azure Digital Twins 之後，您就可以使用此模型定義來建立 **數位分身**。 [數位分身](concepts-twins-graph.md)是模型的執行個體，代表商務環境中的實體，例如伺服陣列上的感應器、建築物中的房間，或汽車的燈。 本節會根據您之前上傳的模型，建立一些數位分身。

將下列程式碼新增至 `Main` 方法的結尾，根據此模型建立並初始化三個數位對應項。

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:example:SampleModel;1";
twinData.Contents.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twinData.Id, twinData);
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

在命令視窗中，使用 `dotnet run` 執行程式。 在輸出中，尋找指出 *sampleTwin-0*、*sampleTwin-1* 和 *sampleTwin-2* 已建立的列印訊息。 

然後，再次執行程式。 

請注意，第二次建立對應項時，即使對應項在第一次執行後已經存在，也不會擲回任何錯誤。 與建立模型不同，就 REST 層級而言，建立對應項是 *PUT* 呼叫加上 *upsert* 語法。 這表示如果對應項已存在，若嘗試再次建立相同的對應項，將會直接取代原始對應項。 不會擲回錯誤。

### <a name="create-relationships"></a>建立關聯性

接下來，您可以建立已建立對應項之間的 **關聯性**，將其連線到 **對應項圖形**。 [對應項圖形](concepts-twins-graph.md)用來表示整個環境。

在 `Main` 方法底下，將 **新的靜態方法** 新增至 `Program` 類別 (程式碼現在有兩個方法)：

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
        await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

接下來，將下列程式碼新增至 `Main` 方法的結尾處，以呼叫 `CreateRelationship` 方法及使用您剛才撰寫的程式碼：

```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

在命令視窗中，使用 `dotnet run` 執行程式。 在輸出中，尋找指出已成功建立兩個關聯性的列印陳述式。

請注意，如果已有另一個具有相同識別碼的關聯性存在，Azure Digital Twins 就不會讓您建立關聯性；所以，如果您執行程式多次，就會看到有關建立關聯性的例外狀況。 此程式碼會攔截例外狀況並加以忽略。 

### <a name="list-relationships"></a>列出關聯性

您將新增的下段程式碼，可讓您查看已建立的關聯性清單。

將下列 **新方法** 新增至 `Program` 類別：

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<BasicRelationship> results = client.GetRelationshipsAsync<BasicRelationship>(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (BasicRelationship rel in results)
        {
            Console.WriteLine($" -{rel.Name}->{rel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

然後，將下列程式碼新增至 `Main` 方法的結尾處，以呼叫 `ListRelationships` 程式碼：

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

在命令視窗中，使用 `dotnet run` 執行程式。 您應該會在輸出陳述式中看到已建立的所有關聯性清單，顯示如下：

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="程式輸出，顯示一則訊息指出「對應項 sampleTwin-0 已連線至：contains->sampleTwin-1, -contains->sampleTwin-2」":::

### <a name="query-digital-twins"></a>查詢數位分身

Azure Digital Twins 的主要功能是能夠輕鬆且有效率地[查詢](concepts-query-language.md)對應項圖形，以回答有關環境的問題。

本教學課程要新增的最後一段程式碼，會針對 Azure Digital Twins 執行個體執行查詢。 此範例中使用的查詢會傳回此執行個體中的所有數位分身。

新增此 `using` 陳述式，以利用 `JsonSerializer` 類別呈現數位對應項資訊：

```csharp
using System.Text.Json;
```

然後，將下列程式碼新增至 `Main` 方法的結尾：

```csharp
// Run a query for all twins   
string query = "SELECT * FROM digitaltwins";
AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);

await foreach (BasicDigitalTwin twin in result)
{
    Console.WriteLine(JsonSerializer.Serialize(twin));
    Console.WriteLine("---------------");
}
```

在命令視窗中，使用 `dotnet run` 執行程式。 您應該會在輸出中看到此執行個體中的所有數位分身。

## <a name="complete-code-example"></a>完整的程式碼範例

現在在本教學課程中，您有一個完整的用戶端應用程式，能夠針對 Azure Digital Twins 執行基本動作。 以下所列為 *Program.cs* 中的程式完整程式碼，供您參考：

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>"; 
            
            var credential = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            Console.WriteLine("Models uploaded to the instance:");
            AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
            await foreach (DigitalTwinsModelData md in modelDataList)
            {
                Console.WriteLine($"{md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:example:SampleModel;1";
            twinData.Contents.Add("data", $"Hello World!");
            
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twinData.Id, twinData);
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query for all twins   
            string query = "SELECT * FROM digitaltwins";
            AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
            
            await foreach (BasicDigitalTwin twin in result)
            {
                Console.WriteLine(JsonSerializer.Serialize(twin));
                Console.WriteLine("---------------");
            }
        }

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
                await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<BasicRelationship> results = client.GetRelationshipsAsync<BasicRelationship>(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (BasicRelationship rel in results)
                {
                    Console.WriteLine($" -{rel.Name}->{rel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>清除資源
 
本教學課程中使用的執行個體可以在下一個教學課程中重複使用，[教學課程：探索範例用戶端應用程式的基本概念](tutorial-command-line-app.md)。 如果打算繼續下一項教學課程，您可以保留在此設定的 Azure Digital Twins 執行個體。
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

最後，刪除您在本機電腦上建立的專案資料夾。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您建立了全新的 .NET 主控台用戶端應用程式。 您為此用戶端應用程式撰寫了程式碼，在 Azure Digital Twins 執行個體上執行基本動作。

請繼續前往下一個教學課程，探索您可以使用這類範例用戶端應用程式執行的工作： 

> [!div class="nextstepaction"]
> [教學課程：探索範例用戶端應用程式的基本概念](tutorial-command-line-app.md)
