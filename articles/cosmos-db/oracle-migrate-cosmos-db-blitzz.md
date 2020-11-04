---
title: 使用 Blitzz 將將資料從 Oracle 遷移至 Azure Cosmos DB Cassandra API
description: 瞭解如何使用 Blitzz 將將資料從 Oracle 資料庫移轉至 Azure Cosmos DB Cassandra API。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 15bcd0c54fc5f6614f4d1925759704309048acae
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336434"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>使用 Blitzz 將將資料從 Oracle 遷移至 Azure Cosmos DB Cassandra API 帳戶
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB 中的 Cassandra API 已成為在 Oracle 上執行之企業工作負載的絕佳選擇，原因如下：

* **更好的擴充性和可用性：** 它可消除單一失敗點、更好的擴充性，以及應用程式的可用性。

* **大幅節省成本：** 您可以使用 Azure Cosmos DB 來節省成本，包括 VM 的成本、頻寬和任何適用的 Oracle 授權。 此外，您不需要管理資料中心、伺服器、SSD 儲存體、網路和電力成本。

* **管理和監視沒有額外負荷：** 做為完全受控的雲端服務，Azure Cosmos DB 消除了管理和監視無數設定的額外負荷。

有多種方式可將資料庫工作負載從某個平臺遷移至另一個平臺。 [Blitzz 將](https://www.blitzz.io) 是一種工具，可提供安全且可靠的方式，從各種不同的資料庫執行零停機時間遷移至 Azure Cosmos DB。 本文說明使用 Blitzz 將將資料從 Oracle 資料庫移轉至 Azure Cosmos DB Cassandra API 所需的步驟。

## <a name="benefits-using-blitzz-for-migration"></a>使用 Blitzz 將進行遷移的優點

Blitzz 將的遷移解決方案遵循逐步方法來遷移複雜的操作工作負載。 以下是 Blitzz 將的零停機時間遷移計畫的一些重要層面：

* 它可自動將商務邏輯（ (資料表、索引、從 Oracle 資料庫) 的視圖）遷移至 Azure Cosmos DB。 您不必手動建立架構。

* Blitzz 將提供大量和平行資料庫複寫。 它可讓來源和目標平臺在遷移期間，使用稱為變更資料-捕獲 (CDC) 的技術進行同步處理。 藉由使用 CDC，Blitzz 將會持續從源資料庫提取 (Oracle) 的變更資料流程，並將其套用至目的地資料庫 (Azure Cosmos DB) 。

* 即使在系統中的硬體或軟體失敗時，它仍可容錯且保證只會一次傳遞資料。

* 它會使用各種安全性方法（例如，TLS/SSL）加密，來保護傳輸期間的資料。

* 它提供的服務可將 PL/SQL 中撰寫的複雜商務邏輯轉換為 Azure Cosmos DB 中的相同商務邏輯。

## <a name="steps-to-migrate-data"></a>遷移資料的步驟

本節說明設定 Blitzz 將以及將資料從 Oracle 資料庫移轉至 Azure Cosmos DB 所需的步驟。

1. 從您計畫安裝 Blitzz 將 replicant 的電腦上，新增安全性憑證。 Blitzz 將 replicant 需要此憑證，才能建立具有指定 Azure Cosmos DB 帳戶的 TLS 連接。 您可以使用下列步驟來新增憑證：

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. ou 可以在 [blitzz 將網站](https://www.blitzz.io)上要求示範，以取得 blitzz 將安裝和二進位檔案。 或者，您也可以傳送 [電子郵件](mailto:success@blitzz.io) 給小組。

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Blitzz 將 replicant tool 下載":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Blitzz 將 replicant 檔":::

1. 從 CLI 終端機，設定源資料庫設定。 使用命令開啟設定檔， **`vi conf/conn/oracle.yml`** 然後新增 oracle 節點的 IP 位址清單（以逗號分隔）、埠號碼、使用者名稱、密碼，以及任何其他必要的詳細資料。 下列程式碼顯示範例設定檔案：

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png" alt-text="開啟 Oracle 連接編輯器":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png" alt-text="Oracle 連接設定":::

   填寫設定詳細資料之後，請儲存並關閉檔案。

1. （選擇性）您可以設定源資料庫篩選檔案。 篩選檔案會指定要遷移的架構或資料表。 使用命令開啟設定檔 **`vi filter/oracle_filter.yml`** ，然後輸入下列設定詳細資料：

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   填寫資料庫篩選詳細資料之後，請儲存並關閉檔案。

1. 接下來，您將設定目的地資料庫的設定。 在定義設定之前，請先 [建立 Azure Cosmos DB Cassandra API 帳戶](create-cassandra-dotnet.md#create-a-database-account)。 從您的資料中[選擇正確的資料分割索引鍵](partitioning-overview.md#choose-partitionkey)，然後建立 Keyspace 和資料表來儲存遷移的資料。

1. 在遷移資料之前，請將容器輸送量增加到您的應用程式快速遷移所需的數量。 例如，您可以將輸送量增加到 100000 ru。 在開始遷移之前調整輸送量，可協助您以較短的時間遷移資料。 

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="整個調整 Azure Cosmos 容器":::

   完成遷移之後，您必須降低輸送量。 根據每項作業所需的儲存資料量和 ru 數目，您可以估計資料移轉之後所需的輸送量。 若要深入瞭解如何估計所需的 RU，請參閱使用 Azure Cosmos DB 容量規劃文章，在 [容器和資料庫上](set-throughput.md) 布建輸送量，並 [預估 RU/秒](estimate-ru-with-capacity-planner.md) 。

1. 從 [ **連接字串** ] 窗格取得 Azure Cosmos 帳戶的 **連絡人點、埠、使用者名稱** 和 **主要密碼** 。 您將在設定檔中使用這些值。

1. 從 CLI 終端機，設定目的地資料庫設定。 使用命令開啟設定檔， **`vi conf/conn/cosmosdb.yml`** 並新增主機 URI、埠號碼、使用者名稱、密碼及其他必要參數的逗號分隔清單。 以下是設定檔中的內容範例：

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. 接下來，使用 Blitzz 將來遷移資料。 您可以在 **完整** 或 **快照** 模式中執行 Blizz replicant：

   * **完整模式** –在此模式中，replicant 會在遷移之後繼續執行，並在來源 Oracle 系統上接聽任何變更。 如果它偵測到任何變更，系統會即時複寫到目標 Azure Cosmos 帳戶。

   * **快照模式** –在此模式中，您可以執行架構遷移和單次資料複寫。 此選項不支援即時複寫。


   藉由使用上述兩種模式，可執行遷移，而不需要停機。

1. 若要遷移資料，請從 Blitzz 將 replicant CLI 終端機執行下列命令：

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   Replicant UI 會顯示覆寫進度。 完成架構遷移和快照集作業之後，進度會顯示100%。 完成遷移之後，您可以驗證目標 Azure Cosmos 資料庫上的資料。

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png" alt-text="Oracle 資料移轉輸出":::

1. 因為您已使用完整模式來進行遷移，所以您可以執行作業，例如在來源 Oracle 資料庫上插入、更新或刪除資料。 稍後您可以驗證它們是否會在目標 Azure Cosmos 資料庫上即時複寫。 遷移之後，請務必減少為 Azure Cosmos 容器設定的輸送量。

1. 您可以停止 replicant 任何點，並使用 **--resume** 參數將它重新開機。 複寫會從它停止的點繼續，而不會影響資料一致性。 下列命令顯示如何使用 resume 參數。

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

若要深入瞭解資料移轉至目的地、即時移轉的詳細資訊，請參閱 [blitzz 將 replicant 示範](https://www.youtube.com/watch?v=y5ZeRK5A-MI)。

## <a name="next-steps"></a>後續步驟

* [在容器和資料庫中佈建輸送量](set-throughput.md) 
* [分割區索引鍵的最佳作法](partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB 容量規劃文章來預估 RU/秒](estimate-ru-with-capacity-planner.md)
