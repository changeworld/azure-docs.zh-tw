---
title: 使用 Azure Advisor 降低服務成本
description: 使用 Azure Advisor 程式將 Azure 部署的成本最佳化。
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 13e7b1d7c6b0fe342020c40e1bb4abeba97d18bb
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788088"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>使用 Azure Advisor 降低服務成本

Advisor 可找出閒置和未充分利用的資源，協助您減少 Azure 的整體費用並加以最佳化。您可以從 Advisor 儀表板上的 [成本]**** 索引標籤取得成本建議。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>關閉使用量過低的執行個體，或者重新調整其大小或，從而將虛擬機器的費用最佳化 

雖然特定應用程式的設計情境可能導致低使用率，但您通常可以藉由調整虛擬機器的大小和數量來節省費用。 當 CPU 使用率的最大值 P95th 小於3%，且網路使用率在7天內低於2% 時，Advisor advanced 評估模型會將虛擬機器視為關閉。 當可能符合較小 SKU （在相同 SKU 系列內）的目前負載，或較小的實例數目時，會將虛擬機器視為正確的大小，如此一來，當非使用者面向的工作負載時，目前的負載不會超過80% 的使用率，而當使用者對應的工作負載時，則不超過40%。 在這裡，工作負載的類型是藉由分析工作負載的 CPU 使用率特性來決定。

建議的動作是 [關閉] 或 [調整大小]，特別是建議的資源。 Advisor 會顯示建議動作的預估成本節約-調整大小或關機。 此外，若要調整建議的動作大小，Advisor 會提供目前和目標的 SKU 資訊。 

如果您想要更積極地找出使用量過低的虛擬機器，您可以根據每個訂用帳戶來調整 CPU 使用率規則。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>移除未佈建的 ExpressRoute 線路來降低成本

Advisor 發現 ExpressRoute 線路的提供者狀態為「未佈建」** 已超過一個月，並建議如果您不打算透過連線提供者佈建該線路，請將其刪除。

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>藉由刪除或重新設定閒置虛擬網路閘道來降低成本

Advisor 會識別已閒置超過90天的虛擬網路閘道。 由於這些閘道是以小時計費，因此，當您不再使用這些閘道時，應考慮重新設定或刪除閘道。 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>購買保留執行個體以省下較隨用隨付多的成本

Advisor 會檢閱您的虛擬機器在過去 30 天的使用量，並判斷購買 Azure 保留是否可為您節省成本。 Advisor 會顯示可能省下最多成本的區域和大小，並顯示購買保留估計省下的成本。 有了 Azure 保留，您可以預先購買基本數量的虛擬機器。 針對其大小和區域與您保留相同的新建或現有 VM，會自動套用折扣。 [深入了解 Azure 保留的 VM 執行個體。](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor 也會通知您，您擁有的保留實例將在接下來的30天內過期。 建議您購買新的保留實例，以避免付費隨用隨付定價。

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>刪除未關聯的公用 IP 位址以節省成本

Advisor 會識別目前未與 Azure 資源相關聯的公用 IP 位址，例如負載平衡器或 Vm。 這些公用 IP 位址附有名義費用。 如果您不打算使用它們，將其刪除可能會導致成本節約。

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>刪除失敗的 Azure Data Factory 管線

Azure Advisor 會偵測重複失敗的 Azure Data Factory 管線，並建議您解決問題，或在不再需要失敗的管線時將其刪除。 這些管線即使在失敗時仍未提供服務，也會向您收取費用。 

## <a name="use-standard-snapshots-for-managed-disks"></a>使用受控磁碟的標準快照集
若要節省 60% 的成本，無論父代磁片的儲存體類型為何，建議您將快照集儲存在標準儲存體中。 此選項是受控磁碟快照集的預設選項。 Azure Advisor 會識別儲存進階儲存體的快照集，並建議您將快照集從 Premium 遷移至標準儲存體。 [深入瞭解受控磁片定價](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>利用生命週期管理
Azure Advisor 將會利用有關 Azure blob 儲存體物件計數、總大小和交易的情報，以偵測是否有一或多個儲存體帳戶最適合用來啟用將生命週期管理分層資料的階層。 它會提示您建立生命週期管理規則，將您的資料自動分層到非經常性或封存，以將您的儲存體成本優化，同時保留 Azure blob 儲存體中的資料以達到應用程式相容性。

## <a name="create-an-ephemeral-os-disk-recommendation"></a>建立暫時的 OS 磁片建議
有了[暫時的 Os 磁片](https://docs.microsoft.com/azure/virtual-machines/windows/ephemeral-os-disks)，客戶可享有下列優點：節省 OS 磁片的儲存成本。 為 OS 磁片取得較低的讀取/寫入延遲。 將 OS （和暫存磁片）重設為其原始狀態，以更快速地進行 VM 重新安裝映射操作。 針對短期的 IaaS Vm 或具有無狀態工作負載的 Vm，使用暫時 OS 磁片是更下班時間的。 Advisor 有資源的建議，可利用暫時的 OS 磁片來獲得好處。 

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何存取 Azure Advisor 中的成本建議

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 從任何頁面搜尋並選取 [ [**Advisor**](https://aka.ms/azureadvisordashboard) ]。

1. 在**Advisor**儀表板上，選取 [**成本**] 索引標籤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：
* [Advisor 簡介](advisor-overview.md)
* [開始使用](advisor-get-started.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [Advisor 高可用性建議](advisor-high-availability-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [Advisor 操作卓越建議](advisor-operational-excellence-recommendations.md)
