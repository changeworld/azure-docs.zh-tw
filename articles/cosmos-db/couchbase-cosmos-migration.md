---
title: 從 CouchBase 遷移到 Azure 宇宙資料庫 SQL API
description: 從 CouchBase 遷移到 Azure Cosmos DB SQL API 的分步指南
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210940"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>從 CouchBase 遷移到 Azure 宇宙資料庫 SQL API

Azure Cosmos DB 是一個可擴展的、全域分佈的完全託管資料庫。 它提供了對資料的保證低延遲訪問。 要瞭解有關 Azure Cosmos DB 的詳細資訊，請參閱[概述](introduction.md)文章。 本文提供了將連接到 Couchbase 的 JAVA 應用程式遷移到 Azure Cosmos DB 中的 SQL API 帳戶的說明。

## <a name="differences-in-nomenclature"></a>命名學的差異

與 Couchbase 相比，以下是 Azure Cosmos DB 中工作方式不同的關鍵功能：

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|沙發基礎伺服器| 帳戶       |
|桶           | 資料庫      |
|桶           | 容器/集合 |
|JSON 文檔    | 專案/文檔 |

## <a name="key-differences"></a>主要差異

* Azure Cosmos DB 在文檔中有一個"ID"欄位，而 Couchbase 的 ID 是存儲桶的一部分。 "ID"欄位在整個分區中是唯一的。

* Azure Cosmos DB 使用分區或分片技術進行縮放。 這意味著它將資料拆分為多個分片/分區。 這些分區/分片是根據您提供的分區鍵屬性創建的。 您可以選擇分區鍵來優化讀取以及寫入操作或讀取/寫入優化。 要瞭解更多資訊，請參閱[分區](./partition-data.md)文章。

* 在 Azure Cosmos DB 中，頂級層次結構不需要指示集合，因為集合名稱已存在。 此功能使 JSON 結構更簡單。 下面是一個示例，該示例顯示了 Couchbase 和 Azure Cosmos DB 之間的資料模型差異：

   **沙發底座**： 文件識別碼 = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure 宇宙 DB**： 請參閱文檔中的"ID"，如下所示

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>JAVA SDK 支援

Azure Cosmos DB 具有以下 SDK 來支援不同的 JAVA 框架：

* 非同步 SDK
* 彈簧啟動 SDK

以下各節介紹何時使用這些 SDK。 考慮一個示例，其中我們有三種類型的工作負載：

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>作為文檔存儲庫的 Couchbase &基於資料的自訂查詢

如果要遷移的工作負載基於基於 Spring Boot 的 SDK，則可以使用以下步驟：

1. 將父級添加到 POM.xml 檔：

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. 將屬性添加到 POM.xml 檔：

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. 向 POM.xml 檔添加依賴項：

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. 在資源下添加應用程式屬性，並指定以下內容。 請確保替換 URL、鍵和資料庫名稱參數：

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. 在模型中定義集合的名稱。 您還可以指定進一步注釋。 例如，ID，分區鍵以顯式表示它們：

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

以下是 CRUD 操作的程式碼片段：

### <a name="insert-and-update-operations"></a>插入和更新操作

*其中_repo*是存儲庫的物件，*文檔*是 POJO 類的物件。 您可以使用`.save`插入或向上設置（如果找到具有指定 ID 的文檔）。 以下程式碼片段演示如何插入或更新文檔物件：

```_repo.save(doc);```

### <a name="delete-operation"></a>刪除作業

請考慮以下程式碼片段，其中文檔物件將具有標識和分區金鑰，必須查找和刪除該物件：

```_repo.delete(doc);```

### <a name="read-operation"></a>讀取作業

您可以使用或不指定分區鍵來讀取文檔。 如果不指定分區鍵，則將其視為跨分區查詢。 請考慮以下代碼示例，首先將使用 ID 和分區鍵欄位執行操作。 第二個示例使用常規欄位&而不指定分區鍵欄位。

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

