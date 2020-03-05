---
title: 使用 Azure 虛擬機器擴展集修復自動實例
description: 瞭解如何為擴展集中的 VM 實例設定自動修復原則
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274810"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>預覽： Azure 虛擬機器擴展集的自動實例修復

針對 Azure 虛擬機器擴展集啟用自動實例修復，可以藉由維護一組狀況良好的實例，協助達到應用程式的高可用性。 如果擴展集中的實例發現[應用程式健康情況延伸](./virtual-machine-scale-sets-health-extension.md)模組或[負載平衡器健康情況探查](../load-balancer/load-balancer-custom-probe-overview.md)所回報的狀況不良，則此功能會藉由刪除狀況不良的實例並建立新的實例來加以取代，以自動執行實例修復。

> [!NOTE]
> 此預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。

## <a name="requirements-for-using-automatic-instance-repairs"></a>使用自動實例修復的需求

**加入宣告自動實例修復預覽**

請使用 REST API 或 Azure PowerShell 加入宣告自動實例修復預覽。 這些步驟會針對預覽功能註冊您的訂用帳戶。 請注意，這只是使用這項功能時所需的一次性設定。 如果您的訂用帳戶已註冊自動實例修復預覽，則您不需要再註冊一次。 

使用 REST API 

1. 使用功能註冊功能[-register](/rest/api/resources/features/register) 

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview/register?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registering"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

2. 等待幾分鐘，讓*狀態*變更為 [*已註冊*]。 您可以使用下列 API 來確認這一點。

