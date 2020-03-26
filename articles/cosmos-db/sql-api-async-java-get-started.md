---
title: 教學課程：使用 Azure Cosmos DB Async Java SDK 建置 Java 應用程式，以管理 SQL API 帳戶中的資料
description: 本教學課程說明如何使用非同步 Java 應用程式，在 Azure Cosmos DB 中儲存及存取 SQL API 帳戶內的資料。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 8704e399156b9cfc6b04ff47af49b956b597a539
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75444894"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>教學課程：使用 Async Java SDK 建置 Java 應用程式以管理 SQL API 帳戶中儲存的資料

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [非同步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

身為開發人員，您可能有使用 NoSQL 文件資料的應用程式。 您可以在 Azure Cosmos DB 中使用 SQL API 帳戶來儲存及存取此文件資料。 本教學課程說明如何使用 Async Java SDK 建置 Java 應用程式，以儲存和管理文件資料。 

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 建立及連線至 Azure Cosmos 帳戶
> * 設定您的解決方案
> * 建立集合
> * 建立 JSON 文件
> * 查詢集合

## <a name="prerequisites"></a>Prerequisites

請確定您具有下列資源：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費帳戶](https://azure.microsoft.com/free/)。 

* [Git](https://git-scm.com/downloads)。

* [Java Development Kit (JDK) 8+](https://aka.ms/azure-jdks)。

* [Maven](https://maven.apache.org/download.cgi).

## <a name="create-an-azure-cosmos-db-account"></a>建立 Azure Cosmos DB 帳戶

使用下列步驟來建立 Azure Cosmos 帳戶：

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="clone-the-github-repository"></a><a id="GitClone"></a>複製 GitHub 存放庫

複製[開始使用 Azure Cosmos DB 和 Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) 的 GitHub 存放庫。 例如，請從本機目錄執行下列命令將範例專案擷取到本機。

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

此目錄包含 `pom.xml` 檔案，以及一個含有 Java 原始程式碼 (包括 `Main.java`) 的 `src/main/java/com/microsoft/azure/cosmosdb/sample` 資料夾。 專案中包含使用 Azure Cosmos DB 執行作業 (例如建立文件和查詢集合內的資料) 所需的程式碼。 `pom.xml` 檔案包含 [Maven 上的 Azure Cosmos DB Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) 上的相依性。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a name="connect-to-an-azure-cosmos-account"></a><a id="Connect"></a>連線至 Azure Cosmos 帳戶

接下來，回到 [Azure 入口網站](https://portal.azure.com)以擷取您的端點和主要金鑰。 必須提供 Azure Cosmos DB 端點 URL 和主要金鑰，您的應用程式才能了解所要連線的位置，而 Azure Cosmos DB 才會信任您的應用程式連線。 `AccountSettings.java` 檔案會保存主要金鑰和 URI 值。 

在 Azure 入口網站中，移至 Azure Cosmos 帳戶，然後按一下 [金鑰]  。 從入口網站複製 URI 和「主要金鑰」，並將其貼到 `AccountSettings.java` 檔案中。 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![從入口網站取得金鑰的螢幕擷取畫面][keys]

## <a name="initialize-the-client-object"></a>初始化用戶端物件

使用 "AccountSettings.java" 檔案中定義的主機 URI 和主要金鑰值，來初始化用戶端物件。

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a name="create-a-database"></a><a id="CreateDatabase"></a>建立資料庫

使用 DocumentClient 類別的 `createDatabaseIfNotExists()` 方法來建立 Azure Cosmos 資料庫。 資料庫是分割給多個集合之 JSON 文件儲存體的邏輯容器。

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a name="create-a-collection"></a><a id="CreateColl"></a>建立集合

您可以使用 DocumentClient 類別的 `createDocumentCollectionIfNotExists()` 方法來建立集合。 集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。

> [!WARNING]
> **createCollection** 會建立含有保留輸送量且具有價格含意的新集合。 如需詳細資訊，請造訪[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)。
> 
> 

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a name="create-json-documents"></a><a id="CreateDoc"></a>建立 JSON 文件

使用 DocumentClient 類別的 createDocument 方法來建立文件。 文件會是使用者定義的 (任意) JSON 內容。 現在可插入一或多份文件。 "src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java" 檔案會定義系列 JSON 文件。 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a name="query-azure-cosmos-db-resources"></a><a id="Query"></a>查詢 Azure Cosmos DB 資源

Azure Cosmos DB 支援針對儲存於每個集合的 JSON 文件進行豐富查詢。 下列範例程式碼示範如何使用 SQL 語法搭配 `queryDocuments` 方法來查詢 Azure Cosmos DB 中的文件。

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a name="run-your-java-console-application"></a><a id="Run"></a>執行您的 Java 主控台應用程式

若要從主控台執行應用程式，移至專案資料夾並使用 Maven 進行編譯：

```bash
mvn package
```

執行 `mvn package` 會從 Maven 下載最新的 Azure Cosmos DB 文件庫，並產生 `GetStarted-0.0.1-SNAPSHOT.jar`。 然後執行下列命令來執行應用程式︰

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

您現在已經完成本 NoSQL 教學課程，並擁有運作中的 Java 主控台應用程式。

## <a name="clean-up-resources"></a>清除資源

若不再需要資源，您可以刪除資源群組、Azure Cosmos 帳戶和所有相關資源。 請選取虛擬機器的資源群組，選取 [刪除]  ，然後確認要刪除的資源群組名稱，即可刪除資源。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Async Java SDK 建置 Java 應用程式來管理 Azure Cosmos DB 中的 SQL API 資料。 您現在可以繼續進行下一篇文章：

> [!div class="nextstepaction"]
> [使用 JavaScript SDK 和 Azure Cosmos DB 建置 Node.js 主控台應用程式](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
