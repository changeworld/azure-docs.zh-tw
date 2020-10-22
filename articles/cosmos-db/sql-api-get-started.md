---
title: 教學課程：建置 .NET 主控台應用程式來管理 Azure Cosmos DB SQL API 帳戶中的資料
description: 教學課程：了解如何使用 C# 主控台應用程式建立 Azure Cosmos DB SQL API 資源。
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.custom: devx-track-csharp
ms.openlocfilehash: 587fe536e860f3039bfd3a2d2c1e3c76cb40e4d5
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278465"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>教學課程：建置 .NET 主控台應用程式來管理 Azure Cosmos DB SQL API 帳戶中的資料

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [非同步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

歡迎使用 Azure Cosmos DB SQL API 入門教學課程。 完成本教學課程之後，您將會有一個主控台應用程式，可用來建立和查詢 Azure Cosmos DB 資源。

本教學課程使用 3.0 版或更新版本的 [Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)。 您可以使用 [.NET Framework 或 .NET Core](https://dotnet.microsoft.com/download)。

本教學課程涵蓋下列項目：

> [!div class="checklist"]
>
> * 建立及連線至 Azure Cosmos 帳戶
> * 在 Visual Studio 中設定專案
> * 建立資料庫和容器
> * 將項目新增至容器
> * 查詢容器
> * 在項目上執行建立、讀取、更新和刪除 (CRUD) 作業
> * 刪除資料庫

沒有時間嗎？ 別擔心！ 您可以在 [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)上找到完整的方案。 請跳至[取得完整的教學課程解決方案](#GetSolution)一節，以取得簡要指示。

讓我們開始吧！

## <a name="prerequisites"></a>必要條件

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/free/)。

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>步驟 1:建立 Azure Cosmos DB 帳戶

讓我們來建立 Azure Cosmos DB 帳戶。 如果您已經有想要使用的帳戶，請略過本節。 若要使用 Azure Cosmos DB 模擬器，請依照 [Azure Cosmos DB 模擬器](local-emulator.md)的步驟來設定模擬器。 然後向前跳到[步驟 2：設定 Visual Studio 專案](#SetupVS)。

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>步驟 2：設定 Visual Studio 專案

1. 開啟 Visual Studio，然後選取 [建立新專案]。
1. 在 [建立新專案] 中，針對 C# 選擇 [主控台應用程式 (.NET Framework)]，然後選取 [下一步]。
1. 將您的專案命名為 *CosmosGettingStartedTutorial*，然後選取 [建立]。

    :::image type="content" source="./media/sql-api-get-started/configure-cosmos-getting-started-2019.png" alt-text="設定您的專案":::

1. 在 [方案總管] 中，以滑鼠右鍵按一下 Visual Studio 解決方案底下的新主控台應用程式，然後選取 [管理 NuGet 套件]。
1. 在 [NuGet 套件管理員] 中，選取 [瀏覽]並搜尋 *Microsoft.Azure.Cosmos*。 選擇 [Microsoft.Azure.Cosmos]，然後選取 [安裝]。

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png" alt-text="設定您的專案":::

   「Azure Cosmos DB SQL API 用戶端程式庫」的套件識別碼是 [Microsoft Azure Cosmos DB 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) \(英文\)。

太棒了！ 現在已完成安裝程式，讓我們開始撰寫一些程式碼。 如需本教學課程的已完成專案，請參閱[使用 Azure Cosmos DB 開發 .NET 主控台應用程式](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)。

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>步驟 3：連線至 Azure Cosmos DB 帳戶

1. 在 *Program.cs* 檔案中，將 C# 應用程式開頭的參考，替換為下列參考：

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. 將下列常數和變數新增至 `Program` 類別。

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > 如果您熟悉舊版 .NET SDK，則可能已熟悉「集合」和「文件」等詞彙。 Azure Cosmos DB 支援多個 API 模型，因此 3.0+ 版的 .NET SDK 會使用「容器」和「項目」這些泛用詞彙。 「容器」可以是集合、圖形或資料表。 「項目」可以是文件、邊緣/頂點或資料列，並且是容器內的內容。 如需詳細資訊，請參閱[使用 Azure Cosmos DB 中的資料庫、容器和項目](account-databases-containers-items.md)。

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 尋找 Azure Cosmos DB 帳戶，然後選取 [金鑰]。

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-portal-keys.png" alt-text="設定您的專案":::

1. 在 *Program.cs* 中，以 [URI] 的值取代 `<your endpoint URL>`。 以 [主要金鑰] 的值取代 `<your primary key>`。

1. 在 **Main** 方法底下，新增名為 **GetStartedDemoAsync** 的新非同步工作，以將新的 `CosmosClient` 具現化。

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    我們會使用 **GetStartedDemoAsync** 作為進入點，來呼叫方法以操作 Azure Cosmos DB 資源。

1. 新增下列程式碼，以從 **Main** 方法執行 **GetStartedDemoAsync** 非同步工作。 **Main** 方法會攔截例外狀況並將它們寫入主控台。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. 選取 F5 鍵執行您的應用程式。

    主控台會顯示訊息：**示範結束，按任意鍵以結束。** 此訊息會確認您的應用程式已建立 Azure Cosmos DB 的連線。 您可以接著關閉主控台視窗。

恭喜！ 您已成功連線至 Azure Cosmos DB 帳戶。

## <a name="step-4-create-a-database"></a>步驟 4：建立資料庫

資料庫是分割於多個容器之項目的邏輯容器。 [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) 類別的 `CreateDatabaseIfNotExistsAsync` 或 `CreateDatabaseAsync` 方法可以建立資料庫。

1. 將 `CreateDatabaseAsync` 方法複製並貼到 `GetStartedDemoAsync` 方法下方。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync` 會以識別碼 `FamilyDatabase` 建立新資料庫 (如果該資料庫不存在)，此識別碼會指定於 `databaseId` 欄位中。

1. 將程式碼複製並貼到 CosmosClient 具現化時的所在處底下，以呼叫您剛才新增的 **CreateDatabaseAsync** 方法。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    您的 *Program.cs* 應該會有如下樣貌，且其中已填入您的端點和主要金鑰。

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;

            // The database we will create
            private Database database;

            // The container we will create.
            private Container container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. 選取 F5 鍵執行您的應用程式。

   > [!NOTE]
   > 如果您收到「503 服務無法使用例外狀況」錯誤，可能是因為直接連線模式所需的[連接埠](sql-sdk-connection-modes.md#service-port-ranges)遭到防火牆封鎖。 若要修正此問題，請開啟所需的連接埠，或使用閘道模式連線，如下列程式碼所示：
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

恭喜！ 您已成功建立 Azure Cosmos 資料庫。  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>步驟 5：建立容器

> [!WARNING]
> `CreateContainerIfNotExistsAsync` 方法會建立具有定價含意的新容器。 如需詳細資訊，請造訪 [定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)。
>
>

使用 `CosmosDatabase` 類別中的 [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) 或 [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) 函式可以建立容器。 容器中包含項目 (在 SQL API 中為 JSON 文件) 和 JavaScript 中相關聯的伺服器端應用程式邏輯，例如預存程序、使用者定義的函式和觸發程序。

1. 將 `CreateContainerAsync` 方法複製並貼到 `CreateDatabaseAsync` 方法下方。 `CreateContainerAsync` 會以識別碼 `FamilyContainer` 建立新容器 (如果該容器不存在)，其做法是使用由 `LastName` 屬性分割的 `containerId` 欄位中指定的識別碼。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. 將程式碼複製並貼到 CosmosClient 具現化時的所在處底下，以呼叫您剛才新增的 **CreateContainer** 方法。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. 選取 F5 鍵執行您的應用程式。

恭喜！ 您已成功建立 Azure Cosmos 容器。  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>步驟 6：將項目新增至容器

`CosmosContainer`類別的 [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_)方法可以建立項目。 使用 SQL API 時，預期的項目會是文件，這是使用者定義的任意 JSON 內容。 您現在可以將項目插入 Azure Cosmos 容器中。

首先，我們來建立 `Family` 類別，以代表此範例中儲存在 Azure Cosmos DB 內的物件。 我們也會建立 `Family` 內使用的 `Parent`、`Child`、`Pet`、`Address` 子類別。 項目必須將 `Id` 屬性序列化為 JSON 中的 `id`。

1. 選取 Ctrl+Shift+A 以開啟 [新增項目]。 將新的類別 `Family.cs` 新增至您的專案。

    :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png" alt-text="設定您的專案":::

1. 將 `Family`、`Parent`、`Child`、`Pet` 和 `Address` 類別複製並貼到 `Family.cs` 中。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. 回到 *Program.cs*中，在 `CreateContainerAsync` 方法後面新增 `AddItemsToContainerAsync` 方法。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    程式碼會先檢查，確認沒有使用相同識別碼的項目存在。 我們會插入兩個項目，一個給「Andersen 家族」，另一個給「Wakefield 家族」。

1. 在 `GetStartedDemoAsync` 方法中新增對 `AddItemsToContainerAsync` 的呼叫。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. 選取 F5 鍵執行您的應用程式。

恭喜！ 您已成功建立兩個 Azure Cosmos 項目。  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>步驟 7：查詢 Azure Cosmos DB 資源

Azure Cosmos DB 支援對儲存於每個容器中的 JSON 文件進行豐富查詢。 如需詳細資訊，請參閱[開始使用 SQL 查詢](sql-api-sql-query.md)。 下列範例程式碼示範如何對上一個步驟中插入的項目執行查詢。

1. 在 `AddItemsToContainerAsync` 方法之後複製並貼上 `QueryItemsAsync` 方法。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. 在 ``GetStartedDemoAsync`` 方法中新增對 ``QueryItemsAsync`` 的呼叫。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. 選取 F5 鍵執行您的應用程式。

恭喜！ 您已成功查詢 Azure Cosmos 容器。

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>步驟 8：取代 JSON 項目

現在，我們會更新 Azure Cosmos DB 中的項目。 我們會變更 `Family` 的 `IsRegistered` 屬性，以及其中一個子系的 `Grade`。

1. 在 `QueryItemsAsync` 方法之後複製並貼上 `ReplaceFamilyItemAsync` 方法。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. 在 `GetStartedDemoAsync` 方法中新增對 `ReplaceFamilyItemAsync` 的呼叫。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. 選取 F5 鍵執行您的應用程式。

恭喜！ 您已成功取代 Azure Cosmos 項目。

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>步驟 9：刪除項目

現在，我們將刪除 Azure Cosmos DB 中的項目。

1. 在 `ReplaceFamilyItemAsync` 方法之後複製並貼上 `DeleteFamilyItemAsync` 方法。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. 在 `GetStartedDemoAsync` 方法中新增對 `DeleteFamilyItemAsync` 的呼叫。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. 選取 F5 鍵執行您的應用程式。

恭喜！ 您已成功刪除 Azure Cosmos 項目。

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>步驟 10：刪除資料庫

我們現在會刪除我們的資料庫。 刪除已建立的資料庫會移除資料庫和所有子系資源。 這些資源包括容器、項目，以及任何預存程式、使用者定義的函式和觸發程序。 我們也會處置 `CosmosClient` 執行個體。

1. 在 `DeleteFamilyItemAsync` 方法之後複製並貼上 `DeleteDatabaseAndCleanupAsync` 方法。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. 在 ``GetStartedDemoAsync`` 方法中新增對 ``DeleteDatabaseAndCleanupAsync`` 的呼叫。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. 選取 F5 鍵執行您的應用程式。

恭喜！ 您已成功刪除 Azure Cosmos 資料庫。

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>步驟 11：一起執行您的 C# 主控台應用程式！

在 Visual Studio 中選取 F5，即可在偵錯模式下建置和執行應用程式。

您應該會在主控台視窗中看到整個應用程式的輸出。 輸出會顯示我們所新增查詢的結果。 其應該會與下列範例文字相符。

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

恭喜！ 您已經完成本教學課程，並擁有運作中的 C# 主控台應用程式！

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a>取得完整的教學課程解決方案

如果您沒有時間完成本教學課程中的步驟，或只想要下載程式碼範例，您可加以下載。

若要建置 `GetStarted` 解決方案，您需要滿足下列必要條件：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/free/)。
* [Azure Cosmos DB 帳戶][cosmos-db-create-account]。
* 您可以在 GitHub 上找到 [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) 方案。

若要在 Visual Studio 中還原 Azure Cosmos DB .NET SDK 的參考，請在 [方案總管] 中的解決方案上按一下滑鼠右鍵，然後選取 [還原 NuGet 套件]。 接下來，在 *App.config* 檔案中，更新 `EndPointUri` 和 `PrimaryKey` 值，如[步驟 3：連線至 Azure Cosmos DB 帳戶](#Connect)所述。

建置就這麼容易，繼續努力！

## <a name="next-steps"></a>後續步驟

* 需要更複雜的 ASP.NET MVC 教學課程嗎？ 請參閱[教學課程：使用 .NET SDK，透過 Azure Cosmos DB 開發 ASP NET Core MVC Web 應用程式](sql-api-dotnet-application.md)。
* 想要使用 Azure Cosmos DB 進行規模和效能測試嗎？ 請參閱 [Azure Cosmos DB 的效能和級別測試](performance-testing.md)。
* 若要了解如何監視 Azure Cosmos DB 要求、使用量及儲存體，請參閱[監視 Azure Cosmos DB 中的效能和儲存體計量](monitor-accounts.md)。
* 若要對我們的範例資料集執行查詢，請參閱 [Query Playground](https://www.documentdb.com/sql/demo)。
* 若要深入了解 Azure Cosmos DB，請參閱[歡迎使用 Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)。

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
