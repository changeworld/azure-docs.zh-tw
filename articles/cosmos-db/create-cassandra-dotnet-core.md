---
title: 快速入門：Cassandra API 和 .NET Core - Azure Cosmos DB
description: 本快速入門示範如何使用 Azure Cosmos DB Cassandra API，以 Azure 入口網站和 .NET Core 建立設定檔應用程式
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
author: TheovanKraay
ms.author: thvankra
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: 46826319cdd2ba55d469704a09656b61c96ce798
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743132"
---
# <a name="quickstart-build-a-cassandra-app-with-net-core-and-azure-cosmos-db"></a>快速入門：使用 .NET Core 和 Azure Cosmos DB 建置 Cassandra 應用程式

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

本快速入門示範如何使用 .NET Core 和 Azure Cosmos DB [Cassandra API](cassandra-introduction.md)，從 GitHub 複製範例來建置設定檔應用程式。 此快速入門也會示範如何使用 Web 型 Azure 入口網站來建立 Azure Cosmos DB 帳戶。

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、資料表、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

## <a name="prerequisites"></a>必要條件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]或者，您可以[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，無須 Azure 訂用帳戶，也無須任何費用和約定付款。

此外，您需要： 
* 如果尚未安裝 Visual Studio 2019，您可以下載並使用**免費**的 [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。
* 安裝 [Git](https://www.git-scm.com/) 來複製範例。

<a id="create-account"></a>
## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]


## <a name="clone-the-sample-application"></a>複製範例應用程式

現在讓我們切換為使用程式碼。 讓我們從 GitHub 複製 Cassandra API 應用程式、設定連接字串，然後加以執行。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟命令提示字元。 建立名為 `git-samples` 的新資料夾。 接著，關閉命令提示字元。

    ```bash
    md "C:\git-samples"
    ```

2. 開啟 git 終端機視窗 (例如 git bash)，並使用 `cd` 命令變更至要安裝範例應用程式的新資料夾。

    ```bash
    cd "C:\git-samples"
    ```

3. 執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-dotnet-core-getting-started.git
    ```

4. 接下來，在 Visual Studio 中開啟 CassandraQuickStartSample 方案檔。 

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 如果您有興趣了解程式碼如何建立資料庫資源，您可以檢閱下列程式碼片段。 這些片段全都取自 `Program.cs` 檔案的 `async Task ProcessAsync()` 方法，該檔案安裝於 `C:\git-samples\azure-cosmos-db-cassandra-dotnet-core-getting-started\CassandraQuickStart` 資料夾。 或者也可以直接跳至[更新您的連接字串](#update-your-connection-string)。

* 連線到 Cassandra 叢集端點以初始化工作階段。 Azure Cosmos DB 上的 Cassandra API 僅支援 TLSv1.2。 

  ```csharp
      var options = new Cassandra.SSLOptions(SslProtocols.Tls12, true, ValidateServerCertificate);
      options.SetHostNameResolver((ipAddress) => CASSANDRACONTACTPOINT);
      Cluster cluster = Cluster
          .Builder()
          .WithCredentials(USERNAME, PASSWORD)
          .WithPort(CASSANDRAPORT)
          .AddContactPoint(CASSANDRACONTACTPOINT)
          .WithSSL(options)
          .Build()
      ;
      ISession session = await cluster.ConnectAsync();
   ```

* 如果已存在現有的 keyspace，則將其卸除。

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("DROP KEYSPACE IF EXISTS uprofile")); 
    ```

* 建立新的 keyspace。

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("CREATE KEYSPACE uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 };"));
    ```

* 建立新的資料表。

   ```csharp
  await session.ExecuteAsync(new SimpleStatement("CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"));
   ```

* 使用 IMapper 物件搭配連線到 uprofile keyspace 的新工作階段來插入使用者實體。

    ```csharp
    await mapper.InsertAsync<User>(new User(1, "LyubovK", "Dubai"));
    ```

* 取得所有使用者資訊的查詢。

    ```csharp
    foreach (User user in await mapper.FetchAsync<User>("Select * from user"))
    {
        Console.WriteLine(user);
    }
    ```

* 取得單一使用者資訊的查詢。

    ```csharp
    mapper.FirstOrDefault<User>("Select * from user where user_id = ?", 3);
    ```

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。 連接字串資訊可讓您的應用程式與託管資料庫進行通訊。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取 [連接字串]。

1. 使用位於螢幕右側的 :::image type="icon" source="./media/create-cassandra-dotnet/copy.png"::: 按鈕來複製 [使用者名稱] 值。

   :::image type="content" source="./media/create-cassandra-dotnet/keys.png" alt-text="在 Azure 入口網站 [連接字串] 頁面中檢視及複製存取金鑰":::

1. 在 Visual Studio 中，開啟 Program.cs 檔案。 

1. 從入口網站將 [使用者名稱] 值貼到 `<PROVIDE>` 的行 13。

    Program.cs 的行 13 現應如下所示 

    `private const string UserName = "cosmos-db-quickstart";`

    您也可以針對接觸點的值，在第 15 行的 `<PROVIDE>` 貼上相同的值：

    `private const string CassandraContactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com"; //  DnsName`

1. 返回入口網站，並複製 [密碼] 值。 從入口網站將 [密碼] 值貼到 `<PROVIDE>` 的行 14。

    Program.cs 的行 14 現應如下所示 

    `private const string Password = "2Ggkr662ifxz2Mg...==";`

1. 返回入口網站，並複製 [連絡點] 值。 從入口網站將 [連絡點] 值貼到第 16 行上的 `<PROVIDE>`。

    Program.cs 的第 16 行現應如下所示 

    `private const string CASSANDRACONTACTPOINT = "quickstart-cassandra-api.cassandra.cosmos.azure.com";`

1. 儲存 Program.cs 檔案。
    
## <a name="run-the-net-core-app"></a>執行 .NET Core 應用程式

1. 在 Visual Studio 中，選取 [工具] >  [NuGet 套件管理員] >  [套件管理員主控台]。

2. 在命令提示字元中，使用下列命令來安裝 .NET 驅動程式的 NuGet 套件。 

    ```cmd
    Install-Package CassandraCSharpDriver
    ```
3. 按 CTRL + F5 來執行應用程式。 您的應用程式會顯示在主控台視窗中。 

    :::image type="content" source="./media/create-cassandra-dotnet/output.png" alt-text="在 Azure 入口網站 [連接字串] 頁面中檢視及複製存取金鑰":::

    按 CTRL + C 來停止執行程式，並關閉主控台視窗。 
    
4. 在 Azure 入口網站中，開啟 [資料總管] 以查詢、修改及使用這個新資料。

    :::image type="content" source="./media/create-cassandra-dotnet/data-explorer.png" alt-text="在 Azure 入口網站 [連接字串] 頁面中檢視及複製存取金鑰":::

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 建立容器，以及如何執行 Web 應用程式。 您現在可以將其他資料匯入到 Cosmos DB 帳戶。 

> [!div class="nextstepaction"]
> [將 Cassandra 資料匯入到 Azure Cosmos DB](cassandra-import-data.md)
