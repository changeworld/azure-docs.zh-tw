---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 965da18c265fad1686473d5d6dcf8ba4a7a53b33
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89324405"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>了解 VM 重新開機 - 維護與停機時間
有三種情況可能會導致 Azure 中的虛擬機器受到影響：未規劃的硬體維護、未預期的停機時間以及規劃的維護。

* **未規劃的硬體維護事件**發生在 Azure 平台預測與實體機器相關聯的硬體或任何平台元件即將失敗 (故障) 時。 平台會在預測到發生失敗時發出未規劃的硬體維護事件，以減少對該硬體上裝載之虛擬機器的影響。 Azure 會使用[即時移轉](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates)技術，將虛擬機器從故障硬體移轉至狀況良好的實體機器。 即時移轉是只會短時間暫停虛擬機器的 VM 保留作業。 系統會維護記憶體、開啟的檔案和網路連線，但在事件之前及 (或) 之後的效能可能會降低。 如果無法使用即時移轉，VM 將會發生未預期的停機時間，如下所述。


* **非預期的停機時間**是指虛擬機器的硬體或實體基礎結構發生意外失敗的時間。 這可能包含本機網路失敗、本機磁碟失敗，或其他機架層級的失敗。 在偵測到失敗時，Azure 平台會自動將虛擬機器移轉 (修復) 至相同資料中心內狀況良好的實體機器。 在修復程序期間，虛擬機器會停機 (重新開機)，而在某些情況下，還會遺失暫存磁碟機。 連結的 OS 和資料磁碟一律會予以保留。

  在可影響整個資料中心或甚至整個地區的罕見中斷或災害事件中，虛擬機器也可能遇到停機情況。 針對這些案例，Azure 提供了[可用性區域](../articles/availability-zones/az-overview.md)和[配對地區](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions)等保護選項。

* **規劃的維護事件**是由 Microsoft 對基礎 Azure 平台進行的定期更新，為虛擬機器在其中執行的平台基礎結構改善整體可靠性、效能和安全性。 這些更新大多數都會在不影響虛擬機器或雲端服務的情況下執行 (請參閱 [VM 保留維護](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance))。 雖然 Azure 平台嘗試在所有可能的情況下使用 VM 保留維護，但是有極少數的情況需要重新啟動虛擬機器以將必要更新套用至基礎結構。 在此情況下，您也可以在適合的時間範圍起始 VM 的維護，以使用維護重新部署作業來執行 Azure 規劃的維護。 如需詳細資訊，請參閱[虛擬機器的規劃維護](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/)。


為了減少一或多個這些事件造成的停機所帶來的影響，建議您為虛擬機器使用下列高可用性的最佳做法：

* [針對備援在可用性設定組中設定多部虛擬機器]
* [將受控磁碟使用於可用性設定組中的 VM]
* [使用排定的事件主動回應 VM 影響事件](../articles/virtual-machines/linux/scheduled-events.md)
* [將每個應用程式層設定至不同的可用性設定組中](../articles/virtual-machines/windows/tutorial-availability-sets.md)
* [將負載平衡器與可用性區域或可用性設定組結合]
* [使用可用性區域來防禦資料中心層級的失敗]

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>使用可用性區域來防禦資料中心層級的失敗

[可用性區域](../articles/availability-zones/az-overview.md)可擴展您必須在 VM 上維護應用程式和資料可用性的控制層級。 「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原能力，在所有已啟用的地區中都至少要有三個個別的區域。 地區內「可用性區域」的實體區隔可保護應用程式和資料不受資料中心故障影響。 區域備援服務會將應用程式和資料複寫至所有「可用性區域」，以防出現單一失敗點。

Azure 區域中的可用性區域是由**容錯網域**和**更新網域**組成。 例如，如果您在 Azure 區域中建立橫跨三個區域的三個 (或更多) VM，您的 VM 會有效地分散到三個容錯網域和三個更新網域。 Azure 平台會從更新網域中辨識此分佈，以確定不會同時更新不同區域中的 VM。

