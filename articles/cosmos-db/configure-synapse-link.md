---
title: 設定及使用適用於 Azure Cosmos DB 的 Azure Synapse Link
description: 瞭解如何啟用 Azure Cosmos DB 帳戶的 Synapse 連結、建立已啟用分析存放區的容器、將 Azure Cosmos 資料庫連線至 Synapse 工作區，以及執行查詢。
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: references_regions
ms.openlocfilehash: 6f9d8603467ac62d443a181fa45615501023a78b
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093565"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db"></a>設定及使用適用於 Azure Cosmos DB 的 Azure Synapse Link
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[適用于 Azure Cosmos DB 的 Azure Synapse 連結](synapse-link.md) 是雲端原生的混合式交易和分析處理 (HTAP) 功能，可讓您對 Azure Cosmos DB 中的運算元據執行近乎即時的分析。 Synapse Link 會在 Azure Cosmos DB 與 Azure Synapse Analytics 之間建立緊密順暢的整合。

Azure Synapse 連結適用于 Azure Cosmos DB SQL API 容器或適用于 Mongo DB 集合的 Azure Cosmos DB API。 使用下列步驟，透過適用于 Azure Cosmos DB 的 Azure Synapse 連結來執行分析查詢：

* [啟用 Azure Cosmos DB 帳戶的 Synapse 連結](#enable-synapse-link)
* [建立已啟用 Azure Cosmos DB 容器的分析存放區](#create-analytical-ttl)
* [將 Azure Cosmos DB 資料庫連線至 Synapse 工作區](#connect-to-cosmos-database)
* [使用 Synapse Spark 查詢分析存放區](#query-analytical-store-spark)
* [使用無伺服器 SQL 集區查詢分析存放區](#query-analytical-store-sql-on-demand)
* [使用無伺服器 SQL 集區來分析 Power BI 中的資料並將其視覺化](#analyze-with-powerbi)

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>啟用 Azure Cosmos DB 帳戶的 Azure Synapse 連結

### <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure 帳戶](create-sql-api-dotnet.md#create-account)，或選取現有的 Azure Cosmos DB 帳戶。

1. 流覽至您的 Azure Cosmos DB 帳戶，然後開啟 [ **功能** ] 窗格。

1. 從功能清單中選取 [Synapse Link]。

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="尋找 Synapse 連結功能":::

1. 接下來，系統會提示您啟用帳戶的 Synapse Link。 選取 [啟用]。 此程式可能需要1到5分鐘的時間才能完成。

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="啟用 Synapse Link 功能":::

1. 您的帳戶現在已啟用，可使用 Synapse Link。 接下來，請參閱如何建立已啟用分析存放區的容器，以自動開始將操作資料從交易存放區複寫到分析存放區。

> [!NOTE]
> 開啟 Synapse 連結並不會自動開啟分析存放區。 當您在 Cosmos DB 帳戶上啟用 Synapse 連結之後，請在建立容器時啟用分析存放區，以開始將作業資料複寫至分析存放區。 

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl">使用分析存放區來建立 Azure Cosmos 容器</a>

建立容器時，您可以在 Azure Cosmos 容器上開啟分析存放區。 您可以在建立容器期間使用 Azure Cosmos DB SDK，以使用 Azure 入口網站或設定 `analyticalTTL` 屬性。

> [!NOTE]
> 目前，只能為 (新的和現有的帳戶中) **新的** 容器啟用分析存放區。 您可以使用[Azure Cosmos DB 的遷移工具](cosmosdb-migrationchoices.md)，將資料從您的現有容器遷移至新的容器。

### <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com/) 或 [Azure Cosmos DB 總管](https://cosmos.azure.com/)。

1. 巡覽至 Azure Cosmos DB 帳戶並開啟 [資料總管] 索引標籤。

1. 選取 [新增容器]，然後輸入資料庫的名稱、容器、分割區索引鍵和輸送量詳細資料。 開啟 [分析存放區] 選項。 啟用分析存放區之後，其會建立一個容器，並將 `AnalyicalTTL` 屬性設為預設值 -1 (無限保留期)。 此分析存放區會保留記錄的所有歷程記錄版本。

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="開啟 Azure Cosmos 容器的分析存放區":::

1. 如果您先前未在此帳戶上啟用 Synapse Link，系統會提示您這麼做，因為在建立已啟用分析存放區的容器時，這是必要條件。 若出現提示，請選取 [啟用 Synapse Link]。 此程式可能需要1到5分鐘的時間才能完成。

1. 選取 [確定]，以建立已啟用分析存放區的 Azure Cosmos 容器。

1. 建立容器之後，請按一下 [ **設定**]，然後在資料總管中的檔下方，確認已啟用分析存放區，並檢查是否已開啟 [ **分析存放區存留時間** ] 選項。

### <a name="net-sdk"></a>.NET SDK

下列程式碼會使用 .NET SDK 來建立具有分析存放區的容器。 將分析 TTL 屬性設定為必要值。 如需允許值的清單，請參閱[分析 TTL 支援的值](analytical-store-introduction.md#analytical-ttl)一文：

```csharp
// Create a container with a partition key, and analytical TTL configured to  -1 (infinite retention)
string containerId = “myContainerName”;
int analyticalTtlInSec = -1;
ContainerProperties cpInput = new ContainerProperties()
            {
Id = containerId,
PartitionKeyPath = "/id",
AnalyticalStorageTimeToLiveInSeconds = analyticalTtlInSec,
};
 await this. cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(cpInput);
```

### <a name="java-v4-sdk"></a>Java V4 SDK

下列程式碼會使用 Java V4 SDK 來建立具有分析存放區的容器。 將 `AnalyticalStoreTimeToLiveInSeconds` 屬性設為必要值︰

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>Python V4 SDK

Python 2.7 和 Azure Cosmos DB SDK 4.1.0 是所需的最低版本，且 SDK 僅與 SQL API 相容。

第一個步驟是確定您使用的是 [Azure Cosmos DB PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)的最小版本4.1.0：

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
下一個步驟會使用 Azure Cosmos DB Python SDK，建立具有分析存放區的容器：

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> 將分析存放區時間更新為即時

使用特定 TTL 值啟用分析存放區之後，稍後可將其更新為其他有效的值。 您可以使用 Azure 入口網站或 SDK 來更新此值。 如需各種分析 TTL 組態選項的相關資訊，請參閱[分析 TTL 支援的值](analytical-store-introduction.md#analytical-ttl)一文。

#### <a name="azure-portal"></a>Azure 入口網站

如果您已透過 Azure 入口網站建立已啟用分析存放區的容器，其會包含 -1 的預設分析 TTL。 請使用下列步驟來更新此值：

1. 登入 [Azure 入口網站](https://portal.azure.com/) 或 [Azure Cosmos DB 總管](https://cosmos.azure.com/)。

1. 巡覽至 Azure Cosmos DB 帳戶並開啟 [資料總管] 索引標籤。

1. 選取已啟用分析存放區的現有容器。 將其展開並修改下列值：

  * 開啟 [規模與設定] 視窗。
  * 在 [設定] 下，尋找 ** [分析儲存體存留時間]**。
  * 選取 [開啟 (無預設值)]，或選取 [開啟] 並設定 TTL 值
  * 按一下 [儲存]  儲存變更。

#### <a name="net-sdk"></a>.NET SDK

下列程式碼說明如何使用 .NET SDK 來更新分析存放區的 TTL：

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>Java V4 SDK

下列程式碼說明如何使用 Java V4 SDK 來更新分析存放區的 TTL：

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> 連線至 Synapse 工作區

使用[連線至 Azure Synapse Link](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) 中的指示，了解如何使用 Azure Synapse Link 從 Azure Synapse Analytics Studio 存取 Azure Cosmos DB 資料庫。

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a> 使用 Azure Synapse Analytics 的 Apache Spark 查詢分析存放區

使用[查詢 Azure Cosmos DB 分析存放區](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md)一文中的指示，了解如何使用 Synapse Spark 進行查詢。 該文章會提供一些範例，說明如何從 Synapse 手勢與分析存放區互動。 當您以滑鼠右鍵按一下容器時，就可以看到這些手勢。 您可以透過手勢快速產生程式碼，並依據您的需求進行調整。 這些手勢也非常適合透過按一下來探索資料。

## <a name="query-the-analytical-store-using-serverless-sql-pool-in-azure-synapse-analytics"></a><a id="query-analytical-store-sql-on-demand"></a> 使用 Azure Synapse Analytics 中的無伺服器 SQL 集區來查詢分析存放區

無伺服器 SQL 集區可讓您在使用 Azure Synapse 連結啟用的 Azure Cosmos DB 容器中查詢及分析資料。 您可以近乎即時地分析資料，而不會影響交易式工作負載的效能。 它提供了一個熟悉的 T-sql 語法，可從分析存放區查詢資料，並透過 T-sql 介面整合各種 BI 和臨機操作查詢工具的連接。 若要深入瞭解，請參閱 [使用無伺服器 SQL 集區的查詢分析存放區](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) 一文。

## <a name="use-serverless-sql-pool-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>使用無伺服器 SQL 集區來分析 Power BI 中的資料並將其視覺化

您可以建立無伺服器 SQL 集區資料庫，並透過 Synapse 連結來進行 Azure Cosmos DB 的流覽。 稍後，您可以查詢 Azure Cosmos 容器，然後使用這些視圖 Power BI 建立模型，以反映該查詢。 若要深入瞭解，請參閱如何使用 [無伺服器 SQL 集區來分析 Azure Cosmos DB 資料與 Synapse 連結](synapse-link-power-bi.md) 文章。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

[Azure Resource Manager 範本](./manage-with-templates.md#azure-cosmos-account-with-analytical-store)會為 SQL API 建立已啟用 Synapse Link Azure Cosmos DB 帳戶。 此範本會在一個區域中建立 Core (SQL) API 帳戶，以及已啟用並設定分析 TTL、並且有選項可使用手動或自動調整輸送量的容器。 若要部署此範本，請在讀我檔案頁面中，按一下 [部署至 Azure]。

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> 開始使用 Azure Synpase 連結 - 範例

您可以在 [GitHub](https://aka.ms/cosmosdb-synapselink-samples)上找到開始使用 Azure Synapse 連結的範例。 這些都展示了 IoT 和零售案例的端對端解決方案。 您也可以在 [mongodb](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples) 資料夾下的相同存放庫中，找到對應至 Azure Cosmos DB API for mongodb 的範例。 

## <a name="next-steps"></a>後續步驟

若要深入了解，請參閱下列文件：

* [適用於 Azure Cosmos DB 的 Azure Synapse Link。](synapse-link.md)

* [Azure Cosmos DB 分析存放區概觀。](analytical-store-introduction.md)

* [關於適用於 Azure Cosmos DB 的 Synapse Link 常見問題。](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Analytics 中的 Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)。

* [Azure Synapse Analytics 中的無伺服器 SQL 集區執行時間支援](../synapse-analytics/sql/on-demand-workspace-overview.md)。
