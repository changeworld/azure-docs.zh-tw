---
title: 建立使用 Azure 位置 Vm 的擴展集
description: 瞭解如何建立 Azure 虛擬機器擴展集，以使用現成的 Vm 來節省成本。
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 03/25/2020
ms.reviewer: jagaveer
ms.custom: jagaveer, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b5888000028ba87d503bb0bc690aad6628a51a37
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072735"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>適用于虛擬機器擴展集的 Azure 位置 Vm 

在擴展集上使用 Azure 位置可讓您以大幅節省的成本來利用未使用的容量。 Azure 基礎結構會在任何時間點恢復容量，Azure 基礎結構將會收回點實例。 因此，找出實例很適合用來處理中斷的工作負載，例如批次處理工作、開發/測試環境、大型計算工作負載等。

可用容量的數量可能會因大小、區域、當日時間及更多而異。 在擴展集上部署點實例時，只有在有可用的容量時，Azure 才會配置實例，但這些實例沒有 SLA。 點擴展集會部署在單一容錯網域中，且不提供高可用性保證。


## <a name="pricing"></a>定價

點實例的定價是根據區域和 SKU 的變數。 如需詳細資訊，請參閱 [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)的定價。 


有了變數定價，您可以選擇使用最多5個小數位數，以美元為單位來設定最大價格（以美元為單位） (USD) 。 例如，值的 `0.98765` 最大價格為每小時 $0.98765 美元。 如果您將最大價格設定為 `-1` ，就不會根據價格來收回實例。 如果有可用的容量和配額，則實例的價格將會是標準實例的目前價格，也就是較低的價格。

## <a name="eviction-policy"></a>收回原則

建立「找不到」擴展集時，您可以設定收回原則，以 *解除配置* (預設) 或 *刪除*。 

*解除配置*原則會將您已收回的實例移到已停止解除配置的狀態，讓您重新部署已收回的實例。 不過，不保證配置會成功。 已解除配置的 VM 會依擴展集執行個體配額計算，您將需要支付您的基礎磁碟費用。 

如果您想要在您的位置擴展集中刪除您的實例，您可以將收回原則設定為 [ *刪除*]。 收回原則設定為刪除的情況下，您可以藉由增加擴展集執行個體計數屬性來建立新的 VM。 Azure 會一同刪除已收回的 VM 與其基礎磁碟，因此您將不需要支付此儲存體的費用。 您也可以使用擴展集的自動調整功能，自動嘗試和補償收回的 VM，不過不保證配置會成功。 建議您在將收回原則設定為 [刪除] 時，只在 [點擴展集] 上使用自動調整功能，以避免您的磁片成本並達到配額限制。 

使用者可以選擇透過 [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md)接收 VM 內通知。 這會在您的 Vm 被收回時通知您，而且您將有30秒的時間來完成任何工作，並在收回之前執行關機工作。 

## <a name="placement-groups"></a>放置群組
放置群組是類似于 Azure 可用性設定組的結構，其具有自己的容錯網域和升級網域。 根據預設，擴展集包含一個大小上限為 100 個 VM 的位置群組。 如果呼叫的擴展集屬性 `singlePlacementGroup` 設為 *false*，則擴展集可以由多個放置群組所組成，且範圍為 0-1000 個 vm。 

> [!IMPORTANT]
> 除非您使用的是與 HPC 搭配的功能，否則強烈建議您將擴展集屬性設 `singlePlacementGroup` 為 *false* ，以啟用多個放置群組，以便在區域或區域之間進行更佳的調整。 

## <a name="deploying-spot-vms-in-scale-sets"></a>在擴展集中部署點 Vm

