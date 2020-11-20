---
title: 限制來賓使用者存取權限-Azure Active Directory |Microsoft Docs
description: 使用 Azure Active Directory 中的 Azure 入口網站、PowerShell 或 Microsoft Graph 來限制來賓使用者存取權限
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/17/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: e89793ed8404bf36f2857f228d94fdf2a8828d43
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94984255"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>在 Azure Active Directory 中限制來賓存取許可權 (預覽) 

Azure Active Directory (Azure AD) 可讓您限制外部來賓使用者在其組織中可看到的 Azure AD。 在 Azure AD 中，來賓使用者預設會設定為有限的許可權等級，而成員使用者的預設值是一組完整的預設使用者許可權。 這是您 Azure AD 組織外部共同作業設定中新來賓使用者權限層級的預覽，以獲得更多限制的存取權，因此您現在可以選擇來賓存取權：

權限層級         | 存取層級
----------------         | ------------
與成員使用者相同     | 來賓具有與成員使用者相同的 Azure AD 資源存取權
有限存取 (預設)  | 來賓可以看到所有非隱藏群組的成員資格
**新) 的限制存取 (**  | **來賓看不到任何群組的成員資格**

當來賓存取受到限制時，來賓只能查看自己的使用者設定檔。 即使來賓正依使用者主體名稱或 objectId 進行搜尋，也不允許查看其他使用者的許可權。 限制存取也會限制來賓使用者看不到他們所屬群組的成員資格。 如需有關整體預設使用者權力的詳細資訊，包括來賓使用者權限，請參閱 [Azure Active Directory 中的預設使用者許可權為何？](../fundamentals/users-default-permissions.md)。

## <a name="permissions-and-licenses"></a>許可權與授權

您必須是全域管理員角色，才能設定外部共同作業設定。 限制來賓存取沒有額外的授權需求。

## <a name="update-in-the-azure-portal"></a>Azure 入口網站中的更新

我們已對來賓使用者權限的現有 Azure 入口網站控制項進行了變更。

1. 使用全域管理員許可權登入 [Azure AD admin center](https://aad.portal.azure.com) 。
1. 在您組織的 **Azure Active Directory** 總覽] 頁面上，選取 [ **使用者設定**]。
1. 在 [ **外部使用者**] 下，選取 [ **管理外部** 共同作業設定]。
1. 在 [ **外部** 共同作業設定] 頁面上，選取 [ **來賓使用者存取限制為其自己的目錄物件的屬性和成員資格]** 選項。

    ![Azure AD 外部共同作業設定頁面](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. 選取 [儲存]。 這些變更最多可能需要15分鐘的時間才會對來賓使用者生效。

## <a name="update-with-the-microsoft-graph-api"></a>使用 Microsoft Graph API 更新

我們新增了新的 Microsoft Graph API，以在您的 Azure AD 組織中設定來賓許可權。 您可以建立下列 API 呼叫，以指派任何許可權層級。 此處使用的 guestUserRoleId 值是說明最受限制的來賓使用者設定。 如需使用 Microsoft Graph 設定來賓許可權的詳細資訊，請參閱 [authorizationPolicy 資源類型](/graph/api/resources/authorizationpolicy)。

### <a name="configuring-for-the-first-time"></a>第一次設定

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

回應應為成功204。

### <a name="updating-the-existing-value"></a>正在更新現有的值

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

回應應為成功204。

### <a name="view-the-current-value"></a>查看目前的值

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

範例回應：

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>使用 PowerShell Cmdlet 進行更新

透過這項功能，我們新增了透過 PowerShell v2 Cmdlet 設定受限制許可權的功能。 取得和設定 PowerShell Cmdlet 已在版本2.0.2.85 中發佈。

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>取得命令： Get-AzureADMSAuthorizationPolicy

範例：

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Set 命令： Set-AzureADMSAuthorizationPolicy

範例：

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> 您必須在要求時輸入 authorizationPolicy 做為識別碼。

## <a name="supported-microsoft-365-services"></a>支援的 Microsoft 365 服務

### <a name="supported-services"></a>支援的服務

藉由支援，我們表示體驗如預期般運作;具體而言，它與目前的來賓體驗相同。

- Teams
- Outlook (OWA) 
- SharePoint

### <a name="services-currently-not-supported"></a>目前不支援的服務

沒有目前支援的服務可能會與新的來賓限制設定有相容性問題。

- 表單
- 小組中的規劃工具
- Planner 應用程式
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>常見問題集 (FAQ)

問題 | Answer
-------- | ------
這些許可權的適用位置為何？ | 這些目錄層級許可權會跨 Azure AD 服務和入口網站強制執行，包括 Microsoft Graph、PowerShell v2、Azure 入口網站和我的應用程式入口網站。 利用 Microsoft 365 群組進行共同作業案例的 Microsoft 365 服務，也會受到影響，尤其是 Outlook、Microsoft 小組和 SharePoint。
限制許可權如何影響來賓可以看見哪些群組？ | 無論預設或受限制的來賓許可權為何，來賓都無法列舉群組或使用者的清單。 來賓可以根據許可權，在 Azure 入口網站和我的應用程式入口網站中看到他們隸屬的群組。<li>**預設許可權**：若要在 Azure 入口網站中尋找其成員的群組，來賓必須在 [ **所有使用者** ] 清單中搜尋其物件識別碼，然後選取 [ **群組**]。 在這裡，他們可以看到其成員的群組清單，包括所有群組詳細資料，包括名稱、電子郵件等等。 在我的應用程式入口網站中，他們可以看到他們擁有的群組清單，以及它們所屬的群組。</li><li>**受限制的來賓許可權**：在 Azure 入口網站中，他們仍然可以在 [所有使用者] 清單中搜尋其物件識別碼，以尋找其成員的群組清單，然後選取 [群組]。 他們只能看到群組的詳細資料，尤其是物件識別碼。 根據設計，名稱和電子郵件資料行是空白的，且群組類型無法辨識。 在我的應用程式入口網站中，他們無法存取其所擁有的群組清單或它們所屬的群組。</li><br>如需來自圖形 API 之目錄許可權的詳細比較，請參閱 [預設使用者](../fundamentals/users-default-permissions.md#member-and-guest-users)權力。
這項功能在我的應用程式入口網站中的哪些部分會受到影響？ | 我的應用程式入口網站中的群組功能將採用這些新的許可權。 這包括在我的應用程式中查看群組清單和群組成員資格的所有路徑。 群組磚可用性未進行任何變更。 群組磚可用性仍然受 Azure 入口網站中的現有群組設定所控制。
這些許可權會覆寫 SharePoint 或 Microsoft 團隊的來賓設定嗎？ | 否。 這些現有的設定仍能控制這些應用程式的體驗和存取。 例如，如果您在 SharePoint 中看到問題，請再次檢查您的外部共用設定。
Planner 和 Yammer 的已知相容性問題有哪些？ | <li>當權限設定為 [受限制] 時，登入 Planner 應用程式或存取 Microsoft 小組中之 Planner 的來賓將無法存取其方案或任何工作。<li>當權限設定為 [受限制] 時，登入 Yammer 的來賓將無法離開群組。
我的租使用者中的現有來賓許可權是否會變更？ | 目前的設定未進行任何變更。 我們維持與您現有設定的回溯相容性。 您決定何時要進行變更。
這些許可權預設會設定嗎？ | 否。 現有的預設許可權會維持不變。 您可以選擇性地將許可權設定為更嚴格的限制。
這項功能是否有任何授權需求？ | 否，這項功能沒有新的授權需求。

## <a name="next-steps"></a>下一步

- 若要深入瞭解 Azure AD 中的現有來賓許可權，請參閱 [Azure Active Directory 中的預設使用者許可權為何？](../fundamentals/users-default-permissions.md)
- 若要查看限制來賓存取的 Microsoft Graph API 方法，請參閱 [authorizationPolicy 資源類型](/graph/api/resources/authorizationpolicy)
- 若要撤銷使用者的所有存取權，請參閱 [Azure AD 中的撤銷使用者存取權](users-revoke-access.md)