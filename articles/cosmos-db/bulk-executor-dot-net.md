---
title: 在 Azure Cosmos DB 中使用大量執行程式 .NET 程式庫來進行大量匯入和更新作業
description: 使用大量執行程式 .NET 程式庫來大量匯入和更新 Azure Cosmos DB 檔。
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 829ddc0b63031722cdcb572a2833926a7837d05d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004114"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>使用大量執行程式 .NET 程式庫，在 Azure Cosmos DB 中執行大量作業

> [!NOTE]
> 本文所描述的大量執行程式程式庫是針對使用 .NET SDK 2.x 版的應用程式所維護。 針對新的應用程式，您可以使用[.NET SDK 3.x 版](tutorial-sql-api-dotnet-bulk-import.md)直接提供的**大量支援**，而不需要任何外部程式庫。 

> 如果您目前使用大量執行程式程式庫，並打算遷移至較新 SDK 上的大量支援，請使用「 [遷移指南](how-to-migrate-from-bulk-executor-library.md) 」中的步驟來遷移您的應用程式。

本教學課程說明如何使用大量執行程式 .NET 程式庫將文件匯入並更新至 Azure Cosmos 容器。 若要深入瞭解大量執行程式程式庫，以及它如何協助您利用大量輸送量和儲存體，請參閱 [大量執行](bulk-executor-overview.md) 程式程式庫的簡介文章。 在本教學課程中，您將看到範例 .NET 應用程式，可將隨機產生的檔大量匯入 Azure Cosmos 容器中。 匯入之後，應用程式會說明如何將修補程式指定為可在特定文件欄位上執行的作業，來大量更新匯入的資料。

目前，只有 Azure Cosmos DB SQL API 和 Gremlin API 帳戶支援大量執行程式程式庫。 本文說明如何搭配 SQL API 帳戶使用大量執行程式 .NET 程式庫。 若要瞭解如何搭配 Gremlin API 帳戶使用大量執行程式 .NET 程式庫，請參閱 [在 Azure Cosmos DB GREMLIN api 中執行大量作業](bulk-executor-graph-dotnet.md)。

## <a name="prerequisites"></a>先決條件

