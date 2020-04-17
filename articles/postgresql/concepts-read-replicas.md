---
title: 讀取複本 - 用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器
description: 本文介紹了 Azure 資料庫中的讀取副本功能,用於 PostgreSQL - 單伺服器。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 545d04bdede76a6ce25c9e4665f39c01ff6caa73
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531978"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>在 Azure 資料庫讀取複本,用於 PostgreSQL - 單一伺服器

讀取複本功能可讓您將資料從適用於 PostgreSQL 的 Azure 資料庫伺服器複寫到唯讀伺服器。 您可以從主要伺服器複寫到最多五個複本。 複本會使用 PostgreSQL 引擎的原生複寫技術以非同步方式更新。

複本是新伺服器，管理方式類似於一般適用於 PostgreSQL 的 Azure 資料庫伺服器。 針對每個讀取複本，系統每月會針對在虛擬核心中所佈建的計算量，以及在儲存體中所佈建的容量 (以 GB 為單位) 向您收費。

了解如何[建立及管理複本](howto-read-replicas-portal.md)。

## <a name="when-to-use-a-read-replica"></a>何時應該使用讀取複本
讀取複本功能可針對需大量讀取的工作負載，協助改善效能及調整能力。 讀取工作負載可隔離到複本，而寫入工作負載可以導向到主要伺服器。

常見的案例是讓 BI 與分析工作負載使用讀取複本做為報告的資料來源。

由於複本是唯讀狀態，因此不會直接降低主要伺服器上的寫入容量負擔。 這項功能不是以寫入密集的工作負載為目標。

讀取複本功能會使用 PostgreSQL 非同步複寫。 此功能不適用於同步複寫案例。 主要伺服器和複本之間將會有顯著的延遲。 複本上的資料最終仍會與主要伺服器上的資料保持一致。 請針對可接受此延遲的工作負載使用此功能。

## <a name="cross-region-replication"></a>跨區域複製
您可以在與主伺服器不同的區域中建立讀取複本。 跨區域複製對於災難恢復規劃或使數據更接近使用者等方案非常有用。

您可以在[PostgreSQL 區域](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql)的任何 Azure 資料庫中擁有主伺服器。 主伺服器可以在其配對區域或通用副本區域中具有副本。 下圖顯示了哪些副本區域可用,具體取決於您的主區域。

