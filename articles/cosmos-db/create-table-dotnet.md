---
title: 快速入門：資料表 API 與 .NET - Azure Cosmos DB
description: 本快速入門示範如何使用 Azure Cosmos DB 資料表 API，以使用 Azure 入口網站與 .NET 建立應用程式
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 629adfe558aec71e156e50c75aa0891eac5a8bcf
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370817"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>快速入門：使用 .NET SDK 和 Azure Cosmos DB 建立資料表 API 應用程式 

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
>  

本快速入門示範如何使用 .NET 與 Azure Cosmos DB [資料表 API](table-introduction.md)，透過從 GitHub 複製範例來建置應用程式。 本快速入門也會示範如何建立 Azure Cosmos DB 帳戶，以及如何使用資料總管在以 Web 為基礎的 Azure 入口網站中建立資料表和實體。

## <a name="prerequisites"></a>Prerequisites

如果您尚未安裝 Visual Studio 2019，您可以下載並使用**免費**的[Visual Studio 2019 的社區版](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>新增資料表

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>新增範例資料

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 GitHub 複製「資料表」應用程式、設定連接字串，然後加以執行。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟命令提示字元，建立名為 git-samples 的新資料夾，然後關閉命令提示字元。

   ```bash
   md "C:\git-samples"
   ```

2. 開啟 git 終端機視窗 (例如 git bash)，並使用 `cd` 命令變更至要安裝範例應用程式的新資料夾。

   ```bash
   cd "C:\git-samples"
   ```

3. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

## <a name="open-the-sample-application-in-visual-studio"></a>在 Visual Studio 中開啟範例應用程式

1. 在 Visual Studio 中，從 [檔案] 功能表上，選擇 [開啟]，然後選擇 [專案/解決方案]。 

   ![開啟解決方案](media/create-table-dotnet/azure-cosmosdb-open-solution.png) 

2. 瀏覽至您複製範例應用程式的資料夾，然後開啟 TableStorage.sln 檔案。

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。 這可讓您的應用程式與託管資料庫進行通訊。 

1. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下 [連接字串]。 使用視窗右方的複製按鈕來複製**主要連接字串**。

   ![在 [連接字串] 窗格中檢視及複製主要連接字串](./media/create-table-dotnet/connection-string.png)

2. 在 Visual Studio 中開啟 **Settings.json** 檔案。 

3. 將來自入口網站的**主要連接字串**貼到 StorageConnectionString 值中。 在引號內貼上字串。

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. 按下 CTRL + S，以儲存 **Settings.json** 檔案。

您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 

## <a name="build-and-deploy-the-app"></a>建置並部署應用程式

1. 在 Visual Studio 中，於 [方案總管] 中的 **CosmosTableSamples** 專案上按一下滑鼠右鍵，然後按一下 [管理 NuGet 套件]。 

   ![Manage NuGet Packages](media/create-table-dotnet/azure-cosmosdb-manage-nuget.png)

2. 在 NuGet [瀏覽] 方塊中，輸入Microsoft.Azure.Cosmos.Table。 這會尋找 Cosmos DB 資料表 API 用戶端程式庫。 請注意，此文件庫目前僅適用於 .NET Framework 與 .NET Standard。 
   
   ![[NuGet 瀏覽] 索引標籤](media/create-table-dotnet/azure-cosmosdb-nuget-browse.png)

3. 按一下 [安裝] 可安裝 **Microsoft.Azure.Cosmos.Table** 程式庫。 這會安裝 Azure Cosmos DB 資料表 API 套件以及所有相依性。

4. 當您執行整個應用程式時，會將範例資料插入資料表實體，並於結束時刪除，因此如果您執行完整的範例，並不會看到插入任何資料。 不過，您可以插入一些中斷點來檢視資料。 開啟 BasicSamples.cs 檔案，然後以滑鼠右鍵按一下第 52 行，選取 [中斷點]，然後選取 [插入中斷點]。 在第 55 行上插入另一個中斷點。

   ![新增中斷點](media/create-table-dotnet/azure-cosmosdb-breakpoint.png) 

5. 按 F5 執行應用程式。 主控台視窗會顯示 Azure Cosmos DB 中新資料表資料庫的名稱 (在此情況下為 demoa13b1)。 
    
   ![主控台輸出](media/create-table-dotnet/azure-cosmosdb-console.png)

   到達第一個中斷點時，請回到 Azure 入口網站中的 [資料總管]。 按一下 [重新整理] 按鈕、展開示範 * 資料表，然後按一下 [實體]。 右側的 [實體] 索引標籤會顯示針對 Walter Harp 新增的新實體。 請注意，新實體的電話號碼為 425-555-0101。

   ![新的實體](media/create-table-dotnet/azure-cosmosdb-entity.png)
    
   如果您收到錯誤，指出執行專案時找不到 Settings.json 檔案，您可以將下列 XML 項目新增至專案設定來解決該問題。 以滑鼠右鍵按一下 [CosmosTableSamples]、選取 [編輯 CosmosTableSamples.csproj]，然後新增下列 itemGroup： 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. 關閉 [資料總管] 中的 [實體] 索引標籤。
    
7. 按 F5 執行應用程式到下一個中斷點。 

    到達中斷點時，請切換回 Azure 入口網站，再按一下 [實體] 可開啟 [實體] 索引標籤，並請注意，電話號碼已經更新為 425-555-0105。

8. 按 F5 以執行應用程式。 
 
   應用程式會新增實體，以在資料表 API 目前不支援的進階範例應用程式中使用。 然後，應用程式會刪除範例應用程式所建立的資料表。

9. 在主控台視窗中，按下 Enter 可結束執行應用程式。 
  

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用資料總管來建立資料表，以及如何執行應用程式。  現在，您可以使用資料表 API 來查詢您的資料。  

> [!div class="nextstepaction"]
> [將資料表資料匯入至資料表 API](table-import.md)

