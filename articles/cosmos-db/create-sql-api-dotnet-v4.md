---
title: 使用 .NET V4 SDK 來管理 Azure Cosmos DB SQL API 資源
description: 使用 .NET V4 SDK 建置主控台應用程式來管理 Azure Cosmos DB SQL API 帳戶資源的快速入門。
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: 67ed8c54e32a301a50fa49b1ebba284b216d8664
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276304"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>快速入門：使用 .NET V4 SDK 建置主控台應用程式來管理 Azure Cosmos DB SQL API 帳戶資源。

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

開始使用適用於 .NET 的 Azure Cosmos DB SQL API 用戶端程式庫。 請依照此文件中的步驟安裝 .NET V4 (Azure.Cosmos) 套件、建置應用程式，並針對 Azure Cosmos DB 中儲存的資料嘗試使用基本 CRUD 作業的範例程式碼。 

Azure Cosmos DB 是 Microsoft 的快速 NoSQL 資料庫，可支援任何規模的開放式 API。 您可以使用 Azure Cosmos DB 快速地建立及查詢機碼/值、文件與圖形資料庫。 使用適用於 .NET 的 Azure Cosmos DB SQL API 用戶端程式庫來：

* 建立 Azure Cosmos 資料庫與容器
* 將範例資料新增至容器
* 查詢資料 
* 刪除資料庫

