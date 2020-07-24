---
title: 將 Azure Cosmos DB 繫結至 Azure Spring Cloud 應用程式
description: 了解如何將 Azure Cosmos DB 繫結至 Azure Spring Cloud 應用程式
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 881005c2597eadc3b3b0be9a01fbf9d82d35d050
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070774"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>將 Azure Cosmos DB 資料庫繫結至 Azure Spring Cloud 應用程式

您可以使用 Azure Spring Cloud 將精選的 Azure 服務自動繫結至應用程式，而不必手動設定 Spring Boot 應用程式。 本文會示範如何將您的應用程式繫結至 Azure Cosmos DB 資料庫。

必要條件：

* 已部署的 Azure Spring Cloud 執行個體。 請遵循[關於如何透過 Azure CLI 來部署的快速入門](spring-cloud-quickstart-launch-app-cli.md)來開始使用。
* 具有參與者最低權限水準的 Azure Cosmos DB 帳戶。

## <a name="bind-azure-cosmos-db"></a>繫結 Azure Cosmos DB

Azure Cosmos DB 有五個不同的 API 類型可支援繫結。 下列程序會示範如何使用這些類型：

1. 建立 Azure Cosmos DB 資料庫。 請參閱關於如何[建立資料庫](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)的快速入門以取得說明。 

1. 記錄資料庫的名稱。 在此程序中，資料庫名稱為 **testdb**。

1. 將下列其中一個相依性新增至 Azure Spring Cloud 應用程式的 pom.xml 檔案。 選擇適用於 API 類型的相依性。

    * API 類型：Core (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * API 類型：MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * API 類型：Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API 類型：Gremlin (圖形)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * API 類型：Azure 資料表

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. 使用 `az spring-cloud app update` 來更新目前的部署，或使用 `az spring-cloud app deployment create` 來建立新的部署。 這些命令會搭配新的相依性來更新或建立應用程式。

1. 在 Azure 入口網站中，移至您的 Azure Spring Cloud 服務頁面。 移至 [應用程式儀表板]，然後選取要繫結至 Azure Cosmos DB 的應用程式。 這是您在上一個步驟中更新或部署的同一個應用程式。

1. 選取 [服務繫結]，然後選取 [建立服務繫結]。 若要填寫表單，請選取：
   * [繫結類型] 值 [Azure Cosmos DB]。
   * API 類型。
   * 資料庫名稱。
   * Azure Cosmos DB 帳戶。

    > [!NOTE]
    > 如果您使用 Cassandra，請為資料庫名稱使用 Keyspace。

1. 選取應用程式頁面上的 [重新啟動]，以重新啟動應用程式。

1. 為確保服務的繫結是否正確，請選取繫結名稱並確認其詳細資料。 `property` 欄位應該會類似下列範例：

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何將 Azure Spring Cloud 應用程式繫結至 Azure Cosmos DB 資料庫。 若要深入了解如何將服務繫結至您的應用程式，請參閱[繫結至 Azure Cache for Redis 快取](spring-cloud-tutorial-bind-redis.md)。
