---
title: 使用 .NET Standard SDK 的 Azure Cosmos DB 資料表 API
description: 了解如何在 Azure Cosmos DB 資料表 API 帳戶中儲存及查詢結構化資料
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 12/03/2019
ms.openlocfilehash: f4d6e1bb0d5db0dbfc30e14abc58321bce8d0baf
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84693170"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>透過 .NET SDK 開始使用 Azure Cosmos DB 資料表 API 和 Azure 資料表儲存體

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

您可以使用 Azure Cosmos DB 資料表 API 或 Azure 資料表儲存體，來將結構化 NoSQL 資料儲存於雲端，為索引鍵/屬性存放區提供無結構描述的設計。 由於 Azure Cosmos DB 資料表 API 和資料表儲存體均無結構描述，因此可輕易地隨著應用程式發展需求來調整資料。 您可以使用 Azure Cosmos DB 資料表 API 或資料表儲存體來儲存具彈性的資料集，例如，Web 應用程式的使用者資料、通訊錄、裝置資訊，或服務所需的其他中繼資料類型。 

本教學課程所述的範例將說明如何將[適用於 .NET 的 Microsoft Azure Cosmos DB 資料表程式庫](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)用於 Azure Cosmos DB 資料表 API 和 Azure 資料表儲存體案例。 您必須使用 Azure 服務特定的連線。 這些案例均會使用 C# 範例來探索，說明如何建立資料表、插入/更新資料、查詢資料，以及刪除資料表。

## <a name="prerequisites"></a>Prerequisites

您需要下列項目才能成功完成此範例︰

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [適用於 .NET 的 Microsoft Azure CosmosDB 資料表程式庫](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)：此程式庫目前適用於 .NET Standard 和 .NET Framework。 

