---
title: 快速入門 - 使用 Spring Data Azure Cosmos DB v3 以 Azure Cosmos DB 建立文件資料庫
description: 本快速入門提供 Spring Data Azure Cosmos DB v3 程式碼範例，讓您用來連線及查詢 Azure Cosmos DB SQL API
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: b0939191a8029ef30f17500bbaaa7eb32b5a6d7e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486543"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>快速入門：建置 Spring Data Azure Cosmos DB v3 應用程式以管理 Azure Cosmos DB SQL API 資料


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

在本快速入門中，您會從 Azure 入口網站以及藉由使用從 GitHub 複製的 Spring Data Azure Cosmos DB v3 應用程式，來建立和管理 Azure Cosmos DB SQL API 帳戶。 首先，您必須使用 Azure 入口網站建立 Azure Cosmos DB SQL API 帳戶，再使用 Spring Data Azure Cosmos DB v3 連接器建立 Spring Boot 應用程式，然後使用 Spring Boot 應用程式將資源新增至您的 Cosmos DB 帳戶。 Azure Cosmos DB 是多模型的資料庫服務，可讓您快速建立及查詢具有全域散發和水平調整功能的文件、資料表、索引鍵/值及圖形資料庫。

> [!IMPORTANT]  
> 這些版本資訊適用於第 3 版的 Spring Data Azure Cosmos DB。 您可以在[這裡](sql-api-sdk-java-spring-v2.md)找到第 2 版的版本資訊。 
>
> Spring Data Azure Cosmos DB 僅支援 SQL API。
>
> 請參閱下列文章，以取得與其他 Azure Cosmos DB API 上的 Spring Data 有關的資訊：
> * [適用於 Apache Cassandra 的 Spring Data 與 Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB 與 Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin 與 Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
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
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要瞭解如何在程式碼中建立資料庫資源，則可檢閱下列程式碼片段。 或是，您可以直接跳到[執行應用程式](#run-the-app)。 

### <a name="application-configuration-file"></a>應用程式組態檔

在此，我們將展示 Spring Boot 和 Spring Data 如何增強使用者經驗 - 建立 Cosmos 用戶端和連線至 Cosmos 資源的程序目前是設定，而非程式碼。 應用程式啟動時，Spring Boot 會使用 **application.properties** 中的設定來處理所有這種未定案的設定：

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

在建立 Azure Cosmos DB 帳戶、資料庫和容器後，只需在組態檔中完成填入，Spring Boot/Spring Data 就會自動執行下列動作：(1) 使用 URI 和金鑰建立基礎 Java SDK `CosmosClient` 執行個體，以及 (2) 連線至資料庫和容器。 您已就緒 - **不再需要資源管理程式碼！**

### <a name="java-source"></a>Java 來源

Spring Data 的附加價值，也來自其簡潔、標準化、可在資料存放區上運作的平台通用介面。 以下的 CRUD 和查詢範例以前述連結的 Spring Data GitHub 範例為基礎，可讓您透過 Spring Data Azure Cosmos DB 操作 Azure Cosmos DB 文件。

* 使用 `save` 方法建立和更新項目。

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* 點讀取使用存放庫中定義的衍生查詢方法。 `findByIdAndLastName` 會執行 `UserRepository` 的點讀取。 方法名稱中說明的欄位，會使 Spring Data 執行 `id` 和 `lastName` 欄位所定義的點讀取：

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* 使用 `deleteAll` 刪除項目：

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* 根據存放庫方法名稱衍生查詢。 Spring Data 會實作 `UserRepository` `findByFirstName` 方法，對 `firstName` 欄位進行 Java SDK SQL 查詢 (此查詢無法實作為讀取點)：

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>執行應用程式

現在，返回 Azure 入口網站以取得連接字串資訊，然後使用端點資訊啟動應用程式。 這可讓您的應用程式與託管資料庫進行通訊。

1. 在 git 終端機視窗中，`cd` 至範例程式碼資料夾。

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. 在 git 終端機視窗中，使用下列命令安裝必要的 Spring Data Azure Cosmos DB 套件。

    ```bash
    mvn clean package
    ```

3. 在 git 終端機視窗中，使用下列命令啟動 Spring Data Azure Cosmos DB 應用程式：

    ```bash
    mvn spring-boot:run
    ```
    
4. 應用程式會載入 **application.properties** ，並連接 Azure Cosmos DB 帳戶中的資源。
5. 應用程式會執行上述的點 CRUD 作業。
6. 應用程式將會執行衍生的查詢。
7. 應用程式不會刪除您的資源。 如果您想要避免產生費用，請切換回入口網站，並從您的帳戶中[清除資源](#clean-up-resources)。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 [資料總管] 來建立 Azure Cosmos DB SQL API 帳戶、文件資料庫和容器，以及如何執行 Spring Data 應用程式來以程式設計方式執行相同的作業。 您現在可以將其他資料匯入 Azure Cosmos DB 帳戶中。 

> [!div class="nextstepaction"]
> [將資料匯入到 Azure Cosmos DB](import-data.md)