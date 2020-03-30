---
title: 維護和更新
description: 在 Azure 中運行的虛擬機器的維護和更新概述。
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: eaf7616b3bd69828829342b4dca9247c009d3475
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250227"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Azure 中虛擬機器進行的維修

Azure 會定期更新其平臺，以提高虛擬機器主機基礎結構的可靠性、性能和安全性。 這些更新的目的涵蓋修補主控環境中的軟體元件，以至升級網路元件或將硬體解除委任。 

更新很少影響託管 VM。 當更新確實有效果時，Azure 會為更新選擇影響最小的方法：

- 如果更新不需要重新開機，則在更新主機時暫停 VM，或者 VM 即時移轉到已更新的主機。 
- 如果維護需要重新開機，則會通知您計畫進行的維護。 Azure 還提供一個時間視窗，您可以在適合您的時候自行啟動維護。 除非維護緊急，否則自維護視窗通常為 30 天。 Azure 正在投資技術，以減少計畫的平臺維護需要重新開機 VM 的情況。 有關管理計畫維護的說明，請參閱使用 Azure [CLI、PowerShell](maintenance-notifications-cli.md)或[門戶](maintenance-notifications-portal.md)處理計畫維護通知。 [PowerShell](maintenance-notifications-powershell.md)

本頁說明 Azure 如何執行這兩種類型的維護。 有關計畫外事件（中斷）的詳細資訊，請參閱 [管理 Windows 的 VM 可用性](./windows/manage-availability.md)或[Linux](./linux/manage-availability.md)的相應文章。

在 VM 中，您可以通過[為 Windows](./windows/scheduled-events.md)或[Linux](./linux/scheduled-events.md)獲取有關即將進行的維護的通知。



## <a name="maintenance-that-doesnt-require-a-reboot"></a>不需要重新開機的維護

大多數平臺更新不會影響客戶 VM。 當無法進行無影響更新時，Azure 會選擇對客戶 VM 影響最小的更新機制。 

大多數非零影響維護會暫停 VM 不到 10 秒。 在某些情況下，Azure 使用記憶體保留維護機制。 這些機制將 VM 暫停長達 30 秒，並將記憶體保留在 RAM 中。 然後恢復 VM，並自動同步其時鐘。 

記憶體保留維護適用于超過 90% 的 Azure VM。 它適用于 G、M、N 和 H 系列。 Azure 越來越多地使用即時移轉技術，並改進記憶體保留維護機制，以減少暫停持續時間。  

不需要重新開機的這些維護操作一次應用一個容錯域。 如果他們收到任何警告的健康信號，它們會停止。 

這些類型的更新可能會影響某些應用程式。 當 VM 即時移轉到其他主機時，某些敏感的工作負載可能會在 VM 暫停前的幾分鐘內出現輕微的性能下降。 要準備 VM 維護並減少 Azure 維護期間的影響，請嘗試為此類應用程式使用 Windows 或[Linux](./linux/scheduled-events.md) [的計畫事件](./windows/scheduled-events.md)。 

在公共預覽版中還有一項功能，即維護控制，可説明管理不需要重新開機的維護。 您必須使用 Azure[專用主機](./linux/dedicated-hosts.md)或隔離[VM。](../security/fundamentals/isolation-choices.md) 通過維護控制，您可以選擇跳過平臺更新，並在 35 天的滾動視窗中在您選擇的時間應用更新。 有關詳細資訊，請參閱[使用維護控制和 Azure CLI 進行的控制更新](maintenance-control-cli.md)。


### <a name="live-migration"></a>即時移轉

即時移轉是一種不需要重新開機且為 VM 保留記憶體的操作。 它會導致暫停或凍結，通常持續時間不超過 5 秒。 除 G、M、N 和 H 系列外，所有基礎設施作為服務 （IaaS） VM 均有資格進行即時移轉。 符合條件的 VM 表示部署到 Azure 佇列的 IaaS VM 的 90% 以上。 

Azure 平臺在以下情況下開始即時移轉：
- 預定的維修
- 硬體故障
- 分配優化

某些計畫維護方案使用即時移轉，您可以使用"計畫事件"提前知道即時移轉操作何時開始。

