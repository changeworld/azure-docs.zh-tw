---
title: 為組分配敏感度標籤 - Azure AD |微軟文檔
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329727"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>在 Azure 活動目錄中為 Office 365 組分配敏感度標籤（預覽）

Azure 活動目錄 （Azure AD） 支援將[Microsoft 365 合規性中心](https://sip.protection.office.com/homepage)發佈的敏感度標籤應用於 Office 365 組。 敏感度標籤適用于跨服務（如 Outlook、Microsoft 團隊和 SharePoint）的分組。 此功能目前為公開預覽狀態。 有關支援 Office 365 應用的詳細資訊，請參閱[Office 365 支援敏感度標籤](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)。

> [!IMPORTANT]
> 要配置此功能，Azure AD 組織中必須至少有一個活動 Azure 活動目錄高級 P1 許可證。

## <a name="enable-sensitivity-label-support-in-powershell"></a>在 PowerShell 中啟用靈敏度標籤支援

要將已發佈的標籤應用於組，必須首先啟用該功能。 這些步驟啟用 Azure AD 中的功能。

1. 在電腦上開啟 Windows PowerShell 視窗。 不需較高的權限即可將它開啟。
1. 執行下列命令，以準備執行 Cmdlet。

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    在"**登錄到您的帳戶**"頁中，輸入管理員帳戶和密碼以將您連接到服務，然後選擇"**登錄**"。
1. 獲取 Azure AD 組織的當前組設置。

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > 如果尚未為此 Azure AD 組織創建組設置，則必須首先創建這些設置。 按照 Azure[活動目錄 Cmdlet 中的步驟配置組設置](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets)，為此 Azure AD 組織創建組設置。

1. 接下來，顯示當前組設置。

    ```PowerShell
    $Setting.Values
    ```

1. 然後啟用該功能：

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. 然後保存更改並應用設置：

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

就這麼簡單。 您已啟用該功能，您可以將已發佈的標籤應用於組。

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>將標籤分配給 Azure 門戶中的新組

1. 登錄到 Azure [AD 管理中心](https://aad.portal.azure.com)。
1. 選擇**組**，然後選擇 **"新群組**"。
1. 在新**組**頁上，選擇**Office 365，** 然後填寫新組所需的資訊，然後從清單中選擇敏感度標籤。

   ![在"新群組"頁中分配敏感度標籤](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. 保存更改並選擇"**創建**"。

創建組，然後自動強制執行與所選標籤關聯的網站和組設置。

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>將標籤分配給 Azure 門戶中的現有組

1. 使用組管理員帳戶或組擁有者登錄到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 選取 [群組]****。
1. 在"**所有組"** 頁上，選擇要標記的組。
1. 在所選組的頁面上，選擇 **"屬性"** 並從清單中選擇敏感度標籤。

   ![在組概覽頁上分配敏感度標籤](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. 選取 [儲存]**** 來儲存變更。

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>從 Azure 門戶中的現有組中刪除標籤

1. 使用全域管理員或組管理員帳戶或組擁有者登錄到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 選取 [群組]****。
1. 從"**所有組"** 頁面中，選擇要從中刪除標籤的組。
1. 在 **"組"** 頁上，選擇 **"屬性**"。
1. 選取 [移除]****。
1. 選取 [儲存]**** 以套用變更。

## <a name="using-classic-azure-ad-classifications"></a>使用經典 Azure AD 分類

啟用此功能後，組的"經典"分類將僅顯示現有組和網站，並且僅當在不支援敏感度標籤的應用中創建組時，才應將其用於新組。 如果需要，管理員可以稍後將其轉換為敏感度標籤。 經典分類是通過在 Azure AD PowerShell 中定義`ClassificationList`設置的值來設置的舊分類。 啟用此功能後，這些分類將不會應用於組。

## <a name="troubleshooting-issues"></a>針對問題進行疑難排解

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>敏感度標籤不適用於組上的分配

僅當滿足以下所有條件時，才會為組顯示敏感度標籤選項：

1. 標籤發佈在 Microsoft 365 符合性中心，用於此租戶。
1. 此功能已啟用，啟用MIP標籤在 PowerShell 中設置為 True。
1. 該組是 Office 365 組。
1. 租戶具有活動 Azure 活動目錄高級 P1 許可證。
1. 當前登錄使用者具有分配標籤的足夠許可權。 使用者必須是全域管理員、組管理員或組擁有者。

請確保滿足所有條件，以便將標籤分配給組。

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>要分配的標籤不在清單中

如果您要查找的標籤不在清單中，則可能出於以下原因之一：

- 該標籤可能不會在 Microsoft 365 合規性中心發佈。 這也適用于不再發佈的標籤。 有關詳細資訊，請諮詢您的管理員。
- 標籤可以發佈，但是，它不適用於已登錄的使用者。 有關如何訪問標籤的詳細資訊，請諮詢管理員。

### <a name="how-to-change-the-label-on-a-group"></a>如何更改組上的標籤

標籤可以隨時使用與向現有組分配標籤相同的步驟進行交換，如下所示：

1. 使用全域或組管理員帳戶或組擁有者登錄到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 選取 [群組]****。
1. 在"**所有組"** 頁上，選擇要標記的組。
1. 在所選組的頁面上，選擇 **"屬性"** 並從清單中選擇新的敏感度標籤。
1. 選取 [儲存]****。

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>未在組上更新對已發佈標籤的組設置更改

最佳做法是，不建議在標籤應用於組後更改標籤的組設置。 當您對 Microsoft [365 合規性中心](https://sip.protection.office.com/homepage)中與已發佈標籤關聯的組設置進行更改時，這些策略更改不會自動應用於受影響的組。

如果必須進行更改，請使用[Azure AD PowerShell 腳本](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)手動將更新應用於受影響的組。 此方法確保所有現有組強制執行新設置。

## <a name="next-steps"></a>後續步驟

- [將敏感度標籤與 Microsoft 團隊、Office 365 組和 SharePoint 網站一起使用](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [使用 Azure AD PowerShell 腳本手動更改標籤策略後更新組](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [編輯群組設定](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [使用 PowerShell 命令管理群組](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
