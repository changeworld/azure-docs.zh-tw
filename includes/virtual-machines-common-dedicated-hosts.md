---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 2daaf9bbdf90029f0aad4333ab94e2d1d1d3d7ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79129106"
---
## <a name="limitations"></a>限制

- 專用主機目前不支援虛擬機器擴展集。

## <a name="benefits"></a>優點 

保留整個主機可提供下列優點：

-   實體伺服器層級的硬體隔離。 主機上不會放置其他 Vm。 專用主機會部署在相同的資料中心，並與其他非隔離的主機共用相同的網路和基礎儲存體基礎結構。
-   控制 Azure 平臺所起始的維護事件。 雖然大部分的維護事件對您的虛擬機器不會有任何影響，但是有一些敏感性工作負載會影響每秒的暫停。 透過專用主機，您可以選擇維護期間，以降低對您服務的影響。
-   透過 Azure 混合式權益，您可以將自己的 Windows 和 SQL 授權帶入 Azure。 使用混合式權益可提供您額外的權益。 如需詳細資訊，請參閱[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)。


## <a name="groups-hosts-and-vms"></a>群組、主機和 Vm  

![查看專用主機的新資源。](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**主機群組**是代表專用主機集合的資源。 您會在區域和可用性區域中建立主機群組，並在其中新增主機。

**主機**是對應至 Azure 資料中心內實體伺服器的資源。 建立主機時，會配置實體伺服器。 主機會在主機群組中建立。 主機有一個 SKU，描述可以建立哪些 VM 大小。 每一部主機都可以裝載多個不同大小的 Vm，只要它們是來自相同的大小系列。

在 Azure 中建立 VM 時，您可以選取要用於 VM 的專用主機。 您可以完全控制要放置在主機上的 Vm。


## <a name="high-availability-considerations"></a>高可用性考慮 

為了達到高可用性，您應該部署多個 Vm，並分散到多部主機（最少2個）。 有了 Azure 專用主機，您有數個選項可以布建您的基礎結構，以塑造您的錯誤隔離界限。

### <a name="use-availability-zones-for-fault-isolation"></a>使用可用性區域進行錯誤隔離

可用性區域是 Azure 區域內的唯一實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 主機群組會建立在單一可用性區域中。 建立之後，所有主機都會放在該區域內。 若要在區域之間達到高可用性，您需要建立多個主機群組（每個區域一個），並據此散佈主機。

如果您將主機群組指派給可用性區域，在該主機上建立的所有 Vm 都必須在相同的區域中建立。

### <a name="use-fault-domains-for-fault-isolation"></a>使用容錯網域來隔離錯誤

主機可以在特定的容錯網域中建立。 就像擴展集或可用性設定組中的 VM 一樣，不同容錯網域中的主機將會放在資料中心內的不同實體機架上。 當您建立主機群組時，您必須指定容錯網域計數。 在主機群組中建立主機時，您可以為每部主機指派容錯網域。 Vm 不需要任何容錯網域指派。

容錯網域與共置不同。 兩部主機具有相同的容錯網域，並不表示彼此鄰近。

容錯網域的範圍限於主機群組。 您不應該對兩個主機群組之間的反親和性進行任何假設（除非它們位於不同的可用性區域中）。

部署到具有不同容錯網域之主機的 Vm，在多個儲存戳記上會有其基礎受控磁片服務，以增加錯誤隔離保護。

### <a name="using-availability-zones-and-fault-domains"></a>使用可用性區域和容錯網域

您可以同時使用這兩種功能來達成更多錯誤隔離。 在此情況下，您將在中指定每個主機群組的可用性區域和容錯網域計數，將容錯網域指派給群組中的每個主機，並將可用性區域指派給每個 Vm

[這裡](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)找到的 Resource Manager 範例範本會使用區域和容錯網域來散佈主機，以取得區域中的最大復原。

## <a name="maintenance-control"></a>維修控制

支援虛擬機器的基礎結構偶爾會更新，以改善可靠性、效能、安全性，以及啟動新功能。 Azure 平臺會嘗試盡可能將平臺維護的影響降至最低，但具有*維護*性工作負載的客戶甚至無法容忍短短幾秒鐘的時間，VM 必須凍結或中斷連線以進行維護。

**維護控制**可讓客戶略過在其專用主機上排定的一般平臺更新，然後在35天的輪流時間範圍內，將其套用至選擇。

> [!NOTE]
>  維護控制目前為公開預覽狀態。 如需詳細資訊，請參閱**使用[CLI](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)或[PowerShell](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)控制維護控制的更新**。

## <a name="capacity-considerations"></a>容量考量

布建專用主機之後，Azure 會將它指派給實體伺服器。 當您需要布建 VM 時，這可保證容量的可用性。 Azure 會使用區域（或區域）中的整個容量來挑選主機的實體伺服器。 這也表示客戶可以預期能夠成長專用的主機使用量，而不需要擔心叢集空間不足。

## <a name="quotas"></a>配額

針對專用主機，每個區域都有3000個 vcpu 的預設配額限制。 但是，您可以部署的主機數目也會受限於用於主機之 VM 大小系列的配額。 例如，隨用**隨付**訂用帳戶在美國東部區域只能有10個 vcpu 的配額可供 Dsv3 大小數列使用。 在此情況下，您必須先將配額增加到至少64個 vcpu，才能部署專用主機。 選取右上角的 [**要求增加**] 按鈕，視需要提出要求。

![入口網站中 [使用量和配額] 頁面的螢幕擷取畫面](./media/virtual-machines-common-dedicated-hosts/quotas.png)

如需詳細資訊，請參閱[虛擬機器 vCPU 配額](/azure/virtual-machines/windows/quotas)。

免費試用和 MSDN 訂用帳戶沒有 Azure 專用主機的配額。

## <a name="pricing"></a>定價

無論部署多少個 Vm，都會以專用主機為使用者付費。 在您的每月語句中，您會看到新的可計費資源類型為 [主機]。 專用主機上的 Vm 仍會顯示在您的語句中，但其價格會是0。

主機價格是根據 VM 系列、類型（硬體大小）和區域來設定。 主機價格相對於主機上支援的最大 VM 大小。

軟體授權、儲存體和網路使用量會與主機和 Vm 分開計費。 這些計費專案不會有任何變更。

如需詳細資訊，請參閱[Azure 專用主機定價](https://aka.ms/ADHPricing)。

您也可以使用[Azure 專用主機的保留實例](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md)來節省成本。
 
## <a name="sizes-and-hardware-generations"></a>大小和硬體層代

SKU 是針對主機定義的，它代表 VM 大小數列和類型。 您可以在單一主機內混合使用多個不同大小的 Vm，只要它們屬於相同的大小系列即可。 

*類型*是硬體世代。 相同 VM 系列的不同硬體類型將來自不同的 CPU 廠商，並具有不同的 CPU 世代和核心數目。 

大小和硬體類型會因區域而異。 若要深入瞭解，請參閱主機[定價頁面](https://aka.ms/ADHPricing)。


## <a name="host-life-cycle"></a>主機生命週期


Azure 會監視並管理您主機的健全狀況狀態。 當您查詢主機時，將會傳回下列狀態：

| 健全狀況狀態   | 說明       |
|----------|----------------|
| 主機可供使用     | 您的主機沒有已知的問題。   |
| 調查中的主機  | 我們正在尋找的主機有一些問題。 這是 Azure 嘗試和識別所識別問題的範圍和根本原因所需的過渡狀態。 在主機上執行的虛擬機器可能會受到影響。 |
| 主機暫止解除配置   | Azure 無法將主機還原為狀況良好的狀態，並要求您將虛擬機器從這個主機重新部署。 如果`autoReplaceOnFailure`已啟用，則您的虛擬機器將會*修復*至狀況良好的硬體。 否則，您的虛擬機器可能正在即將失敗的主機上執行。|
| 主機已解除配置  | 已從主機移除所有虛擬機器。 因為硬體已離開輪替，所以您不再需要為此主機支付費用。   |

