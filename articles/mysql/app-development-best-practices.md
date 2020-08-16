---
title: 應用程式開發最佳做法-適用於 MySQL 的 Azure 資料庫
description: 瞭解使用適用於 MySQL 的 Azure 資料庫建立應用程式時的最佳作法
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 36f9cfa2369032351f6ed2948fc0c98c1648bcb6
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2020
ms.locfileid: "88259243"
---
# <a name="best-practices-for-building-applications-with-azure-database-for-mysql"></a>使用適用於 MySQL 的 Azure 資料庫建立應用程式的最佳作法 

以下是一些最佳作法，可協助您使用可縮短應用程式開發時間的適用於 MySQL 的 Azure 資料庫，建立雲端就緒的應用程式。 

## <a name="application-and-database-resource-configuration"></a>應用程式和資料庫資源設定

### <a name="application-and-database-in-the-same-region"></a>相同區域中的應用程式和資料庫
在 Azure 中部署應用程式時，請確定 **您的所有相依性都位於相同的區域**  。 將實例分散到不同區域或可用性區域會建立網路延遲，這可能會影響應用程式的整體效能。 

### <a name="keep-your-mysql-server-secure"></a>確保您的 MySQL 伺服器安全
您的 MySQL 伺服器應該設定為 [安全](https://docs.microsoft.com/azure/mysql/concepts-security) 且無法公開存取。 請使用下列其中一個選項來保護您的伺服器： 
- [防火牆規則](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) 或
- [虛擬網路](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) 或 
- [私人連結](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

基於安全性，您必須一律透過 **SSL** 連線到您的 mysql 伺服器，並將您的 mysql 伺服器和應用程式設定為使用 **tls 1.2**。 請參閱 [如何設定 SSL/TLS](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security)。 

### <a name="tune-your-server-parameters"></a>微調您的伺服器參數
針對大量讀取的工作負載，若要調整這些參數，' tmp_table_size 和 max_heap_table_size ' 可以協助優化以提升效能。 若要計算 tmp_table_size 和 max_heap_table_size 所需的值，請查看 [每個連接的記憶體] 值和 [基底記憶體]。 每個連接記憶體參數的總和，但不包括 tmp_table_size，並結合了伺服器總記憶體的基本記憶體帳戶。

若要計算 tmp_table_size 和 max_heap_table_size 的最大可能大小，請使用下列公式：

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> [記憶體總計] 表示伺服器在布建的虛擬核心中的總記憶體量。  例如，在一般用途 2 vCore 適用於 MySQL 的 Azure 資料庫伺服器中，記憶體總計會是 5GB * 2。  如需每個層級之記憶體的詳細資訊，請參閱 [定價層](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) 檔。
> [基底記憶體] 指出在伺服器啟動時，MySQL 將初始化和配置的記憶體變數，例如 query_cache_size 和 innodb_buffer_pool_size。  根據連接記憶體（例如 sort_buffer_size 和 join_buffer_size），只有在查詢需要時才會配置記憶體。

### <a name="create-a-non-admin-user"></a>建立非系統管理員使用者 
為每個資料庫[建立非系統管理員使用者](https://docs.microsoft.com/azure/mysql/howto-create-users)。 通常會將使用者名稱識別為資料庫名稱。

### <a name="resetting-your-password"></a>重設您的密碼
您可以使用 Azure 入口網站，為您的 MySQL 伺服器 [重設密碼](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) 。 

重設生產資料庫的伺服器密碼可能會讓您的應用程式關閉。 這是在離峰時間為任何生產環境工作負載重設密碼的良好模式，可將對應用程式使用者的影響降至最低。

## <a name="performance-and-resiliency"></a>效能和復原能力 
以下是一些工具和模式，可讓您用來協助您的應用程式的效能問題。

### <a name="enable-slow-query-logs-identify-performance-issues"></a>啟用緩慢查詢記錄以找出效能問題
您可以在伺服器上啟用 [慢速查詢記錄](https://docs.microsoft.com/azure/mysql/concepts-server-logs) 和 [審核記錄](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) 。 分析緩慢的查詢記錄有助於找出疑難排解的效能瓶頸。 

也可以透過 Azure 監視器記錄、事件中樞和儲存體帳戶中的 Azure 診斷記錄來取得 Audit 記錄。 請參閱 [如何針對查詢效能問題進行疑難排解](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)。

### <a name="use-connection-pooling"></a>使用連接共用
管理資料庫連接可能會對整體應用程式的效能造成重大影響。 為了優化效能，您必須減少建立連線的次數，以及在關鍵程式碼路徑中建立連接的時間。  使用 [連接](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) 共用來連接到適用於 MySQL 的 Azure 資料庫，以改善復原能力和效能。 

[ProxySQL](https://proxysql.com/)是連接共用器，可以有效率地用來管理連接。 使用連接共用器可以減少閒置的連線，並重複使用現有的連線，以避免發生問題。 如需深入瞭解，請參閱 [如何設定 ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) 。 

### <a name="retry-logic-to-handle-transient-errors"></a>處理暫時性錯誤的重試邏輯
您的應用程式可能會遇到 [暫時性錯誤](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) ，其中與資料庫的連接會間歇性地卸載或遺失。 在這種情況下，伺服器會在5-10 秒內啟動並執行一到兩次重試。 

要遵循重試的良好模式是在第一次重試前等待5秒，然後在每次重試之後，逐漸增加最多60秒的等待時間。 限制應用程式認為作業失敗的最大重試次數，讓您可以進一步調查。 如需深入瞭解，請參閱 [如何針對連接錯誤進行疑難排解](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) 。 

### <a name="enable-read-replication-to-mitigate-failovers"></a>啟用讀取複寫以減輕容錯移轉
您可以針對容錯移轉案例使用 [資料](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) 輸入複寫。 使用讀取複本時，主要與複本伺服器之間不會進行自動容錯移轉。 您會注意到主伺服器與複本之間的延遲，因為複寫是非同步。 網路延遲可能會受到許多因素的影響，例如在主伺服器上執行的工作負載有多長，以及資料中心之間的延遲。 在大部分的情況下，複本延遲的範圍是幾秒鐘到幾分鐘。

## <a name="database-deployment"></a>資料庫部署 

### <a name="configure-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>在您的 CI/CD 部署管線中設定適用于 MySQL 的 Azure 資料庫工作
有時候您會需要將變更部署到您的資料庫。 在這種情況下，您可以透過 [Azure 管線](https://azure.microsoft.com/services/devops/pipelines/) 建立使用持續整合 (CI) 和持續傳遞 (CD) ，並針對您的 [MySQL 伺服器](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) 執行自訂腳本，藉以更新資料庫。

### <a name="manual-database-deployment"></a>手動部署資料庫 
在手動部署資料庫期間，以下是將停機時間降到最低或降低部署失敗風險的良好模式： 

1. 使用[mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)或[MySQL 工作臺](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html)，在新的資料庫上建立生產資料庫的複本 
2. 使用您資料庫所需的新架構變更或更新來更新新的資料庫。 
3. 將生產資料庫放在唯讀狀態。 您不應該在生產資料庫上有寫入作業，直到部署完成為止。 
4. 使用步驟1中新更新的資料庫來測試您的應用程式。
5. 部署您的應用程式變更，並確定應用程式現在正在使用新的資料庫，其中包含最新的更新。 
6. 保留舊的生產資料庫，讓您可以復原變更。 接著，您可以評估以刪除舊的生產環境資料庫，或視需要將它匯出至 Azure 儲存體。 

>[!NOTE]
>  - 如果應用程式類似于電子商務應用程式，您可能無法將其置於唯讀狀態，請在進行備份之後，直接在生產資料庫上部署變更。  這些變更應該在離峰時段進行，而且應用程式的流量低，以 minimze 影響，因為某些使用者可能會遇到失敗的要求。 
>  - 請確定您的應用程式程式碼也會處理任何失敗的要求。

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>使用 MySQL 原生計量來查看您的工作負載是否超過記憶體中的臨時表大小
使用大量讀取工作負載時，對您的 MySQL 伺服器執行的查詢可能會超過記憶體中的臨時表大小。 這可能會導致您的伺服器切換到將臨時表寫入磁片，進而影響應用程式的效能。 若要判斷您的伺服器是否因超過臨時表大小而寫入磁片，請查看下列計量：

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
[Created_tmp_disk_tables] 計量會指出在磁片上建立的資料表數目，而 [created_tmp_table] 計量會告訴您，您的工作負載必須在記憶體中形成多少臨時表。 若要判斷是否執行特定查詢將會使用臨時表，請在查詢上執行說明。 如果查詢將使用臨時表執行，「額外」資料行中的細節會指出「使用暫時性」。

若要使用溢出至磁片的查詢來計算工作負載的百分比，請在下列公式中使用您的度量值：

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

在理想的情況下，此百分比應該少於25%。 如果您看到百分比為25% 或以上，建議您修改兩個伺服器參數，tmp_table_size 和 max_heap_table_si


## <a name="database-schema-and-queries"></a>資料庫架構和查詢

當您建立資料庫架構和查詢時，請記住以下幾個秘訣和訣竅。

### <a name="always-use-the-right-datatype-for-your--table-columns"></a>一律針對您的資料表資料行使用正確的 datatype
根據您想要儲存的資料類型使用正確的資料型別，可將儲存體優化，並減少因資料類型不正確而可能發生的任何錯誤。

### <a name="use-indexes"></a>使用索引
若要避免查詢速度緩慢，您可以使用索引。 索引可協助您快速找出具有特定資料行的資料列。 請參閱 [如何在 MySQL 中使用索引](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html)。

### <a name="explain-your-select-queries"></a>說明您的 SELECT 查詢
使用 [說明](https://dev.mysql.com/doc/refman/8.0/en/explain.html) 來取得 MySQL 執行查詢所執行之動作的深入解析。 這可協助您偵測到查詢的瓶頸或問題。 請參閱 [如何使用說明來分析查詢效能](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)。


