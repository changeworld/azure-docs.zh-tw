---
title: 從 CouchBase 遷移至 Azure Cosmos DB SQL API
description: 從 CouchBase 遷移至 Azure Cosmos DB SQL API 的逐步指引
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.custom: devx-track-java
ms.openlocfilehash: e84b80233d87ac4ae5e2281b506e225c4ab1bd9d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357597"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>從 CouchBase 遷移至 Azure Cosmos DB SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 是可調整、全域散發的完全受控資料庫。 其可為您的資料提供保證低度延遲的存取。 若要深入了解 Azure Cosmos DB，請參閱[概觀](introduction.md)一文。 本文提供指示，說明如何將連線至 Couchbase 的 JAVA 應用程式遷移至 Azure Cosmos DB 中的 SQL API 帳戶。

## <a name="differences-in-nomenclature"></a>命名法的差異

以下是與 Couchbase 相較之下，在 Azure Cosmos DB 中以不同方式運作的主要功能：

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Couchbase 伺服器| 帳戶       |
|貯體           | 資料庫      |
|貯體           | 容器/集合 |
|JSON 文件    | 項目/文件 |

## <a name="key-differences"></a>主要差異

* Azure Cosmos DB 在文件中具有 [識別碼] 欄位，而 Couchbase 具有識別碼作為貯體的一部分。 [識別碼] 欄位在分割之間是唯一的。

* Azure Cosmos DB 使用分割或分區化技術進行調整。 這表示其會將資料分割成多個分區/分割。 系統會根據您提供的分割索引鍵屬性來建立這些分割/分區。 您也可以選取分割索引鍵，以最佳化讀取和寫入作業或讀取/寫入作業。 若要深入了解，請參閱[分割](./partitioning-overview.md)一文。

* 在 Azure Cosmos DB 中，最上層階層不需要表示集合，因為集合名稱已經存在。 這項功能可讓 JSON 結構變得更簡單。 以下範例會顯示 Couchbase 與 Azure Cosmos DB 之間資料模型的差異：

   **Couchbase**：Document ID =  "99FF4444"

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

   **Azure Cosmos DB**：請參閱文件中的「識別碼」，如下所示

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

Azure Cosmos DB 具有下列 SDK 以支援不同的 JAVA 架構：

* 非同步 SDK
* Spring Boot SDK

下列幾個章節說明這些 SDK 的使用時機。 請考量有三種工作負載類型的範例：

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase 作為文件存放庫和 Spring Data 型自訂查詢

如果您要遷移的工作負載是以 Spring Boot 型 SDK 為基礎，您可以使用下列步驟：

1. 將父代新增至 POM.xml 檔案：

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. 將屬性新增至 POM.xml 檔案：

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. 將相依性新增至 POM.xml 檔案：

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. 在資源下新增應用程式屬性，並指定下列項目。 請務必取代 URL、索引鍵和資料庫名稱參數：

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. 定義模型中的集合名稱。 您也可以指定進一步的註釋。 例如，識別碼、分割索引鍵，以明確表示：

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

以下是 CRUD 作業的程式碼片段：

### <a name="insert-and-update-operations"></a>插入和更新作業

其中 _repo 是存放庫的物件，doc 是 POJO 類別的物件。 您可以使用 `.save` 來插入或 upsert (如果找到具有指定識別碼的文件)。 下列程式碼片段示範如何插入或更新 doc 物件：

```_repo.save(doc);```

### <a name="delete-operation"></a>刪除作業

請考量下列程式碼片段，其中 doc 物件將會有必要的識別碼和分割索引鍵，以找出物件並加以刪除：

```_repo.delete(doc);```

### <a name="read-operation"></a>讀取作業

您可以在指定或不指定分割索引鍵的情況下，讀取文件。 如果您未指定分割索引鍵，則會將其視為跨分割查詢。 請考量下列程式碼範例，第一個範例會使用識別碼和分割索引鍵欄位來執行作業。 第二個範例會使用一般欄位且未指定分割索引鍵欄位。

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

也就是說，您現在可以搭配 Azure Cosmos DB 使用您的應用程式。 本文件中所述範例的完整程式碼範例，可以在 [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/SpringCosmos) GitHub 存放庫中取得。

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase 作為文件存放庫與使用 N1QL 查詢

N1QL 查詢是在 Couchbase 中定義查詢的方式。

|N1QL 查詢 | Azure CosmosDB 查詢|
|-------------------|-------------------|
|SELECT META(`TravelDocument`).id AS id, `TravelDocument`.* FROM `TravelDocument` WHERE `_type` = "com.xx.xx.xx.xxx.xxx.xxxx " and country = 'India’ and ANY m in Visas SATISFIES m.type == 'Multi-Entry' and m.Country IN ['India', Bhutan’] ORDER BY ` Validity` DESC LIMIT 25 OFFSET 0   | SELECT c.id,c FROM c JOIN m in  c.country=’India’ WHERE c._type = " com.xx.xx.xx.xxx.xxx.xxxx" and c.country = 'India' and m.type = 'Multi-Entry' and m.Country IN ('India', 'Bhutan') ORDER BY c.Validity DESC OFFSET 0 LIMIT 25 |

您可以注意到 N1QL 查詢中的下列變更：

* 您不需要使用中繼關鍵字或參閱第一層文件。 相反地，您可以建立自己的容器參考。 在此範例中，我們將其視為 "c" (可以是任何項目)。 此參考會當做所有第一層欄位的前置詞使用。 例如，c.id、c.country 等等。

