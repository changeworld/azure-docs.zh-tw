---
title: 維護和更新
description: 針對在 Azure 中執行的虛擬機器進行維護和更新的總覽。
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: eaf7616b3bd69828829342b4dca9247c009d3475
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79250227"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Azure 中虛擬機器進行的維修

Azure 會定期更新其平臺，以改善虛擬機器主機基礎結構的可靠性、效能和安全性。 這些更新的目的涵蓋修補主控環境中的軟體元件，以至升級網路元件或將硬體解除委任。 

更新很少會影響託管的 Vm。 當更新有作用時，Azure 會選擇最不影響力的更新方法：

- 如果更新不需要重新開機，VM 會在主機更新時暫停，或 VM 會即時移轉至已更新的主機。 
- 如果維護需要重新開機，系統會通知您已規劃的維護。 Azure 也提供一個時間範圍，讓您能夠在一段時間內自行開始維護。 [自我維護] 視窗通常是30天，除非維護是緊急的。 Azure 正在投資技術，以減少規劃的平臺維護需要重新開機 Vm 的案例數。 如需管理預定維護的指示，請參閱使用 Azure [CLI](maintenance-notifications-cli.md)、 [PowerShell](maintenance-notifications-powershell.md)或[入口網站](maintenance-notifications-portal.md)處理預定的維護通知。

本頁說明 Azure 如何執行這兩種類型的維護。 如需未規劃事件（中斷）的詳細資訊，請參閱管理適用于 [Windows 的 vm 可用性](./windows/manage-availability.md)或適用于[Linux](./linux/manage-availability.md)的對應文章。

在 VM 中，您可以[使用適用于 Windows](./windows/scheduled-events.md)或[Linux](./linux/scheduled-events.md)的 Scheduled Events，來取得即將進行之維護的相關通知。



## <a name="maintenance-that-doesnt-require-a-reboot"></a>不需要重新開機的維護

大部分的平臺更新不會影響客戶 Vm。 當不受影響的更新不可行時，Azure 會選擇最少影響力至客戶 Vm 的更新機制。 

大部分非零影響的維護都會暫停 VM 10 秒。 在某些情況下，Azure 會使用記憶體保留維護機制。 這些機制會將 VM 暫停最多30秒，並將記憶體保留在 RAM 中。 然後會繼續執行 VM，並自動同步處理其時鐘。 

記憶體保留維護適用于超過90% 的 Azure Vm。 它不適用於 G、M、N 和 H 系列。 Azure 逐漸使用即時移轉技術，並改善記憶體保留的維護機制，以減少暫停的持續時間。  

這些不需要重新開機的維護作業，會一次套用一個容錯網域。 如果他們收到任何警告健康情況信號，就會停止。 

這些更新類型可能會影響某些應用程式。 當 VM 即時移轉至不同的主機時，某些機密工作負載可能會在幾分鐘內顯示輕微的效能降低，導致 VM 暫停。 若要準備 VM 維護並降低 Azure 維護期間的影響，請嘗試針對這類應用程式[使用適用于 Windows](./windows/scheduled-events.md)或[Linux](./linux/scheduled-events.md)的 Scheduled Events。 

公開預覽也有一項功能，可協助您管理不需要重新開機的維護。 您必須使用[Azure 專用主機](./linux/dedicated-hosts.md)或[隔離的 VM](../security/fundamentals/isolation-choices.md)。 維護控制可讓您選擇略過平臺更新，並在35天的輪流時間範圍內，將更新套用到您的選擇。 如需詳細資訊，請參閱[使用維護控制控制更新和 Azure CLI](maintenance-control-cli.md)。


### <a name="live-migration"></a>即時移轉

「即時移轉」是一種不需要重新開機的作業，而且會保留 VM 的記憶體。 它會造成暫停或凍結，通常不會超過5秒。 除了 G、M、N 和 H 系列以外，所有基礎結構即服務（IaaS） Vm 都符合即時移轉的資格。 合格的 Vm 代表已部署至 Azure 車隊的 IaaS Vm 90% 以上。 

Azure 平臺會在下列案例中開始進行即時移轉：
- 預定的維修
- 硬體失敗
- 配置優化

某些規劃的維護案例會使用即時移轉，而您可以在即時移轉作業開始時，使用 Scheduled Events 來事先瞭解。

