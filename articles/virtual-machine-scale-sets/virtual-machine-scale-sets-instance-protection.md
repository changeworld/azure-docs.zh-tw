---
title: Azure 虛擬機器擴展集執行個體的執行個體保護
description: 了解如何保護 Azure 虛擬機器擴展集執行個體，使其不受相應縮小和擴展集作業的限制。
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 8c4944da8ffcaa75e6448483918a29809c32830b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124052"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Azure 虛擬機器擴展集執行個體的執行個體保護

Azure 虛擬機器擴展集可透過[自動調整](virtual-machine-scale-sets-autoscale-overview.md)來為工作負載提供較佳彈性，因此您可在基礎結構相應放大及縮小規模時進行設定。 擴展集也可供透過不同的[升級原則](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)設定來集中管理、設定及更新大量 VM。 如果已將升級原則設為自動或輪流，即可在擴展集模型上設定更新，且新設定會自動套用到每個擴展集執行個體。

當應用程式處理流量時，在某些情況下您可能會希望將特定執行個體與其他的擴展集執行個體分開處理。 例如，擴展集中某些執行個體可能正在執行長時間的作業，且您不希望在作業完成前相應縮小這些執行個體。 您也可能已在擴展集中特製化某些執行個體，以執行其他工作或與擴展集成員不同的工作。 您要求這些「特殊」VM 不與擴展集內的其他執行個體一起修改。 執行個體保護會提供其他控制項，以為應用程式提供這些案例和其他案例。

本文描述如何搭配擴展集執行個體來套用及使用不同的執行個體保護功能。

## <a name="types-of-instance-protection"></a>執行個體保護的類型
擴展集提供兩種類型的執行個體保護功能：

-   **防止相應縮小**
    - 透過擴展集執行個體上的 **protectFromScaleIn** 屬性啟用
    - 防止執行個體進行自動調整起始的相應縮小
    - 使用者起始的執行個體作業 (包括執行個體刪除) **不會受到封鎖**
    - 在擴展集上起始的作業 (升級、重新安裝映像、解除配置等) **不會受到封鎖**

-   **防止擴展集動作**
    - 透過擴展集執行個體上的 **protectFromScaleSetActions** 屬性啟用
    - 防止執行個體進行自動調整起始的相應縮小
    - 防止執行個體進行在擴展集上起始的作業 (升級、重新安裝映像、解除配置等)
    - 使用者起始的執行個體作業 (包括執行個體刪除) **不會受到封鎖**
    - 刪除完整擴展集**不會受到封鎖**

## <a name="protect-from-scale-in"></a>防止相應縮小
您可在建立執行個體後，將執行個體保護套用至擴展集執行個體。 您只能在[執行個體模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)上套用及修改保護，而無法在[擴展集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)上這麼做。

有多種方式可在擴展集執行個體上套用相應縮小保護，如下列範例所述。

### <a name="azure-portal"></a>Azure 入口網站

您可透過 Azure 入口網站將相應縮小保護套用至擴展集中的執行個體。 您無法一次調整超過一個執行個體。 針對想要保護的每個執行個體重複執行這些步驟。
 
1. 移至現有的虛擬機器擴展集。
1. 從左側功能表的 [設定] 下，選取 [執行個體]。
1. 選取想要保護的執行個體名稱。
1. 選取 [保護原則] 索引標籤。
1. 在 [保護原則] 刀鋒視窗中，選取 [防止相應縮小] 選項。
1. 選取 [儲存]。 

### <a name="rest-api"></a>REST API

下列範例會將相應縮小保護套用至擴展集中的執行個體。

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
>只有 2019-03-01 以上的 API 版本才支援執行個體保護

### <a name="azure-powershell"></a>Azure PowerShell

使用 [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) Cmdlet，將相應縮小保護套用至擴展集執行個體。

下列範例會將相應縮小保護套用至執行個體識別碼為 0 的擴展集中執行個體。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用 [az vmss update](/cli/azure/vmss#az-vmss-update) 將相應縮小保護套用至擴展集執行個體。

下列範例會將相應縮小保護套用至執行個體識別碼為 0 的擴展集中執行個體。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>防止擴展集動作
您可在建立執行個體後，將執行個體保護套用至擴展集執行個體。 您只能在[執行個體模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)上套用及修改保護，而無法在[擴展集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)上這麼做。

防止執行個體進行擴展集動作，也會防止執行個體進行自動調整起始的相應縮小。

有多種方式可在擴展集執行個體上套用擴展集動作保護，如下列範例所述。

### <a name="azure-portal"></a>Azure 入口網站

您可透過 Azure 入口網站，將擴展集動作保護套用至擴展集中的執行個體。 您無法一次調整超過一個執行個體。 針對想要保護的每個執行個體重複執行這些步驟。
 
1. 移至現有的虛擬機器擴展集。
1. 從左側功能表的 [設定] 下，選取 [執行個體]。
1. 選取想要保護的執行個體名稱。
1. 選取 [保護原則] 索引標籤。
1. 在 [保護原則] 刀鋒視窗中，選取 [防止擴展集動作] 選項。
1. 選取 [儲存]。 

### <a name="rest-api"></a>REST API

下列範例會將擴展集動作保護套用至擴展集中的執行個體。

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
>只有 2019-03-01 以上的 API 版本才支援執行個體保護。</br>
防止執行個體進行擴展集動作，也會防止執行個體進行自動調整起始的相應縮小。 當設定為 "protectFromScaleSetActions": true 時，即無法指定 "protectFromScaleIn": false

### <a name="azure-powershell"></a>Azure PowerShell

使用 [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) Cmdlet，將擴展集動作保護套用至擴展集執行個體。

下列範例會將擴展集動作護套用至執行個體識別碼為 0 的擴展集中執行個體。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用 [az vmss update](/cli/azure/vmss#az-vmss-update) 將擴展集動作保護套用至擴展集執行個體。

下列範例會將擴展集動作護套用至執行個體識別碼為 0 的擴展集中執行個體。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>疑難排解
### <a name="no-protectionpolicy-on-scale-set-model"></a>擴展集模型上不存在任何 protectionPolicy
執行個體保護僅適用於擴展集執行個體，不適用於擴展集模型。

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>執行個體模型上不存在任何 protectionPolicy
根據預設，保護原則在建立時不會套用至執行個體。

建立執行個體後，即可將執行個體保護套用至擴展集執行個體。

### <a name="not-able-to-apply-instance-protection"></a>無法套用執行個體保護
只有 2019-03-01 以上的 API 版本才支援執行個體保護。 檢查正在使用的 API 版本，並視需要進行更新。 您可能也需要將 PowerShell 或 CLI 更新為最新版本。

## <a name="next-steps"></a>後續步驟
了解如何在虛擬機器擴展集上[部署您的應用程式](virtual-machine-scale-sets-deploy-app.md)。
