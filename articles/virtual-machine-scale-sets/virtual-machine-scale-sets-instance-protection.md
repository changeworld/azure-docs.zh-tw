---
title: Azure 虛擬機器擴展集實例的實例保護
description: 瞭解如何保護 Azure 虛擬機器擴展集實例，不受相應縮小和擴展集作業的限制。
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392919"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Azure 虛擬機器擴展集實例的實例保護

Azure 虛擬機器擴展集可讓您透過[自動](virtual-machine-scale-sets-autoscale-overview.md)調整，為您的工作負載提供更好的彈性，讓您可以設定當基礎結構相應放大和縮小規模時。 擴展集也可讓您透過不同的[升級原則](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)設定，集中管理、設定和更新大量的 vm。 如果您已將升級原則設定為 [自動] 或 [輪流]，則您可以在擴展集模型上設定更新，並自動將新設定套用到每個擴展集實例。

當您的應用程式處理流量時，您可能會想要讓特定實例的處理方式不同于擴展集實例的其餘部分。 例如，擴展集中的某些實例可能會執行長時間執行的作業，而且您不想要相應縮小這些實例，直到作業完成為止。 您也可以在擴展集中特製化一些實例，以執行其他工作或不同于擴展集成員的工作。 您要求這些「特殊」 Vm 不會與擴展集內的其他實例一起修改。 實例保護會提供其他控制項，以啟用應用程式的這些和其他案例。

本文說明如何將不同的實例保護功能與擴展集實例搭配使用。

## <a name="types-of-instance-protection"></a>實例保護的類型
擴展集提供兩種類型的實例保護功能：

-   **防止相應縮小**
    - 已透過擴展集實例上的**protectFromScaleIn**屬性啟用
    - 保護實例不受自動調整起始的相應縮小
    - 使用者起始的實例作業（包括實例刪除）**不會遭到封鎖**
    - 在擴展集上起始的作業（升級、重新安裝映射、解除配置等等）**不會遭到封鎖**

-   **防止擴展集動作**
    - 已透過擴展集實例上的**protectFromScaleSetActions**屬性啟用
    - 保護實例不受自動調整起始的相應縮小
    - 保護實例免于在擴展集上起始的作業（例如升級、重新安裝映射、解除配置等等）
    - 使用者起始的實例作業（包括實例刪除）**不會遭到封鎖**
    - **未封鎖**完整擴展集的刪除

## <a name="protect-from-scale-in"></a>防止相應縮小
實例保護可以在建立實例之後，套用至擴展集實例。 只會對[實例模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)套用保護，而不會在[擴展集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)上進行修改。

有多種方式可在您的擴展集實例上套用相應縮小保護，如下列範例所述。

### <a name="azure-portal"></a>Azure 入口網站

您可以透過 Azure 入口網站將相應縮小保護套用至擴展集中的實例。 您不能一次調整一個以上的實例。 針對您要保護的每個實例重複這些步驟。
 
1. 移至現有的虛擬機器擴展集。
1. 從左側功能表的 [**設定**] 底下選取 [**實例**]。
1. 選取您想要保護之實例的名稱。
1. 選取 [**保護原則**] 索引標籤。
1. 在 [**保護原則**] 分頁中，選取 [**從相應縮小保護**] 選項。
1. 選取 [儲存]。 

### <a name="rest-api"></a>REST API

下列範例會將相應縮小保護套用至擴展集中的實例。

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
>只有 API 版本2019-03-01 和更新版本才支援實例保護

### <a name="azure-powershell"></a>Azure PowerShell

使用[get-azvmssvm 指令程式](/powershell/module/az.compute/update-azvmssvm)，將相應縮小保護套用至您的擴展集實例。

下列範例會將相應縮小保護套用至具有實例識別碼0的擴展集內的實例。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用[az vmss update](/cli/azure/vmss#az-vmss-update)將相應縮小保護套用至您的擴展集實例。

下列範例會將相應縮小保護套用至具有實例識別碼0的擴展集內的實例。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>防止擴展集動作
實例保護可以在建立實例之後，套用至擴展集實例。 只會對[實例模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)套用保護，而不會在[擴展集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)上進行修改。

從擴展集動作保護實例也會保護實例不受自動調整起始的相應縮小。

有多種方式可在您的擴展集實例上套用擴展集動作保護，如下列範例所述。

### <a name="azure-portal"></a>Azure 入口網站

您可以透過 Azure 入口網站，將保護從擴展集動作套用至擴展集中的實例。 您不能一次調整一個以上的實例。 針對您要保護的每個實例重複這些步驟。
 
1. 移至現有的虛擬機器擴展集。
1. 從左側功能表的 [**設定**] 底下選取 [**實例**]。
1. 選取您想要保護之實例的名稱。
1. 選取 [**保護原則**] 索引標籤。
1. 在 [**保護原則**] 分頁中，選取 [**從擴展集動作保護**] 選項。
1. 選取 [儲存]。 

### <a name="rest-api"></a>REST API

下列範例會將保護從擴展集動作套用至擴展集中的實例。

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
>只有 API 版本2019-03-01 和更新版本才支援實例保護。</br>
從擴展集動作保護實例也會保護實例不受自動調整起始的相應縮小。 您不能指定 "protectFromScaleIn"： false，設定 "protectFromScaleSetActions"： true

### <a name="azure-powershell"></a>Azure PowerShell

使用[get-azvmssvm](/powershell/module/az.compute/update-azvmssvm) Cmdlet，將保護從擴展集動作套用至擴展集實例。

下列範例會將保護從擴展集動作套用至具有實例識別碼0的擴展集內的實例。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

使用[az vmss update](/cli/azure/vmss#az-vmss-update)將保護從擴展集動作套用至擴展集實例。

下列範例會將保護從擴展集動作套用至具有實例識別碼0的擴展集內的實例。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>疑難排解
### <a name="no-protectionpolicy-on-scale-set-model"></a>擴展集模型上沒有任何 protectionPolicy
實例保護僅適用于擴展集實例，而不適用於擴展集模型。

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>擴展集實例模型上沒有任何 protectionPolicy
根據預設，保護原則在建立時不會套用至實例。

建立實例之後，您可以將實例保護套用至擴展集實例。

### <a name="not-able-to-apply-instance-protection"></a>無法套用實例保護
只有 API 版本2019-03-01 和更新版本才支援實例保護。 檢查所使用的 API 版本，並視需要進行更新。 您可能也需要將您的 PowerShell 或 CLI 更新為最新版本。

## <a name="next-steps"></a>後續步驟
了解如何在虛擬機器擴展集上[部署您的應用程式](virtual-machine-scale-sets-deploy-app.md)。
