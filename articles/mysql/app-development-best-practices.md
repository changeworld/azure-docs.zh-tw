---
title: 應用程式開發最佳做法-適用於 MySQL 的 Azure 資料庫
description: 瞭解使用適用於 MySQL 的 Azure 資料庫建立應用程式的最佳做法。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: dc9764ce68d54418578c293833c1fd38080ba0ef
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91538903"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>使用適用於 MySQL 的 Azure 資料庫建立應用程式的最佳作法 

以下是一些最佳作法，可協助您使用適用於 MySQL 的 Azure 資料庫建立雲端就緒的應用程式。 這些最佳作法可以減少應用程式的開發時間。 

## <a name="configuration-of-application-and-database-resources"></a>應用程式和資料庫資源的設定

### <a name="keep-the-application-and-database-in-the-same-region"></a>將應用程式和資料庫保留在相同區域
在 Azure 中部署應用程式時，請確定您所有的相依性都在相同的區域中。 分配跨區域或可用性區域的實例會建立網路延遲，這可能會影響應用程式的整體效能。 

### <a name="keep-your-mysql-server-secure"></a>保護您的 MySQL 伺服器
將您的 MySQL 伺服器設定為 [安全](https://docs.microsoft.com/azure/mysql/concepts-security) 且無法公開存取。 您可以使用下列其中一個選項來保護您的伺服器： 
- [防火牆規則](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)
- [虛擬網路](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) 
- [Azure Private Link](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

基於安全性，您必須一律透過 SSL 連線到您的 MySQL 伺服器，並將您的 MySQL 伺服器和應用程式設定為使用 TLS 1.2。 請參閱 [如何設定 SSL/TLS](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security)。 

### <a name="tune-your-server-parameters"></a>微調您的伺服器參數
針對大量讀取的工作負載，微調伺服器參數， `tmp_table_size` 並 `max_heap_table_size` 可協助優化以提升效能。 若要計算這些變數所需的值，請查看每個連接記憶體值和基本記憶體的總計。 每個連接記憶體參數的總和，但不包括 `tmp_table_size` ，結合伺服器總記憶體的基本記憶體帳戶。

若要計算和的最大可能大小 `tmp_table_size` `max_heap_table_size` ，請使用下列公式：

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> 記憶體總計表示伺服器在已布建的虛擬核心上的總記憶體量。  例如，在一般用途的雙 vCore 適用於 MySQL 的 Azure 資料庫伺服器中，總記憶體會是 5 GB * 2。 您可以在 [定價層](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) 檔中找到每個階層之記憶體的更多詳細資料。
>
> 基底記憶體表示 `query_cache_size` `innodb_buffer_pool_size` MySQL 將在伺服器啟動時初始化和配置的記憶體變數，例如和。 每個連接的記憶體（例如 `sort_buffer_size` 和 `join_buffer_size` ）都是只有在查詢需要時才會配置的記憶體。

### <a name="create-non-admin-users"></a>建立非系統管理員使用者 
為每個資料庫[建立非系統管理員使用者](https://docs.microsoft.com/azure/mysql/howto-create-users)。 一般而言，會將使用者名稱識別為資料庫名稱。

### <a name="reset-your-password"></a>重設密碼
您可以使用 Azure 入口網站為您的 MySQL 伺服器 [重設密碼](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) 。 

重設生產資料庫的伺服器密碼可能會使您的應用程式停止運作。 在離峰時間為任何生產工作負載重設密碼是很好的作法，將對應用程式使用者的影響降到最低。

## <a name="performance-and-resiliency"></a>效能和復原能力 
以下是一些您可以用來協助您偵測應用程式效能問題的工具和做法。

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>啟用慢速查詢記錄以找出效能問題
您可以在伺服器上啟用 [慢速查詢記錄](https://docs.microsoft.com/azure/mysql/concepts-server-logs) 和 [審核記錄](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) 。 分析慢速查詢記錄有助於找出效能瓶頸以進行疑難排解。 

您也可以透過 Azure 監視器記錄、Azure 事件中樞和儲存體帳戶中的 Azure 診斷記錄來取得 Audit 記錄檔。 請參閱 [如何針對查詢效能問題進行疑難排解](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)。

### <a name="use-connection-pooling"></a>使用連接共用
管理資料庫連接可能會對整個應用程式的效能造成重大影響。 若要將效能優化，您必須減少建立連線的次數，以及在主要程式碼路徑中建立連接的時間。 使用 [連接](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) 共用連接到適用於 MySQL 的 Azure 資料庫，以改善復原能力和效能。 

您可以使用 [ProxySQL](https://proxysql.com/) 連接共用器有效率地管理連接。 使用連接共用器可能會減少閒置的連線，並重複使用現有的連接，這有助於避免問題。 請參閱 [如何設定 ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) 以深入瞭解。 

### <a name="retry-logic-to-handle-transient-errors"></a>處理暫時性錯誤的重試邏輯
您的應用程式可能會遇到 [暫時性的錯誤](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) ，也就是資料庫連接中斷或中斷的情況。 在這種情況下，伺服器會在5到10秒內啟動並執行兩次重試。 

理想的作法是在第一次重試前等待5秒。 然後以逐漸增加的等候次數（最多60秒），以每次重試的方式執行。 限制應用程式認為作業失敗的最大重試次數，讓您可以進一步調查。 若要深入瞭解，請參閱 [如何疑難排解連接錯誤](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) 。 

### <a name="enable-read-replication-to-mitigate-failovers"></a>啟用讀取複寫以減少容錯移轉
您可以使用容錯移轉案例的 [資料輸入複寫](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) 。 當您使用讀取複本時，不會發生來源與複本伺服器之間的自動容錯移轉。 

您會發現來源與複本之間的延遲，因為複寫是非同步。 網路延遲可能會受到許多因素影響，例如在來源伺服器上執行的工作負載大小，以及資料中心之間的延遲。 在大部分的情況下，複本延遲範圍從幾秒鐘到幾分鐘。

## <a name="database-deployment"></a>資料庫部署 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>在您的 CI/CD 部署管線中設定適用于 MySQL 的 Azure 資料庫工作
有時候，您需要將變更部署至您的資料庫。 在這種情況下，您可以透過 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) 使用持續整合 (CI) 和持續傳遞 (CD) ，並針對 [MySQL 伺服器](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) 執行自訂腳本來更新資料庫。

### <a name="use-an-effective-process-for-manual-database-deployment"></a>使用手動資料庫部署的有效流程 
在手動部署資料庫時，請遵循下列步驟來將停機時間降到最低，或降低部署失敗的風險： 

1. 使用 [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) 或 [MySQL 工作臺](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html)，在新的資料庫上建立生產資料庫的複本。 
2. 以新的架構變更或資料庫所需的更新，更新新的資料庫。 
3. 讓生產資料庫處於唯讀狀態。 在部署完成之前，您不應該在生產資料庫上有寫入作業。 
4. 使用步驟1中新更新的資料庫測試您的應用程式。
5. 部署您的應用程式變更，並確定應用程式現在使用新的資料庫，其中包含最新的更新。 
6. 保留舊的生產資料庫，讓您可以復原變更。 然後，您可以評估為刪除舊的生產資料庫，或視需要將它匯出至 Azure 儲存體。 

>[!NOTE]
>如果應用程式與電子商務應用程式類似，而且您無法將其設為唯讀狀態，請在進行備份之後，直接在生產資料庫上部署變更。 這些變更應該會在離峰時段發生，並將流量降至最低，以將影響降至最低，因為某些使用者可能會遇到失敗的要求。 
>
>請確定您的應用程式程式碼也會處理任何失敗的要求。

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>使用 MySQL 原生計量來查看您的工作負載是否超過記憶體內部臨時表大小
如果有大量讀取的工作負載，針對您的 MySQL 伺服器執行的查詢可能會超過記憶體內部臨時表大小。 大量讀取的工作負載可能會導致您的伺服器切換成將臨時表寫入至磁片，而這會影響應用程式的效能。 若要判斷您的伺服器是否因為超過臨時表大小而寫入磁片，請查看下列計量：

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
`created_tmp_disk_tables`度量指出磁片上建立的資料表數目。 此計量會 `created_tmp_table` 告知您有多少臨時表必須在記憶體中形成（假設您的工作負載）。 若要判斷執行特定查詢是否會使用臨時表，請在查詢上執行 [解釋](https://dev.mysql.com/doc/refman/8.0/en/explain.html) 語句。 資料行中的詳細資料 `extra` `Using temporary` 會指出是否要使用臨時表來執行查詢。

若要使用溢出至磁片的查詢來計算工作負載的百分比，請在下列公式中使用您的度量值：

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

在理想情況下，此百分比應少於25%。 如果您看到百分比是25% 或更高，建議您修改兩個伺服器參數，tmp_table_size 和 max_heap_table_size。

## <a name="database-schema-and-queries"></a>資料庫架構和查詢

當您建立資料庫架構和查詢時，請記住以下幾個秘訣。

### <a name="use-the-right-datatype-for-your-table-columns"></a>針對資料表資料行使用正確的資料類型
根據您想要儲存的資料類型使用正確的資料類型，可以將儲存體優化，並減少因資料類型不正確而可能發生的錯誤。

### <a name="use-indexes"></a>使用索引
若要避免查詢速度變慢，您可以使用索引。 索引可協助您快速尋找具有特定資料行的資料列。 瞭解 [如何使用 MySQL 中的索引](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html)。

### <a name="use-explain-for-your-select-queries"></a>使用您的 SELECT 查詢說明
您 `EXPLAIN` 可以使用語句來取得 MySQL 執行查詢所執行工作的見解。 它可協助您偵測查詢的瓶頸或問題。 瞭解 [如何使用說明來分析查詢效能](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)。


