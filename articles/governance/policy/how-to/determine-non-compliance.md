---
title: 判斷不符合的原因
description: 當資源不合規時,有許多可能的原因。 瞭解如何找出導致不合規的原因。
ms.date: 04/26/2019
ms.topic: how-to
ms.openlocfilehash: c931831ddf3cc727b9861e75969eac3bf00c9e45
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "79264631"
---
# <a name="determine-causes-of-non-compliance"></a>判斷不符合的原因

當 Azure 資源被確定為不符合策略規則時,瞭解資源不符合規則的哪一部分會很有説明。 瞭解更改更改以前相容的資源以使其不合規也很有用。 有兩種方法可以查找此資訊:

> [!div class="checklist"]
> - [合規性詳細資訊](#compliance-details)
> - [變更歷程記錄 (預覽)](#change-history)

## <a name="compliance-details"></a>合規性詳細資訊

當資源不符合時,可從 **「策略合規性」** 頁獲得該資源的合規性詳細資訊。 合規性詳細資訊窗格包括以下資訊:

- 資源詳細資訊,如名稱、類型、位置和資源識別碼
- 目前策略配置上次評估的合規性狀態和時間戳
- 資源不合規_的原因_清單

> [!IMPORTANT]
> 由於_不合規_資源的符合性詳細資訊顯示該資源上屬性的當前值,因此用戶必須對資源**類型**具有**讀取**操作。 例如,如果_不符合要求_的資源是**Microsoft.Compute/虛擬機器**,則使用者必須具有**Microsoft.計算/ 虛擬機器 /讀取**操作。 如果用戶沒有所需的操作,將顯示訪問錯誤。

要查看合規性詳細資訊,請按照以下步驟操作:

1. 藉由按一下 [所有服務]**** 然後搜尋並選取 [原則]****，在 Azure 入口網站中啟動 Azure 原則服務。

1. 在 **「概述**」或「**合規性」** 頁上,選擇處於_不符合合規性_**狀態**的策略。

1. 在 **"策略合規性**"頁的 **"資源合規性**"選項卡下,右鍵單擊或選擇_處於不符合_的**合規性狀態**的資源的省略號。 然後選擇 **「查看合規性詳細資訊**」。

   ![檢視合規性詳細資訊選項](../media/determine-non-compliance/view-compliance-details.png)

1. "**合規性詳細資訊**"窗格顯示從資源的最新評估到當前策略分配的資訊。 這個選項,發現字段**Microsoft.Sql/伺服器/版本**為_12.0,_ 而策略定義預期_為 14.0_。 如果由於多種原因資源不符合要求,則每個資源都列在此窗格中。

   ![合規性詳細資訊窗格和不合規的原因](../media/determine-non-compliance/compliance-details-pane.png)

   對於**審核IfNot存在**或**部署存在**策略定義,詳細資訊包括**詳細資訊.type**屬性和任何可選屬性。 有關清單,請參閱[審核IfNot存在屬性](../concepts/effects.md#auditifnotexists-properties)和[部署"不存在"屬性](../concepts/effects.md#deployifnotexists-properties)。 **上次評估的資源**是定義**詳細資訊**部分的相關資源。

   範例部分**部署非存在**定義:

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

   ![合規性詳細資訊窗格 - \if 不存在](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> 為了保護數據,當屬性值為_機密_時,當前值將顯示星號。

這些詳細資訊解釋了為什麼資源當前不合規,但不顯示何時對資源進行更改,導致資源變得不合規。 有關該資訊,請參閱下面的[更改歷史記錄(預覽)。](#change-history)

### <a name="compliance-reasons"></a>合規原因

以下矩陣會每個可能_的原因_對應到策略定義的[設定 條件](../concepts/definition-structure.md#conditions):

|原因 | 條件 |
|-|-|
|當前值必須作為鍵包含目標值。 |包含**金鑰或不包含**金鑰 |
|當前值必須包含目標值。 |包含**或不包含** |
|當前值必須等於目標值。 |等於**或不**等於 |
|當前值必須小於目標值。 |較少或**不**更大 或等於 |
|當前值必須大於或等於目標值。 |更大或等於**或不**減少 |
|當前值必須大於目標值。 |更大或**不是**更少或等於 |
|當前值必須小於或等於目標值。 |不**等於或不更大** |
|當前值必須存在。 |exists |
|當前值必須位於目標值中。 |在**或不**在 |
|當前值必須與目標值類似。 |喜歡或不**喜歡** |
|當前值必須區分大小寫與目標值匹配。 |符合與否**符合** |
|當前值必須不區分大小寫與目標值匹配。 |符合不**敏感或不符合**不敏感 |
|當前值不能將目標值作為鍵包含。 |不要包含金鑰或**不包含**金鑰|
|當前值不能包含目標值。 |不包含或**不含** |
|當前值不能等於目標值。 |不**等於或不等於** |
|當前值必須不存在。 |**不存在**  |
|當前值不能位於目標值中。 |不在或**不在** |
|當前值不能與目標值類似。 |不喜歡或**不喜歡** |
|當前值不能區分大小寫與目標值匹配。 |不符合或**不符合** |
|當前值不能不區分大小寫與目標值匹配。 |不符合**敏感或不符合**不敏感 |
|沒有任何相關資源與策略定義中的效果詳細資訊匹配。 |**在****if** |

## <a name="compliance-details-for-guest-configuration"></a>來賓配置的合規性詳細資訊

對於「_來賓設定_」類別中的_審核IfNot存在_策略,VM 內部可能有多個設置,您需要查看每個設置的詳細資訊。 例如,如果您正在審核密碼策略列表,並且其中只有一個策略的狀態_不符合要求_,則需要知道哪些特定的密碼策略不符合要求以及原因。

您可能還無法直接登錄到 VM,但您需要報告 VM_不符合_的原因。

### <a name="azure-portal"></a>Azure 入口網站

首先,請按照上述部分中的相同步驟查看策略合規性詳細資訊。

在 **「合規性詳細資訊」** 窗格檢視中按一下連結 **「上次評估的資源**」。

   ![檢視稽核不存在定義詳細資訊](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

來賓**分配**頁顯示所有可用的合規性詳細資訊。 視圖中的每一行都表示在計算機內執行的評估。 在 **「原因」** 列中,將顯示一個短語,說明來賓分配_不符合_的原因。 例如,如果要審核密碼策略,**則「原因」** 列將顯示包含每個設置的當前值的文本。

![檢視合規性詳細資訊](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

您還可以從 Azure PowerShell 查看合規性詳細資訊。 首先,請確保安裝了來賓配置模組。

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

您可以使用以下指令檢視 VM 的所有來賓分配的目前狀態:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

要僅查看描述 VM_不合規__原因的原因_短語,應僅返回"原因"子屬性。

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

您還可以在計算機範圍內為來賓分配輸出符合性歷史記錄。 此命令的輸出包括 VM 的每個報表的詳細資訊。

> [!NOTE]
> 輸出可能會返回大量數據。 建議將輸出存儲在變數中。

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

要簡化此檢視,請使用 **「顯示更改」** 參數。 此命令的輸出僅包括合規性狀態更改後的報告。

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

作為新**公共預覽**的一部分,支援[完全模式刪除](../../../azure-resource-manager/templates/complete-mode-deletion.md)的所有 Azure 資源都可以使用更改歷史記錄的最後 14 天。 變更歷程記錄會提供關於何時偵測到變更的詳細資料，以及每項變更的_視覺化差異_。 添加、刪除或更改資源管理器屬性時,將觸發更改檢測。

1. 藉由按一下 [所有服務]**** 然後搜尋並選取 [原則]****，在 Azure 入口網站中啟動 Azure 原則服務。

1. 在 **「概述**」或「**合規性」** 頁上,選擇處於任何**合規性狀態**的策略。

1. 在 **「策略合規性**」頁的 **「資源合規性**」選項卡下,選擇資源。

1. 選取 [資源合規性]**** 頁面上的 [變更歷程記錄 (預覽)]**** 索引標籤。 偵測到的變更清單 (如果有的話) 會隨即顯示。

   ![Azure 政策更改歷史記錄選項卡位於資源合規性頁上](../media/determine-non-compliance/change-history-tab.png)

1. 選取其中一個偵測到的變更。 資源的_視覺差異_顯示在 **「更改歷史記錄**」頁上。

   ![Azure 政策變更變更紀錄記錄在變更歷史記錄頁上的視覺差異](../media/determine-non-compliance/change-history-visual-diff.png)

_視覺化差異_有助於識別資源的變更。 檢測到的更改可能與資源的當前符合性狀態無關。

更改歷史記錄數據由[Azure 資源圖](../../resource-graph/overview.md)提供。 要在 Azure 入口外時查詢此資訊,請參考[資源變更](../../resource-graph/how-to/get-resource-changes.md)。

## <a name="next-steps"></a>後續步驟

- 查看[Azure 策略範例](../samples/index.md)中的範例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 瞭解如何[以程式設計方式建立原則](programmatically-create.md)。
- 瞭解如何[取得合規性資料](get-compliance-data.md)。
- 瞭解如何[修復不合規資源](remediate-resources.md)。
- 使用[Azure 管理組查看](../../management-groups/overview.md)管理組的內容。請組織資源。
