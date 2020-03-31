---
title: 使用 Azure 虛擬機器縮放集進行自動實例修復
description: 瞭解如何在規模集中為 VM 實例配置自動修復策略
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274810"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>預覽：Azure 虛擬機器縮放集的自動實例修復

為 Azure 虛擬機器擴展集啟用自動實例修復有助於通過維護一組正常實例實現應用程式的高可用性。 如果發現比例集中的實例與[應用程式運行狀況擴展](./virtual-machine-scale-sets-health-extension.md)或[負載等化器運行狀況探測器](../load-balancer/load-balancer-custom-probe-overview.md)報告的那樣不正常，則此功能通過刪除不正常的實例並創建新實例來替換它，自動執行實例修復。

> [!NOTE]
> 此預覽功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。

## <a name="requirements-for-using-automatic-instance-repairs"></a>使用自動實例修復的要求

**加入宣告自動實例修復預覽**

使用 REST API 或 Azure PowerShell 加入宣告自動實例修復預覽。 這些步驟將註冊預覽功能的訂閱。 請注意，這只是使用此功能所需的一次性設置。 如果您的訂閱已註冊用於自動實例修復預覽，則無需再次註冊。 

使用 REST API 

1. 使用功能註冊功能[- 註冊](/rest/api/resources/features/register) 

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

2. 等待幾分鐘，*讓國家*更改為*註冊*。 您可以使用以下 API 來確認這一點。

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

3. 一旦*國家*更改為*註冊*，然後運行以下內容。

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

使用 Azure PowerShell

