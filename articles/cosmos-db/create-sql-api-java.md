---
title: 快速入門 - 使用 Java 以 Azure Cosmos DB 建立文件資料庫
description: 本快速入門提供 Java 程式碼範例，讓您用來連線及查詢 Azure Cosmos DB SQL API
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 4b62b591c408f663fd28d5077af924f785ee66c8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090404"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>快速入門：建置 JAVA 應用程式來管理 Azure Cosmos DB SQL API 資料
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

在本快速入門中，您會從 Azure 入口網站以及藉由使用從 GitHub 複製的 Java 應用程式，來建立和管理 Azure Cosmos DB SQL API 帳戶。 首先，您必須使用 Azure 入口網站建立 Azure Cosmos DB SQL API 帳戶、使用 SQL Java SDK 建立 Java 應用程式，然後使用 Java 應用程式將資源新增至您的 Cosmos DB 帳戶。 Azure Cosmos DB 是多模型的資料庫服務，可讓您快速建立及查詢具有全域散發和水平調整功能的文件、資料表、索引鍵/值及圖形資料庫。

> [!IMPORTANT]  
> 本快速入門僅適用於 Azure Cosmos DB Java SDK v4。 如需詳細資訊，請檢視 Azure Cosmos DB Java SDK v4 [版本資訊](sql-api-sdk-java-v4.md)、[Maven 存放庫](https://mvnrepository.com/artifact/com.azure/azure-cosmos)、Azure Cosmos DB Java SDK v4 [效能秘訣](performance-tips-java-sdk-v4-sql.md)和 Azure Cosmos DB Java SDK v4 [疑難排解指南](troubleshoot-java-sdk-v4-sql.md)。 如果您目前使用的版本比 v4 舊，請參閱[遷移至 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南，以取得升級至 v4 的協助。
>

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 或[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) (不需 Azure 訂用帳戶)。 您也可以搭配使用 [Azure Cosmos DB 模擬器](https://aka.ms/cosmosdb-emulator)與 `https://localhost:8081` 的 URI 和金鑰 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`。
- [Java 開發套件 (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk) \(英文\)。 將 `JAVA_HOME` 環境變數指向 JDK 安裝所在的資料夾。
- [Maven 二進位檔封存](https://maven.apache.org/download.cgi)。 在 Ubuntu 上，執行 `apt-get install maven` 來安裝 Maven。
- [Git](https://www.git-scm.com/downloads)。 在 Ubuntu 上，執行 `sudo apt-get install git` 來安裝 Git。

## <a name="introductory-notes"></a>簡介注意事項

Cosmos DB 帳戶的結構。 不論是 API 還是程式設計語言，Cosmos DB「帳戶」會包含零個以上的「資料庫」、「資料庫」(DB) 會包含零個以上的「容器」，「容器」則會包含零個以上的項目，如下圖所示：

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos 帳戶項目" border="false":::

您可以在[這裡](account-databases-containers-items.md)進一步了解資料庫、容器和項目。 幾個重要的屬性會定義於容器層級，其中包括「佈建的輸送量」和「分割區索引鍵」。 

佈建的輸送量會以具有貨幣價格的要求單位 (RU) 來進行測量，在決定帳戶的營運成本時，RU 會是一大因素。 佈建的輸送量可依每一容器的細微性或每一資料庫的細微性來加以選取，不過一般來說，最好是指定容器層級的輸送量。 您可以在[這裡](set-throughput.md)進一步了解輸送量佈建。

隨著 Cosmos DB 容器中不斷插入項目，資料庫也可藉由新增更多儲存體和計算來處理要求，以水平方式跟著成長。 儲存體和計算容量會以離散單位 (稱為「分割區」) 來新增，而且您必須在文件中選擇一個欄位來作為分割區索引鍵，以將每個文件對應至分割區。 分割區的管理方式是為每個分割區指派分割區索引鍵值範圍中大致相等的配量；因此，建議您選擇相對隨機或平均分佈的分割區索引鍵。 否則，某些分割區會看到高出許多的要求 (「熱分割區」)，其他分割區則會看到少了許多的要求 (「冷分割區」)，我們應該避免這種情況。 您可以在[這裡](partitioning-overview.md)深入了解分割。

## <a name="create-a-database-account"></a>建立資料庫帳戶

您必須先使用 Azure Cosmos DB 建立 SQL API 帳戶，才可以建立文件資料庫。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>新增容器

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>新增範例資料

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>查詢資料

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在讓我們切換為使用程式碼。 我們將從 GitHub 複製 SQL API 應用程式、設定連接字串，然後加以執行。 您會看到，以程式設計方式來處理資料有多麼的容易。 

執行下列命令來複製範例存放庫。 此命令會在您的電腦上建立範例應用程式副本。

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要瞭解如何在程式碼中建立資料庫資源，則可檢閱下列程式碼片段。 或是，您可以直接跳到[執行應用程式](#run-the-app)。 


# <a name="sync-api"></a>[同步 API](#tab/sync)

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>使用同步 API 管理資料庫資源

* `CosmosClient` 初始化。 `CosmosClient` 提供適用於 Azure Cosmos 資料庫服務的用戶端邏輯表示法。 此用戶端會用於設定和執行針對服務的要求。
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase` 建立。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer` 建立。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* 使用 `createItem` 方法建立項目。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* 端點讀取是使用 `readItem` 方法來執行。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* 使用 `queryItems` 方法，透過 JSON 執行 SQL 查詢。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

# <a name="async-api"></a>[非同步 API](#tab/async)

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>使用非同步 API 管理資料庫資源

* 非同步 API 呼叫會立即傳回，而不需等待伺服器的回應。 有鑑於此，下列程式碼片段會顯示適當的設計模式，以便使用非同步 API 來完成上述所有管理工作。

* `CosmosAsyncClient` 初始化。 `CosmosAsyncClient` 提供適用於 Azure Cosmos 資料庫服務的用戶端邏輯表示法。 此用戶端用於設定和執行針對服務的非同步要求。
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase` 建立。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer` 建立。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* 就像同步 API 一樣，項目建立是使用 `createItem` 方法來完成。 此範例示範如何藉由訂閱可發出要求並列印通知的「回應式串流」，有效率地發出許多非同步 `createItem` 要求。 由於這個簡單範例會執行到完成並終止，因此 `CountDownLatch` 執行個體用來確保程式在項目建立期間不會終止。 **適當的非同步程式設計做法不會在非同步呼叫上進行封鎖 - 在實際的使用案例中，要求會產生自無限期執行的 main() 迴圈，而不需要在非同步呼叫上進行閂鎖。**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* 就像同步 API 一樣，點讀取是使用 `readItem` 方法來執行。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* 就像同步 API 一樣，透過 JSON 的 SQL 查詢是使用 `queryItems` 方法來執行。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

---

## <a name="run-the-app"></a>執行應用程式

現在，返回 Azure 入口網站以取得連接字串資訊，然後使用端點資訊啟動應用程式。 這可讓您的應用程式與託管資料庫進行通訊。

1. 在 git 終端機視窗中，`cd` 至範例程式碼資料夾。

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. 在 git 終端機視窗中，使用下列命令來安裝必要的 Java 套件。

    ```bash
    mvn package
    ```

3. 在 git 終端機視窗中，使用下列命令啟動 Java 應用程式 (將 SYNCASYNCMODE 取代為 `sync` 或 `async` (取決於您想要執行的範例程式碼)，將 YOUR_COSMOS_DB_HOSTNAME 取代為入口網站中加上引號的 URI 值，並將 YOUR_COSMOS_DB_MASTER_KEY 取代為入口網站中加上引號的主要金鑰)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    終端機視窗會顯示已建立 FamilyDB 資料庫的通知。 
    
4. 此應用程式會建立名稱為 `AzureSampleFamilyDB` 的資料庫
5. 此應用程式會建立名稱為 `FamilyContainer` 的容器
6. 此應用程式會使用物件識別碼和分割索引鍵值 (在我們的範例中為 lastName) 來執行端點讀取。 
7. 此應用程式會查詢項目，以擷取姓氏屬於 ('Andersen', 'Wakefield', 'Johnson') 的所有家族

7. 應用程式並不會刪除已建立的資源。 切換回入口網站，從您的帳戶中[清除資源](#clean-up-resources)，  以免產生費用。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 [資料總管] 來建立 Azure Cosmos DB SQL API 帳戶、文件資料庫和容器，以及如何執行 Java 應用程式來以程式設計方式執行同樣的作業。 您現在可以將其他資料匯入 Azure Cosmos DB 帳戶中。 

> [!div class="nextstepaction"]
> [將資料匯入到 Azure Cosmos DB](import-data.md)
