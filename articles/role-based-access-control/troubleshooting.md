---
title: 排除 Azure RBAC 故障
description: 使用 Azure 基於角色的存取控制 （Azure RBAC） 解決問題。
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 8aedc78772858815a18425fb1e6cb36a4600f647
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385090"
---
# <a name="troubleshoot-azure-rbac"></a>排除 Azure RBAC 故障

本文回答了有關基於 Azure 角色的存取控制 （Azure RBAC） 的一些常見問題，以便了解使用角色時會發生什麼，並可以排除訪問問題。

## <a name="azure-role-assignments-limit"></a>Azure 角色指派限制

Azure 支援每個訂用帳戶最多 **2000** 個角色指派。 如果在嘗試分配角色時收到錯誤訊息"無法創建更多角色指派（代碼：角色指派限制超過）"，請嘗試減少訂閱中的角色指派數。

> [!NOTE]
> 每個訂閱的**2000**角色指派限制已固定，無法增加。

如果您接近此限制，以下是減少角色指派數的一些方法：

- 將使用者添加到組，並將角色指派給組。 
- 將多個內置角色與自訂角色合併。 
- 在較高範圍內（如訂閱或管理組）進行常見角色指派。
- 如果您有 Azure AD 高級 P2，則使角色指派符合[Azure AD 特權標識管理](../active-directory/privileged-identity-management/pim-configure.md)的條件，而不是永久分配。 
- 添加其他訂閱。 

