---
title: 使用 Azure 虛擬機器縮放集進行自動實例修復
description: 瞭解如何在規模集中為 VM 實體設定自動修復原則
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: 8156c563573183e51e06650914117f8787922e93
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603672"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Azure 虛擬機器縮放集的自動實體修復

為 Azure 虛擬機器擴展集啟用自動實例修復有助於透過維護一組正常實例實現應用程式的高可用性。 如果發現比例集中的實例與[應用程式運行狀況擴展](./virtual-machine-scale-sets-health-extension.md)或[負載均衡器運行狀況探測器](../load-balancer/load-balancer-custom-probe-overview.md)報告的那樣不正常,則此功能通過刪除不正常的實例並創建新實例來替換它,自動執行實例修復。

## <a name="requirements-for-using-automatic-instance-repairs"></a>使用自動實體修復的要求

**為規模集啟用應用程式執行狀況監視**

規模集應啟用實例的應用程式運行狀況監視。 這可以使用[應用程式運行狀況擴展](./virtual-machine-scale-sets-health-extension.md)或[負載均衡器運行狀況探測器](../load-balancer/load-balancer-custom-probe-overview.md)來完成。 一次只能啟用其中一個。 應用程式運行狀況擴展或負載均衡器探測在虛擬機器實例上配置的應用程式終結點以確定應用程式運行狀況狀態。 此運行狀況由規模集協調器用於監視實例運行狀況並在需要時執行修復。

**設定終結點以提供執行狀況**

在啟用自動實例修復策略之前,請確保縮放集實例已配置應用程式終結點以發出應用程式運行狀況狀態。 當實例在此應用程式終結點上返回狀態 200 (OK) 時,該實例將標記為"正常"。 在所有其他情況下,實例標記為「不正常」,包括以下方案:

- 當虛擬機器實體中沒有設定應用程式終結點以提供應用程式執行狀況時
- 當應用程式的終結點設定不正確時
- 無法存取應用程式的終結點時

對於標記為「不正常」的實例,自動修復由比例集觸發。 在啟用自動修復策略之前,請確保應用程式終結點配置正確,以避免在配置終結點時意外進行實例修復。

**開啟單一個放置群組**

