---
title: MySQL server 操作最佳做法-適用於 MySQL 的 Azure 資料庫
description: 本文說明在 Azure 上操作 MySQL 資料庫的最佳作法。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354978"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>適用於 MySQL 的 Azure 資料庫單一伺服器上伺服器作業的最佳作法

瞭解使用適用於 MySQL 的 Azure 資料庫的最佳做法。 當我們將新功能新增至平臺時，我們會繼續專注于調整本節中所述的最佳作法。

## <a name="azure-database-for-mysql-operational-guidelines"></a>適用於 MySQL 的 Azure 資料庫操作指導方針 

以下是使用您的適用於 MySQL 的 Azure 資料庫來改善資料庫效能時，應遵循的操作指導方針： 

* **共** 置：若要減少網路延遲，請將用戶端和資料庫伺服器放在相同的 Azure 區域中。

* **監視您的記憶體、CPU 和儲存體使用量**：您可以 [設定警示](howto-alert-on-metric.md) ，以在使用模式變更時通知您，或在您接近部署的容量時通知您，如此您就可以維持系統效能和可用性。 

* **擴大您的 DB 實例**：當您接近儲存體容量限制時，您可以 [擴大](howto-create-manage-server-portal.md) 。 您應該會在儲存體和記憶體中有一些緩衝區，以因應您應用程式的需求增加。 您也可以 [啟用](howto-auto-grow-storage-portal.md) 「開啟」「儲存體自動成長」功能，以確保服務會在接近儲存體限制時自動調整儲存體。 

* **設定備份**：根據業務需求啟用 [本機或地理位置多餘的備份](howto-restore-server-portal.md#set-backup-configuration) 。 此外，您也可以修改備份可用於商務持續性的保留期限。 

* **增加 i/o 容量**：如果您的資料庫工作負載所需的 i/o 比您布建的還多，您資料庫的復原或其他交易式作業將會變慢。 若要增加伺服器實例的 i/o 容量，請執行下列任何或所有動作： 

    * 適用于 MySQL 的 Azure 資料庫以布建的每 GB 儲存體三個 IOPS 速率提供 IOPS 調整。 [增加布建的儲存體](howto-create-manage-server-portal.md#scale-storage-up) 以調整 IOPS，以獲得更好的效能。 

    * 如果您已經在使用布建的 IOPS 儲存體，請布建 [額外的輸送量容量](howto-create-manage-server-portal.md#scale-storage-up)。 

* **調整計算**：資料庫工作負載也可能因為 CPU 或記憶體而受到限制，這可能會對交易處理造成嚴重的影響。 請注意，計算 (定價層) 可以在 [一般用途或記憶體優化](concepts-pricing-tiers.md) 層之間向上或向下調整。 

* **測試容錯移轉**：手動測試您的伺服器實例的容錯移轉，以瞭解程式在使用案例中所花費的時間，並確保存取伺服器實例的應用程式可以在容錯移轉之後自動連接到新的伺服器實例。

* **使用主鍵**：當您操作適用於 MySQL 的 Azure 資料庫時，請確定您的資料表具有主要或唯一索引鍵。 這有助於大量備份、複本等，並提升效能。

* 設定 **TTL 值**：如果您的用戶端應用程式要快取功能變數名稱服務 (DNS) 伺服器實例的資料，請將存留時間 (TTL) 值設定為小於30秒。 因為伺服器實例的基礎 IP 位址可能會在容錯移轉之後變更，所以如果您的應用程式嘗試連線至不再處於服務中的 IP 位址，則快取 DNS 資料可能會導致連線失敗。

* 使用連接共用，以避免 [達到最大連線限制](concepts-server-parameters.md#max_connections)，並使用重試邏輯來避免間歇性的連接問題。 

* 如果您使用的是複本，請使用 [ProxySQL 來平衡](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) 主伺服器與可讀取次要複本伺服器之間的負載。 請參閱這裡的設定步驟。 </br> 

* 布建資源時，請確定您 [已啟用適用於 MySQL 的 Azure 資料庫的](howto-auto-grow-storage-portal.md) 自動成長。 這不會增加任何額外成本，而且會保護資料庫免于您可能遇到的任何儲存體瓶頸。 </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>使用 InnoDB 搭配適用於 MySQL 的 Azure 資料庫

*   如果使用 `ibdata1` 功能（也就是系統資料表空間資料檔），則無法藉由卸載資料表中的資料來壓縮或清除，或將資料表移至每個資料表的每個檔 `tablespaces` 。

* 針對大小大於 1 TB 的資料庫，您應該在 **innodb_file_per_table** 中建立資料表 `tablespace` 。 針對大小大於 1 TB 的單一資料表，您應該要有資料 [分割](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) 資料表。

*   對於具有大量的伺服器 `tablespace` ，引擎啟動將會很慢，因為 MySQL 啟動或容錯移轉期間的連續表資料表掃描。 

* 如果資料表總數小於500，則在建立資料表之前，請先設定 innodb_file_per_table = ON。

* 如果您的資料庫中有500個以上的資料表，則請檢查每個個別資料表的資料表大小。 針對大型資料表，您仍然應該考慮使用每個資料表的資料表空間，以避免系統資料表空間的檔案達到最大儲存空間限制。

> [!NOTE]
> 針對大小小於5GB 的資料表，請考慮使用系統資料表空間 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* 如果您有非常大的資料表，可能會成長超過 1 TB，請在資料表建立時[分割](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html)資料表。

* 使用多個 MySQL 伺服器，並將資料表分散到這些伺服器。 如果您有大約10000個數據表或更多，請避免在單一伺服器上放入太多資料表。 

## <a name="next-steps"></a>後續步驟
- [適用於 MySQL 的 Azure 資料庫效能的最佳作法](concept-performance-best-practices.md)
- [監視適用於 MySQL 的 Azure 資料庫的最佳做法](concept-monitoring-best-practices.md)
