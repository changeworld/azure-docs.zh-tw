---
title: 將資料移轉至 Azure Cosmos DB 的 MongoDB 版 API 的預先遷移步驟
description: 本檔概要說明從 MongoDB 到 Cosmos DB 的資料移轉的必要條件。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 89cd1de3658c16fccdb70567641a68f5c1575507
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791743"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>將資料從 MongoDB 遷移至 Azure Cosmos DB 的 MongoDB 版 API 的預先遷移步驟

在您將資料從 MongoDB （內部部署或雲端）遷移至適用于 MongoDB Azure Cosmos DB 的 API 之前，您應該：

1. [閱讀使用 Azure Cosmos DB 適用于 MongoDB 的 API 的重要考慮](#considerations)
2. [選擇遷移資料的選項](#options)
3. [估計您的工作負載所需的輸送量](#estimate-throughput)
4. [為您的資料選擇最佳的分割區索引鍵](#partitioning)
5. [瞭解您可以在資料上設定的編制索引原則](#indexing)

如果您已完成上述的先前必要條件以進行遷移，您可以[使用 Azure 資料庫移轉服務，將 mongodb 資料移轉至適用于 mongodb 的 AZURE COSMOS DB API](../dms/tutorial-mongodb-cosmos-db.md)。 此外，如果您尚未建立帳戶，您可以流覽任何顯示步驟來建立帳戶的[快速入門](create-mongodb-dotnet.md)。

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>使用 Azure Cosmos DB 適用于 MongoDB 的 API 時的考慮

以下是有關 Azure Cosmos DB 適用于 MongoDB 的 API 的特定特性：

- **容量模型**： Azure Cosmos DB 上的資料庫容量是以輸送量為基礎的模型為基礎。 此模型是以[每秒的要求單位](request-units.md)為基礎，這是一個單位，代表可針對集合以每秒為基礎執行的資料庫作業數目。 此容量可以在[資料庫或集合層級](set-throughput.md)配置，也可以布建在配置模型上，或使用自動調整布[建的輸送量](provision-throughput-autoscale.md)。

- **要求單位**：每個資料庫作業在 Azure Cosmos DB 中都有相關聯的要求單位（ru）成本。 執行時，會從指定秒的可用要求單位層級中減去這種情況。 如果要求所需的 RU 數超過目前配置的 RU/秒，則有兩個選項可解決此問題-增加 ru 的數量，或等到下一秒啟動後再重試一次作業。

- **彈性容量**：指定集合或資料庫的容量可以隨時變更。 這可讓資料庫彈性適應工作負載的輸送量需求。

- **自動分區化**： Azure Cosmos DB 提供只需要分區（或分割區索引鍵）的自動分割系統。 [自動分割機制](partition-data.md)會在所有 Azure Cosmos DB api 之間共用，並可讓您順暢地進行資料，並透過水準散發進行整個調整。

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>適用于 MongoDB 的 Azure Cosmos DB API 的遷移選項

[適用于 Azure Cosmos DB 適用于 MongoDB 的 API 的 Azure 資料庫移轉服務](../dms/tutorial-mongodb-cosmos-db.md)提供一個機制，可提供完全受控的裝載平臺、遷移監視選項和自動節流處理，以簡化資料移轉。 完整的選項清單如下所示：

|**遷移類型**|**解決方法**|**考量**|
|---------|---------|---------|
|離線|[資料移轉工具](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;易於設定和支援多個來源 <br/>&bull;不適合大型資料集。|
|離線|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;易於設定和支援多個來源 <br/>&bull;利用 Azure Cosmos DB 大量執行程式程式庫 <br/>&bull;適用于大型資料集 <br/>&bull;缺少檢查點表示在遷移過程中的任何問題，都需要重新開機整個遷移程式<br/>&bull;缺少寄不出的信件佇列，表示有幾個錯誤的檔案可能會停止整個遷移程式 <br/>&bull;需要自訂程式碼來增加特定資料來源的讀取輸送量|
|離線|[現有的 Mongo 工具（mongodump、mongorestore、Studio3T）](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;易於設定和整合 <br/>&bull;需要適用于節流的自訂處理|
|線上|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull;完全受控的遷移服務。<br/>&bull;提供遷移工作的裝載和監視解決方案。 <br/>&bull;適用于大型資料集，並負責複寫即時變更 <br/>&bull;僅適用于其他 MongoDB 來源|


## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a>估計您的工作負載所需的輸送量

在 Azure Cosmos DB 中，會事先布建輸送量，並以每秒的要求單位（RU）來測量。 不同于 Vm 或內部部署伺服器，ru 很容易就能隨時相應增加和減少。 您可以立即變更已布建的 ru 數目。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的要求單位](request-units.md)。

您可以使用 [ [Azure Cosmos DB 容量計算機](https://cosmos.azure.com/capacitycalculator/)]，根據您的資料庫帳戶設定、資料量、檔案大小，以及每秒所需的讀取和寫入來判斷要求單位數量。

以下是影響必要 ru 數目的重要因素：
- **檔案大小**：當專案/檔的大小增加時，用來讀取或寫入專案/檔的 ru 數目也會增加。

- **檔案屬性計數**：用來建立或更新檔的 ru 數目，與其屬性的數目、複雜度和長度有關。 您可以[限制已編制索引的屬性數目](mongodb-indexing.md)，以減少寫入作業的要求單位耗用量。

- **查詢模式**：查詢的複雜性會影響查詢耗用多少要求單位。 

瞭解查詢成本的最佳方式是使用 Azure Cosmos DB 中的範例資料，並使用`getLastRequestStastistics`命令[從 MongoDB Shell 執行範例查詢](connect-mongodb-account.md)，以取得要求費用，這會輸出取用的 ru 數目：

`db.runCommand({getLastRequestStatistics: 1})`

此命令會輸出類似下列的 JSON 檔：

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

您也可以使用[診斷設定](cosmosdb-monitor-resource-logs.md)來瞭解針對 Azure Cosmos DB 執行之查詢的頻率和模式。 診斷記錄的結果可以傳送至儲存體帳戶、EventHub 實例或[Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>選擇分割區索引鍵
分割（也稱為分區化）是在遷移資料之前的重要考慮點。 Azure Cosmos DB 使用完全受控的分割來增加資料庫中的容量，以符合儲存體和輸送量需求。 這項功能不需要裝載或設定路由伺服器。   

分割功能會以類似的方式自動增加容量，並據此重新平衡資料。 如需為您的資料選擇正確的分割區索引鍵的詳細資訊和建議，請參閱[選擇分割區索引鍵一文](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)。 

## <a name="index-your-data"></a><a id="indexing"></a>為資料編製索引
根據預設，Azure Cosmos DB 會針對插入的所有資料提供自動編制索引。 Azure Cosmos DB 所提供的編制索引功能包括新增複合索引、唯一索引和存留時間（TTL）索引。 索引管理介面會對應到`createIndex()`命令。 深入瞭解[Azure Cosmos DB 的 MONGODB API 中編制索引](mongodb-indexing.md)。

[Azure 資料庫移轉服務](../dms/tutorial-mongodb-cosmos-db.md)會自動遷移具有唯一索引的 MongoDB 集合。 不過，必須先建立唯一索引，然後再進行遷移。 當您的集合中已經有資料時，Azure Cosmos DB 不支援建立唯一索引。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的唯一索引鍵](unique-keys.md)。

## <a name="next-steps"></a>後續步驟
* [使用資料庫移轉服務，將 MongoDB 資料移轉至 Cosmos DB。](../dms/tutorial-mongodb-cosmos-db.md) 
* [在 Azure Cosmos 容器和資料庫上佈建輸送量](set-throughput.md)
* [Azure Cosmos DB 中的資料分割](partition-data.md)
* [Azure Cosmos DB 中的全域散發](distribute-data-globally.md)
* [Azure Cosmos DB 中的編製索引](index-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)
