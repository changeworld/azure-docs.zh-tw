---
title: 範本部署假設
description: 在部署 Azure Resource Manager 範本之前，請先判斷您的資源會發生哪些變更。
author: tfitzmac
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: tomfitz
ms.openlocfilehash: a1ce7f8f718b364dc4b47593cf9ea37e8baf1e72
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563087"
---
# <a name="arm-template-deployment-what-if-operation"></a>ARM 範本部署 what-if 作業 \(部分機器翻譯\)

在 (ARM 範本) 部署 Azure Resource Manager 範本之前，您可以預覽將會發生的變更。 Azure Resource Manager 提供「假設」作業，讓您在部署範本時查看資源的變更方式。 what-if 作業不會對現有的資源進行任何變更。 相反地，其會在部署指定的範本時預測變更。

您可以搭配 Azure PowerShell、Azure CLI 或 REST API 作業來使用「假設」作業。 資源群組、訂用帳戶、管理群組和租使用者層級部署的支援假設。

## <a name="install-azure-powershell-module"></a>安裝 Azure PowerShell 模組

若要在 PowerShell 中使用假設，您必須有 **Az 模組的4.2 版或更新** 版本。

但是，在安裝所需的模組之前，請確定您已 PowerShell Core (6.x 或 7.x) 。 如果您有 PowerShell 5.x 或更早版本，請 [更新您的 powershell 版本](/powershell/scripting/install/installing-powershell)。 您無法在 PowerShell 5.x 或更早版本上安裝所需的模組。

### <a name="install-latest-version"></a>安裝最新版本

若要安裝此模組，請使用：

```powershell
Install-Module -Name Az -Force
```

