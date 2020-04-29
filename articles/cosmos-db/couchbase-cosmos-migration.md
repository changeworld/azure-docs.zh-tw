---
title: 從 CouchBase 遷移至 Azure Cosmos DB SQL API
description: 從 CouchBase 遷移至 Azure Cosmos DB SQL API 的逐步指引
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77210940"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>從 CouchBase 遷移至 Azure Cosmos DB SQL API

Azure Cosmos DB 是可調整、全域散發且完全受控的資料庫。 它可為您的資料提供保證低延遲的存取。 若要深入瞭解 Azure Cosmos DB，請參閱[總覽](introduction.md)文章。 本文提供指示，說明如何將連線至 Couchbase 的 JAVA 應用程式遷移至 Azure Cosmos DB 中的 SQL API 帳戶。

## <a name="differences-in-nomenclature"></a>命名法的差異

以下是與 Couchbase 相較之下，Azure Cosmos DB 中以不同方式工作的主要功能：

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Couchbase 伺服器| 帳戶       |
|位址           | 資料庫      |
|位址           | 容器/集合 |
|JSON 檔    | 專案/檔 |

## <a name="key-differences"></a>主要差異

* Azure Cosmos DB 在檔中具有 "ID" 欄位，而 Couchbase 的識別碼為值區的一部分。 [識別碼] 欄位在資料分割中是唯一的。

* Azure Cosmos DB 使用資料分割或分區化技術進行調整。 這表示它會將資料分割成多個分區/磁碟分割。 系統會根據您提供的分割區索引鍵屬性來建立這些磁碟分割/分區。 您也可以選取資料分割索引鍵，以優化讀取作業或讀取/寫入優化。 若要深入瞭解，請參閱[分割](./partition-data.md)一文。

* 在 Azure Cosmos DB 中，最上層階層不需要表示集合，因為集合名稱已經存在。 這項功能可讓 JSON 結構變得更簡單。 以下範例會顯示 Couchbase 與 Azure Cosmos DB 之間資料模型的差異：

   **Couchbase**： Document ID = "99FF4444"

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

   **Azure Cosmos DB**：請參閱檔中的 "ID"，如下所示

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

Azure Cosmos DB 具有下列 Sdk 來支援不同的 JAVA 架構：

* 非同步 SDK
* 春季開機 SDK

下列各節說明每個 Sdk 的使用時機。 假設有三種工作負載類型的範例：

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase 做為檔存放庫 & 春季資料型自訂查詢

如果您要遷移的工作負載是以以彈簧開機為基礎的 SDK 為基礎，您可以使用下列步驟：

1. 將父系新增至 POM 檔案：

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. 將屬性新增至 POM 檔案：

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. 將相依性新增至 POM 檔案：

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. 在 [資源] 下新增應用程式屬性，並指定下列各項。 請務必取代 URL、金鑰和資料庫名稱參數：

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. 在模型中定義集合的名稱。 您也可以指定進一步的注釋。 例如，識別碼、資料分割索引鍵，以明確表示它們：

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

其中 *_repo*是存放庫的物件，而*DOC*則是 POJO 類別的物件。 您可以使用`.save`插入或 upsert （如果找到具有指定識別碼的檔）。 下列程式碼片段示範如何插入或更新 doc 物件：

```_repo.save(doc);```

### <a name="delete-operation"></a>刪除作業

請考慮下列程式碼片段，其中 doc 物件將會有必要的識別碼和分割區索引鍵，以找出並刪除物件：

```_repo.delete(doc);```

### <a name="read-operation"></a>讀取作業

您可以讀取包含或不指定分割區索引鍵的檔。 如果您未指定資料分割索引鍵，則會將它視為跨分割區查詢。 請考慮下列程式碼範例，第一個會使用識別碼和資料分割索引鍵欄位來執行作業。 第二個範例會使用一般欄位 &，而不需要指定資料分割索引鍵欄位。

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

