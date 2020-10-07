---
title: 教學課程：撰寫用戶端應用程式的程式碼
titleSuffix: Azure Digital Twins
description: 使用 .NET (C#) SDK 撰寫用戶端應用程式基礎程式碼的教學課程。
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: bd8eee2fd6134bb36c0b0ab45492567f4fdbec26
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91297499"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>教學課程：使用 Azure Digital Twins API 撰寫程式碼

開發人員使用 Azure Digital Twins 撰寫用戶端應用程式，與其 Azure Digital Twins 服務執行個體互動，是很常見的情況。 這項以開發人員為主的教學課程，會介紹使用[適用於 .NET 的 Azure IoT Digital Twin 用戶端程式庫 (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) (英文) 設計 Azure Digital Twins 服務的程式。 逐步引導您從頭開始撰寫 C# 主控台用戶端應用程式。

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

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>設定專案

準備好使用您的 Azure Digital Twins 執行個體之後，請開始設定用戶端應用程式專案。 

在您的電腦上開啟命令提示字元或其他主控台視窗，建立儲存本教學課程工作的空白專案目錄。 將目錄命名為任何您想要的名稱 (例如 *DigitalTwinsCodeTutorial*)。

導覽到新目錄。

進入專案目錄後，建立空白的 .NET 主控台應用程式專案。 在命令視窗中執行下列命令，以建立主控台的基礎 C# 專案：

```cmd/sh
dotnet new console
```

這會在您的目錄中建立數個檔案，包括一個名為 *Program.cs* 的檔案，您將在此撰寫大部分的程式碼。

接下來，新增兩個使用 Azure Digital Twins 的必要相依性：

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity
```

第一個相依性是[適用於 .NET 的 Azure IoT Digital Twin 用戶端程式庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) (英文)。 第二個相依性會提供協助驗證 Azure 的工具。

讓命令視窗保持開啟，因為整個教學課程都要繼續使用。

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

您必須有三段資訊才能驗證︰
* 訂用帳戶的「目錄 (租用戶) 識別碼」
* 稍早設定 Azure Digital Twins 執行個體時所建立的「應用程式 (用戶端) 識別碼」
* Azure Digital Twins 執行個體的 *hostName*

>[!TIP]
> 如果不知道自己的「目錄 (租用戶) 識別碼」，您可以在 [Azure Cloud Shell](https://shell.azure.com) 中執行下列命令以取得：
> 
> ```azurecli
> az account show --query tenantId
> ```

在 *Program.cs* 中，將下列程式碼貼在 `Main` 方法中 "Hello, World!" 列印行下方。 將 `adtInstanceUrl` 值設定為您 Azure Digital Twins 執行個體的「主機名稱」、將 `clientId` 設為您的「應用程式識別碼」，然後將 `tenantId` 設成您的「目錄識別碼」。

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

儲存檔案。 

請注意，此範例會使用互動式瀏覽器認證：
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

這種類型的認證會開啟瀏覽器視窗，要求您提供 Azure 認證。 

>[!NOTE]
> 如需其他認證類型的相關資訊，請參閱 [Microsoft 身分識別平台驗證程式庫](../active-directory/develop/reference-v2-libraries.md)的文件。

在命令視窗中，使用此命令執行程式碼： 

```cmd/sh
dotnet run
```

第一次執行時，這會還原相依性，然後執行程式。 
* 如未發生錯誤，程式會列印「服務用戶端已建立 - 準備就緒」。
* 因為此專案還沒有任何錯誤處理機制，所以如果發生問題，您會看到程式碼擲回的例外狀況。

### <a name="upload-a-model"></a>上傳模型

Azure Digital Twins 沒有內建的網域詞彙。 您環境中可以在 Azure Digital Twins 表示的元素類型，是由您使用**模型**定義。 [模型](concepts-models.md)類似物件導向程式設計語言中的類別，為[數位分身](concepts-twins-graph.md)提供日後可遵循並具現化的使用者定義範本。 以類似 JSON，稱為**數位分身定義語言 (DTDL)** 的語言所撰寫。

建立 Azure Digital Twins 解決方案的第一個步驟，是在 DTDL 檔案中至少定義一個模型。

在您建立專案的目錄中，建立新的 *.json* 檔案，名為 *SampleModel.json*。 貼入下列檔案主體： 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
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
> 有一種適用各種語言的 [DTDL 驗證程式範例](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)，您可用來檢查模型文件，以確定 DTDL 有效。 其建置基礎為 DTDL 剖析器程式庫，詳細資訊請參閱[操作說明：剖析與驗證模型](how-to-parse-models.md)。

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
「上傳模型」將會列印在輸出中，但還沒有輸出可指出是否已成功上傳模型。

若要新增指出模型是否確實上傳成功的列印陳述式，請在上一段的程式碼後面直接新增下列程式碼：

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```
再次執行程式之前，請先測試這段新的程式碼，請記得上次執行程式後，已上傳了您的模型。 Azure Digital Twins 不會讓您將相同的模型上傳兩次，因此，如果您再次嘗試上傳相同的模型，程式應該會擲回例外狀況。

現在，在您的命令視窗中，再次使用此命令執行此程式：

```cmd/sh
dotnet run
```

程式應該會擲回例外狀況。 當您嘗試上傳已上傳過的模型時，服務會透過 REST API 傳回「不正確的要求」錯誤。 最後，Azure Digital Twins 用戶端 SDK 就會針對不是傳回成功代碼的每項服務，擲回例外狀況。 

下一節將討論類似這樣的例外狀況，以及該如何在您的程式碼中處理。

### <a name="catch-errors"></a>攔截錯誤

為使程式不損毀，您可以在模型上傳程式碼的周圍新增例外狀況程式碼。 在 try/catch 處理常式中包裝現有的用戶端呼叫 `client.CreateModelsAsync`，如下所示：

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```
如果您正在命令視窗中使用 `dotnet run` 執行程式，就會看到傳回的錯誤碼。 輸出看起來會像這樣：

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Model with same ID already exists dtmi:com:contoso:SampleModel;1. Use Model_List API to view models that already exist. See the Swagger example. (http://aka.ms/ModelListSwSmpl):}}

Headers:
api-supported-versions: REDACTED
Date: Thu, 10 Sep 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

從這裡開始，本教學課程會將所有對服務方法的呼叫包裝在 try/catch 處理常式中。

### <a name="create-digital-twins"></a>建立數位分身

將模型上傳至 Azure Digital Twins 之後，您就可以使用此模型定義來建立**數位分身**。 [數位分身](concepts-twins-graph.md)是模型的執行個體，代表商務環境中的實體，例如伺服陣列上的感應器、建築物中的房間，或汽車的燈。 本節會根據您之前上傳的模型，建立一些數位分身。

在頂端新增新的 `using` 陳述式，因為您在 `System.Text.Json` 中需要內建的 .NET Json 序列化程式：

```csharp
using System.Text.Json;
using Azure.DigitalTwins.Core.Serialization;
```

然後，將下列程式碼新增至 `Main` 方法的結尾，根據此模型建立並初始化三個數位分身。

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

在命令視窗中，使用 `dotnet run` 執行程式。 然後再次執行程式。 

請注意，第二次建立對應項時，即使對應項在第一次執行後已經存在，也不會擲回任何錯誤。 與建立模型不同，就 REST 層級而言，建立對應項是 *PUT* 呼叫加上 *upsert* 語法。 這表示如果已有對應項存在，嘗試再次建立將會取代該對應項。 不需要有任何錯誤。

### <a name="create-relationships"></a>建立關聯性

接下來，您可以建立已建立對應項之間的**關聯性**，將其連線到**對應項圖形**。 [對應項圖形](concepts-twins-graph.md)用來表示整個環境。

若要能夠建立關聯性，您將需要 `Azure.DigitalTwins.Core.Serialization` 命名空間。 您先前已使用此 `using` 陳述式將此新增至專案：

```csharp
using Azure.DigitalTwins.Core.Serialization;
```

將新的靜態方法新增至 `Main` 方法下的 `Program` 類別：
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

然後，將下列程式碼新增至 `Main` 方法的結尾處，以呼叫 `CreateRelationship` 程式碼：
```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

在命令視窗中，使用 `dotnet run` 執行程式。

請注意，如果已有相同識別碼的關聯性，Azure Digital Twins 就不會讓您建立關聯性；所以，如果您執行程式多次，就會看到有關建立關聯性的例外狀況。 此程式碼會攔截例外狀況並加以忽略。 

### <a name="list-relationships"></a>列出關聯性

您將新增的下段程式碼，可讓您查看已建立的關聯性清單。

將下列新方法新增至 `Program` 類別：

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
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

在命令視窗中，使用 `dotnet run` 執行程式。 您應該會看到已建立的所有關聯性清單。

範例輸出如下：

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Type name: System.Collections.Generic.Dictionary'2[System.String,System.String]: dtmi:contosocom:DigitalTwins:SampleModel;1
Create twin: sampleTwin-0
Create twin: sampleTwin-1
Create twin: sampleTwin-2
Created relationship successfully
Created relationship successfully
Twin sampleTwin-0 is connected to:
-contains->sampleTwin-1
-contains->sampleTwin-2

```

### <a name="query-digital-twins"></a>查詢數位分身

Azure Digital Twins 的主要功能是能夠輕鬆且有效率地[查詢](concepts-query-language.md)對應項圖形，以回答有關環境的問題。

本教學課程要新增的最後一段程式碼，會針對 Azure Digital Twins 執行個體執行查詢。 此範例中使用的查詢會傳回此執行個體中的所有數位分身。

將下列程式碼新增至 `Main` 方法的結尾：

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
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
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
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
            AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
            await foreach (ModelData md in modelDataList)
            {
                Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
            twinData.CustomProperties.Add("data", $"Hello World!");
    
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
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

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
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
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
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