[![讀取複本區域](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>通用複本區域
無論主伺服器位於何處,您始終都可以在以下任何區域中創建讀取副本。 這些是通用副本區域:

澳大利亞東部、澳大利亞東南部、美國中部、東亞、美國東部、美國東部、日本東部、日本西部、韓國中部、韓國、美國中北部、北歐、美國中南部、東南亞、英國南部、英國西部、西歐、美國西部。

*西部 US 2 暫時不可用,作為跨區域副本位置。


### <a name="paired-regions"></a>配對的區域
除了通用副本區域外,還可以在主伺服器的 Azure 配對區域中創建讀取副本。 如果不知道區域的對,可以從[Azure 配對區域一文](../best-practices-availability-paired-regions.md)中學習詳細資訊。

如果使用跨區域副本進行災難恢復規劃,我們建議您在配對區域創建副本,而不是在其他區域中創建副本。 配對區域可避免同時更新,並優先考慮物理隔離和數據駐留。  

需要考慮以下限制: 

* 區域可用性:PostgreSQL 的 Azure 資料庫在美國西部 2、法國中部、阿聯酋北部和德國中部提供。 但是,它們配對的區域不可用。
    
* 單向對:某些 Azure 區域僅朝一個方向配對。 這些地區包括西印度、巴西南部。 
   這意味著印度西部的主伺服器可以在印度南部創建副本。 但是,印度南部的主伺服器無法在西印度創建副本。 這是因為西印度的次生地區是南印度,但南印度的次生地區不是西印度。


## <a name="create-a-replica"></a>建立複本
當您開始建立複本的工作流程時，系統會建立空白的「適用於 PostgreSQL 的 Azure 資料庫」伺服器。 新的伺服器會具有主要伺服器上的資料。 建立時間取決於主要伺服器上的資料量，以及距離上次每週完整備份的時間。 時間的範圍可能介於數分鐘到數小時。

每個複本都開啟儲存[自動成長](concepts-pricing-tiers.md#storage-auto-grow)。 自動增長功能允許副本跟上複製到它的數據,並防止存儲錯誤導致複製中斷。

讀取複本功能會使用 PostgreSQL 的實體複寫，而非邏輯複寫。 使用複寫位置的串流複寫是預設作業模式。 必要時，系統會使用記錄傳送來跟上進度。

了解如何[在 Azure 入口網站中建立讀取複本](howto-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>連線到複本
當您建立複本時，其不會繼承主要伺服器的防火牆規則或 VNet 服務端點。 您必須個別針對複本設定這些規則。

複本會從主要伺服器繼承系統管理員帳戶。 系統會將主要伺服器上的所有使用者帳戶複寫到讀取複本。 您只能使用主要伺服器上可用的使用者帳戶來連線到讀取複本。

您可以使用複本的主機名稱和有效的使用者帳戶來連線到該複本，如同連線到一般適用於 PostgreSQL 的 Azure 資料庫伺服器一樣。 對於名為**我的複本**的伺服器,使用管理員使用者名**myadmin**,您可以使用 psql 連線到複本:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

在出現提示時，請輸入使用者帳戶的密碼。

## <a name="monitor-replication"></a>監視複寫
用於 PostgreSQL 的 Azure 資料庫提供了兩個用於監視複製的指標。 這兩個指標是**跨副本的最大延遲**與**複本延遲**。 要瞭解如何查看這些指標,請參閱**監視器**[讀取副本"輔助方法"文章](howto-read-replicas-portal.md)的副本副本部分。

**"跨副本的最大值延遲**"指標顯示主副本和最滯後副本之間的滯後位元節數。 此計量僅適用於主要伺服器。

**副本延遲**指標顯示自上次重播事務以來的時間。 如果主要伺服器上沒有發生交易，計量會反映此時間延隔。 此指標僅適用於副本伺服器。 複本延遲是從檢視計算的`pg_stat_wal_receiver`:

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

停止複製時,副本將丟失指向其以前的主副本和其他副本的所有連結。

了解如何[停止複寫至複本](howto-read-replicas-portal.md)。

## <a name="failover"></a>容錯移轉
主伺服器和副本伺服器之間沒有自動故障轉移。 

由於複製是非同步的,因此主副本和副本之間存在延遲。 延遲量可能受許多因素影響,例如主伺服器上運行的工作負載有多重以及資料中心之間的延遲。 在大多數情況下,副本延遲範圍介於幾秒鐘到幾分鐘之間。 您可以使用可用於每個複本的指標*副本延遲*跟蹤實際複製延遲。 此指標顯示自上次重播事務以來的時間。 我們建議您通過觀察副本滯後一段時間來確定您的平均延遲。 您可以設置副本延遲的警報,以便如果超出預期範圍,則可以執行操作。

> [!Tip]
> 如果故障轉移到副本,則從主副本上分離副本時的延遲將指示數據丟失量。

一旦您決定要故障轉移到副本, 

1. 停止複製到複本<br/>
   此步驟對於使副本伺服器能夠接受寫入是必需的。 作為此過程的一部分,副本伺服器將重新啟動,並從主伺服器中取消連結。 啟動停止複製后,後端進程通常需要大約2分鐘才能完成。 請參閱本文的[停止複製](#stop-replication)部分以瞭解此操作的含義。
    
2. 將應用程式指向(前一個)複本<br/>
   每個伺服器都有一個唯一的連接字串。 更新應用程式以指向(前一個)副本而不是主副本。
    
一旦應用程式成功處理讀取和寫入,您就完成了故障轉移。 應用程式體驗的停機時間取決於何時檢測到問題並完成上述步驟 1 和 2。


## <a name="considerations"></a>考量

本節將摘要說明有關讀取複本功能的考量。

### <a name="prerequisites"></a>Prerequisites
建立讀取複本之前，`azure.replication_support` 參數必須在主要伺服器上設定為 **REPLICA**。 變更此參數後，必須重新啟動伺服器，才能讓變更生效。 `azure.replication_support` 參數僅適用於「一般用途」和「記憶體最佳化」層級。

### <a name="new-replicas"></a>新複本
讀取複本會建立為最新適用於 PostgreSQL 的 Azure 資料庫伺服器。 現有伺服器無法設定為複本。 您無法為另一個讀取複本建立複本。

### <a name="replica-configuration"></a>複本設定
複本使用與主副本相同的計算和存儲設置創建。 建立複本之後，以下設定可以個別地從主要伺服器進行變更：計算世代、虛擬核心、儲存體及備份保留期間。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。

> [!IMPORTANT]
> 在將主設置更新為新值之前,將副本配置更新為相等或更大的值。 此動作可確保複本可以跟上主要伺服器上所做的變更。

PostgreSQL 會要求讀取複本上的 `max_connections` 參數值大於或等於主要伺服器的值，否則讀取複本將不會啟動。 在適用於 PostgreSQL 的 Azure 資料庫中，`max_connections` 參數值會以 SKU 作為基礎。 如需詳細資訊，請參閱[適用於 PostgreSQL 的 Azure 資料庫限制](concepts-limits.md)。 

如果您嘗試更新上述伺服器值,但不符合限制,則收到錯誤。

創建副本或之後,不會從主伺服器繼承防火牆規則、虛擬網路規則和參數設置。

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL 要求](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS)讀取副`max_prepared_transactions`本上的 參數值大於或等於主值;否則,副本將無法啟動。 如果要`max_prepared_transactions`更改主副本,首先在副本上更改它。

### <a name="stopped-replicas"></a>已停止的複本
如果您停止主要伺服器和讀取複本之間的複寫，複本將會重新啟動以套用變更。 停止的複本會變成支接受讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

### <a name="deleted-master-and-standalone-servers"></a>已刪除的主要和獨立伺服器
刪除主要伺服器時，其所有讀取複本都會變成獨立伺服器。 這些複本將會重新啟動以反映此變更。

## <a name="next-steps"></a>後續步驟
* 了解如何[在 Azure 入口網站中建立及管理讀取複本](howto-read-replicas-portal.md)。
* 瞭解如何在[Azure CLI 與 REST API 建立與管理讀取複本](howto-read-replicas-cli.md)。
