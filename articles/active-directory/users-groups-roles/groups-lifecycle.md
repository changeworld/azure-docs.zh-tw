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
ms.topic: how-to
ms.date: 03/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 182f51a150c2ef944b0104b73c63028e915c1a4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84728328"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>為 Office 365 群組設定到期原則

本文說明如何藉由為其設定到期原則來管理 Office 365 群組的生命週期。 您只能針對 Azure Active Directory （Azure AD）中的 Office 365 群組設定到期原則。

一旦您為群組設定到期日：

- 具有使用者活動的群組會在到期時自動更新。
- 群組的擁有者會收到更新群組的通知（如果群組不是自動更新）。
- 任何未更新的群組都會遭到刪除。
- 群組擁有者或系統管理員可以在30天內還原已刪除的任何 Office 365 群組。

目前，Azure AD 組織中的所有 Office 365 群組都只能設定一個到期原則。

> [!NOTE]
> 針對 Office 365 群組設定及使用到期原則時，您需要擁有但不一定要為套用到期原則之所有群組的成員指派 Azure AD Premium 授權。

如需有關如何下載及安裝 Azure AD PowerShell Cmdlet 的資訊，請參閱 [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)。

## <a name="activity-based-automatic-renewal"></a>以活動為基礎的自動更新

使用 Azure AD 情報，群組現在會根據最近是否已使用來自動更新。 這項功能不需要由群組擁有者進行手動動作，因為它是以 Office 365 服務（如 Outlook、SharePoint 或小組）中群組的使用者活動為基礎。 例如，如果擁有者或群組成員執行的作業類似在 SharePoint 中上傳檔、流覽小組頻道，或傳送電子郵件至 Outlook 中的群組，則會自動更新該群組，而且擁有者不會收到任何更新通知。

### <a name="activities-that-automatically-renew-group-expiration"></a>自動更新群組到期的活動

下列使用者動作會造成自動群組更新：

- SharePoint：查看、編輯、下載、移動、共用或上傳檔案
- Outlook：加入群組、從群組空間讀取/寫入群組訊息，像是訊息（在 Outlook Web 存取中）
- 小組：造訪小組頻道

### <a name="auditing-and-reporting"></a>稽核與報告

系統管理員可以從 Azure AD 的活動稽核記錄中，取得自動更新的群組清單。

