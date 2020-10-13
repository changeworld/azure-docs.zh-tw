---
title: 使用 Azure Cosmos DB C# 筆記本 (預覽) 中的內建筆記本命令和功能
description: 了解如何使用內建的命令和功能，使用 Azure Cosmos DB 的內建 C# 筆記本進行一般作業。
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 47ac6b4caf97b264df58732f8d031fc63091171e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019261"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>使用 Azure Cosmos DB C# 筆記本 (預覽) 中的內建筆記本命令和功能

Azure Cosmos DB 中的內建 Jupyter 筆記本可讓您從 Azure 入口網站分析資料並將其視覺化。 本文說明如何使用內建的筆記本命令和功能，在 C# 筆記本中執行一般作業。

## <a name="install-a-new-nuget-package"></a>安裝新的 NuGet 套件
為您的 Azure Cosmos 帳戶啟用筆記本支援之後，您可以開啟新的筆記本並安裝套件。

在新的程式碼儲存格中，插入並執行下列程式碼，並將 ``PackageToBeInstalled`` 取代為所需的 NuGet 套件，且視需要將 ``optionalVersion`` 取代為特定版本的套件。 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

您可以在相同的儲存格中安裝多個 NuGet 套件。 套件將可從 Azure Cosmos 帳戶工作區中的任何筆記本使用。 

目前，C# 筆記本工作區不支援 NuGet 套件的遞迴解析。 如果 NuGet 套件相依於目前未安裝的其他 NuGet 套件，您就必須與父套件一起明確地加以參考。

> [!TIP]
> 如果您的筆記本需要自訂套件，建議您將儲存格新增至您的筆記本以安裝套件，並將其設為第一個儲存格。 這樣可以避免與 Azure Cosmos DB 依預設載入的其他套件發生衝突。 如果您因[重設工作區](#reset-notebooks-workspace)而移除了所有套件，也可輕易重新安裝套件。 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>使用內建的 Azure Cosmos DB .NET SDK
第 3 版的[適用於 SQL API 的 Azure Cosmos DB .NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) 會在安裝後包含在 Azure Cosmos 帳戶的筆記本環境中。

建立 ``CosmosClient`` 的執行個體，以執行任何 SDK 作業。 

例如：

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
若要深入了解，請參閱 [.NET V3 SDK 範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage)。 

> [!IMPORTANT]
> 只有 SQL (Core) API 帳戶支援內建的 Azure Cosmos DB .NET SDK。 對於其他 API，您必須[安裝對應於 API 的相關 .NET 驅動程式](#install-a-new-nuget-package)。 

## <a name="set-custom-options-using-cosmosclientoptions"></a>使用 ``CosmosClientOptions`` 設定自訂選項
如需更多彈性，您可以設定自訂 ``CosmosClientOptions`` 屬性，並將其傳入您的 ``CosmosClient`` 執行個體中。 您可以使用此屬性執行下列動作：

- 在使用者代理程式尾碼中設定應用程式名稱，以將其包含在每個要求中。
- 設定對服務執行作業時所要使用的慣用區域。
- 設定自訂重試原則，以處理速率受限的要求。

請參閱 [CosmosClientOptions API 參考](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions)一文，以了解所有支援的設定。 以下範例說明如何設定 `cosmosClientOptions` 屬性：

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>存取帳戶端點和主要金鑰變數
您可以存取筆記本所在的 Azure Cosmos 帳戶的內建端點和金鑰。

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> ``Cosmos.Key`` 和 ``Cosmos.Endpoint`` 變數僅適用於 SQL API。 對於其他 API，請在 Azure Cosmos 帳戶的 [連接字串] 或 [金鑰] 刀鋒視窗中尋找端點和金鑰。  

## <a name="print-console-output-in-c-code"></a>在 C# 程式碼中列印主控台輸出
在您的程式 C# 代碼中，您可以使用 Display.AsMarkdown() 語法搭配[字串插補](/dotnet/csharp/language-reference/tokens/interpolated)來列印主控台輸出，以在執行儲存格時顯示。 

例如： 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> C# 筆記本目前不支援 Console.WriteLine() 語法。 請使用 Display.AsMarkdown 從您的程式列印主控台輸出。 

## <a name="use-built-in-nteract-data-explorer"></a>使用內建的 nteract 資料總管
您可以使用內建的 [nteract 資料總管](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897)來篩選項目集合，並加以視覺化。 在儲存格中，將您想要視覺化的變數放在最後一行，當您執行儲存格時，該變數就會自動顯示在 nteract 中。

例如，在 *GetingStarted_Csharp.ipynb* 範例中，我們可以列印出結果 ``telemetryEvents`` 的變數。 如需完整範例，請參閱 [GettingStarted_Csharp.ipynb 筆記本](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb)。 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="Csharp 查詢儲存格":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="Csharp 查詢儲存格":::

## <a name="use-built-in-dictionary-viewer"></a>使用內建的字典檢視器
您可以使用內建的字典檢視器來檢視變數。 在儲存格中，將您想要視覺化的變數放在最後一行，當儲存格執行時就會自動顯示該變數。

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="Csharp 查詢儲存格":::

## <a name="upload-json-items-to-a-container"></a>將 JSON 項目上傳至容器
您可以使用 ``%%upload`` 魔術命令，將 JSON 檔案中的資料上傳至指定的 Azure Cosmos 容器。 請使用下列命令來上傳項目：

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- 將 ``{database_id}`` 和 ``{container_id}`` 取代為您 Azure Cosmos 帳戶中的資料庫和容器名稱。 
- 將 ``{url_location_of_file}`` 取代為 JSON 檔案的位置。 該檔案必須是有效 JSON 物件的陣列，且必須可透過公用網際網路存取。

例如：

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

透過輸出統計資料，您可以計算用來上傳項目的有效 RU/秒。 例如，如果在 38 秒內使用了 25,000 個 RU，則有效 RU/秒就是 25,000 個 RU/38 秒 = 658 RU/秒。

## <a name="run-another-notebook-in-current-notebook"></a>在目前的筆記本中執行另一個筆記本 
您可以使用 ``%%run`` 魔術命令，從工作區中目前的筆記本執行另一個筆記本。 使用語法：

```csharp
%%run ./path/to/{notebookName}.ipynb
```
請將 ``{notebookName}`` 取代為您要執行之筆記本的名稱。 筆記本必須位於您目前的「我的筆記本」工作區中。 

## <a name="reset-notebooks-workspace"></a>重設筆記本工作區
若要將筆記本工作區重設為預設設定，請在命令列上選取 [重設工作區]。 這會移除任何已安裝的自訂套件，並重新啟動 Jupyter 伺服器。 您的筆記本、檔案和 Azure Cosmos 資源將不受影響。  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Csharp 查詢儲存格":::

## <a name="next-steps"></a>後續步驟

- 了解 [Azure Cosmos DB Jupyter 筆記本](cosmosdb-jupyter-notebooks.md)的優點
- 了解[適用於 SQL API 的 Azure Cosmos DB .NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3)
