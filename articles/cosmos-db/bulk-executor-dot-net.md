---
title: 在 Azure Cosmos DB 中使用批量執行器 .NET 庫進行大量匯入和更新操作
description: 使用大容量執行器 .NET 庫大量匯入和更新 Azure Cosmos DB 文檔。
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: d7600267dcd196a9a5c06c29774ea21d582cd7ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246873"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>使用批量執行器 .NET 庫在 Azure Cosmos DB 中執行批量操作

本教程提供有關使用大宗執行器 .NET 庫將文檔導入和更新到 Azure Cosmos 容器的說明。 要瞭解批量執行器庫及其如何説明您利用大量輸送量和存儲，請參閱[批量執行器庫概述](bulk-executor-overview.md)一文。 在本教程中，您將看到一個示例 .NET 應用程式，該應用程式將隨機生成的文檔大量匯入到 Azure Cosmos 容器中。 匯入之後，應用程式會說明如何將修補程式指定為可在特定文件欄位上執行的作業，來大量更新匯入的資料。

目前，Azure Cosmos DB SQL API 和 Gremlin API 帳戶僅支援大宗執行器庫。 本文介紹如何使用具有 SQL API 帳戶的批量執行器 .NET 庫。 要瞭解如何將大容量執行器 .NET 庫與 Gremlin API 帳戶一起使用，請參閱[在 Azure Cosmos DB Gremlin API 中執行批量操作](bulk-executor-graph-dotnet.md)。

## <a name="prerequisites"></a>Prerequisites

