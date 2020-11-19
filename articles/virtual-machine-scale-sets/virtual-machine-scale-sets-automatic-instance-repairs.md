---
title: 使用 Azure 虛擬機器擴展集的自動實例修復
description: 瞭解如何為擴展集中的 VM 實例設定自動修復原則
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ae508754775d4eb622d8e91ef58eb0d6e1c45692
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94889009"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Azure 虛擬機器擴展集的執行個體自動修復

針對 Azure 虛擬機器擴展集啟用自動實例修復，可透過維護一組狀況良好的實例，以達到應用程式的高可用性。 如果發現擴展集中的實例狀況不良，因為 [應用程式健康情況延伸](./virtual-machine-scale-sets-health-extension.md) 模組或 [負載平衡器健全狀況探查](../load-balancer/load-balancer-custom-probe-overview.md)所回報，則這項功能會藉由刪除狀況不良的實例，並建立新的實例來取代它，來自動執行實例修復。

## <a name="requirements-for-using-automatic-instance-repairs"></a>使用自動實例修復的需求

**啟用擴展集的應用程式健康情況監視**

擴展集應已啟用實例的應用程式健康情況監視。 您可以使用 [應用程式健康情況擴充](./virtual-machine-scale-sets-health-extension.md) 功能或 [負載平衡器健康情況探查](../load-balancer/load-balancer-custom-probe-overview.md)來完成此操作。 一次只能啟用其中一個。 應用程式健康情況延伸模組或負載平衡器探查會偵測虛擬機器實例上設定的應用程式端點，以判斷應用程式健康情況狀態。 擴展集協調器會使用此健康情況狀態來監視實例健全狀況，並在需要時執行修復。

**設定端點以提供健康狀態**

啟用自動實例修復原則之前，請確定擴展集實例已設定應用程式端點來發出應用程式健康情況狀態。 當實例傳回此應用程式端點上的狀態 200 (確定) ，則實例會標示為「狀況良好」。 在所有其他情況下，實例會標示為「狀況不良」，包括下列案例：

- 當虛擬機器實例內沒有設定應用程式端點來提供應用程式健康情況狀態時
- 當應用程式端點設定不正確時
- 無法連線到應用程式端點時

針對標示為「狀況不良」的實例，自動修復會由擴展集觸發。 在啟用自動修復原則之前，請確定已正確設定應用程式端點，以避免在設定端點時，發生非預期的實例修復。

**擴展集中的實例數目上限**

這項功能目前僅適用于最多200實例的擴展集。 擴展集可以部署為單一放置群組或多個放置群組，但如果擴展集已啟用自動實例修復，則實例計數不能超過200。

**API 版本**

計算 API 2018-10-01 版或更高版本支援自動修復原則。

**資源或訂用帳戶移動的限制**

啟用自動修復功能時，目前不支援對擴展集進行資源或訂用帳戶移動。

**Service fabric 擴展集的限制**

Service fabric 擴展集目前不支援此功能。

## <a name="how-do-automatic-instance-repairs-work"></a>自動實例修復如何運作？

自動實例修復功能依賴擴展集中個別實例的健康情況監視。 擴展集中的 VM 實例可以設定為使用 [應用程式健康情況延伸](./virtual-machine-scale-sets-health-extension.md) 模組或 [負載平衡器健康情況探查](../load-balancer/load-balancer-custom-probe-overview.md)來發出應用程式健康情況狀態。 如果發現實例狀況不良，則擴展集會藉由刪除狀況不良的實例，並建立新的實例來執行修復動作，以取代它。 最新的虛擬機器擴展集模型是用來建立新的實例。 您可以使用 *automaticRepairsPolicy* 物件，在虛擬機器擴展集模型中啟用這項功能。

### <a name="batching"></a>批次處理

自動實例修復作業會以批次方式執行。 在任何指定的時間，擴展集中的實例不超過5% 會透過自動修復原則來修復。 這有助於避免同時刪除和重新建立大量實例（如果發現狀況不良）。

### <a name="grace-period"></a>寬限期

當實例因為 PUT、PATCH 或 POST 動作在擴展集上執行而經歷狀態變更作業 (例如重新安裝映射、重新部署、更新等 ) ，則只有在等候寬限期之後，才會執行該實例上的任何修復動作。 寬限期是允許實例恢復正常狀態的時間量。 寬限期會在狀態變更完成後啟動。 這有助於避免任何過早或意外的修復作業。 擴展集內任何新建立的實例都會接受寬限期， (包括因為修復作業) 所建立的實例。 以 ISO 8601 格式指定的寬限期（以分鐘為單位），而且可以使用 *automaticRepairsPolicy. gracePeriod* 屬性來設定。 寬限期的範圍可介於30分鐘到90分鐘之間，預設值為30分鐘。

### <a name="suspension-of-repairs"></a>修復暫停 