此功能當前僅適用於作為單個放置組部署的規模集。 屬性*單放置組*應設置為*true,* 以便將縮放設定為使用自動實例修復功能。 瞭解有關[放置組](./virtual-machine-scale-sets-placement-groups.md#placement-groups)的更多。

**API 版本**

計算 API 版本 2018-10-01 或更高版本支援自動修復策略。

**資源或訂閱移動的限制**

啟用自動修復功能時,縮放集當前不支援資源或訂閱移動。

**對服務結構規模集的限制**

服務結構縮放集當前不支援此功能。

## <a name="how-do-automatic-instance-repairs-work"></a>自動實例修復如何工作?

自動實例修復功能依賴於對規模集中的各個實例的運行狀況監視。 縮放集中的 VM 實體可設定為使用[應用程式執行狀況擴展](./virtual-machine-scale-sets-health-extension.md)或[負載均衡器執行狀況偵測器](../load-balancer/load-balancer-custom-probe-overview.md)發出應用程式執行狀況。 如果發現實例不正常,則縮放集通過刪除不正常的實例並創建新實例來替換它來執行修復操作。 最新的虛擬機規模集模型用於創建新實例。 此功能可以使用*自動修復策略*物件在虛擬機縮放集模型中啟用。

### <a name="batching"></a>批次處理

自動實例修復操作分批執行。 在任意給定時間,通過自動修復策略修復比例集中的實例不超過 5%。 這有助於避免同時刪除和重新創建大量實例(如果同時發現不正常)。

### <a name="grace-period"></a>寬限期

當實例由於在規模集上執行的 PUT、PATCH 或 POST 操作(例如重新映射、重新部署、更新等)而通過狀態更改操作時,則該實例上的任何修復操作僅在等待寬限期後執行。 寬限期是允許實例返回到正常狀態的時間量。 寬限期在狀態更改完成後開始。 這有助於避免任何過早或意外的維修操作。 對於比例集中中任何新創建的實例(包括由於修復操作而創建的實例),寬限期都適用。 寬限期以 ISO 8601 格式以分鐘為單位指定,可以使用屬性 *「自動修復策略.gracePeriod」* 進行設置。 寬限期的範圍為 30 分鐘到 90 分鐘,預設值為 30 分鐘。

### <a name="suspension-of-repairs"></a>暫停維修 

虛擬機器規模集提供了在需要時臨時掛起自動實例修復的功能。 虛擬機器規模集實例中屬性*業務流程服務*下的自動修復*服務服務狀態*顯示自動修復的當前狀態。 當選擇縮放集進行自動修復時,參數*服務狀態*的值設置為 *「正在運行*」。。 當縮放集的自動修復掛起時,參數*服務狀態*設定為*掛起*。 如果在比例集中定義了*自動修復策略*,但未啟用自動修復功能,則參數*服務狀態*設置為 *「不運行*」。。

如果新創建的用於替換規模集中的不正常實例即使在重複執行維修操作後仍不正常,則作為安全措施,平臺將*服務狀態*更新為"*掛起*"。 通過將自動修復*的服務狀態*值設置為 *「正在運行*」,可以再次恢復自動修復。 有關[查看和更新秤集自動維修策略的服務狀態](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy)的部分提供了詳細說明。 

自動實體修復過程的工作方式如下:

1. [應用程式執行狀況擴展](./virtual-machine-scale-sets-health-extension.md)或[負載均衡器運行狀況探測](../load-balancer/load-balancer-custom-probe-overview.md)在規模集中的每個虛擬機內對應用程式終結點進行 ping,以取得每個實例的應用程式運行狀況狀態。
2. 如果終結點響應狀態為 200 (OK),則實例將標記為"正常"。 在所有其他情況下(包括終結點無法訪問),實例標記為"不正常"。
3. 當發現實例不正常時,縮放集通過刪除不正常的實例並創建新實例來替換它來觸發修復操作。
4. 實例修復分批執行。 在任何給定時間,將修復規模集中總實例的 5%。 如果比例集的實例少於 20 個實例,則一次為一個不正常的實例執行修復。
5. 上述過程將繼續,直到修復比例集中的所有不正常實例。

## <a name="instance-protection-and-automatic-repairs"></a>實體保護並自動修復

如果比例集中的實例通過應用其中一個[保護策略](./virtual-machine-scale-sets-instance-protection.md)受到保護,則不會對該實例執行自動修復。 這同時適用於保護策略:*防止擴展*和*防止縮放集*操作。 

## <a name="terminatenotificationandautomaticrepairs"></a>終止通知與自動修復

如果在規模集中啟用[了終止通知](./virtual-machine-scale-sets-terminate-notification.md)功能,則在自動修復操作期間,刪除不正常的實例將遵循終止通知配置。 取消通知透過 Azure 中繼資料服務傳送 ( 計畫的事件 ), 並且實體刪除在設定的延遲逾時期間延遲。 但是,創建新實例以替換不正常的實例不會等待延遲超時完成。

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>建立新比例集時開啟自動修復原則

您可以在建立新比例集時開啟自動修復政策,請確保滿足選擇加入此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 應正確配置應用程式終結點以用於縮放集實例,以避免在配置終結點時觸發意外修復。 對於新創建的縮放集,任何實例修復僅在等待寬限期後執行。 要在比例集中啟用自動實例修復,請使用虛擬機縮放集模型中的*自動修復策略*物件。

您還可以使用此[快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe)部署虛擬機規模集,其中啟用了負載均衡器運行狀況探測和自動實例修復,寬限期為 30 分鐘。

### <a name="azure-portal"></a>Azure 入口網站
 
在創建新比例集時,以下步驟啟用自動修復策略。
 
1. 跳到**虛擬機器縮放集**。
1. 選擇 **+添加**以創建新的比例集。
1. 跳到 **「運行狀況」** 選項卡。 
1. 找到 **「運行狀況**」 部分。
1. 啟用**監視器應用程式運行狀況**選項。
1. 找到 **「自動維修策略」** 部分。
1. **打開****「自動修復」** 選項。
1. 在**寬限期(分鐘)** 中,以分鐘指定寬限期,允許的值在 30 到 90 分鐘之間。 
1. 建立新比例集後,選擇 **「查看 + 創建**」按鈕。

### <a name="rest-api"></a>REST API

下面的範例展示如何在比例集模型中啟用自動實例修復。 使用 API 版本 2018-10-01 或更高版本。

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

可以使用[New-AzVmsConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet 創建新比例集時啟用自動實例修復功能。 此範例文稿使用設定檔案瀏覽比例集與資源建立:[建立完整的虛擬機器規模集](./scripts/powershell-sample-create-complete-scale-set.md)。 您可以透過參數*啟用"自動修復"* 和 *「自動修復寬限期」* 添加到用於創建比例集的設定物件來設定自動實例修復策略。 下面的示例啟用寬限期為 30 分鐘的功能。

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

下面的範例啟用自動修復策略,同時使用*[az vms 創建](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)* 創建新的縮放集。 首先創建資源組,然後創建一個新的規模集,自動修復策略寬限期設置為 30 分鐘。

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
  --automatic-repairs-period 30
```

上述示例使用現有的負載均衡器和運行狀況探測器來監視實例的應用程式運行狀況狀態。 如果您更喜歡使用應用程式運行狀況擴展進行監視,則可以創建比例集,配置應用程式運行狀況擴展,然後使用*az vmss 更新*啟用自動實例修復策略,如下一節所述。

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>更新現有規模集時開啟自動修復原則

在現有比例集中啟用自動修復策略之前,請確保滿足選擇加入此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 應正確配置應用程式終結點以用於縮放集實例,以避免在配置終結點時觸發意外修復。 要在比例集中啟用自動實例修復,請使用虛擬機縮放集模型中的*自動修復策略*物件。

更新現有比例集的模型后,請確保將最新模型應用於比例的所有實例。 請參閱有關如何使 VM 與[最新的比例集模型](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)更新的說明。

### <a name="azure-portal"></a>Azure 入口網站

您可以通過 Azure 門戶修改現有規模集的自動修復策略。 
 
1. 轉到現有虛擬機規模集。
1. 在左側功能表中的 **「設置」** 下,選擇 **「運行狀況」和「修復**」。
1. 啟用**監視器應用程式運行狀況**選項。
1. 找到 **「自動維修策略」** 部分。
1. **打開****「自動修復」** 選項。
1. 在**寬限期(分鐘)** 中,以分鐘指定寬限期,允許的值在 30 到 90 分鐘之間。 
1. 完成之後，請選取 [儲存]****。 

### <a name="rest-api"></a>REST API

下面的示例啟用寬限期為 40 分鐘的策略。 使用 API 版本 2018-10-01 或更高版本。

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

使用[Update-AzVms](/powershell/module/az.compute/update-azvmss) cmdlet 修改現有規模集中自動實例修復功能的配置。 下面的示例將寬限期更新為 40 分鐘。

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

下面是使用*[az vms 更新](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)* 更新更新現有規模集的自動實例修復策略的範例。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>檢視並更新自動實例修復政策的服務狀態

### <a name="rest-api"></a>REST API 

使用 API 版本 2019-12-01 或更高版本的虛擬機規模設置[獲取實例檢視](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview),以檢視*服務狀態*,以便根據屬性*業務流程服務*進行自動修復。 

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

在虛擬機規模集中使用 API 版本 2019-12-01 或更高版本的*SetOrchestraServiceState* API 來設置自動修復的狀態。 選擇縮放集進入自動修復功能后,可以使用此 API 暫停或恢復縮放集的自動修復。 

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

使用[獲取實例檢視](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view)cmdlet 查看*服務狀態*以進行自動實例修復。 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

使用[集式-編排-服務狀態](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state)cmdlet 更新*服務狀態*以進行自動實例修復。 選擇縮放集進入自動修復功能後,您可以使用此 cmdlet 暫停或恢復縮放集的自動修復。 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

使用帶有參數*實例檢視*[的 Get-AzVms](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) cmdlet 查看*服務狀態*以進行自動實例修復。

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

使用 Set-AzVmsOrchestraServiceServiceServiceScmdlet 更新*服務狀態*以進行自動實例修復。 選擇縮放集進入自動修復功能後,您可以使用此 cmdlet 暫停或恢復縮放集的自動修復。

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>疑難排解

**無法開啟自動修復原則**

如果收到「BadRequest」錯誤,其中顯示一條消息,指出「在類型」屬性「的物件上找不到成員」自動修復策略」」,請檢查用於虛擬機規模集的 API 版本。 此功能需要 API 版本 2018-10-01 或更高版本。

**即使啟用策略,實例也不會修復**

實例可能處於寬限期。 這是執行修復之前實例上任何狀態更改后等待的時間量。 這是為了避免任何過早或意外的維修。 實例的寬限期完成後,應執行修復操作。

**檢視縮放集實體的應用程式執行狀況**

您可以將[獲取實例檢視 API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)用於虛擬機器規模集中的實例以查看應用程式運行狀況狀態。 使用 Azure PowerShell,可以將 cmdlet [Get-AzVmsVM](/powershell/module/az.compute/get-azvmssvm)與 *-實例視圖*標誌一起使用。 應用程式運行狀況狀態在屬性*vmHealth*下提供。

在 Azure 門戶中,您還可以查看運行狀況狀態。 跳到現有比例集,從左側的功能表中選擇**實例**,然後查看"**運行狀況狀態**"列,瞭解每個比例集實例的運行狀況。 

## <a name="next-steps"></a>後續步驟

瞭解如何為規模集設定[應用程式執行狀況延伸](./virtual-machine-scale-sets-health-extension.md)或[負載平衡器執行狀況偵測](../load-balancer/load-balancer-custom-probe-overview.md)。
