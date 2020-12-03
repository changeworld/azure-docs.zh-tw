---
title: 設定 Microsoft 365 群組的到期時間-Azure Active Directory |Microsoft Docs
description: 如何在 Azure Active Directory 中設定 Microsoft 365 群組的到期日
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60d71c44ebaba5b0cbcb03afa41ad15237dceaef
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547469"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>設定 Microsoft 365 群組的到期原則

本文將告訴您如何藉由為其設定到期原則來管理 Microsoft 365 群組的生命週期。 您只能針對 Azure Active Directory (Azure AD) 中的 Microsoft 365 群組設定到期原則。

一旦您為群組設定到期日：

- 具有使用者活動的群組會在到期時自動更新。
- 如果群組不會自動更新，群組的擁有者會收到更新群組的通知。
- 已刪除任何未更新的群組。
- 群組擁有者或系統管理員可以在30天內還原已刪除的任何 Microsoft 365 群組。

目前，您只能為 Azure AD 組織中的所有 Microsoft 365 群組設定一個到期原則。

> [!NOTE]
> 設定和使用 Microsoft 365 群組的到期原則時，您必須擁有但不一定要為套用到期原則之所有群組的成員指派 Azure AD Premium 授權。

如需有關如何下載及安裝 Azure AD PowerShell Cmdlet 的資訊，請參閱 [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)。

## <a name="activity-based-automatic-renewal"></a>以活動為基礎的自動更新

使用 Azure AD 智慧，現在會根據最近使用的方式，自動更新群組。 這項功能不需要由群組擁有者手動採取動作，因為它是以跨 Microsoft 365 服務（例如 Outlook、SharePoint 或團隊）群組中的使用者活動為基礎。 例如，如果擁有者或群組成員有像是上傳 SharePoint 中的檔、造訪團隊頻道，或是傳送電子郵件給 Outlook 中的群組，則會自動更新群組，且擁有者不會收到任何更新通知。

### <a name="activities-that-automatically-renew-group-expiration"></a>自動更新群組到期的活動

下列使用者動作會導致自動群組更新：

- SharePoint：查看、編輯、下載、移動、共用或上傳檔案
- Outlook：從群組空間加入群組、讀取/寫入群組訊息，例如 Outlook 中的訊息 (Web 存取) 
- 小組：造訪團隊頻道

### <a name="auditing-and-reporting"></a>稽核與報告

系統管理員可以從 Azure AD 中的活動稽核記錄取得自動更新的群組清單。

