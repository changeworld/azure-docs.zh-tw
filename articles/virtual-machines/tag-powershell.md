---
title: 如何使用 PowerShell 標記 VM
description: 瞭解如何使用 PowerShell 標記虛擬機器
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 1efb512923caed97126bdb4ee6267c6a9b57f251
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594921"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>如何使用 PowerShell 在 Azure 中標記虛擬機器

本文說明如何使用 PowerShell 在 Azure 中標記 VM。 標記是使用者定義的成對「索引鍵/值」，可直接置於資源或資源群組。 Azure 目前最多支援每個資源和資源群組50個標記。 標記可在建立或加入至現有資源時置於資源上。 如果您想要使用 Azure CLI 來標記虛擬機器，請參閱 [如何使用 Azure CLI 在 Azure 中標記虛擬機器](tag-cli.md)。


使用 `Get-AzVM` Cmdlet 來查看您 VM 目前的標籤清單。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

如果您的虛擬機器已包含標籤，則您將會看到清單格式的所有標記。

若要加入標記，請使用 `Set-AzResource` 命令。 透過 PowerShell 更新標記時，會以整體的方式更新標記。 如果您要將一個標記新增至已有標籤的資源，則必須包含您想要放在資源上的所有標記。 以下是如何透過 PowerShell Cmdlet 將其他標記新增至資源的範例。

`$tags`使用和屬性，將 VM 的所有目前標記指派給變數 `Get-AzResource` `Tags` 。

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

若要查看目前的標記，請輸入變數。

```azurepowershell-interactive
$tags
```

輸出可能如下所示：

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

在下列範例中，我們會加入 `Location` 以值呼叫的標記 `myLocation` 。 用 `+=` 來將新的索引鍵/值組附加至 `$tags` 清單。

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

用 `Set-AzResource` 來設定 VM 上 *$tags* 變數中定義的所有標記。

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

用 `Get-AzResource` 來顯示資源上的所有標記。

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

輸出應該看起來像下面這樣，現在會包含新的標記：

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```


**後續步驟**

- 如需深入了解如何標記您的 Azure 資源，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/management/overview.md)與[使用標記來組織您的 Azure 資源](../azure-resource-manager/management/tag-resources.md)。
- 如需查看標記如何協助您管理使用 Azure 資源，請參閱[了解 Azure 帳單](../cost-management-billing/understand/review-individual-bill.md)與[深入了解 Microsoft Azure 資源耗用量](../cost-management-billing/manage/usage-rate-card-overview.md)。