要獲取角色指派數，可以在 Azure 門戶[中的"存取控制 （IAM）"頁上查看圖表](role-assignments-list-portal.md#list-number-of-role-assignments)。 您還可以使用以下 Azure PowerShell 命令：

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Azure 角色指派問題

- 如果無法在**訪問控制項 （IAM）** 上的 Azure 門戶中添加角色指派，因為**Add** > "**添加添加角色指派**"選項已禁用，或者因為獲取許可權錯誤"具有物件識別碼 的用戶端沒有執行操作的許可權"，請檢查您當前是否當前與已分配的角色（如[擁有者](built-in-roles.md#owner)或[使用者訪問管理員](built-in-roles.md#user-access-administrator)）許可權`Microsoft.Authorization/roleAssignments/write`的使用者登錄，該使用者正在嘗試分配角色。

## <a name="problems-with-custom-roles"></a>自訂角色的問題

- 如果需要如何創建自訂角色的步驟，請參閱使用[Azure 門戶](custom-roles-portal.md)（當前預覽版[）、Azure PowerShell](tutorial-custom-role-powershell.md)或[Azure CLI](tutorial-custom-role-cli.md)的自訂角色教程。
- 如果無法更新現有自訂角色，請檢查您當前是否與已分配了具有`Microsoft.Authorization/roleDefinition/write`許可權（如[擁有者](built-in-roles.md#owner)或[使用者訪問管理員](built-in-roles.md#user-access-administrator)）的角色的使用者登錄。
- 如果無法刪除自訂角色並獲取錯誤訊息"存在引用角色的現有角色指派（代碼：角色定義任務）"，則仍有角色指派使用自訂角色。 請移除這些角色指派，並試著再次刪除自訂角色。
- 如果您收到「已超過角色定義限制」錯誤訊息。 當您嘗試創建新的自訂角色時，無法創建更多角色定義（代碼：角色定義限制超過）"，刪除未使用的任何自訂角色。 Azure 支援目錄中最多**5000**個自訂角色。 （對於 Azure 德國和 Azure 中國 21Vianet，限制為 2000 個自訂角色。
- 如果在嘗試更新自訂角色時收到類似于"用戶端有權對作用域"/訂閱/訂閱/訂閱 id"執行操作"Microsoft.授權/角色定義/寫入"類似的錯誤，請檢查目錄中是否刪除了一個或多個[可分配作用域](role-definitions.md#assignablescopes)。 如果範圍已刪除，則可以建立支援票證，因為此時已沒有可用的自助解決方案。

## <a name="custom-roles-and-management-groups"></a>自訂角色和管理組

- 只能在自訂角色中`AssignableScopes`定義一個管理組。 將管理組添加到`AssignableScopes`當前處於預覽狀態。
- 無法在管理`DataActions`組作用域中分配 具有的自訂角色。
- Azure 資源管理器不驗證管理組在角色定義的可分配作用域中是否存在。
- 有關自訂角色和管理組的詳細資訊，請參閱[使用 Azure 管理組組織資源](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)。

## <a name="transferring-a-subscription-to-a-different-directory"></a>將訂閱傳輸到其他目錄

- 如果需要如何將訂閱轉移到其他 Azure AD 目錄的步驟，請參閱[將 Azure 訂閱的擁有權轉移到其他帳戶](../cost-management-billing/manage/billing-subscription-transfer.md)。
- 如果將訂閱轉移到其他 Azure AD 目錄，則所有角色指派都將從源 Azure AD 目錄**永久**刪除，並且不會遷移到目標 Azure AD 目錄。 您必須在目標目錄中重新創建角色指派。 您還必須手動為 Azure 資源重新創建託管標識。 有關詳細資訊，請參閱[常見問題解答和託管標識的已知問題](../active-directory/managed-identities-azure-resources/known-issues.md)。
- 如果您是 Azure AD 全域管理員，並且在訂閱在目錄之間傳輸後無法訪問該訂閱，請使用**Azure 資源的"訪問"管理**切換以臨時[提升存取權限](elevate-access-global-admin.md)以訪問訂閱。

## <a name="issues-with-service-admins-or-co-admins"></a>服務管理員或共同管理員的問題

- 如果服務管理員或共同管理員遇到問題，請參閱[添加或更改 Azure 訂閱管理員](../cost-management-billing/manage/add-change-subscription-administrator.md)和[經典訂閱管理員角色、Azure 角色和 Azure AD 管理員角色](rbac-and-directory-admin-roles.md)。

## <a name="access-denied-or-permission-errors"></a>訪問被拒絕或許可權錯誤

- 如果在嘗試創建資源時收到許可權錯誤"具有物件識別碼 的用戶端無權執行範圍操作（代碼：授權失敗）"，請檢查您當前是否當前與已分配了已寫入的角色的使用者登錄對所選作用域中的資源的許可權。 例如，若要管理資源群組中的虛擬機器，您應該具有資源群組 (或父範圍) 的[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)角色。 如需每個內建角色的權限清單，請參閱 [Azure 資源的內建角色](built-in-roles.md)。
- 如果在嘗試創建或更新支援票證時收到許可權錯誤"您沒有創建支援請求的許可權"，請檢查您當前是否當前與已分配了具有`Microsoft.Support/supportTickets/write`該許可權的角色（如[支援請求參與者](built-in-roles.md#support-request-contributor)）的使用者登錄。

## <a name="role-assignments-with-unknown-security-principal"></a>具有未知安全主體的角色指派

如果將角色指派給安全主體（使用者、組、服務主體或託管標識），然後以後在不刪除角色指派的情況下刪除該安全主體，則角色指派的安全主體類型將列為**未知**。 下列螢幕擷取畫面顯示 Azure 入口網站中的範例。 安全主體名稱列為**已刪除的身份**，**標識不再存在**。 

![Web 應用程式資源群組](./media/troubleshooting/unknown-security-principal.png)

如果使用 Azure PowerShell 列出此角色指派，您將看到一個空`DisplayName`角色指派`ObjectType`，並且將看到一個設置為"未知"。。" 例如[，Get-AzRole 分配](/powershell/module/az.resources/get-azroleassignment)返回類似于以下內容的角色指派：

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

同樣，如果使用 Azure CLI 列出此角色指派，您將看到空`principalName`。 例如[，az 角色指派清單](/cli/azure/role/assignment#az-role-assignment-list)返回類似于以下內容的角色指派：

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

離開這些角色指派不是問題，但您可以使用類似于其他角色指派的步驟刪除它們。 有關如何刪除角色指派的資訊，請參閱 Azure[門戶](role-assignments-portal.md#remove-a-role-assignment)[、Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)或[Azure CLI](role-assignments-cli.md#remove-a-role-assignment)

在 PowerShell 中，如果您嘗試使用物件識別碼 和角色定義名稱刪除角色指派，並且多個角色指派與您的參數匹配，則會收到錯誤訊息："提供的資訊不映射到角色指派"。 下面顯示了錯誤訊息的示例：

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

如果收到此錯誤訊息，請確保也指定 或`-Scope``-ResourceGroupName`參數。

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>未檢測到角色指派更改

Azure Resource Manager 有時候會快取組態和資料來改善效能。 添加或刪除角色指派時，更改最多可能需要 30 分鐘才能生效。 如果您使用 Azure 入口網站、Azure PowerShell 或 Azure CLI，您可以藉由登出再登入，來強制重新整理角色指派變更。 如果您使用 REST API 呼叫來變更角色指派，您可以重新整理存取權杖來強制重新整理。

如果要在管理組作用域中添加或刪除角色指派，並且角色具有`DataActions`，則資料平面上的存取權限可能無法更新幾個小時。 這僅適用于管理組作用域和資料平面。

## <a name="web-app-features-that-require-write-access"></a>需要寫入存取權的 Web 應用程式功能

如果您授與對單一 Web 應用程式授與使用者唯讀存取權限，部分功能可能會在未預期的情況下停用。 以下管理功能需要對 Web 應用（參與者或擁有者）的**寫入**存取權限，並且在任何唯讀方案中都不可用。

* 命令 (像是開始、停止等)
* 變更像是一般組態的設定、調整設定、備份設定與監視設定
* 存取發佈認證與其他機密，像是應用程式設定與連接字串
* 串流記錄
* 診斷記錄組態
* 主控台 (命令提示字元)
* 有效的近期部署項目 (以便本機 git 持續部署)
* 預估的花費
* Web 測試
* 虛擬網路 (只有當虛擬網路先前是由具備寫入存取權限的使用者所設定，才會顯示出來)。

如果您無法存取上述任何一個磚，請洽詢您的系統管理員，取得 Web 應用程式的參與者存取權限。

## <a name="web-app-resources-that-require-write-access"></a>需要寫入存取權的 Web 應用程式資源

Web 應用程式因為幾個互有關聯的資源而顯得複雜。 以下是具有多個網站的典型資源群組：

![Web 應用程式資源群組](./media/troubleshooting/website-resource-model.png)

如此一來，如果您只授予某人 Web 應用程式存取權限，Azure 入口網站的網站刀鋒視窗上的諸多功能即會停用。

這些項目需要對應至您網站的「應用程式服務方案」**** 的**寫入**權：  

* 檢視 Web 應用程式的定價層 (免費或標準)  
* 調整組態 (執行個體數量、虛擬機器大小、自動調整設定)  
* 配額 (儲存容量、頻寬、CPU)  

這些項目都需要包含您網站的整個「資源群組」**** 的**寫入**權：  

* SSL 憑證與繫結 (相同資源群組與地理位置中的各個網站之間，可共用 SSL 憑證)  
* 警示規則  
* 自動調整設定  
* 應用程式見解元件  
* Web 測試  

## <a name="virtual-machine-features-that-require-write-access"></a>需要寫入存取權的虛擬機器功能

類似於 Web 應用程式，虛擬機器刀鋒視窗上的某些功能需要具備虛擬機器 (或是資源群組中的其他資源) 的寫入存取權限。

虛擬機器與網域名稱、虛擬網路、儲存體帳戶及警示規則相關。

這些項目都需要具備「虛擬機器」**** 的**寫入**權：

* 端點  
* IP 位址  
* 磁碟  
* 延伸模組  

這些項目都需要同時具備「虛擬機器」**** 與所屬之「資源群組」****(連同網域名稱) 的**寫入**權：  

* 可用性設定組  
* 負載平衡集合  
* 警示規則  

如果您無法存取上述任何一個磚，請洽詢您的系統管理員，以取得資源群組的參與者存取權限。

## <a name="azure-functions-and-write-access"></a>Azure Functions 和寫入權限

[Azure Functions](../azure-functions/functions-overview.md) 的某些功能需要寫入存取權。 例如，如果使用者分配了[Reader](built-in-roles.md#reader)角色，則他們將無法在函數應用中查看函數。 入口網站將顯示 **(無存取權)**。

![函數應用程式無存取權](./media/troubleshooting/functionapps-noaccess.png)

讀者可以按一下 [平台功能]**** 索引標籤，然後按一下 [所有設定]**** 以檢視與函數應用程式相關的一些設定 (類似於 Web 應用程式)，但他們不能修改這些設定。 要訪問這些功能，您需要["參與者"](built-in-roles.md#contributor)角色。

## <a name="next-steps"></a>後續步驟

- [針對來賓使用者的疑難排解](role-assignments-external-users.md#troubleshoot)
- [使用 RBAC 和 Azure 入口網站管理 Azure 資源的存取權](role-assignments-portal.md)
- [檢視活動記錄中 Azure 資源的各種 RBAC 變更](change-history-report.md)
