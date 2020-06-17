---
title: 判斷不符合規範的原因
description: 如果資源不符合規範，有許多可能的原因。 了解如何找出導致不符合規範的原因。
ms.date: 05/20/2020
ms.topic: how-to
ms.openlocfilehash: 2b26357e9957259470049209913501cc024caeaa
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684236"
---
# <a name="determine-causes-of-non-compliance"></a>判斷不符合規範的原因

當 Azure 資源被判斷為不符合原則規則時，了解該資源不符合規則的哪個部分很有幫助。 這也有助於了解何種變更改變了先前符合規範的資源，使其變成不合規範。 有兩種方式可尋找此資訊：

> [!div class="checklist"]
> - [合規性詳細資料](#compliance-details)
> - [變更歷程記錄 (預覽)](#change-history)

## <a name="compliance-details"></a>合規性詳細資料

當資源不符合規範時，可從 [原則合規性] 頁面取得該資源的合規性詳細資料。 [合規性詳細資料] 窗格包含下列資訊：

- 資源詳細資料，例如名稱、類型、位置和資源識別碼
- 上次評估目前原則指派的合規性狀態和時間戳記
- 資源不符合規範的「原因」清單

> [!IMPORTANT]
> 當「不符合規範」資源的合規性詳細資料顯示該資源的目前屬性值時，使用者就必須對資源的**類型**進行**讀取**作業。 例如，若「不符合規範」的資源為 **Microsoft.Compute/virtualMachines**，則使用者必須擁有 **Microsoft.Compute/virtualMachines/read** 作業。 如果使用者沒有所需的作業，則會顯示存取錯誤。

若要檢視合規性詳細資料，請遵循下列步驟：

1. 藉由按一下 [所有服務] 然後搜尋並選取 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

1. 在 [概觀] 或 [合規性] 頁面上，選取**合規性狀態**「不符合規範」的原則。

1. 在 [原則合規性] 頁面的 [資源合規性] 索引標籤下，以滑鼠右鍵按一下或選取**合規性狀態**「不符合規範」之資源的省略符號。 然後選取 [檢視合規性詳細資料]。

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="檢視合規性詳細資料選項" border="false":::

1. [合規性詳細資料] 窗格會顯示從資源的最新評估到目前原則指派的資訊。 在此範例中，當原則定義預計是 _14.0_ 時，卻發現 [Microsoft.Sql/servers/version] 欄位為 _12.0_。 如果資源因多種原因而不符合規範，則每個原因都會列在此窗格上。

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="合規性詳細資料窗格和不符合規範的原因" border="false":::

   對於 **auditIfNotExists** 或 **deployIfNotExists** 原則定義，詳細資料包含 **details.type** 屬性和任何選擇性屬性。 如需清單，請參閱 [auditIfNotExists 屬性](../concepts/effects.md#auditifnotexists-properties)和 [deployIfNotExists 屬性](../concepts/effects.md#deployifnotexists-properties)。 [上次評估的資源] 是定義的 **details** 區段中的相關資源。

   範例部分 **deployIfNotExists** 定義：

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="合規性詳細資料窗格 -*ifNotExists" border="false":::

> [!NOTE]
> 若要保護資料，當屬性值是「秘密」時，則目前值會顯示星號。

這些詳細資料會說明資源目前不符合規範的原因，但不會顯示何時對資源進行變更，使其變成不符合規範。 如需相關資訊，請參閱下面的[變更歷程記錄 (預覽)](#change-history)。

### <a name="compliance-reasons"></a>合規性原因

下列矩陣將每個可能的「原因」對應至原則定義中負責的[條件](../concepts/definition-structure.md#conditions)：

|原因 | 條件 |
|-|-|
|目前的值必須包含作為索引鍵的目標值。 |containsKey 或**不是** notContainsKey |
|目前的值必須包含目標值。 |contains 或**不是** notContains |
|目前的值必須等於目標值。 |equals 或**不是** notEquals |
|目前的值必須小於目標值。 |less 或**不是** greaterOrEquals |
|目前的值必須大於或等於目標值。 |greaterOrEquals 或**不是** less |
|目前的值必須大於目標值。 |greater 或**不是** lessOrEquals |
|目前的值必須小於或等於目標值。 |lessOrEquals 或**不是** greater |
|目前的值必須存在。 |exists |
|目前的值必須位於目標值內。 |in 或**不是** notIn |
|目前的值必須與目標值相同。 |like 或**不是** notLike |
|目前的值必須符合目標值 (區分大小寫)。 |match 或**不是** notMatch |
|目前的值必須符合目標值 (不區分大小寫)。 |matchInsensitively 或**不是** notMatchInsensitively |
|目前的值不得包含作為索引鍵的目標值。 |notContainsKey 或**不是** containsKey|
|目前的值不得包含目標值。 |notContains 或**不是** contains |
|目前的值不得等於目標值。 |notEquals 或**不是** equals |
|目前的值不得存在。 |**不**存在  |
|目前的值不得在目標值之中。 |notIn 或**不是** in |
|目前的值不得與目標值相同。 |notLike 或**不是** like |
|目前的值不得符合目標值 (區分大小寫)。 |notMatch 或**不是** match |
|目前的值不得符合目標值 (不區分大小寫)。 |notMatchInsensitively 或**不是** matchInsensitively |
|沒有任何相關的資源，符合原則定義中的效果詳細資料。 |不存在屬於 **then.details.type** 中定義的類型並與原則規則的 **if** 部分中定義的資源相關的資源。 |

## <a name="compliance-details-for-guest-configuration"></a>來賓設定的合規性詳細資料

對於「來賓設定」類別中的 _auditIfNotExists_ 原則，VM 內可能會評估多項設定，而且您必須檢視每項設定的詳細資料。 例如，如果您正在稽核密碼原則清單，而且其中只有一個原則具有「不符合規範」狀態，您就需要知道哪些特定密碼原則不符合規範，以及原因為何。

您也可能沒有直接登入 VM 的權限，但您必須回報 VM 為何「不符合規範」。

### <a name="azure-portal"></a>Azure 入口網站

首先遵循上一節中的相同步驟，以檢視原則合規性詳細資料。

在 [合規性詳細資料] 窗格檢視中，按一下 [上次評估的資源] 連結。

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="檢視 auditIfNotExists 定義詳細資料" border="false":::

[來賓指派] 頁面會顯示所有可用的合規性詳細資料。 檢視中的每一列都代表在機器內執行的評估。 [原因] 欄會顯示一個詞組，說明來賓指派為何「不符合規範」。 例如，若您要稽核密碼原則，[原因] 欄會顯示包含每項設定目前值的文字。

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="檢視合規性詳細資料" border="false":::

### <a name="azure-powershell"></a>Azure PowerShell

您也可以從 Azure PowerShell 檢視合規性詳細資料。 首先，請確定您已安裝「來賓設定」模組。

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

您可以使用下列命令，檢視 VM 的所有來賓指派的目前狀態：

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

若只要檢視說明 VM 為何「不符合規範」的「原因」詞組，則只會傳回 Reason 子屬性。

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

您也可以針對電腦範圍內的來賓指派，輸出合規性歷程記錄。 此命令的輸出包含 VM 的每份報告詳細資料。

> [!NOTE]
> 輸出可能會傳回大量資料。 建議您將輸出儲存在變數中。

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

若要簡化此檢視，請使用 **ShowChanged** 參數。 此命令的輸出只包含合規性狀態變更後的報告。

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="change-history-preview"></a><a name="change-history"/>變更歷程記錄 (預覽)

在新的**公開預覽**版中，過去 14 天的變更歷程記錄適用於所有支援[完整模式刪除](../../../azure-resource-manager/templates/complete-mode-deletion.md)的 Azure 資源。 變更歷程記錄會提供關於何時偵測到變更的詳細資料，以及每項變更的_視覺化差異_。 新增、移除或更改 Resource Manager 屬性時，就會觸發變更偵測。

1. 藉由按一下 [所有服務] 然後搜尋並選取 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

1. 在 [概觀] 或 [合規性] 頁面上，選取處於任何**合規性狀態**的原則。

1. 在 [原則合規性] 頁面的 [資源合規性] 索引標籤下方，選取資源。

1. 選取 [資源合規性] 頁面上的 [變更歷程記錄 (預覽)] 索引標籤。 偵測到的變更清單 (如果有的話) 會隨即顯示。

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="資源合規性頁面上的 Azure 原則變更歷程記錄索引標籤" border="false":::

1. 選取其中一個偵測到的變更。 資源會在 [變更歷程記錄] 頁面上顯示其「視覺化差異」。

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="變更歷程記錄頁面上的 Azure 原則變更歷程記錄視覺化差異" border="false":::

_視覺化差異_有助於識別資源的變更。 偵測到的變更可能與資源目前的合規性狀態不相關。

變更歷程記錄資料是由 [Azure Resource Graph](../../resource-graph/overview.md) 提供。 若要在 Azure 入口網站之外查詢此資訊，請參閱[取得資源變更](../../resource-graph/how-to/get-resource-changes.md)。

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 了解如何[以程式設計方式建立原則](programmatically-create.md)。
- 了解如何[取得合規性資料](get-compliance-data.md)。
- 了解如何[補救不符合規範的資源](remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
