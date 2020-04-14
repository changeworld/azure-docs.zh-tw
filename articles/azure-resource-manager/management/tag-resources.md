---
title: 為邏輯組織標記資源、資源組和訂閱
description: 示範如何套用標籤以針對計費及管理來組織 Azure 資源。
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 2f437682a2ac415ce8478b09a44bff044bd9511b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255119"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>使用標記組織 Azure 資源和管理層次結構

將標記應用於 Azure 資源、資源組和訂閱,以邏輯方式將它們組織到分類中。 每個標記都是由一個名稱和一個值配對組成。 例如，您可以將「環境」名稱和「生產」值套用至生產環境中的所有資源。

有關如何實現標記策略的建議,請參閱[資源命名和標記決策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)。

> [!IMPORTANT]
> 標記名稱不區分大小寫。 標記值區分大小寫。

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>必要的存取

要將標記應用於資源,您必須具有對**Microsoft.Resource/標記**資源類型的寫入存取許可權。 ["標記參與者"](../../role-based-access-control/built-in-roles.md#tag-contributor)角色允許您將標記應用於實體,而無需訪問實體本身。 目前,標記參與者角色無法通過門戶將標記應用於資源或資源組。 它可以通過門戶將標記應用於訂閱。 它透過 PowerShell 和 REST API 支援所有標記操作。  

["參與者"](../../role-based-access-control/built-in-roles.md#contributor)角色還授予向任何實體應用標記所需的訪問許可權。 若只要將標記套用到一個資源類型，則使用適用於該資源的參與者角色。 例如，若要將標記套用到虛擬機器，可使用[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)。

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>套用標記

Azure PowerShell 提供兩個用於應用程式標記的指令 -[新 AzTag](/powershell/module/az.resources/new-aztag)與[Update-AzTag](/powershell/module/az.resources/update-aztag)。 您必須具有 Az.Resources 模組 1.12.0 或更高版本。 您可以使用 檢查您的`Get-Module Az.Resources`版本 。 您可以安裝該模組或[安裝 Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 或更高版本。

**New-AzTag**替換資源、資源組或訂閱上的所有標記。 調用命令時,將要標記的實體的資源 ID 傳遞。

以下範例將一組標記應用於儲存帳戶:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

命令完成後,請注意資源有兩個標記。

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

如果再次運行該命令,但這次使用不同的標記,請注意,前面的標記將被刪除。

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

要將標記加入到已具有標記的資源,請使用**Update-Aztag**。 將 **-操作**參數設定為**合併**。

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

請注意,兩個新標記已添加到兩個現有標記中。

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

每個標記名稱只能有一個值。 如果為標記提供新值,則即使使用合併操作,也會替換舊值。 下面的示例將狀態標記從"正常"更改為綠色。

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

將 **-操作**參數設置為 **「替換**」時,現有標記將被新的標記集替換。

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

資源上僅保留新標記。

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

相同的命令也可用於資源組或訂閱。 您傳遞要標記的資源組或訂閱的標識碼。

要向資源群組新增新標記集,請使用:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

要更新資源群組的標記,請使用:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

要向訂閱新增新標記集,請使用:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

要更新訂閱的標記,請使用:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

資源組中可能有多個同名資源。 在這種情況下,可以使用以下命令設定每個資源:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>列出標籤

要取得資源、資源組或訂閱的標記,請使用[Get-AzTag](/powershell/module/az.resources/get-aztag)命令並傳遞實體的資源 ID。

要檢視資源的標記,請使用:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

要檢視資源群組的標記,請使用:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

要查看訂閱的標記,請使用:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>依標籤列出

要取得具有特定標記名稱和值的資源,請使用:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

要取得具有具有任何標記值的特定標記名稱的資源,請使用:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

要取得具有特定標記名稱和值的資源組,請使用:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>刪除標籤

要刪除特定標記,請使用**Update-AzTag**並將 **-操作**設定為 **"刪除**"。 傳遞要刪除的標記。

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

指定的標記將被刪除。

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

要刪除所有標記,請使用[「刪除-AzTag」](/powershell/module/az.resources/remove-aztag)命令。

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>套用標記

將標記添加到資源組或資源時,可以覆蓋現有標記或將新標記追加到現有標記。

要覆蓋資源的標記,請使用:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

要將標記追加到資源上的現有標記,請使用:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

要覆蓋資源群組中的現有標記,請使用:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

要將標記追加到資源組的現有標記,請使用:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

目前,Azure CLI 不支援將標記應用於訂閱。

### <a name="list-tags"></a>列出標籤

要檢視資源的現有標記,請使用:

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

### <a name="list-by-tag"></a>依標籤列出

若要取得「具有特定標籤和值的所有資源」，請使用 `az resource list`：

```azurecli-interactive
az resource list --tag Dept=Finance
```

若要取得「具有特定標籤的資源群組」，請使用 `az group list`：

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>處理空間

如果標記名稱或值包含空格,則必須執行幾個額外的步驟。 以下範例將資源組中的所有標記應用於其資源,當標記可能包含空格時。

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

您可以在部署期間使用資源管理器範本標記資源、資源組和訂閱。

### <a name="apply-values"></a>套用值

下面的範例部署具有三個標記的存儲帳戶。 兩個標記(`Dept``Environment`和 ) 設定為文字值。 一個標記`LastDeployed`( ) 設定為預設為當前日期的參數。

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

您可定義存放數個標籤的物件參數，並將該物件套用至標籤元素。 此方法提供了比前一個示例更大的靈活性,因為物件可以具有不同的屬性。 物件中的每個屬性會變成資源的個別標籤。 下列範例具有名為 `tagValues` 且套用至標籤元素的參數。

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

若要將多個值儲存於單一標籤，請套用代表這些值的 JSON 字串。 整個 JSON 字串儲存為一個不能超過 256 個字元的標記。 下列範例具有名為 `CostCenter` 的單一標籤，其中包含 JSON 字串中的數個值︰  

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

### <a name="apply-tags-from-resource-group"></a>套用資源群組中的標記

要將資源組中的標記應用於資源,請使用[資源組](../templates/template-functions-resource.md#resourcegroup)函數。 獲取標記值時,請使用`tags[tag-name]`語法而不是`tags.tag-name`語法,因為某些字元在點表示法中未正確解析。

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

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>將標記應用於資源群組或訂閱

您可以通過部署**Microsoft.Resources/標記**資源類型向資源組或訂閱添加標記。 標記將應用於部署的目標資源組或訂閱。 每次部署範本時,都會替換任何標記。這些標記以前都應用過。

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

要將標記應用於資源組,請使用 PowerShell 或 Azure CLI。 部署到要標記的資源組。

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

要將標記應用於訂閱,請使用 PowerShell 或 Azure CLI。 部署到要標記的訂閱。

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

以下範本將物件的標記添加到資源組或訂閱。

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

要透過 Azure REST API 處理標記,請使用:

* [標記 ─ 在範圍內建立或更新](/rest/api/resources/tags/createorupdateatscope)(PUT 操作)
* [標記 ─ 在範圍內更新](/rest/api/resources/tags/updateatscope)(PATCH 操作)
* [標記 ─ 在範圍上取得](/rest/api/resources/tags/getatscope)(GET 操作)
* [標記 ─ 在範圍內移除](/rest/api/resources/tags/deleteatscope)(刪除操作)

## <a name="inherit-tags"></a>繼承標記

應用於資源組或訂閱的標記不會由資源繼承。 要將訂閱或資源群組中的標記應用於資源,請參閱[Azure 策略 - 標記](tag-policies.md)。

## <a name="tags-and-billing"></a>標記與計費

您可以使用標籤將您的計費資料分組。 例如，如果您針對不同組織執行多個 VM，請使用標籤依成本中心將使用量分組。 您也可以使用標籤來根據執行階段環境將成本分類。例如，在生產環境中執行之 VM 的計費使用量。

您可以透過 [Azure 資源使用狀況和 RateCard API](../../billing/billing-usage-rate-card-overview.md) 或使用狀況逗號分隔值 (CSV) 檔案，擷取關於標記的資訊。 您可以從 [Azure 帳戶中心](https://account.azure.com/Subscriptions)或 Azure 入口網站下載使用量檔案。 如需詳細資訊，請參閱[下載或檢視您的 Azure 帳單發票和每日使用量資料](../../billing/billing-download-azure-invoice-daily-usage-date.md)。 從「Azure 帳戶中心」下載使用量檔案時，請選取 [版本 2]****。 針對支援在計費方面使用標籤的服務，標籤會顯示在 [標籤]**** 資料行中。

若為 REST API 作業，請參閱 [Azure 計費 REST API 參考](/rest/api/billing/)。

## <a name="limitations"></a>限制

標籤具有下列限制：

* 並非所有資源類型都支援標記。 若要判斷您是否可以將標記套用至資源類型，請參閱 [Azure 資源的標記支援](tag-support.md)。
* 管理組當前不支援標記。
* 每個資源、資源組和訂閱最多可以有 50 個標記名稱/值對。 如果需要應用的標記數大於最大允許數,請使用 JSON 字串作為標記值。 JSON 字串可以包含許多套用至單一標記名稱的值。 資源組或訂閱可以包含許多資源,每個資源具有 50 個標記名稱/值對。
* 標記名稱上限為 512 個字元，且標記值上限為 256 字元。 儲存體帳戶的標記名稱上限為 128 個字元，且標記值上限為 256 個字元。
* 通用 VM 不支援標記。
* 標記無法套用到類似雲服務的傳統資源。
* 標記名稱不得包含這些字元：`<`、`>`、`%`、`&`、`\`、`?`、`/`

   > [!NOTE]
   > 目前,Azure DNS 區域和流量管理器服務也不允許在標記中使用空格。

## <a name="next-steps"></a>後續步驟

* 並非所有資源類型都支援標記。 若要判斷您是否可以將標記套用至資源類型，請參閱 [Azure 資源的標記支援](tag-support.md)。
* 有關如何實現標記策略的建議,請參閱[資源命名和標記決策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)。
