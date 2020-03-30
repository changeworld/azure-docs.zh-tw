---
title: Azure 虛擬機器縮放集實例的實例保護
description: 瞭解如何保護 Azure 虛擬機器縮放集實例免受擴展和縮放集操作的監視。
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254114"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Azure 虛擬機器縮放集實例的實例保護

Azure 虛擬機器擴展集通過[自動縮放](virtual-machine-scale-sets-autoscale-overview.md)為工作負荷提供更好的彈性，因此您可以配置基礎結構何時擴展以及何時擴展。 縮放集還使您能夠通過不同的[升級策略](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)設置集中管理、配置和更新大量 VM。 您可以在比例集模型上配置更新，如果將升級策略設置為"自動"或"滾動"，則新配置將自動應用於每個比例集實例。

當應用程式處理流量時，在某些情況下，您可能希望將特定實例與規模集實例的其餘部分區別對待。 例如，規模集中的某些實例可能執行長時間運行的操作，並且不希望在操作完成之前將這些實例放大。 在比例集中，您可能還有一些專用實例來執行與比例集的其他成員執行其他或不同的任務。 您需要這些"特殊"VM 不得與比例集中的其他實例一起修改。 實例保護提供了其他控制項，用於為應用程式啟用這些方案和其他方案。

本文介紹如何應用和使用具有縮放集實例的不同實例保護功能。

## <a name="types-of-instance-protection"></a>實例保護的類型
縮放集提供兩種類型的實例保護功能：

-   **防止放大**
    - 通過縮放集實例上的**保護FromScaleIn**屬性啟用
    - 保護實例免受自動縮放啟動的放大
    - **不會阻止**使用者啟動的實例操作（包括實例刪除）
    - **未阻止**在規模集上啟動的操作（升級、重新映射、取消分配等）

-   **防止縮放集操作**
    - 通過規模集實例上的**保護FromScaleSetActions**屬性啟用
    - 保護實例免受自動縮放啟動的放大
    - 保護實例免受在規模集上啟動的操作（如升級、重新映射、取消分配等）
    - **不會阻止**使用者啟動的實例操作（包括實例刪除）
    - **未阻止**刪除完整比例集

## <a name="protect-from-scale-in"></a>防止放大
實例保護可以在創建實例後應用於縮放集實例。 保護僅在[實例模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)上應用和修改，而不是在[比例設置模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)上應用和修改。

在規模設置實例上應用擴展保護的方法有多種，如下例中詳細說明。

### <a name="azure-portal"></a>Azure 入口網站

可以通過 Azure 門戶將擴展保護應用於比例集中的實例。 不能一次調整多個實例。 對要保護的每個實例重複這些步驟。
 
1. 轉到現有虛擬機器規模集。
1. 從左側的功能表中選擇"**設置**"下的**實例**。
1. 選擇要保護的實例的名稱。
1. 選擇 **"保護原則"** 選項卡。
1. 在 **"保護原則"** 邊欄選項卡中，選擇"**防止放大"** 選項。
1. 選取 [儲存]****。 

### <a name="rest-api"></a>REST API

下面的示例將縮放保護應用於比例集中的實例。

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>僅支援 API 版本 2019-03-01 及以上實例保護

### <a name="azure-powershell"></a>Azure PowerShell

使用[Update-AzVmsVM](/powershell/module/az.compute/update-azvmssvm) Cmdlet 將擴展保護應用於規模集實例。

下面的示例將縮放保護應用於具有實例 ID 0 的比例集中的實例。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用[az vms 更新](/cli/azure/vmss#az-vmss-update)將擴展保護應用於規模集實例。

下面的示例將縮放保護應用於具有實例 ID 0 的比例集中的實例。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>防止縮放集操作
實例保護可以在創建實例後應用於縮放集實例。 保護僅在[實例模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)上應用和修改，而不是在[比例設置模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)上應用和修改。

保護實例免受縮放集操作的保護還可以保護實例免受自動縮放啟動的放大。

在規模設置實例上應用比例集操作保護的方法有多種，如下例中詳細說明。

### <a name="azure-portal"></a>Azure 入口網站

可以通過 Azure 門戶將比例集操作的保護應用於比例集中的實例。 不能一次調整多個實例。 對要保護的每個實例重複這些步驟。
 
1. 轉到現有虛擬機器規模集。
1. 從左側的功能表中選擇"**設置**"下的**實例**。
1. 選擇要保護的實例的名稱。
1. 選擇 **"保護原則"** 選項卡。
1. 在 **"保護原則"** 邊欄選項卡中，選擇"**從規模設置操作中保護"** 選項。
1. 選取 [儲存]****。 

### <a name="rest-api"></a>REST API

下面的示例將比例集操作的保護應用於比例集中的實例。

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>僅支援 API 版本 2019-03-01 及以上版本的實例保護。</br>
保護實例免受縮放集操作的保護還可以保護實例免受自動縮放啟動的放大。 不能指定"保護從範圍"：在設置"保護從範圍設置操作"時為 false：true

### <a name="azure-powershell"></a>Azure PowerShell

使用[Update-AzVmsVM](/powershell/module/az.compute/update-azvmssvm) Cmdlet 將規模集操作的保護應用於規模集實例。

下面的示例將比例集操作的保護應用於具有實例 ID 0 的比例集中的實例。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用[az vms 更新](/cli/azure/vmss#az-vmss-update)將比例集操作的保護應用於規模集實例。

下面的示例將比例集操作的保護應用於具有實例 ID 0 的比例集中的實例。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>疑難排解
### <a name="no-protectionpolicy-on-scale-set-model"></a>規模設置模型上無保護原則
實例保護僅適用于比例集實例，不適用於比例集模型。

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>縮放集實例模型上無保護原則
預設情況下，保護原則在創建實例時不會應用於實例。

創建實例後，可以將實例保護應用於縮放集實例。

### <a name="not-able-to-apply-instance-protection"></a>無法應用實例保護
僅支援 API 版本 2019-03-01 及以上版本的實例保護。 檢查正在使用的 API 版本並根據需要進行更新。 您可能還需要將 PowerShell 或 CLI 更新到最新版本。

## <a name="next-steps"></a>後續步驟
了解如何在虛擬機器擴展集上[部署您的應用程式](virtual-machine-scale-sets-deploy-app.md)。
