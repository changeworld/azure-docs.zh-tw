---
title: 讀取複本-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的「讀取複本」功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: f2f752d6435b311c1737d531f5572aed5af223f2
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276646"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>讀取適用於 PostgreSQL 的 Azure 資料庫中的複本-單一伺服器

讀取複本功能可讓您將資料從適用於 PostgreSQL 的 Azure 資料庫伺服器複寫到唯讀伺服器。 您可以從主要伺服器複寫到最多五個複本。 複本會使用 PostgreSQL 引擎的原生複寫技術以非同步方式更新。

複本是新伺服器，管理方式類似於一般適用於 PostgreSQL 的 Azure 資料庫伺服器。 針對每個讀取複本，系統每月會針對在虛擬核心中所佈建的計算量，以及在儲存體中所佈建的容量 (以 GB 為單位) 向您收費。

了解如何[建立及管理複本](howto-read-replicas-portal.md)。

## <a name="when-to-use-a-read-replica"></a>何時應該使用讀取複本
讀取複本功能可針對需大量讀取的工作負載，協助改善效能及調整能力。 讀取工作負載可隔離到複本，而寫入工作負載可以導向到主要伺服器。

常見的案例是讓 BI 與分析工作負載使用讀取複本做為報告的資料來源。

由於複本是唯讀狀態，因此不會直接降低主要伺服器上的寫入容量負擔。 這項功能不是以寫入密集的工作負載為目標。

讀取複本功能會使用 PostgreSQL 非同步複寫。 此功能不適用於同步複寫案例。 主要伺服器和複本之間將會有顯著的延遲。 複本上的資料最終仍會與主要伺服器上的資料保持一致。 請針對可接受此延遲的工作負載使用此功能。

## <a name="cross-region-replication"></a>跨區域複寫
您可以從主要伺服器在不同的區域中建立讀取複本。 跨區域複寫有助於災害復原規劃或讓資料更接近使用者之類的案例。

>[!NOTE]
> 基本層伺服器僅支援相同的區域複寫。

您可以在任何[適用於 PostgreSQL 的 Azure 資料庫區域](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql)中擁有主伺服器。 主要伺服器可以在其配對區域或全球複本區域中擁有複本。 下圖顯示根據您的主要區域而可供使用的複本區域。

