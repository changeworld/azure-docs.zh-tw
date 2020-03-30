---
title: Azure 網路安全性群組概述
titlesuffix: Azure Virtual Network
description: 瞭解網路安全性群組。 網路安全性群組可説明您篩選 Azure 資源之間的網路流量。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 8f3497f113981ae563023750ad8979c88c640f5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123342"
---
# <a name="network-security-groups"></a>網路安全性群組
<a name="network-security-groups"></a>

您可以透過網路安全性群組，篩選在 Azure 虛擬網路中進出 Azure 資源的網路流量。 網路安全性群組包含安全性規則，可允許或拒絕進出於多種 Azure 資源類型的輸入和輸出網路流量。 若要了解虛擬網路中可部署哪些 Azure 資源，並使這些資源與網路安全性群組產生關聯，請參閱 [Azure 服務的虛擬網路整合](virtual-network-for-azure-services.md)。 您可以為每個規則指定來源和目的地、連接埠及通訊協定。

本文將說明網路安全性群組的概念，協助您有效地使用此功能。 如果您從未建立過網路安全性群組，可以完成快速[教學課程](tutorial-filter-network-traffic.md)，以取得一些建立體驗。 如果您熟悉網路安全性群組，並且有管理需求，請參閱[管理網路安全性群組](manage-network-security-group.md)。 如果您有通訊問題，因而需要對網路安全性群組進行疑難排解，請參閱[診斷虛擬機器網路流量篩選問題](diagnose-network-traffic-filter-problem.md)。 您可以啟用[網路安全性群組流程記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，針對與網路安全性群組相關聯的資源，分析進出其中的網路流量。

## <a name="security-rules"></a>安全性規則

視 Azure 訂用帳戶[限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)而定，網路安全性群組可包含零個或多個規則。 每個規則都會指定下列屬性：

|屬性  |說明  |
|---------|---------|
|名稱|網路安全性群組中的唯一名稱。|
|優先順序 | 100 和 4096 之間的數字。 系統會依照優先權順序處理規則，較低的數字會在較高的數字之前處理，因為較低的數字具有較高的優先順序。 一旦流量符合規則，處理就會停止。 因此，如果存在較低優先順序 (較高數字) 的規則具有與較高優先順序之規則相同的屬性，則不會進行處理。|
|來源或目的地| 任何或個別的 IP 位址、無類別網域間路由 (CIDR) 區塊 (例如 10.0.0.0/24)、[服務標籤](service-tags-overview.md)或[應用程式安全性群組](#application-security-groups)。 當您指定 Azure 資源的位址時，可以指定指派給資源的私人 IP 位址。 在 Azure 針對輸入流量將公用 IP 位址轉譯為私人 IP 位址之後，和 Azure 針對輸出流量將私人 IP 位址轉譯為公用 IP 位址之前，網路安全性群組會進行處理。 深入了解 Azure [IP 位址](virtual-network-ip-addresses-overview-arm.md)。 指定範圍、服務標籤或應用程式安全性群組，可讓您建立較少的安全性規則。 在規則中指定多個單獨的 IP 位址和範圍（無法指定多個服務標記或應用程式組）的能力稱為[增強安全規則](#augmented-security-rules)。 增強型安全性規則只可以在透過 Resource Manager 部署模型建立的網路安全性群組中建立。 您無法在透過傳統部署模型建立的網路安全性群組中指定多個 IP 位址與 IP 位址範圍。 瞭解有關[Azure 部署模型](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的更多。|
|通訊協定     | TCP、UDP、ICMP 或任何。|
|方向| 規則是否套用至輸入或輸出流量。|
|連接埠範圍     |您可以指定個別連接埠或連接埠範圍。 例如，您可以指定 80 或 10000-10005。 指定範圍可讓您建立較少的安全性規則。 增強型安全性規則只可以在透過 Resource Manager 部署模型建立的網路安全性群組中建立。 您無法在透過傳統部署模型建立之網路安全性群組的相同安全性規則中指定多個連接埠與連接埠範圍。   |
|動作     | 允許或拒絕        |

系統會依優先順序使用 5 項 Tuple 資訊 (來源、來源連接埠、目的地、目的地連接埠和通訊協定) 來評估網路安全性群組的安全性規則，以允許或拒絕流量。 系統會為現有連線建立流程記錄。 允許或拒絕通訊都會以此流程記錄的連線狀態為依據。 流程記錄可讓網路安全性群組成為具狀態的形式。 如果您將輸出安全性規則指定至任何透過連接埠 80 (舉例來說) 的位址，則不必為了回應輸出流量來指定輸入安全性規則。 如果在外部起始通訊，您只需要指定輸入安全性規則。 反之亦然。 如果允許透過連接埠傳送輸入流量，則不必指定輸出安全性規則來透過連接埠回應流量。
當您移除啟用流量的安全性規則時，現有的連線不會中斷。 當連線停止，且兩個方向至少有數分鐘都沒有流量時，流量即會中斷。

您可以在網路安全性群組中建立的安全性規則數量會有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

### <a name="default-security-rules"></a>預設安全性規則

Azure 會在您建立的每個網路安全性群組中，建立下列預設規則：

#### <a name="inbound"></a>輸入

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|優先順序|來源|來源連接埠|Destination|目的地連接埠|通訊協定|存取|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|任意|Allow|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|優先順序|來源|來源連接埠|Destination|目的地連接埠|通訊協定|存取|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|任意|Allow|

##### <a name="denyallinbound"></a>DenyAllInbound

|優先順序|來源|來源連接埠|Destination|目的地連接埠|通訊協定|存取|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|任意|拒絕|

#### <a name="outbound"></a>輸出

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|優先順序|來源|來源連接埠| Destination | 目的地連接埠 | 通訊協定 | 存取 |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | 任意 | Allow |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|優先順序|來源|來源連接埠| Destination | 目的地連接埠 | 通訊協定 | 存取 |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | 任意 | Allow |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|優先順序|來源|來源連接埠| Destination | 目的地連接埠 | 通訊協定 | 存取 |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | 任意 | 拒絕 |

在 [來源]**** 和 [目的地]**** 欄中，*VirtualNetwork*、*AzureLoadBalancer*和 *Internet* 都是[服務標籤](service-tags-overview.md)，而不是 IP 位址。 在協定列中，"**任意"** 包含 TCP、UDP 和 ICMP。 創建規則時，可以指定 TCP、UDP、ICMP 或 Any。 [來源]**** 和 [目的地]**** 欄中的 *0.0.0.0/0* 代表所有位址。 Azure 門戶、Azure CLI 或 PowerShell 等用戶端可以為此運算式使用 * 或任何。
 
您無法移除預設規則，但可以建立較高優先順序的規則來覆寫預設規則。

### <a name="augmented-security-rules"></a>增強型安全性規則

增強型安全性規則可簡化虛擬網路的安全性定義，讓您定義更大且複雜的網路安全性原則 (但規則比較少)。 您可以將多個連接埠和多個明確 IP 位址與範圍，合併成易於了解的單一安全性規則。 在規則的來源、目的地和連接埠欄位中使用增強型規則。 為了簡化安全規則定義的維護，請將增強安全規則與[服務標記](service-tags-overview.md)或[應用程式安全性群組](#application-security-groups)相結合。 您可以在規則中指定的位址、範圍和連接埠數量會有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

#### <a name="service-tags"></a>服務標籤

服務標記表示給定 Azure 服務的一組 IP 位址首碼。 它有助於將網路安全規則頻繁更新的複雜性降至最低。

有關詳細資訊，請參閱[Azure 服務標記](service-tags-overview.md)。 有關如何使用存儲服務標記限制網路訪問的示例，請參閱[限制網路訪問 PaaS 資源](tutorial-restrict-network-access-to-resources.md)。

#### <a name="application-security-groups"></a>應用程式安全性群組

應用程式安全性群組可讓您將網路安全性設定為應用程式結構的自然擴充功能，讓您將虛擬機器分組，並定義以這些群組為基礎的網路安全性原則。 您可以大規模重複使用您的安全性原則，而不需進行明確 IP 位址的手動維護。 要瞭解更多資訊，請參閱[應用程式安全性群組](application-security-groups.md)。

## <a name="how-traffic-is-evaluated"></a>評估流量的方式

您可以將數個 Azure 服務的資源部署到 Azure 虛擬網路。 如需完整清單，請參閱[可以部署至虛擬網路的服務](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)。 您可以將零個或一個網路安全性群組關聯至每個虛擬網路[子網路](virtual-network-manage-subnet.md#change-subnet-settings)，以及虛擬機器中的[網路介面](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)。 您可以將相同的網路安全性群組關聯至所需數量的子網路和網路介面。

下圖以不同案例說明網路安全性群組可如何部署，以允許網路流量透過 TCP 連接埠 80 來進出網際網路：

![NSG 處理](./media/security-groups/nsg-interaction.png)

請參考上圖及下列文字，以了解 Azure 如何處理網路安全性群組的輸入和輸出規則：

### <a name="inbound-traffic"></a>輸入流量

針對輸入流量，Azure 會先針對與子網路相關聯的網路安全性群組，處理其中的規則 (如果有的話)，然後再針對與網路介面相關聯的網路安全性群組，處理其中的規則 (如果有的話)。

- **VM1**：NSG1** 中的安全性規則會進行處理，因為它與 Subnet1** 和 VM1** 相關聯，並且位於 Subnet1** 中。 除非您已建立一個規則來允許連接埠 80 的輸入，否則流量會遭到 [DenyAllInbound](#denyallinbound) 預設安全性規則拒絕，並永遠不會由 NSG2** 進行評估，因為 NSG2** 與網路介面相關聯。 如果 NSG1** 具有允許連接埠 80 的安全性規則，流量接著會由 NSG2** 進行處理。 若要允許流量從連接埠 80 輸入虛擬機器，則 NSG1** 和 NSG2** 都必須有規則來允許從網際網路輸入流量的連接埠 80。
- **VM2**：在 NSG1** 中的規則會進行處理，因為VM2** 也位於 Subnet1** 中。 由於 VM2** 沒有與其網路介面相關聯的網路安全性群組，因此會接收允許通過 NSG1** 的所有流量，或拒絕所有 NSG1** 拒絕的流量。 如果網路安全性群組與子網路相關聯，則相同子網路中的所有資源會一起接收或拒絕流量。
- **VM3**：由於沒有任何網路安全性群組與 Subnet2** 相關聯，流量會允許進入子網路並由NSG2 ** 處理流量，因為 NSG2** 與連結至 VM3** 的網路介面相關聯。
- **VM4**：流量會允許進入 VM4**，因為網路安全性群組未與 Subnet3** 或虛擬機器中的網路介面相關聯。 如果沒有任何網路安全性群組與子網路和網路介面相關聯，則所有網路流量都可以通過子網路和網路介面。

### <a name="outbound-traffic"></a>輸出流量

針對輸出流量，Azure 會先針對與網路介面相關聯的網路安全性群組，處理其中的規則 (如果有的話)，然後再針對與子網路相關聯的網路安全性群組，處理其中的規則 (如果有的話)。

- **VM1**：NSG2** 中的安全性規則會進行處理。 除非您建立安全性規則來拒絕向網際網路輸出流量的連接埠 80，否則 NSG1** 和 NSG2** 中的 [AllowInternetOutbound](#allowinternetoutbound) 預設安全性規則會允許流量通過。 如果 NSG2** 具有拒絕連接埠 80 的安全性規則，則流量會遭到拒絕，且永遠不會由 NSG1** 進行評估。 若要拒絕流量從連接埠 80 輸出虛擬機器，其中一個網路安全性群組或兩個網路安全性群組必須有規則來拒絕將流量流向網際網路的連接埠 80。
- **VM2**：所有流量都會通過網路介面流向子網路，因為連結到 VM2** 的網路介面沒有與網路安全性群組相關聯。 NSG1** 中的規則會進行處理。
- **VM3**：如果 NSG2** 具有拒絕連接埠 80 的安全性規則，則流量會遭到拒絕。 如果 NSG2** 有允許連接埠 80 的安全性規則，則連接埠 80 允許輸出流量到網際網路，因為沒有與 Subnet2** 相關聯的網路安全性群組。
- **VM4**：所有網路流量會允許從 VM4** 輸出，因為網路安全性群組未與連結至虛擬機器的網路介面或 Subnet3** 相關聯。


### <a name="intra-subnet-traffic"></a>子網內流量

請務必注意，與子閘道聯的 NSG 中的安全規則可能會影響其內部 VM 之間的連接。 例如，如果將規則添加到*NSG1，* 該規則拒絕所有入站和出站流量，*則 VM1*和*VM2*將不再能夠相互通信。 必須特別添加另一條規則才能允許這樣做。 



藉由檢視網路介面的[有效安全性規則](virtual-network-network-interface.md#view-effective-security-rules)，可以輕鬆地檢視套用至網路介面的彙總規則。 您也可以使用 Azure 網路監看員中的 [IP 流量確認](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)功能來判斷是否允許網路介面的雙向通訊。 IP 流量確認會告訴您已允許會拒絕通訊，以及哪個網路安全性規則允許或拒絕流量。

> [!NOTE]
> 網路安全性群組與子網或部署在經典部署模型中的虛擬機器和雲服務以及資源管理器部署模型中的子網或網路介面相關聯。 若要深入了解 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

> [!TIP]
> 除非您有特殊原因要這麼做，否則我們建議您讓網路安全性群組與子網路或網路介面的其中一個建立關聯，而非同時與這兩者建立關聯。 因為如果與子網路相關聯的網路安全性群組中，以及與網路介面相關聯的網路安全性群組中都存在規則，則這兩個規則可能會發生衝突，您可能會遇到需要進行疑難排解的非預期通訊問題。

## <a name="azure-platform-considerations"></a>Azure 平台的考量

- **主機節點的虛擬 IP：** 通過虛擬化主機 IP 位址 168.63.129.16 和 169.254 提供的基本基礎設施服務，如 DHCP、DNS、IMDS 和運行狀況監視。 這些 IP 位址屬於 Microsoft，而且是針對此目的唯一用於所有地區的虛擬 IP。 有效的安全規則和有效的路由不包括這些平臺規則。 要覆蓋此基本基礎結構通信，可以創建安全規則，通過使用網路安全性群組規則上的以下[服務標記](service-tags-overview.md)來拒絕流量：AzurePlatformDNS、AzurePlatformIMDS、AzurePlatformLKM。 瞭解如何[診斷網路流量篩選](diagnose-network-traffic-filter-problem.md)和[診斷網路路由](diagnose-network-routing-problem.md)。
- **授權 (金鑰管理服務)**：必須授權在虛擬機器中執行的 Windows 映像。 若要確保授權，授權要求會傳送至處理此類查詢的金鑰管理服務主機伺服器。 此要求是透過連接埠 1688 輸出。 若為使用[預設路由 0.0.0.0/0](virtual-networks-udr-overview.md#default-route)組態的部署，將會停用此平台規則。
- **負載平衡集區中的虛擬機器**：套用的來源連接埠和位址範圍是來自原始電腦，而不是負載平衡器。 目的地連接埠和位址範圍屬於目的地電腦，而不是負載平衡器。
- **Azure 服務執行個體**：虛擬網路子網路中會部署數個 Azure 服務的執行個體，例如 HDInsight、應用程式服務環境及虛擬機器擴展集。 如需您可以部署到虛擬網路的完整服務清單，請參閱 [Azure 服務的虛擬網路](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)。 將網路安全性群組套用至部署資源的子網路之前，請先確定您熟悉每個服務的連接埠需求。 如果您拒絕服務所需要的連接埠，服務就無法正常運作。
- **傳送外寄電子郵件**：Microsoft 建議您利用已驗證的 SMTP 轉送服務 (通常透過 TCP 連接埠 587 連線，但也可透過其他連接埠連線)，從 Azure 虛擬機器傳送電子郵件。 SMTP 轉送服務是專為寄件者信譽所設計，可將第三方電子郵件提供者拒絕訊息的可能性降到最低。 這類 SMTP 轉送服務包括但不限於 Exchange Online Protection 和 SendGrid。 不論您的訂用帳戶類型為何，在 Azure 中使用 SMTP 轉送服務不受限制。 

  如果您在 2017 年 11 月 15 日前建立了 Azure 訂用帳戶，除了能夠使用 SMTP 轉送服務，您還可以直接透過 TCP 連接埠 25 傳送電子郵件。 如果您在 2017 年 11 月 15 日後建立了訂用帳戶，您可能無法直接透過連接埠 25 傳送電子郵件。 透過連接埠 25 的連出通訊行為取決於您擁有的訂用帳戶類型，如下所示：

     - **Enterprise 合約**：允許輸出通訊埠 25 通訊。 您能夠從虛擬機器將外寄電子郵件直接傳送到外部電子郵件提供者 (Azure 平台沒有限制)。 
     - **隨用隨付：** 所有資源的輸出連接埠 25 通訊都遭到封鎖。 如果您需要將電子郵件從虛擬機器直接傳送給外部電子郵件提供者 (不使用已驗證的 SMTP 轉送)，可以提出移除限制的要求。 要求是在 Microsoft 的斟酌之下審查與核准，而且只會在執行反詐騙檢查之後授權。 若要提出要求，請開啟問題類型為 [技術]**、[虛擬網路連線]**、[無法傳送電子郵件 (SMTP/連接埠 25)]** 的支援案例。 在您的支援案例中，請包含訂用帳戶需要將電子郵件直接傳送到郵件提供者，而不需經過已驗證 SMTP 轉送之原因的詳細資料。 如果您的訂用帳戶獲得豁免，則只有在豁免日期之後建立的虛擬機器能夠透過連接埠 25 對外通訊。
     - **MSDN、Azure Pass、Azure in Open、Education、BizSpark 和免費試用**：所有資源的輸出連接埠 25 通訊都遭到封鎖。 無法進行任何移除限制的要求，因為要求未獲授權。 如果您必須從虛擬機器傳送電子郵件，就必須使用 SMTP 轉送服務。
     - **雲端服務提供者**：透過雲端服務提供者使用 Azure 資源的客戶，可以建立其雲端服務提供者的支援案例，以及在安全的 SMTP 轉送無法使用時，要求提供者代表他們建立解除封鎖案例。

  如果 Azure 允許您透過連接埠 25 傳送電子郵件，Microsoft 無法保證電子郵件提供者會接受您虛擬機器所發出的內送電子郵件。 如果特定提供者拒絕來自虛擬機器的郵件，請直接與提供者合作以解決任何訊息傳遞或垃圾郵件篩選問題，或使用已驗證的 SMTP 轉送服務。

## <a name="next-steps"></a>後續步驟

* 了解如何[建立網路安全性群組](tutorial-filter-network-traffic.md)。
