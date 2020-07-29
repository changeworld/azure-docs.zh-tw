---
title: 標記邏輯組織的資源、資源群組和訂用帳戶
description: 示範如何套用標籤以針對計費及管理來組織 Azure 資源。
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: 08612831007eeba781a473ca704d92a52ab0a638
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337529"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>使用標記來組織您的 Azure 資源和管理階層

您會將標籤套用至您的 Azure 資源、資源群組和訂用帳戶，以邏輯方式將它們組織成分類法。 每個標記都是由一個名稱和一個值配對組成。 例如，您可以將「環境」名稱和「生產」值套用至生產環境中的所有資源。

如需如何執行標記策略的建議，請參閱[資源命名和標記決策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)。

> [!IMPORTANT]
> 作業的標記名稱不區分大小寫。 具有標記名稱的標記（不論大小寫為何）都會更新或抓取。 不過，資源提供者可能會保留您為標記名稱提供的大小寫。 您會在成本報告中看到大小寫。
> 
> 標記值會區分大小寫。

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>必要的存取權

若要將標籤套用至資源，您必須具有**Microsoft .resources/tags**資源類型的寫入權限。 「[標記參與者](../../role-based-access-control/built-in-roles.md#tag-contributor)」角色可讓您將標記套用至實體，而不需要存取實體本身。 標記參與者角色目前無法透過入口網站將標記套用至資源或資源群組。 它可以透過入口網站將標記套用至訂用帳戶。 它可透過 PowerShell 和 REST API 來支援所有標記作業。  

「[參與者](../../role-based-access-control/built-in-roles.md#contributor)」角色也會授與必要的存取權，將標記套用至任何實體。 若只要將標記套用到一個資源類型，則使用適用於該資源的參與者角色。 例如，若要將標記套用到虛擬機器，可使用[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)。

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>套用標記

Azure PowerShell 提供兩個命令來套用標記- [AzTag](/powershell/module/az.resources/new-aztag)和[AzTag](/powershell/module/az.resources/update-aztag)。 您必須具有 Az .Resources 模組1.12.0 或更新版本。 您可以使用來檢查您的版本 `Get-Module Az.Resources` 。 您可以安裝該模組，或[安裝 Azure PowerShell](/powershell/azure/install-az-ps)的3.6.1 或更新版本。

**AzTag**會取代資源、資源群組或訂用帳戶上的所有標記。 呼叫命令時，傳入您想要標記之實體的資源識別碼。

下列範例會將一組標記套用至儲存體帳戶：

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

當命令完成時，請注意資源有兩個標記。

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

如果您再次執行此命令，但這次使用不同的標記，請注意先前的標記會被移除。

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

若要將標記新增至已有標記的資源，請使用**AzTag**。 將 **-Operation**參數設定為**Merge**。

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

請注意，兩個新的標記已新增至兩個現有的標記。

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

每個標記名稱只能有一個值。 如果您提供新的標記值，即使您使用 merge 作業，還是會取代舊的值。 下列範例會將狀態標記從 [一般] 變更為綠色。

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

當您將 **-Operation**參數設定為**Replace**時，現有的標記會取代為新的標記集。

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

只有新的標記會保留在資源上。

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

相同的命令也適用于資源群組或訂用帳戶。 您會傳入要標記之資源群組或訂用帳戶的識別碼。

若要將一組新的標記新增至資源群組，請使用：

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

若要更新資源群組的標記，請使用：

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

若要將一組新的標記新增至訂用帳戶，請使用：

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

若要更新訂用帳戶的標記，請使用：

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

在資源群組中，您可能會有一個以上的資源具有相同的名稱。 在此情況下，您可以使用下列命令來設定每個資源：

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>列出標籤

若要取得資源、資源群組或訂用帳戶的標記，請使用[AzTag](/powershell/module/az.resources/get-aztag)命令，並傳入實體的資源識別碼。

若要查看資源的標記，請使用：

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

若要查看資源群組的標記，請使用：

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

若要查看訂用帳戶的標記，請使用：

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>依標記列出

若要取得具有特定標籤名稱和值的資源，請使用：

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

若要取得具有特定標籤名稱且具有任何標記值的資源，請使用：

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

若要取得具有特定標籤名稱和值的資源群組，請使用：

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>移除標記

若要移除特定標記，請使用**AzTag** ，並將 **-Operation**設定為**Delete**。 傳入您想要刪除的標記。

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

已移除指定的標記。

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

若要移除所有標記，請使用[AzTag](/powershell/module/az.resources/remove-aztag)命令。

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>套用標記

將標籤新增至資源群組或資源時，您可以覆寫現有的標籤，或將新的標記附加至現有的標記。

若要覆寫資源上的標記，請使用：

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

若要將標記附加至資源上的現有標記，請使用：

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

若要覆寫資源群組上的現有標記，請使用：

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

若要將標記附加至資源群組上的現有標記，請使用：

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Azure CLI 目前沒有將標記套用至訂閱的命令。 不過，您可以使用 CLI 來部署將標籤套用至訂用帳戶的 ARM 範本。 請參閱[將標記套用至資源群組或](#apply-tags-to-resource-groups-or-subscriptions)訂用帳戶。

### <a name="list-tags"></a>列出標籤

若要查看資源的現有標記，請使用：

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

若要查看資源群組的現有標籤，請使用：

```azurecli-interactive
az group show -n examplegroup --query tags
```

該指令碼會傳回下列格式︰

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

### <a name="list-by-tag"></a>依標記列出

若要取得「具有特定標籤和值的所有資源」，請使用 `az resource list`：

```azurecli-interactive
az resource list --tag Dept=Finance
```

若要取得「具有特定標籤的資源群組」，請使用 `az group list`：

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>處理空間

如果您的標籤名稱或值包含空格，則必須採取幾個額外的步驟。 下列範例會在標記可能包含空格時，將所有標記從資源群組套用至其資源。

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>範本

您可以使用 Resource Manager 範本，在部署期間標記資源、資源群組和訂用帳戶。

### <a name="apply-values"></a>套用值

下列範例會部署具有三個標記的儲存體帳戶。 其中兩個標記（ `Dept` 和 `Environment` ）會設定為常值。 一個標記（ `LastDeployed` ）會設定為預設為目前日期的參數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-an-object"></a>套用物件

您可定義存放數個標籤的物件參數，並將該物件套用至標籤元素。 這種方法比上一個範例提供更大的彈性，因為物件可以有不同的屬性。 物件中的每個屬性會變成資源的個別標籤。 下列範例具有名為 `tagValues` 且套用至標籤元素的參數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string"></a>套用 JSON 字串

若要將多個值儲存於單一標籤，請套用代表這些值的 JSON 字串。 整個 JSON 字串會儲存成一個不能超過256個字元的標記。 下列範例具有名為 `CostCenter` 的單一標籤，其中包含 JSON 字串中的數個值︰  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>從資源群組套用標記

若要將標記從資源群組套用至資源，請使用[resourceGroup （）](../templates/template-functions-resource.md#resourcegroup)函數。 取得標記值時，請使用 `tags[tag-name]` 語法，而不是 `tags.tag-name` 語法，因為在點標記法中無法正確剖析某些字元。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>將標記套用至資源群組或訂用帳戶

您可以藉由部署**Microsoft .resources/tags**資源類型，將標記新增至資源群組或訂用帳戶。 標記會套用至部署的目標資源群組或訂用帳戶。 每次部署範本時，您會取代先前套用的任何標記。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

若要將標籤套用至資源群組，請使用 PowerShell 或 Azure CLI。 部署至您想要標記的資源群組。

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

若要將標籤套用至訂用帳戶，請使用 PowerShell 或 Azure CLI。 部署至您想要標記的訂用帳戶。

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

如需訂用帳戶部署的詳細資訊，請參閱在訂用帳戶[層級建立資源群組和資源](../templates/deploy-to-subscription.md)。

下列範本會從物件將標籤新增至資源群組或訂用帳戶。

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>入口網站

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

若要透過 Azure REST API 處理標記，請使用：

* [標記-在範圍（PUT 作業）建立或更新](/rest/api/resources/tags/createorupdateatscope)
* [標記-在範圍更新](/rest/api/resources/tags/updateatscope)（修補作業）
* [標記-取得範圍](/rest/api/resources/tags/getatscope)（取得作業）
* [標記-在範圍刪除](/rest/api/resources/tags/deleteatscope)（刪除作業）

## <a name="inherit-tags"></a>繼承標記

資源不會繼承套用至資源群組或訂用帳戶的標記。 若要將訂用帳戶或資源群組中的標記套用至資源，請參閱[Azure 原則-標記](tag-policies.md)。

## <a name="tags-and-billing"></a>標記與計費

您可以使用標籤將您的計費資料分組。 例如，如果您針對不同組織執行多個 VM，請使用標籤依成本中心將使用量分組。 您也可以使用標籤來根據執行階段環境將成本分類，例如在生產環境中執行之 VM 的計費使用量。

您可以透過[Azure 資源使用量和費率卡片 api](../../cost-management-billing/manage/usage-rate-card-overview.md)或使用方式的逗號分隔值（CSV）檔案，來抓取標記的相關資訊。 您可以從 [Azure 帳戶中心](https://account.azure.com/Subscriptions)或 Azure 入口網站下載使用量檔案。 如需詳細資訊，請參閱[下載或檢視您的 Azure 帳單發票和每日使用量資料](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)。 從「Azure 帳戶中心」下載使用量檔案時，請選取 [版本 2]****。 針對支援在計費方面使用標籤的服務，標籤會顯示在 [標籤]**** 資料行中。

若為 REST API 作業，請參閱 [Azure 計費 REST API 參考](/rest/api/billing/)。

## <a name="limitations"></a>限制

標籤具有下列限制：

* 並非所有資源類型都支援標記。 若要判斷您是否可以將標記套用至資源類型，請參閱 [Azure 資源的標記支援](tag-support.md)。
* 每個資源、資源群組和訂用帳戶最多可以有50個標記名稱/值配對。 如果您需要套用的標記超過允許的最大數目，請使用標記值的 JSON 字串。 JSON 字串可以包含許多套用至單一標記名稱的值。 資源群組或訂用帳戶可以包含多個具有50標記名稱/值組的資源。
* 標記名稱上限為 512 個字元，且標記值上限為 256 字元。 儲存體帳戶的標記名稱上限為 128 個字元，且標記值上限為 256 個字元。
* 標記無法套用到類似雲服務的傳統資源。
* 標記名稱不得包含這些字元：`<`、`>`、`%`、`&`、`\`、`?`、`/`

   > [!NOTE]
   > 目前，Azure DNS 區域和流量管理員服務也不允許在標記中使用空格。
   >
   > Azure Front 門板不支援 `#` 在標記名稱中使用。
   >
   > Azure 自動化和 Azure CDN 僅支援在資源上有15個標記。

## <a name="next-steps"></a>後續步驟

* 並非所有資源類型都支援標記。 若要判斷您是否可以將標記套用至資源類型，請參閱 [Azure 資源的標記支援](tag-support.md)。
* 如需如何執行標記策略的建議，請參閱[資源命名和標記決策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)。