如需安裝模組的詳細資訊，請參閱 [安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="install-azure-cli-module"></a>安裝 Azure CLI 模組

若要在 Azure CLI 中使用 what-if，則必須具有 Azure CLI 2.5.0 或更新版本。 如有需要，請[安裝 Azure CLI 的最新版本](/cli/azure/install-azure-cli)。

## <a name="see-results"></a>查看結果

當您在 PowerShell 或 Azure CLI 中使用 if if 時，輸出會包含以色彩標示的結果，可協助您查看不同類型的變更。

![Resource Manager 範本部署假設作業 fullresourcepayload 和變更類型](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

文字輸出為：

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> 假設作業無法解析 [參考函數](template-functions-resource.md#reference)。 每次您將屬性設定為包含參考函數的範本運算式時，屬性會變更的假設報表。 發生此行為的原因是，假設比較屬性 (的目前值（例如）， `true` 或 `false` 使用無法解析的範本運算式) 的布林值。 很明顯地，這些值不會相符。 當您部署範本時，只有在範本運算式解析為不同的值時，屬性才會變更。

## <a name="what-if-commands"></a>假設命令

### <a name="azure-powershell"></a>Azure PowerShell

若要在部署範本之前預覽變更，請使用 [new->new-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 或 [AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment)。 將 `-Whatif` 切換參數新增至部署命令。

* `New-AzResourceGroupDeployment -Whatif` 資源群組部署
* `New-AzSubscriptionDeployment -Whatif` 以及 `New-AzDeployment -Whatif` 適用于訂用帳戶層級部署

您可以使用 `-Confirm` 切換參數來預覽變更，並提示您繼續進行部署。

* `New-AzResourceGroupDeployment -Confirm` 資源群組部署
* `New-AzSubscriptionDeployment -Confirm` 以及 `New-AzDeployment -Confirm` 適用于訂用帳戶層級部署

上述命令會傳回可供您手動檢查的文字摘要。 若要取得可透過程式設計方式檢查變更的物件，請使用 [AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) 或 [AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult)。

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` 資源群組部署
* `$results = Get-AzSubscriptionDeploymentWhatIfResult` 或 `$results = Get-AzDeploymentWhatIfResult` 用於訂用帳戶層級部署

### <a name="azure-cli"></a>Azure CLI

若要在部署範本之前預覽變更，請使用：

* [az deployment group](/cli/azure/deployment/group#az-deployment-group-what-if) for 資源群組部署的假設
* [az deployment sub if](/cli/azure/deployment/sub#az-deployment-sub-what-if) for 訂用帳戶層級部署
* [az 部署 mg](/cli/azure/deployment/mg#az-deployment-mg-what-if) 適用于管理群組部署的結果
* [az deployment tenant 假設](/cli/azure/deployment/tenant#az-deployment-tenant-what-if) 租使用者部署

您可以使用 `--confirm-with-what-if` 參數 (或其簡短形式 `-c`) 來預覽變更，並提示您繼續進行部署。 將此參數新增至：

* [az 部署群組建立](/cli/azure/deployment/group#az-deployment-group-create)
* [az deployment sub create](/cli/azure/deployment/sub#az-deployment-sub-create)。
* [az 部署 mg 建立](/cli/azure/deployment/mg#az-deployment-mg-create)
* [az deployment tenant create](/cli/azure/deployment/tenant#az-deployment-tenant-create)

例如， `az deployment group create --confirm-with-what-if` `-c` 針對資源群組部署使用或。

上述命令會傳回可供您手動檢查的文字摘要。 若要取得可透過程式設計方式檢查變更的 JSON 物件，請使用 `--no-pretty-print` 參數。 例如，用於 `az deployment group what-if --no-pretty-print` 資源群組部署。

如果您想要傳回沒有色彩的結果，請開啟您的 [Azure CLI 配置](/cli/azure/azure-cli-configuration) 檔。 將 **no_color** 設定為 **[是]**。

### <a name="azure-rest-api"></a>Azure REST API

針對 REST API，請使用：

* [部署-](/rest/api/resources/deployments/whatif) 資源群組部署的 What If
* [部署-](/rest/api/resources/deployments/whatifatsubscriptionscope) 訂用帳戶部署的訂用帳戶範圍 What If
* 部署-管理群組部署的[管理群組範圍 What If](/rest/api/resources/deployments/whatifatmanagementgroupscope)
* [部署-](/rest/api/resources/deployments/whatifattenantscope) 租使用者部署的租使用者範圍 What If。

## <a name="change-types"></a>變更類型

「假設」作業會列出六種不同類型的變更：

- **建立**：資源目前不存在，但已在範本中定義。 將會建立資源。

- **Delete**：只有在使用 [完整模式](deployment-modes.md) 進行部署時，才適用此變更類型。 資源存在，但未在範本中定義。 將使用「完整模式」刪除資源。 只有 [支援完整模式刪除](complete-mode-deletion.md) 的資源會包含在此變更類型中。

- **略** 過：資源存在，但未在範本中定義。 將不會部署或修改資源。

- **NoChange**：資源存在，而且是在範本中定義。 資源將會重新部署，但資源的屬性不會變更。 當 [ResultFormat](#result-format) 設定為 `FullResourcePayloads` （這是預設值）時，就會傳回此變更類型。

- **Modify**：資源存在，而且是在範本中定義。 資源將會重新部署，且資源的屬性將會變更。 當 [ResultFormat](#result-format) 設定為 `FullResourcePayloads` （這是預設值）時，就會傳回此變更類型。

- **部署**：資源存在，而且是在範本中定義。 將會重新部署資源。 資源的屬性不一定會變更。 當作業沒有足夠資訊可判斷是否有任何屬性變更時，作業會傳回此變更類型。 當 [ResultFormat](#result-format) 設定為時，您只會看到此情況 `ResourceIdOnly` 。

## <a name="result-format"></a>結果格式

您可以控制針對預測的變更所傳回的詳細資料層級。 您有兩個選擇：

* **FullResourcePayloads** -傳回將變更的資源清單，以及即將變更之屬性的詳細資料
* **ResourceIdOnly** -傳回將變更的資源清單

預設值為 **FullResourcePayloads**。

針對 PowerShell 部署命令，請使用 `-WhatIfResultFormat` 參數。 在程式設計物件命令中，使用 `ResultFormat` 參數。

針對 Azure CLI，請使用 `--result-format` 參數。
 
下列結果會顯示兩個不同的輸出格式：

- 完整資源承載

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- 僅限資源識別碼

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>執行模擬操作

### <a name="set-up-environment"></a>設定環境

為了查看假設的運作方式，讓我們來執行一些測試。 首先，部署 [可建立虛擬網路的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json)。 您將使用此虛擬網路來測試如何回報變更。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>測試修改

部署完成之後，您就可以開始測試「假設」作業。 這次您會部署 [變更虛擬網路的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)。 遺漏了原始標記、子網已移除，且位址首碼已變更。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

what-if 輸出會與以下內容相似：

![Resource Manager 範本部署假設作業輸出](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

文字輸出為：

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

請注意，輸出的上方會定義色彩來指出變更的類型。

在輸出的底部，它會顯示已刪除標記擁有者。 位址首碼從 10.0.0.0/16 變更為 10.0.0.0/15。 名為 subnet001 的子網已刪除。 請記住，這些變更尚未部署。 您會看到部署範本時所發生之變更的預覽。

某些列為已刪除的屬性，實際上不會變更。 當屬性不在範本中時，可能會錯誤地報告為已刪除，但會在部署期間自動設定為預設值。 這項結果在假設回應中被視為「雜訊」。 最後部署的資源將會設定屬性的值。 假設作業逐漸成熟，這些屬性將會從結果中篩選掉。

## <a name="programmatically-evaluate-what-if-results"></a>以程式設計方式評估假設結果

現在，讓我們透過將命令設定為變數，以程式設計方式評估假設結果。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

您可以看到每個變更的摘要。

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>確認刪除

假設作業支援使用 [部署模式](deployment-modes.md)。 當設定為 [完成] 模式時，會刪除不在範本中的資源。 下列範例會部署未在完整模式中 [定義任何資源的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) 。

若要在部署範本之前先預覽變更，請在部署命令中使用 confirm switch 參數。 如果變更如您所預期，即可認可完成部署。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

因為範本中未定義任何資源，且部署模式設定為 [完成]，所以將會刪除虛擬網路。

![Resource Manager 範本部署假設作業輸出部署模式完成](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

文字輸出為：

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

您會看到預期的變更，並確認您想要執行部署。

## <a name="sdks"></a>SDK

您可以透過 Azure Sdk 使用「假設」作業。

* 若為 Python，請使用 [假設](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-)。

* 針對 JAVA，請使用 [DeploymentWhatIf 類別](/java/api/com.microsoft.azure.management.resources.deploymentwhatif)。

* 針對 .NET，請使用 [DeploymentWhatIf 類別](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif)。

## <a name="next-steps"></a>後續步驟

- 如果您注意到「假設」作業有不正確的結果，請在中報告問題 [https://aka.ms/whatifissues](https://aka.ms/whatifissues) 。
- 若要使用 Azure PowerShell 部署範本，請參閱 [使用 ARM 範本部署資源和 Azure PowerShell](deploy-powershell.md)。
- 若要使用 Azure CLI 部署範本，請參閱 [使用 ARM 範本部署資源和 Azure CLI](deploy-cli.md)。
- 若要使用 REST 部署範本，請參閱 [使用 ARM 範本部署資源和 Resource Manager REST API](deploy-rest.md)。
