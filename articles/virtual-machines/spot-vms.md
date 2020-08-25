---
title: 使用 Azure 位置 Vm
description: 瞭解如何使用 Azure 位置 Vm 來節省成本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/20/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: c0b8f395dde1d94c4c1efa32a2f78707d1456d88
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817540"
---
# <a name="use-spot-vms-in-azure"></a>在 Azure 中使用點 Vm

使用現成的 Vm 可讓您以大幅節省的成本來利用未使用的容量。 Azure 基礎結構會在任何時間點恢復容量，以找出 Vm。 因此，找出 Vm 很適合用來處理中斷的工作負載，例如批次處理作業、開發/測試環境、大型計算工作負載等。

可用容量的數量可能會因大小、區域、當日時間及更多而異。 部署現成的 Vm 時，如果有可用的容量，Azure 將會配置 Vm，但這些 Vm 不會有 SLA。 點 VM 不提供高可用性保證。 Azure 基礎結構會在有30秒的通知的任何時間點收回 Vm。 


## <a name="eviction-policy"></a>收回原則

您可以根據容量或您所設定的最大價格來收回 Vm。 建立點 VM 時，您可以將收回原則設定為 *解除配置* (預設) 或 *刪除*。 

*解除配置*原則會將您的 VM 移至已停止解除配置的狀態，讓您稍後重新部署。 不過，不保證配置會成功。 已解除配置的 Vm 將計入您的配額，而您將會支付基礎磁片的儲存體成本。 

如果您想要在收回 VM 時將其刪除，您可以將收回原則設定為 [ *刪除*]。 系統會將已收回的 Vm 與其基礎磁片一起刪除，因此不會繼續向您收取儲存體費用。 

您可以選擇透過 [Azure Scheduled Events](./linux/scheduled-events.md)接收 VM 內通知。 這會在您的 Vm 被收回時通知您，而且您將有30秒的時間來完成任何工作，並在收回之前執行關機工作。 


| 選項 | 結果 |
|--------|---------|
| 最大價格設定為 >= 目前的價格。 | 如果容量和配額可供使用，則會部署 VM。 |
| 最大價格設定為 < 目前的價格。 | 未部署 VM。 您會收到一則錯誤訊息，指出最大價格需要 >= 目前價格。 |
| 如果最大價格 >= 目前價格，則重新開機停止/解除配置 VM | 如果有容量和配額，則會部署 VM。 |
| 如果最大價格 < 目前價格，則重新開機停止/解除配置 VM | 您會收到一則錯誤訊息，指出最大價格需要 >= 目前價格。 | 
| VM 的價格已滿，現在已 > 最大價格。 | VM 會被收回。 在實際收回之前，您會收到30秒通知。 | 
| 收回之後，VM 的價格會回到 < 的最大價格。 | VM 將不會自動重新開機。 您可以自行重新開機 VM，它會以目前的價格計費。 |
| 如果最大價格設定為 `-1` | 因為定價原因，將不會收回 VM。 最大價格將會是目前的價格，最高可達標準 Vm 的價格。 您永遠不會被收取標準價格的費用。| 
| 變更最大價格 | 您必須將 VM 解除配置，才能變更最大價格。 解除配置 VM、設定新的最大價格，然後更新 VM。 |


## <a name="limitations"></a>限制

下列 VM 大小不支援用於找出 Vm：
 - B 系列
 - 任何大小 (的促銷版本，例如 Dv2、NV、NC、H 促銷大小) 

現成的 Vm 可以部署到任何區域，但 Microsoft Azure 中國世紀除外。

<a name="channel"></a>

目前支援下列 [供應專案類型](https://azure.microsoft.com/support/legal/offer-details/) ：

-   Enterprise 合約
-   隨用隨付
-   贊助
- 若為雲端服務提供者 (CSP) ，請洽詢您的合作夥伴


## <a name="pricing"></a>定價

現成 Vm 的定價是根據區域和 SKU 的變數。 如需詳細資訊，請參閱 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定價。 


有了變數定價，您可以選擇使用最多5個小數位數，以美元為單位來設定最大價格（以美元為單位） (USD) 。 例如，值的 `0.98765` 最大價格為每小時 $0.98765 美元。 如果您將最大價格設定為 `-1` ，將不會根據價格來收回 VM。 如果有可用的容量和配額，則 VM 的價格將是標準 VM 的目前價格或價格的價格。


##  <a name="frequently-asked-questions"></a>常見問題集

**問：** 一旦建立之後，就會有與一般標準 VM 相同的點 VM？

**答：** 是，除了現成的 Vm 沒有 SLA，而且可以隨時收回。


**問：** 當您收回但仍需要容量時，該怎麼辦？

**答：** 如果您需要立即使用容量，建議您使用標準 Vm，而不是找出 Vm。


**問：** 如何針對找出的 Vm 管理配額？

**答：** 現成的 Vm 將會有個別的配額集區。 將在 Vm 和擴展集實例之間共用點配額。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額和條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。


**問：** 我可以要求找出額外的配額嗎？

**答：** 是，您可以提交要求，以透過 [標準配額要求](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)程式來增加您的現成 vm 配額。


**問：** 我可以在哪裡張貼問題？

**答：** 您可以 `azure-spot` 在 [Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html)張貼並標記問題。 

## <a name="next-steps"></a>後續步驟
使用 [CLI](./linux/spot-cli.md)、 [入口網站](./windows/spot-portal.md)、 [ARM 範本](./linux/spot-template.md)或 [PowerShell](./windows/spot-powershell.md) 來部署現成的 vm。

您也可以 [使用點 VM 實例來部署擴展集](../virtual-machine-scale-sets/use-spot.md)。

如果您遇到錯誤，請參閱 [錯誤碼](./error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
