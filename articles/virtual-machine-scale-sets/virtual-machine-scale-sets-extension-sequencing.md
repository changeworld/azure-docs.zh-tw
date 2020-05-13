---
title: 使用擴充功能排序搭配 Azure 虛擬機器擴展集
description: 了解在虛擬機器擴展集上部署多個擴充功能時如何擴充功能佈建排序。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3271041b9f4db100cd05588129c7d714d4478f10
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121026"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>為虛擬機器擴展集中的擴充功能佈建排序
Azure 虛擬機器擴充功能可提供多種功能，例如部署後的設定和管理、監視、安全性等等。 生產環境部署通常會搭配使用多個為 VM 執行個體設定的擴充功能，以達成所需的結果。

在一個虛擬機器上使用多個擴充功能時，務必要確定需要相同 OS 資源的多個擴充功能不會同時嘗試取得這些資源。 有些擴充功能也會相依於其他擴充功能，以提供環境設定和祕密之類的必要組態。 若未設置正確的排序，相依擴充功能的部署可能會失敗。

本文詳細說明如何對要為虛擬機器擴展集中的 VM 執行個體設定的擴充功能進行排序。

## <a name="prerequisites"></a>先決條件
本文假設您已熟悉以下各項：
-   Azure 虛擬機器[擴充](../virtual-machines/extensions/overview.md)功能
-   [修改](virtual-machine-scale-sets-upgrade-scale-set.md)虛擬機器擴展集

## <a name="when-to-use-extension-sequencing"></a>何時應使用擴充功能排序
除非有明確指定，否則您不一定要對擴展集進行擴充功能排序，擴充功能可依照任何順序佈建在擴展集執行個體上。

例如，如果您的擴展集模型中指定了兩個擴充功能 (擴充功能 A 和擴充功能 B)，則可能的佈建順序會有以下兩種：
-   擴充功能 A -> 擴充功能 B
-   擴充功能 B -> 擴充功能 A

如果您的應用程式要求擴充功能 A 一律須在擴充功能 B 之前佈建，您即應使用本文說明的擴充功能排序。 使用擴充功能排序時，可能的順序就只有一種：
-   擴充功能 A -> 擴充功能 B

已定義的佈建順序中未指定的任何擴充功能，都可以隨時佈建，包括在定義的順序之前、之後或期間。 擴充功能排序只會指定某個特定的擴充功能將在另一個擴充功能之後佈建。 這並不會影響模型中定義的任何其他擴充功能的佈建。

例如，如果您的擴展集模型中指定了三個擴充功能 (擴充功能 A、擴充功能 B 和擴充功能 C)，且擴充功能 C 設定為要在擴充功能 A 之後佈建，則可能的佈建順序如下：
-   擴充功能 A -> 擴充功能 C -> 擴充功能 B
-   擴充功能 B -> 擴充功能 A -> 擴充功能 C
-   擴充功能 A -> 擴充功能 B -> 擴充功能 C

如果您必須確定在已定義的擴充功能順序執行期間不會佈建其他擴充功能，建議您為擴展集模型中的所有擴充功能排序。 在上述範例中，擴充功能 B 可以設定為在擴充功能 C 之後佈建，如此就只會有一種順序：
-   擴充功能 A -> 擴充功能 C -> 擴充功能 B


## <a name="how-to-use-extension-sequencing"></a>如何使用擴充功能排序
若要為擴充功能佈建排序，您必須更新擴展集模型中的擴充功能定義以納入屬性 "provisionAfterExtensions"，此屬性可接受擴充功能名稱陣列。 此屬性陣列值中所列的擴充功能必須完整定義於擴展集模型中。

### <a name="template-deployment"></a>範本部署
下列範例會定義擴展集有擴充功能 A、擴充功能 B 和擴充功能 C 這三個擴充功能的範本，使擴充功能以下列順序佈建：
-   擴充功能 A -> 擴充功能 B -> 擴充功能 C

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

由於 "provisionAfterExtensions" 屬性可接受擴充功能名稱陣列，因此您可以修改上述範例，使擴充功能 C 在擴充功能 A 和擴充功能 B 之後佈建，但擴充功能 A 和擴充功能 B 之間不需要一定的順序。 下列範本可用來因應此案例的需求：

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>REST API
下列範例會將名為「擴充功能 C」的新擴充功能新增至擴展集模型。 擴充功能 C 具有對擴充功能 A 和擴充功能 B 的相依性，而這兩個擴充功能已定義於擴展集模型中。

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

如果擴充功能 C 先前已定義於擴展集模型中，而您此時想要新增其相依性，您可以執行 `PATCH`，對已部署的擴充功能編輯屬性。

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
對現有擴展集執行個體所做的變更會在下一次[升級](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)時套用。

### <a name="azure-powershell"></a>Azure PowerShell
使用 [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) Cmdlet，將應用程式健康情況擴充功能新增至擴展集模型定義。 擴充功能排序需要使用 Az PowerShell 1.2.0 或更新版本。

下列範例會將[應用程式健康情況擴充功能](virtual-machine-scale-sets-health-extension.md)新增至 `extensionProfile` (位於 Windows 型擴展集的擴展集模型中)。 應用程式健康情況擴充功能會在佈建已定義於擴展集內的[自訂指令碼擴充功能](../virtual-machines/extensions/custom-script-windows.md)之後佈建。

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set)，可將應用程式健康狀態延伸模組新增至擴展集模型定義。 擴充功能排序需要使用 Azure CLI 2.0.55 或更新版本。

下列範例會將[應用程式健康情況擴充功能](virtual-machine-scale-sets-health-extension.md)新增至 Windows 型擴展集的擴展集模型中。 應用程式健康情況擴充功能會在佈建已定義於擴展集內的[自訂指令碼擴充功能](../virtual-machines/extensions/custom-script-windows.md)之後佈建。

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>疑難排解

### <a name="not-able-to-add-extension-with-dependencies"></a>無法新增具有相依性的擴充功能嗎？
1. 確定 provisionAfterExtensions 中指定的擴充功能已定義於擴展集模型中。
2. 確定未導入任何循環相依性。 例如，不允許下列順序：擴充功能 a-> 擴充功能 b-> 擴充功能 c-> 擴充功能 a
3. 確定所相依的任何擴充功能在擴充功能「屬性」下都有「設定」屬性。 例如，如果擴充功能 B 必須在擴充功能 A 之後佈建，則擴充功能 A 的「屬性」下必須要有「設定」欄位。 如果擴充功能未要求任何必要設定，則可以指定空的「設定」屬性。

### <a name="not-able-to-remove-extensions"></a>無法移除擴充功能嗎？
請確定要移除的擴充功能未列在任何其他擴充功能的 provisionAfterExtensions 下。

## <a name="next-steps"></a>後續步驟
了解如何在虛擬機器擴展集上[部署您的應用程式](virtual-machine-scale-sets-deploy-app.md)。
