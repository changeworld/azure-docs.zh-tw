---
title: 範本部署假設（預覽）
description: 在部署 Azure Resource Manager 範本之前，請先判斷您的資源會發生哪些變更。
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: b9d4150779842614a5dc284a2b3a489593fabfe1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388552"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Resource Manager 範本部署假設作業（預覽）

在部署範本之前，您可能會想要預覽即將發生的變更。 Azure Resource Manager 提供「假設」作業，讓您在部署範本時查看資源的變更方式。 「假設」作業不會對現有的資源進行任何變更。 相反地，它會在部署指定的範本時預測變更。

> [!NOTE]
> 「假設」作業目前為預覽狀態。 若要加以使用，您必須[註冊預覽版](https://aka.ms/armtemplatepreviews)。 作為預覽版本，結果有時可能會顯示資源將會變更，但實際上不會發生任何變更。 我們正努力減少這些問題，但我們需要您的協助。 請在[https://aka.ms/whatifissues](https://aka.ms/whatifissues)報告這些問題。

您可以搭配 PowerShell 命令或 REST API 作業來使用「假設」操作。

在 PowerShell 中，輸出包含色彩編碼的結果，可協助您查看不同類型的變更。

![Resource Manager 範本部署假設作業 fullresourcepayload 和變更類型](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

文字 ouptput 為：

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

## <a name="what-if-commands"></a>假設命令

您可以使用 Azure PowerShell 或 Azure REST API 進行「假設」作業。

### <a name="azure-powershell"></a>Azure PowerShell

若要在部署範本之前查看變更的預覽，請將 `-Whatif` 切換參數新增至部署命令。

* 資源群組部署的 `New-AzResourceGroupDeployment -Whatif`
* 訂用帳戶層級部署的 `New-AzSubscriptionDeployment -Whatif` 和 `New-AzDeployment -Whatif`

或者，您可以使用 `-Confirm` 切換參數來預覽變更，並提示您是否要繼續進行部署。

* 資源群組部署的 `New-AzResourceGroupDeployment -Confirm`
* 訂用帳戶層級部署的 `New-AzSubscriptionDeployment -Confirm` 和 `New-AzDeployment -Confirm`

上述命令會傳回您可以手動檢查的文字摘要。 若要取得可透過程式設計方式檢查是否有變更的物件，請使用：

* 資源群組部署的 `$results = Get-AzResourceGroupDeploymentWhatIf`
* 訂用帳戶層級部署的 `$results = Get-AzSubscriptionDeploymentWhatIf` 或 `$results = Get-AzDeploymentWhatIf`

> [!NOTE]
> 在版本 2.0.1-Alpha5 之前，您使用 `New-AzDeploymentWhatIf` 命令。 此命令已由 `Get-AzDeploymentWhatIf`、`Get-AzResourceGroupDeploymentWhatIf`和 `Get-AzSubscriptionDeploymentWhatIf` 命令所取代。 如果您已使用舊版，則需要更新該語法。 已移除 `-ScopeType` 參數。

### <a name="azure-rest-api"></a>Azure REST API

針對 REST API，請使用：

* [部署-](/rest/api/resources/deployments/whatif)資源群組部署的 What If
* [部署-](/rest/api/resources/deployments/whatifatsubscriptionscope)訂用帳戶層級部署的訂用帳戶範圍 What If

## <a name="change-types"></a>變更類型

「假設」作業會列出六種不同類型的變更：

- **建立**：資源目前不存在，但在範本中定義。 將會建立資源。

- **刪除**：只有在使用[完整模式](deployment-modes.md)進行部署時，才會套用此變更類型。 資源存在，但未在範本中定義。 使用完整模式時，將會刪除資源。 只有[支援完整模式刪除](complete-mode-deletion.md)的資源才會包含在此變更類型中。

- **忽略**：資源存在，但未在範本中定義。 將不會部署或修改資源。

- **NoChange**：資源存在，並定義于範本中。 資源將會重新部署，但資源的屬性不會變更。 當[ResultFormat](#result-format)設定為 `FullResourcePayloads`（這是預設值）時，就會傳回這種變更類型。

- **修改**：資源存在，並定義于範本中。 資源將會重新部署，而且資源的屬性將會變更。 當[ResultFormat](#result-format)設定為 `FullResourcePayloads`（這是預設值）時，就會傳回這種變更類型。

- **部署**：資源存在，並定義于範本中。 資源將會重新部署。 資源的屬性不一定會變更。 當作業沒有足夠的資訊可判斷是否有任何屬性變更時，作業會傳回此變更類型。 當[ResultFormat](#result-format)設定為 `ResourceIdOnly`時，您只會看到此條件。

## <a name="result-format"></a>結果格式

您可以控制針對預測變更所傳回的詳細資料層級。 在部署命令（`New-Az*Deployment`）中，使用 **-WhatIfResultFormat**參數。 在程式設計物件命令（`Get-Az*DeploymentWhatIf`）中，使用**ResultFormat**參數。

將 format 參數設定為**FullResourcePayloads** ，以取得將會變更的資源清單，以及將變更之屬性的詳細資料。 將 format 參數設定為**ResourceIdOnly** ，以取得將會變更的資源清單。 預設值為**FullResourcePayloads**。  

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

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>測試修改

部署完成之後，您就可以測試「假設」作業。 此時，請部署[會變更虛擬網路的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)。 缺少一個原始標記、已移除子網，而且位址首碼已經變更。

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

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

在輸出的底部，它會顯示已刪除標記擁有者。 位址前置詞已從 10.0.0.0/16 變更為 10.0.0.0/15。 已刪除名為 subnet001 的子網。 請記住，尚未實際部署這種變更。 您會看到當您部署範本時，將會發生變更的預覽。

列為 deleted 的部分屬性實際上不會變更。 當屬性不在範本中時，可能會錯誤地回報為已刪除，但會在部署期間自動設定為預設值。 此結果會被視為「假設」回應中的「雜訊」。 最後部署的資源將會有為屬性設定的值。 當假設作業成熟時，這些屬性將會從結果中篩選掉。

## <a name="programmatically-evaluate-what-if-results"></a>以程式設計方式評估假設結果

現在，讓我們藉由將命令設定為變數，以程式設計方式評估假設結果。

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
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

## <a name="confirm-deletion"></a>確認刪除

「假設」作業支援使用[部署模式](deployment-modes.md)。 當設定為完成模式時，不在範本中的資源會被刪除。 下列範例會部署未在完整模式中[定義任何資源的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json)。

若要在部署範本之前預覽變更，請使用 `-Confirm` 切換參數搭配部署命令。 如果變更如您所預期，請確認您想要部署完成。

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

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

## <a name="next-steps"></a>後續步驟

- 如果您注意到「假設」的預覽版本中有不正確的結果，請在[https://aka.ms/whatifissues](https://aka.ms/whatifissues)報告問題。
- 若要使用 Azure PowerShell 部署範本，請參閱[使用 Resource Manager 範本和 Azure PowerShell 來部署資源](deploy-powershell.md)。
- 若要使用 REST 部署範本，請參閱[使用 Resource Manager 範本部署資源和 Resource Manager REST API](deploy-rest.md)。