```
GET on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registered"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

3. *狀態*變更為 [*已註冊*] 之後，請執行下列程式。

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

使用 Azure PowerShell

1. 使用 Cmdlet [register-register-azurermresourceprovider](/powershell/module/azurerm.resources/register-azurermresourceprovider) ，後面接著[register-azurermproviderfeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)註冊功能

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. 等候幾分鐘，讓*RegistrationState*變更為 [*已註冊*]。 您可以使用下列 Cmdlet 來確認這一點。

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 回應應如下所示。

| 功能                           | ProviderName            | RegistrationState       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | 已登錄              |

3. 當*RegistrationState*變更為 [*已註冊*] 之後，請執行下列 Cmdlet。

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**啟用擴展集的應用程式健康情況監視**

擴展集應該具有已啟用實例的應用程式健康情況監視。 這可以使用[應用程式健康狀態延伸](./virtual-machine-scale-sets-health-extension.md)模組或[負載平衡器健康情況探查](../load-balancer/load-balancer-custom-probe-overview.md)來完成。 一次只能啟用其中一個。 應用程式健康情況延伸模組或負載平衡器探查會偵測虛擬機器實例上設定的應用程式端點，以判斷應用程式的健康狀態。 擴展集協調器會使用此健康情況狀態來監視實例健全狀況，並在需要時執行修復。

**設定端點以提供健全狀況狀態**

啟用自動實例修復原則之前，請確定擴展集實例已設定應用程式端點，以發出應用程式健康情況狀態。 當實例在此應用程式端點上傳回狀態200（確定）時，會將實例標示為「狀況良好」。 在所有其他情況下，實例會標示為「狀況不良」，包括下列案例：

- 當虛擬機器實例內未設定應用程式端點以提供應用程式健康狀態時
- 當應用程式端點的設定不正確時
- 當無法連線到應用程式端點時

針對標示為「狀況不良」的實例，自動修復會由擴展集觸發。 請先確定已正確設定應用程式端點，再啟用自動修復原則，以避免在設定端點時進行非預期的實例修復。

**啟用單一放置群組**

此預覽目前僅適用于部署為單一放置群組的擴展集。 您的擴展集應該將屬性*singlePlacementGroup*設定為*true* ，才能使用自動實例修復功能。 深入瞭解[放置群組](./virtual-machine-scale-sets-placement-groups.md#placement-groups)。

**API 版本**

計算 API 2018-10-01 版或更高版本支援自動修復原則。

**資源或訂用帳戶移動的限制**

在此預覽中，當啟用自動修復原則時，擴展集目前不支援資源或訂用帳戶移動。

**Service fabric 擴展集的限制**

Service fabric 擴展集目前不支援此預覽功能。

## <a name="how-do-automatic-instance-repairs-work"></a>自動實例修復如何正常執行？

自動實例修復功能會依賴擴展集中個別實例的健全狀況監視。 擴展集中的 VM 實例可以設定為使用[應用程式健康情況延伸](./virtual-machine-scale-sets-health-extension.md)模組或[負載平衡器健全狀況探查](../load-balancer/load-balancer-custom-probe-overview.md)來發出應用程式健康狀態。 如果發現實例狀況不良，擴展集會藉由刪除狀況不良的實例並建立新的實例來取代它，藉以執行修復動作。 您可以使用*automaticRepairsPolicy*物件，在虛擬機器擴展集模型中啟用這項功能。

### <a name="batching"></a>批次處理

自動實例修復作業會以批次方式執行。 在任何指定的時間，擴展集中的實例數目不會超過5%，而是透過自動修復原則來進行修復。 如果同時發現狀況不良，這有助於避免同時刪除和重新建立大量實例。

### <a name="grace-period"></a>寬限期

當實例因在擴展集上執行的 PUT、PATCH 或 POST 動作而進行狀態變更作業（例如重新安裝映射、重新部署、更新等等）時，只有在等候寬限期之後，才會執行該實例上的任何修復動作。 寬限期是允許實例回到狀況良好狀態的時間量。 寬限期會在狀態變更完成後開始。 這有助於避免任何過早或意外的修復作業。 擴展集內任何新建立的實例都會接受寬限期（包括做為修復作業的結果所建立的）。 寬限期是以 ISO 8601 格式指定，而且可以使用*automaticRepairsPolicy. gracePeriod*屬性來設定。 寬限期的範圍可以介於30分鐘到90分鐘之間，預設值為30分鐘。

自動實例修復進程的運作方式如下：

1. [應用程式健康情況延伸](./virtual-machine-scale-sets-health-extension.md)模組或[負載平衡器健全狀況探查](../load-balancer/load-balancer-custom-probe-overview.md)會偵測到擴展集中每個虛擬機器內的應用程式端點，以取得每個實例的應用程式健全狀況狀態。
2. 如果端點以狀態200（確定）回應，則實例會標示為「狀況良好」。 在所有其他情況下（包括無法連線到端點），實例會標示為「狀況不良」。
3. 當發現實例狀況不良時，擴展集會藉由刪除狀況不良的實例並建立新的實例來觸發修復動作，以取代它。
4. 實例修復會以批次方式執行。 在任何指定的時間，擴展集中的總實例總數不會超過5%。 如果擴展集的實例少於20個，則會一次對一個狀況不良的實例進行修復。
5. 上述程式會繼續進行，直到修復擴展集中所有狀況不良的實例為止。

## <a name="instance-protection-and-automatic-repairs"></a>實例保護和自動修復

如果擴展集中的實例受到套用 [ *[從擴展集保護] 保護原則](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* 的保護，則不會在該實例上執行自動修復。

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>在建立新的擴展集時啟用自動修復原則

在建立新的擴展集時，若要啟用自動修復原則，請確定已符合加入宣告這項功能的所有[需求](#requirements-for-using-automatic-instance-repairs)。 應用程式端點應正確設定為擴展集實例，以避免在設定端點時觸發非預期的修復。 針對新建立的擴展集，只有在等候寬限期的持續時間之後，才會執行任何實例修復。 若要在擴展集中啟用自動實例修復，請使用虛擬機器擴展集模型中的*automaticRepairsPolicy*物件。

### <a name="rest-api"></a>REST API

下列範例顯示如何在擴展集模型中啟用自動實例修復。 使用 API 版本2018-10-01 或更高版本。

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

使用[new-azvmssconfig](/powershell/module/az.compute/new-azvmssconfig)指令程式建立新的擴展集時，可以啟用自動實例修復功能。 此範例腳本會逐步解說如何使用設定檔來建立擴展集和相關聯的資源：[建立完整的虛擬機器擴展集](./scripts/powershell-sample-create-complete-scale-set.md)。 您可以將*EnableAutomaticRepair*和*AutomaticRepairGracePeriod*參數新增至設定物件，以設定自動實例修復原則，以建立擴展集。 下列範例會在30分鐘的寬限期內啟用此功能。

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>在更新現有的擴展集時啟用自動修復原則

在現有的擴展集內啟用自動修復原則之前，請確定已符合加入宣告這項功能的所有[需求](#requirements-for-using-automatic-instance-repairs)。 應用程式端點應正確設定為擴展集實例，以避免在設定端點時觸發非預期的修復。 若要在擴展集中啟用自動實例修復，請使用虛擬機器擴展集模型中的*automaticRepairsPolicy*物件。

### <a name="rest-api"></a>REST API

下列範例會啟用寬限期為40分鐘的原則。 使用 API 版本2018-10-01 或更高版本。

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

使用[get-azvmss](/powershell/module/az.compute/update-azvmss) Cmdlet 來修改現有擴展集中自動實例修復功能的設定。 下列範例會將寬限期更新為40分鐘。

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>疑難排解

**無法啟用自動修復原則**

如果您收到「BadRequest」錯誤訊息，指出「找不到 ' properties ' 類型之物件上的成員 ' automaticRepairsPolicy '」，請檢查用於虛擬機器擴展集的 API 版本。 這項功能需要 API 2018-10-01 版或更新版本。

**實例未獲得修復，即使已啟用原則**

實例可能在寬限期內。 這是在執行修復之前，實例上的任何狀態變更後所要等待的時間量。 這是為了避免任何過早或意外的修復。 完成實例的寬限期之後，就會發生修復動作。

**正在查看擴展集實例的應用程式健康情況狀態**

您可以針對虛擬機器擴展集中的實例使用[取得實例視圖 API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) ，以查看應用程式健全狀況狀態。 使用 Azure PowerShell，您可以使用 Cmdlet [get-azvmssvm](/powershell/module/az.compute/get-azvmssvm)搭配 *-InstanceView*旗標。 應用程式健康狀態會在屬性*vmHealth*底下提供。

## <a name="next-steps"></a>後續步驟

瞭解如何為您的擴展集設定[應用程式健康情況延伸](./virtual-machine-scale-sets-health-extension.md)模組或[負載平衡器健康情況探查](../load-balancer/load-balancer-custom-probe-overview.md)。
