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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129106"
---
## <a name="limitations"></a>限制

- 專用主機當前不支援虛擬機器縮放集。

## <a name="benefits"></a>優點 

保留整個主機具有以下好處：

-   物理伺服器級別的硬體隔離。 主機上不會放置其他 VM。 專用主機部署在同一資料中心，並與其他非隔離主機共用相同的網路和基礎存儲基礎結構。
-   控制由 Azure 平臺啟動的維護事件。 雖然大多數維護事件對虛擬機器的影響很小或沒有影響，但有些敏感的工作負載會影響每一秒的暫停。 使用專用主機，您可以加入宣告維護視窗，以減少對服務的影響。
-   借助 Azure 混合權益，您可以將自己的 Windows 和 SQL 許可證帶到 Azure。 使用混合權益可為您提供其他好處。 有關詳細資訊，請參閱[Azure 混合權益](https://azure.microsoft.com/pricing/hybrid-benefit/)。


## <a name="groups-hosts-and-vms"></a>組、主機和 VM  

![查看專用主機的新資源。](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**主機組**是表示專用主機集合的資源。 在區域和可用性區域中創建主機組，並將主機添加到其中。

**主機**是一種資源，映射到 Azure 資料中心中的物理伺服器。 創建主機時分配物理伺服器。 在主機組中創建主機。 主機具有 SKU，用於描述可以創建哪些 VM 大小。 每個主機可以承載多個不同大小的 VM，只要它們來自同一大小系列。

在 Azure 中創建 VM 時，可以選擇用於 VM 的專用主機。 您可以完全控制將哪些 VM 放置在主機上。


## <a name="high-availability-considerations"></a>高可用性注意事項 

為獲得高可用性，應部署多個 VM，分佈在多個主機（至少 2 個）。 使用 Azure 專用主機，您可以預配基礎結構以形成故障隔離邊界。

### <a name="use-availability-zones-for-fault-isolation"></a>使用可用性區域進行故障隔離

可用性區域是 Azure 區域中唯一的物理位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 在單個可用性區域中創建主機組。 創建後，所有主機都將放置在該區域內。 要跨區域實現高可用性，您需要創建多個主機組（每個區域一個），並相應地擴展主機。

如果將主機組分配給可用性區域，則必須在同一區域中創建在該主機上創建的所有 VM。

### <a name="use-fault-domains-for-fault-isolation"></a>使用容錯域進行故障隔離

可以在特定的容錯域中創建主機。 與規模集或可用性集中的 VM 一樣，不同容錯域中的主機將放置在資料中心的不同物理機架上。 創建主機組時，需要指定容錯域計數。 在主機組中創建主機時，會為每個主機分配容錯域。 VM 不需要任何容錯域分配。

容錯域與配置不同。 兩個主機具有相同的容錯域並不意味著它們彼此接近。

容錯域的範圍為主機組。 不應對兩個主機組之間的反關聯進行任何假設（除非它們位於不同的可用性區域）。

部署到具有不同容錯域的主機的 VM 將在多個存儲戳上具有其基礎託管磁片服務，以提高故障隔離保護。

### <a name="using-availability-zones-and-fault-domains"></a>使用可用性區域和容錯域

可以同時使用這兩種功能來實現更多的故障隔離。 在這種情況下，您將為每個主機組指定可用性區和容錯域計數，將容錯域分配給組中的每個主機，並為每個 VM 分配一個可用性區域

[此處](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)找到的資源管理器示例範本使用區域和容錯域來擴展主機，以實現區域中的最大恢復能力。

## <a name="maintenance-control"></a>維修控制

支援虛擬機器的基礎結構可能會偶爾更新，以提高可靠性、性能、安全性並啟動新功能。 Azure 平臺嘗試盡可能將平臺維護的影響降至最低，但具有*維護敏感*工作負荷的客戶甚至無法容忍 VM 需要凍結或斷開連接進行維護的幾秒鐘。

**維護控制**為客戶提供一個選項，可以跳過計畫在其專用主機上安排的常規平臺更新，然後在他們選擇的時間在 35 天的滾動視窗中應用它。

> [!NOTE]
>  維護控制當前處於公共預覽版中。 有關詳細資訊，請參閱使用**[CLI](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)或[PowerShell](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)使用維護控制的控制更新**。

## <a name="capacity-considerations"></a>容量考量

預配專用主機後，Azure 會將其分配給物理伺服器。 這保證了在需要預配 VM 時容量的可用性。 Azure 使用區域（或區域）中的整個容量為主機選擇物理伺服器。 這也意味著客戶能夠擴展其專用主機佔用空間，而不必擔心群集中空間不足。

## <a name="quotas"></a>配額

每個區域的專用主機的預設配額限制為 3000 vCPU。 但是，可以部署的主機數也受到用於主機的 VM 大小系列的配額的限制。 例如，在美國東部區域，即**用即付**訂閱可能只有 10 個 vCPU 的配額可用於 Dsv3 大小系列。 在這種情況下，您需要請求將配額增加到至少 64 個 vCPU，然後才能部署專用主機。 選擇右上角的請求**增加**按鈕，根據需要提交請求。

![門戶中使用和配額頁的螢幕截圖](./media/virtual-machines-common-dedicated-hosts/quotas.png)

有關詳細資訊，請參閱虛擬機器[vCPU 配額](/azure/virtual-machines/windows/quotas)。

免費試用和 MSDN 訂閱沒有 Azure 專用主機的配額。

## <a name="pricing"></a>定價

無論部署的 VM 數是多少，使用者都會按專用主機付費。 在每月對帳單中，您將看到新的計費資源類型的主機。 專用主機上的 VM 仍將顯示在您的語句中，但價格為 0。

主機價格基於 VM 系列、類型（硬體大小）和地區設置。 主機價格相對於主機上支援的最大 VM 大小相關。

軟體許可、存儲和網路使用方式與主機和 VM 分開計費。 這些可計費專案沒有變化。

有關詳細資訊，請參閱[Azure 專用主機定價](https://aka.ms/ADHPricing)。

您還可以使用[Azure 專用主機的預留實例](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md)來節省成本。
 
## <a name="sizes-and-hardware-generations"></a>尺寸和硬體代數

SKU 為主機定義，它表示 VM 大小系列和類型。 您可以在單個主機中混合多個不同大小的 VM，只要它們的大小相同。 

*類型*是硬體生成。 同一 VM 系列的不同硬體類型將來自不同的 CPU 供應商，並且具有不同的 CPU 代和內核數。 

大小和硬體類型因地區而異。 請參閱主機[定價頁面](https://aka.ms/ADHPricing)以瞭解更多資訊。


## <a name="host-life-cycle"></a>主機生命週期


Azure 監視和管理主機的運行狀況。 查詢主機時，將返回以下狀態：

| 健全狀況狀態   | 描述       |
|----------|----------------|
| 主機可用     | 主機沒有已知問題。   |
| 主機正在調查中  | 我們正在調查的主機有一些問題。 這是 Azure 嘗試確定所識別問題的範圍和根本原因所需的過渡狀態。 在主機上運行的虛擬機器可能會受到影響。 |
| 主機掛起的取消分配   | Azure 無法將主機還原到正常狀態，並要求您將虛擬機器從此主機中重新部署。 如果`autoReplaceOnFailure`啟用，您的虛擬機器將*修復為*健康的硬體。 否則，虛擬機器可能在即將發生故障的主機上運行。|
| 主機交易  | 所有虛擬機器都已從主機中刪除。 由於硬體已退出旋轉，因此不再為此主機向您收費。   |

