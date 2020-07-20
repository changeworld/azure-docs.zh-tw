---
title: 維護和更新
description: 針對在 Azure 中執行的虛擬機器進行維護和更新的概觀。
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: shants
ms.openlocfilehash: 3caf8b63cbd86338bbef653e9be9916907578627
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84675874"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Azure 中虛擬機器進行的維修

Azure 會定期更新其平台來提升虛擬機器之主機基礎結構的可靠性、效能及安全性。 這些更新的目的涵蓋修補主控環境中的軟體元件，以至升級網路元件或將硬體解除委任。 

更新很少會影響託管的 VM。 當更新會有影響時，Azure 會選擇影響最小的更新方法：

- 如果更新不需要重新開機，則 VM 會在主機更新時暫停，否則 VM 會即時遷移至已更新好的主機。 
- 如果進行維護需要重新開機，則您會收到計劃性維護的通知。 Azure 也會提供一個時間範圍，讓您能夠在這段時間內自行啟動維護作業。 除非是緊急維護，否則自我維護時段通常是 35 天。 Azure 會投資各項技術，以減少計劃性平台維護需要將 VM 重新開機的情況。 如需如何管理計劃性維護的指示，請參閱使用 Azure [CLI](maintenance-notifications-cli.md)、[PowerShell](maintenance-notifications-powershell.md) 或[入口網站](maintenance-notifications-portal.md)來處理計劃性維護通知。

本頁說明 Azure 如何執行這兩種類型的維護。 如需非計劃性事件 (中斷) 的詳細資訊，請參閱 [管理 Windows VM 的可用性](./windows/manage-availability.md)或 [Linux](./linux/manage-availability.md) 的對應文章。

在 VM 內，您可以[使用適用於 Windows 的 Scheduled Events](./windows/scheduled-events.md) 或適用於 [Linux](./linux/scheduled-events.md) 的 Scheduled Events，來針對即將進行的維護取得相關通知。



## <a name="maintenance-that-doesnt-require-a-reboot"></a>不需要重新開機的維護

大部分的平台更新不會影響客戶 VM。 當更新會有影響時，Azure 會選擇對客戶 VM 影響最少的更新機制。 

大部分會有影響的維護都會將 VM 暫停不到 10 秒。 在某些情況下，Azure 會使用記憶體保留維護機制。 這些機制會將 VM 暫停最多 30 秒，並將記憶體保留在 RAM 中。 然後 VM 會繼續執行，且其時鐘會自動同步。 

記憶體保留維護適用於超過 90% 的 Azure VM。 但不適用於 G、M、N 和 H 系列。 Azure 越來越常使用即時移轉技術，並改進了記憶體保留維護機制以縮短暫停時間。  

這些不需要重新開機的維護作業一次會套用到一個容錯網域。 作業在收到平台監視工具所傳來的任何警告健康情況訊號時便會停止。 

這些類型的更新可能會影響某些應用程式。 將 VM 即時移轉至不同主機時，有些敏感性工作負載可能會在該幾分鐘內出現些微的效能降低，而導致 VM 暫停。 若要準備 VM 維護並降低 Azure 維護期間的影響，請嘗試為這類應用程式[使用適用於 Windows 的 Scheduled Events](./windows/scheduled-events.md) 或[使用適用於 Linux 的 Scheduled Events](./linux/scheduled-events.md)。 

若要對所有維護活動 (包括無影響的更新和不必重新開機的更新) 進行更好的控制，您可以使用維護控制功能。 您必須使用 [Azure 專用主機](./linux/dedicated-hosts.md)或[隔離的 VM](../security/fundamentals/isolation-choices.md)。 維護控制可讓您選擇略過所有平台更新，並在 35 天的滾動時間範圍內擇時套用更新。 如需詳細資訊，請參閱[使用維護控制和 Azure CLI 來控制更新](maintenance-control.md)。


### <a name="live-migration"></a>即時移轉

即時移轉作業不需要重新開機，而且會保留 VM 的記憶體。 其會造成暫停或凍結，通常不會持續超過 5 秒。 所有基礎結構即服務 (IaaS) VM 都符合即時移轉資格 (但 G、M、N 和 H 系列除外)。 合格的 VM 代表 90% 以上已部署至 Azure Fleet 的 IaaS VM。 

Azure 平台會在下列案例中開始進行即時移轉：
- 預定的維修
- 硬體失敗
- 配置最佳化

某些計劃性維護案例會使用即時移轉，而您可以使用 Scheduled Events 來事先了解即時移轉作業將要開始。

