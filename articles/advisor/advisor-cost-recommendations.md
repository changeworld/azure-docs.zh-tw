---
title: 使用 Azure Advisor 降低服務成本
description: 使用 Azure Advisor 程式將 Azure 部署的成本最佳化。
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 3b7e57aa178380ff4cf0561718c292dc1e6c6aa9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448169"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>使用 Azure Advisor 降低服務成本

Azure Advisor 藉由識別閒置和使用量過低的資源，協助您優化並減少整體 Azure 支出。您可以從 Advisor 儀表板上的 [成本]**** 索引標籤取得成本建議。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>關閉使用量過低的執行個體，或者重新調整其大小或，從而將虛擬機器的費用最佳化 

雖然特定應用程式的設計情境可能導致低使用率，但您通常可以藉由調整虛擬機器的大小和數量來節省費用。 

建議的動作會關閉或調整大小，特定于正在評估的資源。

當所有這些陳述都成立時，Advisor 中的 advanced 評估模型會考慮關閉虛擬機器： 
- CPU 使用率的最大值 P95th 小於3%。 
- 在七天的期間內，網路使用率低於2%。
- 記憶體壓力低於臨界值

建議程式會考慮調整虛擬機器的大小，以便在相同 SKU 系列內的較小 SKU (中符合目前的負載) 或較少的實例數目，如下所示：
- 目前的負載不會超過使用者面向之工作負載的80% 使用率。 
- 針對使用者面向的工作負載，負載不會超過40%。 

在此，Advisor 會分析工作負載的 CPU 使用率特性，以決定工作負載的類型。

Advisor 會針對建議的動作顯示預估成本節省：調整大小或關閉。 針對調整大小，Advisor 會提供目前和目標的 SKU 資訊。

如果您想要更積極地識別使用量過低的虛擬機器，您可以根據每個訂用帳戶調整 CPU 使用率規則。

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>使用適當的大小調整，將適用于 mariadb、MySQL 和于 postgresql 伺服器的費用優化 
Advisor 會分析您的使用量，並評估您的適用于 mariadb、MySQL 或于 postgresql 資料庫伺服器資源在過去七天內是否已過延長的時間。 資源使用率低會導致不必要的費用，您可以在不影響效能的情況下進行修正。 若要降低成本並有效率地管理您的資源，建議您將計算大小 (虛擬核心) 減一半。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>移除未佈建的 ExpressRoute 線路來降低成本

Advisor 會識別已處於「 **未布建** 」的「提供者」狀態的 Azure ExpressRoute 線路超過一個月。 如果您不打算透過連線提供者布建線路，建議您刪除線路。

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>藉由刪除或重新設定閒置虛擬網路閘道來降低成本

Advisor 會識別已閒置超過90天的虛擬網路閘道。 因為這些閘道會按小時計費，所以如果您不想再使用這些閘道，則應該考慮重新設定或刪除它們。 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>購買保留執行個體以省下較隨用隨付多的成本

