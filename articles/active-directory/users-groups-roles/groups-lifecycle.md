---
title: 設定 Office 365 群組的到期日 - Azure Active Directory | Microsoft Docs
description: 如何為 Azure Active Directory 中的 Office 365 群組設定到期日
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b551bc8a46a45135bf6a9a8e328b4b0e74f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048255"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>為 Office 365 群組設定到期原則

本文介紹如何通過為 Office 365 組設置過期策略來管理它們的生命週期。 只能為 Azure 活動目錄 （Azure AD） 中的 Office 365 組設置過期策略。

一旦您為群組設定到期日：

- 隨著過期的臨近，具有使用者活動的組將自動續訂。
- 如果組未自動續訂，則會通知組的擁有者續訂該組。
- 任何未續訂的組都將被刪除。
- 任何已刪除的 Office 365 組都可以在 30 天內由組擁有者或管理員還原。

目前，只能為 Azure AD 組織中的所有 Office 365 組配置一個過期策略。

> [!NOTE]
> 配置和使用 Office 365 組的過期策略要求您擁有但不一定為應用過期策略的所有組的成員分配 Azure AD 高級許可證。

如需有關如何下載及安裝 Azure AD PowerShell Cmdlet 的資訊，請參閱 [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)。

## <a name="activity-based-automatic-renewal"></a>以活動為基礎的自動更新

使用 Azure AD 智慧，現在根據是否最近使用組自動續訂。 此功能消除了組擁有者手動操作的需要，因為它基於跨 Office 365 服務（如 Outlook、SharePoint 或 Teams）的組中的使用者活動。 例如，如果擁有者或組成員執行諸如在 SharePoint 中上載文檔、訪問 Teams 通道或向 Outlook 中的組發送電子郵件等，則該組將自動續訂，並且擁有者不會收到任何續訂通知。

### <a name="activities-that-automatically-renew-group-expiration"></a>自動續訂組過期的活動

以下使用者操作會導致自動組續訂：

- SharePoint：查看、編輯、下載、移動、共用或上傳檔
- Outlook：加入組，從組空間讀取/寫入組消息，類似于消息（在 Outlook Web 訪問中）
- 團隊：訪問團隊通道

### <a name="auditing-and-reporting"></a>稽核與報告

管理員可以從 Azure AD 中的活動稽核日誌中獲取自動續訂的組的清單。

