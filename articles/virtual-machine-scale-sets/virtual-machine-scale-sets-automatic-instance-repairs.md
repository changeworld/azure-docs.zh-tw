---
title: 使用 Azure 虛擬機器擴展集修復自動實例
description: 瞭解如何為擴展集中的 VM 實例設定自動修復原則
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 9e2b15eceff9bca4cee960fa462eb5148e3716dd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83197026"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Azure 虛擬機器擴展集的執行個體自動修復

針對 Azure 虛擬機器擴展集啟用自動實例修復，可以藉由維護一組狀況良好的實例，協助達到應用程式的高可用性。 如果擴展集中的實例發現[應用程式健康情況延伸](./virtual-machine-scale-sets-health-extension.md)模組或[負載平衡器健康情況探查](../load-balancer/load-balancer-custom-probe-overview.md)所回報的狀況不良，則此功能會藉由刪除狀況不良的實例並建立新的實例來加以取代，以自動執行實例修復。

## <a name="requirements-for-using-automatic-instance-repairs"></a>使用自動實例修復的需求

**啟用擴展集的應用程式健康情況監視**

擴展集應該具有已啟用實例的應用程式健康情況監視。 這可以使用[應用程式健康狀態延伸](./virtual-machine-scale-sets-health-extension.md)模組或[負載平衡器健康情況探查](../load-balancer/load-balancer-custom-probe-overview.md)來完成。 一次只能啟用其中一個。 應用程式健康情況延伸模組或負載平衡器探查會偵測虛擬機器實例上設定的應用程式端點，以判斷應用程式的健康狀態。 擴展集協調器會使用此健康情況狀態來監視實例健全狀況，並在需要時執行修復。

**設定端點以提供健全狀況狀態**

啟用自動實例修復原則之前，請確定擴展集實例已設定應用程式端點，以發出應用程式健康情況狀態。 當實例在此應用程式端點上傳回狀態200（確定）時，會將實例標示為「狀況良好」。 在所有其他情況下，實例會標示為「狀況不良」，包括下列案例：

- 當虛擬機器實例內未設定應用程式端點以提供應用程式健康狀態時
- 當應用程式端點的設定不正確時
- 當無法連線到應用程式端點時

針對標示為「狀況不良」的實例，自動修復會由擴展集觸發。 請先確定已正確設定應用程式端點，再啟用自動修復原則，以避免在設定端點時進行非預期的實例修復。

**啟用單一放置群組**

