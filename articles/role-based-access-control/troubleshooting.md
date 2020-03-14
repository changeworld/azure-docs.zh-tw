---
title: 針對適用於 Azure 資源的 RBAC 進行疑難排解 | Microsoft Docs
description: 針對適用於 Azure 資源的角色型存取控制 (RBAC) 問題進行疑難排解。
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
ms.date: 11/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 67d624bb81105b8219030c57460b6d7bf7458671
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245521"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>針對適用於 Azure 資源的 RBAC 進行疑難排解

此文章將回答適用於 Azure 資源之角色型存取控制 (RBAC) 的常見問題，讓您知道在 Azure 入口網站中使用角色時會有什麼樣的結果，以及如何解決存取權相關問題。

## <a name="problems-with-rbac-role-assignments"></a>RBAC 角色指派的問題

- 如果您無法在 [**存取控制（IAM）** ] 的 Azure 入口網站中新增角色指派，因為 [**新增** > **新增角色指派**] 選項已停用，或因為您收到許可權錯誤「具有物件識別碼的用戶端沒有執行動作的授權」，請確認您目前已登入的使用者已獲指派角色，且該角色具有 [`Microsoft.Authorization/roleAssignments/write`] 許可權，例如您嘗試指派角色之範圍的 [[擁有](built-in-roles.md#owner)者] 或 [[使用者存取系統管理員](built-in-roles.md#user-access-administrator)]。
- 如果您在嘗試指派角色時收到錯誤訊息「無法建立更多角色指派（程式碼： RoleAssignmentLimitExceeded）」，請嘗試改為將角色指派給群組，以減少角色指派的數目。 Azure 支援每個訂用帳戶最多 **2000** 個角色指派。 這個角色指派限制是固定的，而且無法增加。

## <a name="problems-with-custom-roles"></a>自訂角色的問題

- 如果您需要如何建立自訂角色的步驟，請參閱使用[Azure PowerShell](tutorial-custom-role-powershell.md)或[Azure CLI](tutorial-custom-role-cli.md)的自訂角色教學課程。
- 如果您無法更新現有的自訂角色，請確認您目前登入的使用者已獲指派具有 [`Microsoft.Authorization/roleDefinition/write`] 許可權的角色，例如 [[擁有](built-in-roles.md#owner)者] 或 [[使用者存取系統管理員](built-in-roles.md#user-access-administrator)]。
- 如果您無法刪除自訂角色，並收到錯誤訊息「有現有的角色指派參考角色（代碼： RoleDefinitionHasAssignments）」，則仍有使用自訂角色的角色指派。 請移除這些角色指派，並試著再次刪除自訂角色。
- 如果您收到「已超過角色定義限制」錯誤訊息。 當您嘗試建立新的自訂角色時，無法建立更多角色定義（代碼： RoleDefinitionLimitExceeded）」，請刪除未使用的任何自訂角色。 Azure 在租使用者中最多支援**5000**個自訂角色。 (若為特製化的雲端，例如 Azure Government、Azure 德國和 Azure 中國 21Vianet，則限制為 2000 個自訂角色)。
- 如果您收到類似于「用戶端具有在範圍 '/subscriptions/{subscriptionid} ' 上執行動作 ' roleDefinitions/write ' 的許可權，但找不到連結的訂用帳戶」的錯誤，請檢查租使用者中是否已刪除一或多個可[指派的範圍](role-definitions.md#assignablescopes)。 如果範圍已刪除，則可以建立支援票證，因為此時已沒有可用的自助解決方案。

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>當訂用帳戶在租用戶之間移動時復原 RBAC

- 如果您需要如何將訂用帳戶轉移至不同 Azure AD 租使用者的步驟，請參閱將[Azure 訂用帳戶的擁有權轉移給另一個帳戶](../cost-management-billing/manage/billing-subscription-transfer.md)。
- 如果您將訂用帳戶轉移至不同的 Azure AD 租用戶，所有角色指派都會從來源 Azure AD 租用戶中永久刪除，而且不會遷移至目標 Azure AD 租用戶。 您必須在目標租用戶中重新建立角色指派。 您也必須手動重新建立適用于 Azure 資源的受控識別。 如需詳細資訊，請參閱[常見問題和受控識別的已知問題](../active-directory/managed-identities-azure-resources/known-issues.md)。
- 如果您是 Azure AD 全域管理員，而且在租使用者之間移動後無法存取訂用帳戶，請使用**Azure 資源的存取管理**切換來暫時[提高存取](elevate-access-global-admin.md)權，以取得訂用帳戶的存取權。

## <a name="issues-with-service-admins-or-co-admins"></a>服務管理員或共同管理員的問題

- 如果您有服務管理員或共同管理員的問題，請參閱[新增或變更 azure 訂](../cost-management-billing/manage/add-change-subscription-administrator.md)用帳戶管理員和[傳統訂用帳戶管理員角色、Azure RBAC 角色和 Azure AD 系統管理員角色](rbac-and-directory-admin-roles.md)。

## <a name="access-denied-or-permission-errors"></a>拒絕存取或許可權錯誤

- 如果您在嘗試建立資源時收到許可權錯誤「具有物件識別碼的用戶端沒有執行動作的授權（代碼： AuthorizationFailed）」，請檢查您目前登入的使用者是否已獲指派具有寫入權限的角色所選範圍內資源的許可權。 例如，若要管理資源群組中的虛擬機器，您應該具有資源群組 (或父範圍) 的[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)角色。 如需每個內建角色的權限清單，請參閱 [Azure 資源的內建角色](built-in-roles.md)。
- 如果您在嘗試建立或更新支援票證時收到許可權錯誤「您沒有建立支援要求的許可權」，請檢查您目前登入的使用者是否已獲指派具有 [`Microsoft.Support/supportTickets/write`] 許可權的角色，例如[支援要求參與者](built-in-roles.md#support-request-contributor)。

## <a name="role-assignments-with-unknown-security-principal"></a>具有不明安全性主體的角色指派

如果您將角色指派給安全性主體（使用者、群組、服務主體或受控識別），然後在稍後刪除該安全性主體而未移除角色指派，則角色指派的安全性主體類型會列為 [**不明**]。 下列螢幕擷取畫面顯示 Azure 入口網站中的範例。 安全性主體名稱列為 [**已刪除身分識別**]，而 [識別] 已**不存在**。 

![Web 應用程式資源群組](./media/troubleshooting/unknown-security-principal.png)

如果您使用 Azure PowerShell 列出此角色指派，您會看到空白的 `DisplayName` 和 `ObjectType` 設定為 [未知]。 例如， [new-azroleassignment](/powershell/module/az.resources/get-azroleassignment)會傳回與下列類似的角色指派：

```azurepowershell
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

同樣地，如果您使用 Azure CLI 列出此角色指派，您會看到空白的 `principalName`。 例如， [az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)會傳回與下列類似的角色指派：

```azurecli
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

這不是保留這些角色指派的問題，但您可以使用與其他角色指派類似的步驟來移除它們。 如需有關如何移除角色指派的詳細資訊，請參閱[Azure 入口網站](role-assignments-portal.md#remove-a-role-assignment)、 [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)或[Azure CLI](role-assignments-cli.md#remove-a-role-assignment)

在 PowerShell 中，如果您嘗試使用物件識別碼和角色定義名稱移除角色指派，而且有一個以上的角色指派符合您的參數，您會收到錯誤訊息：「提供的資訊未對應至角色指派」。 以下顯示錯誤訊息的範例：

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

如果您收到此錯誤訊息，請確定您也指定了 `-Scope` 或 `-ResourceGroupName` 參數。

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>偵測不到 RBAC 的變更

Azure Resource Manager 有時候會快取組態和資料來改善效能。 建立或刪除角色指派時，這些變更可能需要 30 分鐘的時間才會生效。 如果您使用 Azure 入口網站、Azure PowerShell 或 Azure CLI，您可以藉由登出再登入，來強制重新整理角色指派變更。 如果您使用 REST API 呼叫來變更角色指派，您可以重新整理存取權杖來強制重新整理。

## <a name="web-app-features-that-require-write-access"></a>需要寫入存取權的 Web 應用程式功能

如果您授與對單一 Web 應用程式授與使用者唯讀存取權限，部分功能可能會在未預期的情況下停用。 以下管理功能需要 Web 應用程式的**寫入**存取權限 (參與者或擁有者)，而且無法在任何唯讀情況中使用。

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

這些項目需要對應至您網站的「應用程式服務方案」的**寫入**權：  

* 檢視 Web 應用程式的定價層 (免費或標準)  
* 調整組態 (執行個體數量、虛擬機器大小、自動調整設定)  
* 配額 (儲存容量、頻寬、CPU)  

這些項目都需要包含您網站的整個「資源群組」的**寫入**權：  

* SSL 憑證與繫結 (相同資源群組與地理位置中的各個網站之間，可共用 SSL 憑證)  
* 警示規則  
* 自動調整設定  
* 應用程式見解元件  
* Web 測試  

## <a name="virtual-machine-features-that-require-write-access"></a>需要寫入存取權的虛擬機器功能

類似於 Web 應用程式，虛擬機器刀鋒視窗上的某些功能需要具備虛擬機器 (或是資源群組中的其他資源) 的寫入存取權限。

虛擬機器與網域名稱、虛擬網路、儲存體帳戶及警示規則相關。

這些項目都需要具備「虛擬機器」的**寫入**權：

* 端點  
* IP 位址  
* 磁碟  
* 延伸模組  

這些項目都需要同時具備「虛擬機器」與所屬之「資源群組」(連同網域名稱) 的**寫入**權：  

* 可用性設定組  
* 負載平衡集合  
* 警示規則  

如果您無法存取上述任何一個磚，請洽詢您的系統管理員，以取得資源群組的參與者存取權限。

## <a name="azure-functions-and-write-access"></a>Azure Functions 和寫入權限

[Azure Functions](../azure-functions/functions-overview.md) 的某些功能需要寫入存取權。 例如，如果使用者被指派「[讀取](built-in-roles.md#reader)者」角色，他們將無法在函式應用程式中查看函式。 入口網站將顯示 **(無存取權)** 。

![函數應用程式無存取權](./media/troubleshooting/functionapps-noaccess.png)

讀者可以按一下 [平台功能] 索引標籤，然後按一下 [所有設定] 以檢視與函數應用程式相關的一些設定 (類似於 Web 應用程式)，但他們不能修改這些設定。 若要存取這些功能，您將需要「[參與者](built-in-roles.md#contributor)」角色。

## <a name="next-steps"></a>後續步驟

- [針對來賓使用者進行疑難排解](role-assignments-external-users.md#troubleshoot)
- [使用 RBAC 和 Azure 入口網站管理 Azure 資源的存取權](role-assignments-portal.md)
- [檢視活動記錄中 Azure 資源的各種 RBAC 變更](change-history-report.md)

