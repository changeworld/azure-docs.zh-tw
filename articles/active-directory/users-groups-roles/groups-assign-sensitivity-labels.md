---
title: 將敏感度標籤指派給群組-Azure AD |Microsoft Docs
description: 如何建立成員資格規則，以自動填入群組和規則參考。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/24/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 667fb39aabfec14cff01221b82a45ba8ad1d68d4
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78329727"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>在 Azure Active Directory 中將敏感度標籤指派給 Office 365 群組（預覽）

Azure Active Directory （Azure AD）支援將[Microsoft 365 合規性中心](https://sip.protection.office.com/homepage)發佈的敏感度標籤套用至 Office 365 群組。 敏感度標籤會套用至各種服務的群組，例如 Outlook、Microsoft 小組和 SharePoint。 此功能目前為公開預覽狀態。 如需 Office 365 應用程式支援的詳細資訊，請參閱[適用于敏感度標籤的 office 365 支援](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)。

> [!IMPORTANT]
> 若要設定此功能，您的 Azure AD 組織中至少必須有一個 active Azure Active Directory Premium P1 授權。

## <a name="enable-sensitivity-label-support-in-powershell"></a>啟用 PowerShell 中的敏感度標籤支援

若要將已發行的標籤套用至群組，您必須先啟用此功能。 這些步驟會啟用 Azure AD 中的功能。

1. 在電腦上開啟 Windows PowerShell 視窗。 不需較高的權限即可將它開啟。
1. 執行下列命令，以準備執行 Cmdlet。

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    在 [登**入您的帳戶**] 頁面上，輸入您的系統管理員帳戶和密碼，以將您連線到您的服務，然後選取 [登**入**]。
1. 提取 Azure AD 組織目前的群組設定。

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > 如果尚未為此 Azure AD 組織建立任何群組設定，您必須先建立設定。 請遵循 Azure Active Directory Cmdlet 中的步驟來[進行群組設定](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets)，以建立此 Azure AD 組織的群組設定。

1. 接下來，顯示目前的群組設定。

    ```PowerShell
    $Setting.Values
    ```

1. 然後啟用功能：

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. 然後儲存變更並套用設定：

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

就這麼簡單。 您已啟用此功能，而且可以將已發行的標籤套用至群組。

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>在 Azure 入口網站中將標籤指派給新群組

1. 登入 Azure AD 系統[管理中心](https://aad.portal.azure.com)。
1. 選取 [**群組**]，然後選取 [**新增群組**]。
1. 在 [**新增群組**] 頁面上，選取 [ **Office 365**]，然後填寫新群組所需的資訊，並從清單中選取 [敏感度] 標籤。

   ![在 [新增群組] 頁面中指派敏感度標籤](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. 儲存您的變更，然後選取 [**建立**]。

系統會建立您的群組，然後自動強制執行與所選標籤相關聯的網站和群組設定。

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>將標籤指派給 Azure 入口網站中的現有群組

1. 以群組管理帳戶或群組擁有者身分登入[Azure AD 系統管理中心](https://aad.portal.azure.com)。
1. 選取 [群組]。
1. 從 [**所有群組**] 頁面中，選取您要加上標籤的群組。
1. 在 [選取的群組] 頁面上，選取 [**屬性**]，並從清單中選取 [敏感度] 標籤。

   ![在群組的 [總覽] 頁面上指派敏感度標籤](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. 選取 [儲存] 來儲存變更。

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>從 Azure 入口網站中的現有群組移除標籤

1. 使用全域管理員或群組系統管理員帳戶，或以群組擁有者身分登入[Azure AD 系統管理中心](https://aad.portal.azure.com)。
1. 選取 [群組]。
1. 從 [**所有群組**] 頁面中，選取您想要移除標籤的群組。
1. 在 [**群組**] 頁面上，選取 [**屬性**]。
1. 選取 [移除]。
1. 選取 [儲存] 以套用變更。

## <a name="using-classic-azure-ad-classifications"></a>使用傳統 Azure AD 分類

啟用此功能之後，群組的「傳統」分類將只會顯示現有的群組和網站，而只有在在不支援敏感度標籤的應用程式中建立群組時，才應該將它們用於新群組。 您的系統管理員可以稍後視需要將它們轉換成敏感度標籤。 傳統分類是您藉由在 Azure AD PowerShell 中定義 [`ClassificationList`] 設定的值而設定的舊分類。 啟用這項功能時，這些分類將不會套用至群組。

## <a name="troubleshooting-issues"></a>疑難排解問題

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>不能在群組上指派敏感度標籤

只有在符合下列所有條件時，才會顯示群組的 [敏感度標籤] 選項：

1. 標籤會在此租使用者的 Microsoft 365 合規性中心發佈。
1. 已啟用此功能，PowerShell 中的 EnableMIPLabels 會設定為 True。
1. 此群組是 Office 365 群組。
1. 租使用者具有 active Azure Active Directory Premium P1 授權。
1. 目前已登入的使用者有足夠的許可權可指派標籤。 使用者必須是全域管理員、群組管理員或群組擁有者。

請確定符合所有條件，才能將標籤指派給群組。

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>我想要指派的標籤不在清單中

如果您要尋找的標籤不在清單中，可能是下列其中一個原因所造成的情況：

- 標籤可能不會在 Microsoft 365 合規性中心發佈。 這也適用于不再發行的標籤。 如需詳細資訊，請洽詢您的系統管理員。
- 標籤可能會發佈，但無法供登入的使用者使用。 請洽詢您的系統管理員，以取得如何存取標籤的詳細資訊。

### <a name="how-to-change-the-label-on-a-group"></a>如何變更群組上的標籤

標籤可以使用與將標籤指派給現有群組相同的步驟隨時交換，如下所示：

1. 使用全域或群組系統管理員帳戶或群組擁有者登入[Azure AD 系統管理中心](https://aad.portal.azure.com)。
1. 選取 [群組]。
1. 從 [**所有群組**] 頁面中，選取您要加上標籤的群組。
1. 在 [選取的群組] 頁面上，選取 [**屬性**]，並從清單中選取新的 [敏感度] 標籤。
1. 選取 [儲存]。

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>群組設定變更已發行的標籤不會在群組上更新

基於最佳作法，我們不建議您在將標籤套用至群組之後，變更標籤的群組設定。 當您對[Microsoft 365 合規性中心](https://sip.protection.office.com/homepage)內已發行標籤相關聯的群組設定進行變更時，這些原則變更不會自動套用至受影響的群組。

如果您必須進行變更，請使用[Azure AD PowerShell 腳本](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)，以手動方式將更新套用至受影響的群組。 這個方法可確保所有現有的群組都會強制執行新的設定。

## <a name="next-steps"></a>後續步驟

- [搭配 Microsoft 小組、Office 365 群組和 SharePoint 網站使用敏感度標籤](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [使用 Azure AD PowerShell 腳本，以手動方式變更標籤原則之後更新群組](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [編輯群組設定](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [使用 PowerShell 命令管理群組](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