Advisor 會在過去30天內審核您的虛擬機器使用量，以判斷您是否可以購買 Azure 保留來節省成本。 Advisor 會顯示可能節省成本的區域和大小，以及購買保留的預估節省成本。 有了 Azure 保留，您可以預先購買基本數量的虛擬機器。 折扣會自動套用至新的或現有的 Vm，這些 Vm 的大小和區域與您的保留相同。 [深入了解 Azure 保留的 VM 執行個體。](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor 也會通知您保留的實例將在接下來的30天內到期。 建議您購買新的保留實例，以避免隨用隨付定價。

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>購買數個資源類型的保留實例，以節省您的隨用隨付費用

Advisor 會針對下列資源分析過去30天的使用模式，並建議可將成本優化的保留容量購買。

### <a name="azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB 保留容量
Advisor 會分析您過去30天的 Azure Cosmos DB 使用模式，並建議保留容量購買以將成本優化。 使用保留容量，您可以預先購買 Azure Cosmos DB 的每小時使用量，並節省隨用隨付費用。 保留容量是帳單權益，並會自動套用至新的和現有的部署。 Advisor 會使用3年期的保留定價，並推斷過去30天內觀察到的使用模式，計算個別訂用帳戶的節省金額。 共用範圍建議適用于保留容量購買，並可提高節省成本。

### <a name="sql-database-and-sql-managed-instance-reserved-capacity"></a>SQL Database 和 SQL 受控執行個體保留容量
Advisor 會分析過去30天內 SQL Database 和 SQL 受控執行個體使用模式。 然後建議可將成本優化的保留容量購買。 使用保留容量時，您可以預先購買 SQL DB 每小時使用量，並節省您的 SQL 計算成本。 您的 SQL 授權會分開計費，而且不會被保留折扣。 保留容量是帳單權益，並會自動套用至新的和現有的部署。 Advisor 會使用3年期的保留定價，並推斷過去30天內觀察到的使用模式，計算個別訂用帳戶的節省金額。 共用範圍建議適用于保留容量購買，並可提高節省成本。 如需詳細資訊，請參閱 [Azure SQL Database & SQL 受控執行個體保留容量](https://docs.microsoft.com/azure/azure-sql/database/reserved-capacity-overview)。

### <a name="app-service-stamp-fee-reserved-capacity"></a>App Service 戳記費用保留容量
Advisor 會分析過去30天內 Azure App Service 隔離環境的戳記費用使用模式，並建議可將成本優化的保留容量購買。 使用保留容量時，您可以預先購買隔離環境戳記費用的每小時使用量，並節省隨用隨付費用。 請注意，保留容量僅適用于戳記費用，無法 App Service 實例。 保留容量是帳單權益，並會自動套用至新的和現有的部署。 建議程式會根據過去30天的使用量模式，使用3年期的保留定價來計算個別訂用帳戶的儲存估價。

### <a name="blob-storage-reserved-capacity"></a>Blob 儲存體保留容量
Advisor 會分析您的 Azure Blob 儲存體，並在過去30天內 Azure Data Lake 儲存體使用量。 然後，它會計算可將成本優化的保留容量購買。 使用保留容量時，您可以預先購買每小時使用量，並節省您目前的隨選成本。 Blob 儲存體保留容量僅適用于 Azure Blob 一般用途 v2 和 Azure Data Lake Storage Gen2 帳戶上所儲存的資料。 保留容量是帳單權益，並會自動套用至新的和現有的部署。 Advisor 會使用3年期的保留定價和過去30天觀察到的使用模式，計算個別訂用帳戶的節省金額。 共用範圍建議適用于保留容量購買，並可提高節省成本。

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>適用于 mariadb、MySQL 和于 postgresql 保留容量
Advisor 會分析您過去30天內適用於 MariaDB 的 Azure 資料庫、適用於 MySQL 的 Azure 資料庫和適用於 PostgreSQL 的 Azure 資料庫的使用模式。 然後建議可將成本優化的保留容量購買。 使用保留容量，您可以預先購買適用于 mariadb、MySQL 和于 postgresql 的每小時使用量，並節省您目前的成本。 保留容量是帳單權益，並會自動套用至新的和現有的部署。 Advisor 會使用3年期的保留定價和過去30天觀察到的使用模式，計算個別訂用帳戶的節省金額。 共用範圍建議適用于保留容量購買，並可提高節省成本。

### <a name="azure-synapse-analytics-formerly-sql-data-warehouse-reserved-capacity"></a>Azure Synapse Analytics (先前的 SQL 資料倉儲) 保留容量
Advisor 會分析您過去30天內的 Azure Synapse Analytics 使用模式，並建議可將成本優化的保留容量購買。 使用保留容量時，您可以預先購買 Synapse Analytics 的每小時使用量，並節省隨選成本。 保留容量是帳單權益，並會自動套用至新的和現有的部署。 Advisor 會使用3年期的保留定價和過去30天觀察到的使用模式，計算個別訂用帳戶的節省金額。 共用範圍建議適用于保留容量購買，並可提高節省成本。

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>刪除未關聯的公用 IP 位址以節省成本

Advisor 會識別未與 Azure 資源（例如負載平衡器和 Vm）相關聯的公用 IP 位址。 名義費用會與這些公用 IP 位址相關聯。 如果您不打算使用它們，您可以藉由刪除它們來節省成本。

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>刪除失敗的 Azure Data Factory 管線

Advisor 會偵測重複失敗的 Azure Data Factory 管線。 它會建議您解決問題，或在不需要時刪除管線。 即使這些管線在失敗時不會提供您的服務，您仍需支付這些管線的費用。

## <a name="use-standard-snapshots-for-managed-disks"></a>針對受控磁片使用標準快照集
為了節省60% 的成本，建議您將快照集儲存在標準儲存體中，不論父磁片的儲存體類型為何。 此選項是受控磁片快照集的預設選項。 Advisor 會識別儲存在 premium 儲存體中的快照集，並建議從 premium 遷移至標準儲存體。 [深入瞭解受控磁片定價。](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="use-lifecycle-management"></a>使用生命週期管理
藉由使用 Azure Blob 儲存體物件計數、總大小和交易的相關情報，Advisor 會偵測您是否應該啟用生命週期管理，以將一或多個儲存體帳戶上的資料分層。 它會提示您建立生命週期管理規則，以將您的資料自動分層到非經常性或封存儲存體，以將您的儲存體成本優化，同時保留 Azure Blob 儲存體中的資料以提供應用程式相容性。

## <a name="create-an-ephemeral-os-disk-recommendation"></a>建立暫時 OS 磁碟建議
[暫時 OS 磁片](../virtual-machines/ephemeral-os-disks.md) 可讓您： 
- 節省 OS 磁片的儲存成本。 
- 為 OS 磁片取得較低的讀取/寫入延遲。 
- 將作業系統 (和暫存磁片) 重設為其原始狀態，以取得更快的 VM 重新安裝映射作業。

最好將暫時性 OS 磁片用於存留期較短的 IaaS Vm 或具有無狀態工作負載的 Vm。 Advisor 針對可從暫時性 OS 磁片獲益的資源提供建議。

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>減少 Azure 資料總管資料表快取期間 (原則) 以進行叢集成本優化 (預覽) 
Advisor 會識別減少資料表快取原則的資源，以釋出具有低 CPU 使用率、記憶體和高快取大小設定的 Azure 資料總管叢集節點。

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何在 Azure 建議程式中存取成本建議

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 從任何頁面搜尋並選取 [ [**Advisor**](https://aka.ms/azureadvisordashboard) ]。

1. 在 **Advisor** 儀表板上，選取 [ **成本** ] 索引標籤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：
* [Advisor 簡介](advisor-overview.md)
* [Advisor 分數](azure-advisor-score.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 效能建議](advisor-performance-recommendations.md)
* [Advisor 高可用性建議](advisor-high-availability-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [Advisor 操作卓越建議](advisor-operational-excellence-recommendations.md)
