---
title: '範本部署假設 (預覽) '
description: 在部署 Azure Resource Manager 範本之前，請先判斷您的資源會發生哪些變更。
author: tfitzmac
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: tomfitz
ms.openlocfilehash: 27efe1e03b8a0d373d566106a53a41007731973e
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810066"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>ARM 範本部署假設作業 (預覽) 

將 Azure Resource Manager 範本部署 (ARM 範本) 之前，您可以預覽將會發生的變更。 Azure Resource Manager 提供「假設」作業，讓您在部署範本時查看資源的變更方式。 「假設」作業不會對現有的資源進行任何變更。 相反地，它會在部署指定的範本時預測變更。

> [!NOTE]
> 「假設」作業目前為預覽狀態。 作為預覽版本，結果有時可能會顯示資源將會變更，但實際上不會發生任何變更。 我們正努力減少這些問題，但我們需要您的協助。 請在報告這些問題 [https://aka.ms/whatifissues](https://aka.ms/whatifissues) 。

您可以使用 Azure PowerShell、Azure CLI 或 REST API 作業的「假設」操作。 資源群組、訂用帳戶、管理群組和租使用者層級部署支援的假設。

## <a name="install-azure-powershell-module"></a>安裝 Azure PowerShell 模組

若要在 PowerShell 中使用「假設」，您必須具有**4.2 或更新版本的 Az 模組**。

但是，在安裝所需的模組之前，請確定您有 PowerShell Core (6.x 或 7.x) 。 如果您有 PowerShell 5.x 或更早版本，請[更新您的 powershell 版本](/powershell/scripting/install/installing-powershell)。 您無法在 PowerShell 5.x 或更早版本上安裝所需的模組。

### <a name="install-latest-version"></a>安裝最新版本

若要安裝模組，請使用：

```powershell
Install-Module -Name Az -Force
```

如需安裝模組的詳細資訊，請參閱[Install Azure PowerShell](/powershell/azure/install-az-ps)。

### <a name="uninstall-alpha-version"></a>卸載 Alpha 版本

如果您先前已安裝 Alpha 版的假設模組，請將該模組卸載。 Alpha 版本只適用于註冊早期預覽的使用者。 如果您未安裝該預覽版，可以略過本節。

1. 以系統管理員身分執行 PowerShell
1. 檢查您已安裝的 Az .Resources 模組版本。

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. 如果您的版本號碼格式為 2.x **-Alpha**的已安裝版本，請卸載該版本。

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. 取消註冊您用來安裝預覽的假設存放庫。

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

您已準備好使用 [假設]。

## <a name="install-azure-cli-module"></a>安裝 Azure CLI 模組

若要在 Azure CLI 中使用「假設」，您必須具有 Azure CLI 2.5.0 或更新版本。 如有需要，請[安裝最新版本的 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="see-results"></a>查看結果

當您使用 PowerShell 或 Azure CLI 中的 if 時，輸出會包含色彩編碼的結果，以協助您查看不同類型的變更。

![Resource Manager 範本部署假設作業 fullresourcepayload 和變更類型](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

文字輸出如下：

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
> 「假設」作業無法解析[reference 函數](template-functions-resource.md#reference)。 每次您將屬性設定為包含參考函數的範本運算式時，屬性將會變更的假設報表。 發生此行為的原因是，假設會將屬性 (的目前值（例如 `true` 或） `false` 與未解析的範本運算式) 的布林值進行比較。 很明顯地，這些值不會相符。 當您部署範本時，只有在範本運算式解析成不同的值時，屬性才會變更。

## <a name="what-if-commands"></a>假設命令

### <a name="azure-powershell"></a>Azure PowerShell

若要在部署範本之前預覽變更，請使用 New-azresourcegroupdeployment 或[AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment)的[新](/powershell/module/az.resources/new-azresourcegroupdeployment)。 將 `-Whatif` 切換參數新增至部署命令。

* `New-AzResourceGroupDeployment -Whatif`針對資源群組部署
* `New-AzSubscriptionDeployment -Whatif`和 `New-AzDeployment -Whatif` 適用于訂用帳戶層級部署

您可以使用 `-Confirm` 切換參數來預覽變更，並提示您繼續進行部署。

* `New-AzResourceGroupDeployment -Confirm`針對資源群組部署
* `New-AzSubscriptionDeployment -Confirm`和 `New-AzDeployment -Confirm` 適用于訂用帳戶層級部署

上述命令會傳回您可以手動檢查的文字摘要。 若要取得可透過程式設計方式檢查是否有變更的物件，請使用[AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult)或[get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult)。

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`針對資源群組部署
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`或 `$results = Get-AzDeploymentWhatIfResult` 適用于訂用帳戶層級部署

### <a name="azure-cli"></a>Azure CLI

若要在部署範本之前預覽變更，請使用：

* [az deployment group](/cli/azure/deployment/group#az-deployment-group-what-if)適用于資源群組部署的假設
* 適用于訂用帳戶層級部署的[az deployment sub what if](/cli/azure/deployment/sub#az-deployment-sub-what-if)
* [az deployment mg](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-what-if)適用于管理群組部署的假設
* 適用于租使用者部署的[az deployment tenant if](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-what-if)

您可以使用 `--confirm-with-what-if` 參數 (或其簡短形式 `-c`) 來預覽變更，並提示您是否要繼續進行部署。 將下列參數新增至：

* [az deployment group create](/cli/azure/deployment/group#az-deployment-group-create)
* [az deployment sub create](/cli/azure/deployment/sub#az-deployment-sub-create)。
* [az deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create)
* [az deployment tenant create](/cli/azure/deployment/tenant#az-deployment-tenant-create)

例如，使用 `az deployment group create --confirm-with-what-if` 或做 `-c` 為資源群組部署。

上述命令會傳回您可以手動檢查的文字摘要。 若要取得可透過程式設計方式檢查是否有變更的 JSON 物件，請使用 `--no-pretty-print` 參數。 例如，用於 `az deployment group what-if --no-pretty-print` 資源群組部署。

如果您想要傳回沒有色彩的結果，請開啟您的[Azure CLI 配置](/cli/azure/azure-cli-configuration)檔。 將**no_color**設定為 **[是]**。

### <a name="azure-rest-api"></a>Azure REST API

針對 REST API，請使用：

* [部署-](/rest/api/resources/deployments/whatif)資源群組部署的 What If
* [部署-](/rest/api/resources/deployments/whatifatsubscriptionscope)訂用帳戶部署的訂用帳戶範圍 What If
* 部署-管理群組部署的[管理群組範圍 What If](/rest/api/resources/deployments/whatifatmanagementgroupscope)
* [部署-](/rest/api/resources/deployments/whatifattenantscope)租使用者部署的租使用者範圍 What If。

## <a name="change-types"></a>變更類型

「假設」作業會列出六種不同類型的變更：

- **建立**：資源目前不存在，但在範本中定義。 將會建立資源。

- **刪除**：只有在使用[完整模式](deployment-modes.md)進行部署時，才會套用此變更類型。 資源存在，但未在範本中定義。 使用完整模式時，將會刪除資源。 只有[支援完整模式刪除](complete-mode-deletion.md)的資源才會包含在此變更類型中。

- **忽略**：資源存在，但未在範本中定義。 將不會部署或修改資源。

- **NoChange**：資源存在，並定義于範本中。 資源將會重新部署，但資源的屬性不會變更。 當[ResultFormat](#result-format)設定為 `FullResourcePayloads` （這是預設值）時，就會傳回這種變更類型。

- **修改**：資源存在，並定義于範本中。 資源將會重新部署，而且資源的屬性將會變更。 當[ResultFormat](#result-format)設定為 `FullResourcePayloads` （這是預設值）時，就會傳回這種變更類型。

- **部署**：資源存在，並定義于範本中。 資源將會重新部署。 資源的屬性不一定會變更。 當作業沒有足夠的資訊可判斷是否有任何屬性變更時，作業會傳回此變更類型。 當[ResultFormat](#result-format)設定為時，您才會看到這種情況 `ResourceIdOnly` 。

## <a name="result-format"></a>結果格式

您可以控制針對預測變更所傳回的詳細資料層級。 您有兩個選擇：

* **FullResourcePayloads** -傳回將變更的資源清單，以及將變更之屬性的詳細資料
* **ResourceIdOnly** -傳回將變更的資源清單

預設值為**FullResourcePayloads**。

針對 PowerShell 部署命令，請使用 `-WhatIfResultFormat` 參數。 在程式設計物件命令中，使用 `ResultFormat` 參數。

針對 Azure CLI，請使用 `--result-format` 參數。
 
下列結果顯示兩種不同的輸出格式：

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

## <a name="run-what-if-operation"></a>執行假設作業

### <a name="set-up-environment"></a>設定環境

若要查看假設的運作方式，讓我們執行一些測試。 首先，部署[可建立虛擬網路的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json)。 您將使用此虛擬網路來測試如何回報變更。

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

部署完成之後，您就可以測試「假設」作業。 這次您會部署可[變更虛擬網路的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)。 缺少一個原始標記、已移除子網，而且位址首碼已經變更。

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

假設輸出如下所示：

![Resource Manager 範本部署假設作業輸出](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

文字輸出如下：

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

請注意，輸出的頂端會定義色彩來指出變更的類型。

在輸出的底部，它會顯示已刪除標記擁有者。 位址前置詞已從 10.0.0.0/16 變更為 10.0.0.0/15。 已刪除名為 subnet001 的子網。 請記住，這些變更並未部署。 您會看到當您部署範本時，將會發生變更的預覽。

列為 deleted 的部分屬性實際上不會變更。 當屬性不在範本中時，可能會錯誤地回報為已刪除，但會在部署期間自動設定為預設值。 此結果會被視為「假設」回應中的「雜訊」。 最後部署的資源將會有為屬性設定的值。 當假設作業成熟時，這些屬性將會從結果中篩選掉。

## <a name="programmatically-evaluate-what-if-results"></a>以程式設計方式評估假設結果

現在，讓我們藉由將命令設定為變數，以程式設計方式評估假設結果。

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

「假設」作業支援使用[部署模式](deployment-modes.md)。 當設定為完成模式時，不在範本中的資源會被刪除。 下列範例會部署未在完整模式中[定義任何資源的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json)。

若要在部署範本之前預覽變更，請搭配使用 confirm 參數和 deployment 命令。 如果變更如預期般，請確認您想要部署完成。

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

因為範本中未定義任何資源，且部署模式設定為 [完成]，所以會刪除虛擬網路。

![Resource Manager 範本部署假設作業輸出部署模式完成](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

文字輸出如下：

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

您會看到預期的變更，並且可以確認您想要執行部署。

## <a name="sdks"></a>SDK

您可以透過 Azure Sdk 使用「假設」作業。

* 針對 Python，請使用 [[假設](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations?view=azure-python#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-)]。

* 若為 JAVA，請使用[DeploymentWhatIf 類別](/java/api/com.microsoft.azure.management.resources.deploymentwhatif?view=azure-java-stable)。

* 針對 .NET，請使用[DeploymentWhatIf 類別](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif?view=azure-dotnet)。

## <a name="next-steps"></a>後續步驟

- 如果您注意到「假設」的預覽版本中有不正確的結果，請在回報問題 [https://aka.ms/whatifissues](https://aka.ms/whatifissues) 。
- 若要使用 Azure PowerShell 部署範本，請參閱[使用 ARM 範本部署資源和 Azure PowerShell](deploy-powershell.md)。
- 若要使用 Azure CLI 部署範本，請參閱[使用 ARM 範本部署資源和 Azure CLI](deploy-cli.md)。
- 若要使用 REST 部署範本，請參閱[使用 ARM 範本部署資源和 Resource Manager REST API](deploy-rest.md)。
