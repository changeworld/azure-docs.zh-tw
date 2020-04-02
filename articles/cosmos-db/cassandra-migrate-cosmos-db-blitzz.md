---
title: 使用 Blitzz 將資料從卡桑德拉遷移到 Azure 宇宙 DB Cassandra API
description: 瞭解如何使用 Blitzz 將數據從 Apache Cassandra 資料庫遷移到 Azure 宇宙 DB Cassandra API。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: b2e7f371e587c1c7f0debfa018ea8f25a30718a8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548099"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>使用 Blitzz 將資料從卡桑德拉遷移到 Azure 宇宙 DB Cassandra API 帳戶

Azure Cosmos DB 中的 Cassandra API 已成為在 Apache Cassandra 上執行的企業工作負載的絕佳選擇,原因有很多,例如: 

* **管理與監控沒有花費:** 它消除了跨作業系統、JVM 和 yaml 檔及其交互管理和監視無數設置的開銷。

* **顯著節約成本:** 您可以使用 Azure Cosmos DB 來節省成本,其中包括 VM 的成本、頻寬和任何適用的許可證。 此外,您不必管理數據中心、伺服器、SSD 存儲、網路和電力成本。 

* **能夠使用現有代碼與工具:** Azure Cosmos DB 提供與現有 Cassandra SDK 和工具的線協定級相容性。 此相容性可確保您可以透過 Azure Cosmos DB Cassandra API 使用現有程式碼基底執行瑣碎的變更。