虛擬機器擴展集可讓您視需要暫時暫停自動實例修復。 在虛擬機器擴展集的實例中，[屬性 *orchestrationServices* ] 下的 [自動修復] *serviceState* 會顯示自動修復的目前狀態。 當擴展集加入宣告自動修復時，[參數 *serviceState* ] 的值會設定為 [*執行中]。* 當擴展集的自動修復暫止時，參數 *serviceState* 會設定為 [已 *暫停*]。 如果 *automaticRepairsPolicy* 定義在擴展集上，但未啟用自動修復功能，則參數 *serviceState* 會設定為 [ *未* 執行]。

如果新建立的實例取代擴展集中的狀況不良，即使在重複執行修復作業之後，仍會繼續保持狀況不良，然後以安全性措施的方式，將 *serviceState* 更新為自動修復以 *暫停*。 您可以藉由將 [自動修復] 的 [ *serviceState* ] 值 *設定為 [* 執行中]，再次繼續自動修復。 如需詳細指示，請見關於如何針對擴展集的 [自動修復原則來查看和更新服務狀態](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) 。 

自動實例修復程式的運作方式如下：

1. [應用程式健康情況延伸](./virtual-machine-scale-sets-health-extension.md) 模組或 [負載平衡器健全狀況探查](../load-balancer/load-balancer-custom-probe-overview.md) 會偵測擴展集中每個虛擬機器內的應用程式端點，以取得每個實例的應用程式健康情況狀態。
2. 如果端點以狀態200回應 (確定) ，則實例會標示為「狀況良好」。 在其他所有情況下 (包括端點無法連線) ，則實例會標示為「狀況不良」。
3. 當發現實例狀況不良時，擴展集會藉由刪除狀況不良的實例，並建立新的實例來取代它，以觸發修復動作。
4. 實例修復會以批次方式執行。 在任何指定的時間內，擴展集內的實例總數不超過5% 的修復。 如果擴展集的實例少於20個，一次就會針對一個狀況不良的實例進行修復。
5. 上述程式會繼續進行，直到擴展集中的所有狀況不良實例都已修復為止。

## <a name="instance-protection-and-automatic-repairs"></a>實例保護和自動修復

如果擴展集中的實例是藉由套用其中一個 [保護原則](./virtual-machine-scale-sets-instance-protection.md)來保護，則不會在該實例上執行自動修復。 這同時適用于保護原則： *防止相應縮小* ，並 *防止調整規模設定* 動作。 

## <a name="terminatenotificationandautomaticrepairs"></a>終止通知和自動修復

如果已在擴展集上啟用 [終止通知](./virtual-machine-scale-sets-terminate-notification.md) 功能，則在自動修復作業期間，若刪除狀況不良的實例，將會遵循終止通知設定。 終止通知是透過 Azure 中繼資料服務（排程的事件）來傳送，而實例刪除會在設定的延遲超時時間延遲。 但是，建立新的實例來取代狀況不良的實例，並不會等候延遲的完成時間。

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>在建立新的擴展集時啟用自動修復原則

