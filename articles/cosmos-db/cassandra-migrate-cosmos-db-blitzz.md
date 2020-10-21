---
title: 使用 Blitzz 將將資料從 Cassandra 遷移至 Azure Cosmos DB Cassandra API
description: 瞭解如何使用 Blitzz 將將資料從 Apache Cassandra 資料庫移轉至 Azure Cosmos DB Cassandra API。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 66314155a8de5036009b8e42bf84a8ae8860d0ea
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278947"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>使用 Blitzz 將將資料從 Cassandra 遷移至 Azure Cosmos DB Cassandra API 帳戶

Azure Cosmos DB 中的 Cassandra API 已成為在 Apache Cassandra 上執行之企業工作負載的絕佳選擇，原因如下： 

* **管理和監視沒有額外負荷：** 它消除了管理和監視各種作業系統、JVM 和 yaml 檔案與其互動的設定時，會產生的額外負荷。

* **大幅節省成本：** 您可以使用 Azure Cosmos DB 來節省成本，包括 VM 的成本、頻寬和任何適用的授權。 此外，您不需要管理資料中心、伺服器、SSD 儲存體、網路和電力成本。 

* **使用現有程式碼和工具的能力：** Azure Cosmos DB 提供與現有 Cassandra Sdk 和工具的有線通訊協定層級相容性。 此相容性可確保您可以透過 Azure Cosmos DB Cassandra API 使用現有程式碼基底執行瑣碎的變更。