![根據活動自動更新群組](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>角色和權限

下列角色可設定和使用 Azure AD 中 Microsoft 365 群組的到期時間。

角色 | 權限
-------- | --------
全域管理員、群組管理員或使用者系統管理員 | 可以建立、讀取、更新或刪除 Microsoft 365 群組到期原則設定<br>可以更新任何 Microsoft 365 群組
User | 可以更新他們擁有的 Microsoft 365 群組<br>可以還原他們所擁有的 Microsoft 365 群組<br>可以讀取到期原則設定

如需有關還原已刪除群組之許可權的詳細資訊，請參閱 [Azure Active Directory 中的還原已刪除的 Microsoft 365 群組](groups-restore-deleted.md)。

## <a name="set-group-expiration"></a>設定群組到期日

1. 使用您 Azure AD 組織中的全域管理員帳戶，開啟 [Azure AD 系統管理中心](https://aad.portal.azure.com) 。

2. 選取 [ **群組**]，然後選取 [ **到期** ] 以開啟到期設定。
  
   ![群組的到期設定](./media/groups-lifecycle/expiration-settings.png)

3. 在 [ **到期** ] 頁面上，您可以：

    - 設定群組的存留期 (以天為單位)。 您可以選取其中一個預設值，或選取自訂值 (應為30天或更) 。
    - 指定當群組沒有擁有者時應該傳送續訂和到期通知的電子郵件地址。
    - 選取過期的 Microsoft 365 群組。 您可以設定到期日：
      - **全部** Microsoft 365 群組
      - **所選** Microsoft 365 群組的清單
      - **無** 可限制所有群組的到期日
    - 當您完成時，選取 [儲存] 會儲存您的設定。

> [!NOTE]
> - 當您第一次設定到期日時，任何早于到期間隔的群組會設定為35天直到到期為止，除非群組自動更新或擁有者更新該群組。
> - 刪除並還原動態群組時，會將它視為新的群組，並根據規則重新填入。 此程序最多可能需要 24 小時。
> - 小組中所使用群組的到期通知會出現在小組擁有者摘要中。

## <a name="email-notifications"></a>電子郵件通知

如果群組不會自動更新，這類電子郵件通知會在群組到期前的30天、15天和前1天傳送給 Microsoft 365 群組擁有者。 電子郵件的語言取決於群組擁有者的慣用語言或 Azure AD 語言設定。 如果群組擁有者已定義慣用語言，或多個擁有者都有相同的慣用語言，則會使用該語言。 針對其他所有情況，則會使用 Azure AD 語言] 設定。

![到期電子郵件通知](./media/groups-lifecycle/expiration-notification.png)

在 **更新群組** 通知電子郵件中，群組擁有者可以直接存取 [存取面板](https://account.activedirectory.windowsazure.com/r#/applications)中的 [群組詳細資料] 頁面。 使用者可以從該處取得群組的相關詳細資訊，例如其描述、其最後更新時間、其到期時間，還能夠更新群組。 [群組詳細資料] 頁面現在也包含 Microsoft 365 群組資源的連結，讓群組擁有者可以輕鬆地查看其群組中的內容和活動。

當群組到期時，會在到期日隔天刪除群組。 這種電子郵件通知會傳送給 Microsoft 365 群組擁有者，通知他們其 Microsoft 365 群組的到期和後續刪除。

![群組刪除電子郵件通知](./media/groups-lifecycle/deletion-notification.png)

您可以選取 [ **還原群組** ] 或使用 PowerShell Cmdlet，在刪除的30天內還原群組，如 Azure Active Directory 的 [還原已刪除的 Microsoft 365 群組中](groups-restore-deleted.md)所述。 請注意，您無法自訂 30 天的群組還原期間。

如果您要還原的群組包含文件、SharePoint 網站或其他持續物件，則可能需要 24 小時，才能完全還原群組及其內容。

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>如何取出 Microsoft 365 群組到期日

除了可讓使用者查看群組詳細資料的存取面板（包括到期日和上次更新日期）之外，Microsoft 365 群組的到期日也可以從 Microsoft Graph REST API Beta 版中取出。 expirationDateTime 已在 Microsoft Graph Beta 中啟用為群組屬性。 您可以使用 GET 要求進行抓取。 如需詳細資訊，請參閱 [此範例](/graph/api/group-get?view=graph-rest-beta#example)。

> [!NOTE]
> 若要在存取面板上管理群組成員資格，請在 [Azure Active Directory 群組一般] 設定中，將 [限制對存取面板中群組的存取權] 設定為 [否]。

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Microsoft 365 群組到期如何與信箱搭配法定保存

當群組到期而被刪除時，在刪除後的 30 天之後，系統就會將來自應用程式 (例如「行事曆」、「網站」或「小組」) 的群組資料永久刪除，但是會保留法務保留中的群組信箱，而不會永久刪除。 系統管理員可以使用 Exchange Cmdlet 來還原信箱以擷取資料。

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Microsoft 365 群組到期如何與保留原則搭配運作

保留原則是透過「安全性與合規性中心」來設定的。 如果您已為 Microsoft 365 群組設定保留原則，當群組到期且已刪除時，群組信箱中的群組交談和群組網站中的檔案會保留在保留的容器中，保留期原則中所定義的特定天數。 在群組到期後，使用者將看不到該群組及其內容，但可以透過電子文件探索來復原網站和信箱資料。

## <a name="powershell-examples"></a>PowerShell 範例

以下範例說明如何使用 PowerShell Cmdlet 來設定 Azure AD 組織中 Microsoft 365 群組的到期設定：

1. 安裝 PowerShell v2.0 模組，並在 PowerShell 提示字元中登入：

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. 設定到期設定使用 New-AzureADMSGroupLifecyclePolicy Cmdlet，將 Azure AD 組織中所有 Microsoft 365 群組的存留期設定為365天。 沒有擁有者之 Microsoft 365 群組的更新通知將會傳送至 ' emailaddress@contoso.com '
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. 取出現有的原則 Get-azureadmsgrouplifecyclepolicy：此 Cmdlet 會取得已設定的目前 Microsoft 365 群組到期設定。 在此範例中，您可以看見：

   - 原則識別碼
   - Azure AD 組織中所有 Microsoft 365 群組的存留期設定為365天
   - 沒有擁有者之 Microsoft 365 群組的更新通知將會傳送至 ' emailaddress@contoso.com . '
  
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
  
1. 移除現有的原則 Get-azureadmsgrouplifecyclepolicy：此 Cmdlet 會刪除 Microsoft 365 群組到期設定，但需要原則識別碼。 此 Cmdlet 會停用 Microsoft 365 群組的到期日。
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
下列 Cmdlet 可用來更詳細地設定原則。 如需詳細資訊，請參閱 [PowerShell 檔](/powershell/module/azuread/?branch=master&view=azureadps-2.0-preview#groups)。

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
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