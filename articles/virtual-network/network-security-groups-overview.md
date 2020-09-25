---
title: Azure 網路安全性群組總覽
titlesuffix: Azure Virtual Network
description: 深入瞭解網路安全性群組。 網路安全性群組可協助您篩選 Azure 資源之間的網路流量。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: kumud
ms.reviewer: kumud
ms.custom: contperfq1
ms.openlocfilehash: 76f3ba000a9bde4a306d19e8281ebeb41f1616e5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335862"
---
# <a name="network-security-groups"></a>網路安全性群組
<a name="network-security-groups"></a>

您可以使用 Azure 網路安全性群組來篩選與 Azure 虛擬網路中的 Azure 資源之間的網路流量。 網路安全性群組包含 [安全性規則](#security-rules) ，可允許或拒絕來自數種 Azure 資源類型的輸入網路流量或輸出網路流量。 您可以為每個規則指定來源和目的地、連接埠及通訊協定。

本文說明網路安全性群組規則的屬性、套用的 [預設安全性規則](#default-security-rules) ，以及您可以修改以建立增強型 [安全性規則](#augmented-security-rules)的規則內容。

## <a name="security-rules"></a><a name="security-rules"></a> 安全性規則

視 Azure 訂用帳戶[限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)而定，網路安全性群組可包含零個或多個規則。 每個規則都會指定下列屬性：

|屬性  |說明  |
|---------|---------|
|名稱|網路安全性群組內的唯一名稱。|
|優先順序 | 100 到 4096 之間的數字。 系統會依照優先權順序處理規則，較低的數字會在較高的數字之前處理，因為較低的數字具有較高的優先順序。 一旦流量符合規則，處理就會停止。 因此，如果存在較低優先順序 (較高數字) 的規則具有與較高優先順序之規則相同的屬性，則不會進行處理。|
|來源或目的地| 任何或個別的 IP 位址、無類別網域間路由 (CIDR) 區塊 (例如 10.0.0.0/24)、服務標籤或應用程式安全性群組。 當您指定 Azure 資源的位址時，可以指定指派給資源的私人 IP 位址。 在 Azure 針對輸入流量將公用 IP 位址轉譯為私人 IP 位址之後，和 Azure 針對輸出流量將私人 IP 位址轉譯為公用 IP 位址之前，網路安全性群組會進行處理。 . 指定範圍、服務標籤或應用程式安全性群組，可讓您建立較少的安全性規則。 指定多個個別 IP 位址和範圍 (您無法在規則中指定多個服務標籤或應用程式) 群組的功能，稱為增強型 [安全性規則](#augmented-security-rules)。 增強型安全性規則只可以在透過 Resource Manager 部署模型建立的網路安全性群組中建立。 您無法在透過傳統部署模型建立的網路安全性群組中指定多個 IP 位址與 IP 位址範圍。|
|通訊協定     | TCP、UDP、ICMP 或任何。|
|方向| 規則適用於連入還是連出流量。|
|連接埠範圍     |您可以指定個別連接埠或連接埠範圍。 例如，您可以指定 80 或 10000-10005。 指定範圍可讓您建立較少的安全性規則。 增強型安全性規則只可以在透過 Resource Manager 部署模型建立的網路安全性群組中建立。 您無法在透過傳統部署模型建立之網路安全性群組的相同安全性規則中指定多個連接埠與連接埠範圍。   |
|動作     | 允許或拒絕        |

系統會依優先順序使用 5 項 Tuple 資訊 (來源、來源連接埠、目的地、目的地連接埠和通訊協定) 來評估網路安全性群組的安全性規則，以允許或拒絕流量。 您可能不會建立兩個具有相同優先順序和方向的安全性規則。 系統會為現有連線建立流程記錄。 允許或拒絕通訊都會以此流程記錄的連線狀態為依據。 流程記錄可讓網路安全性群組成為具狀態的形式。 如果您將輸出安全性規則指定至任何透過連接埠 80 (舉例來說) 的位址，則不必為了回應輸出流量來指定輸入安全性規則。 如果在外部起始通訊，您只需要指定輸入安全性規則。 反之亦然。 如果允許透過連接埠傳送輸入流量，則不必指定輸出安全性規則來透過連接埠回應流量。

當您移除啟用流量的安全性規則時，現有的連線不會中斷。 當連線停止，且兩個方向至少有數分鐘都沒有流量時，流量即會中斷。

您可以在網路安全性群組中建立的安全性規則數量會有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

### <a name="default-security-rules"></a><a name="default-security-rules"></a> 預設安全性規則

Azure 會在您建立的每個網路安全性群組中，建立下列預設規則：

#### <a name="inbound"></a>輸入

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|優先順序|來源|來源連接埠|Destination|目的地連接埠|通訊協定|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|任意|Allow|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|優先順序|來源|來源連接埠|Destination|目的地連接埠|通訊協定|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|任意|Allow|

##### <a name="denyallinbound"></a>DenyAllInbound

|優先順序|來源|來源連接埠|Destination|目的地連接埠|通訊協定|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|任意|拒絕|

#### <a name="outbound"></a>輸出

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|優先順序|來源|來源連接埠| Destination | 目的地連接埠 | 通訊協定 | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | 任意 | Allow |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|優先順序|來源|來源連接埠| Destination | 目的地連接埠 | 通訊協定 | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | 任意 | Allow |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|優先順序|來源|來源連接埠| Destination | 目的地連接埠 | 通訊協定 | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | 任意 | 拒絕 |

在 [來源]**** 和 [目的地]**** 欄中，*VirtualNetwork*、*AzureLoadBalancer*和 *Internet* 都是[服務標籤](service-tags-overview.md)，而不是 IP 位址。 在 [通訊協定] 資料行中， **任何** 包含 TCP、UDP 和 ICMP。 建立規則時，您可以指定 TCP、UDP、ICMP 或任何。 [來源]**** 和 [目的地]**** 欄中的 *0.0.0.0/0* 代表所有位址。 用戶端（例如 Azure 入口網站、Azure CLI 或 PowerShell）可以使用 * 或任何適用于此運算式的。
 
您無法移除預設規則，但可以建立較高優先順序的規則來覆寫預設規則。

### <a name="augmented-security-rules"></a><a name="augmented-security-rules"></a> 增強型安全性規則

增強型安全性規則可簡化虛擬網路的安全性定義，讓您定義更大且複雜的網路安全性原則 (但規則比較少)。 您可以將多個連接埠和多個明確 IP 位址與範圍，合併成易於了解的單一安全性規則。 在規則的來源、目的地和連接埠欄位中使用增強型規則。 若要簡化安全性規則定義的維護，請結合增強型安全性規則與 [服務](service-tags-overview.md) 標籤或 [應用程式安全性群組](#application-security-groups)。 您可以在規則中指定的位址、範圍和連接埠數量會有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

#### <a name="service-tags"></a>服務標籤

服務標籤代表來自指定 Azure 服務的一組 IP 位址前置詞。 它有助於將頻繁更新網路安全性規則的複雜性降至最低。

如需詳細資訊，請參閱 [Azure 服務標記](service-tags-overview.md)。 如需如何使用儲存體服務標記來限制網路存取的範例，請參閱 [限制對 PaaS 資源的網路存取](tutorial-restrict-network-access-to-resources.md)。

#### <a name="application-security-groups"></a>應用程式安全性群組

應用程式安全性群組可讓您將網路安全性設定為應用程式結構的自然擴充功能，讓您將虛擬機器分組，並定義以這些群組為基礎的網路安全性原則。 您可以大規模重複使用您的安全性原則，而不需進行明確 IP 位址的手動維護。 若要深入瞭解，請參閱 [應用程式安全性群組](application-security-groups.md)。

## <a name="azure-platform-considerations"></a>Azure 平台的考量

- **主機節點的虛擬 IP**：基本基礎結構服務（例如 DHCP、DNS、IMDS 和健康情況監視）是透過虛擬化主機 IP 位址168.63.129.16 和169.254.169.254 提供。 這些 IP 位址屬於 Microsoft，而且是針對此目的唯一用於所有地區的虛擬 IP。 有效的安全性規則和有效的路由不會包含這些平臺規則。 若要覆寫此基本基礎結構通訊，您可以使用網路安全性群組規則上的下列 [服務](service-tags-overview.md) 標籤來建立安全性規則，以拒絕流量： AzurePlatformDNS、AzurePlatformIMDS、AzurePlatformLKM。 瞭解如何 [診斷網路流量篩選](diagnose-network-traffic-filter-problem.md) 及 [診斷網路路由](diagnose-network-routing-problem.md)。
- **授權 (金鑰管理服務)**：必須授權在虛擬機器中執行的 Windows 映像。 若要確保授權，授權要求會傳送至處理此類查詢的金鑰管理服務主機伺服器。 此要求是透過連接埠 1688 輸出。 若為使用[預設路由 0.0.0.0/0](virtual-networks-udr-overview.md#default-route)組態的部署，將會停用此平台規則。
- **負載平衡集區中的虛擬機器**：套用的來源連接埠和位址範圍是來自原始電腦，而不是負載平衡器。 目的地連接埠和位址範圍屬於目的地電腦，而不是負載平衡器。
- **Azure 服務執行個體**：虛擬網路子網路中會部署數個 Azure 服務的執行個體，例如 HDInsight、應用程式服務環境及虛擬機器擴展集。 如需您可以部署到虛擬網路的完整服務清單，請參閱 [Azure 服務的虛擬網路](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)。 將網路安全性群組套用至部署資源的子網路之前，請先確定您熟悉每個服務的連接埠需求。 如果您拒絕服務所需要的連接埠，服務就無法正常運作。
- **傳送外寄電子郵件**：Microsoft 建議您利用已驗證的 SMTP 轉送服務 (通常透過 TCP 連接埠 587 連線，但也可透過其他連接埠連線)，從 Azure 虛擬機器傳送電子郵件。 SMTP 轉送服務是專為寄件者信譽所設計，可將第三方電子郵件提供者拒絕訊息的可能性降到最低。 這類 SMTP 轉送服務包括但不限於 Exchange Online Protection 和 SendGrid。 不論您的訂用帳戶類型為何，在 Azure 中使用 SMTP 轉送服務不受限制。 

  如果您在 2017 年 11 月 15 日前建立了 Azure 訂用帳戶，除了能夠使用 SMTP 轉送服務，您還可以直接透過 TCP 連接埠 25 傳送電子郵件。 如果您在 2017 年 11 月 15 日後建立了訂用帳戶，您可能無法直接透過連接埠 25 傳送電子郵件。 透過連接埠 25 的連出通訊行為取決於您擁有的訂用帳戶類型，如下所示：

     - **Enterprise 合約**：允許輸出通訊埠 25 通訊。 您可以直接從虛擬機器傳送輸出電子郵件給外部電子郵件提供者，而不受 Azure 平臺的限制。 
     - **隨用隨付：** 所有資源的輸出連接埠 25 通訊都遭到封鎖。 如果您需要將電子郵件從虛擬機器直接傳送給外部電子郵件提供者 (不使用已驗證的 SMTP 轉送)，可以提出移除限制的要求。 要求是在 Microsoft 的斟酌之下審查與核准，而且只會在執行反詐騙檢查之後授權。 若要提出要求，請開啟問題類型為 [技術]**、[虛擬網路連線]**、[無法傳送電子郵件 (SMTP/連接埠 25)]** 的支援案例。 在您的支援案例中，請包含訂用帳戶需要將電子郵件直接傳送到郵件提供者，而不需經過已驗證 SMTP 轉送之原因的詳細資料。 如果您的訂用帳戶獲得豁免，則只有在豁免日期之後建立的虛擬機器能夠透過連接埠 25 對外通訊。
     - **MSDN、Azure Pass、Azure in Open、Education、BizSpark 和免費試用**：所有資源的輸出連接埠 25 通訊都遭到封鎖。 無法進行任何移除限制的要求，因為要求未獲授權。 如果您必須從虛擬機器傳送電子郵件，就必須使用 SMTP 轉送服務。
     - **雲端服務提供者**：透過雲端服務提供者使用 Azure 資源的客戶，可以建立其雲端服務提供者的支援案例，以及在安全的 SMTP 轉送無法使用時，要求提供者代表他們建立解除封鎖案例。

  如果 Azure 允許您透過連接埠 25 傳送電子郵件，Microsoft 無法保證電子郵件提供者會接受您虛擬機器所發出的內送電子郵件。 如果特定提供者拒絕來自虛擬機器的郵件，請直接與提供者合作以解決任何訊息傳遞或垃圾郵件篩選問題，或使用已驗證的 SMTP 轉送服務。

## <a name="next-steps"></a>後續步驟

* 若要瞭解哪些 Azure 資源可以部署至虛擬網路，並有與其相關聯的網路安全性群組，請參閱 [Azure 服務的虛擬網路整合](virtual-network-for-azure-services.md)
* 若要瞭解如何使用網路安全性群組來評估流量，請參閱 [網路安全性群組的運作方式](network-security-group-how-it-works.md)。
* 如果您從未建立過網路安全性群組，可以完成快速[教學課程](tutorial-filter-network-traffic.md)，以取得一些建立體驗。
* 如果您熟悉網路安全性群組，並且有管理需求，請參閱[管理網路安全性群組](manage-network-security-group.md)。 
* 如果您有通訊問題，因而需要對網路安全性群組進行疑難排解，請參閱[診斷虛擬機器網路流量篩選問題](diagnose-network-traffic-filter-problem.md)。 
* 瞭解如何啟用 [網路安全性群組流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ，以分析與具有相關聯網絡安全性群組之資源之間的網路流量。