當 Azure Machine Learning 演算法預測到即將發生硬體故障或您想要將 VM 配置最佳化時，也可以使用即時移轉來移動 VM。 如需可偵測到硬體降級情況的預測性模型詳細資訊，請參閱[使用預測性機器學習和即時移轉來改善 Azure VM 復原能力](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)。 如果您使用這些服務，即時移轉通知就會出現在 Azure 入口網站的「監視器」和「服務健康狀態」記錄以及 Scheduled Events 中。

## <a name="maintenance-that-requires-a-reboot"></a>需要重新開機的維護

如果遇到計劃性維護需要將 VM 重新開機的罕見情況，您會事先收到通知。 計劃性維護有兩個階段：自助階段和排程維護階段。

在*自助階段*期間 (一般會持續四週)，您可以在 VM 上啟動維護作業。 在自助期間，您可以查詢每個 VM，以查看其狀態和上一次維護要求的結果。

當您開始進行自助維護時，您的 VM 會重新部署至已經更新的節點。 因為 VM 會重新開機，所以暫存磁碟會遺失，而系統會更新與虛擬網路介面關聯的動態 IP 位址。

如果在自助維護期間發生錯誤，作業將會停止、VM 不會更新，而且您可以選擇重試自助維護。 

當自助階段結束時，*排程維護階段*就會開始。 在這個階段，您仍然可以查詢維護階段，但無法自行啟動維護作業。

若要深入了解如何管理需要重新開機的維護，請參閱使用 Azure [CLI](maintenance-notifications-cli.md)、[PowerShell](maintenance-notifications-powershell.md) 或[入口網站](maintenance-notifications-portal.md)來**處理計劃性維護通知**。 

### <a name="availability-considerations-during-scheduled-maintenance"></a>排程維護期間的可用性考量 

如果您決定等到排程維護階段，為了維持 VM 的最高可用性，請考慮下列事項。 

#### <a name="paired-regions"></a>配對的區域

每個 Azure 區域都會與相同鄰近地理位置內的另一個區域配對。 兩者一起形成區域配對。 在排程維護階段，Azure 只會更新區域配對中單一區域的 VM。 舉例來說，在更新美國中北部的 VM 時，Azure 並不會同時更新任何美國中南部的 VM。 不過，像是北歐等其他區域可以和美國東部一同進行維護。 了解區域配對的運作方式可協助您將 VM 更妥善地分散於各區域。 如需詳細資訊，請參閱 [Azure 區域配對](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

#### <a name="availability-sets-and-scale-sets"></a>可用性設定組和擴展集

在 Azure VM 上部署工作負載時，您可以在一個*可用性設定組*內建立 VM，以便為應用程式提供高可用性。 使用可用性設定組，您便可以確保在發生需要重新開機的中斷或維護事件期間，至少會有一部 VM 可供使用。

在可用性設定組內，個別 VM 會散佈在多達 20 個更新網域。 在排程維護期間，在任何指定時間都只會更新一個更新網域。 更新網域不一定會依序更新。 

虛擬機器*擴展集*是一個可讓您以單一資源的形式，部署和管理一組相同 VM 的 Azure 計算資源。 擴展集會自動部署於更新網域，如可用性設定組中的 VM。 和可用性設定組一樣，當您使用擴展集時，排程維護期間的任何指定時間都只會更新一個更新網域。

如需如何設定 VM 以獲得高可用性的詳細資訊，請參閱 [管理 Windows VM 的可用性](./windows/manage-availability.md)或 [Linux](./linux/manage-availability.md) 的對應文章。

#### <a name="availability-zones"></a>可用性區域

可用性區域是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原能力，在所有已啟用的地區中都至少要有三個個別的區域。 

可用性區域結合了容錯網域和更新網域。 如果您在 Azure 區域中建立橫跨三個區域的三個 (或更多) VM，您的 VM 會有效地分散到三個容錯網域和三個更新網域。 Azure 平台會從更新網域中辨識此分佈，以確定不會同時更新不同區域中的 VM。

每個基礎結構更新都會在單一區域內逐區推出。 但是，您可以在 Zone 1 進行部署，並同時在 Zone 2 進行不同的部署。 部署並未完全序列化。 但是，單一部署一次只會推出到一區，以降低風險。

## <a name="next-steps"></a>後續步驟 

您可以使用 [Azure CLI](maintenance-notifications-cli.md)、[Azure PowerShell](maintenance-notifications-powershell.md) 或[入口網站](maintenance-notifications-portal.md)來管理計劃性維護。 
