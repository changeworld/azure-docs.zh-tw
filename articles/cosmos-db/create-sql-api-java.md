---
title: 快速入門 - 使用 Java 以 Azure Cosmos DB 建立文件資料庫
description: 本快速入門提供 Java 程式碼範例，讓您用來連線及查詢 Azure Cosmos DB SQL API
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8c2ae82bae8457a1c715f160994c7a0da94193ff
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134501"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>快速入門：建置 JAVA 應用程式來管理 Azure Cosmos DB SQL API 資料


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

在本快速入門中，您會從 Azure 入口網站以及藉由使用從 GitHub 複製的 Java 應用程式，來建立和管理 Azure Cosmos DB SQL API 帳戶。 首先，您必須使用 Azure 入口網站建立 Azure Cosmos DB SQL API 帳戶、使用 SQL Java SDK 建立 Java 應用程式，然後使用 Java 應用程式將資源新增至您的 Cosmos DB 帳戶。 Azure Cosmos DB 是多模型的資料庫服務，可讓您快速建立及查詢具有全域散發和水平調整功能的文件、資料表、索引鍵/值及圖形資料庫。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 或[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) (不需 Azure 訂用帳戶)。 您也可以搭配使用 [Azure Cosmos DB 模擬器](https://aka.ms/cosmosdb-emulator)與 `https://localhost:8081` 的 URI 和金鑰 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`。
- [Java 開發套件 (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk) \(英文\)。 將 `JAVA_HOME` 環境變數指向 JDK 安裝所在的資料夾。
- [Maven 二進位檔封存](https://maven.apache.org/download.cgi)。 在 Ubuntu 上，執行 `apt-get install maven` 來安裝 Maven。
- [Git](https://www.git-scm.com/downloads)。 在 Ubuntu 上，執行 `sudo apt-get install git` 來安裝 Git。

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

* `CosmosClient` 初始化。 `CosmosClient` 提供適用於 Azure Cosmos 資料庫服務的用戶端邏輯表示法。 此用戶端會用於設定和執行針對服務的要求。
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* 建立 CosmosDatabase。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* 建立 CosmosContainer。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* 使用 `createItem` 方法建立項目。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* 端點讀取是使用 `getItem` 和 `read` 方法來執行

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* 使用 `queryItems` 方法，透過 JSON 執行 SQL 查詢。

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

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

3. 在 git 終端機視窗中，使用下列命令啟動 Java 應用程式 (請將 YOUR_COSMOS_DB_HOSTNAME 取代為入口網站中加上引號的 URI 值，並將 YOUR_COSMOS_DB_MASTER_KEY 取代為入口網站中加上引號的主要金鑰)

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

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
