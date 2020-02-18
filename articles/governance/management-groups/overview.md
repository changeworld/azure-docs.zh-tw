---
title: 使用管理群組來組織資源 - Azure Governance
description: 了解管理群組及權限如何運作，以及如何使用。
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.date: 12/18/2019
ms.topic: overview
ms.openlocfilehash: 319f48d4d0f8ce8501fecb74282760340b597188
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186992"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>使用 Azure 管理群組來組織資源

如果貴組織有多個訂用帳戶，您可能需要一個方法來有效率地管理這些訂用帳戶的存取、原則和相容性。 Azure 管理群組可以在訂用帳戶之上提供範圍層級。 您要將訂用帳戶整理到稱為「管理群組」的容器中，並將治理條件套用至管理群組。 管理群組內的所有訂用帳戶都會自動繼承套用到管理群組的條件。 無論具有何種類型的訂用帳戶，管理群組都可為您提供企業級的大規模管理功能。 單一管理群組中的所有訂用帳戶都必須信任相同的 Azure Active Directory 租用戶。

例如，您可以將原則套用至管理群組，以限制可建立虛擬機器 (VM) 的區域。 此原則會套用至所有的管理群組、訂用帳戶以及該管理群組下的資源，而僅允許在該區域中建立的 VM。

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>管理群組和訂用帳戶的階層

您可以建置管理群組和訂用帳戶的彈性結構，將資源組織到一個階層中，以便執行統一原則與存取管理。 下圖顯示使用管理群組建立治理階層的範例。

![管理群組階層樹狀結構範例](./media/tree.png)

您可以建立套用原則的階層，例如將名為「生產」群組中的 VM 位置限制為美國西部區域。 此原則將會繼承管理群組底下的所有 Enterprise 合約 (EA) 訂用帳戶，並套用至那些訂用帳戶底下的所有 VM。 此安全性原則無法由資源或訂用帳戶擁有者改變，並能進一步提升治理能力。

另一個案例是使用管理群組來向使用者提供多個訂用帳戶的存取權。 將多個訂用帳戶移至該管理群組底下，讓您能在管理群組上建立[角色型存取控制](../../role-based-access-control/overview.md) (RBAC) 指派；如此一來，所有訂用帳戶均能繼承該存取權。
只需要單獨對管理群組進行指派，使用者便能存取其所需要的所有內容，無需透過指令碼將 RBAC 指派給多個訂用帳戶。

### <a name="important-facts-about-management-groups"></a>關於管理群組的重要事實

- 單一目錄中可支援 10,000 個管理群組。
- 管理群組樹狀結構最多可支援六個層級的深度。
  - 此限制不包含根層級或訂用帳戶層級。
