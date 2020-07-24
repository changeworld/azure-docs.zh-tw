---
title: 範本規格總覽
description: 描述如何建立範本規格，並與組織中的其他使用者共用。
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 47dcf44b35ad5c0b77dd0b88d683071a7f2f4ecb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096143"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Azure Resource Manager 範本規格（預覽）

範本規格是一種新的資源類型，可用於將 Azure Resource Manager 範本（ARM 範本）儲存在 Azure 中，供日後部署之用。 此資源類型可讓您與組織中的其他使用者共用 ARM 範本。 就像任何其他 Azure 資源一樣，您可以使用角色型存取控制（RBAC）來共用範本規格。使用者只需要範本規格的 [讀取] 許可權就能部署其範本，因此您可以共用範本，而不允許其他人加以修改。

**TemplateSpecs**是範本規格的新資源類型。 其中包含主要範本和任何數目的連結範本。 Azure 會安全地將範本規格儲存在資源群組中。 範本規格支援[版本](#versioning)設定。

若要部署範本規格，您可以使用標準的 Azure 工具，例如 PowerShell、Azure CLI、Azure 入口網站、REST，以及其他支援的 Sdk 和用戶端。 您可以使用相同的命令，並傳入範本的相同參數。

使用範本規格的優點是您組織中的小組不需要重新建立或複製常見案例的範本。 您會建立標準範本並加以共用。 您在範本規格中包含的範本應由組織中的系統管理員驗證，以遵循組織的需求和指導方針。

> [!NOTE]
> 範本規格目前為預覽狀態。 若要使用它，您必須[註冊等待清單](https://aka.ms/templateSpecOnboarding)。

## <a name="create-template-spec"></a>建立範本規格

下列範例顯示在 Azure 中建立儲存體帳戶的簡單範本。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

當您建立範本規格時，會將 PowerShell 或 CLI 命令傳遞至主要範本檔案。 如果主要範本參考連結的範本，這些命令將會尋找並封裝它們，以建立範本規格。若要深入瞭解，請參閱[使用連結的範本建立範本規格](#create-a-template-spec-with-linked-templates)。

使用下列方法建立範本規格：

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -TemplateJsonFile ./mainTemplate.json
```

您可以使用下列方式，在訂用帳戶中查看所有範本規格：

```azurepowershell
Get-AzTemplateSpec
```

您可以透過下列方式來查看範本規格的詳細資料，包括其版本：

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

## <a name="deploy-template-spec"></a>部署範本規格

建立範本規格之後，具有範本規格之**讀取**許可權的使用者可以部署它。 如需授與存取權的詳細資訊，請參閱[教學課程：使用 Azure PowerShell 將 Azure 資源的存取權授與群組](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)。

範本規格可以透過入口網站、PowerShell、Azure CLI 或較大範本部署中的連結範本來部署。 組織中的使用者可以將範本規格部署到 Azure 中的任何範圍（資源群組、訂用帳戶、管理群組或租使用者）。

您可以藉由提供資源識別碼來部署範本規格，而不是傳入範本的路徑或 URI。 資源識別碼的格式如下：

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

請注意，資源識別碼包含範本規格的版本號碼。

例如，您可以使用下列 PowerShell 命令來部署範本規格。

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

在實務上，您通常會執行 `Get-AzTemplateSpec` 以取得您想要部署之範本規格的識別碼。

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

## <a name="create-a-template-spec-with-linked-templates"></a>使用連結的範本建立範本規格

如果範本規格的主要範本參考連結的範本，則 PowerShell 和 CLI 命令可以自動從您的本機磁片磁碟機尋找並封裝連結的範本。 您不需要手動設定儲存體帳戶或存放庫來裝載範本規格，所有專案都會獨立于範本規格資源中。

下列範例包含具有兩個連結範本的主要範本。 此範例只是範本的摘錄。 請注意，它會使用名 `relativePath` 為的屬性來連結至其他範本。 您必須使用 `apiVersion` `2020-06-01` 或更新版本來部署資源。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/webapp.json"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/database.json"
                }
            }
        }
    ],
    "outputs": {}
}

```

針對上述範例執行用來建立範本規格的 PowerShell 或 CLI 命令時，此命令會尋找三個檔案-主要範本、web 應用程式範本（ `webapp.json` ）和資料庫範本（ `database.json` ），並將它們封裝到範本規格中。

如需詳細資訊，請參閱[教學課程：使用連結的範本建立範本規格](template-specs-create-linked.md)。

## <a name="deploy-template-spec-as-a-linked-template"></a>將範本規格部署為連結的範本

建立範本規格之後，就可以輕鬆地從 ARM 範本或另一個範本規格重複使用它。您可以將其資源識別碼新增至範本，以連結至範本規格。 當您部署主要範本時，會自動部署連結的範本規格。 此行為可讓您開發模組化範本規格，並視需要加以重複使用。

例如，您可以建立部署網路資源的範本規格，以及另一個部署存放裝置資源的範本規格。 在 ARM 範本中，每當您需要設定網路功能或存放裝置資源時，您都可以連結到這兩個範本規格。

下列範例類似于先前的範例，但您可以使用 `id` 屬性連結至範本規格，而不是 `relativePath` 要連結至本機範本的屬性。 使用 `2020-06-01` 作為部署資源的 API 版本。 在範例中，範本規格位於名為**templateSpecsRG**的資源群組中。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "networkingDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "storageDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
                }
            }
        }
    ],
    "outputs": {}
}
```

如需連結範本規格的詳細資訊，請參閱[教學課程：將範本規格部署為連結的範本](template-specs-deploy-linked-template.md)。

## <a name="versioning"></a>版本控制

當您建立範本規格時，您會提供它的版本號碼。 當您逐一查看範本程式碼時，您可以更新現有的版本（適用于修補程式）或發行新版本。 版本是文字字串。 您可以選擇遵循任何版本設定系統，包括語義版本設定。 範本規格的使用者可以在部署時提供他們想要使用的版本號碼。

## <a name="next-steps"></a>後續步驟

* 若要建立及部署範本規格，請參閱[快速入門：建立及部署範本規格](quickstart-create-template-specs.md)。

* 如需範本規格中連結範本的詳細資訊，請參閱[教學課程：使用連結的範本建立範本規格](template-specs-create-linked.md)。

* 如需將範本規格部署為連結範本的詳細資訊，請參閱[教學課程：將範本規格部署為連結的範本](template-specs-deploy-linked-template.md)。
