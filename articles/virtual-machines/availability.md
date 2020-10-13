---
title: 可用性選項
description: 瞭解在 Azure 中執行虛擬機器的可用性功能
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: b4009f3c132c76f6632d6bb706649befa746001f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972521"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Azure 中虛擬機器的可用性選項

本文提供 Azure 虛擬機器 (Vm) 的可用性功能總覽。

## <a name="high-availability"></a>高可用性

工作負載通常會分散在不同的虛擬機器上，以獲得高輸送量、效能，並在 VM 因為更新或其他事件而受到影響時建立冗余。 

Azure 提供幾個選項來達成高可用性。 首先，讓我們來討論基本的結構。 

### <a name="availability-zones"></a>可用性區域

[可用性區域](../availability-zones/az-overview.md)可擴展您必須在 VM 上維護應用程式和資料可用性的控制層級。 可用性區域是 Azure 區域內的實體獨立區域。 每個 Azure 地區支援三個可用性區域。 

每個可用性區域各有不同的電力來源、網路和冷卻系統。 將您的解決方案架構為使用區域中複寫的 VM，即可保護您的應用程式和資料免於遭受資料中心損失。 如果有個區域遭到入侵，就可以立即在另一個區域中使用複寫的應用程式和資料。 

![可用性區域](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

深入了解如何在可用性區域中部署 [Windows](./windows/create-powershell-availability-zone.md) 或[Linux](./linux/create-cli-availability-zone.md) VM。


### <a name="fault-domains"></a>容錯網域

容錯網域是共用通用電源和網路交換器的基礎硬體邏輯群組，類似於內部部署資料中心內的機架。 

### <a name="update-domains"></a>更新網域

更新網域是可以同時進行維護或重新啟動的基礎硬體邏輯群組。 

此方法可確保當 Azure 平台進行定期維護時，您的應用程式至少有一個執行個體一直保持執行中。 在維護期間，重新開機的更新網域順序不一定會繼續，但是一次只能重新開機一個更新網域。


## <a name="virtual-machines-scale-sets"></a>虛擬機器擴展集 

Azure 虛擬機器擴展集可讓您建立和管理一組負載平衡的 VM。 VM 執行個體的數目可以自動增加或減少，以因應需求或已定義的排程。 擴展集可為您的應用程式提供高可用性，並可讓您集中管理、設定和更新許多 Vm。 建議您在擴展集內建立兩個或多個 Vm，以提供高可用性應用程式，並符合 [99.95% 的 AZURE SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。 擴展集本身不會產生任何費用，您只需為您所建立的每個 VM 實例付費。 當單一 VM 是使用 [Azure 進階 SSD](./disks-types.md#premium-ssd) 時，非計劃性的維護事件適用 Azure SLA。 擴展集中的虛擬機器可以部署到多個更新網域和容錯網域，以將可用性和復原能力最大化，因為資料中心中斷，以及規劃或未規劃的維護事件。 擴展集中的虛擬機器也可以部署到單一可用性區域或地區中。 可用性區域部署選項可能會根據協調流程模式而有所不同。

**容錯網域和更新網域**

虛擬機器擴展集藉由調整容錯網域和更新網域，簡化高可用性的設計。 您只需要定義擴展集的容錯網域計數。 擴展集可用的容錯網域數目可能會因區域而異。 請參閱 [在 Azure 中管理虛擬機器的可用性](./manage-availability.md)。


## <a name="availability-sets"></a>可用性設定組
可用性設定組是資料中心內 VM 的邏輯群組，可讓 Azure 了解您應用程式的建置方式，以提供備援和可用性。 建議您在可用性設定組內建立兩個或多個 VM，以提供具高可用性的應用程式，以及符合 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。 可用性設定組本身不需要任何成本，您只需針對您建立的每個 VM 執行個體支付費用。 當單一 VM 是使用 [Azure 進階 SSD](./disks-types.md#premium-ssd) 時，非計劃性的維護事件適用 Azure SLA。

在可用性設定組中，Vm 會自動分散到這些容錯網域。 此方法可限制潛在實體硬體錯誤、網路中斷或電源中斷的影響。

若 VM 使用 [Azure 受控磁碟](./faq-for-disks.md)，VM 會在使用受管理的可用性設定組時配合使用受控磁碟容錯網域。 此一配合行為可確保連接到 VM 的所有受控磁碟都位於相同的受控磁碟容錯網域。 

在受管理的可用性設定組中只能建立使用受控磁碟的 VM。 受控磁碟容錯網域數目會依區域而異，每個區域會有兩個或三個受控磁碟容錯網域。 閱讀更多適用於 [Linux VM](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) 或 [Windows VM](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) 的這些受控磁碟容錯網域相關資訊。

![受控可用性設定組](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


可用性設定組內的 Vm 也會自動分散到各個更新網域。 

![可用性設定組](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>後續步驟
您現在可以開始使用這些可用性和備援功能來建置 Azure 環境。 如需最佳作法資訊，請參閱 [Azure 可用性最佳作法](/azure/architecture/checklist/resiliency-per-service)。