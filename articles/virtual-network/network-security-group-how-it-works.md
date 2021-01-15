---
title: 網路安全性群組-運作方式
titlesuffix: Azure Virtual Network
description: 瞭解網路安全性群組如何協助您篩選 Azure 資源之間的網路流量。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 5cf0345ccffe95286b95607c6c7322752df6342b
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223273"
---
# <a name="how-network-security-groups-filter-network-traffic"></a>網路安全性群組篩選網路流量的方式
<a name="network-security-groups"></a>

您可以使用 Azure 網路安全性群組來篩選與 Azure 虛擬網路中的 Azure 資源之間的網路流量。 網路安全性群組包含 [安全性規則](./network-security-groups-overview.md#security-rules) ，可允許或拒絕來自數種 Azure 資源類型的輸入網路流量或輸出網路流量。 您可以為每個規則指定來源和目的地、連接埠及通訊協定。

您可以將數個 Azure 服務的資源部署到 Azure 虛擬網路。 如需完整清單，請參閱[可以部署至虛擬網路的服務](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)。 您可以將零個或一個網路安全性群組關聯至每個虛擬網路[子網路](virtual-network-manage-subnet.md#change-subnet-settings)，以及虛擬機器中的[網路介面](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)。 您可以將相同的網路安全性群組關聯至所需數量的子網路和網路介面。

下圖以不同案例說明網路安全性群組可如何部署，以允許網路流量透過 TCP 連接埠 80 來進出網際網路：

![NSG 處理](./media/network-security-group-how-it-works/network-security-group-interaction.png)

請參考上圖及下列文字，以了解 Azure 如何處理網路安全性群組的輸入和輸出規則：

## <a name="inbound-traffic"></a>輸入流量

針對輸入流量，Azure 會先針對與子網路相關聯的網路安全性群組，處理其中的規則 (如果有的話)，然後再針對與網路介面相關聯的網路安全性群組，處理其中的規則 (如果有的話)。

- **VM1**：NSG1 中的安全性規則會進行處理，因為它與 Subnet1 和 VM1 相關聯，並且位於 Subnet1 中。 除非您已建立一個規則來允許連接埠 80 的輸入，否則流量會遭到 [DenyAllInbound](./network-security-groups-overview.md#denyallinbound) 預設安全性規則拒絕，並永遠不會由 NSG2 進行評估，因為 NSG2 與網路介面相關聯。 如果 NSG1 具有允許連接埠 80 的安全性規則，流量接著會由 NSG2 進行處理。 若要允許流量從連接埠 80 輸入虛擬機器，則 NSG1 和 NSG2 都必須有規則來允許從網際網路輸入流量的連接埠 80。
- **VM2**：在 NSG1 中的規則會進行處理，因為VM2 也位於 Subnet1 中。 由於 VM2 沒有與其網路介面相關聯的網路安全性群組，因此會接收允許通過 NSG1 的所有流量，或拒絕所有 NSG1 拒絕的流量。 如果網路安全性群組與子網路相關聯，則相同子網路中的所有資源會一起接收或拒絕流量。
- **VM3**：由於沒有任何網路安全性群組與 Subnet2 相關聯，流量會允許進入子網路並由NSG2  處理流量，因為 NSG2 與連結至 VM3 的網路介面相關聯。
- **VM4**：流量會允許進入 VM4，因為網路安全性群組未與 Subnet3 或虛擬機器中的網路介面相關聯。 如果沒有任何網路安全性群組與子網路和網路介面相關聯，則所有網路流量都可以通過子網路和網路介面。

## <a name="outbound-traffic"></a>輸出流量

針對輸出流量，Azure 會先針對與網路介面相關聯的網路安全性群組，處理其中的規則 (如果有的話)，然後再針對與子網路相關聯的網路安全性群組，處理其中的規則 (如果有的話)。

- **VM1**：NSG2 中的安全性規則會進行處理。 除非您建立安全性規則來拒絕向網際網路輸出流量的連接埠 80，否則 NSG1 和 NSG2 中的 [AllowInternetOutbound](./network-security-groups-overview.md#allowinternetoutbound) 預設安全性規則會允許流量通過。 如果 NSG2 具有拒絕連接埠 80 的安全性規則，則流量會遭到拒絕，且永遠不會由 NSG1 進行評估。 若要拒絕流量從連接埠 80 輸出虛擬機器，其中一個網路安全性群組或兩個網路安全性群組必須有規則來拒絕將流量流向網際網路的連接埠 80。
- **VM2**：所有流量都會通過網路介面流向子網路，因為連結到 VM2 的網路介面沒有與網路安全性群組相關聯。 NSG1 中的規則會進行處理。
- **VM3**：如果 NSG2 具有拒絕連接埠 80 的安全性規則，則流量會遭到拒絕。 如果 NSG2 有允許連接埠 80 的安全性規則，則連接埠 80 允許輸出流量到網際網路，因為沒有與 Subnet2 相關聯的網路安全性群組。
- **VM4**：所有網路流量會允許從 VM4 輸出，因為網路安全性群組未與連結至虛擬機器的網路介面或 Subnet3 相關聯。


## <a name="intra-subnet-traffic"></a>Intra-Subnet 流量

請務必注意，與子網相關聯之 NSG 中的安全性規則可能會影響其內部 VM 之間的連線能力。 例如，如果將規則新增至拒絕所有輸入和輸出流量的 *NSG1* ， *VM1* 和 *VM2* 將無法再彼此通訊。 您必須特別新增另一個規則，以允許這種情況。 

藉由檢視網路介面的[有效安全性規則](virtual-network-network-interface.md#view-effective-security-rules)，可以輕鬆地檢視套用至網路介面的彙總規則。 您也可以使用 Azure 網路監看員中的 [IP 流量確認](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)功能來判斷是否允許網路介面的雙向通訊。 IP 流程驗證會告訴您是否允許或拒絕通訊，以及哪個網路安全性規則允許或拒絕流量。

> [!NOTE]
> 網路安全性群組會與子網或部署在傳統部署模型中的虛擬機器和雲端服務相關聯，以及 Resource Manager 部署模型中的子網或網路介面相關聯。 若要深入了解 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

> [!TIP]
> 除非您有特定原因，否則建議您將網路安全性群組與子網或網路介面建立關聯，但不能同時與兩者建立關聯。 因為如果與子網路相關聯的網路安全性群組中，以及與網路介面相關聯的網路安全性群組中都存在規則，則這兩個規則可能會發生衝突，您可能會遇到需要進行疑難排解的非預期通訊問題。

## <a name="next-steps"></a>後續步驟

* 若要了解虛擬網路中可部署哪些 Azure 資源，並使這些資源與網路安全性群組產生關聯，請參閱 [Azure 服務的虛擬網路整合](virtual-network-for-azure-services.md)。
* 如果您從未建立過網路安全性群組，可以完成快速[教學課程](tutorial-filter-network-traffic.md)，以取得一些建立體驗。
* 如果您熟悉網路安全性群組，並且有管理需求，請參閱[管理網路安全性群組](manage-network-security-group.md)。 
* 如果您有通訊問題，因而需要對網路安全性群組進行疑難排解，請參閱[診斷虛擬機器網路流量篩選問題](diagnose-network-traffic-filter-problem.md)。 
* 瞭解如何啟用 [網路安全性群組流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ，以分析與具有相關聯網絡安全性群組之資源之間的網路流量。