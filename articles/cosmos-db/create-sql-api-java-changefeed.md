---
title: 使用變更摘要建立端對端 Azure Cosmos DB JAVA SDK v4 應用程式範例
description: 本指南會逐步引導您瞭解簡單的 JAVA SQL API 應用程式，它會將檔插入 Azure Cosmos DB 容器中，同時使用變更摘要來維護容器的具體化視圖。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: 5e8656e891d250547174aa3deb27a94eebaa0ba3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125667"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>如何建立 JAVA 應用程式，以使用 Azure Cosmos DB SQL API 和變更摘要處理器

> [!IMPORTANT]  
> 如需有關 Azure Cosmos DB JAVA SDK v4 的詳細資訊，請參閱 Azure Cosmos DB JAVA SDK v4 版本資訊、 [Maven 存放庫](https://mvnrepository.com/artifact/com.azure/azure-cosmos)、AZURE COSMOS DB java sdk v4[效能秘訣](performance-tips-java-sdk-v4-sql.md)和 Azure Cosmos DB JAVA sdk v4[疑難排解指南](troubleshoot-java-sdk-v4-sql.md)。
>

本操作指南會逐步引導您使用 Azure Cosmos DB SQL API 將檔插入 Azure Cosmos DB 容器中的簡單 JAVA 應用程式，同時使用變更摘要和變更摘要處理器來維護容器的具體化視圖。 JAVA 應用程式會使用 Azure Cosmos DB JAVA SDK v4 與 Azure Cosmos DB SQL API 進行通訊。

## <a name="prerequisites"></a>先決條件

* Azure Cosmos DB 帳戶的 URI 和金鑰

* Maven

* Java 8

## <a name="background"></a>背景

Azure Cosmos DB 變更摘要會提供事件驅動介面，以觸發動作以回應檔插入。 這有許多用途。 例如，在讀取和寫入繁重的應用程式中，變更摘要的主要用途是在內嵌檔時，建立容器的即時**具體化視圖**。 具體化檢視容器會保存相同的資料，但會加以分割以便有效率的讀取，讓應用程式能夠有效率的讀取和寫入。

管理變更摘要事件的工作，主要是由 SDK 內建的變更摘要處理器程式庫負責處理。 此程式庫的功能強大，可以在多個背景工作角色之間散發變更摘要事件（如果需要的話）。 您只需要提供變更摘要庫回呼。

這個簡單的範例示範變更摘要處理器程式庫，其中包含單一背景工作從具體化視圖建立和刪除檔。

## <a name="setup"></a>安裝程式

如果您尚未這麼做，請複製應用程式範例存放庫：

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

開啟存放庫目錄中的終端機。 藉由執行來建置應用程式

```bash
mvn clean package
```

## <a name="walkthrough"></a>逐步介紹

1. 第一次檢查時，您應該會有 Azure Cosmos DB 帳戶。 在瀏覽器中開啟**Azure 入口網站**，移至您的 Azure Cosmos DB 帳戶，然後在左窗格中，流覽至 [**資料總管**]。

    ![Azure Cosmos DB 帳戶](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. 使用下列命令在終端機中執行應用程式：

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. 當您看到以下內容時按 Enter 鍵

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    然後回到瀏覽器中的 [Azure 入口網站資料總管。 您會看到已新增三個空白容器的資料庫 **GroceryStoreDatabase**： 

    * **InventoryContainer** - 我們的範例雜貨店的清查記錄，其依據項目 ```id``` (這是 UUID) 分割。
    * **InventoryContainer-pktype** - 清查記錄的具體化檢視，已針對項目 ```type``` 的查詢最佳化
    * **InventoryContainer-租用**-變更摘要一律需要租用容器;租用會追蹤應用程式讀取變更摘要的進度。


    ![空的容器](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. 在終端機中，您應該會看到提示

    ```bash
    Press enter to start creating the materialized view...
    ```

    按 Enter 鍵。 現在，下列程式碼區塊會執行並初始化另一個執行緒上的變更摘要處理器： 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>JAVA SDK V4 （Maven com）。 azure：： azure-cosmos）非同步 API

    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for change feed processor start
    ```

    ```"SampleHost_1"``` 是變更摘要處理器背景工作角色的名稱。 ```changeFeedProcessorInstance.start()``` 實際上會啟動變更摘要處理器。

    返回瀏覽器中的 [Azure 入口網站] 資料總管。 在 **InventoryContainer-leases** 容器底下，按一下 **items** 以查看其內容。 您會看到變更摘要處理器已填入租用容器，也就是處理器已在 **InventoryContainer** 的某些分割區上將租用指派給 ```SampleHost_1``` 背景工作角色。

    ![租用](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. 在終端機中再次按 Enter 鍵。 這會觸發 10 份要插入 **InventoryContainer** 中的文件。 每份檔插入都會以 JSON 形式出現在變更摘要中。下列回呼程式碼會藉由將 JSON 檔鏡像到具體化視圖中，來處理這些事件：

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>JAVA SDK V4 （Maven com）。 azure：： azure-cosmos）非同步 API

    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

1. 允許程式碼執行 5-10 秒。 然後返回 [Azure 入口網站] 資料總管，然後流覽至**InventoryContainer > 專案**]。 您應會看到項目插入清查容器中；請記下資料分割索引鍵 (```id```)。

    ![摘要容器](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. 現在，在資料總管中導覽至 **InventoryContainer-pktype > items**。 這是具體化的視圖-此容器鏡像**InventoryContainer**中的專案，因為它們是透過變更摘要以程式設計方式插入。 請記下資料分割索引鍵 (```type```)。 所以此具體化檢視已針對篩選 ```type``` 的查詢最佳化，這在 **InventoryContainer** 上沒有效率，因為其依據 ```id``` 進行分割。

    ![具體化檢視](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. 我們只要使用單一 ```upsertItem()``` 呼叫，就會同時從 **InventoryContainer** 和 **InventoryContainer-pktype** 中刪除文件。 首先，請參閱 Azure 入口網站資料總管。 我們會刪除 ```/type == "plums"```的文件；其在底下以紅色圍住

    ![具體化檢視](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    再次按 Enter 鍵，以呼叫範例程式碼中的 ```deleteDocument()``` 函式。 如下所示，此函式會使用 ```/ttl == 5``` 來 upsert 新版的文件，以將文件存留時間 (TTL) 設定為 5 秒。 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>JAVA SDK V4 （Maven com）。 azure：： azure-cosmos）非同步 API

    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    變更摘要 ```feedPollDelay``` 已設定為100毫秒; 因此，變更摘要幾乎會立即回應此更新，並會 ```updateInventoryTypeMaterializedView()``` 顯示上述的呼叫。 最後一個函式呼叫會將 TTL 為 5 秒的新文件 upsert 到 **InventoryContainer-pktype** 中。

    結果是大約在 5 秒後，文件就會到期並從這兩個容器中刪除。

    此程式是必要的，因為變更摘要只會發出專案插入或更新時的事件，而不是刪除專案時。

1. 再按一次 Enter 鍵，關閉程式並清除其資源。
