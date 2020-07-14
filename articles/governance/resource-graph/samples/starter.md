---
title: 入門查詢範例
description: 使用 Azure Resource Graph 來執行某些起始查詢，包括計算資源、排序資源或依特定標記。
ms.date: 07/06/2020
ms.topic: sample
ms.openlocfilehash: 7359c9677ff9d5670ed6fc44bae3170653dc7a11
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958763"
---
# <a name="starter-resource-graph-query-samples"></a>入門 Resource Graph 查詢範例

透過 Azure Resource Graph 了解查詢的第一個步驟是對[查詢語言](../concepts/query-language.md)進行基本的認識。 如果您還不熟悉 [Kusto Query Language (KQL)](/azure/kusto/query/index)，建議檢閱 [KQL 的教學課程](/azure/kusto/query/tutorial)，以了解如何撰寫所需資源的要求。

我們將逐步解說下列入門查詢︰

- [計算的 Azure 資源計數](#count-resources)
- [計算金鑰保存庫資源](#count-keyvaults)
- [列出依名稱排序的資源](#list-resources)
- [依名稱遞減順序顯示所有虛擬機器](#show-vms)
- [依名稱顯示前五個虛擬機器及其作業系統類型](#show-sorted)
- [依作業系統類型計算的虛擬機器計數](#count-os)
- [顯示包含儲存體的資源](#show-storage)
- [列出所有公用 IP 位址](#list-publicip)
- [計算具有按訂用帳戶設定之 IP 位址的資源計數](#count-resources-by-ip)
- [列出具有特定標籤值的資源](#list-tag)
- [列出具有特定標籤值的所有儲存體帳戶](#list-specific-tag)
- [顯示虛擬機器資源的別名](#show-aliases)
- [顯示特定別名的相異值](#distinct-alias-values)
- [顯示沒有關聯的網路安全性群組](#unassociated-nsgs)
- [從 Azure Advisor 取得節省成本摘要](#advisor-savings)

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

## <a name="language-support"></a>語言支援

Azure CLI (透過擴充功能) 與 Azure PowerShell (透過模組) 支援 Azure Resource Graph。 在執行下列任何查詢之前，請檢查您的環境已準備就緒。 請參閱 [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) 與 [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module)，以了解安裝及驗證所選殼層環境的步驟。

## <a name="count-azure-resources"></a><a name="count-resources"></a>計算的 Azure 資源計數

此查詢會傳回您具有存取權之訂用帳戶中存在的 Azure 資源的數目。 這也是很好的查詢，可驗證您選擇的殼層是否已安裝適當的 Azure Resource Graph 元件，並處於正常運作狀態。

```kusto
Resources
| summarize count()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="count-key-vault-resources"></a><a name="count-keyvaults"></a>計算金鑰保存庫資源

此查詢會使用 `count` 而不是 `summarize` 來計算所傳回的記錄數目。 只有金鑰保存庫會包含在計數中。

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="list-resources-sorted-by-name"></a><a name="list-resources"></a>列出依名稱排序的資源

此查詢會傳回任何類型的資源，但僅限於 **name**、**type** 和 **location** 屬性。 它會使用 `order by`，以遞增 (`asc`) 順序依 **name** 屬性將屬性排序。

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"></a>依名稱遞減順序顯示所有虛擬機器

若只要列出虛擬機器 (類型為 `Microsoft.Compute/virtualMachines`)，我們可以在結果中比對 **type** 屬性。 與上述查詢類似，`desc` 會將 `order by` 變更為遞減。 類型比對中的 `=~` 會告知 Resource Graph 不區分大小寫。

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"></a>依名稱顯示前五個虛擬機器及其作業系統類型

此查詢會使用 `top`，僅擷取按名稱排序的五個相符記錄。 Azure 資源類型為 `Microsoft.Compute/virtualMachines`。 `project` 會告訴 Azure Resource Graph 要包含哪些屬性。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="count-virtual-machines-by-os-type"></a><a name="count-os"></a>依作業系統類型計算的虛擬機器計數

在上一個查詢的基礎上，我們仍會依 `Microsoft.Compute/virtualMachines` 類型的 Azure 資源限制，但不再限制傳回的記錄數目。
相反地，我們使用 `summarize` 和 `count()` 來定義如何依屬性分組和彙總值，在此範例中為 `properties.storageProfile.osDisk.osType`。 如需此字串在完整物件中顯示方式的範例，請參閱[探索資源 - 虛擬機器探索](../concepts/explore-resources.md#virtual-machine-discovery)。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

撰寫相同查詢的另一種方式是 `extend` 屬性，並為其提供臨時的名稱，以便用於查詢，在此情況下為 **os**。 **os** 接著會由 `summarize` 與 `count()` 使用，如同上述範例所述。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

> [!NOTE]
> 請注意，雖然 `=~` 允許不區分大小寫的比對，但在查詢中使用屬性 (例如 **properties.storageProfile.osDisk.osType**) 要求大小寫正確無誤。 如果屬性的大小寫錯誤，傳回的值會是 Null 或不正確，且群組或摘要會不正確。

## <a name="show-resources-that-contain-storage"></a><a name="show-storage"></a>顯示包含儲存體的資源

此範例查詢並非明確定義要比對的類型，而是尋找 `contains` 字組 **storage** 的任何 Azure 資源。

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="list-all-public-ip-addresses"></a><a name="list-publicip"></a>列出所有公用 IP 位址

與上述查詢類似，尋找類型具有字組 **publicIPAddresses** 的所有項目。
此查詢將該模式擴展為僅包含 **properties.ipAddress**
`isnotempty` 的結果、只傳回 **properties.ipAddress**，以及將結果 `limit` 在前
100. 您可能需要逸出的引號，根據您所選擇的殼層而定。

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"></a>計算具有按訂用帳戶設定之 IP 位址的資源計數

使用上一個範例查詢，並新增 `summarize` 和 `count()`，我們可以取得具有已設定 IP 位址之資源的訂用帳戶清單。

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="list-resources-with-a-specific-tag-value"></a><a name="list-tag"></a>列出具有特定標籤值的資源

我們可以根據 Azure 資源類型以外的屬性限制結果，例如標籤。 在此範例中，我們會篩選標籤名稱為 **Environment** 的 Azure 資源進行篩選，其值為 **Internal**。

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

若也要提供資源具有哪些標籤和其值，請將 **tags** 屬性新增至 `project` 關鍵字。

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"></a>列出具有特定標籤值的所有儲存體帳戶

結合前一個範例的篩選功能，依 **type** 屬性篩選 Azure 資源類型。 此查詢也會將搜尋限制為具有特定標籤名稱和值的特定類型 Azure 資源。

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

> [!NOTE]
> 此範例會使用 `==` 進行比對，而非使用 `=~` 條件。 `==` 是區分大小寫的比對。

## <a name="show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases"></a>顯示虛擬機器資源的別名

「Azure 原則」會使用[Azure 原則別名](../../policy/concepts/definition-structure.md#aliases)來管理資源合規性。 Azure Resource Graph 可以傳回資源類型的「別名」。 建立自訂原則定義時，可以使用這些值來比較目前的別名值。 在查詢的結果中，預設並不會提供「別名」陣列。 請使用 `project aliases` 來將其明確新增到結果中。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20limit%201%0D%0A%7C%20project%20aliases" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20limit%201%0D%0A%7C%20project%20aliases" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20limit%201%0D%0A%7C%20project%20aliases" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="show-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values"></a>顯示特定別名的相異值

查看單一資源上的別名值相當有幫助，但它不會顯示使用 Azure Resource Graph 來查詢所有訂用帳戶時的實際值。 此範例會查看某個特定別名的所有值，並傳回相異值。

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20alias%20%3D%20aliases%5B%27Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType%27%5D%0D%0A%7C%20distinct%20tostring%28alias%29" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20alias%20%3D%20aliases%5B%27Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType%27%5D%0D%0A%7C%20distinct%20tostring%28alias%29" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20alias%20%3D%20aliases%5B%27Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType%27%5D%0D%0A%7C%20distinct%20tostring%28alias%29" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="show-unassociated-network-security-groups"></a><a name="unassociated-nsgs"></a>顯示沒有關聯的網路安全性群組

此查詢會傳回並未與網路介面或子網相關聯的網路安全性群組 (NSG)。

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="get-cost-savings-summary-from-azure-advisor"></a><a name="advisor-savings"></a>從 Azure Advisor 取得節省成本摘要

此查詢會摘要說明每個 [Azure Advisor](../../../advisor/advisor-overview.md) 建議中省下的成本。

```kusto
advisorresources
| where type == 'microsoft.advisor/recommendations'
| where properties.category == 'Cost'
| extend
    resources = tostring(properties.resourceMetadata.resourceId),
    savings = todouble(properties.extendedProperties.savingsAmount),
    solution = tostring(properties.shortDescription.solution),
    currency = tostring(properties.extendedProperties.savingsCurrency)
| summarize
    dcount(resources), 
    bin(sum(savings), 0.01)
    by solution, currency
| project solution, dcount_resources, sum_savings, currency
| order by sum_savings desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png":::在 Azure Resource Graph 總管中試用此查詢：

- Azure 入口網站：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure Government 入口網站：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure China 21Vianet 入口網站：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="next-steps"></a>後續步驟

- 深入了解[查詢語言](../concepts/query-language.md)。
- 深入了解如何[探索資源](../concepts/explore-resources.md)。
- 請參閱[進階查詢](advanced.md)的範例。