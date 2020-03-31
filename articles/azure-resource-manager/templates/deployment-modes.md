---
title: 部署模式
description: 說明如何指定是否要透過 Azure Resource Manager 使用完整或累加部署模式。
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 1077d92f076797fb03c4fe750b353e2306f9b6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460240"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager 部署模式

部署您的資源時，您可以指定部署是累加式更新或完整更新。 這兩種模式之間的區別是資源管理器如何處理不在範本中的資源組中的現有資源。

在這兩種模式中，Resource Manager 都會嘗試建立範本內指定的所有資源。 如果資源已存在於資源群組中，且其設定保持不變，則不會對該資源執行任何作業。 如果您變更資源的屬性值，則會以這些新值來更新資源。 如果您嘗試更新現有資源的位置或類型，部署會失敗並發生錯誤。 相反地，請以您需要的位置或類型部署新的資源。

預設模式為累加。

## <a name="complete-mode"></a>完整模式

在完整模式中，Resource Manager 會**刪除**現存於資源群組中但未在範本內指定的資源。

如果範本包含由於[條件](conditional-resource-deployment.md)評估為 false 而未部署的資源，則結果取決於用於部署範本的 REST API 版本。 如果使用的版本早于 2019-05-10，**則不會刪除**資源。 使用 2019-05-10 或更高版本，**資源將被刪除**。 最新版本的 Azure PowerShell 和 Azure CLI 將刪除資源。

小心使用帶有[複製迴圈](copy-resources.md)的完整模式。 解析複製迴圈後範本中未指定的任何資源都將被刪除。

如果部署到[範本中的多個資源組](cross-resource-group-deployment.md)，則部署操作中指定的資源組中的資源才有資格被刪除。 不會刪除輔助資源組中的資源。

資源類型處理完整模式刪除的方式存在一些差異。 當不在以完整模式部署的範本中時，將自動刪除父代資源。 當不在範本中時，不會自動刪除某些子系資源。 但是，如果刪除父資源，這些子資源將被刪除。

例如，如果您的資源群組包含 DNS 區域 (Microsoft.Network/dnsZones 資源類型) 和 CNAME 記錄 (Microsoft.Network/dnsZones/CNAME 資源類型)，則 DNS 區域是 CNAME 記錄的父代資源。 如果您使用完整模式部署，且範本中不包含 DNS 區域，則 DNS 區域和 CNAME 記錄都將同時刪除。 如果在範本中包含 DNS 區域，但不包括 CNAME 記錄，則不會刪除 CNAME。

如需資源類型如何處理刪除的清單，請參閱[刪除完整模式部署的 Azure 資源](complete-mode-deletion.md)。

如果資源組[已鎖定](../management/lock-resources.md)，則完整模式不會刪除資源。

> [!NOTE]
> 只有根層級範本支援完整部署模式。 針對[連結或巢狀的範本](linked-templates.md)，您必須使用累加模式。
>
> [訂閱級部署](deploy-to-subscription.md)不支援完整模式。
>
> 目前，門戶不支援完整模式。
>

## <a name="incremental-mode"></a>累加模式

在累加模式中，Resource Manager 會讓現存於資源群組中但未在範本內指定的資源**保持不變**。 範本中的資源**將添加到**資源組中。

> [!NOTE]
> 在增量模式下重新部署現有資源時，將重新應用所有屬性。 **屬性不會增量添加**。 一個常見的誤解是認為範本中未指定的屬性保持不變。 如果不指定某些屬性，資源管理器會將部署解釋為覆蓋這些值。 範本中未包括的屬性將重置為預設值。 指定資源的所有非預設值，而不僅僅是要更新的值。 範本中的資源定義始終包含資源的最終狀態。 它無法表示對現有資源的部分更新。

## <a name="example-result"></a>範例結果

若要說明增量和完整模式之間的差異，請考慮下列案例。

**資源群組**包含：

* 資源 A
* 資源 B
* 資源 C

**範本**包含：

* 資源 A
* 資源 B
* 資源 D

部署於**累加**模式時，資源群組具有：

* 資源 A
* 資源 B
* 資源 C
* 資源 D

若部署在**完整**模式中，資源 C 會遭到刪除。 資源群組具有：

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

* 要瞭解如何創建資源管理器範本，請參閱[創作 Azure 資源管理器範本](template-syntax.md)。
* 若要了解如何部署資源，請參閱 [使用 Azure 資源管理員範本部署應用程式](deploy-powershell.md)。
* 若要檢視資源提供者的作業，請參閱 [Azure REST API](/rest/api/)。