* 如果您還沒有安裝 Visual Studio 2019，可以下載並使用 [Visual Studio 2019 社區版](https://www.visualstudio.com/downloads/)。 請確定您在 Visual Studio 設定期間啟用「Azure 開發」。

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 您可以[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，無須 Azure 訂用帳戶，也無須任何費用和約定付款。 或者，您可以使用 [Azure Cosmos DB 模擬器](https://docs.microsoft.com/azure/cosmos-db/local-emulator) 搭配 `https://localhost:8081` 端點。 [驗證要求](local-emulator.md#authenticating-requests)中會提供主索引鍵。

* 使用 .NET 快速入門文章中＜[建立資料庫帳戶](create-sql-api-dotnet.md#create-account)＞一節所述的步驟，建立 Azure Cosmos DB SQL API 帳戶。

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在讓我們從 GitHub 下載範例 .NET 應用程式，切換至使用程式碼。 此應用程式會對 Azure Cosmos 帳戶中儲存的資料執行大量作業。 若要複製應用程式，請開啟命令提示字元，流覽至您要複製的目錄，然後執行下列命令：

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

複製的存放庫包含兩個範例 "BulkImportSample" 和 "BulkUpdateSample"。 您可以開啟其中一個範例應用程式、使用 Azure Cosmos DB 帳戶的連接字串更新 App.config 檔案中的連接字串、建立解決方案，然後執行它。

"BulkImportSample" 應用程式會產生隨機檔，並將它們大量匯入到您的 Azure Cosmos 帳戶。 "BulkUpdateSample" 應用程式會將修補程式指定為可在特定文件欄位上執行的作業，來大量更新匯入的文件。 在後面幾節中，您將檢閱這裡每一個範例應用程式中的程式碼。

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>將資料大量匯入至 Azure Cosmos 帳戶

1. 瀏覽至 "BulkImportSample" 資料夾，然後開啟 "BulkImportSample.sln" 檔案。  

2. 從 App.config 檔案取出 Azure Cosmos DB 的連接字串，如下列程式碼所示：  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   大量匯入工具會使用資料庫名稱、容器名稱和 App.config 檔案中指定的輸送量值，來建立新的資料庫和容器。

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

4. 針對等候時間和節流的要求，BulkExecutor 物件會使用高重試值進行初始化。 接著，這些值會設定為 0，如此會將壅塞控制傳遞給 BulkExecutor 以決定其存留期。  

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

5. 應用程式會叫用 BulkImportAsync API。 .NET 程式庫提供大量匯入 API 的兩個多載-一個接受已序列化 JSON 檔的清單，另一個則接受已還原序列化的 POCO 檔案清單。 若要深入瞭解每個多載方法的定義，請參閱 [API 檔](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet)。

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
   
   |**參數**  |**說明** |
   |---------|---------|
   |enableUpsert    |   在檔上啟用 upsert 作業的旗標。 如果具有指定識別碼的檔已經存在，則會更新。 預設會設定為 false。      |
   |disableAutomaticIdGeneration    |    停用自動產生識別碼的旗標。 預設會設定為 true。     |
   |maxConcurrencyPerPartitionKeyRange    | 每個資料分割索引鍵範圍的最大並行程度，若設定為 Null，程式庫會使用預設值 20。 |
   |maxInMemorySortingBatchSize     |  從檔列舉值提取的檔數目上限，會傳遞至每個階段中的 API 呼叫。 針對大量匯入之前發生的記憶體中排序階段，將此參數設定為 null 將會導致程式庫使用預設的最小值 (檔。 count，1000000) 。       |
   |cancellationToken    |    要正常結束大量匯入作業的解除標記。     |

   **大量匯入回應物件定義**大量匯入 API 呼叫的結果包含下列屬性：

   |**參數**  |**說明**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  從提供給大量匯入 API 呼叫的檔總數中，成功匯入的檔總數。       |
   |TotalRequestUnitsConsumed (double)   |   大量匯入 API 呼叫取用的要求單位 (RU) 總數。      |
   |TotalTimeTaken (TimeSpan)    |   大量匯入 API 呼叫完成執行所花費的總時間。      |
   |BadInputDocuments (List\<object>)   |     格式錯誤而未成功匯入大量匯入 API 呼叫的文件清單。 修正傳回的檔，然後重試匯入。 格式錯誤的文件包含其識別碼值不是字串 (Null 或任何其他視為無效的資料類型) 的文件。    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>大量更新 Azure Cosmos 帳戶中的資料

您可以使用 BulkUpdateAsync API 來更新現有的文件。 在此範例中，您會將 `Name` 欄位設定為新的值，並 `Description` 從現有的檔中移除欄位。 如需完整的支援更新作業集，請參閱 [API 檔](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)。

1. 瀏覽至 "BulkUpdateSample" 資料夾，然後開啟 "BulkUpdateSample.sln" 檔案。  

2. 定義更新專案以及對應的欄位更新作業。 在此範例中，您將使用 `SetUpdateOperation` 更新 `Name` 欄位，並 `UnsetUpdateOperation` `Description` 從所有檔中移除欄位。 您也可以執行其他作業，例如透過指定值來遞增文件欄位、將特定值推送至陣列欄位中，或是從陣列欄位中移除特定值。 若要了解大量更新 API 提供的不同方法，請參閱 [API 文件](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)。

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

3. 應用程式會叫用 BulkUpdateAsync API。 若要瞭解 BulkUpdateAsync 方法的定義，請參閱 [API 檔](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet)。  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync 方法可接受下列參數：**

   |**參數**  |**說明** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   每個分割區索引鍵範圍的最大並行程度，將此參數設定為 null，會讓程式庫使用預設值 (20) 。   |
   |maxInMemorySortingBatchSize    |    在每個階段中傳遞給 API 呼叫的更新專案列舉值所提取的更新專案數目上限。 針對大量更新之前發生的記憶體中排序階段，將此參數設定為 null 將會導致程式庫使用預設的最小值 (updateItems，1000000) 。     |
   | cancellationToken|要正常結束大量更新作業的解除標記。 |

   **大量更新回應物件定義**大量更新 API 呼叫的結果包含下列屬性：

   |**參數**  |**說明** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   在提供給大量更新 API 呼叫的檔總數中，成功更新的檔數目。      |
   |TotalRequestUnitsConsumed (double)   |    大量更新 API 呼叫所耗用 (ru) 的總要求單位數。    |
   |TotalTimeTaken (TimeSpan)   | 大量更新 API 呼叫完成執行所花費的總時間。 |
    
## <a name="performance-tips"></a>效能秘訣 

使用大量執行程式程式庫時，請考慮下列各點以獲得更好的效能：

* 為了達到最佳效能，請從與您 Azure Cosmos 帳戶寫入區域相同區域的 Azure 虛擬機器執行您的應用程式。  

* 建議您在 `BulkExecutor` 對應到特定 Azure Cosmos 容器的單一虛擬機器中，將整個應用程式的單一物件具現化。  

* 由於單一大量作業 API 執行會耗用大量用戶端機器的 CPU 和網路 IO (因此會在內部產生多個工作) 。 避免在執行大量作業 API 呼叫的應用程式進程內，產生多個並行工作。 如果在單一虛擬機器上執行的單一大量作業 API 呼叫無法取用整個容器的輸送量 (如果您的容器輸送量 > 1000000 RU/秒) ，則建議建立個別的虛擬機器，以同時執行大量作業 API 呼叫。  

* 請確定在具 `InitializeAsync()` 現化 BulkExecutor 物件之後叫用方法，以提取目標 Cosmos 容器的分割區對應。  

* 在應用程式的 App.Config 中，為擁有最佳效能，請確保已啟用 **gcServer**
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* 程式庫會發出可收集到記錄檔或在主控台的追蹤。 若要啟用這兩者，請將下列程式碼加入至應用程式的 App.Config 檔。

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

* 若要瞭解 NuGet 套件詳細資料和版本資訊，請參閱 [大量執行程式 SDK 詳細資料](sql-api-sdk-bulk-executor-dot-net.md)。