使用「可用性區域」時，Azure 可提供業界最佳的 99.99% VM 執行時間 SLA。 將您的解決方案架構為使用區域中複寫的 VM，即可保護您的應用程式和資料免於遭受資料中心損失。 如果有個區域遭到入侵，就可以立即在另一個區域中使用複寫的應用程式和資料。

![可用性區域](./media/virtual-machines-common-manage-availability/three-zones-per-region.png)

深入了解如何在可用性區域中部署 [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) 或[Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM。

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>針對備援在可用性設定組中設定多部虛擬機器
可用性設定組是另一個資料中心設定，用於提供 VM 備援和可用性。 資料中心內的這項組態可以確保在規劃或未規劃的維護事件發生期間，至少有一部虛擬機器可以使用，且符合 99.95% 的 Azure SLA。 如需相關資訊，請參閱 [虛擬機器的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。

> [!IMPORTANT]
> 可用性設定組中的單一執行個體虛擬機器本身應該針對所有作業系統磁碟和資料磁碟使用進階 SSD 或 Ultra 磁碟，以符合虛擬機器連線能力至少 99.9% 的 SLA 資格。 
> 
> 具有標準 SSD 的單一執行個體虛擬機器的 SLA 至少為 99.5%，而具有標準 HDD 的單一執行個體虛擬機器的 SLA 至少為 95%。  請查看[虛擬機器的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)

基礎 Azure 平台會為可用性集合中的每部虛擬機器指派一個**更新網域**和一個**容錯網域**。 在指定的可用性設定組中，預設指派五個非使用者可設定的更新網域 (接著可以增加 Resource Manager 部署，以提供最多 20 個更新網域)，表示虛擬機器群組和可同時重新啟動的基礎實體硬體。 當一個可用性設定組中設定了超過五部虛擬機器，會將第六部虛擬機器放入與第一部虛擬機器相同的更新網域中，而第七部則會放入與第二部相同的更新網域中，以此類推。 重新啟動的更新網域順序可能不會在規劃的維護事件期間循序進行，而只會一次重新啟動一個更新網域。 在不同的更新網域上起始維護之前，重新啟動的更新網域有 30 分鐘的復原時間。

容錯網域定義共用通用電源和網路交換器的虛擬機器群組。 根據預設，可用性設定組中設定的虛擬機器會分置於最多三個容錯網域中，以進行 Resource Manager 部署 (如果是傳統，則是兩個容錯網域)。 將虛擬機器放入可用性設定組，並無法保護應用程式不會遭受作業系統錯誤或特定應用程式錯誤，而只會限制可能的實體硬體錯誤、網路中斷或電源中斷所帶來的影響。

<!--Image reference-->
   ![更新網域和容錯網域組態的概念圖](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>將受控磁碟使用於可用性設定組中的 VM
如果您目前使用 VM 搭配非受控磁碟，強烈建議您[將可用性設定組中的 VM 轉換為受控磁碟](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md)。

[受控磁碟](../articles/virtual-machines/managed-disks-overview.md)可確保可用性設定組中的 VM 磁碟彼此充分隔離，以避免單一失敗點，為可用性設定組提供更高的可靠性。 作法是自動將磁碟置於不同的儲存體容錯網域 (儲存體叢集)，並將它們與 VM 容錯網域對齊。 如果因為硬體或軟體失敗而造成儲存體容錯網域失敗，則只有磁碟是在這些儲存體容錯網域上的 VM 執行個體才會失敗。
![受控磁碟 FD](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> 受控可用性設定組的容錯網域數目會依區域而異，每個區域會有兩個或三個。 您可以執行下列指令碼，來查看每個區域的容錯網域。

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> 在某些情況下，相同可用性設定組中的兩個 VM 可能會共用一個容錯網域。 您可以藉由移至可用性設定組並且檢查 [容錯網域] 資料行，確認共用的容錯網域。 共用的容錯網域可能是當您部署 VM 時，完成下列順序所造成：
> 1. 部署第一個 VM。
> 1. 停止/解除配置第一個 VM。
> 1. 部署第二個 VM。
>
> 在這些情況下，第二個 VM 的 OS 磁碟可能會建立在與第一個 VM 相同的容錯網域上，因此這兩個 VM 會位於相同的容錯網域。 若要避免此問題，我們建議您不要在部署之間停止/解除配置 VM。

如果您打算使用 VM 搭配非受控磁碟，請針對 VM 的虛擬硬碟 (VHD) 在其中儲存為[分頁 Blob](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) 的儲存體帳戶，遵循以下的最佳做法。

1. **將與 VM 相關聯的所有磁碟 (OS 和資料) 保留於相同的儲存體帳戶中**
2. 將更多 VHD 新增至儲存體帳戶之前，**請檢閱 Azure 儲存體帳戶中非受控磁碟數目的[限制](../articles/storage/blobs/scalability-targets-premium-page-blobs.md)**
3. **針對可用性設定組中的每個 VM 使用個別的儲存體帳戶。** 請勿與相同可用性設定組中的多個 VM 共用儲存體帳戶。 如果遵循上述的最佳做法，即可接受位於不同可用性設定組的 VM 共用儲存體帳戶 ![非受控磁碟 FD](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>使用排定的事件主動回應 VM 影響事件

當您訂閱[排定的事件](../articles/virtual-machines/linux/scheduled-events.md)時，您的 VM 將會收到即將有維護事件可能會影響到 VM 的通知。 排定的事件啟用時，您的虛擬機器在維護活動執行之前將有最低限度的時間可因應。 例如，可能對 VM 造成影響的主機 OS 更新會排入佇列中作為事件，以指出相關影響，並在您未採取任何動作時指出將執行維護的時間。 當 Azure 偵測到即將有硬體故障可能對 VM 造成影響時，也會將排程事件排入佇列，以讓您決定應於何時執行修復。 客戶可以使用此事件在維護之前執行適當工作，例如儲存狀態、容錯移轉至次要機器等等。 在您完成依正常程序處理維護事件的邏輯之後，您可以核准待處理的已排定事件，以允許平台繼續進行維護。


## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>將負載平衡器與可用性區域或可用性設定組結合
將 [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) 與可用性區域或可用性設定組結合，以獲得最多的應用程式備援能力。 Azure 負載平衡器會在多部虛擬機器之間分配流量。 我們的標準層虛擬機器中包含 Azure 負載平衡器。 並非所有的虛擬機器階層都包含 Azure Load Balancer。 如需關於負載平衡虛擬機器的詳細資訊，請參閱 [負載平衡虛擬機器](../articles/virtual-machines/linux/tutorial-load-balancer.md)。

若負載平衡器沒有設定為平衡多部虛擬機器之間的流量，則所有計劃性維護事件都只會影響處理流量的虛擬機器，並導致應用程式層中斷。 將同一個層的多部虛擬機器放在相同的負載平衡器和可用性設定組下，可讓至少一個執行個體持續處理流量。

如需有關如何跨可用性區域進行負載平衡的教學課程，請參閱[使用 Azure CLI 跨所有可用性區域對 VM 進行負載平衡](../articles/load-balancer/load-balancer-standard-public-zone-redundant-cli.md)。


<!-- Link references -->
[針對備援在可用性設定組中設定多部虛擬機器]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[將負載平衡器與可用性區域或可用性設定組結合]: #combine-a-load-balancer-with-availability-zones-or-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[將受控磁碟使用於可用性設定組中的 VM]: #use-managed-disks-for-vms-in-an-availability-set
[使用可用性區域來防禦資料中心層級的失敗]: #use-availability-zones-to-protect-from-datacenter-level-failures
