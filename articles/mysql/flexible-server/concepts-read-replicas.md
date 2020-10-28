---
title: 讀取複本-適用於 MySQL 的 Azure 資料庫彈性的伺服器
description: 瞭解適用於 MySQL 的 Azure 資料庫彈性伺服器中的讀取複本：建立複本、連接至複本、監視複寫，以及停止複寫。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: 6f3482bdc608d97e4adba5f99393e74f2e6c7cde
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795152"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>在適用於 MySQL 的 Azure 資料庫彈性的伺服器中讀取複本

> [!IMPORTANT]
> 在適用於 MySQL 的 Azure 資料庫彈性的伺服器中讀取複本處於預覽階段。

MySQL 是執行網際網路規模的 web 和行動應用程式的其中一種熱門資料庫引擎。 許多客戶都將它用於線上教育服務、影片串流服務、數位付款解決方案、電子商務平臺、遊戲服務、新聞入口網站、政府和醫療保健網站。 這些服務必須在 web 或行動應用程式的流量增加時進行服務和調整。

在應用程式端，應用程式通常是以 JAVA 或 php 來開發，並遷移為在 Azure 虛擬機器擴展集或 Azure App 服務上執行，或容器化在 Azure Kubernetes Service (AKS) 上執行。 使用虛擬機器擴展集、App Service 或 AKS 做為基礎結構，可透過立即布建新的 Vm，並複寫應用程式的無狀態元件來滿足要求，以簡化應用程式調整，但通常資料庫最後是集中式具狀態元件的瓶頸。

讀取複本功能可讓您將適用於 MySQL 的 Azure 資料庫彈性伺服器的資料複寫至唯讀伺服器。 您可以從來源伺服器複製到最多 **10 個** 複本。 複本會使用 MySQL 引擎的原生二進位記錄 (binlog) 檔案位置型複寫技術來進行非同步更新。 若要深入了解 binlog 複寫，請參閱 [MySQL binlog 複寫概觀](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) \(英文\)。

