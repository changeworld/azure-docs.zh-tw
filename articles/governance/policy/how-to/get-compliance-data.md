---
title: 取得原則合規性資料
description: Azure 原則評估和效果會決定合規性。 了解如何取得 Azure 資源的合規性詳細資料。
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: 112badce00ec56df0f80c7b51bb4789a414cdcbd
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920231"
---
# <a name="get-compliance-data-of-azure-resources"></a>取得 Azure 資源的合規性資料

Azure 原則的其中一個最大優點，就是能夠針對訂用帳戶中的資源或訂用帳戶的[管理群組](../../management-groups/overview.md)，提供相關見解和控制權。 此控制權可運用在許多不同方面，例如防止在錯誤的位置建立資源、強制執行通用且一致的標記使用方式，或稽核現有的資源以取得適當的組態和設定。 在所有情況下，Azure 原則都會產生資料來讓您了解環境的合規性狀態。

有數種方式可存取原則和計畫指派所產生的合規性資訊：

- 使用 [Azure 入口網站](#portal)
- 透過[命令列](#command-line)指令碼

在查看這些報告合規性的方法之前，讓我們來看何時會更新合規性資訊，以及觸發評估週期的頻率和事件。

> [!WARNING]
> 如果合規性狀態回報為 [ **未註冊**]，請確認已註冊 **Microsoft.policyinsights** 資源提供者，且使用者具有適當的 azure 角色型存取控制 (azure rbac) 許可權，如 [Azure 原則中的 azure rbac 許可權](../overview.md#azure-rbac-permissions-in-azure-policy)所述。

## <a name="evaluation-triggers"></a>評估觸發程序

透過 `PolicyStates` 和 `PolicyEvents` 作業可在 `Microsoft.PolicyInsights`「資源提供者」中取得完成的評估週期結果。 如需有關 Azure 原則見解 REST API 之作業的詳細資訊，請參閱 [Azure 原則見解](/rest/api/policy-insights/)。

下列各種事件都會導致評估指派的原則和計畫：

- 將新的原則或計畫指派給範圍。 將指派套用至定義的範圍需要大約 30 分鐘的時間。 套用之後，就會針對新指派的原則或計畫開始該範圍內資源的評估週期，並根據原則或方案所使用的效果，將資源標示為符合規範、不符合規範或豁免。 針對大範圍資源評估大型原則或方案可能需要一些時間。 因此，對於何時會完成評估週期，並沒有任何預先定義的預期。 完成之後，會在入口網站和 SDK 中提供更新的合規性結果。

- 更新已指派給範圍的原則或計畫。 此案例的評估週期和時間與範圍的新指派相同。

- 資源會透過 Azure Resource Manager、REST API 或支援的 SDK，在具有指派的範圍內部署或更新。 在此案例中，個別資源的效果事件 (附加、稽核、拒絕、部署) 和合規性狀態資訊，約 15 分鐘後會在入口網站和 SDK 中變成可用。 此事件不會導致對其他資源進行評估。

- 已建立、更新或刪除 [原則豁免](../concepts/exemption-structure.md) 。 在此案例中，會針對定義的豁免範圍評估對應的指派。

- 標準合規性評估週期。 每 24 小時會自動對指派進行一次重新評估。 由許多資源組成的大型原則或方案可能需要一些時間，因此對於何時會完成評估週期，並沒有任何預先定義的預期。 完成之後，會在入口網站和 SDK 中提供更新的合規性結果。

- [來賓組態](../concepts/guest-configuration.md)資源提供者會以受控資源的合規性詳細資料進行更新。

- 隨選掃描

### <a name="on-demand-evaluation-scan"></a>隨選評估掃描

訂用帳戶或資源群組的評估掃描可以使用 Azure CLI、Azure PowerShell、REST API 的呼叫，或使用 [Azure 原則合規性掃描 GitHub 動作](https://github.com/marketplace/actions/azure-policy-compliance-scan)來啟動。
這個掃描是一個非同步程序。

#### <a name="on-demand-evaluation-scan---github-action"></a>隨選評估掃描-GitHub 動作

使用 [Azure 原則合規性掃描動作](https://github.com/marketplace/actions/azure-policy-compliance-scan) ，在一或多個資源、資源群組或訂用帳戶上，從您的 [GitHub 工作流程](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) 觸發隨選評估掃描，並根據資源的合規性狀態將工作流程閘道。 您也可以將工作流程設定為在排程的時間執行，以便在方便的時間取得最新的合規性狀態。 （選擇性）此 GitHub 動作可以產生掃描的資源合規性狀態報表，以供進一步分析或封存。

下列範例會執行訂用帳戶的合規性掃描。 

```yaml
on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

如需詳細資訊和工作流程範例，請參閱 [Azure 原則合規性掃描](https://github.com/Azure/policy-compliance-scan)存放庫的 GitHub 動作。

#### <a name="on-demand-evaluation-scan---azure-cli"></a>隨選評估掃描-Azure CLI

相容性掃描會使用 [az policy state 觸發程式掃描](/cli/azure/policy/state#az_policy_state_trigger_scan) 命令來啟動。

根據預設，`az policy state trigger-scan` 會針對目前訂用帳戶中的所有資源啟動評估。 若要開始評估特定的資源群組，請使用 **資源群組** 參數。 下列範例會在目前的訂用帳戶中針對 _MyRG_ 資源群組啟動合規性掃描：

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

您可以選擇不等待非同步進程完成，然後再繼續進行 **無等待** 參數。

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>隨選評估掃描 - Azure PowerShell

合規性掃描是使用 [Start-AzPolicyComplianceScan](/powershell/module/az.policyinsights/start-azpolicycompliancescan) Cmdlet 來啟動。

根據預設，`Start-AzPolicyComplianceScan` 會針對目前訂用帳戶中的所有資源啟動評估。 若要針對特定的資源群組啟動評估，請使用 **ResourceGroupName** 參數。 下列範例會在目前的訂用帳戶中針對 _MyRG_ 資源群組啟動合規性掃描：

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

您可以讓 PowerShell 等候非同步呼叫完成再提供結果輸出，或是讓其以[作業](/powershell/module/microsoft.powershell.core/about/about_jobs)的形式於背景執行。 若要使用 PowerShell 作業來在背景執行合規性掃描，請使用 **AsJob** 參數並將值設定到物件上，例如此範例中的 `$job`：

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

您可以檢查 `$job` 物件來檢查作業的狀態。 作業的類型為 `Microsoft.Azure.Commands.Common.AzureLongRunningJob`。 使用 `$job` 物件上的 `Get-Member` 來查看可用的屬性與方法。

在合規性掃描執行時，檢查 `$job` 物件會輸出如下的結果：

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

當合規性掃描完成時，**State** 屬性會變更為 _Completed_。

#### <a name="on-demand-evaluation-scan---rest"></a>隨選評估掃描 - REST

作為非同步程序，啟動掃描的 REST 端點並不會等待掃描完成以回應。 相反地，它會提供 URI，可用來查詢要求之評估的狀態。

在每個 REST API URI 中有一些變數，需要您以自己的值取代它們：

- `{YourRG}` - 以您的資源群組名稱取代
- `{subscriptionId}` - 以您的訂用帳戶識別碼取代

掃描支援訂用帳戶或資源群組中的資源評估。 請使用 REST API **POST** 命令，運用下列 URI 結構來依據範圍啟動掃描：

- 訂用帳戶

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- 資源群組

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

呼叫會傳回 **202 已接受** 狀態。 包含在回應標頭中的是 **Location** 屬性，其格式如下：

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` 是針對要求的範圍以靜態方式所產生。 如果某個範圍已在執行隨選掃描，則不會啟動新的掃描。 相反地，系統會為新要求提供相同的 `{ResourceContainerGUID}` **位置** URI 作為狀態。 針對 **Location** URI 的 REST API **GET** 命令會傳回 **202 已接受**，同時持續進行評估。 評估掃描完成時，會傳回 **200 確定** 狀態。 完成掃描的主體是具有狀態的 JSON 回應：

```json
{
    "status": "Succeeded"
}
```

#### <a name="on-demand-evaluation-scan---visual-studio-code"></a>隨選評估掃描-Visual Studio Code

Visual Studio 程式碼的 Azure 原則延伸模組能夠針對特定資源執行評估掃描。 這項掃描是同步處理的程式，與 Azure PowerShell 和 REST 方法不同。
如需詳細資訊和步驟，請參閱 [使用 VS Code 擴充功能的隨選評估](./extension-for-vscode.md#on-demand-evaluation-scan)。

## <a name="how-compliance-works"></a>合規性的運作方式

在指派中，如果資源沒有遵循原則或方案規則，也不會 _豁免_，則資源不 **符合規範**。 下表顯示不同的原則效果如何與結果合規性狀態的條件評估搭配使用：

| 資源狀態 | 效果 | 原則評估 | 相容性狀態 |
| --- | --- | --- | --- |
| 新功能或更新功能 | Audit、Modify、AuditIfNotExist | True | 不符合規範 |
| 新功能或更新功能 | Audit、Modify、AuditIfNotExist | False | 相容 |
| Exists | Deny、Audit、Append、Modify、DeployIfNotExist、AuditIfNotExist | True | 不符合規範 |
| Exists | Deny、Audit、Append、Modify、DeployIfNotExist、AuditIfNotExist | False | 相容 |

> [!NOTE]
> DeployIfNotExist 和 AuditIfNotExist 效果要求 IF 陳述式為 TRUE 且存在條件為 FALSE，才不符合規範。 若為 TRUE，IF 條件會觸發相關資源的存在條件評估。

例如，假設您有資源群組 – ContosoRG，且部分的儲存體帳戶 (以紅色醒目提示) 會公開至公用網路。

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="在 Contoso R G 資源群組中公開至公用網路的儲存體帳戶圖表。" border="false":::
   此圖顯示 Contoso R G 資源群組中五個儲存體帳戶的影像。  儲存體帳戶一和三是藍色，而儲存體帳戶二、四和五是紅色。
:::image-end:::

在此範例中，您必須注意安全性風險。 現在您已建立原則指派，它會針對 ContosoRG 資源群組中所有包含和非豁免的儲存體帳戶進行評估。 其會稽核三個不符合規範的儲存體帳戶，並因此將其狀態變更為 **不符合規範**。

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Contoso R G 資源群組中儲存體帳戶合規性的圖表。" border="false":::
   此圖顯示 Contoso R G 資源群組中五個儲存體帳戶的影像。 儲存體帳戶的一個和三個現在在其下有綠色核取記號，而儲存體帳戶二、四和五現在都有紅色警告符號。
:::image-end:::

除了 **符合規範** 和 **不符合規範** 之外，原則和資源還有四個其他狀態：

- **豁免**：資源在指派範圍內，但有 [定義的豁免](../concepts/exemption-structure.md)。
- **衝突**：有兩個或多個原則定義有衝突的規則。 例如，兩個定義會附加具有不同值的相同標記。
- **未啟動**：尚未針對原則或資源開始評估週期。
- **未註冊**：「Azure 原則資源提供者」尚未註冊，或登入的帳戶無權讀取合規性資料。

Azure 原則使用定義中的 **類型**、 **名稱** 或 **種類** 欄位來判斷資源是否相符。 當資源相符時，就會將其視為適用，且其狀態會是 [ **符合規範**]、[ **不符合規範**] 或 [ **豁免**]。 如果 **類型**、 **名稱** 或 **種類** 是定義中的唯一屬性，則會將所有包含和非豁免的資源視為適用，並進行評估。

合規性百分比是藉由將 **符合** 和 **豁免** 資源除以 _總資源_ 而定。 _總資源_ 會定義為 **符合規範**、 **不符合規範**、 **豁免** 和 **衝突** 資源的總和。 整體合規性數目是 **符合規範** 或 **豁免** 的相異資源與所有相異資源總和的總和。 在下圖中，有 20 種適用的不同資源，只有一種是 **不符合規範**。
整體資源合規性為 95% (20 分之 19)。

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="[合規性] 頁面中原則合規性詳細資料的螢幕擷取畫面。" border="false":::

> [!NOTE]
> Azure 原則中的法規合規性是預覽功能。 在入口網站中，SDK 和頁面中的相容性屬性會因啟用的計畫而有所不同。 如需詳細資訊，請參閱 [法規合規性](../concepts/regulatory-compliance.md)

## <a name="portal"></a>入口網站

Azure 入口網站示範視覺化並了解您環境中合規性狀態的圖形化體驗。 在 [原則] 頁面上，[概觀] 選項提供有關原則和計畫合規性之可用範圍的詳細資料。 除了每個指派的合規性狀態和計數之外，還包含一個圖表，顯示過去七天的合規性。 [合規性] 頁面包含大部分相同的資訊 (圖表除外)，但提供額外的篩選和排序選項。

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="相容性頁面、篩選選項和詳細資料的螢幕擷取畫面。" border="false":::

由於可以將一個原則或方案指派給不同的範圍，因此表格包含每個指派的範圍，以及所指派的定義類型。 也會提供每個指派的不符合規範的資源和不符合規範的原則數目。 在資料表中選取原則或方案，可讓您更深入瞭解該特定指派的合規性。

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="[合規性詳細資料] 頁面的螢幕擷取畫面，包括計數和符合資源規範的詳細資料。" border="false":::

[資源合規性] 索引標籤上的資源清單會顯示目前指派的現有資源評估狀態。 此索引標籤預設為 [不符合規範]，但您可以進行篩選。
[ **事件** ] 索引標籤底下會顯示 ([附加]、[審核]、[拒絕]、[部署]、[建立資源] 所觸發的) 的事件。

> [!NOTE]
> 針對 AKS 引擎原則，所顯示的資源是資源群組。

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="合規性詳細資料頁面上 [事件] 索引標籤的螢幕擷取畫面。" border="false":::

<a name="component-compliance"></a> 針對 [資源提供者模式](../concepts/definition-structure.md#resource-provider-modes) 資源，在 [ **資源相容性** ] 索引標籤上，選取資源或以滑鼠右鍵按一下資料列，然後選取 [ **View 合規性詳細資料** ] 會開啟元件合規性詳細資料。 此頁面也會提供索引標籤以查看指派至此資源的原則、事件、元件事件，以及變更歷程記錄。

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="[元件相容性] 索引標籤的螢幕擷取畫面以及資源提供者模式指派的合規性詳細資料。" border="false":::

返回 [資源合規性] 頁面，以滑鼠右鍵按一下您想要收集更多詳細資料的事件資料列，然後選取 [顯示活動記錄]。 活動記錄頁面隨即開啟，並會預先篩選至顯示指派和事件詳細資料的搜尋結果。 活動記錄檔提供有關這些事件的其他內容和資訊。

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="活動記錄的螢幕擷取畫面，適用于 Azure 原則活動與評估。" border="false":::

### <a name="understand-non-compliance"></a>了解不符合規範

當系統將資源判斷為 **不符合規範** 時，其有許多可能的原因。 若要判斷資源為 **不符合規範** 的原因，或是找出導致此情況的變更，請參閱 [判斷不符合規範的原因](./determine-non-compliance.md)。

## <a name="command-line"></a>命令列

您可以使用 REST API (（包括 [ARMClient](https://github.com/projectkudu/ARMClient)) 、Azure PowerShell 和 Azure CLI）來抓取入口網站中可用的相同資訊。 如需 REST API 的完整詳細資訊，請參閱 [Azure 原則見解](/rest/api/policy-insights/)參考。 REST API 參考頁面上有每個作業的 [試用] 綠色按鈕，可讓您直接在瀏覽器中試用。

使用 ARMClient 或類似工具來處理 REST API 範例的 Azure 驗證。

### <a name="summarize-results"></a>摘要結果

使用 REST API 時，可以由容器、定義或指派來執行摘要。 以下是使用 Azure 原則見解的[對訂用帳戶進行摘要](/rest/api/policy-insights/policystates/summarizeforsubscription)，在訂用帳戶層級進行摘要的範例：

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
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
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant'",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>查詢資源

在上述範例中，**value.policyAssignments.policyDefinitions.results.queryResultsUri** 會為特定原則定義的所有不符合規範資源提供一個範例 URI。 查看 **$filter** 值時，ComplianceState 等於 (eq) 為 ' 不相容」，則會為原則定義指定 PolicyAssignmentId，然後指定 PolicyDefinitionId 本身。 之所以要在篩選中包含 PolicyAssignmentId，是因為 PolicyDefinitionId 可能存在於數個具有各種不同範圍的原則或方案指派中。 藉由同時指定 PolicyAssignmentId 和 the PolicyDefinitionId，我們便可以明確指出要尋找的結果。 之前，針對 PolicyStates，我們使用了 **latest**，這會自動將 **from** 和 **to** 時間範圍設定為過去 24 小時。

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
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
        "ComplianceState": "NonCompliant",
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

當建立或更新資源時，會產生原則評估結果。 結果稱為 _原則事件_。 您可以使用下列 URI 來檢視與訂用帳戶建立關聯的最新原則事件。

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2019-10-01
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

如需有關查詢原則事件的詳細資訊，請參閱 [Azure 原則事件](/rest/api/policy-insights/policyevents)參考文章。

### <a name="azure-cli"></a>Azure CLI

Azure 原則的 [Azure CLI](/cli/azure/what-is-azure-cli) 命令群組涵蓋了 REST 或 Azure PowerShell 中可用的大部分作業。 如需可用命令的完整清單，請參閱 [Azure CLI-Azure 原則總覽](/cli/azure/policy)。

範例：取得不符合規範資源數最高之最上層指派原則的狀態摘要。

```azurecli-interactive
az policy state summarize --top 1
```

回應的上半部看起來如以下範例所示：

```json
{
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
    "odataid": null,
    "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
            "policyDefinitions": [{
                "effect": "audit",
                "policyDefinitionGroupNames": [
                    ""
                ],
                "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
                "policyDefinitionReferenceId": "",
                "results": {
                    "nonCompliantPolicies": null,
                    "nonCompliantResources": 398,
                    "policyDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "policyGroupDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2020-07-14 14:01:22Z&$to=2020-07-15 14:01:22Z and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8' and PolicyDefinitionId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a'",
                    "resourceDetails": [{
                            "complianceState": "noncompliant",
                            "count": 398
                        },
                        {
                            "complianceState": "compliant",
                            "count": 4
                        }
                    ]
                }
            }],
    ...
```

範例：取得最近評估之資源的狀態記錄 (預設是依時間戳記遞減排序)。

```azurecli-interactive
az policy state list --top 1
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "Compliant",
    "effectiveParameters": "",
    "isCompliant": true,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/securitycenterbuiltin",
    "policyAssignmentName": "SecurityCenterBuiltIn",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "auditifnotexists",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionName": "aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionReferenceId": "identityenablemfaforownerpermissionsmonitoring",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "security center",
    "policySetDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionName": "1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "",
    "resourceId": "/subscriptions/{subscriptionId}",
    "resourceLocation": "",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Resources/subscriptions",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.903433+00:00"
  }
]
```

範例：取得所有不符合規範虛擬網路資源的詳細資料。

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'"
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

範例：取得與不符合規範虛擬網路資源相關且在特定日期後發生的事件。

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'" --from '2020-07-14T00:00:00Z'
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

### <a name="azure-powershell"></a>Azure PowerShell

在 PowerShell 資源庫中，會以 [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights) \(英文\) 的形式提供適用於 Azure 原則的 Azure PowerShell 模組。 您可以使用 PowerShellGet，透過 `Install-Module -Name Az.PolicyInsights` 來安裝模組 (請確定您已安裝最新的 [Azure PowerShell](/powershell/azure/install-az-ps))：

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

範例：取得不符合規範資源數最高之最上層指派原則的狀態摘要。

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

範例：取得最近評估之資源的狀態記錄 (預設是依時間戳記遞減排序)。

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
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

範例：取得所有不符合規範虛擬網路資源的詳細資料。

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
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

範例：取得與不相容的虛擬網路資源相關的事件，這些資源會在特定日期之後發生、轉換成 CSV 物件，以及匯出至檔案。

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

物件的輸出 `$policyEvents` 看起來如下所示：

```output
Timestamp                  : 9/19/2020 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
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

您可以透過 Azure PowerShell Cmdlet `Get-AzADUser` 使用 [PrincipalOid] 欄位來取得特定使用者。 以您從上一個範例取得的回應取代 **{principalOid}** 。

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure 監視器記錄

如果您的 [Log Analytics 工作區](../../../azure-monitor/log-query/log-query-overview.md) 與 `AzureActivity` 您的訂用帳戶系結的 [活動記錄分析解決方案](../../../azure-monitor/platform/activity-log.md) 中，您也可以使用簡單的 Kusto 查詢和資料表，從評估新的和更新的資源來查看不符合規範的結果 `AzureActivity` 。 有了「Azure 監視器」記錄中的詳細資料，您便可以設定警示來監看不符合規範的情況。

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Azure 監視器記錄的螢幕擷取畫面，其中顯示 AzureActivity 資料表中 Azure 原則動作。" border="false":::

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 了解如何[以程式設計方式建立原則](programmatically-create.md)。
- 了解如何[補救不符合規範的資源](remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
