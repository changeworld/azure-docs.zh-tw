---
title: 虛擬機器的 Azure 專用主機總覽
description: 深入瞭解如何使用 Azure 專用主機來部署虛擬機器。
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 4e29bb0fee496af6a8c0fd30d5559bf865123c39
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007886"
---
# <a name="azure-dedicated-hosts"></a>Azure 專用主機

Azure 專用主機是一個服務，可讓實體伺服器裝載一或多個虛擬機器 - 專用於一個 Azure 訂用帳戶。 專用主機是資料中心所使用的相同實體伺服器，以資源的形式提供。 您可以在區域、可用性區域與容錯網域中佈建專用主機。 然後，您可以依照最能滿足您需要的設定，將 VM 直接放入已佈建的主機中。


## <a name="benefits"></a>優點 

保留整個主機提供下列優點：

-   實體伺服器層級的硬體隔離。 系統不會在您的主機上放置其他 Vm。 專用主機會部署在相同的資料中心，並與其他非隔離的主機共用相同的網路和基礎的儲存基礎結構。
-   控制由 Azure 平臺起始的維護事件。 雖然大部分的維護事件幾乎不會影響您的虛擬機器，但是有一些敏感性工作負載，其中每秒的暫停都可能會有影響。 使用專用主機時，您可以選擇維護時段，以降低對您服務的影響。
-   透過 Azure 混合式權益，您可以將自己的 Windows 和 SQL 授權帶到 Azure。 使用混合式權益可提供額外的權益。 如需詳細資訊，請參閱 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)。


## <a name="groups-hosts-and-vms"></a>群組、主機和 Vm  