若要在建立新的擴展集時啟用自動修復原則，請確定已符合加入宣告這項功能的所有 [需求](#requirements-for-using-automatic-instance-repairs) 。 應用程式端點應針對擴展集實例正確設定，以避免在設定端點時觸發非預期的修復。 針對新建立的擴展集，系統只會在等候寬限期的持續期間之後，才執行任何實例修復。 若要在擴展集中啟用自動實例修復，請使用虛擬機器擴展集模型中的 *automaticRepairsPolicy* 物件。

您也可以使用此 [快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) 來部署虛擬機器擴展集，其中包含負載平衡器健康情況探查，以及在寬限期30分鐘內啟用的自動實例修復。

### <a name="azure-portal"></a>Azure 入口網站
 
下列步驟會在建立新的擴展集時啟用自動修復原則。
 
1. 移至 **虛擬機器擴展集**。
1. 選取 [ **+ 新增** ] 以建立新的擴展集。
1. 移至 [ **健康** 情況] 索引標籤。 
1. 找出 **健全狀況** 區段。
1. 啟用 [ **監視應用程式健全狀況** ] 選項。
1. 找出 [ **自動修復原則** ] 區段。
1. 開啟 **[** **自動修復** ] 選項。
1. 在 **寬限期 (min)** 中，指定寬限期（以分鐘為單位），允許的值介於30到90分鐘。 
1. 當您完成建立新的擴展集時，請選取 [ **審核 + 建立** ] 按鈕。

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

使用 [>new-azvmssconfig](/powershell/module/az.compute/new-azvmssconfig) Cmdlet 建立新的擴展集時，可以啟用自動實例修復功能。 此範例腳本會逐步解說如何使用設定檔建立擴展集和相關聯的資源： [建立完整的虛擬機器擴展集](./scripts/powershell-sample-create-complete-scale-set.md)。 您可以藉由將 *EnableAutomaticRepair* 和 *AutomaticRepairGracePeriod* 參數新增至設定物件來建立擴展集，來設定自動實例修復原則。 下列範例會啟用寬限期30分鐘的功能。

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

下列範例會在使用 *[az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)* 建立新的擴展集時啟用自動修復原則。 首先，建立資源群組，然後建立新的擴展集，並將自動修復原則寬限期設定為30分鐘。

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

上述範例使用現有的負載平衡器和健康情況探查來監視實例的應用程式健康情況狀態。 如果您想要改為使用應用程式健全狀況延伸模組進行監視，您可以建立擴展集、設定應用程式健康情況延伸模組，然後使用 *az vmss update* 來啟用自動實例修復原則，如下一節所述。

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>在更新現有的擴展集時啟用自動修復原則

在現有的擴展集中啟用自動修復原則之前，請確定已符合加入宣告這項功能的所有 [需求](#requirements-for-using-automatic-instance-repairs) 。 應用程式端點應針對擴展集實例正確設定，以避免在設定端點時觸發非預期的修復。 若要在擴展集中啟用自動實例修復，請使用虛擬機器擴展集模型中的 *automaticRepairsPolicy* 物件。

更新現有擴展集的模型之後，請確定最新的模型已套用至所有規模的實例。 請參閱 [如何使用最新的擴展集模型將 vm 帶到](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)最新狀態的指示。

### <a name="azure-portal"></a>Azure 入口網站

您可以透過 Azure 入口網站修改現有擴展集的自動修復原則。 
 
1. 移至現有的虛擬機器擴展集。
1. 在左側功能表的 [ **設定** ] 底下，選取 [ **健全狀況和修復**]。
1. 啟用 [ **監視應用程式健全狀況** ] 選項。
1. 找出 [ **自動修復原則** ] 區段。
1. 開啟 **[** **自動修復** ] 選項。
1. 在 **寬限期 (min)** 中，指定寬限期（以分鐘為單位），允許的值介於30到90分鐘。 
1. 完成之後，請選取 [儲存]。 

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

使用 [new-azvmss](/powershell/module/az.compute/update-azvmss) 指令程式，在現有的擴展集內修改自動實例修復功能的設定。 下列範例會將寬限期更新為40分鐘。

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

以下範例示範如何使用 *[az vmss update](/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)* 來更新現有擴展集的自動實例修復原則。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>查看及更新自動實例修復原則的服務狀態

### <a name="rest-api"></a>REST API 

針對虛擬機器擴展集使用具有 API 2019-12-01 版或更高版本的 [[取得實例視圖](/rest/api/compute/virtualmachinescalesets/getinstanceview)]，以在屬性 *orchestrationServices* 下查看自動修復的 *serviceState* 。 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

使用 *setOrchestrationServiceState* api 搭配虛擬機器擴展集上的 api 版本2019-12-01 或更高版本，以設定自動修復的狀態。 一旦將擴展集加入宣告自動修復功能，您就可以使用此 API 來暫停或繼續擴展集的自動修復。 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

使用 [get 實例-view](/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) Cmdlet 來查看自動實例修復的 *serviceState* 。 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

使用 [設定協調流程-服務狀態](/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state) Cmdlet 來更新 *serviceState* ，以進行自動實例修復。 一旦將擴展集加入宣告自動修復功能之後，您就可以使用此 Cmdlet 來暫停或繼續擴展集的自動修復。 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

使用 [new-azvmss 指令程式](/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) 搭配參數 *InstanceView* 來查看自動實例修復的 *ServiceState* 。

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

使用 Set-AzVmssOrchestrationServiceState Cmdlet 來更新 *serviceState* ，以進行自動實例修復。 一旦將擴展集加入至自動修復功能，您就可以使用此 Cmdlet 來暫停或繼續擴展集的自動修復。

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>疑難排解

**無法啟用自動修復原則**

如果您收到「BadRequest」錯誤訊息，指出「找不到類型 ' properties ' 的物件上的成員 ' automaticRepairsPolicy '」，則請檢查虛擬機器擴展集所使用的 API 版本。 這項功能需要 API 版本2018-10-01 或更高版本。

**即使已啟用原則，仍無法修復實例**

實例可能在寬限期內。 這是在執行修復之前，實例上的任何狀態變更之後要等待的時間量。 這是為了避免任何過早或意外的修復。 完成實例的寬限期後，就會發生修復動作。

**正在查看擴展集實例的應用程式健全狀況狀態**

您可以針對虛擬機器擴展集中的實例，使用「 [取得實例」視圖 API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) 來查看應用程式健康情況狀態。 使用 Azure PowerShell 時，您可以使用 [>get-azvmssvm](/powershell/module/az.compute/get-azvmssvm) 搭配 *-InstanceView* 旗標。 應用程式健康情況狀態會在屬性 *vmHealth* 下提供。

在 Azure 入口網站中，您也可以看到健康情況狀態。 移至現有的擴展集，從左側功能表中選取 [ **實例** ]，然後查看每個擴展集實例健康情況狀態的 [ **健全狀況狀態** ] 資料行。 

## <a name="next-steps"></a>後續步驟

瞭解如何為您的擴展集設定 [應用程式健康情況擴充](./virtual-machine-scale-sets-health-extension.md) 功能或 [負載平衡器健全狀況探查](../load-balancer/load-balancer-custom-probe-overview.md) 。