可通過多種方式將資料庫工作負載從一個平臺遷移到另一個平臺。 [Blitzz](https://www.blitzz.io)是一種工具,它提供了一種安全可靠的方法,用於從各種資料庫到 Azure Cosmos DB 執行零停機時間遷移。 本文介紹了使用 Blitzz 將數據從 Apache Cassandra 資料庫遷移到 Azure 科斯科 DB Cassandra API 所需的步驟。

## <a name="benefits-using-blitzz-for-migration"></a>使用 Blitzz 進行遷移的好處

Blitzz 的遷移解決方案遵循一步一步的方法遷移複雜的操作工作負載。 以下是 Blitzz 零停機時間遷移計劃的一些關鍵方面:

* 它提供業務邏輯(表、索引、視圖)從 Apache Cassandra 資料庫到 Azure Cosmos DB 的自動遷移。 您不必手動創建架構。

* Blitzz 提供大容量和並行資料庫複製。 它通過使用一種稱為更改-數據捕獲 (CDC) 的技術,使源平臺和目標平臺在遷移期間能夠保持同步。 通過使用 CDC,Blitzz 不斷從源資料庫(Apache Cassandra)提取更改流並將其應用於目標資料庫(Azure Cosmos DB)。

* 它是容錯的,即使在系統中的硬體或軟體故障期間也能保證一次交付數據。

* 它使用 TLS、加密等多種安全方法在傳輸過程中保護數據。

## <a name="steps-to-migrate-data"></a>移轉資料的步驟

本節介紹設置 Blitzz 並將數據從 Apache Cassandra 資料庫遷移到 Azure Cosmos DB 所需的步驟。

1. 從計劃安裝 Blitzz 副本的電腦中,添加安全證書。 Blitzz 副本需要此證書才能與指定的 Azure Cosmos DB 帳戶建立 TLS 連接。 您可以使用以下步驟加入憑證:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. 你可以得到布利茨安裝和二進位檔案要麼通過請求在[布利茨網站上的](https://www.blitzz.io)演示。 或者,您也可以向團隊[傳送電子郵件](mailto:success@blitzz.io)。

   ![布利茨複製工具下載](./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![布利茨複製檔案](./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png)

1. 從 CLI 終端設定源資料庫配置。 使用**`vi conf/conn/cassandra.yml`** 命令打開設定檔,並添加卡桑德拉節點的 IP 位址、埠號、使用者名、密碼和任何其他必需詳細資訊的逗號分隔清單。 以下是設定檔中內容的範例:

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   ![開啟卡桑德拉連線編輯器](./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png)

   ![Cattconconcon](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png)

   填寫配置詳細資訊後,儲存並關閉該檔。

1. 可以選擇,您可以設置源資料庫篩選器檔。 篩選器檔指定要遷移的架構或表。 使用**`vi filter/cassandra_filter.yml`** 指令開啟設定檔並輸入以下設定詳細資訊:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   填寫資料庫篩選器詳細資訊後,保存並關閉該檔。

1. 接下來,您將設置目標資料庫配置。 在定義設定之前,[請建立 Azure Cosmos DB Cassandra API 帳戶](create-cassandra-dotnet.md#create-a-database-account),然後建立 Keyspace 和一個表來儲存遷移的資料。 由於您將從 Azure Cosmos DB 中的 Apache Cassandra 移至卡桑德拉 API,因此可以使用與 Apache cassandra 相同的分區密鑰。

1. 在遷移數據之前,將容器輸送量增加到應用程式快速遷移所需的量。 例如,您可以將輸送量增加到 100000 個 R。 在開始遷移之前縮放輸送量將説明您在更短的時間內遷移數據。

   ![在整個範圍內縮放 Azure 宇宙容器](./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png)

   遷移完成後降低輸送量。 根據每個操作所需的數據存儲量和 R,可以估計數據遷移後所需的輸送量。 要瞭解有關如何估計所需的 RU 的更多詳細資訊,請參閱[在容器和資料庫上預配吞吐量](set-throughput.md),[以及使用 Azure Cosmos DB 容量規劃器文章估計 RU/s。](estimate-ru-with-capacity-planner.md)

1. 從**連接字串**窗格取得 Azure Cosmos 帳號的**聯絡人點、連接埠、使用者名稱****與主密碼**。 您將在設定檔中使用這些值。

1. 從 CLI 終端設定目標資料庫配置。 使用**`vi conf/conn/cosmosdb.yml`** 命令打開設定檔,並添加主機 URI、埠號、使用者名、密碼和其他必需參數的逗號分隔清單。 下面的範例顯示設定檔的內容:

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. 接下來使用 Blitzz 遷移數據。 您可以在**完整**或**快照**模式下運行暴雪副本:

   * **完全模式**– 在此模式下,複製副本在遷移後繼續運行,並偵聽源 Apache Cassandra 系統上的任何更改。 如果它檢測到任何更改,它們將即時複製到目標 Azure Cosmos 帳戶上。

   * **快照模式**– 在此模式下,可以執行架構遷移和一次性數據複製。 此選項不支援即時複製。

   通過使用上述兩種模式,可以以零停機時間執行遷移。 

1. 要遷移資料,從 Blitzz 複製複本 CLI 終端執行以下命令:

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   複製 UI 顯示複製進度。 完成架構遷移和快照操作後,進度將顯示 100%。 遷移完成後,可以驗證目標 Azure Cosmos 資料庫上的數據。

   ![Catd](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png)


1. 由於已使用完整模式進行遷移,因此可以在源 Apache Cassandra 資料庫上執行插入、更新或刪除數據等操作。 稍後驗證它們是否在目標 Azure Cosmos 資料庫上即時複製。 遷移後,請確保減少為 Azure Cosmos 容器配置的輸送量。

1. 您可以停止複製的任何點,然後使用 **--resume**開關重新啟動它。 複製從停止點開始恢復,而不影響數據一致性。 以下命令演示如何使用恢復開關。

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

要瞭解有關資料移到目的地的即時移轉、即時遷移的更多內容,請參閱[Blitzz 複製演示](https://www.youtube.com/watch?v=fsUhF9LUZmM)。

## <a name="next-steps"></a>後續步驟

* [在容器和資料庫中佈建輸送量](set-throughput.md) 
* [分割區鍵最佳實作](partitioning-overview.md#choose-partitionkey)
* [使用 Azure 宇宙資料庫容量規劃器文章估計 RU/s](estimate-ru-with-capacity-planner.md)