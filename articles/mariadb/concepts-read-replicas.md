---
title: 讀取複本-適用於 MariaDB 的 Azure 資料庫
description: 瞭解適用於 MariaDB 的 Azure 資料庫中的讀取複本：選擇區域、建立複本、連接到複本、監視複寫，以及停止複寫。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: bed89b325ce28ab969bad5ed30802bdb67a21a96
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86076550"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>「適用於 MariaDB 的 Azure 資料庫」中的讀取複本

讀取複本功能可讓您將資料從適用於 MariaDB 的 Azure 資料庫伺服器複寫到唯讀伺服器。 您可以從主要伺服器複寫到最多五個複本。 複本會使用適用于 mariadb 引擎的二進位記錄檔（binlog）以檔案位置為基礎的複寫技術（具有全域交易識別碼（GTID））以非同步方式更新。 若要深入了解 binlog 複寫，請參閱 [binlog 複寫概觀](https://mariadb.com/kb/en/library/replication-overview/) \(英文\)。

複本是您所管理的新伺服器，類似于一般適用於 MariaDB 的 Azure 資料庫伺服器。 針對每個讀取複本，系統每月會針對在虛擬核心中所佈建的計算量，以及在儲存體中所佈建的容量 (以 GB 為單位) 向您收費。

若要深入瞭解 GTID 複寫，請參閱[適用于 mariadb 複寫檔](https://mariadb.com/kb/en/library/gtid/)。

> [!NOTE]
> 偏差-免費通訊
>
> Microsoft 支援多樣化和 inclusionary 的環境。 本文包含對_一詞的_參考。 [適用于無偏差通訊的 Microsoft 樣式指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)可辨識此為 exclusionary 單字。 本文中會使用這個字來進行一致性，因為它目前是出現在軟體中的單字。 當軟體更新為移除此單字時，此文章將會更新為對齊。
>

## <a name="when-to-use-a-read-replica"></a>何時應該使用讀取複本

讀取複本功能可針對需大量讀取的工作負載，協助改善效能及調整能力。 讀取工作負載可隔離到複本，而寫入工作負載可以導向到主要伺服器。

常見的案例是讓 BI 與分析工作負載使用讀取複本做為報告的資料來源。

由於複本是唯讀狀態，因此不會直接降低主要伺服器上的寫入容量負擔。 這項功能不是以寫入密集的工作負載為目標。

「讀取複本」功能會使用非同步複寫。 此功能不適用於同步複寫案例。 主要伺服器和複本之間將會有顯著的延遲。 複本上的資料最終仍會與主要伺服器上的資料保持一致。 請針對可接受此延遲的工作負載使用此功能。

## <a name="cross-region-replication"></a>跨區域複寫
您可以從主要伺服器在不同的區域中建立讀取複本。 跨區域複寫有助於災害復原規劃或讓資料更接近使用者之類的案例。

您可以在任何[適用於 MariaDB 的 Azure 資料庫區域](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb)中擁有主伺服器。  主要伺服器可以在其配對區域或全球複本區域中擁有複本。 下圖顯示根據您的主要區域而可供使用的複本區域。

[ ![讀取複本區域](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>全球的複本區域
無論您的主要伺服器位於何處，您都可以在下列任何區域中建立讀取複本。 支援的全球複本區域包括：

澳大利亞東部、澳大利亞東南部、美國中部、東亞、美國東部、美國東部2、日本東部、日本西部、韓國中部、南韓南部、美國中北部、北歐、美國中南部、東南亞、英國南部、英國西部、西歐、美國西部、美國西部2、美國中西部。

### <a name="paired-regions"></a>配對的區域
除了全球複本區域外，您還可以在主要伺服器的 Azure 配對區域中建立讀取複本。 如果您不知道所在區域的配對，則可以從 [Azure 配對區域](../best-practices-availability-paired-regions.md)一文深入了解。

如果您使用跨區域複本來規劃災害復原，建議您在配對區域中建立複本，而不要在其他區域之一建立。 配對區域可避免同時更新，並排定實體隔離和資料落地的優先順序。  

不過，其中有一些限制需要考慮： 

* 區域可用性：適用於 MariaDB 的 Azure 資料庫適用于法國中部、阿拉伯聯合大公國北部和德國中部。 不過，卻沒有提供其配對區域。
    
* 單向配對：某些 Azure 區域只會單向配對。 這些區域包括印度西部、巴西南部和 US Gov 維吉尼亞州。 
   這表示位於印度西部的主要伺服器可以在印度南部建立複本。 但位於印度南部的主要伺服器無法在印度西部建立複本。 其原因是印度西部的次要區域是印度南部，但印度南部的次要區域卻不是印度西部。

## <a name="create-a-replica"></a>建立複本

> [!IMPORTANT]
> 「讀取複本」功能僅適用于一般用途或記憶體優化定價層中的適用於 MariaDB 的 Azure 資料庫伺服器。 請確定主要伺服器處於這些定價層中。

如果主要伺服器沒有現有的複本伺服器，則主要伺服器會先重新開啟動以準備好進行複寫。

當您啟動建立複本工作流程時，會建立空白的適用於 MariaDB 的 Azure 資料庫伺服器。 新的伺服器會具有主要伺服器上的資料。 建立時間取決於主要伺服器上的資料量，以及距離上次每週完整備份的時間。 時間的範圍可能介於數分鐘到數小時。

> [!NOTE]
> 如果您沒有在伺服器上設定儲存體警示，建議您這麼做。 警示會在伺服器接近儲存體限制時向您發出通知，因為此狀況會影響複寫。

了解如何[在 Azure 入口網站中建立讀取複本](howto-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>連線到複本

複本會於建立時繼承主要伺服器的防火牆規則。 之後，這些規則與主伺服器無關。

複本會從主要伺服器繼承系統管理員帳戶。 系統會將主要伺服器上的所有使用者帳戶複寫到讀取複本。 您只能使用主要伺服器上可用的使用者帳戶來連線到讀取複本。

您可以使用主機名稱和有效的使用者帳戶連接到複本，如同在一般適用於 MariaDB 的 Azure 資料庫伺服器上所做的一樣。 若伺服器名稱為 **myreplica**，且統管理員使用者名稱為 **myadmin**，則您可以使用 mysql CLI 來連線到複本：

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

在出現提示時，請輸入使用者帳戶的密碼。

## <a name="monitor-replication"></a>監視複寫

適用於 MariaDB 的 Azure 資料庫在 Azure 監視器中提供複寫**延遲（以秒為單位）** 度量。 此計量僅適用於複本。

此計量是使用 `seconds_behind_master` 適用于 mariadb 的命令所提供的計量來計算 `SHOW SLAVE STATUS` 。

設定警示，以在複寫延遲到達您的工作負載無法接受的值時通知您。

## <a name="stop-replication"></a>停止複寫

您可以停止主要伺服器與複本之間的複寫。 當主要伺服器和讀取複本之間的複寫停止時，複本就會成為獨立伺服器。 獨立伺服器中的資料是起始「停止複寫」命令時，複本上所包含的可用資料。 獨立伺服器不會跟上主要伺服器。

當您選擇停止複寫至複本時，其會失去與其先前主要伺服器和其他複本的所有連結。 主要伺服器及其複本之間沒有自動容錯移轉功能。

> [!IMPORTANT]
> 獨立伺服器無法再次設定為複本。
> 在您停止讀取複本上的複寫之前，請確定該複本上已經有您所需要的所有資料。

了解如何[停止複寫至複本](howto-read-replicas-portal.md)。

## <a name="failover"></a>容錯移轉

主要和複本伺服器之間沒有自動容錯移轉。 

由於複寫是非同步，因此主要和複本之間會有延遲。 延遲量可能會受到一些因素的影響，例如，在主伺服器上執行的工作負載有多長，以及資料中心之間的延遲。 在大部分的情況下，複本延遲的範圍是幾秒鐘到幾分鐘的時間。 您可以使用每個複本可用的計量*複本延遲*來追蹤實際的複寫延遲。 此度量會顯示上次重新執行交易之後的時間。 我們建議您在一段時間內觀察複本延遲，以識別您的平均延遲。 您可以針對複本延遲設定警示，如此一來，如果它超出預期的範圍，您就可以採取動作。

> [!Tip]
> 如果您容錯移轉至複本，從主伺服器取消複本時的延遲將會指出遺失的資料量。

一旦決定要容錯移轉至複本之後， 

1. 停止複寫至複本<br/>
   若要讓複本伺服器能夠接受寫入，必須執行此步驟。 做為此程式的一部分，複本伺服器將會從主機 delinked。 一旦您起始停止複寫，後端進程通常需要大約2分鐘的時間才能完成。 請參閱本文的[停止](#stop-replication)複寫一節，以瞭解此動作的含意。
    
2. 將您的應用程式指向（先前的）複本<br/>
   每部伺服器都有唯一的連接字串。 更新您的應用程式，使其指向（先前）複本，而不是 master。
    
一旦您的應用程式成功處理讀取和寫入，您就已完成容錯移轉。 您的應用程式體驗所需的停機時間將取決於您偵測到問題，並完成上述步驟1和2。

## <a name="considerations-and-limitations"></a>考量與限制

### <a name="pricing-tiers"></a>定價層

目前只有「一般用途」與「記憶體最佳化」定價層提供讀取複本。

> [!NOTE]
> 執行複本伺服器的成本取決於執行複本伺服器的區域。

### <a name="master-server-restart"></a>主要伺服器重新啟動

當您為沒有任何現有複本的主要伺服器建立複本時，主要伺服器首先將會重新啟動，以準備本身進行複寫。 請考慮這一點，並在離峰期間執行這些作業。

### <a name="new-replicas"></a>新複本

讀取複本會建立為新的適用於 MariaDB 的 Azure 資料庫伺服器。 現有伺服器無法設定為複本。 您無法為另一個讀取複本建立複本。

### <a name="replica-configuration"></a>複本設定

系統會使用與主要伺服器相同的伺服器設定來建立複本。 建立複本之後，您可以從主伺服器獨立變更數個設定：計算世代、虛擬核心、儲存體、備份保留期限和適用于 mariadb 引擎版本。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。

> [!IMPORTANT]
> 在將主要伺服器設定更新為新值之前，應將複本的設定更新為相等或更大的值。 此動作可確保複本可以跟上主要伺服器上所做的變更。

建立複本時，複本會從主要伺服器繼承防火牆規則和參數設定。 之後，複本的規則就已獨立。

### <a name="stopped-replicas"></a>已停止的複本

如果您停止在主要伺服器和讀取複本之間進行複寫，已停止的複本就會變成可同時接受讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

### <a name="deleted-master-and-standalone-servers"></a>已刪除的主要和獨立伺服器

刪除主要伺服器時，會在所有讀取複本上停止複寫。 這些複本會自動變成獨立伺服器，並且可以同時接受讀取和寫入。 主要伺服器本身會被刪除。

### <a name="user-accounts"></a>使用者帳戶

系統會將主要伺服器上的使用者複寫到讀取複本。 您只能使用主要伺服器上可用的使用者帳戶來連線到讀取複本。

### <a name="server-parameters"></a>伺服器參數

若要防止資料不同步，以及避免潛在的資料遺失或損毀，則會在使用讀取複本時，有些伺服器參數會被鎖定而無法更新。

主要伺服器和複本伺服器上都會鎖定下列伺服器參數：
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

複本伺服器上會鎖定 [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) 參數。

若要更新主要伺服器上的上述其中一個參數，請刪除複本伺服器、更新主要伺服器上的參數值，然後重新建立複本。

### <a name="other"></a>其他

- 不支援建立複本的複本。
- 記憶體中的資料表可能會導致複本不同步。這是適用于 mariadb 複寫技術的限制。
- 請確定主要伺服器資料表有主索引鍵。 缺少主索引鍵可能會造成主體和複本之間產生複寫延遲。

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Azure 入口網站來建立及管理讀取複本](howto-read-replicas-portal.md)
- 了解如何[使用 Azure CLI 和 REST API 來建立及管理讀取複本](howto-read-replicas-cli.md)
