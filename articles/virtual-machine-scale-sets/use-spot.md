---
title: 建立使用 Azure 點 Vm 的擴展集
description: 瞭解如何建立使用點 Vm 的 Azure 虛擬機器擴展集，以節省成本。
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 03/25/2020
ms.reviewer: jagaveer
ms.custom: jagaveer
ms.openlocfilehash: 756e0d62927b67d26ae75af90c64facfe9c92d31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84310558"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>適用于虛擬機器擴展集的 Azure 位置 Vm 

在擴展集上使用 Azure 位置可讓您以可觀的成本節約，利用我們未使用的容量。 Azure 基礎結構會在任何時間點回復此容量，以收回點實例。 因此，點實例適用于可處理中斷的工作負載，例如批次處理作業、開發/測試環境、大型計算工作負載等。

可用容量的數量可能會因大小、區域、時間和更多而有所不同。 在擴展集上部署點實例時，只有在有可用容量的情況下，Azure 才會配置實例，但這些實例沒有 SLA。 「點」擴展集會部署在單一容錯網域中，且不提供高可用性保證。


## <a name="pricing"></a>定價

「點」實例的定價是以區域和 SKU 為依據的變數。 如需詳細資訊，請參閱[Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)的定價。 


有了可變的定價，您可以選擇使用最多5個小數位數來設定最大價格（以美元（美元）為單位）。 例如，此值 `0.98765` 是每小時 $0.98765 美元的最大價格。 如果您將最大價格設為 `-1` ，則不會根據價格來收回實例。 實例的價格將會是標準實例的目前價格或價格（這種情況較少），只要有可用的容量和配額即可。

## <a name="eviction-policy"></a>收回原則

建立「點」擴展集時，您可以將收回原則設定為*解除配置*（預設值）或*刪除*。 

*解除配置*原則會將您收回的實例移至已停止（已解除配置）狀態，讓您重新部署已收回的實例。 不過，不保證配置會成功。 已解除配置的 VM 會依擴展集執行個體配額計算，您將需要支付您的基礎磁碟費用。 

如果您想要在您的位置擴展集中刪除實例，當它們被收回時，您可以將收回原則設定為 [*刪除*]。 收回原則設定為刪除的情況下，您可以藉由增加擴展集執行個體計數屬性來建立新的 VM。 Azure 會一同刪除已收回的 VM 與其基礎磁碟，因此您將不需要支付此儲存體的費用。 您也可以使用擴展集的自動調整功能，自動嘗試和補償收回的 VM，不過不保證配置會成功。 建議您在將收回原則設定為 [刪除] 時，只在「點」擴展集上使用自動調整功能，以避免您的磁片成本並達到配額限制。 

使用者可以選擇透過[Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md)接收 VM 內通知。 這會在您的 Vm 被收回時通知您，而且您將有30秒的時間完成任何作業，並在收回前執行關閉工作。 


## <a name="deploying-spot-vms-in-scale-sets"></a>在擴展集中部署點 Vm

若要在擴展集上部署點 Vm，您可以將新的*優先權*旗標設定為 [*點*]。 擴展集中的所有 Vm 都將設定為 [點]。 若要建立具有點 Vm 的擴展集，請使用下列其中一種方法：
- [Azure 入口網站](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure 資源管理員範本](#resource-manager-templates)

## <a name="portal"></a>入口網站

建立使用點 Vm 之擴展集的程式，與使用者入門[文章](quick-create-portal.md)中所述的流程相同。 當您部署擴展集時，您可以選擇設定「點」旗標和收回原則：使用「 ![ 點」 vm 來建立擴展集](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

建立具有點 Vm 之擴展集的程式，與使用者入門[文章](quick-create-cli.md)中所述的流程相同。 只要新增 [--Priority 位置] 和 [新增] 即可 `--max-price` 。 在此範例中，我們 `-1` 會使用 for， `--max-price` 讓實例不會根據價格來收回。

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

建立具有點 Vm 之擴展集的程式，與使用者入門[文章](quick-create-powershell.md)中所述的流程相同。
只需新增「-優先順序點」，並將提供 `-max-price` 給[new-azvmssconfig](/powershell/module/az.compute/new-azvmssconfig)。

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

建立使用點 Vm 之擴展集的程式，與適用于[Linux](quick-create-template-linux.md)或[Windows](quick-create-template-windows.md)的使用者入門文章中所述的流程相同。 

針對點範本部署，請使用 `"apiVersion": "2019-03-01"` 或更新版本。 將 `priority` 、 `evictionPolicy` 和屬性新增 `billingProfile` 至 `"virtualMachineProfile":` 範本中的區段： 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

若要在收回實例之後將它刪除，請將 `evictionPolicy` 參數變更為 `Delete` 。

## <a name="faq"></a>常見問題集

**問：** 建立後，它是一個與標準實例相同的點實例嗎？

**答：** 是，但沒有適用于點 Vm 的 SLA，而且可以隨時收回。


**問：** 當您收回但仍需要容量時，該怎麼辦？

**答：** 如果您需要立即使用容量，建議您使用標準 Vm，而不是找出 Vm。


**問：** 如何管理適用于點的配額？

**答：** 點實例和標準實例會有不同的配額集區。 點配額會在 Vm 與擴展集實例之間共用。 如需詳細資訊，請參閱[Azure 訂用帳戶和服務限制、配額和條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。


**問：** 我可以要求額外的時間配額嗎？

**答：** 是，您可以提交要求以增加您的配額，以透過[標準配額要求](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)程式來找出您的 vm。


**問：** 我可以將現有的擴展集轉換成點擴展集嗎？

**答：** 否， `Spot` 只有在建立時才支援設定旗標。


**問：** 如果我使用 `low` 的是低優先順序的擴展集，是否需要改為開始使用 `Spot` ？

**答：** 目前，和都可以使用 `low` `Spot` ，但您應該使用開始轉換成 `Spot` 。


**問：** 我可以同時建立具有一般 Vm 和點 Vm 的擴展集嗎？

**答：** 否，擴展集不能支援一種以上的優先順序類型。


**問：** 我可以搭配使用自動調整與點擴展集嗎？

**答：** 是，您可以在您的位置擴展集上設定自動調整規則。 如果您的 Vm 已收回，自動調整可以嘗試建立新的點 Vm。 請記住，不保證容量足夠。 


**問：** 自動調整會同時使用收回原則（解除配置和刪除）嗎？

**答：** 建議您在使用自動調整時，將收回原則設定為 [刪除]。 這是因為解除配置執行個體是按照擴展集的容量計數進行計算。 使用自動調整時，由於已解除配置收回的執行個體，可能會很快達到目標執行個體計數。 


**問：** 哪些通道支援找出 Vm 的位置？

**答：** 請參閱下表中的點機可用性。

<a name="channel"></a>

| Azure 頻道               | Azure 點 Vm 可用性       |
|------------------------------|-----------------------------------|
| Enterprise 合約         | Yes                               |
| 隨用隨付                | Yes                               |
| 雲端服務提供者 (CSP) | [聯絡您的合作夥伴](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| 優點                     | 無法使用                     |
| 商                    | Yes                               |
| 免費試用                   | 無法使用                     |


**問：** 我可以在何處張貼問題？

**答：** 您可以 `azure-spot` 在[Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html)張貼和標記您的問題。 

## <a name="next-steps"></a>後續步驟

如需定價詳細資料，請參閱[虛擬機器擴展集定價網頁](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)。