這就是，您現在可以使用您的應用程式搭配 Azure Cosmos DB。 如需本檔中所述範例的完整程式碼範例，請參閱[CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub 存放庫 \ （英文 \）。

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase 做為使用 N1QL 查詢 & 的檔存放庫

N1QL 查詢是在 Couchbase 中定義查詢的方式。

|N1QL 查詢 | Azure CosmosDB 查詢|
|-------------------|-------------------|
|SELECT META （`TravelDocument`）. id AS id， `TravelDocument`. * FROM `TravelDocument` WHERE `_type` = ".com. xx. xx.. x.. x.. x.. x.. x.. x... x m ` Validity` .. x.. x.. m.. x。   | 選取 [c]。 c 中的 id、c，c. country = ' 印度 '，其中 c. _type = ".com. xx. x. x.. x. x.. x. x. x = ' 印度 ' 和 m. 類型 = ' 多重輸入 ' 和 m. 國家/地區（' 印度 '，' 不丹 '）依 c 排序。有效性 DESC 位移0限制25 |

您可以注意到 N1QL 查詢中的下列變更：

* 您不需要使用中繼關鍵字或參閱第一層檔。 相反地，您可以建立自己的容器參考。 在此範例中，我們將它視為 "c" （它可以是任何專案）。 此參考會當做所有第一層欄位的前置詞使用。 Fr 範例、c.id、c. country 等等

* 現在您可以在子檔上執行聯結，而不是「任何」，而是使用「m」之類的專用別名來參考。 建立子檔的別名之後，您必須使用別名。 例如，m. Country。

* Azure Cosmos DB 查詢中的位移順序不同，您必須先指定 OFFSET then LIMIT。 如果您使用的是自訂定義的最大查詢，建議您不要使用春季資料 SDK，因為它在用戶端可能會有不必要的額外負荷，同時將查詢傳遞給 Azure Cosmos DB。 相反地，我們有直接的非同步 JAVA SDK，在此情況下可以有效率地使用。

### <a name="read-operation"></a>讀取作業

使用 Async JAVA SDK，並執行下列步驟：

1. 在 POM 檔案上設定下列相依性：

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. 使用`ConnectionBuilder`方法建立 Azure Cosmos DB 的連線物件，如下列範例所示。 請確定您將此宣告放入 bean 中，讓下列程式碼只會執行一次：

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

1. 若要執行查詢，您需要執行下列程式碼片段：

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

現在，透過上述方法的協助，您可以傳遞多個查詢並執行，而不會有任何麻煩。 如果您需要執行一個大型查詢，可以分割成多個查詢，請嘗試下列程式碼片段，而不是前一個：

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

使用先前的程式碼，您可以平行執行查詢，並增加要優化的分散式執行。 此外，您也可以執行 insert 和 update 作業：

### <a name="insert-operation"></a>插入動作

若要插入檔，請執行下列程式碼：

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

Upsert 作業會要求您指定需要更新的檔。 若要提取完整的檔，您可以使用標題讀取作業底下所述的程式碼片段，然後修改所需的欄位。 下列程式碼片段會更新插入檔：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然後訂閱 mono。 請參閱插入作業中的 mono 訂用帳戶程式碼片段。

### <a name="delete-operation"></a>刪除動作

下列程式碼片段會執行刪除作業：

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然後訂閱 mono，請參閱插入作業中的 mono 訂閱程式碼片段。 完整的程式碼範例可在[CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub 存放庫中取得。

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase 做為索引鍵/值組

這是簡單的工作負載類型，您可以在其中執行查閱，而不是查詢。 針對索引鍵/值組，請使用下列步驟：

1. 請考慮將 "/ID" 當做主要金鑰，這可確保您可以直接在特定分割區中執行查閱作業。 建立集合，並指定 "/ID" 做為分割區索引鍵。

1. 完全關閉索引。 因為您將會執行查閱作業，所以沒有任何時間點的索引額外負荷。 若要關閉索引編制，請登入 Azure 入口網站，前往 Azure Cosmos DB 帳戶。 開啟**資料總管**，並選取您的**資料庫**和**容器**。 開啟 [**調整 & 設定**] 索引標籤，然後選取 [**檢索原則**]。 目前的編制索引原則看起來如下所示：
    
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

   將上述的索引編制原則取代為下列原則：

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. 使用下列程式碼片段來建立連線物件。 Connection 物件（要放入@Bean或設為靜態）：

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

現在您可以執行 CRUD 作業，如下所示：

### <a name="read-operation"></a>讀取作業

若要讀取專案，請使用下列程式碼片段：

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

若要插入專案，您可以執行下列程式碼：

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

然後訂閱 mono，如下所示：

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

若要更新專案的值，請參閱下列程式碼片段：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然後訂閱 mono，請參閱插入作業中的 mono 訂閱程式碼片段。

### <a name="delete-operation"></a>刪除動作

使用下列程式碼片段來執行刪除作業：

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然後訂閱 mono，請參閱插入作業中的 mono 訂閱程式碼片段。 完整的程式碼範例可在[CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub 存放庫中取得。

## <a name="data-migration"></a>資料移轉

有兩種方式可以遷移資料。

* **使用 Azure Data Factory：** 這是最建議用來遷移資料的方法。 將來源設定為 Couchbase 和 sink Azure Cosmos DB SQL API，如需詳細步驟，請參閱 Azure [Cosmos DB Data Factory 連接器](../data-factory/connector-azure-cosmos-db.md)一文。

* **使用 Azure Cosmos DB 資料匯入工具：** 建議使用具有較少資料的 Vm 來進行遷移。 如需詳細步驟，請參閱[資料匯入](./import-data.md)工具文章。

## <a name="next-steps"></a>後續步驟

* 若要執行效能測試，請參閱[Azure Cosmos DB 文章的效能和規模測試](./performance-testing.md)。
* 若要將程式碼優化，請參閱[Azure Cosmos DB 的效能秘訣](./performance-tips-async-java.md)一文。
* 探索 JAVA Async V3 SDK， [sdk 參考](https://github.com/Azure/azure-cosmosdb-java/tree/v3)github 存放庫。
