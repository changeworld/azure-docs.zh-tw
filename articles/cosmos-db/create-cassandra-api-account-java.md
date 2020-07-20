---
title: 教學課程：建置 Java 應用程式來建立 Azure Cosmos DB Cassandra API 帳戶
description: 本教學課程說明如何使用 Java 應用程式建立 Cassandra API 帳戶、新增資料庫 (也稱為 Keyspace)，以及將資料表新增至該帳戶。
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: e114bf3a87f3018cc51c5752d57ce5911053542f
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2020
ms.locfileid: "85118401"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>教學課程：使用 Java 應用程式在 Azure Cosmos DB 中建立 Cassandra API 帳戶，用以儲存索引鍵/值資料

身為開發人員，您可能有使用索引鍵/值組的應用程式。 您可以使用 Azure Cosmos DB 中的 Cassandra API 帳戶來儲存索引鍵/值資料。 本教學課程說明如何使用 Java 應用程式來建立 Azure Cosmos DB Cassandra API 帳戶、新增資料庫 (也稱為 keyspace) 以及新增資料表。 Java 應用程式會使用 [Java 驅動程式](https://github.com/datastax/java-driver)來建立使用者資料庫，其中包含使用者識別碼、使用者名稱、使用者所在城市等詳細資料。  

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 建立 Cassandra 資料庫帳戶
> * 取得帳戶連接字串
> * 建立 Maven 專案和相依性
> * 新增資料庫和資料表
> * 執行應用程式

## <a name="prerequisites"></a>必要條件 

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 

* 取得最新版本的 [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable)。 

* [下載](https://maven.apache.org/download.cgi)並[安裝](https://maven.apache.org/install.html)[Maven](https://maven.apache.org/) 二進位封存檔。 
  - 在 Ubuntu 上，您可以執行 `apt-get install maven` 來安裝 Maven。 

## <a name="create-a-database-account"></a>建立資料庫帳戶 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 

2. 選取 [建立資源] > [資料庫] > [Azure Cosmos DB]。 

3. 在 [新增帳戶] 窗格中，輸入新 Azure Cosmos 帳戶的設定。 

   |設定   |建議的值  |描述  |
   |---------|---------|---------|
   |ID   |   輸入唯一名稱    | 輸入唯一名稱來識別此 Azure Cosmos 帳戶。 <br/><br/>因為 cassandra.cosmosdb.azure.com 會附加到您所提供的識別碼以建立接觸點，所以請使用可供辨識的唯一識別碼。         |
   |API    |  Cassandra   |  API 會決定要建立的帳戶類型。 <br/> 選取 [Cassandra]，因為在本文中您將建立可以使用 Cassandra 查詢語言 (CQL) 語法來查詢的寬欄資料庫。  |
   |訂用帳戶    |  您的訂用帳戶        |  選取您要用於此 Azure Cosmos 帳戶的 Azure 訂用帳戶。        |
   |資源群組   | 輸入名稱    |  選取 [新建]，然後為您的帳戶輸入新的資源群組名稱。 為求簡化，您可以使用和識別碼相同的名稱。    |
   |Location    |  選取最接近使用者的區域    |  選取用來裝載 Azure Cosmos 帳戶的地理位置。 使用最接近使用者的位置，讓他們能以最快速度存取資料。    |

   :::image type="content" source="./media/create-cassandra-api-account-java/create-account.png" alt-text="使用入口網站建立帳戶":::

4. 選取 [建立]。 <br/>建立帳戶需要幾分鐘的時間。 建立資源之後，您可以在入口網站的右側看到**部署成功**通知。

## <a name="get-the-connection-details-of-your-account"></a>取得您帳戶的連線詳細資料  

從 Azure 入口網站取得連接字串資訊，並將其複製到 Java 設定檔。 連接字串可讓您的應用程式與託管資料庫進行通訊。 

1. 在 [Azure 入口網站](https://portal.azure.com/)中，移至您的 Azure Cosmos 帳戶。 

2. 開啟 [連接字串] 窗格。  

3. 複製**接觸點**、**連接埠**、**使用者名稱**以及**主要密碼**的值，以用於後續步驟。

## <a name="create-the-project-and-the-dependencies"></a>建立專案和相依性 

您在本文中使用的 Java 範例專案會裝載在 GitHub 中。 您可以執行本文件中的步驟，或從 [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) 存放庫下載範例。 

下載檔案後，請更新 `java-examples\src\main\resources\config.properties` 檔案內的連接字串資訊，並加以執行。  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

使用下列步驟從頭建置範例： 

1. 從終端機或命令提示字元中，建立名為 Cassandra-demo 的新 Maven 專案。 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. 找到 `cassandra-demo` 資料夾。 使用文字編輯器，開啟產生的 `pom.xml` 檔案。 

   新增 Cassandra 相依性，並建置專案所需的外掛程式，如 [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/pom.xml) 檔案中所示。  

3. 在 `cassandra-demo\src\main` 資料夾下方，建立名為 `resources` 的新資料夾。  在資源資料夾下方，新增 config.properties 和 log4j.properties 檔案：

   - [Config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/resources/config.properties) 檔案會儲存 Cassandra API 帳戶的連線端點和索引鍵值。 
   
   - [Log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/resources/log4j.properties) 檔案會定義與 Cassandra API 互動所需的記錄層級。  

4. 瀏覽至 `src/main/java/com/azure/cosmosdb/cassandra/` 資料夾。 在 Cassandra 資料夾內，建立名為 `utils` 的另一個資料夾。 新資料夾會儲存連線至 Cassandra API 帳戶時所需的公用程式類別。 

   新增 [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) 類別來建立叢集，以及開啟和關閉 Cassandra 工作階段。 叢集會連線至 Azure Cosmos DB 中的 Cassandra API，並傳回要存取的工作階段。 使用 [Configurations](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) 類別來讀取 config.properties 檔案中的連接字串資訊。 

5. Java 範例會以使用者的資訊 (例如使用者名稱、使用者識別碼、使用者所在城市) 來建立資料庫。 您需要定義 get 和 set 方法，以在main 函式中存取使用者詳細資料。
 
   使用 get 和 set 方法，在 `src/main/java/com/azure/cosmosdb/cassandra/` 資料下方建立 [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/java/com/azure/cosmosdb/cassandra/examples/UserProfile.java) 類別。 

## <a name="add-a-database-and-a-table"></a>新增資料庫和資料表  

本節說明如何使用 CQL 來新增資料庫 (Keyspace) 和資料表。

1. 在 `src\main\java\com\azure\cosmosdb\cassandra` 資料夾下方，建立名為 `repository` 的新資料夾。 

2. 建立 `UserRepository` Java 類別，並將下列程式碼新增至該類別： 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. 找到 `src\main\java\com\azure\cosmosdb\cassandra` 資料夾並建立名為 `examples` 的新子資料夾。

4. 建立 `UserProfile` Java 類別。 此類別包含會呼叫您先前所定義 createKeyspace 和 createTable 方法的主要方法： 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>執行應用程式 

1. 開啟命令提示字元或終端機視窗。 貼上下列程式碼區塊。 

   此程式碼會將目錄 (cd) 變更為您建立專案的資料夾路徑。 然後，會執行 `mvn clean install` 命令來在目標資料夾內產生 `cosmosdb-cassandra-examples.jar` 檔案。 最後，會執行 Java 應用程式。

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   終端機視窗會顯示 keyspace 和資料表已建立的通知。 
   
2. 現在，在 Azure 入口網站中開啟 [資料總管]，確認已建立 Keyspace 和資料表。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Java 應用程式建立 Azure Cosmos DB 中的 Cassandra API 帳戶、資料庫及資料表。 您現在可以繼續進行下一篇文章：

> [!div class="nextstepaction"]
> [將範例資料載入 Cassandra API 資料表中](cassandra-api-load-data.md)。