![查看專用主機的新資源。](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**主機群組** 是代表專用主機集合的資源。 您會在區域和可用性區域中建立主機群組，並在其中新增主機。

**主機** 是資源，對應至 Azure 資料中心內的實體伺服器。 實體伺服器會在建立主機時配置。 主機會建立在主機群組內。 主機有一個 SKU，描述可建立的 VM 大小。 每一部主機都可以裝載多個不同大小的 Vm，只要它們是來自相同的大小序列即可。


## <a name="high-availability-considerations"></a>高可用性考慮 

為了達到高可用性，您應該部署多個 Vm，並將其分散到多部主機， (最少2個) 。 使用 Azure 專用主機時，您有數個選項可布建基礎結構，以塑造您的錯誤隔離界限。

### <a name="use-availability-zones-for-fault-isolation"></a>使用可用性區域進行錯誤隔離

可用性區域是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 主機群組會建立在單一可用性區域中。 建立之後，所有主機都會放置在該區域內。 若要達到跨區域的高可用性，您需要建立多個主機群組 (每個區域) 一個，並據以散佈您的主機。

如果您將主機群組指派給可用性區域，則在該主機上建立的所有 Vm 都必須建立在相同的區域中。

### <a name="use-fault-domains-for-fault-isolation"></a>使用容錯網域進行錯誤隔離

您可以在特定的容錯網域中建立主機。 就像擴展集或可用性設定組中的 VM 一樣，位於不同容錯網域中的主機會放在資料中心內的不同實體機架上。 當您建立主機群組時，您必須指定容錯網域計數。 在主機群組中建立主機時，您可以為每部主機指派容錯網域。 Vm 不需要任何容錯網域指派。

容錯網域與共置不同。 讓兩部主機具有相同的容錯網域，並不表示它們彼此相近。

容錯網域的範圍限於主機群組。 除非兩個主機群組位於不同的可用性區域) ，否則您不應該對兩個主機 (群組之間的反親和性進行任何假設。

部署至具有不同容錯網域之主機的 Vm 將會在多個儲存戳記上擁有其基礎受控磁片服務，以提高錯誤隔離保護。

### <a name="using-availability-zones-and-fault-domains"></a>使用可用性區域和容錯網域

您可以同時使用這兩項功能來達成更多的錯誤隔離。 在此情況下，您將為每個主機群組指定可用性區域和容錯網域計數、將容錯網域指派給群組中的每個主機，並將可用性區域指派給每個 Vm

[這裡](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)找到的 Resource Manager 範例範本會使用區域和容錯網域來散佈主機，以在區域中獲得最大的復原能力。


## <a name="manual-vs-automatic-placement"></a>手動與自動放置 

在 Azure 中建立 VM 時，您可以選取要使用的專用主機。 您也可以使用選項，將 Vm 自動放置在主機群組內的現有主機上。 

建立新的主機群組時，請確定已選取 [自動 VM 放置] 的設定。 建立 VM 時，請選取主機群組，並讓 Azure 為您的 VM 挑選最佳主機。 

啟用自動放置的主機群組不需要自動放置所有 Vm。 您仍然可以明確挑選主機，即使已選取主機群組的自動放置也一樣。 

### <a name="limitations"></a>限制

使用自動 VM 放置時的已知問題和限制：

- 您將無法在專用主機上套用 Azure 混合式權益。
- 您將無法重新部署 VM。 
- 您將無法使用 Lsv2、NVasv4、NVsv3、Msv2 或 M 系列 Vm 搭配專用主機 


## <a name="virtual-machine-scale-set-support"></a>虛擬機器擴展集支援

虛擬機器擴展集可讓您將一組虛擬機器視為單一資源，並以群組形式套用可用性、管理、調整和協調流程原則。 您現有的專用主機也可以用於虛擬機器擴展集。 

建立虛擬機器擴展集時，您可以指定現有的主機群組，以在專用主機上建立所有的 VM 實例。

在專用主機群組中建立虛擬機器擴展集時，適用下列需求：

- 需要啟用自動 VM 放置。
- 主機群組的可用性設定應符合您的擴展集。 
    - 區域主機群組 (建立而不指定可用性區域) 應用於區域擴展集。
    - 主機群組和擴展集必須使用相同的可用性區域。 
    - 主機群組層級的容錯網域計數應符合您擴展集的容錯網域計數。 此 Azure 入口網站可讓您指定擴展集的 *最大分配* ，將容錯網域計數設定為1。
- 您應該先建立專用主機（具有足夠的容量），以及調整集區域和容錯網域的相同設定。
- 您專用主機所支援的 VM 大小應符合您的擴展集所使用的大小。

專用主機不支援所有的擴展集協調流程和優化設定。 將下列設定套用至您的擴展集： 
- 不建議使用過度布建，而且預設為停用。 您可以啟用過度布建，但如果主機群組沒有適用于所有 Vm （包括過度布建實例）的容量，則擴展集配置將會失敗。 
- 使用 ScaleSetVM 協調流程模式 
- 請勿針對共置使用鄰近位置群組



## <a name="maintenance-control"></a>維修控制

支援您虛擬機器的基礎結構有時可能會更新，以改善可靠性、效能、安全性，並推出新功能。 Azure 平臺會盡可能將平臺維護的影響降到最低，但具有 *維護敏感性* 工作負載的客戶，在 VM 需要凍結或中斷連線以進行維護時，不能容忍幾秒鐘的時間。

**維護控制** 讓客戶可以選擇略過在其專用主機上排程的一般平臺更新，然後在35天的輪流時段內套用。 在 [維護] 視窗中，您可以依任何順序直接在主機層級套用維護。 維護期間結束後，Microsoft 會繼續進行，並將擱置中的維護套用至主機，但可能不會遵循使用者定義的容錯網域。

如需詳細資訊，請參閱 [使用維護控制管理平臺更新](./maintenance-control.md)。

## <a name="capacity-considerations"></a>容量考量

布建專用主機之後，Azure 會將它指派給實體伺服器。 當您需要布建 VM 時，這可保證容量的可用性。 Azure 會使用區域 (或區域) 中的整個容量來挑選主機的實體伺服器。 這也表示客戶可以預期能夠成長專用的主機足跡，而不需要考慮到叢集中的空間不足。

## <a name="quotas"></a>配額

當您部署專用主機時，會使用兩種類型的配額。

1. 專用主機 vCPU 配額。 預設配額限制為每個區域3000個 vcpu。
1. VM 大小系列配額。 例如， **隨用隨付** 訂用帳戶在美國東部區域的 Dsv3 大小系列中，只能有10個個 vcpu 的配額。 若要部署 Dsv3 專用主機，您必須在部署專用主機之前，要求增加至少64個 vcpu 的配額。 

若要要求增加配額，請在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)中建立支援要求。