若要在擴展集上部署現成的 Vm，您可以設定新的 *優先權* 旗標以 *找*出。 擴展集中的所有 Vm 將會設定為「現成」。 若要建立具有點 Vm 的擴展集，請使用下列其中一種方法：
- [Azure 入口網站](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure 資源管理員範本](#resource-manager-templates)

## <a name="portal"></a>入口網站

建立使用點 Vm 之擴展集的程式，與使用者入門 [文章](quick-create-portal.md)中所述的程式相同。 當您部署擴展集時，可以選擇設定點旗標和收回原則： ![ 使用點 vm 建立擴展集](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

使用現成的 Vm 建立擴展集的程式，與使用者入門 [文章](quick-create-cli.md)中所述的程式相同。 只需新增「--Priority 點」並加入 `--max-price` 。 在此範例中，我們使用 `-1` for，如此就不會 `--max-price` 根據價格來收回實例。

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

使用現成的 Vm 建立擴展集的程式，與使用者入門 [文章](quick-create-powershell.md)中所述的程式相同。
只需新增「-Priority 點」，並提供 `-max-price` 給 [>new-azvmssconfig](/powershell/module/az.compute/new-azvmssconfig)。

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Resource Manager 範本

建立使用點 Vm 之擴展集的程式，與 [Linux](quick-create-template-linux.md) 或 [Windows](quick-create-template-windows.md)的使用者入門文章中所述的程式相同。 

針對「點範本」部署，請使用 `"apiVersion": "2019-03-01"` 或更新版本。 

將、和屬性新增至區段，並將屬性新增至 `priority` `evictionPolicy` `billingProfile` `"virtualMachineProfile":` `"singlePlacementGroup": false,` `"Microsoft.Compute/virtualMachineScaleSets"` 範本中的區段：

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

若要在收回實例之後將其刪除，請將 `evictionPolicy` 參數變更為 `Delete` 。

## <a name="faq"></a>常見問題集

**問：** 一旦建立之後，就會有與標準實例相同的位置實例嗎？

**答：** 是，除了現成的 Vm 沒有 SLA，而且可以隨時收回。


**問：** 當您收回但仍需要容量時，該怎麼辦？

**答：** 如果您需要立即使用容量，建議您使用標準 Vm，而不是找出 Vm。


**問：** 如何管理配額以找出配額？

**答：** 點實例和標準實例將會有不同的配額集區。 將在 Vm 和擴展集實例之間共用點配額。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額和條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md)。


**問：** 我可以要求找出額外的配額嗎？

**答：** 是，您可以提交要求，以透過 [標準配額要求](../azure-portal/supportability/per-vm-quota-requests.md)程式來增加您的現成 vm 配額。


**問：** 我可以將現有的擴展集轉換為找出擴展集嗎？

**答：** 否， `Spot` 只有在建立時才支援設定旗標。


**問：** 如果我使用 `low` 的是低優先順序擴展集，是否需要改為開始使用 `Spot` ？

**答：** 現在和都可以 `low` `Spot` 運作，但您應該開始轉換成使用 `Spot` 。


**問：** 我可以建立具有一般 Vm 和虛擬機器的擴展集嗎？

**答：** 否，擴展集不能支援一個以上的優先順序類型。


**問：**  我可以搭配使用自動調整與現成擴展集嗎？

**答：** 是，您可以在您的位置擴展集上設定自動調整規則。 如果您的 Vm 已收回，自動調整可能會嘗試建立新的點 Vm。 請記住，不保證容量足夠。 


**問：**  自動調整是否適用于收回原則， (解除配置和刪除) ？

**答：** 建議您在使用自動調整時，將收回原則設定為 [刪除]。 這是因為解除配置執行個體是按照擴展集的容量計數進行計算。 使用自動調整時，由於已解除配置收回的執行個體，可能會很快達到目標執行個體計數。 


**問：** 哪些通道支援現成的 Vm？

**答：** 請參閱下表以取得點 VM 可用性。

<a name="channel"></a>

| Azure 通道               | Azure 位置 Vm 可用性       |
|------------------------------|-----------------------------------|
| Enterprise 合約         | 是                               |
| 隨用隨付                | 是                               |
| 雲端服務提供者 (CSP) | [洽詢您的合作夥伴](/partner-center/azure-plan-get-started) |
| 優點                     | 無法使用                     |
| 贊助                    | 是                               |
| 免費試用                   | 無法使用                     |


**問：** 我可以在哪裡張貼問題？

**答：** 您可以 `azure-spot` 在 [Q&A](/answers/topics/azure-spot.html)張貼並標記問題。 

## <a name="next-steps"></a>後續步驟

如需定價詳細資料，請參閱[虛擬機器擴展集定價網頁](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)。