即時移轉也可以在 Azure Machine Learning 演算法預測即將發生的硬體故障或您想要優化 VM 配置時，用來移動 Vm。 如需偵測已降級硬體之實例的預測性模型的詳細資訊，請參閱[使用預測性機器學習和即時移轉來改善 AZURE VM 復原](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)。 如果您使用這些服務，即時移轉通知會出現在監視器和服務健康狀態記錄以及 Scheduled Events 中的 Azure 入口網站。

## <a name="maintenance-that-requires-a-reboot"></a>需要重新開機的維護

在需要重新開機 Vm 以進行預定維護的罕見情況下，您會事先收到通知。 規劃的維護有兩個階段：自助階段和排定的維護階段。

在*自助階段*期間，通常會持續四周，您可以開始在 vm 上進行維護。 作為自助服務的一部分，您可以查詢每個 VM，以查看其狀態和上一次維護要求的結果。

當您開始進行自助維護時，您的 VM 會重新部署至已經更新的節點。 因為 VM 會重新開機，所以暫存磁片會遺失，而且會更新與虛擬網路介面相關聯的動態 IP 位址。

如果在自助式維護期間發生錯誤，作業會停止，VM 不會更新，您可以選擇重試自助式維護。 

當自助階段結束時，*排程的維護階段*便會開始。 在此階段中，您仍然可以查詢維護階段，但無法自行開始進行維護。

如需管理需要重新開機之維護的詳細資訊，請參閱使用 Azure [CLI](maintenance-notifications-cli.md)、 [PowerShell](maintenance-notifications-powershell.md)或[入口網站](maintenance-notifications-portal.md)**處理預定的維護通知**。 

### <a name="availability-considerations-during-scheduled-maintenance"></a>排定的維護期間的可用性考慮 

如果您決定等到排定的維護階段，您應該考慮幾件事，以維持 Vm 的最高可用性。 

#### <a name="paired-regions"></a>配對區域

每個 Azure 區域都會與相同地理位置內的另一個區域配對。 兩者一起建立一個區域配對。 在排定的維護階段期間，Azure 只會更新區域配對中單一區域的 Vm。 例如，在美國中北部更新 VM 時，Azure 不會同時更新美國中南部的任何 VM。 不過，像是北歐等其他區域可以和美國東部一同進行維護。 了解區域配對的運作方式可協助您將 VM 更妥善地分散於各區域。 如需詳細資訊，請參閱 [Azure 區域配對](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

#### <a name="availability-sets-and-scale-sets"></a>可用性設定組和擴展集

在 Azure Vm 上部署工作負載時，您可以在*可用性設定組*內建立 vm，為您的應用程式提供高可用性。 使用可用性設定組，您可以確保在需要重新開機的中斷或維護事件期間，至少有一部 VM 可供使用。

在可用性設定組內，個別 VM 會散佈在多達 20 個更新網域。 在排定的維護期間，在任何指定時間都只會更新一個更新網域。 更新網域不一定會依序更新。 

虛擬機器*擴展集*是一種 Azure 計算資源，可讓您用來部署和管理一組完全相同的 vm 作為單一資源。 擴展集會自動部署在 ud 之間，例如可用性設定組中的 Vm。 如同可用性設定組，當您使用擴展集時，在排定的維護期間，任何指定的時間都只會更新一個 UD。

如需設定 Vm 以獲得高可用性的詳細資訊，請參閱管理適用于 [Windows 的 vm 可用性](./windows/manage-availability.md)或適用于[Linux](./linux/manage-availability.md)的對應文章。

#### <a name="availability-zones"></a>可用性區域

可用性區域是 Azure 區域內的唯一實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原能力，在所有已啟用的地區中都至少要有三個個別的區域。 

「可用性區域」是容錯網域和更新網域的組合。 如果您在 Azure 區域中的三個區域上建立三個或多個 Vm，則您的 Vm 會有效率地分散到三個容錯網域和三個更新網域。 Azure 平台會從更新網域中辨識此分佈，以確定不會同時更新不同區域中的 VM。

每個基礎結構更新會在單一區域內依區域推出區域。 但是，您可以在區域1中進行部署，並同時在區域2中進行不同的部署。 部署並未全部序列化。 但是，單一部署一次只會匯總一個區域，以降低風險。

## <a name="next-steps"></a>後續步驟 

您可以使用[Azure CLI](maintenance-notifications-cli.md)、 [Azure PowerShell](maintenance-notifications-powershell.md)或[入口網站](maintenance-notifications-portal.md)來管理預定進行的維護。 