* 如果您尚未安裝 Visual Studio 2019，您可以下載並使用 Visual Studio [2019 社區版](https://www.visualstudio.com/downloads/)。 請確保在視覺化工作室設置期間啟用"Azure 開發"。

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 您可以[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，無須 Azure 訂用帳戶，也無須任何費用和約定付款。 或者，您可以將 Azure [Cosmos DB 模擬器](https://docs.microsoft.com/azure/cosmos-db/local-emulator)與`https://localhost:8081`終結點一起使用。 [驗證要求](local-emulator.md#authenticating-requests)中會提供主索引鍵。

* 使用 .NET 快速入門文章中＜[建立資料庫帳戶](create-sql-api-dotnet.md#create-account)＞一節所述的步驟，建立 Azure Cosmos DB SQL API 帳戶。

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，讓我們從 GitHub 下載示例 .NET 應用程式來切換到使用代碼。 此應用程式對存儲在 Azure Cosmos 帳戶中的資料執行批量操作。 要克隆應用程式，請打開命令提示符，導航到要複製它的目錄並運行以下命令：

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

克隆的存儲庫包含兩個示例"大量匯入示例"和"批量更新示例"。 您可以開啟其中一個應用程式範例，使用 Azure Cosmos DB 帳戶的連接字串更新 App.config 檔案中的連接字串，然後建置解決方案並加以執行。

"大量匯入示例"應用程式生成隨機文檔，並將它們大量匯入到 Azure Cosmos 帳戶。 "BulkUpdateSample" 應用程式會將修補程式指定為可在特定文件欄位上執行的作業，來大量更新匯入的文件。 在後面幾節中，您將檢閱這裡每一個範例應用程式中的程式碼。

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>批量將資料導入 Azure Cosmos 帳戶

1. 瀏覽至 "BulkImportSample" 資料夾，然後開啟 "BulkImportSample.sln" 檔案。  

2. Azure Cosmos DB 的連接字串會從 App.config 檔案中擷取，如下列程式碼所示：  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   大量匯入程式創建一個新的資料庫和一個容器，其中包含 App.config 檔中指定的資料庫名稱、容器名稱和輸送量值。

3. 接下來，DocumentClient 物件會使用直接 TCP 連線模式進行初始化：  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. BulkExecutor 物件初始化時，等待時間和限制請求的重試值較高。 接著，這些值會設定為 0，如此會將壅塞控制傳遞給 BulkExecutor 以決定其存留期。  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. 應用程式會叫用 BulkImportAsync API。 .NET 庫提供大量匯入 API 的兩個重載 - 一個接受序列化 JSON 文檔的清單，另一個接受非序列化 POCO 文檔的清單。 要瞭解有關每個重載方法的定義的詳細資訊，請參閱[API 文檔](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet)。

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **BulkImportAsync 方法可接受下列參數：**
   
   |**參數**  |**描述** |
   |---------|---------|
   |enableUpsert    |   用於啟用對文檔的 upsert 操作的標誌。 如果已存在具有給定 ID 的文檔，則該文檔將更新。 預設會設定為 false。      |
   |disableAutomaticIdGeneration    |    停用自動產生識別碼的旗標。 預設會設定為 true。     |
   |maxConcurrencyPerPartitionKeyRange    | 每個資料分割索引鍵範圍的最大並行程度，若設定為 Null，程式庫會使用預設值 20。 |
   |maxInMemorySortingBatchSize     |  從文檔枚舉器提取的最大文檔數，在每個階段傳遞到 API 呼叫。 對於大量匯入之前發生的記憶體內排序階段，將此參數設置為 null 將導致庫使用預設最小值（文檔.count，100000）。       |
   |cancellationToken    |    取消權杖以正常退出大量匯入操作。     |

   **大量匯入回應物件定義**大量匯入 API 呼叫的結果包含下列屬性：

   |**參數**  |**描述**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  從提供給大量匯入 API 呼叫的文檔中成功導入的文檔總數。       |
   |TotalRequestUnitsConsumed (double)   |   大量匯入 API 呼叫取用的要求單位 (RU) 總數。      |
   |TotalTimeTaken (TimeSpan)    |   大量匯入 API 呼叫完成執行所佔用的總時間。      |
   |錯誤輸入文檔（清單\<物件>）   |     格式錯誤而未成功匯入大量匯入 API 呼叫的文件清單。 修復返回的文檔並重試導入。 格式錯誤的文件包含其識別碼值不是字串 (Null 或任何其他視為無效的資料類型) 的文件。    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>批量更新 Azure Cosmos 帳戶中的資料

您可以使用 BulkUpdateAsync API 來更新現有的文件。 在此示例中，您將該`Name`欄位設置為新值，並從現有文檔中刪除該`Description`欄位。 有關支援的完整更新操作集，請參閱[API 文檔](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)。

1. 瀏覽至 "BulkUpdateSample" 資料夾，然後開啟 "BulkUpdateSample.sln" 檔案。  

2. 定義更新項以及相應的欄位更新操作。 在此示例中，`SetUpdateOperation`您將使用 更新`Name`該欄位並從`UnsetUpdateOperation`所有文檔中刪除`Description`該欄位。 您也可以執行其他作業，例如透過指定值來遞增文件欄位、將特定值推送至陣列欄位中，或是從陣列欄位中移除特定值。 若要了解大量更新 API 提供的不同方法，請參閱 [API 文件](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)。

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. 應用程式會叫用 BulkUpdateAsync API。 要瞭解 BulkUpdateAsync 方法的定義，請參閱[API 文檔](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet)。  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync 方法可接受下列參數：**

   |**參數**  |**描述** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   每個分區鍵範圍和發的最大程度，將此參數設置為 null 將使庫使用預設值 （20）。   |
   |maxInMemorySortingBatchSize    |    從更新項枚舉器提取的最大更新項數，每個階段傳遞到 API 呼叫。 對於在批量更新之前發生的記憶體內排序階段，將此參數設置為 null 將導致庫使用預設最小值（updateItems.count，1000000）。     |
   | cancellationToken|取消權杖以正常退出批量更新操作。 |

   **大量更新回應物件定義**大量更新 API 呼叫的結果包含下列屬性：

   |**參數**  |**描述** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   從提供給批量更新 API 呼叫的文檔中成功更新的文檔數。      |
   |TotalRequestUnitsConsumed (double)   |    批量更新 API 呼叫使用的總請求單位 （R）。    |
   |TotalTimeTaken (TimeSpan)   | 批量更新 API 呼叫完成執行所佔用的總時間。 |
    
## <a name="performance-tips"></a>效能秘訣 

使用大宗執行器庫時，請考慮以下幾點以獲得更好的性能：

* 為了獲得最佳性能，請從與 Azure Cosmos 帳戶的寫入區域位於同一區域的 Azure 虛擬機器運行應用程式。  

* 建議在對應于特定 Azure Cosmos 容器`BulkExecutor`的單個虛擬機器中具現化整個應用程式的單個物件。  

* 由於單個批量操作 API 執行會佔用用戶端電腦的 CPU 和網路 IO 的很大一大塊（這通過在內部生成多個任務來發生）。 避免在應用程式進程中生成多個執行批量操作 API 呼叫的併發任務。 如果在單個虛擬機器上運行的單個批量操作 API 呼叫無法使用整個容器的輸送量（如果容器的輸送量> 100 萬 RU/s），則最好創建單獨的虛擬機器以同時執行批量操作 API 呼叫。  

* 確保在`InitializeAsync()`具現化 BulkExecutor 物件以獲取目標 Cosmos 容器的分區映射後調用該方法。  

* 在應用程式的 App.Config 中，為擁有最佳效能，請確保已啟用 **gcServer**
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* 程式庫會發出可收集到記錄檔或在主控台的追蹤。 要啟用這兩個代碼，請向應用程式的 App.Config 檔添加以下代碼。

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>後續步驟

* 要瞭解 Nuget 包詳細資訊和版本資訊，請參閱[批量執行器 SDK 詳細資訊](sql-api-sdk-bulk-executor-dot-net.md)。
