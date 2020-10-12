---
title: 將敏感度標籤指派給群組-Azure AD |Microsoft Docs
description: 瞭解如何將敏感度標籤指派給群組。 請參閱疑難排解資訊並查看其他可用的資源。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3179bb294678ee030218e67dafa1c69dcf5d77a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056263"
---
# <a name="assign-sensitivity-labels-to-microsoft-365-groups-in-azure-active-directory"></a>將敏感度標籤指派給 Azure Active Directory 中的 Microsoft 365 群組

Azure Active Directory (Azure AD) 支援將 [Microsoft 365 合規性中心](https://sip.protection.office.com/homepage) 所發佈的敏感度標籤套用至 Microsoft 365 群組。 敏感度標籤適用于所有服務的群組，例如 Outlook、Microsoft 小組和 SharePoint。 這項功能目前為公開 GA。 如需 Microsoft 365 apps 支援的詳細資訊，請參閱 [Microsoft 365 支援敏感度標籤](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)。

> [!IMPORTANT]
> 若要設定此功能，您的 Azure AD 組織中至少必須有一個 active Azure Active Directory Premium P1 授權。

## <a name="enable-sensitivity-label-support-in-powershell"></a>在 PowerShell 中啟用敏感度標籤支援

若要將已發行的標籤套用至群組，您必須先啟用此功能。 這些步驟會啟用 Azure AD 中的功能。

1. 在電腦上開啟 Windows PowerShell 視窗。 不需較高的權限即可將它開啟。
1. 執行下列命令，以準備執行 Cmdlet。

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    在 [登 **入您的帳戶** ] 頁面上，輸入您的系統管理員帳戶和密碼，以將您連線到您的服務，然後選取 [登 **入**]。
1. 提取 Azure AD 組織目前的群組設定。

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > 如果未針對此 Azure AD 組織建立任何群組設定，您必須先建立設定。 遵循 Azure Active Directory Cmdlet 中的步驟來 [設定群組設定](./groups-settings-cmdlets.md) ，以建立這個 Azure AD 組織的群組設定。

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

您也需要同步處理您的敏感度標籤以 Azure AD。 如需相關指示，請參閱 [如何啟用容器的敏感度標籤和同步處理標籤](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide#how-to-enable-sensitivity-labels-for-containers-and-synchronize-labels)。

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>將標籤指派給 Azure 入口網站中的新群組

1. 登入 Azure AD 系統 [管理中心](https://aad.portal.azure.com)。
1. 選取 [ **群組**]，然後選取 [ **新增群組**]。
1. 在 [ **新增群組** ] 頁面上，選取 [ **Office 365**]，然後填寫新群組所需的資訊，然後從清單中選取敏感度標籤。

   ![在 [新增群組] 頁面中指派敏感度標籤](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. 儲存您的變更，然後選取 [ **建立**]。

系統會建立您的群組，然後自動強制執行與所選標籤相關聯的網站和群組設定。

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>在 Azure 入口網站中將標籤指派給現有的群組

1. 使用群組系統管理員帳戶或群組擁有者登入 [Azure AD 系統管理中心](https://aad.portal.azure.com) 。
1. 選取 [群組]。
1. 在 [ **所有群組** ] 頁面上，選取您要加上標籤的群組。
1. 在所選群組的頁面上，選取 [ **屬性** ]，然後從清單中選取敏感度標籤。

   ![在群組的 [總覽] 頁面上指派敏感度標籤](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. 選取 [儲存] 來儲存變更。

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>從 Azure 入口網站中的現有群組移除標籤

1. 以全域管理員或群組系統管理員帳戶或群組擁有者身分登入 [Azure AD admin center](https://aad.portal.azure.com) 。
1. 選取 [群組]。
1. 從 [ **所有群組** ] 頁面中，選取您要從中移除標籤的群組。
1. 在 [ **群組** ] 頁面上，選取 [ **屬性**]。
1. 選取 [移除]。
1. 選取 [儲存] 來套用您的變更。

## <a name="using-classic-azure-ad-classifications"></a>使用傳統 Azure AD 分類

啟用這項功能之後，群組的「傳統」分類將只會顯示現有的群組和網站，而且只有在不支援敏感度標籤的應用程式中建立群組時，才應該將它們用於新的群組。 您的系統管理員可以稍後視需要將其轉換為敏感度標籤。 傳統分類是您藉由 `ClassificationList` 在 Azure AD PowerShell 中定義設定的值，所設定的舊分類。 啟用此功能時，將不會對群組套用這些分類。

## <a name="troubleshooting-issues"></a>針對問題進行疑難排解

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>無法在群組上指派敏感度標籤

只有在符合下列所有條件時，才會顯示群組的 [敏感度標籤] 選項：

1. 標籤會在此 Azure AD 組織的 Microsoft 365 合規性中心內發佈。
1. 這項功能已啟用，Azure AD PowerShell 模組中的 EnableMIPLabels 設定為 True。
1. Lables 會使用安全性 & 合規性 PowerShell 模組中的 Execute-AzureAdLabelSync Cmdlet，同步處理至 Azure AD。
1. 群組是 Microsoft 365 群組。
1. 組織具有 active Azure Active Directory Premium P1 授權。
1. 目前已登入的使用者有足夠的許可權可指派標籤。 使用者必須是全域管理員、群組系統管理員或群組擁有者。

請確認符合所有條件，才能將標籤指派給群組。

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>我要指派的標籤不在清單中

如果您要尋找的標籤不在清單中，則可能是下列其中一個原因所致：

- 標籤可能不會在 Microsoft 365 合規性中心內發佈。 這也適用于不再發佈的標籤。 如需詳細資訊，請洽詢您的系統管理員。
- 您可以發佈標籤，但它無法供已登入的使用者使用。 如需如何取得標籤存取權的詳細資訊，請洽詢您的系統管理員。

### <a name="how-to-change-the-label-on-a-group"></a>如何變更群組上的標籤

您可以使用與將標籤指派給現有群組相同的步驟，隨時交換標籤，如下所示：

1. 使用全域或群組系統管理員帳戶或群組擁有者登入 [Azure AD admin center](https://aad.portal.azure.com) 。
1. 選取 [群組]。
1. 在 [ **所有群組** ] 頁面上，選取您要加上標籤的群組。
1. 在所選群組的頁面上，選取 [ **屬性** ]，然後從清單中選取新的敏感度標籤。
1. 選取 [儲存]****。

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>群組的設定變更已發行的標籤不會更新

最佳做法是，建議您在將標籤套用至群組之後，變更標籤的群組設定。 當您對 [Microsoft 365 合規性中心](https://sip.protection.office.com/homepage)中已發佈標籤相關聯的群組設定進行變更時，這些原則變更不會自動套用到受影響的群組。

如果您必須進行變更，請使用 [Azure AD PowerShell 腳本](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) ，手動將更新套用到受影響的群組。 這個方法可確保所有現有的群組都強制執行新的設定。

## <a name="next-steps"></a>後續步驟

- [搭配 Microsoft 小組、Microsoft 365 群組和 SharePoint 網站使用敏感度標籤](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [使用 Azure AD PowerShell 腳本手動變更標籤原則之後更新群組](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [編輯群組設定](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [使用 PowerShell 命令管理群組](./groups-settings-v2-cmdlets.md)
