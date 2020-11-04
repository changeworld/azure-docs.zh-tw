---
title: 使用資料表 API 和 JAVA 建立應用程式 - Azure Cosmos DB
description: 本快速入門示範如何使用 Azure Cosmos DB 資料表 API，以使用 Azure 入口網站與 Java 建立應用程式
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: java
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 48335e577ed248a42914bdaa1b1e662daf2d26cc
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090162"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-table-api-data"></a>快速入門：建置 JAVA 應用程式來管理 Azure Cosmos DB 資料表 API 資料
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
> 

在本快速入門中，您會建立 Azure Cosmos DB 資料表 API 帳戶，並使用從 GitHub 複製的資料總管和 Java 應用程式來建立資料表和實體。 Azure Cosmos DB 是多模型的資料庫服務，可讓您快速建立及查詢具有全域散發和水平調整功能的文件、資料表、索引鍵/值及圖形資料庫。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 或[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) (不需 Azure 訂用帳戶)。 您也可以搭配使用 [Azure Cosmos DB 模擬器](https://aka.ms/cosmosdb-emulator)與 `https://localhost:8081` 的 URI 和金鑰 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`。
- [Java 開發套件 (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk) \(英文\)。 將 `JAVA_HOME` 環境變數指向 JDK 安裝所在的資料夾。
- [Maven 二進位檔封存](https://maven.apache.org/download.cgi)。 
- [Git](https://www.git-scm.com/downloads)。 

## <a name="create-a-database-account"></a>建立資料庫帳戶

> [!IMPORTANT] 
> 您需要建立新的資料表 API 帳戶，以與正式推出的資料表 API SDK 搭配使用。 正式推出的 SDK 不支援在預覽期間建立的資料表 API 帳戶。
>

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
    git clone https://github.com/Azure-Samples/storage-table-java-getting-started.git 
    ```

> [!TIP]
> 如需類似程式碼的詳細逐步解說，請參閱 [Cosmos DB 資料表 API 範例](table-storage-how-to-use-java.md)一文。 

## <a name="review-the-code"></a>檢閱程式碼

此為選用步驟。 若您想要瞭解如何在程式碼中建立資料庫資源，則可檢閱下列程式碼片段。 或是，您可以跳至本文件的[更新您的連接字串](#update-your-connection-string)一節。

* 下列程式碼會示範如何在 Azure 儲存體內建立資料表：

  ```java
  private static CloudTable createTable(CloudTableClient tableClient, String tableName) throws StorageException, RuntimeException, IOException, InvalidKeyException,   IllegalArgumentException, URISyntaxException, IllegalStateException {
  
    // Create a new table
    CloudTable table = tableClient.getTableReference(tableName);
    try {
        if (table.createIfNotExists() == false) {
            throw new IllegalStateException(String.format("Table with name \"%s\" already exists.", tableName));
        }
    }
    catch (StorageException s) {
        if (s.getCause() instanceof java.net.ConnectException) {
            System.out.println("Caught connection exception from the client. If running with the default configuration please make sure you have started the storage emulator.");
        }
        throw s;
    }

    return table;
  }
  ```

* 下列程式碼會示範如何在資料表中插入資料：

  ```javascript
  private static void batchInsertOfCustomerEntities(CloudTable table) throws StorageException {
  
  // Create the batch operation
  TableBatchOperation batchOperation1 = new TableBatchOperation();
  for (int i = 1; i <= 50; i++) {
      CustomerEntity entity = new CustomerEntity("Smith", String.format("%04d", i));
      entity.setEmail(String.format("smith%04d@contoso.com", i));
      entity.setHomePhoneNumber(String.format("425-555-%04d", i));
      entity.setWorkPhoneNumber(String.format("425-556-%04d", i));
      batchOperation1.insertOrMerge(entity);
  }
  
  // Execute the batch operation
  table.execute(batchOperation1);
  }
  ```

* 下列程式碼會示範如何查詢資料表中的資料：

  ```java
  private static void partitionScan(CloudTable table, String partitionKey) throws StorageException {
  
      // Create the partition scan query
      TableQuery<CustomerEntity> partitionScanQuery = TableQuery.from(CustomerEntity.class).where(
          (TableQuery.generateFilterCondition("PartitionKey", QueryComparisons.EQUAL, partitionKey)));
  
      // Iterate through the results
      for (CustomerEntity entity : table.execute(partitionScanQuery)) {
          System.out.println(String.format("\tCustomer: %s,%s\t%s\t%s\t%s", entity.getPartitionKey(), entity.getRowKey(), entity.getEmail(), entity.getHomePhoneNumber(), entity.  getWorkPhoneNumber()));
      }
  }
  ```

* 下列程式碼會示範如何刪除資料表中的資料：

  ```java
  
  System.out.print("\nDelete any tables that were created.");
  
  if (table1 != null && table1.deleteIfExists() == true) {
      System.out.println(String.format("\tSuccessfully deleted the table: %s", table1.getName()));
  }
  
  if (table2 != null && table2.deleteIfExists() == true) {
      System.out.println(String.format("\tSuccessfully deleted the table: %s", table2.getName()));
  }
  ```

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。 這可讓您的應用程式與託管資料庫進行通訊。 

1. 在 [Azure 入口網站](https://portal.azure.com/)中的 Azure Cosmos DB 帳戶中，選取 [連接字串]。 

   :::image type="content" source="./media/create-table-java/cosmos-db-quickstart-connection-string.png" alt-text="在 [連接字串] 窗格中檢視連接字串資訊":::

2. 使用右側的複製按鈕複製 PRIMARY CONNECTION STRING。

3. 從 C:\git-samples\storage-table-java-getting-started\src\main\resources 資料夾中開啟 config.properties。 

5. 註解化第一行，並取消註解第二行。 前兩行現在應顯示如下。

    ```xml
    #StorageConnectionString = UseDevelopmentStorage=true
    StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=[ACCOUNTNAME];AccountKey=[ACCOUNTKEY]
    ```

6. 將 PRIMARY CONNECTION STRING 從入口網站貼到第 2 行上的 StorageConnectionString 值中。 

    > [!IMPORTANT]
    > 如果端點使用 documents.azure.com，這表示您擁有預覽帳戶，因此您需要建立一個[新的資料表 API 帳戶](#create-a-database-account)與正式推出的資料表 API SDK 搭配使用。
    >

7. 儲存 config.properties 檔案。

您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 

## <a name="run-the-app"></a>執行應用程式

1. 在 git 終端機視窗中，`cd` 至 storage-table-java-getting-started 資料夾。

    ```git
    cd "C:\git-samples\storage-table-java-getting-started"
    ```

2. 在 git 終端機視窗中，執行下列命令以執行 Java 應用程式。

    ```git
    mvn compile exec:java 
    ```

    主控台視窗會顯示要新增至 Azure Cosmos DB 中的新資料表資料庫的資料表資料。

    您現在可以返回 [資料總管]，以查看、查詢、修改及使用這項新資料。 

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用資料總管來建立資料表，以及如何執行 Java 應用程式來新增資料表的資料。  現在，您可以使用資料表 API 來查詢您的資料。  

> [!div class="nextstepaction"]
> [將資料表資料匯入至資料表 API](table-import.md)