![根據活動自動更新群組](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>角色和權限

以下是可以針對 Azure AD 中的 Office 365 群組設定及使用到期日的角色。

角色 | 權限
-------- | --------
全域管理員、群組系統管理員或使用者系統管理員 | 可以建立、讀取、更新或刪除 Office 365 群組到期日原則設定<br>可以更新任何 Office 365 群組
使用者 | 可以更新它們所擁有的 Office 365 群組<br>可以還原它們所擁有的 Office 365 群組<br>可以讀取到期原則設定

如需有關將已刪除群組還原之權限的詳細資訊，請參閱[在 Azure Active Directory 中還原已刪除的 Office 365 群組](groups-restore-deleted.md)。

## <a name="set-group-expiration"></a>設定群組到期日

1. 使用在您 Azure AD 組織中為全域管理員的帳戶開啟[Azure AD 系統管理中心](https://aad.portal.azure.com)。

2. 選取 [**群組**]，然後選取 [**到期**] 以開啟到期設定。
  
   ![群組的到期設定](./media/groups-lifecycle/expiration-settings.png)

3. 在 [**到期**] 頁面上，您可以：

    - 設定群組的存留期 (以天為單位)。 您可以選取其中一個預設值或自訂值 (應為 31 天或更多)。
    - 指定當群組沒有擁有者時應該傳送續訂和到期通知的電子郵件地址。
    - 選取到期的 Office 365 群組。 您可以設定到期日：
      - **全部**Office 365 群組
      - **選取**的 Office 365 群組清單
      - **無**以限制所有群組的到期
    - 當您完成時，選取 [儲存]**** 會儲存您的設定。

> [!NOTE]
> - 當您第一次設定到期日時，任何早于到期間隔的群組都會設定為35天，直到到期為止，除非群組自動更新或擁有者續訂為止。
> - 刪除並還原動態群組時，會將它視為新的群組，並根據規則重新填入。 此程序最多可能需要 24 小時。
> - 小組中所使用群組的到期通知會出現在小組擁有者摘要中。

## <a name="email-notifications"></a>電子郵件通知

如果未自動更新群組，則會在群組到期前30天、15天和1天，將這類電子郵件通知傳送給 Office 365 群組擁有者。 電子郵件的語言是由群組擁有者的慣用語言或 Azure AD 語言設定來決定。 如果群組擁有者已定義慣用語言，或多個擁有者都有相同的慣用語言，則會使用該語言。 若是其他所有情況，則會使用 Azure AD 語言設定。

![到期電子郵件通知](./media/groups-lifecycle/expiration-notification.png)

在**更新群組**通知電子郵件中，群組擁有者可以直接存取[存取面板](https://account.activedirectory.windowsazure.com/r#/applications)中的群組詳細資料頁面。 使用者可以從該處取得群組的相關詳細資訊，例如其描述、其最後更新時間、其到期時間，還能夠更新群組。 群組詳細資料頁面現在也包含 Office 365 群組資源的連結，可方便群組擁有者檢視其群組中的內容和活動。

當群組到期時，會在到期日隔天刪除群組。 這種電子郵件通知會傳送給 Office 365 群組擁有者，通知他們 Office 365 群組的到期日和後續刪除。

![群組刪除電子郵件通知](./media/groups-lifecycle/deletion-notification.png)

在刪除群組後的 30 天內，您可以選取 [還原群組]**** 或使用 PowerShell Cmdlet 來還原群組，如[在 Azure Active Directory 中還原已刪除的 Office 365 群組](groups-restore-deleted.md)所述。 請注意，您無法自訂 30 天的群組還原期間。

如果您要還原的群組包含文件、SharePoint 網站或其他持續物件，則可能需要 24 小時，才能完全還原群組及其內容。

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>如何取出 Office 365 群組到期日

除了可讓使用者查看群組詳細資料的存取面板，包括到期日和上次更新日期，可以從 Microsoft Graph REST API Beta 抓取 Office 365 群組的到期日。 已在 Microsoft Graph Beta 中啟用 expirationDateTime 為群組屬性。 您可以使用 GET 要求來抓取此檔案。 如需詳細資訊，請參閱[此範例](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example)。

> [!NOTE]
> 若要在存取面板上管理群組成員資格，必須在 [Azure Active Directory 群組] [一般] 設定中，將 [限制存取存取面板中的群組] 設定為 [否]。

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Office 365 群組到期如何與法務保留中的信箱搭配運作

當群組到期而被刪除時，在刪除後的 30 天之後，系統就會將來自應用程式 (例如「行事曆」、「網站」或「小組」) 的群組資料永久刪除，但是會保留法務保留中的群組信箱，而不會永久刪除。 系統管理員可以使用 Exchange Cmdlet 來還原信箱以擷取資料。

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Office 365 群組到期如何與保留原則搭配運作

保留原則是透過「安全性與合規性中心」來設定的。 如果您已經為 Office 365 群組設定保留原則，當群組到期而被刪除時，系統會根據保留原則中所定義的特定天數，將群組信箱中的群組對話及群組網站中的檔案會保留在保留容器中。 在群組到期後，使用者將看不到該群組及其內容，但可以透過電子文件探索來復原網站和信箱資料。

## <a name="powershell-examples"></a>PowerShell 範例

以下範例說明如何使用 PowerShell Cmdlet 來設定 Azure AD 組織中 Office 365 群組的到期設定：

1. 安裝 PowerShell v2.0 模組，並在 PowerShell 命令提示字元中登入：

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. 設定到期設定使用 Remove-azureadmsgrouplifecyclepolicy 指令程式，將 Azure AD 組織中所有 Office 365 群組的存留期設為365天。 不具擁有者之 Office 365 群組的續約通知會傳送給 ' emailaddress@contoso.com '
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. 擷取現有的原則 Get-AzureADMSGroupLifecyclePolicy：此 Cmdlet 會擷取目前已設定的 Office 365 群組到期設定。 在此範例中，您可以看見：

   - 原則識別碼
   - Azure AD 組織中所有 Office 365 群組的存留期皆設定為365天
   - 不具擁有者之 Office 365 群組的續約通知會傳送給 ' ' emailaddress@contoso.com 。
  
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
  
1. 移除現有的原則 Remove-AzureADMSGroupLifecyclePolicy：此 Cmdlet 會刪除 Office 365 群組到期設定，但需要原則識別碼。 此 Cmdlet 會停用 Office 365 群組的到期日。
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
下列 Cmdlet 可用來更詳細地設定原則。 如需詳細資訊，請參閱[PowerShell 檔](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups)。

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
