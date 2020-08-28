---
title: 在 Azure Cosmos DB 中佈建自動調整輸送量
description: 了解如何使用 Azure 入口網站、CLI、PowerShell 及各種其他 SDK，在 Azure Cosmos DB 中的容器和資料庫層級佈建自動調整輸送量。
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4e7c5f3f4bf84b7a267cb883df5f375f2a8cf981
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017136"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中的資料庫或容器上佈建自動調整輸送量

本文說明如何在 Azure Cosmos DB 中的資料庫或容器 (集合、圖表或資料表) 上佈建自動調整輸送量。 您可在單一容器上啟用自動調整，或在資料庫上佈建自動調整輸送量，並在資料庫中的所有容器之間共用。

## <a name="azure-portal"></a>Azure 入口網站

### <a name="create-new-database-or-container-with-autoscale"></a>建立可自動調整的新資料庫或容器

1. 登入 [Azure 入口網站](https://portal.azure.com)或 [Azure Cosmos DB 總管](https://cosmos.azure.com/)。

1. 巡覽至 Azure Cosmos DB 帳戶並開啟 [資料總管] 索引標籤。

1. 選取 [新增容器]。 輸入資料庫的名稱、容器和分割區索引鍵。 在 [輸送量] 下，選取 [自動調整] 選項，然後設定資料庫或容器可調整的[最大輸送量 (RU/秒)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works)。

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="建立容器並設定自動調整佈建輸送量":::

1. 選取 [確定]。

若要在共用輸送量資料庫上佈建自動調整，請在建立新的資料庫時，選取 [佈建資料庫輸送量] 選項。 

### <a name="enable-autoscale-on-existing-database-or-container"></a>在現有的資料庫或容器上啟用自動調整

> [!IMPORTANT]
> 在目前版本中，Azure 入口網站是在自動調整與標準 (手動) 佈建輸送量之間進行移轉的唯一方式。 

1. 登入 [Azure 入口網站](https://portal.azure.com)或 [Azure Cosmos DB 總管](https://cosmos.azure.com/)。

1. 巡覽至 Azure Cosmos DB 帳戶並開啟 [資料總管] 索引標籤。

1. 針對容器選取 [調整與設定]，或針對資料庫選取 [調整]。

1. 在 [調整] 下，依序選取 [自動調整] 選項和 [儲存]。

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="在現有的容器上啟用自動調整":::

> [!NOTE]
> 當在現有的資料庫或容器上啟用自動調整時，最大 RU/秒的起始值是由系統根據您目前手動佈建輸送量設定和儲存體來決定。 作業完成之後，即可視需要變更最大 RU/秒。 [深入了解。](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) 

## <a name="azure-cosmos-db-net-v3-sdk-for-sql-api"></a>適用於 SQL API 的 Azure Cosmos DB .NET V3 SDK

使用適用於 SQL API 的 Azure Cosmos DB .NET SDK [3.9 版或更高版本](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)來管理自動調整資源。 

> [!IMPORTANT]
> 您可使用此 .NET SDK 來建立新的自動調整資源。 此 SDK 不支援在自動調整與標準 (手動) 輸送量之間進行移轉。 目前只有 Azure 入口網站支援移轉案例。 

### <a name="create-database-with-shared-throughput"></a>建立具有共用輸送量的資料庫

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>建立具有專用輸送量的容器

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>讀取目前的輸送量 (RU/秒)

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>變更自動調整的最大輸送量 (RU/秒)

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk-for-sql-api"></a>適用於 SQL API 的 Azure Cosmos DB Java V4 SDK

您可使用適用於 SQL API 的 Azure Cosmos DB Java SDK [4.0 版或更高版本](https://mvnrepository.com/artifact/com.azure/azure-cosmos)來管理自動調整資源。

> [!IMPORTANT]
> 您可使用此 Java SDK 來建立新的自動調整資源。 此 SDK 不支援在自動調整與標準 (手動) 輸送量之間進行移轉。 目前只有 Azure 入口網站支援移轉案例。

### <a name="create-database-with-shared-throughput"></a>建立具有共用輸送量的資料庫

#### <a name="async"></a>[非同步](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(MASTER)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

#### <a name="sync"></a>[同步處理](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(MASTER)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>建立具有專用輸送量的容器

#### <a name="async"></a>[非同步](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

#### <a name="sync"></a>[同步處理](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>讀取目前的輸送量 (RU/秒)

#### <a name="async"></a>[非同步](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

#### <a name="sync"></a>[同步處理](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>變更自動調整的最大輸送量 (RU/秒)

#### <a name="async"></a>[非同步](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

#### <a name="sync"></a>[同步處理](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="cassandra-api"></a>Cassandra API

您可以使用 [CQL 命令](manage-scale-cassandra.md#use-autoscale)、 [Azure CLI](cli-samples.md)、 [Azure PowerShell](powershell-samples.md) 或 [Azure Resource Manager 範本](resource-manager-samples.md)，布建 Cassandra API 的 Azure Cosmos DB 帳戶以進行自動調整。

## <a name="azure-cosmos-db-api-for-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

您可以使用 [mongodb 延伸模組命令](mongodb-custom-commands.md)、 [Azure CLI](cli-samples.md)、 [Azure PowerShell](powershell-samples.md) 或 [Azure Resource Manager 範本](resource-manager-samples.md)，為 mongodb API 的 Azure Cosmos DB 帳戶布建自動調整。

## <a name="azure-resource-manager"></a>Azure Resource Manager

您可以使用 Azure Resource Manager 範本，在資料庫或容器層級資源上布建所有 Azure Cosmos DB Api 的自動調整輸送量。 如需範例，請參閱 [Azure Cosmos DB Azure Resource Manager 範本](resource-manager-samples.md) 。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 可以用來針對所有 Azure Cosmos DB 的 Api，在資料庫或容器層級資源上布建自動調整輸送量。 如需範例，請參閱 [Azure Cosmos DB 的 Azure CLI 範例](cli-samples.md)。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 可以用來針對所有 Azure Cosmos DB 的 Api，在資料庫或容器層級資源上布建自動調整輸送量。 如需範例，請參閱 [Azure Cosmos DB 的 Azure PowerShell 範例](powershell-samples.md)。

## <a name="next-steps"></a>後續步驟

* 了解[可自動調整的佈建輸送量優點](provision-throughput-autoscale.md#benefits-of-autoscale)。
* 了解如何[在手動與自動調整輸送量之間進行選擇](how-to-choose-offer.md)。
* 檢閱[自動調整常見問題集](autoscale-faq.md)。
