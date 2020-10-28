---
title: 使用 Gremlin API 來建置 Azure Cosmos DB .NET Framework、Core 應用程式
description: 提供可用來連線及查詢 Azure Cosmos DB 的 .NET Framework/Core 程式碼範例
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: jasonh
ms.custom: devx-track-dotnet
ms.openlocfilehash: 0b81bf951df6db0da7ed049c435a319f6ee33b8a
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479046"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>快速入門：使用 Azure Cosmos DB Gremlin API 帳戶建置 .NET Framework 或 Core 應用程式

> [!div class="op_single_selector"]
> * [Gremlin 主控台](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本快速入門示範如何使用 Azure 入口網站建立 Azure Cosmos DB [Gremlin API](graph-introduction.md) 帳戶、資料庫和圖表 (容器)。 您會接著使用開放原始碼 [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) 來建置和執行主控台應用程式。  

## <a name="prerequisites"></a>必要條件

如果尚未安裝 Visual Studio 2019，您可以下載並使用 **免費** 的 [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發** 。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>新增圖形

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 GitHub 複製 Gremlin API 應用程式、設定連接字串，然後加以執行。 您會看到，以程式設計方式來處理資料有多麼的容易。 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. 然後開啟 Visual Studio 並開啟方案檔案。

5. 在專案中還原 NuGet 套件。 這應該包含 Gremlin.Net 驅動程式，以及 Newtonsoft.Json 套件。


6. 您也可以使用 Nuget 套件管理員或 [nuget 命令列公用程式](/nuget/install-nuget-client-tools)，手動安裝 Gremlin.Net 驅動程式： 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要瞭解如何在程式碼中建立資料庫資源，則可檢閱下列程式碼片段。 或者也可以直接跳至[更新您的連接字串](#update-your-connection-string)。 

下列程式碼片段全部取自 Program.cs 檔案。

* 根據上面建立的帳戶來設定您的連線參數： 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* 要執行的 Gremlin 命令會列於字典中：

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* 使用上面提供的參數來建立新的 `GremlinServer` 和 `GremlinClient` 連線物件：

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* 使用 `GremlinClient` 物件搭配非同步工作，執行每個 Gremlin 查詢。 您可以從上一個步驟中定義的字典讀取 Gremlin 查詢，然後加以執行。 之後，使用 Newtonsoft.Json 套件中的 `JsonSerializer` 類別，取得結果並讀取已格式化為字典的值：

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽至您的圖形資料庫帳戶。 在 [概觀] 索引標籤中，您可以看到兩個端點： 
 
   **.NET SDK URI** - 當您使用 Microsoft.Azure.Graphs 程式庫連線至圖形帳戶時，將會使用此值。 

   **Gremlin 端點** - 當您使用 Gremlin.Net 程式庫連線至圖形帳戶時，將會使用此值。

    :::image type="content" source="./media/create-graph-dotnet/endpoint.png" alt-text="複製端點&quot;:::

   若要執行此範例，請複製 [Gremlin 端點] 值，並刪除結尾處的連接埠號碼，使 URI 變成 `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`。 端點值看起來應該像 `testgraphacct.gremlin.cosmosdb.azure.com`

1. 接下來，瀏覽至 [金鑰] 索引標籤，然後從 Azure 入口網站複製 [主要金鑰] 值。 

1. 在複製您帳戶的 URI 與主要金鑰後，請在執行應用程式的本機電腦上，將該字串儲存到新的環境變數中。 若要設定環境變數，請開啟命令提示字元視窗，然後執行下列命令。 請務必取代 <Your_Azure_Cosmos_account_URI> 和 <Your_Azure_Cosmos_account_PRIMARY_KEY> 值。

   ```console
   setx Host &quot;<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com&quot;
   setx PrimaryKey &quot;<Your_Azure_Cosmos_account_PRIMARY_KEY>"
   ```

1. 開啟 Program.cs 檔案，並使用上面所建立的資料庫和容器 (也是圖形名稱) 名稱來更新「資料庫」和「容器」變數。

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. 儲存 Program.cs 檔案。 

您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 

## <a name="run-the-console-app"></a>執行主控台應用程式

按 CTRL + F5 來執行應用程式。 此應用程式會在主控台中列印 Gremlin 查詢命令和結果。

   主控台視窗會顯示將要新增至圖形的頂點和邊緣。 當指令碼完成時，請按 ENTER 以關閉主控台視窗。

## <a name="browse-using-the-data-explorer"></a>使用資料總管進行瀏覽

您現在可以回到 Azure 入口網站中的 [資料總管]，瀏覽及查詢新的圖形資料。

1. 在 [資料總管] 中，新的資料庫會出現在 [圖形] 窗格中。 展開資料庫和容器節點，然後按一下 [圖形]。

2. 按一下 [套用篩選條件] 按鈕，以使用預設查詢來檢視圖形中的所有頂點。 範例應用程式所產生的資料會顯示在 [圖形] 窗格中。

    您可以縮放圖形、展開圖形顯示空間、新增其他頂點，並在顯示介面上移動頂點。

    :::image type="content" source="./media/create-graph-dotnet/graph-explorer.png" alt-text="複製端點&quot;:::

   若要執行此範例，請複製 [Gremlin 端點] 值，並刪除結尾處的連接埠號碼，使 URI 變成 `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`。 端點值看起來應該像 `testgraphacct.gremlin.cosmosdb.azure.com`

1. 接下來，瀏覽至 [金鑰] 索引標籤，然後從 Azure 入口網站複製 [主要金鑰] 值。 

1. 在複製您帳戶的 URI 與主要金鑰後，請在執行應用程式的本機電腦上，將該字串儲存到新的環境變數中。 若要設定環境變數，請開啟命令提示字元視窗，然後執行下列命令。 請務必取代 <Your_Azure_Cosmos_account_URI> 和 <Your_Azure_Cosmos_account_PRIMARY_KEY> 值。

   ```console
   setx Host &quot;<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com&quot;
   setx PrimaryKey &quot;<Your_Azure_Cosmos_account_PRIMARY_KEY>":::

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 來建立圖形，以及如何執行應用程式。 您現在可以使用 Gremlin 來建置更複雜的查詢和實作強大的圖形周遊邏輯。 

> [!div class="nextstepaction"]
> [使用 Gremlin 進行查詢](tutorial-query-graph.md)