- 每個管理群組和訂用帳戶只能支援一個父系。
- 每個管理群組可以有多個子系。
- 所有訂用帳戶和管理群組都包含在每個目錄的單一階層中。 請參閱[關於根管理群組的重要事實](#important-facts-about-the-root-management-group)。

## <a name="root-management-group-for-each-directory"></a>每個目錄的根管理群組

每個目錄會都會有一個最上層管理群組，名為「根」管理群組。
這個根管理群組會建置於階層內，讓所有的管理群組和訂用帳戶摺疊於其中。 這個根管理群組可讓全域原則和 RBAC 指派在目錄層級套用。 [Azure AD 全域管理員必須提高本身的權限](../../role-based-access-control/elevate-access-global-admin.md)，以成為此根群組最初的使用者存取管理員角色。 提高存取權限後，管理員即可將任何 RBAC 角色指派給其他目錄使用者或群組，以管理階層。 身為管理員，您可以將自己的帳戶指派為根管理群組的擁有者。

### <a name="important-facts-about-the-root-management-group"></a>關於根管理群組的重要事實

- 依預設，根管理群組的顯示名稱為**租用戶根群組**。 識別碼則是 Azure Active Directory 識別碼。
- 若要變更顯示名稱，必須在根管理群組中指派帳戶擁有者或參與者角色。 請參閱[變更管理群組的名稱](manage.md#change-the-name-of-a-management-group)，以更新管理群組的名稱。
- 根管理群組不同於其他管理群組，無法移動或刪除。  
- 所有的訂用帳戶和管理群組可摺疊到目錄內的一個根管理群組中。
  - 目錄中的所有資源可摺疊到根管理群組，以便進行全域管理。
  - 建立新的訂用帳戶時，會自動預設為根管理群組。
- 所有 Azure 客戶都可以看到根管理群組，但並非所有的客戶都有權管理該根管理群組。
  - 有訂用帳戶存取權的所有人，都可以查看訂用帳戶在階層中的上下內容。  
  - 根管理群組的預設存取權不會授予任何人。 Azure AD 全域管理員是唯一可以提升自己以取得存取權的使用者。  一旦全域管理員擁有根管理群組存取權之後，就可以將任何 RBAC 角色指派給其他使用者以進行管理。  

> [!IMPORTANT]
> 根管理群組上的任何使用者存取權指派或原則指派，都會**套用至目錄中的所有資源**。
> 因此，所有客戶都應評估是否需要在此範圍內定義項目。
> 在這個範圍內，只應指派「必要」的使用者存取權和原則。  

## <a name="initial-setup-of-management-groups"></a>管理群組的初始設定

當任何使用者開始使用管理群組時，會發生初始的設定程序。 第一個步驟是在目錄中建立根管理群組。 建立此群組後，所有存在於目錄中的現有訂用帳戶都將成為根管理群組的子系。 此程序的原因是要確定目錄中只有一個管理群組階層。 目錄內的單一階層可讓系統管理客戶得以套用全域存取權和原則，讓目錄內的其他客戶無法略過。 在根目錄上指派的任何項目都將套用至整個階層當中，其中包括 Azure AD 租用戶中所有的管理群組、訂用帳戶、資源群組和資源。

## <a name="trouble-seeing-all-subscriptions"></a>看不到所有訂用帳戶

若目錄在 2018 年 6 月 25 日 之前已在早期預覽版本中開始使用管理群組，則您可能會發現並非所有訂用帳戶都在階層內。 系統會在目錄中的根管理群組中將角色或原則指派完成之後，才會進行將訂用帳戶排定至階層的流程。 

### <a name="how-to-resolve-the-issue"></a>如何解決此問題

您有兩種方法可以解決此問題。

1. 從根管理群組中移除所有角色和原則指派
   1. 從根管理群組中移除所有原則和角色指派之後，服務會將所有訂用帳戶回填至下一個隔日工作週期階層。  此流程是為了確保並未意外地將存取權或原則指派提供給所有租用戶訂用帳戶。
   1. 要在不影響您服務的情況下進行此流程，最好的方法是將角色或原則指派套用至比根管理群組低一階的層級。 如此一來，您就可以從根範圍中移除所有指派。
1. 直接呼叫 API 以啟動回填流程
   1. 目錄中的所有客戶都可以呼叫 *TenantBackfillStatusRequest* 或 *StartTenantBackfillRequest* API。 呼叫了 StartTenantBackfillRequest API 之後，系統就會開始初步的設定流程，將所有訂用帳戶移動至該階層。 此流程也會強制將所有新的訂用帳戶均設為根管理群組的子系。 您可以在未變更根層級上任何指派的情況下完成此程序。 您可以呼叫 API，表示根的任何原則或存取指派都能套用至所有訂用帳戶。

若您對於此回填流程有任何問題，請連絡：managementgroups@microsoft.com  
  
## <a name="management-group-access"></a>管理群組存取

Azure 管理群組支援對所有的資源存取和角色定義使用 [Azure 角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)。
這些權限會被存在於階層中的子資源繼承。 可以將任何 RBAC 角色指派至繼承階層直到資源的管理群組。
例如，可以將 RBAC 角色 VM 參與者指派至管理群組。 此角色對管理群組沒有任何作用，但會繼承該管理群組下的所有 VM。

下圖顯示了角色清單與管理群組上支援的動作。

| RBAC 角色名稱             | 建立 | 重新命名 | 移動** | 刪除 | 指派存取權 | 指派原則 | 讀取  |
|:-------------------------- |:------:|:------:|:------:|:------:|:-------------:| :------------:|:-----:|
|擁有者                       | X      | X      | X      | X      | X             | X             | X     |
|參與者                 | X      | X      | X      | X      |               |               | X     |
|MG 參與者*             | X      | X      | X      | X      |               |               | X     |
|讀取者                      |        |        |        |        |               |               | X     |
|MG 讀取者*                  |        |        |        |        |               |               | X     |
|資源原則參與者 |        |        |        |        |               | X             |       |
|使用者存取系統管理員   |        |        |        |        | X             | X             |       |

*：MG 參與者和 MG 讀取者僅允許使用者執行管理群組範圍的動作。  
**：在根管理群組上的角色指派不需要將訂用帳戶或管理群組來回移動。  如需在階層中移動項目的詳細資訊，請參閱[使用管理群組管理您的資源](manage.md)。

## <a name="custom-rbac-role-definition-and-assignment"></a>自訂 RBAC 角色定義和指派

管理群組的自訂 RBAC 角色支援目前為預覽版，但有一些 [限制](#limitations)。  您可以在角色定義的可指派範圍中定義管理群組範圍。  接著，該自訂 RBAC 角色即可在該管理群組，以及任何管理群組、訂用帳戶、資源群組或其下的資源上指派。 這個自訂角色會繼承階層，就像任何內建角色一樣。    

### <a name="example-definition"></a>範例定義
[定義和建立自訂角色](../../role-based-access-control/custom-roles.md)並不會隨著管理群組的納入而變更。 使用完整路徑來定義管理群組 **/providers/Microsoft.Management/managementgroups/{groupId}** 。 

使用管理群組的識別碼，而不是管理群組的顯示名稱。 這個常見錯誤的發生原因是，在建立管理群組時兩者都是自訂定義的欄位。 

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>中斷角色定義和指派階層路徑時發生的問題
角色定義是管理群組階層內的可指派範圍。 角色定義可定義於父管理群組上，而實際的角色指派存在於子訂用帳戶上。 因為這兩個項目之間有關聯性，所以當您嘗試分隔指派與其定義時，將會收到錯誤。 

例如：讓我們查看一小部分的視覺效果階層。 

![子樹狀結構](./media/subtree.png)

假設行銷管理群組上有定義的自訂角色。 接著，該自訂角色會在兩個免費試用訂用帳戶上指派。  

如果我們嘗試將其中一個訂用帳戶移至生產管理群組的子系，此動會將中斷從訂用帳戶角色指派到行銷管理群組角色定義的路徑。 在此種情況中，您會收到錯誤，指出因為移動會中斷此關聯性而不允許移動。  

有幾個不同的選項可修正這種情況：
- 將訂用帳戶移至新的父系 MG 之前，請先從訂用帳戶中移除角色指派。
- 將訂用帳戶新增至角色定義的可指派範圍。
- 變更角色定義內的可指派範圍。 在上述範例中，您可以將可指派的範圍從 [行銷] 更新為 [根管理群組]，讓階層的兩個分支都可以觸達該定義。   
- 建立將在另一個分支中定義的其他自訂角色。  這個新角色會要求您也要在訂用帳戶上變更角色指派。  

### <a name="limitations"></a>限制  
在管理群組上使用自訂角色時，有一些限制存在。 

 - 您只能在新角色的可指派範圍中定義一個管理群組。  這項限制是為了減少角色定義和角色指派中斷連線的情況。  當具有角色指派的訂用帳戶或管理群組移至沒有角色定義的不同父系時，就會發生這種情況。   
 - 不允許在管理群組自訂角色中定義 RBAC 資料平面動作。  有這項限制是因為 RBAC 動作更新資料平面資源提供者時會發生延遲問題。 此延遲問題正在處理中，而且將從角色定義停用這些動作，以降低任何風險。
 - Azure Resource Manager 不會驗證角色定義的可指派範圍中是否存在管理群組。  如果有錯字或列出了不正確的管理群組識別碼，則仍會建立角色定義。   

## <a name="moving-management-groups-and-subscriptions"></a>移動管理群組和訂用帳戶 

若要將管理群組或訂用帳戶作為另一個管理群組的子系，則有三個規則必須評估為 true。

如果您要執行移動動作，您需要： 

-  子訂用帳戶或管理群組上的管理群組寫入和角色指派寫入權限。
   - 內建角色範例**擁有者**
- 目標父管理群組上的管理群組寫入權限。
   - 內建角色範例：**擁有者**、**參與者**、**管理群組參與者**
- 現有父管理群組上的管理群組寫入權限。
   - 內建角色範例：**擁有者**、**參與者**、**管理群組參與者**

**例外狀況**：如果目標或現有父管理群組是根管理群組，則不適用權限需求。 因為根管理群組是所有新管理群組和訂用帳戶的預設登陸點，所以您不需要其權限即可移動項目。

如果訂用帳戶上的擁有者角色繼承自目前的管理群組，則您的移動目標會受到限制。 您只能將訂用帳戶移至具有擁有者角色的另一個管理群組。 因為您會失去訂用帳戶的擁有權，所以無法將其移至您是參與者的管理群組。 如果直接將您指派給訂用帳戶的擁有者角色 (而不是繼承自管理群組)，您可以將其移至您是參與者的任何管理群組。 

## <a name="audit-management-groups-using-activity-logs"></a>使用活動記錄稽核管理群組

[Azure 活動記錄](../../azure-monitor/platform/platform-logs-overview.md)中支援管理群組。 在與其他 Azure 資源位於相同中央位置的管理群組中，您可以搜尋其中發生的所有事件。  例如，您可以看到對特定的管理群組的所有角色指派或原則指派變更。

![使用管理群組的活動記錄](media/al-mg.png)

在 Azure 入口網站外部查詢管理群組時，管理群組的目標範圍像是 **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** 。

## <a name="next-steps"></a>後續步驟

若要深入了解管理群組，請參閱：

- [建立管理群組以組織 Azure 資源](create.md)
- [如何變更、刪除或管理您的管理群組](manage.md)
- [檢閱 Azure PowerShell 資源模組中的管理群組](/powershell/module/az.resources#resources)
- [檢閱 REST API 中的管理群組](/rest/api/resources/managementgroups)
- [檢閱 Azure CLI 中的管理群組](/cli/azure/account/management-group)
