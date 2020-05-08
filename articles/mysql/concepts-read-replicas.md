---
title: 讀取複本-適用於 MySQL 的 Azure 資料庫。
description: 瞭解適用於 MySQL 的 Azure 資料庫中的讀取複本：選擇區域、建立複本、連接到複本、監視複寫，以及停止複寫。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/4/2020
ms.openlocfilehash: cb82b3223d50c66b4d6c176a274d5ccf8d510911
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792100"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>讀取「適用於 MySQL 的 Azure 資料庫」中的複本

讀取複本功能可讓您將資料從適用於 MySQL 的 Azure 資料庫伺服器複寫到唯讀伺服器。 您可以從主要伺服器複寫到最多五個複本。 複本會使用 MySQL 引擎的原生二進位記錄 (binlog) 檔案位置型複寫技術來進行非同步更新。 若要深入了解 binlog 複寫，請參閱 [MySQL binlog 複寫概觀](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) \(英文\)。

複本是您所管理的新伺服器，類似于一般適用於 MySQL 的 Azure 資料庫伺服器。 針對每個讀取複本，系統每月會針對在虛擬核心中所佈建的計算量，以及在儲存體中所佈建的容量 (以 GB 為單位) 向您收費。

若要深入瞭解 MySQL 複寫功能和問題，請參閱[mysql 複寫檔](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)。

## <a name="when-to-use-a-read-replica"></a>何時應該使用讀取複本

讀取複本功能可針對需大量讀取的工作負載，協助改善效能及調整能力。 讀取工作負載可隔離到複本，而寫入工作負載可以導向到主要伺服器。

常見的案例是讓 BI 與分析工作負載使用讀取複本做為報告的資料來源。

由於複本是唯讀狀態，因此不會直接降低主要伺服器上的寫入容量負擔。 這項功能不是以寫入密集的工作負載為目標。

讀取複本功能會使用 MySQL 非同步複寫。 此功能不適用於同步複寫案例。 主要伺服器和複本之間將會有顯著的延遲。 複本上的資料最終仍會與主要伺服器上的資料保持一致。 請針對可接受此延遲的工作負載使用此功能。

## <a name="cross-region-replication"></a>跨區域複寫
您可以從主伺服器在不同的區域中建立讀取複本。 跨區域複寫適用于嚴重損壞修復計畫之類的案例，或將資料帶入更接近您的使用者。

您可以在任何[適用於 MySQL 的 Azure 資料庫區域](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)中擁有主伺服器。  主伺服器的配對區域或通用複本區域中可以有複本。 下圖顯示哪些複本區域可供使用，視您的主要區域而定。