布建專用主機將會耗用專用主機 vCPU 和 VM 系列 vCPU 配額，但不會取用區域 vCPU。


![入口網站中 [使用量和配額] 頁面的螢幕擷取畫面](./media/virtual-machines-common-dedicated-hosts/quotas.png)

如需詳細資訊，請參閱 [虛擬機器 vCPU 配額](./windows/quotas.md)。

免費試用與 MSDN 訂用帳戶沒有 Azure 專用主機的配額。

## <a name="pricing"></a>定價

無論部署多少部 Vm，都會依專用主機收費。 在您的每月帳單中，您會看到新的可計費資源類型的主機。 專用主機上的 Vm 仍會顯示在您的語句中，但會具有0的價格。

主機價格是根據 VM 系列、輸入 (硬體大小) 和區域來設定。 主機價格相對於主機上所支援的最大 VM 大小。

軟體授權、存放裝置和網路使用量會與主機和 Vm 分開計費。 這些計費專案沒有任何變更。

如需詳細資訊，請參閱 [Azure 專用主機定價](https://aka.ms/ADHPricing)。

您也可以使用 [Azure 專用主機的保留實例](prepay-dedicated-hosts-reserved-instances.md)來節省成本。
 
## <a name="sizes-and-hardware-generations"></a>大小和硬體世代

定義主機的 SKU，它代表 VM 大小系列和類型。 您可以在單一主機內混用多個不同大小的 Vm，只要它們是相同大小的數列。 

此 *類型* 是硬體世代。 相同 VM 系列的不同硬體類型將來自不同的 CPU 廠商，並具有不同的 CPU 世代和核心數目。 

大小和硬體類型會因地區而異。 若要深入瞭解，請參閱主機[定價頁面](https://aka.ms/ADHPricing)。

> [!NOTE]
> Provisoned 專用主機之後，您就無法變更大小或類型。 如果您需要不同的類型大小，您必須建立新的主機。  

## <a name="host-life-cycle"></a>主機生命週期


Azure 會監視及管理主機的健全狀況狀態。 當您查詢主機時，將會傳回下列狀態：

| 健全狀況狀態   | 描述       |
|----------|----------------|
| 主機可用     | 您的主機沒有任何已知問題。   |
| 正在調查的主機  | 我們正在尋找的主機有一些問題。 這是 Azure 用來嘗試的過渡狀態，並識別出問題的範圍和根本原因。 主機上執行的虛擬機器可能會受到影響。 |
| 主機暫止解除配置   | Azure 無法將主機還原回狀況良好的狀態，並要求您將虛擬機器重新部署到此主機。 如果已 `autoReplaceOnFailure` 啟用，您的虛擬機器就會 *修復* 至狀況良好的硬體。 否則，您的虛擬機器可能會在即將失敗的主機上執行。|
| 主機已解除配置  | 所有虛擬機器都已從主機中移除。 您不再需要支付此主機的費用，因為硬體已離開輪替。   |


## <a name="next-steps"></a>後續步驟

- 您可以使用 [Azure PowerShell](./windows/dedicated-hosts-powershell.md)、 [入口網站](./dedicated-hosts-portal.md)和 [Azure CLI](./linux/dedicated-hosts-cli.md)來部署專用主機。

- [這裡](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有範例範本，範例中使用區域和容錯網域來獲得區域中的最大復原。

- 您也可以使用 [Azure 專用主機的保留實例](prepay-dedicated-hosts-reserved-instances.md)來節省成本。
