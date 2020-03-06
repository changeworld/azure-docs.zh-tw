---
title: 判斷不符合的原因
description: 當資源不符合規範時，有許多可能的原因。 學習如何找出導致不符合規範的原因。
ms.date: 04/26/2019
ms.topic: how-to
ms.openlocfilehash: c931831ddf3cc727b9861e75969eac3bf00c9e45
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385222"
---
# <a name="determine-causes-of-non-compliance"></a>判斷不符合的原因

當 Azure 資源判斷為不符合原則規則時，瞭解該資源不符合規則的哪個部分會很有説明。 這也有助於瞭解哪些變更改變了先前符合規範的資源，使其不符合規範。 有兩種方式可以尋找這份資訊：

> [!div class="checklist"]
> - [合規性詳細資料](#compliance-details)
> - [變更歷程記錄（預覽）](#change-history)

## <a name="compliance-details"></a>合規性詳細資料

當資源不符合規範時，可以從 [**原則相容性**] 頁面取得該資源的相容性詳細資料。 [合規性詳細資料] 窗格包含下列資訊：

- 資源詳細資料，例如名稱、類型、位置和資源識別碼
- 目前原則指派上次評估的合規性狀態和時間戳記
- 資源不符合規範的_原因_清單

> [!IMPORTANT]
> 因為_不符合規範_之資源的相容性詳細資料會顯示該資源上屬性的目前值，所以使用者必須具有資源**類型**的**讀取**作業。 例如，如果_不符合規範_的資源是**microsoft** ，則使用者必須擁有**microsoft. compute/virtualMachines/read**作業。 如果使用者沒有所需的作業，則會顯示存取錯誤。

若要查看合規性詳細資料，請遵循下列步驟：

1. 藉由按一下 [所有服務] 然後搜尋並選取 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

1. 在 [**總覽**] 或 [**相容性**] 頁面上，選取**合規性狀態**中不_符合規範_的原則。

1. 在 [**原則相容性**] 頁面的 [**資源相容性**] 索引標籤底下，以滑鼠右鍵按一下或選取**合規性狀態**中不_符合規範_的資源的省略號。 然後選取 [ **View 合規性詳細資料**]。

   ![[查看相容性詳細資料] 選項](../media/determine-non-compliance/view-compliance-details.png)

1. [**相容性詳細資料**] 窗格會顯示從資源的最新評估到目前原則指派的資訊。 在此範例中，當原則定義預期為_14.0_時，會發現**Microsoft .sql/servers/version**欄位為_12.0_ 。 如果資源因多種原因而不符合規範，則每個都列在此窗格中。

   ![[合規性詳細資料] 窗格和不符合規範的原因](../media/determine-non-compliance/compliance-details-pane.png)

   針對**auditIfNotExists**或**deployIfNotExists**原則定義，詳細資料包括**詳細資料。 type**屬性和任何選擇性的屬性。 如需清單，請參閱[auditIfNotExists 屬性](../concepts/effects.md#auditifnotexists-properties)和[deployIfNotExists 屬性](../concepts/effects.md#deployifnotexists-properties)。 **上次評估的資源**是來自訂之 [**詳細資料**] 區段中的相關資源。

   範例部分**deployIfNotExists**定義：

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

   ![合規性詳細資料窗格-* ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> 若要保護資料，當屬性值為_秘密_時，目前的值會顯示星號。

這些詳細資料會說明資源目前不符合規範的原因，但不會顯示何時對資源進行變更，使其變成不符合規範。 如需該資訊，請參閱下方的[變更歷程記錄（預覽）](#change-history) 。

### <a name="compliance-reasons"></a>合規性理由

下列矩陣會將每個可能的_原因_對應至原則定義中的負責[條件](../concepts/definition-structure.md#conditions)：

|原因 | 條件 |
|-|-|
|目前的值必須包含目標值做為索引鍵。 |containsKey 或**not** notContainsKey |
|目前的值必須包含目標值。 |包含或**不**notContains |
|目前的值必須等於目標值。 |equals 或**not** notEquals |
|目前的值必須小於目標值。 |less 或**not** greaterOrEquals |
|目前的值必須大於或等於目標值。 |greaterOrEquals 或**不**小於 |
|目前的值必須大於目標值。 |大於或**不**lessOrEquals |
|目前的值必須小於或等於目標值。 |lessOrEquals 或**不**大於 |
|目前的值必須存在。 |exists |
|目前的值必須在目標值中。 |in 或**not** notIn |
|目前的值必須與目標值相同。 |like 或**not** notLike |
|目前的值必須符合目標值的區分大小寫。 |match 或**not** notMatch |
|目前的值必須與目標值不區分大小寫。 |matchInsensitively 或**not** notMatchInsensitively |
|目前的值不得包含目標值做為索引鍵。 |notContainsKey 或**not** containsKey|
|目前的值不得包含目標值。 |notContains 或**不**包含 |
|目前的值不得等於目標值。 |notEquals 或**不**等於 |
|目前的值不能存在。 |**不**存在  |
|目前的值不得在目標值中。 |notIn 或**不**在 |
|目前的值不得與目標值相同。 |notLike 或**不**喜歡 |
|目前的值不得符合目標值的區分大小寫。 |notMatch 或**不**相符 |
|目前的值不得與目標值不區分大小寫。 |notMatchInsensitively 或**not** matchInsensitively |
|沒有任何相關資源符合原則定義中的效果詳細資料。 |Then 中所定義之類型的資源 **。詳細資料. 類型**，以及與原則規則的**if**部分中所定義的資源相關的資訊並不存在。 |

## <a name="compliance-details-for-guest-configuration"></a>來賓設定的相容性詳細資料

對於 [_來賓_設定] 類別中的_auditIfNotExists_原則，可能會在 VM 內評估多個設定，而且您必須查看每個設定的詳細資料。 例如，如果您正在審核密碼原則清單，而且只有其中一個狀態_不符合規範_，您必須知道哪些特定的密碼原則不符合規範，以及原因為何。

您也可能沒有直接登入 VM 的存取權，但您必須回報 VM 不_符合規範_的原因。

### <a name="azure-portal"></a>Azure 入口網站

一開始請遵循上一節中的相同步驟，以查看原則相容性詳細資料。

在 [**相容性詳細資料**] 窗格中，按一下 [**上次評估的資源**] 連結。

   ![View auditIfNotExists 定義詳細資料](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

[**來賓指派**] 頁面會顯示所有可用的合規性詳細資料。 View 中的每個資料列都代表在機器內執行的評估。 在 [**原因**] 資料行中，會顯示說明為何不_符合_「來賓指派」的片語。 例如，如果您要審核密碼原則，[**原因**] 欄會顯示文字，包括每個設定的目前值。

![查看合規性詳細資料](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

您也可以從 Azure PowerShell 查看合規性詳細資料。 首先，請確定您已安裝「來賓設定」模組。

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

您可以使用下列命令來查看 VM 的所有來賓指派的目前狀態：

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

若只要查看描述 VM 不_符合規範_的_原因_片語，只會傳回 reason 子屬性。

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

您也可以針對電腦範圍內的來賓指派，輸出合規性歷程記錄。 此命令的輸出包含 VM 每個報告的詳細資料。

> [!NOTE]
> 輸出可能會傳回大量的資料。 建議您將輸出儲存在變數中。

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

若要簡化此視圖，請使用**ShowChanged**參數。 此命令的輸出只會包含遵循合規性狀態變更的報告。

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

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>變更歷程記錄（預覽）

做為新**公開預覽**的一部分，所有支援[完整模式刪除](../../../azure-resource-manager/templates/complete-mode-deletion.md)的 Azure 資源都可使用變更歷程記錄的過去14天。 變更歷程記錄會提供關於何時偵測到變更的詳細資料，以及每項變更的_視覺化差異_。 新增、移除或更改 Resource Manager 的屬性時，就會觸發變更偵測。

1. 藉由按一下 [所有服務] 然後搜尋並選取 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

1. 在 [**總覽**] 或 [**相容性**] 頁面上，選取任何**合規性狀態**中的原則。

1. 在 [**原則合規性**] 頁面的 [**資源合規性**] 索引標籤底下，選取資源。

1. 選取 [資源合規性] 頁面上的 [變更歷程記錄 (預覽)] 索引標籤。 偵測到的變更清單 (如果有的話) 會隨即顯示。

   ![資源合規性頁面上的 Azure 原則變更歷程記錄 索引標籤](../media/determine-non-compliance/change-history-tab.png)

1. 選取其中一個偵測到的變更。 資源的_視覺差異_會顯示在 [變更歷程**記錄**] 頁面上。

   ![在變更歷程記錄頁面上 Azure 原則變更歷程記錄視覺差異](../media/determine-non-compliance/change-history-visual-diff.png)

_視覺化差異_有助於識別資源的變更。 偵測到的變更可能與資源目前的合規性狀態不相關。

變更歷程記錄資料是由[Azure Resource Graph](../../resource-graph/overview.md)所提供。 若要在 Azure 入口網站之外查詢這項資訊，請參閱[取得資源變更](../../resource-graph/how-to/get-resource-changes.md)。

## <a name="next-steps"></a>後續步驟

- 如[Azure 原則範例](../samples/index.md)，請參閱範例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 瞭解如何以程式設計[方式建立原則](programmatically-create.md)。
- 瞭解如何[取得合規性資料](get-compliance-data.md)。
- 瞭解如何[補救不符合規範的資源](remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