當 Azure 機器學習演算法預測即將發生的硬體故障或想要優化 VM 分配時，還可以使用即時移轉來移動 VM。 有關檢測降級硬體實例的預測建模的詳細資訊，請參閱[通過預測機器學習和即時移轉提高 Azure VM 恢復能力](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)。 如果使用這些服務，即時移轉通知將顯示在監視器和服務運行狀況日誌中的 Azure 門戶中以及"計畫事件"中。

## <a name="maintenance-that-requires-a-reboot"></a>需要重新開機的維護

在需要重新開機 VM 進行計畫維護的罕見情況下，我們會提前通知您。 計畫維護有兩個階段：自助服務階段和計畫維護階段。

在*自助服務階段*（通常持續四周）中，您將開始對 VM 進行維護。 作為自助服務的一部分，您可以查詢每個 VM 以查看其狀態和上次維護請求的結果。

當您開始進行自助維護時，您的 VM 會重新部署至已經更新的節點。 由於 VM 重新開機，臨時磁片將丟失，並且與虛擬網路介面關聯的動態 IP 位址將更新。

如果在自助服務維護過程中出現錯誤，操作將停止，VM 未更新，您可以選擇重試自助服務維護。 

自助服務階段結束時，*計畫維護階段*開始。 在此階段，您仍然可以查詢維護階段，但不能自行啟動維護。

有關管理需要重新開機的維護的詳細資訊，請參閱使用 Azure [CLI、PowerShell](maintenance-notifications-cli.md)或[門戶](maintenance-notifications-portal.md)**處理計畫的維護通知**。 [PowerShell](maintenance-notifications-powershell.md) 

### <a name="availability-considerations-during-scheduled-maintenance"></a>計畫維護期間的可用性注意事項 

如果您決定等到計畫維護階段，您應該考慮一些事項來保持 VM 的最高可用性。 

#### <a name="paired-regions"></a>配對的區域

每個 Azure 區域都與同一地理位置範圍內的另一個區域配對。 它們一起製作區域對。 在計畫維護階段，Azure 僅更新區域對單個區域中的 VM。 例如，在美國中北部更新 VM 時，Azure 不會同時更新美國中南部的任何 VM。 不過，像是北歐等其他區域可以和美國東部一同進行維護。 了解區域配對的運作方式可協助您將 VM 更妥善地分散於各區域。 如需詳細資訊，請參閱 [Azure 區域配對](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

#### <a name="availability-sets-and-scale-sets"></a>可用性設定組和擴展集

在 Azure VM 上部署工作負荷時，可以在*可用性集中*創建 VM，以便為應用程式提供高可用性。 使用可用性集，可以確保在需要重新開機的中斷或維護事件期間，至少有一個 VM 可用。

在可用性設定組內，個別 VM 會散佈在多達 20 個更新網域。 在計畫維護期間，在任何給定時間僅更新一個更新域。 更新域不一定按順序更新。 

虛擬機器*縮放集*是一種 Azure 計算資源，可用於將一組相同的 VM 作為單個資源進行部署和管理。 規模集會自動跨國防部部署，如可用性集中的 VM。 與可用性集一樣，當您使用比例集時，在計畫維護期間的任何給定時間，只有一個 UD 更新。

有關為高可用性設置 VM 的詳細資訊，請參閱 [管理適用于 Windows 的 VM 的可用性](./windows/manage-availability.md)或針對[Linux](./linux/manage-availability.md)的相應文章。

#### <a name="availability-zones"></a>可用性區域

可用性區域是 Azure 區域中唯一的物理位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原能力，在所有已啟用的地區中都至少要有三個個別的區域。 

可用性區域是容錯域和更新域的組合。 如果在 Azure 區域中的三個區域中創建三個或多個 VM，則 VM 將有效地分佈在三個容錯域和三個更新域中。 Azure 平台會從更新網域中辨識此分佈，以確定不會同時更新不同區域中的 VM。

每個基礎結構更新都會在單個區域內按區域展開。 但是，您可以在區域 1 中進行部署，同時在區域 2 中進行不同的部署。 部署並非全部序列化。 但是，單個部署一次只推出一個區域以降低風險。

## <a name="next-steps"></a>後續步驟 

您可以使用 Azure [CLI、Azure](maintenance-notifications-cli.md) [PowerShell](maintenance-notifications-powershell.md)或[門戶](maintenance-notifications-portal.md)來管理計畫維護。 