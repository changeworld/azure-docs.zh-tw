---
title: 將您的資料遷移至 Azure Cosmos DB 中的 Cassandra API 帳戶 - 教學課程
description: 在此教學課程中，您將了解如何使用 CQL Copy 命令和 Spark，將資料從 Apache Cassandra 複製到 Azure Cosmos DB 中的 Cassandra API 帳戶。
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: b450d8463c09647b8f87547afe9d714c4b8659c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82858570"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>教學課程：將您的資料遷移至 Azure Cosmos DB 中的 Cassandra API 帳戶

身為開發人員，您可能已有在內部部署環境或雲端中執行的 Cassandra 工作負載，而且想將這些工作負載遷移到 Azure。 您可以將這類資料遷移至 Azure Cosmos DB 中的 Cassandra API 帳戶。 本教學課程會根據可用來將 Apache Cassandra 資料遷移至 Azure Cosmos DB 中 Cassandra API 帳戶的不同選項提供指示。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 規劃移轉
> * 進行移轉的必要條件
> * 使用 cqlsh COPY 命令來移轉資料
> * 使用 Spark 來移轉資料

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites-for-migration"></a>進行移轉的必要條件

* **估計輸送量需求：** 將資料遷移至 Azure Cosmos DB 中的 Cassandra API 帳戶之前，您應該預估工作負載的輸送量需求。 一般而言，建議您從 CRUD 作業所需的平均輸送量開始著手，然後再納入「擷取、轉換、載入」(ETL) 或棘手作業所需的額外輸送量。 您需要有下列詳細資料，才能為移轉進行規劃： 

  * **現有資料大小或預估資料大小：** 定義最基本的資料庫大小和輸送量需求。 如果您要為新應用程式預估資料大小，您可以假設資料平均分散至所有資料列，然後藉由乘以資料大小來預估值。 

  * **所需的輸送量：** 大約的讀取 (查詢/取得) 和寫入 (更新/刪除/插入) 輸送量比率。 必須要有此值，才能計算所需要求單位及穩定的狀態資料大小。  

  * **結構描述：** 透過 cqlsh 連線至您現有的 Cassandra 叢集，然後從 Cassandra 匯出結構描述： 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    在識別現有工作負載的需求之後，您應該根據所收集的輸送量需求，建立 Azure Cosmos 帳戶、資料庫及容器。  

  * **判斷作業的 RU 費用：** 您可以使用 Cassandra API 支援的任何 SDK 來判斷 RU。 此範例會示範取得 RU 費用的 .NET 版本。

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **配置所需的輸送量：** Azure Cosmos DB 可以依據您的需求成長，自動調整儲存體和輸送量。 您可以使用 [Azure Cosmos DB 要求單位計算機](https://www.documentdb.com/capacityplanner)來評估您的輸送量需求。 

* **在 Cassandra API 帳戶中建立資料表：** 開始遷移資料之前，請先從 Azure 入口網站或從 cqlsh 預先建立所有資料表。 如果您要遷移至具有資料庫層級輸送量的 Azure Cosmos 帳戶，請務必在建立 Azure Cosmos 容器時提供分割區索引鍵。

* **增加輸送量：** 資料移轉持續時間取決於您為 Azure Cosmos DB 中資料表佈建的輸送量多寡。 請針對移轉持續時間增加輸送量。 藉由較高的輸送量，您可以避免速率限制，並花費較少的時間進行移轉。 完成移轉之後，再降低輸送量以節省成本。 此外，也建議您使用相同區域中的 Azure Cosmos 帳戶作為來源資料庫。 

* **啟用 TLS：** Azure Cosmos DB 有嚴格的安全性需求和標準。 與您的帳戶互動時，請務必啟用 TLS。 當您透過 SSH 使用 CQL 時，會有選項要您提供 TLS 資訊。

## <a name="options-to-migrate-data"></a>移轉資料的選項

您可以使用下列選項，將資料從現有的 Cassandra 工作負載移至 Azure Cosmos DB：

* [使用 cqlsh COPY 命令](#migrate-data-using-cqlsh-copy-command)  
* [使用 Spark](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>使用 cqlsh COPY 命令來移轉資料

[CQL COPY 命令](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh)可用來將本機資料複製到 Azure Cosmos DB 中的 Cassandra API 帳戶。 請使用下列步驟來複製資料：

1. 取得您 Cassandra API 帳戶的連接字串資訊：

   * 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至您的 Azure Cosmos 帳戶。

   * 開啟 [連接字串]  窗格，其中包含從 cqlsh 連線至 Cassandra API 帳戶所需的一切資訊。

2. 使用入口網站中的連線資訊來登入 cqlsh。

3. 使用 CQL COPY 命令將本機資料複製到 Cassandra API 帳戶。

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>使用 Spark 來移轉資料 

搭配 Spark 使用下列步驟，將資料遷移至 Cassandra API 帳戶：

- 佈建一個 [Azure Databricks 叢集](cassandra-spark-databricks.md)或 [HDInsight 叢集](cassandra-spark-hdinsight.md) 

- 使用[資料表複製作業](cassandra-spark-table-copy-ops.md)將資料移至目的地 Cassandra API 端點 

如果您的資料位於 Azure 虛擬機器或任何其他雲端的現有叢集中，則建議使用 Spark 作業來遷移資料。 此選項需要將 Spark 設定為一次性或定期擷取的中繼項目。 您可以使用內部部署環境與 Azure 之間的 Azure ExpressRoute 連線，來加速這項移轉。 

## <a name="clean-up-resources"></a>清除資源

若不再需要資源，您可以刪除資源群組、Azure Cosmos 帳戶和所有相關資源。 請選取虛擬機器的資源群組，選取 [刪除]  ，然後確認要刪除的資源群組名稱，即可刪除資源。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何將資料遷移至 Azure Cosmos DB 中的 Cassandra API 帳戶。 您現在可以繼續進行下列文章，了解 Azure Cosmos DB 的其他概念：

> [!div class="nextstepaction"]
> [Azure Cosmos DB 中的 Tunable 資料一致性層級](../cosmos-db/consistency-levels.md)


