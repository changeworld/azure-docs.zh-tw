---
title: 部署模式
description: 說明如何指定是否要透過 Azure Resource Manager 使用完整或累加部署模式。
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 45eee255cec06925095ed0696c669b5c205f8b56
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724403"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager 部署模式

部署您的資源時，您可以指定部署是累加式更新或完整更新。 這兩種模式之間的差異在於 Resource Manager 如何處理不在範本中的資源群組中的現有資源。

在這兩種模式中，Resource Manager 都會嘗試建立範本內指定的所有資源。 如果資源已存在於資源群組中，且其設定保持不變，則不會對該資源執行任何作業。 如果您變更資源的屬性值，則會以這些新值來更新資源。 如果您嘗試更新現有資源的位置或類型，部署會失敗並發生錯誤。 相反地，請以您需要的位置或類型部署新的資源。

預設模式為累加。

## <a name="complete-mode"></a>完整模式

在完整模式中，Resource Manager 會 **刪除** 現存於資源群組中但未在範本內指定的資源。

> [!NOTE]
> 在以完整模式部署範本之前，請一律使用「 [假設](template-deploy-what-if.md) 」作業。 假設會顯示要建立、刪除或修改哪些資源。 使用假設來避免不慎刪除資源。

如果您的範本包含因為 [條件](conditional-resource-deployment.md) 評估為 false 而未部署的資源，則結果取決於您用來部署範本的 REST API 版本。 如果您使用的版本早于2019-05-10，則 **不會刪除** 資源。 使用2019-05-10 或更新版本時， **會刪除** 資源。 Azure PowerShell 和 Azure CLI 的最新版本會刪除資源。

使用具有 [複製迴圈](copy-resources.md)的完整模式時請小心。 在解析複製迴圈之後，未在範本中指定的任何資源都會刪除。

如果您 [在範本中部署至多個資源群組](./deploy-to-resource-group.md)，部署作業中所指定資源群組中的資源就有資格刪除。 次要資源群組中的資源不會被刪除。

資源類型處理完整模式刪除的方式有一些差異。 當不在以完整模式部署的範本中時，將自動刪除父代資源。 當不在範本中時，不會自動刪除某些子系資源。 但是，如果刪除父資源，則會刪除這些子資源。

例如，如果您的資源群組包含 DNS 區域 (`Microsoft.Network/dnsZones` 資源類型) 和 cname 記錄 (`Microsoft.Network/dnsZones/CNAME` 資源類型) ，則 DNS 區域是 cname 記錄的父資源。 如果您使用完整模式部署，且範本中不包含 DNS 區域，則 DNS 區域和 CNAME 記錄都將同時刪除。 如果您在範本中包含 DNS 區域，但未包含 CNAME 記錄，則不會刪除 CNAME。

如需資源類型如何處理刪除的清單，請參閱[刪除完整模式部署的 Azure 資源](complete-mode-deletion.md)。

如果資源群組已 [鎖定](../management/lock-resources.md)，[完成] 模式不會刪除資源。

> [!NOTE]
> 只有根層級範本支援完整部署模式。 針對[連結或巢狀的範本](linked-templates.md)，您必須使用累加模式。
>
> [訂用帳戶層級部署](deploy-to-subscription.md) 不支援完整模式。
>
> 目前，入口網站不支援完成模式。
>

## <a name="incremental-mode"></a>累加模式

在累加模式中，Resource Manager 會讓現存於資源群組中但未在範本內指定的資源 **保持不變**。 範本中的資源 **會新增** 至資源群組。

> [!NOTE]
> 在累加模式中重新部署現有的資源時，會重新套用所有屬性。 這些 **屬性不會以累加方式新增**。 常見的誤解是將未在範本中指定的屬性保持不變。 如果您未指定某些屬性，Resource Manager 會將部署解讀為覆寫這些值。 範本中未包含的屬性會重設為預設值。 指定資源的所有非預設值，而不只是您要更新的值。 範本中的資源定義一律會包含資源的最終狀態。 它無法表示對現有資源的部分更新。
>
> 在罕見的情況下，您為資源指定的屬性實際上會實作為子資源。 例如，當您提供 web 應用程式的網站設定值時，這些值會在子資源類型中執行 `Microsoft.Web/sites/config` 。 如果您重新部署 web 應用程式，並為網站設定值指定空的物件，則不會更新子資源。 但是，如果您提供新的網站設定值，則會更新子資源類型。

## <a name="example-result"></a>範例結果

若要說明增量和完整模式之間的差異，請考慮下列案例。

**資源群組** 包含：

* 資源 A
* 資源 B
* 資源 C

**範本** 包含：

* 資源 A
* 資源 B
* 資源 D

部署於 **累加** 模式時，資源群組具有：

* 資源 A
* 資源 B
* 資源 C
* 資源 D

若部署在 **完整** 模式中，資源 C 會遭到刪除。 資源群組具有：

* 資源 A
* 資源 B
* 資源 D

## <a name="set-deployment-mode"></a>設定部署模式

若要在使用 PowerShell 部署時設定部署模式，使用 `Mode` 參數。

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

若要在使用 Azure CLI 部署時設定部署模式，使用 `mode` 參數。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

下列範例示範設定為累加部署模式的已連結範本：

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2017-05-10",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
          <nested-template-or-external-template>
    }
  }
]
```

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何建立 Resource Manager 範本，請參閱 [瞭解 ARM 範本的結構和語法](template-syntax.md)。
* 若要瞭解如何部署資源，請參閱 [使用 ARM 範本部署資源和 Azure PowerShell](deploy-powershell.md)。
* 若要檢視資源提供者的作業，請參閱 [Azure REST API](/rest/api/)。
