---
title: 包含檔案
description: 包含檔案
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 069baf627c0230b6a4727c375494352ab3e6a803
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67173913"
---
若要將兩個標記新增至資源群組，請使用 [Set-AzResourceGroup](/powershell/module/az.resources/set-azresourcegroup) 命令：

```azurepowershell-interactive
Set-AzResourceGroup -Name myResourceGroup -Tag @{ Dept="IT"; Environment="Test" }
```

假設您想要新增第三個標記。 每當您將標記套用到資源或資源群組時，即會覆寫該資源或資源群組上現有的標記。 若要新增新的標記又不會遺失現有標記，您必須擷取現有標記、新增標記，然後重新套用標記的集合：

```azurepowershell-interactive
# Get existing tags and add a new tag
$tags = (Get-AzResourceGroup -Name myResourceGroup).Tags
$tags.Add("Project", "Documentation")

# Reapply the updated set of tags 
Set-AzResourceGroup -Tag $tags -Name myResourceGroup
```

資源不會從資源群組繼承標記。 目前，您的資源群組具有三個標記，但是資源並沒有任何標記。 若要將所有標記從資源群組套用至其資源，並在資源上保留所有非重複的現有標記，請使用下列指令碼︰

```azurepowershell-interactive
# Get the resource group
$group = Get-AzResourceGroup myResourceGroup

if ($group.Tags -ne $null) {
    # Get the resources in the resource group
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName

    # Loop through each resource
    foreach ($r in $resources)
    {
        # Get the tags for this resource
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        
        # If the resource has existing tags, add new ones
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }

            # Reapply the updated tags to the resource 
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

或者，您可以從資源群組將標記套用至資源，而不保留現有標記：

```azurepowershell-interactive
# Get the resource group
$g = Get-AzResourceGroup -Name myResourceGroup

# Find all the resources in the resource group, and for each resource apply the tags from the resource group
Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
```

若要在單一標記中合併數個值，請使用 JSON 字串。

```azurepowershell-interactive
Set-AzResourceGroup -Name myResourceGroup -Tag @{ CostCenter="{`"Dept`":`"IT`",`"Environment`":`"Test`"}" }
```

若要新增一個具有數個值的新標記又不會遺失現有標記，您必須擷取現有標記、對新標記使用 JSON 字串，然後重新套用標記的集合：

```azurepowershell-interactive
# Get existing tags and add a new tag
$ResourceGroup = Get-AzResourceGroup -Name myResourceGroup
$Tags = $ResourceGroup.Tags
$Tags.Add("CostCenter", "{`"Dept`":`"IT`",`"Environment`":`"Test`"}")

# Reapply the updated set of tags
$ResourceGroup | Set-AzResourceGroup -Tag $Tags
```

若要移除所有標記，您要傳遞空的雜湊表。

```azurepowershell-interactive
Set-AzResourceGroup -Name myResourceGroup -Tag @{ }
```