1. 使用 Cmdlet[寄存器-AzureRm 資來源提供者](/powershell/module/azurerm.resources/register-azurermresourceprovider)註冊該功能，後跟[註冊 -AzureRm 提供程式功能](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. 等待幾分鐘，註冊*狀態*更改為*註冊*。 您可以使用以下 Cmdlet 來確認這一點。

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 答覆應如下。

| 功能名稱                           | ProviderName            | 註冊國       |
|---------------------------------------|-------------------------|-------------------------|
| 修復VMScale設置實例預覽      | Microsoft.Compute       | 已登錄              |

3. 一旦*註冊狀態*更改為*已註冊*，然後運行以下 Cmdlet。

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**為規模集啟用應用程式運行狀況監視**

規模集應啟用實例的應用程式運行狀況監視。 這可以使用[應用程式運行狀況擴展](./virtual-machine-scale-sets-health-extension.md)或[負載等化器運行狀況探測器](../load-balancer/load-balancer-custom-probe-overview.md)來完成。 一次只能啟用其中一個。 應用程式運行狀況擴展或負載等化器探測在虛擬機器實例上配置的應用程式終結點以確定應用程式運行狀況狀態。 此運行狀況由規模集協調器用於監視實例運行狀況並在需要時執行修復。

**配置終結點以提供運行狀況**

在啟用自動實例修復策略之前，請確保縮放集實例已配置應用程式終結點以發出應用程式運行狀況狀態。 當實例在此應用程式終結點上返回狀態 200 （OK） 時，該實例將標記為"正常"。 在所有其他情況下，實例標記為"不正常"，包括以下方案：

- 當虛擬機器實例中沒有配置應用程式終結點以提供應用程式運行狀況時
- 當應用程式終結點配置不正確時
- 無法訪問應用程式終結點時

對於標記為"不正常"的實例，自動修復由比例集觸發。 在啟用自動修復策略之前，請確保應用程式終結點配置正確，以避免在配置終結點時意外進行實例修復。

**啟用單個放置組**

此預覽當前僅適用于作為單個放置組部署的規模集。 屬性*單放置組*應設置為*true，* 以便將縮放設置為使用自動實例修復功能。 瞭解有關[放置組](./virtual-machine-scale-sets-placement-groups.md#placement-groups)的更多。

**API 版本**

計算 API 版本 2018-10-01 或更高版本支援自動修復策略。

**對資源或訂閱移動的限制**

作為此預覽的一部分，啟用自動修復策略時，縮放集當前不支援資源或訂閱移動。

**對服務結構規模集的限制**

服務結構縮放集當前不支援此預覽功能。

## <a name="how-do-automatic-instance-repairs-work"></a>自動實例修復如何工作？

自動實例修復功能依賴于對規模集中的各個實例的運行狀況監視。 縮放集中的 VM 實例可以配置為使用[應用程式運行狀況擴展](./virtual-machine-scale-sets-health-extension.md)或[負載等化器運行狀況探測器](../load-balancer/load-balancer-custom-probe-overview.md)發出應用程式運行狀況。 如果發現實例不正常，則縮放集通過刪除不正常的實例並創建新實例來替換它來執行修復操作。 此功能可以使用*自動修復策略*物件在虛擬機器縮放集模型中啟用。

### <a name="batching"></a>批次處理

自動實例修復操作分批執行。 在任意給定時間，通過自動修復策略修復比例集中的實例不超過 5%。 這有助於避免同時刪除和重新創建大量實例（如果同時發現不正常）。

### <a name="grace-period"></a>寬限期

當實例由於在規模集上執行的 PUT、PATCH 或 POST 操作（例如重新映射、重新部署、更新等）而通過狀態更改操作時，則該實例上的任何修復操作僅在等待寬限期後執行。 寬限期是允許實例返回到正常狀態的時間量。 寬限期在狀態更改完成後開始。 這有助於避免任何過早或意外的維修操作。 對於比例集中中任何新創建的實例（包括由於修復操作而創建的實例），寬限期都適用。 寬限期以 ISO 8601 格式以分鐘為單位指定，可以使用屬性 *"自動修復策略.gracePeriod"* 進行設置。 寬限期的範圍為 30 分鐘到 90 分鐘，預設值為 30 分鐘。

自動實例修復過程的工作方式如下：

1. [應用程式運行狀況擴展](./virtual-machine-scale-sets-health-extension.md)或[負載等化器運行狀況探測](../load-balancer/load-balancer-custom-probe-overview.md)在規模集中的每個虛擬機器內對應用程式終結點進行 ping，以獲取每個實例的應用程式運行狀況狀態。
2. 如果終結點回應狀態為 200 （OK），則實例將標記為"正常"。 在所有其他情況下（包括終結點無法訪問），實例標記為"不正常"。
3. 當發現實例不正常時，縮放集通過刪除不正常的實例並創建新實例來替換它來觸發修復操作。
4. 實例修復分批執行。 在任何給定時間，將修復規模集中總實例的 5%。 如果比例集的實例少於 20 個實例，則一次為一個不正常的實例執行修復。
5. 上述過程將繼續，直到修復比例集中的所有不正常實例。

## <a name="instance-protection-and-automatic-repairs"></a>實例保護和自動修復

如果比例集中的實例通過應用*["保護從比例集操作"保護原則](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* 進行保護，則不會對該實例執行自動修復。

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>創建新比例集時啟用自動修復策略

對於在創建新比例集時啟用自動修復策略，請確保滿足加入宣告此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 應正確配置應用程式終結點以用於縮放集實例，以避免在配置終結點時觸發意外修復。 對於新創建的縮放集，任何實例修復僅在等待寬限期後執行。 要在比例集中啟用自動實例修復，請使用虛擬機器縮放集模型中的*自動修復策略*物件。

### <a name="rest-api"></a>REST API

下面的示例演示如何在比例集模型中啟用自動實例修復。 使用 API 版本 2018-10-01 或更高版本。

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

可以使用[New-AzVmsConfig](/powershell/module/az.compute/new-azvmssconfig) Cmdlet 創建新比例集時啟用自動實例修復功能。 此示例腳本使用設定檔流覽比例集和相關資源的創建：[創建完整的虛擬機器規模集](./scripts/powershell-sample-create-complete-scale-set.md)。 您可以通過將參數*啟用"自動修復"* 和 *"自動修復寬限期"* 添加到用於創建比例集的設定物件來配置自動實例修復策略。 下面的示例啟用寬限期為 30 分鐘的功能。

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>更新現有規模集時啟用自動修復策略

在現有比例集中啟用自動修復策略之前，請確保滿足加入宣告此功能的所有[要求](#requirements-for-using-automatic-instance-repairs)。 應正確配置應用程式終結點以用於縮放集實例，以避免在配置終結點時觸發意外修復。 要在比例集中啟用自動實例修復，請使用虛擬機器縮放集模型中的*自動修復策略*物件。

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

使用[Update-AzVms](/powershell/module/az.compute/update-azvmss) Cmdlet 修改現有規模集中自動實例修復功能的配置。 下面的示例將寬限期更新為 40 分鐘。

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>疑難排解

**無法啟用自動修復策略**

如果收到"BadRequest"錯誤，其中顯示一條消息，指出"在類型'屬性'的物件上找不到成員'自動修復策略'"，請檢查用於虛擬機器規模集的 API 版本。 此功能需要 API 版本 2018-10-01 或更高版本。

**即使啟用策略，實例也不會修復**

實例可能處於寬限期。 這是執行修復之前實例上任何狀態更改後等待的時間量。 這是為了避免任何過早或意外的維修。 實例的寬限期完成後，應執行修復操作。

**查看縮放集實例的應用程式運行狀況**

您可以將[獲取實例視圖 API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)用於虛擬機器規模集中的實例以查看應用程式運行狀況狀態。 使用 Azure PowerShell，可以將 Cmdlet [Get-AzVmsVM](/powershell/module/az.compute/get-azvmssvm)與 *-實例視圖*標誌一起使用。 應用程式運行狀況狀態在屬性*vmHealth*下提供。

## <a name="next-steps"></a>後續步驟

瞭解如何為規模集配置[應用程式運行狀況擴展](./virtual-machine-scale-sets-health-extension.md)或[負載平衡器運行狀況探測](../load-balancer/load-balancer-custom-probe-overview.md)。