* 並非 "ANY"，現在您可以在子文件上執行聯結，並且以專用別名 (例如 "m") 加以參考。 建立子文件的別名之後，您必須使用別名。 例如，m.Country。

* OFFSET 的順序在 Azure Cosmos DB 查詢中不同，首先您必須指定 OFFSET 然後指定 LIMIT。 如果您使用的是自訂定義最大查詢，建議您不要使用 Spring Data SDK，因為在將查詢傳遞給 Azure Cosmos DB 時，在用戶端可能會有不必要的額外負荷。 相反地，我們有直接的非同步 JAVA SDK，在此情況下可以更有效率地使用。

### <a name="read-operation"></a>讀取作業

使用非同步 JAVA SDK，並執行下列步驟：

1. 在 POM.xml 檔案上設定下列相依性：

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. 使用 `ConnectionBuilder` 方法來建立 Azure Cosmos DB 的連線物件，如下列範例所示。 請確定您將此宣告放入 Bean 中，讓下列程式碼只會執行一次：

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(PrimaryKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. 若要執行查詢，您需要執行下列程式碼片段：

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

現在，透過上述方法的協助，您可以傳遞多個查詢並執行，一點也不麻煩。 如果您需要執行一個可以分割成多個查詢的大型查詢，請嘗試下列程式碼片段，而不是前一個程式碼片段：

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

使用先前的程式碼，您可以平行執行查詢，並增加分散式執行以便最佳化。 此外，您也可以執行插入和更新作業：

### <a name="insert-operation"></a>插入動作

若要插入文件，請執行下列程式碼：

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

然後訂閱 Mono，如下所示：

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

### <a name="upsert-operation"></a>Upsert 作業

Upsert 作業會要求您指定需要更新的文件。 若要擷取完整的文件，您可以使用標題讀取作業底下所述的程式碼片段，然後修改所需的欄位。 下列程式碼片段會對文件進行 upsert 作業：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然後訂閱 Mono。 請參閱插入作業中的 Mono 訂閱程式碼片段。

### <a name="delete-operation"></a>刪除動作

下列程式碼片段會執行刪除作業：

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然後訂閱 Mono，參閱插入作業中的 Mono 訂閱程式碼片段。 完整程式碼範例可以在 [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncInSpring) GitHub 存放庫中取得。

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase 作為索引鍵/值組

這是簡單的工作負載類型，您可以在其中執行查閱，而不是查詢。 針對索引鍵/值組，請使用下列步驟：

1. 請考量將 "/ID" 當做主要索引鍵，這樣會確保您可以直接在特定分割中執行查閱作業。 建立集合，並指定 "/ID" 作為分割索引鍵。

1. 完全關閉索引。 因為您將會執行查閱作業，所以沒有任何點會產生索引額外負荷。 若要關閉編製索引，請登入 Azure 入口網站，前往 Azure Cosmos DB 帳戶。 開啟 [資料總管]，選取您的 [資料庫] 和 [容器]。 開啟 [調整與設定] 索引標籤，然後選取 [編制索引原則]。 目前的編制索引原則看起來如下所示：
    
   ```json
   {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ]
    }
   ````

   將上述的編制索引原則取代為下列原則：

   ```json
   {
    "indexingMode": "none",
    "automatic": false,
    "includedPaths": [],
    "excludedPaths": []
    }
   ```

1. 使用下列程式碼片段來建立連線物件。 連線物件 (要放置在 @Bean 中或使其成為靜態)：

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(PrimaryKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

現在您可以執行 CRUD 作業，如下所示：

### <a name="read-operation"></a>讀取作業

若要讀取項目，請使用下列程式碼片段：

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

若要插入項目，您可以執行下列程式碼：

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

然後訂閱 Mono，如下所示：

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

### <a name="upsert-operation"></a>Upsert 作業

若要更新項目的值，請參閱下列程式碼片段：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然後訂閱 Mono，參閱插入作業中的 Mono 訂閱程式碼片段。

### <a name="delete-operation"></a>刪除動作

使用下列程式碼片段來執行刪除作業：

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然後訂閱 Mono，參閱插入作業中的 Mono 訂閱程式碼片段。 完整程式碼範例可以在 [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncKeyValue) GitHub 存放庫中取得。

## <a name="data-migration"></a>資料移轉

遷移資料的方式有兩種。

* **使用 Azure Data Factory：** 這是最建議的遷移資料方法。 將來源設定為 Couchbase 和接收為 Azure Cosmos DB SQL API，如需詳細步驟，請參閱 Azure [Cosmos DB Data Factory 連接器](../data-factory/connector-azure-cosmos-db.md)一文。

* **使用 Azure Cosmos DB 資料匯入工具：** 使用具有較少資料量的 VM 時，建議使用這個遷移選項。 如需詳細步驟，請參閱[資料匯入工具](./import-data.md)一文。

## <a name="next-steps"></a>後續步驟

* 若要執行效能測試，請參閱 [Azure Cosmos DB 的效能和規模測試](./performance-testing.md)一文。
* 若要將程式碼最佳化，請參閱 [Azure Cosmos DB 的效能祕訣](./performance-tips-async-java.md)一文。
* 探索 JAVA Async V3 SDK，[SDK 參考](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub 存放庫。
