---
title: 跨範圍部署資源
description: 顯示如何在部署期間將目標設為多個範圍。 範圍可以是租使用者、管理群組、訂用帳戶和資源群組。
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 6161401ac039551a814b595715f56df1ac62dd6c
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374510"
---
# <a name="deploy-azure-resources-across-scopes"></a>跨範圍部署 Azure 資源

使用 Azure Resource Manager 範本（ARM 範本），您可以在單一部署中部署至一個以上的範圍。 可用的範圍包括租使用者、管理群組、訂用帳戶和資源群組。 例如，您可以將資源部署到一個資源群組，然後在相同的範本中，將資源部署至另一個資源群組。 或者，您也可以將資源部署至管理群組，並將資源部署到該管理群組內的資源群組。

您可以使用[嵌套或連結的範本](linked-templates.md)來指定與部署作業的主要範圍不同的範圍。

## <a name="available-scopes"></a>可用的範圍

您用於部署作業的範圍會決定可用的其他範圍。 您可以部署至[租](deploy-to-tenant.md)使用者、[管理群組](deploy-to-management-group.md)、[訂](deploy-to-subscription.md)用帳戶或[資源群組](deploy-powershell.md)。 從主要部署層級中，您無法在階層中往上移層級。 例如，如果您部署至訂用帳戶，則無法逐步執行將資源部署至管理群組的層級。 不過，您可以部署至管理群組和步驟降低層級，以部署至訂用帳戶或資源群組。

針對每個範圍，部署範本的使用者必須具有建立資源所需的許可權。

## <a name="cross-resource-groups"></a>跨資源群組

若要以不同於父代範本的資源群組為目標，請使用[巢狀或連結的範本](linked-templates.md)。 在部署資源類型中，指定將成為巢狀範本部署目標的「訂用帳戶識別碼」和「資源群組」的值。 資源群組可以存在於不同的訂用帳戶中。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

如未指定訂用帳戶識別碼或資源群組，則會使用父代範本中的訂用帳戶及資源群組。 執行部署之前，所有資源群組都必須存在。

> [!NOTE]
> 您可以在單一部署中部署至 **800 個資源群組**。 一般而言，此限制表示您可以部署至一個指定用於父代範本的資源群組，並且可在巢狀或連結的部署中部署至最多 799 個資源群組。 不過，如果父代範本只包含巢狀或連結的範本，本身未部署任何資源，則您可以在巢狀或連結的部署中包含最多 800 個資源群組。

下列範例會部署兩個儲存體帳戶。 第一個儲存體帳戶會部署到部署作業中指定的資源群組。 第二個儲存體帳戶會部署到 `secondResourceGroup` 和 `secondSubscriptionID` 參數指定的資源群組：

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

如果您將 `resourceGroup` 設定為不存在的資源群組名稱，部署就會失敗。

若要測試上述範本並查看結果，請使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要將兩個儲存體帳戶部署至**相同訂用帳戶**中的兩個資源群組，請使用：

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

若要將兩個儲存體帳戶部署至**兩個訂用帳戶**，請使用：

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要將兩個儲存體帳戶部署至**相同訂用帳戶**中的兩個資源群組，請使用：

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

若要將兩個儲存體帳戶部署至**兩個訂用帳戶**，請使用：

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="cross-subscription-management-group-and-tenant"></a>跨訂用帳戶、管理群組和租使用者

為訂用帳戶、管理群組和租使用者層級部署指定不同的範圍時，您可以使用像是資源群組範例的嵌套部署。 您用來指定範圍的屬性可能不同。 這些案例涵蓋在部署層級的相關文章中。 如需詳細資訊，請參閱

* [在訂用帳戶層級建立資源群組和資源](deploy-to-subscription.md)
* [在管理群組層級建立資源](deploy-to-management-group.md)
* [在租用戶層級建立資源](deploy-to-tenant.md)

## <a name="how-functions-resolve-in-scopes"></a>函數在範圍中的解析方式

當您部署到一個以上的範圍時， [resourceGroup （）](template-functions-resource.md#resourcegroup)和[訂用帳戶（）](template-functions-resource.md#subscription)函式會根據您指定範本的方式，以不同方式進行解析。 當您連結至外部範本時，函式一律會解析為該範本的範圍。 當您將範本巢狀嵌入父代範本中時，請使用 `expressionEvaluationOptions` 屬性來指定函式要解析為父代範本或巢狀範本的資源群組和訂用帳戶。 屬性設定為 `inner`，會解析成巢狀範本的範圍。 屬性設定為 `outer`，會解析成父代範本的範圍。

下表顯示函式會解析成父代或內嵌的資源群組和訂用帳戶。

| 範本類型 | 影響範圍 | 解決方案 |
| ------------- | ----- | ---------- |
| 巢狀        | 外部 (預設值) | 父代資源群組 |
| 巢狀        | inner | 子資源群組 |
| 連結        | N/A   | 子資源群組 |

以下[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json)示範：

* 具有預設 (外部) 範圍的巢狀範本
* 具有內部範圍的巢狀範本
* 連結的範本

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

若要測試上述範本並查看結果，請使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

前述範例的輸出為：

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

前述範例的輸出為：

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---



## <a name="next-steps"></a>後續步驟

* 若要了解如何在您的範本中定義參數，請參閱[了解 Azure Resource Manager 範本的結構和語法](template-syntax.md)。
* 如需解決常見部署錯誤的秘訣，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](common-deployment-errors.md)。
* 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](secure-template-with-sas-token.md)。