複本是您管理的新伺服器，與您的來源適用於 MySQL 的 Azure 資料庫彈性伺服器類似。 您將會根據在虛擬核心和儲存體中布建的計算（以 GB/月為單位），產生每個讀取複本的帳單費用。 如需詳細資訊，請參閱 [定價](./concepts-compute-storage.md#pricing)。

若要深入了解 MySQL 複寫功能與問題，請參閱 [MySQL 複寫文件](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) \(英文\)。

> [!NOTE]
> 無偏差通訊
>
> Microsoft 支援多元和包容性的環境。 本文包含 _slave_ 單字的參考。 Microsoft [無偏差通訊的樣式指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)會將這個單字辨識為排他性的單字。 本文中會使用這個單字來保持一致性，因為這個單字是目前出現在軟體中的單字。 當軟體更新為移除此單字時，此文章將會更新以保持一致。
>

## <a name="common-use-cases-for-read-replica"></a>讀取複本的常見使用案例

讀取複本功能可針對需大量讀取的工作負載，協助改善效能及調整能力。 讀取工作負載可隔離到複本，而寫入工作負載可以導向至來源。

常見案例包括：

* 使用輕量連線 proxy （例如 [ProxySQL](https://aka.ms/ProxySQLLoadBalanceReplica) 或使用以微服務為基礎的模式）來調整來自應用程式的讀取工作負載，以向外延展從應用程式到讀取複本的讀取查詢
* BI 或分析報告工作負載可以使用讀取複本作為報告的資料來源
* 適用于將遙測資訊內嵌至 MySQL 資料庫引擎的 IoT 或製造案例，而多個讀取複本用於報告資料

由於複本是唯讀的，因此不會直接減少來源上的寫入容量負擔。 這項功能不是以寫入密集的工作負載為目標。

讀取複本功能會使用 MySQL 非同步複寫。 此功能不適用於同步複寫案例。 來源與複本之間會有可測量的延遲。 複本上的資料最終會與來源上的資料一致。 請針對可接受此延遲的工作負載使用此功能。

## <a name="create-a-replica"></a>建立複本

如果來源伺服器沒有現有的複本伺服器，則來源將會先重新開機以準備複寫。

當您開始建立複本的工作流程時，系統會建立空白的「適用於 MySQL 的 Azure 資料庫」伺服器。 新伺服器會填入來源伺服器上的資料。 建立時間取決於來源上的資料量，以及上次每週完整備份之後的時間。 時間的範圍可能介於數分鐘到數小時。

> [!NOTE]
> 使用與來源相同的伺服器設定來建立讀取複本。 複本伺服器設定在建立後可以變更。 複本伺服器一律會建立在相同的資源群組、與來源伺服器相同的位置和相同的訂用帳戶中。 如果您想要將複本伺服器建立到不同的資源群組或不同的訂閱，您可以在建立後[移動複本伺服器](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription)。 建議將複本伺服器的設定保留為等於或大於來源的值，以確保複本能夠跟上來源。

了解如何[在 Azure 入口網站中建立讀取複本](how-to-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>連線到複本

建立時，複本會繼承來源伺服器的連接方法。 您無法變更複本的連接方法。 例如，如果來源伺服器具有 **私人存取 (VNet 整合)** 則複本不能是 **公用存取 (允許的 IP 位址)** 。

複本會從來源伺服器繼承系統管理員帳戶。 來源伺服器上的所有使用者帳戶都會複寫到讀取複本。 您只能使用來源伺服器上所提供的使用者帳戶，連接到讀取複本。

您可以使用其主機名稱和有效的使用者帳戶來連線至複本，就像在一般的適用於 MySQL 的 Azure 資料庫彈性伺服器上一樣。 若伺服器名稱為 **myreplica** ，且統管理員使用者名稱為 **myadmin** ，則您可以使用 mysql CLI 來連線到複本：

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

在出現提示時，請輸入使用者帳戶的密碼。

## <a name="monitor-replication"></a>監視複寫

適用於 MySQL 的 Azure 資料庫彈性的伺服器會提供 Azure 監視器的複寫 **延遲（以秒為單位）** 。 此計量僅適用於複本。 在計算此計量時，會使用可於 MySQL 的 `SHOW SLAVE STATUS` 命令中取得的 `seconds_behind_master` 計量。 請設定警示，以在複寫延遲時間接近您工作負載無法接受的值時通知您。

如果您看到複寫延遲增加，請參閱 [疑難排解複寫延遲](./../howto-troubleshoot-replication-latency.md) ，以疑難排解並瞭解可能的原因。

## <a name="stop-replication"></a>停止複寫

您可以停止來源與複本之間的複寫。 當來源伺服器和讀取複本之間的複寫停止時，複本會變成獨立伺服器。 獨立伺服器中的資料是起始「停止複寫」命令時，複本上所包含的可用資料。 獨立伺服器不會跟上來源伺服器的趕上。

當您選擇停止複寫到複本時，它會失去其先前來源和其他複本的所有連結。 來源與其複本之間沒有自動容錯移轉。

> [!IMPORTANT]
> 獨立伺服器無法再次設定為複本。
> 在您停止讀取複本上的複寫之前，請確定該複本上已經有您所需要的所有資料。

了解如何[停止複寫至複本](how-to-read-replicas-portal.md)。

## <a name="failover"></a>容錯移轉

來源與複本伺服器之間沒有自動容錯移轉。 

讀取複本適用于擴充大量讀取的工作負載，而且不是為了符合伺服器的高可用性需求而設計。 來源與複本伺服器之間沒有自動容錯移轉。 停止讀取複本上的複寫，以讀取寫入模式讓它上線，是執行這項手動容錯移轉的方式。

由於複寫是非同步，因此來源與複本之間會有延遲。 延遲量可能會受到一些因素的影響，例如，在來源伺服器上執行的工作負載的繁重程度，以及資料中心之間的延遲。 在大部分的情況下，複本延遲的範圍是幾秒鐘到幾分鐘。 您可以使用計量 *複本延遲* （可用於每個複本）來追蹤實際的複寫延遲。 此計量會顯示上次重新執行交易之後的時間。 我們建議您在一段時間內觀察您的複本延遲，以找出您的平均延遲。 您可以設定複本延遲的警示，如此一來，如果超出預期的範圍，您可以採取動作。

> [!Tip]
> 如果您容錯移轉至複本，從來源取消複本時的延遲將會指出遺失的資料量。

一旦您決定要容錯移轉至複本， 

1. 停止複寫至複本<br/>
   您必須執行此步驟，讓複本伺服器能夠接受寫入。 作為此程式的一部分，複本伺服器將會從來源 delinked。 當您起始停止複寫後，後端進程通常需要大約2分鐘才能完成。 請參閱本文的「 [停止](#stop-replication) 複寫」一節，以瞭解此動作的含意。
    
2. 將您的應用程式指向 (之前的) 複本<br/>
   每一部伺服器都有唯一的連接字串。 更新您的應用程式，使其指向 (之前的) 複本，而不是來源。
    
一旦您的應用程式成功處理讀取和寫入，您就已完成容錯移轉。 當您偵測到問題並完成上述步驟1和2時，您的應用程式所經歷的停機時間將會取決於您的情況。

## <a name="considerations-and-limitations"></a>考量與限制

| 案例 | 限制/考慮 |
|:-|:-|
| 已啟用區域冗余 HA 之伺服器上的複本 | 不支援 |
| 定價 | 執行複本伺服器的成本是以複本伺服器執行所在的區域為基礎 |
| 來源伺服器重新開機 | 當您為沒有現有複本的來源建立複本時，來源會先重新開機以準備複寫。 請考慮這一點，並在離峰期間執行這些作業 |
| 新複本 | 讀取複本會建立為新的適用於 MySQL 的 Azure 資料庫彈性伺服器。 現有伺服器無法設定為複本。 您無法建立另一個讀取複本的複本 |
| 複本設定 | 使用與來源相同的伺服器設定來建立複本。 建立複本之後，您可以從來源伺服器個別變更數個設定：計算世代、虛擬核心、儲存體和備份保留期限。 計算層也可以獨立變更。<br> <br> **重要**  <br> -在來源伺服器設定更新為新值之前，請將複本設定更新為相等或更大的值。 此動作可確保複本可以跟上來源伺服器上所做的變更。 <br/> 建立複本時，連接方法和參數設定會從來源伺服器繼承至複本。 之後，複本的規則就已獨立。 |
| 已停止的複本 | 如果您停止來源伺服器和讀取複本之間的複寫，已停止的複本會變成同時接受讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。 |
| 已刪除來源與獨立伺服器 | 刪除來源伺服器時，會停止所有讀取複本的複寫。 這些複本會自動變成獨立伺服器，並且可以同時接受讀取和寫入。 來源伺服器本身也會遭到刪除。 |
| 使用者帳戶 | 來源伺服器上的使用者會複寫到讀取複本。 您只能使用來源伺服器上的可用使用者帳戶連接到讀取複本。 |
| 伺服器參數 | 若要防止資料不同步，以及避免潛在的資料遺失或損毀，則會在使用讀取複本時，有些伺服器參數會被鎖定而無法更新。 <br> 來源伺服器和複本伺服器上的下列伺服器參數都已鎖定：<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> 複本伺服器上會鎖定 [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) 參數。 <br> 若要更新來源伺服器上的上述其中一個參數，請刪除複本伺服器、更新來源的參數值，然後重新建立複本。 |
| 其他 | -不支援建立複本的複本。 <br> -記憶體中的資料表可能會導致複本不同步。這是 MySQL 複寫技術的限制。 如需詳細資訊，請參閱 [MySQL 參考文件](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) \(英文\)。 <br>-確定來源伺服器資料表具有主鍵。 缺少主鍵可能會導致來源與複本之間的複寫延遲。<br>-請參閱[mysql 檔](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)中的 mysql 複寫限制完整清單 |

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Azure 入口網站來建立及管理讀取複本](how-to-read-replicas-portal.md)
- 了解如何[使用 Azure CLI 來建立及管理讀取複本](how-to-read-replicas-cli.md)