這就是它，您現在可以將應用程式與 Azure Cosmos DB 一起使用。 此文檔中描述的示例的完整代碼示例可在[CouchbasetoCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub 存儲庫中提供。

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>使用 N1QL 查詢&作為文檔存儲庫的 Couchbase

N1QL 查詢是在 Couchbase 中定義查詢的方法。

|N1QL 查詢 | Azure 宇宙DB 查詢|
|-------------------|-------------------|
|SELECT`TravelDocument`META（ .id `TravelDocument`AS ID， .* 從`TravelDocument`WHERE `_type` @ "com.xx.xx.xx.xxx.xxx" 和國家 = '印度' 和任何 m 在 簽證 滿足 m.type = '多入口'` Validity`和 m.國家在 ['印度'， 不丹 ' _ ORDER BY DESC LIMIT 25   | 選擇 c.id，c 從 c JOIN m 在 c.國家_'印度' where c._type = "com.xx.xx.xx.xxx.xxx"和 c.國家 = '印度' 和 m.type = '多條目'和 m.國家 IN （'印度'， '不丹' ORDER BY c.有效性 DESC OFFSET 0 LIMIT 25 |

您可以在 N1QL 查詢中注意到以下更改：

* 您無需使用 META 關鍵字或引用第一級文檔。 相反，您可以創建自己對容器的引用。 在此示例中，我們將其視為"c"（可以是任何內容）。 此引用用作所有第一級欄位的首碼。 Fr 示例，c.id，c.國家等。

* 現在，您可以在子文檔中進行聯接，並引用專用別名（如"m"）而不是"ANY"。 為子文檔創建別名後，需要使用別名。 例如，m.國家/

* 在 Azure Cosmos DB 查詢中，OFFSET 的順序不同，首先需要指定 OFFSET，然後再指定"限制"。 如果使用最大自訂定義的查詢，建議不要使用 Spring Data SDK，因為它在將查詢傳遞到 Azure Cosmos DB 時在用戶端可能有不必要的開銷。 相反，我們有一個直接的AsyncJAVA SDK，在這種情況下可以非常有效地使用它。

### <a name="read-operation"></a>讀取操作

將 Async JAVA SDK 用於以下步驟：

1. 將以下依賴項配置到 POM.xml 檔中：

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. 使用`ConnectionBuilder`如下示例所示的方法為 Azure Cosmos DB 創建連線物件。 請確保將此聲明放入 Bean 中，以便以下代碼只能執行一次：

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. 要執行查詢，您需要運行以下程式碼片段：

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

現在，在上述方法的説明下，您可以傳遞多個查詢並沒有任何麻煩地執行。 如果需要執行一個大型查詢（可以拆分為多個查詢，請嘗試以下程式碼片段而不是前一個查詢程式碼片段）：

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

使用前面的代碼，您可以並行執行查詢，並增加分散式執行以進行優化。 此外，您還可以運行插入和更新操作：

### <a name="insert-operation"></a>插入動作

要插入文檔，請運行以下代碼：

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

然後訂閱單聲道作為：

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>Upsert 操作

Upsert 操作要求您指定需要更新的文檔。 要獲取完整文檔，可以使用標題讀取操作下提及的程式碼片段，然後修改所需的欄位。 以下程式碼片段使文檔向上：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然後訂閱單聲道。 請參閱插入操作中的單聲道訂閱程式碼片段。

### <a name="delete-operation"></a>刪除動作

以下程式碼片段將執行刪除操作：

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然後訂閱單聲道，在插入操作中引用單聲道訂閱程式碼片段。 完整的代碼示例在[CouchbasetoCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub 存儲庫中提供。

## <a name="couchbase-as-a-keyvalue-pair"></a>沙發底座作為鍵/值對

這是一種簡單的工作負載類型，您可以在其中執行查找而不是查詢。 對鍵/值對使用以下步驟：

1. 請考慮將"/ID"作為主鍵，這將確保可以直接在特定分區中執行查找操作。 創建集合並將"/ID"指定為分區鍵。

1. 完全關閉索引。 由於您將執行查找操作，因此沒有進行索引開銷的點。 要關閉索引，請登錄到 Azure 門戶，轉到 Azure Cosmos DB 帳戶。 打開**資料資源管理器**，選擇**您的資料庫**和**容器**。 打開 **"縮放&設置"** 選項卡並選擇**索引策略**。 當前索引策略如下所示：
    
   ```json
   {
       "indexingMode": "consistent",
       "includedPaths": 
       [
           {
            "path": "/*",
            "indexes": 
             [
                {
                  "kind": "Range",
                  "dataType": "Number"
                },
                {
                  "kind": "Range",
                  "dataType": "String"
                },
                {
                   "kind": "Spatial",
                   "dataType": "Point"
                }
             ]
          }
       ],
       "excludedPaths": 
       [
         {
             "path": "/path/to/single/excluded/property/?"
         },
         {
             "path": "/path/to/root/of/multiple/excluded/properties/*"
         }
      ]
   }
   ````

   將上述索引策略替換為以下策略：

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. 使用以下程式碼片段創建連線物件。 連線物件（放置在其中@Bean或使其為靜態物件）：

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

現在，您可以執行 CRUD 操作，如下所示：

### <a name="read-operation"></a>讀取操作

要閱讀該專案，請使用以下程式碼片段：

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>插入動作

要插入專案，可以執行以下代碼：

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

然後訂閱單聲道作為：

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert 操作

要更新項的值，請參閱下面的程式碼片段：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然後訂閱單聲道，在插入操作中引用單聲道訂閱程式碼片段。

### <a name="delete-operation"></a>刪除動作

使用以下程式碼片段執行刪除操作：

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然後訂閱單聲道，在插入操作中引用單聲道訂閱程式碼片段。 完整的代碼示例在[CouchbasetoCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub 存儲庫中提供。

## <a name="data-migration"></a>資料移轉

有兩種方法可以遷移資料。

* **使用 Azure 資料工廠：** 這是最推薦的方法來遷移資料。 將源配置為 Couchbase 並將接收器配置為 Azure Cosmos DB SQL API，有關詳細步驟，請參閱 Azure [Cosmos DB 資料工廠連接器](../data-factory/connector-azure-cosmos-db.md)一文。

* **使用 Azure 宇宙資料庫資料導入工具：** 建議使用資料量較少的 VM 遷移此選項。 有關詳細步驟，請參閱[資料導入器](./import-data.md)一文。

## <a name="next-steps"></a>後續步驟

* 要執行效能測試，請參閱[使用 Azure Cosmos DB 一文進行性能和縮放測試](./performance-testing.md)。
* 要優化代碼，請參閱[Azure Cosmos DB](./performance-tips-async-java.md)一文的性能提示。
* 探索 JAVA 同步 V3 [SDK，SDK 參考](https://github.com/Azure/azure-cosmosdb-java/tree/v3)GitHub 存儲庫。
