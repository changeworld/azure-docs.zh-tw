---
title: 配置最終使用者如何同意使用 Azure AD 的應用程式
description: 瞭解如何管理使用者如何以及何時同意可以訪問組織資料的應用程式。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd305d2943d1b12756171748f28d32300081d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443398"
---
# <a name="configure-how-end-users-consent-to-applications"></a>配置最終使用者如何同意應用程式

應用程式可以與 Microsoft 標識平臺集成，以允許使用者在 Azure 活動目錄 （Azure AD） 中使用其工作或學校帳戶登錄，並訪問組織的資料以提供豐富的資料驅動體驗。 不同的許可權允許應用程式對使用者和組織資料進行不同級別的訪問。

預設情況下，使用者可以同意訪問組織資料的應用程式，儘管僅限於某些許可權。 例如，預設情況下，使用者可以同意允許應用訪問其自己的郵箱或使用者所屬團隊的 Teams 對話，但不能同意允許無人參與的應用訪問讀取和寫入組織中的所有 SharePoint 網站。 雖然允許使用者自行同意，但允許使用者輕鬆獲取與 Microsoft 365、Azure 和其他服務集成的有用應用程式，但如果不仔細使用和監視，則可能會構成風險。

Microsoft 建議禁用將來的使用者同意操作，以説明減少您的表面積並減輕此風險。 如果禁用使用者同意，以前的同意授予仍將得到遵守，但管理員必須執行以後的所有同意操作。 使用者可以通過集成[的管理員同意請求工作流](configure-admin-consent-workflow.md)或通過您自己的支援流程請求租戶範圍的管理員同意。 有關詳細資訊[，請參閱保護標識基礎結構的五個步驟](../../security/fundamentals/steps-secure-identity.md)。

## <a name="configure-user-consent-to-applications"></a>配置使用者對應用程式的同意
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>禁用或啟用 Azure 門戶的使用者同意

可以使用 Azure 門戶禁用或啟用使用者同意訪問組織資料的應用程式的許可權：

1. 以[全域管理員](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)身份登錄到[Azure 門戶](https://portal.azure.com)。
2. 選擇**Azure 活動目錄**，然後選擇**企業應用程式**，然後**選擇使用者設置**。
3. 啟用或禁用使用者同意與控制項標記**使用者可以同意應用程式訪問公司資料代表他們**。
4. （可選）配置[管理員同意請求工作流](configure-admin-consent-workflow.md)，以確保不允許同意應用的使用者可以請求批准。

> [!TIP]
> 允許使用者要求管理員審閱不允許使用者同意的應用程式（例如，因為使用者同意已被禁用，或者因為應用程式請求不允許使用者授予的許可權），請考慮[建構管理員同意工作流](configure-admin-consent-workflow.md)。

### <a name="disable-or-enable-user-consent-using-powershell"></a>使用 PowerShell 禁用或啟用使用者同意

您可以使用 Azure AD PowerShell v1 模組[（MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)） 來啟用或禁用使用者同意訪問組織資料的應用程式的能力。

1. 通過運行此 Cmdlet 登錄到您的組織：

    ```powershell
    Connect-MsolService
    ```

2. 通過運行此 Cmdlet 檢查是否啟用了使用者同意：

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. 啟用或禁用使用者同意。 例如，要禁用使用者同意，請運行此 Cmdlet：

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>配置組擁有者同意訪問組資料的應用

> [!IMPORTANT]
> 以下資訊用於即將推出的功能，該功能將允許組擁有者授予應用程式對其組資料的訪問。 釋放此功能時，預設情況下將啟用此功能。 儘管此功能尚未廣泛發佈，但您可以使用這些說明在功能發佈之前禁用該功能。

組擁有者可以授權應用程式（例如，由協力廠商供應商發佈的應用程式）訪問組織與組關聯的資料。 例如，團隊擁有者（是團隊 Office 365 組的擁有者）可以允許應用讀取團隊中的所有 Teams 消息，或列出組成員的基本設定檔。

> [!NOTE]
> 與此設置無關，始終允許組擁有者將其他使用者或應用直接添加為組擁有者。

### <a name="configure-group-owner-consent-using-powershell"></a>使用 PowerShell 配置組擁有者同意

您可以使用 Azure AD PowerShell 預覽模組[（AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)） 來啟用或禁用組擁有者同意訪問組織為其擁有的組訪問組織資料的能力。

1. 確保使用[AzureAD 預覽](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)模組（如果同時安裝了[AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0)模組和[AzureAD 預覽](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)模組，則此步驟非常重要）。

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. 連接到 Azure AD 電源外殼。

   ```powershell
   Connect-AzureAD
   ```

3. 檢索租戶中的 *"同意策略設置"目錄設置*的當前值。 這要求檢查是否創建了此功能的目錄設置，如果沒有，則使用相應目錄設置範本中的值。

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

4. 瞭解設置值。 有兩個設置值定義哪些使用者能夠允許應用訪問其組的資料：

    | 設定       | 類型         | 描述  |
    | ------------- | ------------ | ------------ |
    | _啟用組特定同意_   | Boolean |  指示是否允許組擁有者授予特定于組的許可權的標誌。 |
    | _約束組特定同意組成員組 ID_ | Guid | 如果_啟用組特定同意_設置為"True"，並且此值設置為組的物件識別碼，則標識的組成員將有權向其擁有的組授予特定于組的許可權。 |

5. 更新所需配置的設置值：

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

6. 保存設置。

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>後續步驟

[建構管理員同意工作流](configure-admin-consent-workflow.md)

[授予租戶範圍管理員對應用程式的同意](grant-admin-consent.md)

[Microsoft 身份平臺中的許可權和同意](../develop/active-directory-v2-scopes.md)

[堆疊溢位上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
