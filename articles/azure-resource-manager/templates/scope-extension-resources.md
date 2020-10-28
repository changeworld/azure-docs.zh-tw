---
title: 延伸模組資源類型的範圍
description: 描述如何在部署延伸模組資源類型時使用 scope 屬性。
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: bd468d31454c38bd314269243702d7df4f279a5e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681480"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>在 ARM 範本中設定延伸模組資源的範圍

延伸模組資源是修改其他資源的資源。 例如，您可以將角色指派給資源，以限制存取。 角色指派是延伸模組資源類型。

如需延伸模組資源類型的完整清單，請參閱 [擴充其他資源功能的資源類型](../management/extension-resource-types.md)。

本文說明如何在使用 Azure Resource Manager 範本 (ARM 範本) 部署延伸模組資源類型的範圍。 它描述套用至資源時，適用于擴充功能資源的範圍屬性。

## <a name="apply-at-deployment-scope"></a>套用於部署範圍

若要在目標部署範圍套用延伸模組資源類型，請將資源新增至您的範本，就像使用任何資源類型一樣。 可用的範圍包括 [資源群組](deploy-to-resource-group.md)、 [訂](deploy-to-subscription.md)用帳戶、 [管理群組](deploy-to-management-group.md)和 [租](deploy-to-tenant.md)使用者。 部署範圍必須支援資源類型。

下列範本會部署鎖定。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

當部署至資源群組時，會鎖定資源群組。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

下一個範例會指派角色。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

當部署至訂用帳戶時，它會將角色指派給訂用帳戶。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>適用于資源

若要將擴充功能資源套用至資源，請使用 `scope` 屬性。 將 [範圍] 屬性設定為您要新增延伸的資源名稱。 [範圍] 屬性是延伸模組資源類型的根屬性。

下列範例會建立儲存體帳戶，並將角色套用至該帳戶。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>後續步驟

* 若要了解如何在您的範本中定義參數，請參閱[了解 Azure Resource Manager 範本的結構和語法](template-syntax.md)。
* 如需解決常見部署錯誤的秘訣，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](common-deployment-errors.md)。
* 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](secure-template-with-sas-token.md)。
