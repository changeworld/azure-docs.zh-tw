---
title: 使用 Azure Advisor 降低服務成本
description: 使用 Azure Advisor 程式將 Azure 部署的成本最佳化。
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0237feab59551ecab87d78b0d4d66b9fc7b47e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259691"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>使用 Azure Advisor 降低服務成本

Advisor 可找出閒置和未充分利用的資源，協助您減少 Azure 的整體費用並加以最佳化。您可以從 Advisor 儀表板上的 [成本]**** 索引標籤取得成本建議。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>關閉使用量過低的執行個體，或者重新調整其大小或，從而將虛擬機器的費用最佳化 

雖然特定應用程式的設計情境可能導致低使用率，但您通常可以藉由調整虛擬機器的大小和數量來節省費用。 Advisor 高級評估模型考慮在 CPU 利用率最大值的 P95 小於 3% 且網路利用率低於 2% 時關閉虛擬機器。 當可以在較小的 SKU（同一 SKU 系列）或數量較少的實例中安裝當前負載時，虛擬機器被視為正確的大小，這樣當非使用者面對工作負載時，當前負載的利用率不會超過 80%，而面向使用者的工作負載則不超過 40%。 在這裡，通過分析工作負載的 CPU 利用率特徵來確定工作負載的類型。

建議的操作是關閉或調整大小，特定于建議的資源。 Advisor 顯示建議操作（調整大小或關閉）的估計成本節約。 此外，為了調整建議的操作大小，Advisor 提供當前和目標 SKU 資訊。 

如果要更積極地識別未充分利用的虛擬機器，可以根據每個訂閱調整 CPU 利用率規則。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>移除未佈建的 ExpressRoute 線路來降低成本

Advisor 發現 ExpressRoute 線路的提供者狀態為「未佈建」** 已超過一個月，並建議如果您不打算透過連線提供者佈建該線路，請將其刪除。

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>藉由刪除或重新設定閒置虛擬網路閘道來降低成本

Advisor 標識已空閒超過 90 天的虛擬網路閘道。 由於這些閘道是以小時計費，因此，當您不再使用這些閘道時，應考慮重新設定或刪除閘道。 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>購買保留執行個體以省下較隨用隨付多的成本

Advisor 會檢閱您的虛擬機器在過去 30 天的使用量，並判斷購買 Azure 保留是否可為您節省成本。 Advisor 會顯示可能省下最多成本的區域和大小，並顯示購買保留估計省下的成本。 有了 Azure 保留，您可以預先購買基本數量的虛擬機器。 針對其大小和區域與您保留相同的新建或現有 VM，會自動套用折扣。 [深入了解 Azure 保留的 VM 執行個體。](https://azure.microsoft.com/pricing/reserved-vm-instances/)

顧問還將通知您保留的實例，您擁有的實例將在未來 30 天內過期。 它將建議您購買新的預留實例，以避免即用即付定價。

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>刪除未關聯的公共 IP 位址以節省資金

Advisor 標識當前未與 Azure 資源（如負載等化器或 VM）關聯的公共 IP 位址。 這些公共 IP 位址附帶名義費用。 如果您不打算使用它們，刪除它們可以節省成本。

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>刪除失敗的 Azure Data Factory 管線

Azure Advisor 將檢測重複失敗的 Azure 資料工廠管道，並建議您解決問題或刪除故障管道（如果不再需要）。 即使這些管道在發生故障時沒有為您服務，您也會為這些管道收費。 

## <a name="use-standard-snapshots-for-managed-disks"></a>對託管磁片使用標準快照
若要節省 60% 的成本，無論父代磁片的儲存體類型為何，建議您將快照集儲存在標準儲存體中。 此選項是託管磁片快照的預設選項。 Azure 顧問將標識存儲的高級存儲的快照，並建議將快照從高級存儲遷移到標準存儲。 [瞭解有關託管磁片定價的更多](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>利用生命週期管理
Azure Advisor 將利用有關 Azure Blob 存儲物件計數、總大小和事務的情報來檢測一個或多個存儲帳戶是否最適合啟用分層資料的生命週期管理。 它將提示您創建生命週期管理規則，以自動將資料分層到 Cool 或 Archive，以優化存儲成本，同時將資料保留在 Azure Blob 存儲中以進行應用程式相容性。

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何存取 Azure Advisor 中的成本建議

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 從任何頁面搜索並選擇[**顧問**](https://aka.ms/azureadvisordashboard)。

1. 在 **"顧問**"儀表板上，選擇 **"成本**"選項卡。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：
* [Advisor 簡介](advisor-overview.md)
* [入門](advisor-get-started.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [Advisor 高可用性建議](advisor-high-availability-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [顧問卓越運營建議](advisor-operational-excellence-recommendations.md)
