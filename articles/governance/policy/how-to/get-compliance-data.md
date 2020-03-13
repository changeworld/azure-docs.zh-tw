---
title: 取得原則合規性資料
description: Azure 原則評估和效果會決定合規性。 瞭解如何取得 Azure 資源的合規性詳細資料。
ms.date: 02/01/2019
ms.topic: how-to
ms.openlocfilehash: 891c9c72d8e83dc8f9adb930e8ebd11b70f6aad8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280634"
---
# <a name="get-compliance-data-of-azure-resources"></a>取得 Azure 資源的相容性資料

Azure 原則的其中一個最大優點，就是能夠針對訂用帳戶中的資源或訂用帳戶的[管理群組](../../management-groups/overview.md)，提供相關見解和控制權。 此控制權可運用在許多不同方面，例如防止在錯誤的位置建立資源、強制執行通用且一致的標記使用方式，或稽核現有的資源以取得適當的組態和設定。 在所有情況下，Azure 原則都會產生資料，讓您瞭解環境的合規性狀態。

有數種方式可存取原則和計畫指派所產生的合規性資訊：

- 使用 [Azure 入口網站](#portal)
- 透過[命令列](#command-line)指令碼

在查看這些報告合規性的方法之前，讓我們來看何時會更新合規性資訊，以及觸發評估週期的頻率和事件。

> [!WARNING]
> 如果合規性狀態報表為 [**未註冊**]，請確認**Microsoft.policyinsights**資源提供者已註冊，且使用者具有適當的角色型存取控制（RBAC）許可權，如 Azure 原則中的[RBAC 中](../overview.md#rbac-permissions-in-azure-policy)所述。

## <a name="evaluation-triggers"></a>評估觸發程序

透過 `Microsoft.PolicyInsights` 和 `PolicyStates` 作業可在 `PolicyEvents`「資源提供者」中取得完成的評估週期結果。 如需 Azure 原則 Insights REST API 作業的詳細資訊，請參閱[Azure 原則 Insights](/rest/api/policy-insights/)。

下列各種事件都會導致評估指派的原則和計畫：

- 將新的原則或計畫指派給範圍。 將指派套用至定義的範圍需要大約 30 分鐘的時間。 套用之後，就會針對新指派的原則或方案，開始該範圍內資源的評估週期，並根據原則或方案所使用的效果，將資源標示為符合規範或不符合規範。 針對大範圍資源評估大型原則或方案可能需要一些時間。 因此，對於何時將完成評估週期，並沒有任何預先定義的預期。 完成之後，會在入口網站和 SDK 中提供更新的合規性結果。

- 更新已指派給範圍的原則或計畫。 此案例的評估週期和時間與範圍的新指派相同。

- 資源會透過 Resource Manager、REST、Azure CLI 或 Azure PowerShell 部署到具有指派的範圍。 在此案例中，個別資源的效果事件 (附加、稽核、拒絕、部署) 和合規性狀態資訊，約 15 分鐘後會在入口網站和 SDK 中變成可用。 此事件不會導致對其他資源進行評估。

- 標準合規性評估週期。 每 24 小時會自動對指派進行一次重新評估。 由許多資源組成的大型原則或方案可能需要一些時間，因此對於何時將完成評估週期，並沒有任何預先定義的預期。 完成之後，會在入口網站和 SDK 中提供更新的合規性結果。

- [來賓組態](../concepts/guest-configuration.md)資源提供者會以受控資源的合規性詳細資料進行更新。

- 隨選掃描

### <a name="on-demand-evaluation-scan"></a>隨選評估掃描

針對訂用帳戶或資源群組的評估掃描，可透過呼叫 REST API 來啟動。 這個掃描是一個非同步程序。 這種情況下，REST 端點會開始掃描，而不會等待掃描完成回應。 相反地，它會提供 URI，可用來查詢要求之評估的狀態。

在每個 REST API URI 中有一些變數，需要您以自己的值取代它們：

- `{YourRG}` - 以您的資源群組名稱取代
- `{subscriptionId}` - 以您的訂用帳戶識別碼取代

掃描支援訂用帳戶或資源群組中的資源評估。 請使用 REST API **POST** 命令，運用下列 URI 結構來依據範圍啟動掃描：

- 訂用帳戶

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- 資源群組

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

呼叫會傳回 **202 已接受**狀態。 包含在回應標頭中的是 **Location** 屬性，其格式如下：

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` 是針對要求的範圍以靜態方式所產生。 如果某個範圍已在執行隨選掃描，則不會啟動新的掃描。 相反地，新的要求會針對狀態提供相同的 `{ResourceContainerGUID}`**位置**URI。 針對 **Location** URI 的 REST API **GET** 命令會傳回 **202 已接受**，同時持續進行評估。 評估掃描完成時，會傳回 **200 確定**狀態。 完成掃描的主體是具有狀態的 JSON 回應：

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>合規性的運作方式

在指派中，如果資源沒有遵循原則或方案規則，則該資源**不符合規範**。
下表顯示不同的原則效果如何與結果合規性狀態的條件評估搭配使用：

| 資源狀態 | 效果 | 原則評估 | 合規性狀態 |
| --- | --- | --- | --- |
| Exists | 拒絕、稽核、附加\*、DeployIfNotExist\*、AuditIfNotExist\* | True | 不相容 |
| Exists | 拒絕、稽核、附加\*、DeployIfNotExist\*、AuditIfNotExist\* | False | 相容 |
| 新增 | 稽核、AuditIfNotExist\* | True | 不相容 |
| 新增 | 稽核、AuditIfNotExist\* | False | 相容 |

\* Append、DeployIfNotExist 和 AuditIfNotExist 效果需要 IF 陳述式為 TRUE。
這些效果也需要存在條件為 FALSE，以呈現不符合規範。 若為 TRUE，IF 條件會觸發相關資源的存在條件評估。

例如，假設您有資源群組 – ContosoRG，且部分的儲存體帳戶 (以紅色醒目提示) 會公開至公用網路。

![公開至公用網路的儲存體帳戶](../media/getting-compliance-data/resource-group01.png)

在此範例中，您必須注意安全性風險。 既然您已建立原則指派，系統就會針對 ContosoRG 資源群組中的所有儲存體帳戶評估該原則指派。 其會稽核三個不符合規範的儲存體帳戶，並因此將其狀態變更為**不符合規範**。

![已稽核不符合規範的儲存體帳戶](../media/getting-compliance-data/resource-group03.png)

除了**符合規範**和**不符合規範**以外，原則和資源還有其他三種狀態：

- **衝突**：有兩個或多個原則有衝突的規則。 例如，兩個附加含有不同值之相同標籤的原則。
- **未啟動：未**針對原則或資源開始評估週期。
- **未註冊**： Azure 原則資源提供者尚未註冊，或登入的帳戶沒有讀取合規性資料的許可權。

Azure 原則會使用定義中的**type**和**name**欄位來判斷資源是否相符。 當資源相符時，就會將資源視為適用，且其狀態會是 [符合規範]或 [不符合規範]。 如果 **type** 或 **name** 是定義中的唯一屬性，則會將所有資源都視為適用並進行評估。

合規性百分比是透過將**符合規範**資源除以_總資源_來決定的。
_總資源_定義為**符合規範**、**不符合規範**和**衝突**資源的總和。 整體的合規性數字是**符合規範**之不同資源的總和除以所有不同資源的總和。 在下圖中，有 20 種適用的不同資源，只有一種是**不符合規範**。 整體資源合規性為 95% (20 分之 19)。

![合規性頁面中的原則合規性範例](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>入口網站

Azure 入口網站示範視覺化並了解您環境中合規性狀態的圖形化體驗。 在 [原則] 頁面上，[概觀] 選項提供有關原則和計畫合規性之可用範圍的詳細資料。 除了每個指派的合規性狀態和計數之外，還包含一個圖表，顯示過去七天的合規性。 [合規性] 頁面包含大部分相同的資訊 (圖表除外)，但提供額外的篩選和排序選項。

![Azure 原則合規性頁面的範例](../media/getting-compliance-data/compliance-page.png)

由於可以將一個原則或方案指派給不同的範圍，因此表格包含每個指派的範圍，以及所指派的定義類型。 也會提供每個指派的不符合規範的資源和不符合規範的原則數目。 按一下表格中的原則或計畫可讓您更深入地查看該特定指派的合規性。

![Azure 原則合規性詳細資料頁面的範例](../media/getting-compliance-data/compliance-details.png)

[資源合規性] 索引標籤上的資源清單會顯示目前指派的現有資源評估狀態。 此索引標籤預設為 [不符合規範]，但您可以進行篩選。
由要求所觸發來建立資源的事件 (附加、稽核、拒絕、部署) 會顯示在 [事件] 索引標籤底下。

> [!NOTE]
> 針對 AKS 引擎原則，顯示的資源是資源群組。

![Azure 原則合規性事件的範例](../media/getting-compliance-data/compliance-events.png)

針對[資源提供者模式](../concepts/definition-structure.md#resource-provider-modes)資源，請在 [**資源相容性**] 索引標籤上選取資源，或以滑鼠右鍵按一下資料列，然後選取 [**查看相容性詳細資料**]，以開啟元件合規性詳細資料 此頁面也提供索引標籤，以查看指派給此資源、事件、元件事件和變更歷程記錄的原則。

![Azure 原則元件合規性詳細資料的範例](../media/getting-compliance-data/compliance-components.png)

回到 [資源合規性] 頁面上，以滑鼠右鍵按一下您想要收集更多詳細資料的事件列，然後選取 [**顯示活動記錄**]。 活動記錄頁面隨即開啟，並會預先篩選至顯示指派和事件詳細資料的搜尋結果。 活動記錄檔提供有關這些事件的其他內容和資訊。

![Azure 原則合規性活動記錄的範例](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>瞭解不符合規範

<a name="change-history-preview"></a>

當資源判斷為**不符合規範**時，有許多可能的原因。 若要判斷資源不**符合規範**的原因，或找出所需的變更，請參閱[判斷不符合規範](./determine-non-compliance.md)。

## <a name="command-line"></a>命令列

您可以使用 REST API （包括[ARMClient](https://github.com/projectkudu/ARMClient)）、Azure PowerShell 和 Azure CLI （預覽）來抓取入口網站中可用的相同資訊。
如需 REST API 的完整詳細資料，請參閱[Azure 原則 Insights](/rest/api/policy-insights/)參考。 REST API 參考頁面上有每個作業的 [試用] 綠色按鈕，可讓您直接在瀏覽器中試用。

使用 ARMClient 或類似的工具來處理對 Azure 的驗證，以取得 REST API 範例。

### <a name="summarize-results"></a>摘要結果

使用 REST API 時，可以由容器、定義或指派來執行摘要。 以下是使用 Azure 原則深入解析[摘要的](/rest/api/policy-insights/policystates/summarizeforsubscription)訂用帳戶，在訂用帳戶層級摘要的範例：

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

此輸出會摘要訂用帳戶。 在下列範例輸出中，摘要的合規性位於 **value.results.nonCompliantResources** 和 **value.results.nonCompliantPolicies** 下方。 此要求提供進一步詳細資料，包括組成不相容數目的每個指派及每個指派的定義資訊。 階層中的每個原則物件提供可在該層級用來取得其他詳細資料的 **queryResultsUri**。

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>查詢資源

在上述範例中，**value.policyAssignments.policyDefinitions.results.queryResultsUri** 會為特定原則定義的所有不符合規範資源提供一個範例 URI。 檢視 **$filter** 值，IsCompliant 會等於 (eq) false，而 PolicyAssignmentId 會指定給原則定義，再指定給 PolicyDefinitionId 本身。 之所以要在篩選中包含 PolicyAssignmentId，是因為 PolicyDefinitionId 可能存在於數個具有各種不同範圍的原則或方案指派中。 藉由同時指定 PolicyAssignmentId 和 the PolicyDefinitionId，我們便可以明確指出要尋找的結果。 之前，針對 PolicyStates，我們使用了 **latest**，這會自動將 **from** 和 **to** 時間範圍設定為過去 24 小時。

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

為了簡潔起見，以下範例回應已修剪成單一的不符合規範資源。 詳細的回應則包含數項有關資源、原則或方案及指派的資料。 請注意，您也可以查看哪些指派參數已傳遞至原則定義。

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>檢視事件

當建立或更新資源時，會產生原則評估結果。 結果稱為_原則事件_。 您可以使用下列 URI 來檢視與訂用帳戶建立關聯的最新原則事件。

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

您的結果類似下列範例：

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

如需查詢原則事件的詳細資訊，請參閱[Azure 原則事件](/rest/api/policy-insights/policyevents)參考文章。

### <a name="azure-powershell"></a>Azure PowerShell

Azure 原則的 Azure PowerShell 模組可在 PowerShell 資源庫上作為[Az. microsoft.policyinsights](https://www.powershellgallery.com/packages/Az.PolicyInsights)。
您可以使用 PowerShellGet，透過 `Install-Module -Name Az.PolicyInsights` 來安裝模組 (請確定您已安裝最新的 [Azure PowerShell](/powershell/azure/install-az-ps))：

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

模組擁有下列 Cmdlet：

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

範例：取得其不相容資源數目最高之最上層指派原則的狀態摘要。

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

範例：取得最近評估資源的狀態記錄 (預設是依時間戳記遞減排序)。

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

範例：取得所有不相容虛擬網路資源的詳細資料。

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

範例：取得特定日期之後出現之不相容虛擬網路資源的相關事件。

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

您可以透過 Azure PowerShell Cmdlet  **使用 [PrincipalOid]** `Get-AzADUser` 欄位來取得特定使用者。 以您從上一個範例取得的回應取代 **{principalOid}** 。

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure 監視器記錄

如果您有[Log Analytics 工作區](../../../log-analytics/log-analytics-overview.md)，且其 `AzureActivity` 來自與您的訂用帳戶系結的[活動記錄分析解決方案](../../../azure-monitor/platform/activity-log-collect.md)，您也可以使用簡單的 Kusto 查詢和 `AzureActivity` 資料表，從評估週期中查看不符合規範的結果。 有了「Azure 監視器」記錄中的詳細資料，您便可以設定警示來監看不符合規範的情況。


![使用 Azure 監視器記錄 Azure 原則合規性](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>後續步驟

- 如[Azure 原則範例](../samples/index.md)，請參閱範例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 瞭解如何以程式設計[方式建立原則](programmatically-create.md)。
- 瞭解如何[補救不符合規範的資源](remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。