[![讀取複本區域](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>通用複本區域
無論您的主伺服器位於何處，您都可以在下列任何區域中建立讀取複本。 支援的通用複本區域包括：

澳大利亞東部、澳大利亞東南部、美國中部、東亞、美國東部、美國東部2、日本東部、日本西部、韓國中部、南韓南部、美國中北部、北歐、美國中南部、東南亞、英國南部、英國西部、西歐、美國西部。

* 美國西部2暫時無法當做跨區域複本位置使用。

### <a name="paired-regions"></a>配對區域
除了通用複本區域之外，您還可以在主伺服器的 Azure 配對區域中建立讀取複本。 如果您不知道您的區域配對，可以從[Azure 配對區域一文](../best-practices-availability-paired-regions.md)深入瞭解。

如果您使用跨區域複本進行嚴重損壞修復計畫，建議您在配對的區域中建立複本，而不是在其他其中一個區域。 配對的區域會避免同時更新，並排定實體隔離和資料存放區的優先順序。  

不過，有一些限制需要考慮： 

* 區域可用性：適用於 MySQL 的 Azure 資料庫適用于美國西部2、法國中部、阿拉伯聯合大公國北部和德國中部。 不過，它們的配對區域無法使用。
    
* 單向配對：某些 Azure 區域只會以單一方向配對。 這些區域包括印度西部、巴西南部和 US Gov 維吉尼亞州。 
   這表示印度西部的主伺服器可以在印度南部中建立複本。 不過，印度南部中的主伺服器無法在印度西部建立複本。 這是因為印度西部的次要地區印度南部，但印度南部的次要地區並非印度西部。

## <a name="create-a-replica"></a>建立複本

如果主伺服器沒有任何現有的複本伺服器，則主機會先重新開機以準備進行複寫。

當您啟動建立複本工作流程時，會建立空白的適用於 MySQL 的 Azure 資料庫伺服器。 新的伺服器會具有主要伺服器上的資料。 建立時間取決於主要伺服器上的資料量，以及距離上次每週完整備份的時間。 時間的範圍可能介於數分鐘到數小時。

每個複本都會啟用儲存體[自動成長](concepts-pricing-tiers.md#storage-auto-grow)。 自動成長功能可讓複本跟上複寫的資料，並防止因儲存錯誤而造成的複寫中斷。

了解如何[在 Azure 入口網站中建立讀取複本](howto-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>連線到複本

在建立時，複本會繼承主伺服器的防火牆規則。 之後，這些規則與主伺服器無關。

複本會從主要伺服器繼承系統管理員帳戶。 系統會將主要伺服器上的所有使用者帳戶複寫到讀取複本。 您只能使用主要伺服器上可用的使用者帳戶來連線到讀取複本。

您可以使用主機名稱和有效的使用者帳戶連接到複本，如同在一般適用於 MySQL 的 Azure 資料庫伺服器上所做的一樣。 對於名為**myreplica**且具有管理員使用者名稱**myadmin**的伺服器，您可以使用 mysql CLI 來連線到複本：

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

在出現提示時，請輸入使用者帳戶的密碼。

## <a name="monitor-replication"></a>監視複寫

適用於 MySQL 的 Azure 資料庫在 Azure 監視器中提供複寫**延遲（以秒為單位）** 度量。 此計量僅適用於複本。

此計量是使用 MySQL 的`seconds_behind_master` `SHOW SLAVE STATUS`命令所提供的計量來計算。

設定警示，以在複寫延遲到達您的工作負載無法接受的值時通知您。

## <a name="stop-replication"></a>停止複寫

您可以停止主要伺服器與複本之間的複寫。 當主要伺服器和讀取複本之間的複寫停止時，複本就會成為獨立伺服器。 獨立伺服器中的資料是起始「停止複寫」命令時，複本上所包含的可用資料。 獨立伺服器不會跟上主要伺服器。

當您選擇停止複寫至複本時，它會失去先前主要和其他複本的所有連結。 主要及其複本之間沒有自動容錯移轉。

> [!IMPORTANT]
> 獨立伺服器無法再次設定為複本。
> 在您停止讀取複本上的複寫之前，請確定該複本上已經有您所需要的所有資料。

了解如何[停止複寫至複本](howto-read-replicas-portal.md)。

## <a name="considerations-and-limitations"></a>考量與限制

### <a name="pricing-tiers"></a>定價層

目前只有「一般用途」與「記憶體最佳化」定價層提供讀取複本。

### <a name="master-server-restart"></a>主要伺服器重新啟動

當您為沒有現有複本的主伺服器建立複本時，主伺服器會先重新開機，以準備好進行複寫。 請將此納入考慮，並在離峰期間執行這些作業。

### <a name="new-replicas"></a>新複本

讀取複本會建立為新的適用於 MySQL 的 Azure 資料庫伺服器。 現有伺服器無法設定為複本。 您無法為另一個讀取複本建立複本。

### <a name="replica-configuration"></a>複本設定

系統會使用與主要伺服器相同的伺服器設定來建立複本。 建立複本之後，以下設定可以個別地從主要伺服器進行變更：計算世代、虛擬核心、儲存體及備份保留期間。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。

> [!IMPORTANT]
> 在將主要伺服器設定更新為新值之前，應將複本的設定更新為相等或更大的值。 此動作可確保複本可以跟上主要伺服器上所做的變更。

建立複本時，防火牆規則和參數設定會從主伺服器繼承到複本。 之後，複本的規則就是獨立的。

### <a name="stopped-replicas"></a>已停止的複本

如果您停止主伺服器和讀取複本之間的複寫，已停止的複本會成為可接受讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

### <a name="deleted-master-and-standalone-servers"></a>已刪除的主要和獨立伺服器

刪除主要伺服器時，會在所有讀取複本上停止複寫。 這些複本會自動成為獨立伺服器，而且可以同時接受讀取和寫入。 主要伺服器本身會被刪除。

### <a name="user-accounts"></a>使用者帳戶

系統會將主要伺服器上的使用者複寫到讀取複本。 您只能使用主要伺服器上可用的使用者帳戶來連線到讀取複本。

### <a name="server-parameters"></a>伺服器參數

若要防止資料不同步，以及避免潛在的資料遺失或損毀，則會在使用讀取複本時，有些伺服器參數會被鎖定而無法更新。

主要和複本伺服器上的下列伺服器參數都會被鎖定：
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

已[`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler)在複本伺服器上鎖定參數。 

若要更新主伺服器上的上述其中一個參數，請刪除複本伺服器、更新主要主機上的參數值，然後重新建立複本。

### <a name="other"></a>其他

- 不支援全域交易識別碼 (GTID)。
- 不支援建立複本的複本。
- 記憶體中的資料表可能會導致複本不同步。這是 MySQL 複寫技術的限制。 如需詳細資訊，請參閱 [MySQL 參考文件](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) \(英文\)。
- 請確定主要伺服器資料表有主索引鍵。 缺少主索引鍵可能會造成主體和複本之間產生複寫延遲。
- 檢閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) \(英文\) 中的完整 MySQL 複寫限制清單

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Azure 入口網站來建立及管理讀取複本](howto-read-replicas-portal.md)
- 瞭解如何[使用 Azure CLI 和 REST API 來建立和管理讀取複本](howto-read-replicas-cli.md)