有多種方式可將資料庫工作負載從某個平臺遷移至另一個平臺。 [Blitzz 將](https://www.blitzz.io) 是一種工具，可提供安全且可靠的方式，從各種不同的資料庫執行零停機時間遷移至 Azure Cosmos DB。 本文說明使用 Blitzz 將將資料從 Apache Cassandra 資料庫移轉至 Azure Cosmos DB Cassandra API 所需的步驟。

## <a name="benefits-using-blitzz-for-migration"></a>使用 Blitzz 將進行遷移的優點

Blitzz 將的遷移解決方案遵循逐步方法來遷移複雜的操作工作負載。 以下是 Blitzz 將的零停機時間遷移計畫的一些重要層面：

* 它可自動將商務邏輯（ (資料表、索引、從 Apache Cassandra 資料庫) 的視圖）遷移至 Azure Cosmos DB。 您不必手動建立架構。

* Blitzz 將提供大量和平行資料庫複寫。 它可讓來源和目標平臺在遷移期間，使用稱為變更資料-捕獲 (CDC) 的技術進行同步處理。 藉由使用 CDC，Blitzz 將會持續從源資料庫提取 (Apache Cassandra) 的變更串流，並將其套用至目的地資料庫 (Azure Cosmos DB) 。

* 它具有容錯能力，即使在系統中的硬體或軟體失敗時，也只會提供一次資料傳遞。

* 它會使用各種安全性方法（例如 TLS、加密）來保護傳輸期間的資料。

## <a name="steps-to-migrate-data"></a>遷移資料的步驟

本節說明設定 Blitzz 將，並將資料從 Apache Cassandra 資料庫移轉至 Azure Cosmos DB 所需的步驟。

1. 從您計畫安裝 Blitzz 將 replicant 的電腦上，新增安全性憑證。 Blitzz 將 replicant 需要此憑證，才能建立具有指定 Azure Cosmos DB 帳戶的 TLS 連接。 您可以使用下列步驟來新增憑證：

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. 您可以在 [blitzz 將網站](https://www.blitzz.io)上要求示範，以取得 blitzz 將安裝和二進位檔案。 或者，您也可以傳送 [電子郵件](mailto:success@blitzz.io) 給小組。

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Blitzz 將 replicant tool 下載":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Blitzz 將 replicant tool 下載":::

1. 從 CLI 終端機，設定源資料庫設定。 使用命令開啟設定檔， **`vi conf/conn/cassandra.yml`** 並新增 Cassandra 節點的 IP 位址清單（以逗號分隔）、埠號碼、使用者名稱、密碼，以及任何其他必要的詳細資料。 以下是設定檔中的內容範例：

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png" alt-text="Blitzz 將 replicant tool 下載":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png" alt-text="Blitzz 將 replicant tool 下載":::

   填寫設定詳細資料之後，請儲存並關閉檔案。

1. （選擇性）您可以設定源資料庫篩選檔案。 篩選檔案會指定要遷移的架構或資料表。 使用命令開啟設定檔 **`vi filter/cassandra_filter.yml`** ，然後輸入下列設定詳細資料：

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   填寫資料庫篩選詳細資料之後，請儲存並關閉檔案。

1. 接下來，您將設定目的地資料庫設定。 在定義設定之前，請先 [建立 Azure Cosmos DB Cassandra API 帳戶](create-cassandra-dotnet.md#create-a-database-account) ，然後建立 Keyspace 和資料表來儲存遷移的資料。 因為您是從 Apache Cassandra 遷移至 Azure Cosmos DB 中的 Cassandra API，所以您可以使用與 Apache Cassandra 搭配使用的相同分割區索引鍵。

1. 在遷移資料之前，請將容器輸送量增加到您的應用程式快速遷移所需的數量。 例如，您可以將輸送量增加到 100000 ru。 在開始遷移之前調整輸送量，可協助您以較短的時間遷移資料。

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="Blitzz 將 replicant tool 下載":::

   完成遷移之後，請降低輸送量。 根據每項作業所需的儲存資料量和 ru 數目，您可以估計資料移轉之後所需的輸送量。 若要深入瞭解如何估計所需的 RU，請參閱使用 Azure Cosmos DB 容量規劃文章，在 [容器和資料庫上](set-throughput.md) 布建輸送量，並 [預估 RU/秒](estimate-ru-with-capacity-planner.md) 。

1. 從 [**連接字串**] 窗格取得 Azure Cosmos 帳戶的**連絡人點、埠、使用者名稱**和**主要密碼**。 您將在設定檔中使用這些值。

1. 從 CLI 終端機，設定目的地資料庫設定。 使用命令開啟設定檔， **`vi conf/conn/cosmosdb.yml`** 並新增主機 URI、埠號碼、使用者名稱、密碼及其他必要參數的逗號分隔清單。 下列範例顯示設定檔的內容：

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. 接下來，使用 Blitzz 將來遷移資料。 您可以在 **完整** 或 **快照** 模式中執行 Blizz replicant：

   * **完整模式** –在此模式中，replicant 會在遷移之後繼續執行，並在來源 Apache Cassandra 系統上接聽任何變更。 如果它偵測到任何變更，系統會即時複寫到目標 Azure Cosmos 帳戶。

   * **快照模式** –在此模式中，您可以執行架構遷移和單次資料複寫。 此選項不支援即時複寫。

   藉由使用上述兩種模式，可執行遷移，而不需要停機。 

1. 若要遷移資料，請從 Blitzz 將 replicant CLI 終端機執行下列命令：

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   Replicant UI 會顯示覆寫進度。 完成架構遷移和快照集作業之後，進度會顯示100%。 完成遷移之後，您可以驗證目標 Azure Cosmos 資料庫上的資料。

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png" alt-text="Blitzz 將 replicant tool 下載":::


1. 因為您已使用完整模式來進行遷移，所以您可以執行作業，例如插入、更新或刪除來源 Apache Cassandra 資料庫上的資料。 稍後會驗證它們是否已在目標 Azure Cosmos 資料庫上即時複寫。 遷移之後，請務必減少為 Azure Cosmos 容器設定的輸送量。

1. 您可以停止 replicant 任何點，並使用 **--resume** 參數將它重新開機。 複寫會從它停止的點繼續，而不會影響資料一致性。 下列命令顯示如何使用 resume 參數。

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

若要深入瞭解資料移轉至目的地、即時移轉的詳細資訊，請參閱 [blitzz 將 replicant 示範](https://www.youtube.com/watch?v=fsUhF9LUZmM)。

## <a name="next-steps"></a>後續步驟

* [在容器和資料庫中佈建輸送量](set-throughput.md) 
* [分割區索引鍵的最佳作法](partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB 容量規劃文章來預估 RU/秒](estimate-ru-with-capacity-planner.md)