* [Azure Cosmos DB 資料表 API 帳戶](create-table-dotnet.md#create-a-database-account)。

## <a name="create-an-azure-cosmos-db-table-api-account"></a>建立 Azure Cosmos DB 表格 API 帳戶

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>建立 .NET 主控台專案

在 Visual Studio 中，建立新的 .NET 主控台應用程式。 下列步驟說明如何在 Visual Studio 2019 中建立主控台應用程式。 您可以在任何類型的 .NET 應用程式 (包括 Azure 雲端服務或 Web 應用程式，以及桌面與行動應用程式) 中使用 Azure CosmosDB 資料表程式庫。 在本指南中，為求簡化，我們會使用主控台應用程式。

1. 選取 [File] \(檔案\)   >  [New] \(新增\)   >  [Project] \(專案\)  。

1. 選擇 [主控台應用程式 (.NET Core)]  ，然後選取 [下一步]  。

1. 在 [專案名稱]  欄位中輸入應用程式的名稱，例如 **CosmosTableSamples**。 (您可以視需要提供不同的名稱。)

1. 選取 [建立]  。

此範例中的所有程式碼範例均可新增至主控台應用程式 **Program.cs** 檔案的 Main() 方法。

## <a name="install-the-required-nuget-package"></a>安裝必要的 NuGet 套件

若要取得 NuGet 套件，請遵循下列步驟：

1. 在 [方案總管]  中以滑鼠右鍵按一下專案，然後選擇 [管理 NuGet 封裝]  。

1. 線上搜尋 [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)、[`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)、[`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json)、[`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)，然後選取 [安裝]  以安裝 Microsoft Azure CosmosDB 資料表程式庫。

## <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串

1. 在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽至您的 Azure Cosmos 帳戶或資料表儲存體帳戶。 

1. 開啟 [連接字串]  或 [存取金鑰]  窗格。 使用視窗右方的複製按鈕來複製**主要連接字串**。

   ![在 [連接字串] 窗格中檢視及複製主要連接字串](./media/create-table-dotnet/connection-string.png)
   
1. 若要設定連接字串，從 Visual Studio 中，以滑鼠右鍵按一下您的專案 **CosmosTableSamples**。

1. 選取 [新增]  ，然後選取 [新增項目]  。 建立檔案類型為 **TypeScript JSON 組態檔**的新檔案 **Settings.json**。 

1. 將 Settings.json 檔案中的程式碼取代為下列程式碼，並指派您的主要連接字串：

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. 以滑鼠右鍵按一下您的專案 **CosmosTableSamples**。 選取 [新增]  、[新增項目]  ，然後新增名為 **AppSettings.cs** 的類別。

1. 將下列程式碼新增至 AppSettings.cs 檔案。 此檔案會從 Settings.json 檔案中讀取連接字串，並將它指派給設定參數：

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>剖析及驗證連線詳細資料 

1. 以滑鼠右鍵按一下您的專案 **CosmosTableSamples**。 選取 [新增]  、[新增項目]  ，然後新增名為 **Common.cs** 的類別。 您將撰寫程式碼來驗證連線詳細資料，並在此類別內建立資料表。

1. 定義 `CreateStorageAccountFromConnectionString` 方法，如下所示。 此方法將剖析連接字串詳細資料，並驗證 "Settings.json" 檔案中所提供的帳戶名稱和帳戶金鑰詳細資料為有效的。 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>建立資料表 

[CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) 類別可讓您擷取表格儲存體中儲存的資料表。 因為我們在 Cosmos DB 資料表 API 帳戶中沒有任何資料表，讓我們在 **Common.cs** 類別中新增 `CreateTableAsync` 方法來建立資料表：

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

如果您收到「503 服務無法使用例外狀況」錯誤，可能是因為連線模式所需的連接埠遭到防火牆封鎖。 若要修正此問題，請開啟所需的連接埠，或使用閘道模式連線，如下列程式碼所示：

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>定義實體 

使用衍生自 [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity)的自訂類別，將實體對應至 C# 物件。 若要將實體新增至資料表，請建立一個類別來定義實體的屬性。

以滑鼠右鍵按一下您的專案 **CosmosTableSamples**。 選取 [新增]  、[新增資料夾]  ，然後將其命名為 **Model**。 在 [Model] 資料夾內新增一個名為 **CustomerEntity.cs** 的類別，然後將下列程式碼新增至其中。

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

此程式碼會定義一個實體類別，使用客戶名字作為資料列索引鍵，並使用姓氏作為分割區索引鍵。 系統會在資料表中以實體的資料分割和資料列索引鍵共同針對實體進行唯一識別。 查詢具有相同分割區索引鍵的實體，其速度快於查詢具有不同分割區索引鍵的實體，但使用不同的分割區索引鍵可針對平行作業提供更佳的延展性。 若要將實體儲存於資料表，其必須屬於支援的類型，例如衍生自 [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity) 類別。 您想要儲存在資料表中的實體屬性必須是該類型的公用屬性，而且同時支援值的取得和設定。 此外，您的實體類型必須公開無參數建構函式。

## <a name="insert-or-merge-an-entity"></a>插入或合併實體

下列程式碼範例會建立實體物件，並將它新增至資料表。 [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) \(英文\) 類別內的 InsertOrMerge 方法可用來插入或合併實體。 呼叫 [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) \(英文\) 方法來執行作業。 

以滑鼠右鍵按一下您的專案 **CosmosTableSamples**。 選取 [新增]  、[新增項目]  ，然後新增名為 **SamplesUtils.cs** 的類別。 此類別會儲存在實體上執行 CRUD 作業所需的所有程式碼。 

```csharp
 public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
 {
     if (entity == null)
     {
         throw new ArgumentNullException("entity");
     }
     try
     {
         // Create the InsertOrReplace table operation
         TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

         // Execute the operation.
         TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
         CustomerEntity insertedCustomer = result.Result as CustomerEntity;

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
         if (result.RequestCharge.HasValue)
         {
             Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
         }

         return insertedCustomer;
     }
     catch (StorageException e)
     {
         Console.WriteLine(e.Message);
         Console.ReadLine();
         throw;
     }
 }
```

### <a name="get-an-entity-from-a-partition"></a>從分割區取得實體

您也可以使用 [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) \(英文\) 類別下方的 Retrieve 方法，從分割區取得實體。 下列程式碼範例會取得分割區索引鍵的資料列索引鍵、客戶實體的電子郵件及電話號碼。 此範例也會列印出查詢實體所耗用的要求單位。 若要查詢實體，將下列程式碼附加至 **SamplesUtils.cs** 檔案： 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>刪除實體

使用更新實體時所展示的相同方法，輕鬆地在擷取實體後將其刪除。 下列程式碼會擷取並刪除客戶實體。 若要刪除實體，將下列程式碼附加至 **SamplesUtils.cs** 檔案： 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>針對範例資料執行 CRUD 作業

當您定義方法來建立資料表、插入或合併實體之後，請針對範例資料執行這些方法。 若要執行此動作，以滑鼠右鍵按一下您的專案 **CosmosTableSamples**。 選取 [新增]  、[新增項目]  ，然後新增名為 **BasicSamples.cs** 的類別，並將下列程式碼新增至其中。 此程式碼會建立資料表並將實體新增至其中。 如果您想要在專案結束時刪除實體和資料表，請從下列程式碼中移除 `table.DeleteIfExistsAsync()` 和 `SamplesUtils.DeleteEntityAsync(table, customer)` 方法的註解：

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

上述程式碼會建立以 "demo" 開頭的資料表，並將產生的 GUID 附加至資料表名稱。 它接著會使用名字和姓氏 "Harp Walter" 新增客戶實體，並於稍後更新此使用者的電話號碼。 

在本教學課程中，您會建置程式碼，針對儲存於資料表 API 帳戶的資料執行基本的 CRUD 作業。 您也可以執行進階作業，例如，批次插入資料、查詢分割區內的所有資料、查詢分割區內某個範圍的資料、在名稱開頭為指定前置詞的帳戶中列出資料表。 您可以從 [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) \(英文\) GitHub 存放庫下載完整的範例。 [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) 類別具有更多您可針對資料執行的作業。  

## <a name="run-the-project"></a>執行專案

從您的專案 **CosmosTableSamples**。 開啟名為 **Program.cs** 的類別，並在其中新增下列程式碼，以便在專案執行時呼叫 BasicSamples。

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

立即建置解決方案，然後按 F5 執行專案。 執行專案時，您將在命令提示字元中看見下列輸出：

![來自命令提示字元的輸出](./media/tutorial-develop-table-standard/output-from-sample.png)

如果您收到錯誤，指出執行專案時找不到 Settings.json 檔案，您可以將下列 XML 項目新增至專案設定來解決該問題。 以滑鼠右鍵按一下 [CosmosTableSamples]、選取 [編輯 CosmosTableSamples.csproj]，然後新增下列 itemGroup： 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
現在您可以登入 Azure 入口網站，並確認資料存在於資料表中。 

![入口網站中的結果](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>後續步驟

您現在可以繼續進行下一個教學課程，以了解如何將資料合併至 Azure Cosmos DB 資料表 API 帳戶。 

> [!div class="nextstepaction"]
>[如何查詢資料](../cosmos-db/table-import.md)
