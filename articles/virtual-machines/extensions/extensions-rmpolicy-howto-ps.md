---
title: 使用 Azure 原則來限制 VM 延伸模組安裝
description: 使用 Azure 原則來限制延伸模組部署。
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: b86429c90f436007116a45c6dbab443d6cc889e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188547"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>使用 Azure 原則來限制 Windows VM 上的延伸模組安裝

如果您想要防止在 Windows Vm 上使用或安裝特定擴充功能，您可以使用 PowerShell 來建立 Azure 原則定義，以限制資源群組內的 Vm 擴充功能。 

本教學課程會使用 Cloud Shell 內的 Azure PowerShell，這會不斷更新至最新版本。 

 

## <a name="create-a-rules-file"></a>建立規則檔

為了限制可安裝的延伸模組，您必須要有一個[規則](../../governance/policy/concepts/definition-structure.md#policy-rule)來提供識別延伸模組的邏輯。

以下範例說明如何在 Azure Cloud Shell 中建立規則檔來拒絕安裝 'Microsoft.Compute' 所發佈的延伸模組，但如果您是在本機 PowerShell 中運作，則也可以建立本機檔案，然後將路徑 ($home/clouddrive) 取代成您電腦上該本機檔案的路徑。

在 [Cloud Shell](https://shell.azure.com/powershell) 中，輸入：

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

將下列 .json 複製並貼到檔案中。

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

完成時，按 **Ctrl + O**，然後按 **Enter** 以儲存檔案。 按 **Ctrl + X** 以關閉檔案並結束。

## <a name="create-a-parameters-file"></a>建立參數檔案

您也需要一個[參數](../../governance/policy/concepts/definition-structure.md#parameters)檔，以建立供您用於傳遞要封鎖之延伸模組清單的結構。 

以下範例說明如何在 Cloud Shell 中為 VM 建立參數檔，但如果您是在本機 PowerShell 中運作，則也可以建立本機檔案，然後將路徑 ($home/clouddrive) 取代成您電腦上該本機檔案的路徑。

在 [Cloud Shell](https://shell.azure.com/powershell) 中，輸入：

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

將下列 .json 複製並貼到檔案中。

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

完成時，按 **Ctrl + O**，然後按 **Enter** 以儲存檔案。 按 **Ctrl + X** 以關閉檔案並結束。

## <a name="create-the-policy"></a>建立原則

原則定義是一個用來儲存您所要使用之設定的物件。 原則定義會使用規則檔和參數檔來定義原則。 請使用 [New-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition) Cmdlet 來建立原則定義。

 原則規則和參數是您在 Cloud Shell 中以 .json 檔案形式建立並儲存的檔案。


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>指派原則

以下範例會使用 [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment) 將原則指派給資源群組。 任何建立在 **myResourceGroup** 資源群組中的 VM 都將無法安裝「VM 存取代理程式」或「自訂指令碼」延伸模組。 

請使用 [Get-AzSubscription | Format-Table](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) Cmdlet 來取得您的訂用帳戶 ID，以用來取代範例中的 ID。

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>測試原則

若要測試原則，請嘗試使用「VM 存取」延伸模組。 下列情況應該會失敗，並出現「原則不允許資源 ' Set-azvmaccessextension： Resource ' Myvmaccess」訊息 '」訊息。

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

在入口網站中，密碼變更應該會失敗並出現「因為違反了原則，所以範本部署失敗。」 訊息。

## <a name="remove-the-assignment"></a>移除指派

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>移除原則

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱 [Azure 原則](../../governance/policy/overview.md)。