![基於活動自動續訂組](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>角色和權限

以下是可以針對 Azure AD 中的 Office 365 群組設定及使用到期日的角色。

角色 | 權限
-------- | --------
全域管理員、組管理員或使用者管理員 | 可以建立、讀取、更新或刪除 Office 365 群組到期日原則設定<br>可以更新任何 Office 365 群組
User | 可以更新它們所擁有的 Office 365 群組<br>可以還原它們所擁有的 Office 365 群組<br>可以讀取到期原則設定

如需有關將已刪除群組還原之權限的詳細資訊，請參閱[在 Azure Active Directory 中還原已刪除的 Office 365 群組](groups-restore-deleted.md)。

## <a name="set-group-expiration"></a>設定群組到期日

1. 使用 Azure AD 組織中全域管理員的帳戶打開[Azure AD 管理中心](https://aad.portal.azure.com)。

2. 選擇 **"組**"，然後選擇 **"過期"** 以打開過期設置。
  
   ![組的過期設置](./media/groups-lifecycle/expiration-settings.png)

3. 在 **"過期"** 頁上，您可以：

    - 設定群組的存留期 (以天為單位)。 您可以選取其中一個預設值或自訂值 (應為 31 天或更多)。
    - 指定當群組沒有擁有者時應該傳送續訂和到期通知的電子郵件地址。
    - 選取到期的 Office 365 群組。 您可以將過期設置為：
      - **全部**辦公室 365 個組
      - **所選**辦公室 365 個組的清單
      - **無**限制所有組的過期
    - 當您完成時，選取 [儲存]**** 會儲存您的設定。

> [!NOTE]
> - 首次設置過期時，任何早于過期間隔的組將設置為 35 天，直到過期，除非該組自動續訂或擁有者重新續訂它。
> - 刪除並還原動態組時，它被視為新組，並根據規則重新填充。 此程序最多可能需要 24 小時。
> - Teams 中使用的組的過期通知將顯示在"團隊擁有者"源中。

## <a name="email-notifications"></a>電子郵件通知

如果未自動續訂組，則此類電子郵件通知將在組過期前 30 天、15 天和 1 天發送給 Office 365 組擁有者。 電子郵件的語言由組擁有者的首選語言或 Azure AD 語言設置決定。 如果群組擁有者已定義慣用語言，或多個擁有者都有相同的慣用語言，則會使用該語言。 對於所有其他情況，使用 Azure AD 語言設置。

![過期電子郵件通知](./media/groups-lifecycle/expiration-notification.png)

從 **"續訂組**通知電子郵件"中，組擁有者可以直接存取[訪問面板](https://account.activedirectory.windowsazure.com/r#/applications)中的組詳細資訊頁面。 使用者可以從該處取得群組的相關詳細資訊，例如其描述、其最後更新時間、其到期時間，還能夠更新群組。 群組詳細資料頁面現在也包含 Office 365 群組資源的連結，可方便群組擁有者檢視其群組中的內容和活動。

當群組到期時，會在到期日隔天刪除群組。 這種電子郵件通知會傳送給 Office 365 群組擁有者，通知他們 Office 365 群組的到期日和後續刪除。

![組刪除電子郵件通知](./media/groups-lifecycle/deletion-notification.png)

在刪除群組後的 30 天內，您可以選取 [還原群組]**** 或使用 PowerShell Cmdlet 來還原群組，如[在 Azure Active Directory 中還原已刪除的 Office 365 群組](groups-restore-deleted.md)所述。 請注意，您無法自訂 30 天的群組還原期間。

如果您要還原的群組包含文件、SharePoint 網站或其他持續物件，則可能需要 24 小時，才能完全還原群組及其內容。

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>如何檢索 Office 365 組到期日期

除了訪問面板，使用者可以查看組詳細資訊，包括到期日期和上次續訂日期，還可以從 Microsoft 圖形 REST API 測試版檢索 Office 365 組的到期日期。 過期日期時間作為組屬性已在 Microsoft 圖形測試版中啟用。 可以使用 GET 請求檢索它。 有關詳細資訊，請參閱[此示例](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example)。

> [!NOTE]
> 為了管理訪問面板上的組成員身份，需要在 Azure 活動目錄組常規設置中將"限制訪問訪問面板中的組"設置為"否"。

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Office 365 群組到期如何與法務保留中的信箱搭配運作

當群組到期而被刪除時，在刪除後的 30 天之後，系統就會將來自應用程式 (例如「行事曆」、「網站」或「小組」) 的群組資料永久刪除，但是會保留法務保留中的群組信箱，而不會永久刪除。 系統管理員可以使用 Exchange Cmdlet 來還原信箱以擷取資料。

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Office 365 群組到期如何與保留原則搭配運作

保留原則是透過「安全性與合規性中心」來設定的。 如果您已經為 Office 365 群組設定保留原則，當群組到期而被刪除時，系統會根據保留原則中所定義的特定天數，將群組信箱中的群組對話及群組網站中的檔案會保留在保留容器中。 在群組到期後，使用者將看不到該群組及其內容，但可以透過電子文件探索來復原網站和信箱資料。

## <a name="powershell-examples"></a>PowerShell 範例

以下是如何使用 PowerShell Cmdlet 配置 Azure AD 組織中 Office 365 組的過期設置的示例：

1. 安裝 PowerShell v2.0 模組並在 PowerShell 提示符處登錄：

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. 配置過期設置 使用 New-AzureMSGroup 生命週期策略 Cmdlet 將 Azure AD 組織中所有 Office 365 組的存留期設置為 365 天。 沒有擁有者的 Office 365 組的續訂通知將發送到emailaddress@contoso.com''
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. 擷取現有的原則 Get-AzureADMSGroupLifecyclePolicy：此 Cmdlet 會擷取目前已設定的 Office 365 群組到期設定。 在此範例中，您可以看見：

   - 原則識別碼
   - Azure AD 組織中所有 Office 365 組的存留期設置為 365 天
   - 沒有擁有者的 Office 365 組的續訂通知將發送到emailaddress@contoso.com'。
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. 更新現有的原則 Set-AzureADMSGroupLifecyclePolicy：此 Cmdlet 可用來更新現有的原則。 在以下範例中，現有原則中的群組存留期會從 365 天變更為 180 天。
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. 將特定群組新增至原則 Add-AzureADMSLifecyclePolicyGroup：此 Cmdlet 會將群組新增至生命週期原則。 例如：
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. 移除現有的原則 Remove-AzureADMSGroupLifecyclePolicy：此 Cmdlet 會刪除 Office 365 群組到期設定，但需要原則識別碼。 此 Cmdlet 禁用 Office 365 組的過期。
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
下列 Cmdlet 可用來更詳細地設定原則。 有關詳細資訊，請參閱[PowerShell 文檔](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups)。

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>後續步驟

這些文章提供有關 Azure AD 群組的其他資訊。

- [查看現有的群組](../fundamentals/active-directory-groups-view-azure-portal.md)
- [管理群組的設定](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [管理群組的成員](../fundamentals/active-directory-groups-members-azure-portal.md)
- [管理群組的成員資格](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理群組中使用者的動態規則](groups-dynamic-membership.md)
