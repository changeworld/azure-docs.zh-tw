---
title: 快速入門：使用適用於 MongoDB 的 Azure Cosmos DB API 和 Java SDK 建置 Web 應用程式
description: 了解如何建置 Java 程式碼範例，您可使用適用於 MongoDB 的 Azure Cosmos DB API 進行其連線及查詢。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 2dcd72f9231726d5a2f473dbbfa4d8fd81e64b97
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098955"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>快速入門：在 Azure Cosmos DB 中使用 Java 和 MongoDB API 建立主控台應用程式
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

在本快速入門中，您會從 Azure 入口網站建立和管理適用於 Mongo DB API 的 Azure Cosmos DB 帳戶，並使用從 GitHub 複製的 Java SDK 應用程式來新增資料。 Azure Cosmos DB 是多模型的資料庫服務，可讓您快速建立及查詢具有全域散發和水平調整功能的文件、資料表、索引鍵/值及圖形資料庫。

## <a name="prerequisites"></a>Prerequisites
- 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 或[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) (不需 Azure 訂用帳戶)。 您也可以使用 [Azure Cosmos DB 模擬器](https://aka.ms/cosmosdb-emulator)搭配連接字串 `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`。
- [Java 開發套件 (JDK) 第 8 版](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk)。 
- [Maven](https://maven.apache.org/download.cgi). 或執行 `apt-get install maven` 以安裝 Maven。
- [Git](https://git-scm.com/downloads)。 

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>新增集合

將新資料庫命名為 **db** ，以及將新集合命名為 **coll** 。

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-mongodb-create-collection.md)] 

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 GitHub 複製應用程式、設定連接字串，然後加以執行。 您會看到，以程式設計方式來處理資料有多麼的容易。 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. 然後在您慣用的編輯器中開啟程式碼。 

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要瞭解如何在程式碼中建立資料庫資源，則可檢閱下列程式碼片段。 或者也可以直接跳至[更新您的連接字串](#update-your-connection-string)。 

下列程式碼片段全部取自 Program.java  檔案。

此主控台應用程式會使用 [MongoDB Java 驅動程式](https://docs.mongodb.com/ecosystem/drivers/java/)。 

* 已初始化 DocumentClient。

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* 已建立新的資料庫和集合。

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* 已使用 `MongoCollection.insertOne` 插入一些文件

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* 已使用 `MongoCollection.find` 執行一些查詢

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。

1. 從 Azure Cosmos DB 帳戶，選取 [快速入門]  ，選取 [Java]  ，然後將連接字串複製到剪貼簿。

2. 開啟 Program.java  檔案，以連接字串取代 MongoClientURI 建構函式的引數。 您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 
    
## <a name="run-the-console-app"></a>執行主控台應用程式

1. 在終端機中執行 `mvn package` 以安裝必要的 npm 模組

2. 在終端機中執行 `mvn exec:java -D exec.mainClass=GetStarted.Program` 來啟動您的 Java 應用程式。

您現在可以使用 [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) 來查詢、修改及處理這項新資料。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立適用於 Mongo DB API 的 Azure Cosmos DB 帳戶、使用 [資料總管] 建立資料庫和容器，以及使用 Java 主控台應用程式來新增資料。 您現在可以將其他資料匯入到 Cosmos 資料庫。 

> [!div class="nextstepaction"]
> [將 MongoDB 資料匯入到 Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%252fazure%252fcosmos-db%252ftoc.json%253ftoc%253d%252fazure%252fcosmos-db%252ftoc.json)