[ ![讀取複本區域](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>全球的複本區域
無論您的主伺服器位於何處，您都可以在下列任何區域中建立讀取複本。 這些是通用複本區域：

澳大利亞東部、澳大利亞東南部、美國中部、東亞、美國東部、美國東部2、日本東部、日本西部、韓國中部、南韓南部、美國中北部、北歐、美國中南部、東南亞、英國南部、英國西部、西歐、美國西部、美國西部2、美國中西部。

### <a name="paired-regions"></a>配對的區域
除了全球複本區域外，您還可以在主要伺服器的 Azure 配對區域中建立讀取複本。 如果您不知道所在區域的配對，則可以從 [Azure 配對區域](../best-practices-availability-paired-regions.md)一文深入了解。

如果您使用跨區域複本來規劃災害復原，建議您在配對區域中建立複本，而不要在其他區域之一建立。 配對區域可避免同時更新，並排定實體隔離和資料落地的優先順序。  

有一些限制需要考慮： 

* 區域可用性：適用於 PostgreSQL 的 Azure 資料庫適用于法國中部、阿拉伯聯合大公國北部和德國中部。 不過，卻沒有提供其配對區域。
    
* 單向配對：某些 Azure 區域只會單向配對。 這些區域包括印度西部、巴西南部。 
   這表示位於印度西部的主要伺服器可以在印度南部建立複本。 但位於印度南部的主要伺服器無法在印度西部建立複本。 其原因是印度西部的次要區域是印度南部，但印度南部的次要區域卻不是印度西部。


## <a name="create-a-replica"></a>建立複本
當您開始建立複本的工作流程時，系統會建立空白的「適用於 PostgreSQL 的 Azure 資料庫」伺服器。 新的伺服器會具有主要伺服器上的資料。 建立時間取決於主要伺服器上的資料量，以及距離上次每週完整備份的時間。 時間的範圍可能介於數分鐘到數小時。

每個複本都會啟用儲存體[自動成長](concepts-pricing-tiers.md#storage-auto-grow)。 自動成長功能可讓複本跟上複寫的資料，並防止因儲存體錯誤而造成的複寫中斷。

讀取複本功能會使用 PostgreSQL 的實體複寫，而非邏輯複寫。 使用複寫位置的串流複寫是預設作業模式。 必要時，系統會使用記錄傳送來跟上進度。

了解如何[在 Azure 入口網站中建立讀取複本](howto-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>連線到複本
當您建立複本時，其不會繼承主要伺服器的防火牆規則或 VNet 服務端點。 您必須個別針對複本設定這些規則。

複本會從主要伺服器繼承系統管理員帳戶。 系統會將主要伺服器上的所有使用者帳戶複寫到讀取複本。 您只能使用主要伺服器上可用的使用者帳戶來連線到讀取複本。

您可以使用複本的主機名稱和有效的使用者帳戶來連線到該複本，如同連線到一般適用於 PostgreSQL 的 Azure 資料庫伺服器一樣。 對於名為「**我的複本**」的伺服器與系統管理員使用者名稱**myadmin**，您可以使用 psql 來連線到複本：

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

在出現提示時，請輸入使用者帳戶的密碼。

## <a name="monitor-replication"></a>監視複寫
適用於 PostgreSQL 的 Azure 資料庫提供兩個計量來監視複寫。 這兩個度量是跨複本與**複本延遲**的**最大延隔**時間。 若要瞭解如何查看這些計量，請參閱[讀取複本操作說明文章](howto-read-replicas-portal.md)中的**監視複本**一節。

[**跨越複本的最大延遲**] 計量會顯示主伺服器和最延遲複本之間的延遲（以位元組為單位）。 此計量僅適用於主要伺服器。

[**複本延遲**] 度量會顯示上次重新執行交易之後的時間。 如果主要伺服器上沒有發生交易，計量會反映此時間延隔。 此度量僅適用于複本伺服器。 複本延遲是從 view 計算而來 `pg_stat_wal_receiver` ：

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

請設定警示，以在複本延隔時間接近您工作負載無法接受的值時通知您。 

如需其他見解，請直接查詢主要伺服器以取得所有複本的複本延隔時間 (以位元組為單位)。

在 PostgreSQL 第 10 版中：

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

在 PostgreSQL 第 9.6 版和較早版本中：

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> 如果主要伺服器或讀取複本重新啟動，其重新啟動完畢並跟上進度所花費的時間將會反映在 [複本延隔時間] 計量中。

## <a name="stop-replication"></a>停止複寫
您可以停止主要伺服器與複本之間的複寫。 停止動作會導致複本重新啟動以移除其複寫設定。 當主要伺服器和讀取複本之間的複寫停止時，複本就會成為獨立伺服器。 獨立伺服器中的資料是起始「停止複寫」命令時，複本上所包含的可用資料。 獨立伺服器不會跟上主要伺服器。

> [!IMPORTANT]
> 獨立伺服器無法再次設定為複本。
> 在您停止讀取複本上的複寫之前，請確定該複本上已經有您所需要的所有資料。

當您停止複寫時，複本會失去其前一個主要複本和其他複本的所有連結。

了解如何[停止複寫至複本](howto-read-replicas-portal.md)。

## <a name="failover"></a>容錯移轉
主要和複本伺服器之間沒有自動容錯移轉。 

由於複寫是非同步，因此主要和複本之間會有延遲。 延遲量可能會受到一些因素的影響，例如，在主伺服器上執行的工作負載有多長，以及資料中心之間的延遲。 在大部分的情況下，複本延遲的範圍是幾秒鐘到幾分鐘的時間。 您可以使用每個複本可用的計量*複本延遲*來追蹤實際的複寫延遲。 此度量會顯示上次重新執行交易之後的時間。 我們建議您在一段時間內觀察複本延遲，以識別您的平均延遲。 您可以針對複本延遲設定警示，如此一來，如果它超出預期的範圍，您就可以採取動作。

> [!Tip]
> 如果您容錯移轉至複本，從主伺服器取消複本時的延遲將會指出遺失的資料量。

一旦決定要容錯移轉至複本之後， 

1. 停止複寫至複本<br/>
   若要讓複本伺服器能夠接受寫入，必須執行此步驟。 在此過程中，複本伺服器將會重新開機，並從主要 delinked。 一旦您起始停止複寫，後端進程通常需要大約2分鐘的時間才能完成。 請參閱本文的[停止](#stop-replication)複寫一節，以瞭解此動作的含意。
    
2. 將您的應用程式指向 (先前) 複本<br/>
   每部伺服器都有唯一的連接字串。 更新您的應用程式，使其指向 (之前的) 複本，而不是 master。
    
一旦您的應用程式成功處理讀取和寫入，您就已完成容錯移轉。 您的應用程式體驗所需的停機時間將取決於您偵測到問題，並完成上述步驟1和2。


## <a name="considerations"></a>考量事項

本節將摘要說明有關讀取複本功能的考量。

### <a name="prerequisites"></a>必要條件
讀取複本和[邏輯解碼](concepts-logical.md)兩者都相依于 Postgres 寫 (WAL) 以取得資訊。 這兩個功能需要來自 Postgres 的不同記錄層級。 邏輯解碼需要比讀取複本更高的記錄層級。

若要設定正確的記錄層級，請使用 Azure 複寫支援參數。 Azure 複寫支援有三個設定選項：

* **Off** -將最少的資訊放在 WAL 中。 大部分適用於 PostgreSQL 的 Azure 資料庫伺服器上都無法使用此設定。  
* **複本**-比**關閉**更詳細的資訊。 這是[讀取複本](concepts-read-replicas.md)正常執行所需的最基本記錄層級。 這是大多數伺服器上的預設設定。
* **邏輯**比**複本**更詳細。 這是要使用之邏輯解碼的最低記錄層級。 讀取複本也適用于此設定。

此參數變更之後，必須重新開機伺服器。 就內部而言，此參數會設定 Postgres 參數 `wal_level` 、 `max_replication_slots` 和 `max_wal_senders` 。

### <a name="new-replicas"></a>新複本
讀取複本會建立為最新適用於 PostgreSQL 的 Azure 資料庫伺服器。 現有伺服器無法設定為複本。 您無法為另一個讀取複本建立複本。

### <a name="replica-configuration"></a>複本設定
使用與主伺服器相同的計算和儲存設定來建立複本。 建立複本之後，可以變更數個設定，包括儲存體和備份保留期限。

在下列情況下，您也可以在複本上變更虛擬核心和定價層：
* PostgreSQL 會要求讀取複本上的 `max_connections` 參數值大於或等於主要伺服器的值，否則讀取複本將不會啟動。 在適用於 PostgreSQL 的 Azure 資料庫中， `max_connections` 參數值是以 SKU (虛擬核心和定價層) 為基礎。 如需詳細資訊，請參閱[適用於 PostgreSQL 的 Azure 資料庫限制](concepts-limits.md)。 
* 不支援從基本定價層進行調整

> [!IMPORTANT]
> 將主要設定更新為新值之前，請將複本設定更新為相等或更大的值。 此動作可確保複本可以跟上主要伺服器上所做的變更。

如果您嘗試更新上述的伺服器值，但未遵守限制，您會收到錯誤。

建立複本或之後，防火牆規則、虛擬網路規則和參數設定不會從主伺服器繼承到複本。

### <a name="basic-tier"></a>基本層
基本層伺服器僅支援相同的區域複寫。

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[于 postgresql 要求](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) `max_prepared_transactions` 讀取複本上的參數值必須大於或等於主要值，否則複本將不會啟動。 如果您想要 `max_prepared_transactions` 在主伺服器上變更，請先在複本上變更它。

### <a name="stopped-replicas"></a>已停止的複本
如果您停止主要伺服器和讀取複本之間的複寫，複本將會重新啟動以套用變更。 停止的複本會變成支接受讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

### <a name="deleted-master-and-standalone-servers"></a>已刪除的主要和獨立伺服器
刪除主要伺服器時，其所有讀取複本都會變成獨立伺服器。 這些複本將會重新啟動以反映此變更。

## <a name="next-steps"></a>後續步驟
* 了解如何[在 Azure 入口網站中建立及管理讀取複本](howto-read-replicas-portal.md)。
* 瞭解如何[在 Azure CLI 和 REST API 中建立及管理讀取複本](howto-read-replicas-cli.md)。
