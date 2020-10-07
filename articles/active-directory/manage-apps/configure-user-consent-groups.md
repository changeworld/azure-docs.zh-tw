---
title: 使用 Azure AD 將群組擁有者同意設定至存取群組資料的應用程式
description: 學習管理群組和小組擁有者是否可以同意可存取群組或小組資料的應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: e590981fabcd20f23f25d12b4176b6730cb0fc3c
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804298"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>設定群組擁有者同意存取群組資料的應用程式

群組和小組擁有者可以授權應用程式，例如協力廠商發行的應用程式，以存取您組織與群組相關聯的資料。 例如，Microsoft Teams 中的小組擁有者可以允許應用程式讀取小組中的所有 Teams 訊息，或列出群組成員的基本設定檔。 若要深入瞭解，請參閱 [Microsoft 小組中的資源特定同意](https://docs.microsoft.com/microsoftteams/resource-specific-consent) 。

## <a name="manage-group-owner-consent-to-apps"></a>管理應用程式的群組擁有者同意

您可以設定哪些使用者同意存取其群組或小組資料的應用程式，或者您可以為所有使用者停用此設定。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

遵循下列步驟來管理群組擁有者對存取群組資料的應用程式的同意：

1. 以[全域管理員](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 **Azure Active Directory**  >  **企業應用程式**  >  **同意和權限**  >  **使用者同意設定**。
3. 在 [群組擁有者同意存取資料的應用程式] 底下，選取您要啟用的選項。
4. 選取 [儲存] 以儲存您的設定。

在此範例中，所有群組擁有者都可以同意應用程式存取其群組的資料：

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="群組擁有者同意設定":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

您可以使用 Azure AD PowerShell Preview 模組 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)，啟用或停用群組擁有者同意應用程式存取其所擁有貴組織群組資料的能力。

1. 請確定您使用的是 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 模組。 如果您已安裝 [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true) 模組和 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 模組兩者，則這個步驟很重要。

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. 連線至 Azure AD PowerShell。

   ```powershell
   Connect-AzureAD
   ```

1. 擷取您租用戶中**同意原則設定**目錄設定的目前值。 這需要檢查是否已建立這項功能的目錄設定，如果未建立，則使用對應目錄設定範本中的值。

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

1. 了解設定值。 有兩個設定值可定義哪些使用者可以允許應用程式存取其群組的資料：

    | 設定       | 類型         | 描述  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean | 表示是否允許群組擁有者授與群組特定權限的旗標。 |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | 如果 _EnableGroupSpecificConsent_ 設定為 "True"，且此值設定為群組的物件識別碼，則已識別群組的成員會獲得授權，將群組特定權限授與他們所擁有的群組。 |

1. 更新所需設定的設定值：

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

1. 儲存您的設定。

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

---

## <a name="next-steps"></a>後續步驟

若要深入了解：

* [設定使用者同意設定](configure-user-consent.md)
* [設定管理員同意工作流程](configure-admin-consent-workflow.md)
* [了解如何管理對應用程式的同意及評估同意要求](manage-consent-requests.md)
* [對應用程式授與全租用戶的管理員同意](grant-admin-consent.md)
* [Microsoft 身分識別平台中的權限和同意](../develop/active-directory-v2-scopes.md)

若要取得協助或尋找您的問題解答：
* [StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
