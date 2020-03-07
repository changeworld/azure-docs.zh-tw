---
title: Azure Cosmos DB：適用於 SQL API 的 Java 範例
description: 在 GitHub 上尋找適合使用 Azure Cosmos DB SQL API 執行之一般工作 (包括 CRUD 作業) 的 Java 範例。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: sample
ms.date: 02/08/2019
ms.author: sngun
ms.openlocfilehash: 8b133f0044bdf8f99fdee657177d561ef5bb406b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364437"
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB：適用於 SQL API 的 Java 範例

> [!div class="op_single_selector"]
> * [.NET V2 SDK 範例](sql-api-dotnet-samples.md)
> * [.NET V3 SDK 範例](sql-api-dotnet-v3sdk-samples.md)
> * [Java 範例](sql-api-java-samples.md)
> * [非同步 Java 範例](sql-api-async-java-samples.md)
> * [Node.js 範例](sql-api-nodejs-samples.md)
> * [Python 範例](sql-api-python-samples.md)
> * [Azure 程式碼範例庫](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

[azure-documentdb-java](https://github.com/Azure/azure-documentdb-java) GitHub 存放庫中包含可對 Azure Cosmos DB 資源執行 CRUD 作業和其他常見作業的最新範例應用程式。 本文提供：

* 每個範例 Java 專案檔中各項工作的連結。 
* 相關 API 參考內容的連結。

**必要條件**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- 您可以 [啟用 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)：您的 Visual Studio 訂用帳戶每個月都會提供額度，供您用在 Azure 付費服務。

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

執行此範例應用程式需要下列項目：

* Java Development Kit 7
* Microsoft Azure DocumentDB Java SDK

您可選擇使用 Maven 來取得最新的 Microsoft Azure DocumentDB Java SDK 二進位檔，以使用於您的專案中。 Maven 會自動加入任何必要的相依性。 否則，您可以直接下載 pom.xml 檔案中列出的相依性，並將它們加入至您的組建路徑。

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**執行範例應用程式**

複製範例存放庫：
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

您可以使用 Eclipse 或使用 Maven 從命令列執行這些範例。

從 Eclipse 執行：
* 在 Eclipse 載入主要父專案 pom.xml 檔案；它應該會自動載入 documentdb-examples。
* 若要執行這些範例，您需要有效的 Azure Cosmos DB 端點。 端點是從 `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java` 讀取。
* 您可以在 Eclipse JUnit 執行組態中將端點認證做為 VM 引數傳遞，或將端點認證置於 AccountCredentials.java 中。
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* 現在您可以在 Eclipse 中執行這些範例做為 JUnit 測試。

從命令列執行：
* 另一個執行範例的方式是使用 Maven：
* 執行 Maven 並傳遞 Azure Cosmos DB 端點認證：
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > 每個範例都各自獨立，會自己設定，並於完成後自行清理。 這些範例會對 [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) 發出多個呼叫。 每當執行此動作時，即會根據所建立集合的效能層，對您的訂用帳戶計入 1 小時的使用費。 
   > 
   > 

## <a name="database-examples"></a>資料庫範例
[DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) 檔案示範如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 資料庫，請參閱[使用資料庫、容器和項目](databases-containers-items.md)概念性文章。 

| Task | API 參考資料 |
| --- | --- |
| [建立和讀取資料庫](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |
| [建立和刪除資料庫](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedatabase) |
| [建立和查詢資料庫](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydatabases) |

## <a name="collection-examples"></a>集合範例
[CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) 檔案示範如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 集合，請參閱[使用資料庫、容器和項目](databases-containers-items.md)概念性文章。

| Task | API 參考資料 |
| --- | --- |
| [建立單一分割區集合](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) \(英文\) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [建立自訂多重分割區集合](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) \(英文\) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions) |
| [刪除集合](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletecollection) |

## <a name="document-examples"></a>文件範例
[DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) 檔案示範如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 文件，請參閱[使用資料庫、容器和項目](databases-containers-items.md)概念性文章。

| Task | API 參考資料 |
| --- | --- |
| [建立、讀取和刪除文件](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) \(英文\) | [DocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument) |
| [建立含可程式化文件定義的文件](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) \(英文\) | [文件](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |

## <a name="indexing-examples"></a>索引範例
[CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) 檔案示範如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的索引功能，請參閱[索引原則](index-policy.md)、[索引類型](index-types.md)及[索引路徑](index-paths.md)概念性文章。 

| Task | API 參考資料 |
| --- | --- |
| [建立索引並設定編制索引原則](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) \(英文\) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy) |

如需索引編製的詳細資訊，請參閱 [Azure Cosmos DB 編製索引原則](index-policy.md)。

## <a name="query-examples"></a>查詢範例
[DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) 檔案示範如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的 SQL 查詢參考，請參閱 [SQL 查詢範例](how-to-sql-query.md)概念性文章。 


| Task | API 參考資料 |
| --- | --- |
| [執行簡單的跨分割區文件查詢](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) \(英文\) | [DocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setenablecrosspartitionquery) |
| [依查詢排序](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) \(英文\) | [FeedResponse\<T>.getQueryIterator](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse.getqueryiterator) |

如需撰寫查詢的詳細資訊，請參閱 [在 Azure Cosmos DB 中執行 SQL 查詢](how-to-sql-query.md)。

## <a name="offer-examples"></a>供應項目範例
[OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) 檔案示範如何執行下列工作：

| Task | API 參考資料 |
| --- | --- |
| [建立集合並設定輸送量](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) \(英文\) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions.setofferthroughput) |
| [讀取集合以尋找相關聯的供應項目](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) \(英文\) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.queryoffers) |

## <a name="partition-key-examples"></a>分割區索引鍵範例
[SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) 檔案示範如何執行下列工作。 若要在執行下列範例之前了解 Azure Cosmos DB 中的分割與分割區索引鍵，請參閱[分割](partitioning-overview.md)概念性文章。 


| Task | API 參考資料 |
| --- | --- |
| [建立單一分割區集合](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) \(英文\) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [變更單一分割區集合的輸送量供應項目](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) \(英文\) | [DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>預存程序範例
[StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) 檔案示範如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的伺服器端程式設計，請參閱[預存程序、觸發程序和使用者定義函式](stored-procedures-triggers-udfs.md)概念性文章。 


| Task | API 參考資料 |
| --- | --- |
| [建立預存程序](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createstoredprocedure) |
| [執行含引數的預存程序](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) \(英文\) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
| [執行含物件引數的預存程序](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) \(英文\) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