這項功能目前僅適用于部署為單一放置群組的擴展集。 您的擴展集應該將屬性*singlePlacementGroup*設定為*true* ，才能使用自動實例修復功能。 深入瞭解[放置群組](./virtual-machine-scale-sets-placement-groups.md#placement-groups)。

**API 版本**

計算 API 2018-10-01 版或更高版本支援自動修復原則。

**資源或訂用帳戶移動的限制**

當自動修復功能已啟用時，擴展集目前不支援資源或訂用帳戶移動。

**Service fabric 擴展集的限制**

Service fabric 擴展集目前不支援這項功能。

## <a name="how-do-automatic-instance-repairs-work"></a>自動實例修復如何正常執行？

自動實例修復功能會依賴擴展集中個別實例的健全狀況監視。 擴展集中的 VM 實例可以設定為使用[應用程式健康情況延伸](./virtual-machine-scale-sets-health-extension.md)模組或[負載平衡器健全狀況探查](../load-balancer/load-balancer-custom-probe-overview.md)來發出應用程式健康狀態。 如果發現實例狀況不良，擴展集會藉由刪除狀況不良的實例並建立新的實例來取代它，藉以執行修復動作。 最新的虛擬機器擴展集模型是用來建立新的實例。 您可以使用*automaticRepairsPolicy*物件，在虛擬機器擴展集模型中啟用這項功能。

### <a name="batching"></a>批次處理

自動實例修復作業會以批次方式執行。 在任何指定的時間，擴展集中的實例數目不會超過5%，而是透過自動修復原則來進行修復。 如果同時發現狀況不良，這有助於避免同時刪除和重新建立大量實例。

### <a name="grace-period"></a>寬限期

當實例因在擴展集上執行的 PUT、PATCH 或 POST 動作而進行狀態變更作業（例如重新安裝映射、重新部署、更新等等）時，只有在等候寬限期之後，才會執行該實例上的任何修復動作。 寬限期是允許實例回到狀況良好狀態的時間量。 寬限期會在狀態變更完成後開始。 這有助於避免任何過早或意外的修復作業。 擴展集內任何新建立的實例都會接受寬限期（包括做為修復作業的結果所建立的）。 寬限期是以 ISO 8601 格式指定，而且可以使用*automaticRepairsPolicy. gracePeriod*屬性來設定。 寬限期的範圍可以介於30分鐘到90分鐘之間，預設值為30分鐘。

### <a name="suspension-of-repairs"></a>修復暫停 

虛擬機器擴展集可讓您在需要時暫時暫止自動實例修復的功能。 [虛擬機器擴展集] 的 [在實例中*orchestrationServices* ] 屬性下的自動修復*serviceState*會顯示自動修復的目前狀態。 當擴展集選擇自動修復時，參數*serviceState*的值會設定為 [*執行中]。* 當擴展集的自動修復已暫停時，參數*serviceState*會設定為 [已*暫停*]。 如果*automaticRepairsPolicy*是在擴展集上定義，但是未啟用自動修復功能，則參數*serviceState*會設定為「*未*執行」。

如果新建立的實例取代擴展集中的狀況不良，即使重複執行修復作業之後，仍會繼續保持狀況不良，然後做為安全措施，則平臺會更新*serviceState* ，以讓自動修復*暫停*。 您可以將*serviceState*的值設定為 [自動修復]，以再次繼續自動*修復。* 如需詳細指示，請閱讀如何針對您的擴展集來[查看和更新自動修復原則的服務狀態](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy)一節中所述。 

自動實例修復進程的運作方式如下：

1. [應用程式健康情況延伸](./virtual-machine-scale-sets-health-extension.md)模組或[負載平衡器健全狀況探查](../load-balancer/load-balancer-custom-probe-overview.md)會偵測到擴展集中每個虛擬機器內的應用程式端點，以取得每個實例的應用程式健全狀況狀態。
2. 如果端點以狀態200（確定）回應，則實例會標示為「狀況良好」。 在所有其他情況下（包括無法連線到端點），實例會標示為「狀況不良」。
3. 當發現實例狀況不良時，擴展集會藉由刪除狀況不良的實例並建立新的實例來觸發修復動作，以取代它。
4. 實例修復會以批次方式執行。 在任何指定的時間，擴展集中的總實例總數不會超過5%。 如果擴展集的實例少於20個，則會一次對一個狀況不良的實例進行修復。
5. 上述程式會繼續進行，直到修復擴展集中所有狀況不良的實例為止。

## <a name="instance-protection-and-automatic-repairs"></a>實例保護和自動修復

如果擴展集中的實例受到應用其中一個[保護原則](./virtual-machine-scale-sets-instance-protection.md)的保護，則不會在該實例上執行自動修復。 這同時適用于保護原則： [*從相應縮小保護*] 和 [*從調整集保護*] 動作。 

## <a name="terminatenotificationandautomaticrepairs"></a>終止通知和自動修復

如果已在擴展集上啟用「[終止通知](./virtual-machine-scale-sets-terminate-notification.md)」功能，則在自動修復操作期間，刪除狀況不良的實例會遵循終止通知設定。 終止通知會透過 Azure 中繼資料服務（排程的事件）傳送，而在設定的延遲超時期間，會延遲刪除實例。 不過，建立新的實例來取代狀況不良的實例，並不會等待延遲超時完成。

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>在建立新的擴展集時啟用自動修復原則

在建立新的擴展集時，若要啟用自動修復原則，請確定已符合加入宣告這項功能的所有[需求](#requirements-for-using-automatic-instance-repairs)。 應用程式端點應正確設定為擴展集實例，以避免在設定端點時觸發非預期的修復。 針對新建立的擴展集，只有在等候寬限期的持續時間之後，才會執行任何實例修復。 若要在擴展集中啟用自動實例修復，請使用虛擬機器擴展集模型中的*automaticRepairsPolicy*物件。

您也可以使用此[快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe)來部署虛擬機器擴展集，其中包含負載平衡器健康情況探查，以及啟用自動實例修復，寬限期為30分鐘。

### <a name="azure-portal"></a>Azure 入口網站
 
下列步驟會在建立新的擴展集時啟用自動修復原則。
 
1. 移至 [**虛擬機器擴展集**]。
1. 選取 [ **+ 新增**] 以建立新的擴展集。
1. 移至 [**健康**情況] 索引標籤。 
1. 找出 [**健全狀況**] 區段。
1. 啟用 [**監視應用程式健全狀況**] 選項。
1. 找出 [**自動修復原則**] 區段。
1. 開啟 **[** **自動修復**] 選項。
1. 在 [**寬限期（分）**] 中，以分鐘為單位指定寬限期，允許的值介於30到90分鐘之間。 
1. 當您完成建立新的擴展集時，請選取 [**審核] + [建立**] 按鈕。

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

### <a name="azure-cli-20"></a>Azure CLI 2.0

下列範例會在使用*[az vmss create](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)* 建立新的擴展集時，啟用自動修復原則。 首先，建立資源群組，然後建立一個新的擴展集，並將 [自動修復] 原則寬限期設為30分鐘。

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

上述範例使用現有的負載平衡器和健康情況探查來監視實例的應用程式健全狀況狀態。 如果您想要改為使用應用程式健全狀況延伸模組來進行監視，您可以建立擴展集、設定應用程式健康狀態延伸模組，然後使用*az vmss update*啟用自動實例修復原則，如下一節中所述。

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>在更新現有的擴展集時啟用自動修復原則

在現有的擴展集內啟用自動修復原則之前，請確定已符合加入宣告這項功能的所有[需求](#requirements-for-using-automatic-instance-repairs)。 應用程式端點應正確設定為擴展集實例，以避免在設定端點時觸發非預期的修復。 若要在擴展集中啟用自動實例修復，請使用虛擬機器擴展集模型中的*automaticRepairsPolicy*物件。

更新現有擴展集的模型之後，請確定最新的模型已套用至該尺規的所有實例。 請參閱指示，[以瞭解如何使用最新的擴展集模型將 vm 保持在最新狀態](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)。

### <a name="azure-portal"></a>Azure 入口網站

您可以透過 Azure 入口網站，修改現有擴展集的自動修復原則。 
 
1. 移至現有的虛擬機器擴展集。
1. 在左側功能表的 [**設定**] 下，選取 [**健康情況和修復**]。
1. 啟用 [**監視應用程式健全狀況**] 選項。
1. 找出 [**自動修復原則**] 區段。
1. 開啟 **[** **自動修復**] 選項。
1. 在 [**寬限期（分）**] 中，以分鐘為單位指定寬限期，允許的值介於30到90分鐘之間。 
1. 完成之後，請選取 [儲存]****。 

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

### <a name="azure-cli-20"></a>Azure CLI 2.0

以下是使用*[az vmss update](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)* 來更新現有擴展集之自動實例修復原則的範例。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>查看和更新自動實例修復原則的服務狀態

### <a name="rest-api"></a>REST API 

針對虛擬機器擴展集使用[取得實例視圖](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview)搭配 API 版本2019-12-01 或更高版本，以在屬性*orchestrationServices*下查看自動修復的*serviceState* 。 

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

在虛擬機器擴展集上使用 API 版本2019-12-01 或更高的*setOrchestrationServiceState* api，以設定自動修復的狀態。 將擴展集加入自動修復功能之後，您就可以使用此 API 來暫停或繼續自動修復您的擴展集。 

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

使用[get-instance-view](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) Cmdlet 來查看自動實例修復的*serviceState* 。 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

使用[設定-協調流程-服務狀態](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state)Cmdlet 來更新*serviceState* ，以進行自動實例修復。 將擴展集加入自動修復功能之後，您就可以使用此 Cmdlet 來暫停或繼續自動修復您的擴展集。 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

使用[get-azvmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss?view=azps-3.7.0)指令程式搭配參數*InstanceView* ，以查看自動實例修復的*ServiceState* 。

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

使用 AzVmssOrchestrationServiceState Cmdlet 來更新自動實例修復的*serviceState* 。 將擴展集加入自動修復功能之後，您就可以使用此 Cmdlet 來暫停或繼續自動修復您的擴展集。

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>疑難排解

**無法啟用自動修復原則**

如果您收到「BadRequest」錯誤訊息，指出「找不到 ' properties ' 類型之物件上的成員 ' automaticRepairsPolicy '」，請檢查用於虛擬機器擴展集的 API 版本。 這項功能需要 API 2018-10-01 版或更新版本。

**實例未獲得修復，即使已啟用原則**

實例可能在寬限期內。 這是在執行修復之前，實例上的任何狀態變更後所要等待的時間量。 這是為了避免任何過早或意外的修復。 完成實例的寬限期之後，就會發生修復動作。

**正在查看擴展集實例的應用程式健康情況狀態**

您可以針對虛擬機器擴展集中的實例使用[取得實例視圖 API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) ，以查看應用程式健全狀況狀態。 使用 Azure PowerShell，您可以使用 Cmdlet [get-azvmssvm](/powershell/module/az.compute/get-azvmssvm)搭配 *-InstanceView*旗標。 應用程式健康狀態會在屬性*vmHealth*底下提供。

在 Azure 入口網站中，您也可以查看健全狀況狀態。 移至現有的擴展集，從左側功能表中選取 [**實例**]，然後查看 [**健全狀況狀態**] 資料行中每個擴展集實例的健全狀況狀態。 

## <a name="next-steps"></a>後續步驟

瞭解如何為您的擴展集設定[應用程式健康情況延伸](./virtual-machine-scale-sets-health-extension.md)模組或[負載平衡器健康情況探查](../load-balancer/load-balancer-custom-probe-overview.md)。