[程式庫原始程式碼](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [套件 (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [免費建立一個](https://azure.microsoft.com/free/)，或者您可以[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，無須 Azure 訂用帳戶，也無須任何費用和約定付款。 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). 您可以藉由執行 `dotnet --version`，來確認您環境中可使用的版本。

## <a name="setting-up"></a>設定

此節將逐步引導您建立 Azure Cosmos 帳戶，並設定使用適用於 .NET 的 Azure Cosmos DB SQL API 用戶端程式庫來管理資源的專案。 此文章中描述的範例程式碼會在該資料庫內建立 `FamilyDatabase` 資料庫與家庭成員 (每個家庭成員都是一個項目)。 每個家庭成員都有屬性，例如 `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`。 `LastName` 屬性會作為容器的資料分割索引鍵使用。 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>建立 Azure Cosmos 帳戶

如果您使用 [免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 選項來建立 Azure Cosmos 帳戶，則必須建立類型為 **SQL API** 的 Azure Cosmos DB 帳戶。 已為您建立 Azure Cosmos DB 測試帳戶。 您不需要明確建立帳戶，因此您可以略過此小節並移至下一小節。

如果您有自己的 Azure 訂用帳戶，或已建立免費訂用帳戶，您應該明確地建立 Azure Cosmos 帳戶。 下列程式碼會建立包含工作階段一致性的 Azure Cosmos 帳戶。 該帳戶會在 `South Central US` 與 `North Central US` 中複寫。  

您可以使用 Azure Cloud Shell 來建立 Azure Cosmos 帳戶。 Azure Cloud Shell 是可經由瀏覽器存取的已驗證互動式殼層，應用在 Azure 資源管理上。 它可讓您彈性地選擇最適合您工作方式的殼層體驗 (Bash 或 PowerShell)。 在本快速入門中，選擇 [Bash] 模式。 Azure Cloud Shell 也需要儲存體帳戶，您可以在出現提示時建立一個。

選取下列程式碼旁邊的 [試試看] 按鈕，選擇 [Bash] 模式，再選取 [建立儲存體帳戶] 並登入 Cloud Shell。 接下來，複製下列程式碼並貼到 Azure Cloud Shelll 中執行。 Azure Cosmos 帳戶名稱必須是全域唯一的，請務必先更新 `mysqlapicosmosdb` 值，再執行命令。

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

建立 Azure Cosmos 帳戶需要一些時間，一旦作業成功，您就可以看到確認輸出。 命令順利完成之後，請登入 [Azure 入口網站](https://portal.azure.com/)，並確認具有指定名稱的 Azure Cosmos 帳戶存在。 您可以在建立資源之後，關閉 [Azure Cloud Shell] 視窗。 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>建立新的 .NET 應用程式

在您慣用的編輯器或 IDE 中，建立新的 .NET 應用程式。 從您的本機電腦開啟 Windows 命令提示字元或終端機視窗。 您將會從命令提示字元或終端機執行下一節中的所有命令。  執行下列 dotnet new 命令來建立名稱為 `todo` 的新應用程式。 --langVersion 參數會設定所建立專案檔中的 LangVersion 屬性。

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

   ```bash
   cd todo
   dotnet build
   ```

組建的預期輸出看起來應像這樣：

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>安裝 Azure Cosmos DB 套件

若您仍在應用程式目錄中，請使用 dotnet add package 命令安裝適用於 .NET Core 的 Azure Cosmos DB 用戶端程式庫。

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>從 Azure 入口網站複製您的 Azure Cosmos 帳戶認證

範例應用程式需要驗證您的 Azure Cosmos 帳戶。 若要進行驗證，您應該將 Azure Cosmos 帳號認證傳遞給應用程式。 請依照下列步驟取得您的 Azure Cosmos 帳戶認證：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 瀏覽至 Azure Cosmos 帳戶。

1. 開啟 [金鑰] 窗格，然後複製您帳戶的 **URI** 與 **主索引鍵** 。 在下一個步驟中，您會將 URI 和索引鍵值新增至環境變數。

## <a name="object-model"></a><a id="object-model"></a>物件模型

在開始建置應用程式之前，讓我們看一下 Azure Cosmos DB 中的資源階層，以及用於建立及存取這些資源的物件模型。 Azure Cosmos DB 會依照下列順序建立資源：

* Azure Cosmos 帳戶 
* 資料庫 
* 容器 
* 項目

若要了解有關不同實體的階層，請參閱[使用 Azure Cosmos DB 中的資料庫、容器和項目](account-databases-containers-items.md) \(部分機器翻譯\) 一文。 您將使用下列 .NET 類別與這些資源互動：

* CosmosClient - 此類別提供適用於 Azure Cosmos DB 服務的用戶端邏輯表示法。 用戶端物件會用於設定及執行針對服務的要求。
* CreateDatabaseIfNotExistsAsync - 此方法會建立 (如果不存在) 或取得 (如果已經存在) 資料庫資源作為非同步作業。 
* CreateContainerIfNotExistsAsync - 此方法會建立 (如果不存在) 或取得 (如果已經存在) 容器作為非同步作業。 您可以檢查來自回應的狀態碼，以判斷容器是新建立的 (201) 還是傳回的現有容器 (200)。 
* CreateItemAsync - 此方法會在容器內建立項目。
* UpsertItemAsync - 此方法會在容器內建立項目 (如果尚未存在) 或取代項目 (如果已存在)。 
* GetItemQueryIterator - 此方法會使用具有參數化值的 SQL 陳述式，在 Azure Cosmos 資料庫中的容器下建立項目的查詢。 
* DeleteAsync - 從您的 Azure Cosmos 帳戶中刪除指定的資料庫。 `DeleteAsync` 方法只會刪除資料庫。

 ## <a name="code-examples"></a><a id="code-examples"></a>程式碼範例

此文章中描述的範例程式碼會在 Azure Cosmos DB 中建立家庭資料庫。 家庭資料庫包含家庭詳細資料，例如姓名、地址、位置、相關聯的家長、子女和寵物。 將資料填入您的 Azure Cosmos 帳戶之前，請定義家庭項目的屬性。 在範例應用程式的根層級建立一個名為 `Family.cs` 的新類別，並在其中新增下列程式碼：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>新增 using 指示詞並定義用戶端物件

從專案目錄中，在您的編輯器中開啟 `Program.cs` 檔案，並在應用程式的頂端新增下列 using 指示詞：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


在 `Program` 類別中新增下列全域變數。 這些變數將包含端點和授權金鑰、資料庫名稱，以及您將建立的容器。 請務必根據您的環境取代端點和授權金鑰值。 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

最後，取代 `Main` 方法：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>建立資料庫 

在 `program.cs` 類別內定義 `CreateDatabaseAsync` 方法。 如果 `FamilyDatabase` 不存在，則此方法會建立它。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>建立容器

在 `Program` 類別內定義 `CreateContainerAsync` 方法。 如果 `FamilyContainer` 不存在，則此方法會建立它。 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>建立項目

透過使用下列程式碼新增 `AddItemsToContainerAsync` 方法來建立家庭項目。 您可以使用 `CreateItemAsync` 或 `UpsertItemAsync` 方法來建立項目：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>查詢項目

插入項目之後，您可以執行查詢以取得 "Andersen" 家庭的詳細資料。 下列程式碼顯示如何直接使用 SQL 查詢來執行查詢。 取得 "Andersen" 家庭詳細資料的 SQL 查詢是：`SELECT * FROM c WHERE c.LastName = 'Andersen'`。 在 `Program` 類別中定義 `QueryItemsAsync` 方法，並在其中加入下列程式碼：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>取代項目 

讀取家庭項目，然後透過下列程式碼新增 `ReplaceFamilyItemAsync` 方法來將其更新。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>刪除項目 

透過使用下列程式碼新增 `DeleteFamilyItemAsync` 方法來刪除家庭項目。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>刪除資料庫 

最後，您可以使用下列程式碼來刪除新增 `DeleteDatabaseAndCleanupAsync` 方法的資料庫：

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

新增所有必要的方法之後，請儲存 `Program` 檔案。 

## <a name="run-the-code"></a>執行程式碼

接下來建置並執行應用程式，以建立 Azure Cosmos DB 資源。

   ```bash
   dotnet run
   ```

執行應用程式時會產生下列輸出。 您也可以登入 Azure 入口網站並驗證是否已建立資源：

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

您可以透過登入 Azure 入口網站並查看 Azure Cosmos 帳戶中的必要項目，來驗證是否已建立資料。 

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 Azure CLI 或 Azure PowerShell 移除 Azure Cosmos 帳戶與對應的資源群組。 下列命令說明如何使用 Azure CLI 刪除資源群組：

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何建立 Azure Cosmos 帳戶、使用 .NET Core 應用程式建立資料庫與容器。 您現在可以使用下列文章中的指示，將其他資料匯入到您的 Azure Cosmos 帳戶。 

> [!div class="nextstepaction"]
> [將資料匯入到 Azure Cosmos DB](import-data.md)
