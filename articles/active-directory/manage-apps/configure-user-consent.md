---
title: 設定使用者使用 Azure AD 同意應用程式的方式
description: 了解如何管理使用者如何及何時同意可存取貴組織資料的應用程式。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0a508e52189938447ea6fc1928d441d81deab392
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713985"
---
# <a name="configure-how-end-users-consent-to-applications"></a>設定使用者同意應用程式的方式

您可以將應用程式與 Microsoft 身分識別平台整合，以允許使用者使用其公司或學校帳戶登入，並存取貴組織的資料，以提供豐富的資料驅動體驗。

在應用程式可以存取貴組織的資料之前，使用者必須授與應用程式權限。 不同的權限允許不同的存取層級。 根據預設，所有使用者都能同意應用程式，以取得不需要系統管理員同意的權限。 例如，根據預設，使用者可以同意允許應用程式存取其信箱，但是不能同意允許應用程式不受約束地存取，以讀取和寫入貴組織中的所有檔案。

藉由允許使用者將資料的存取權授與應用程式，使用者可以輕鬆地取得有用的應用程式並提高生產力。 不過，在某些情況下，如果未謹慎監視和控制，此設定會是一個風險。

## <a name="user-consent-settings"></a>使用者同意設定

若要控制使用者可以同意應用程式的情況，請選擇將套用至所有使用者的同意原則。 以下是三個同意原則選項：

* **停用使用者同意** - 使用者無法對應用程式授與權限。 使用者可以繼續登入先前已同意或由系統管理員代表他們同意的應用程式，但是不允許他們自行同意新的權限或新的應用程式。 只有已授與目錄角色 (包括授與同意的權限) 的使用者，才能夠同意新的權限或新的應用程式。

* **使用者可以同意來自已驗證發行者的應用程式，但是僅適用於您選取的權限 (預覽)** - 所有使用者只能同意由[已驗證的發行者](../develop/publisher-verification-overview.md)所發行的應用程式，以及在您的租用戶中註冊的應用程式。 使用者只能同意您已分類為「低影響」的權限。

  請務必[分類權限](#configure-permission-classifications-preview)，以選取允許使用者同意的權限。

* **使用者可以同意所有應用程式** - 此選項可讓所有使用者同意任何應用程式的任何權限，而不需要管理員同意。 

   若要降低惡意應用程式嘗試誘騙使用者授與貴組織資料存取權的風險，建議您只允許使用者同意由[已驗證的發行者](../develop/publisher-verification-overview.md)發佈的應用程式。

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>從 Azure 入口網站設定使用者同意設定

若要透過 Azure 入口網站設定使用者同意設定：

1. 以[全域管理員](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 **Azure Active Directory**  >  **企業應用程式**  >  **同意和權限**  >  **使用者同意設定**。
1. 在 [應用程式的使用者同意] 底下，選取您要為所有使用者設定的同意設定。
1. 選取 [儲存] 以儲存您的設定。

![使用者同意設定](./media/configure-user-consent/setting-for-all-users.png)

> [!TIP]
> 請考量[啟用管理員同意工作流程](configure-admin-consent-workflow.md)，讓使用者要求系統管理員檢閱和核准不允許使用者同意的應用程式，例如，當使用者同意已停用，或應用程式要求使用者不被允許授與的權限時。

### <a name="configure-user-consent-settings-using-powershell"></a>使用 PowerShell 設定使用者同意設定

您可以使用最新的 Azure AD PowerShell Preview 模組 [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)，選擇哪一種同意原則可管理應用程式的使用者同意。

* **停用使用者同意** - 若要停用使用者同意，請將管理使用者同意的同意原則設定為空白：

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **允許使用者同意來自已驗證發行者的應用程式、選取的權限 (預覽)** - 若只要允許來自已驗證發行者的應用程式和在您的租用戶中所註冊應用程式的有限使用者同意，且僅針對您分類為「低影響」的權限，請設定名為 `microsoft-user-default-low` 的內建同意原則：

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   請記得[分類權限](#configure-permission-classifications-preview)，以選取允許使用者同意的權限。

* **允許使用者同意所有應用程式** - 允許使用者同意所有應用程式：

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   此選項可讓所有使用者同意任何應用程式的任何權限，而不需要管理員同意。 我們建議您只允許使用者同意來自已驗證發行者的應用程式。

## <a name="configure-permission-classifications-preview"></a>設定權限分類 (預覽)

權限分類可讓您根據組織的原則和風險評估，識別不同權限的影響。 例如，您可以在同意原則中使用權限分類，以識別允許使用者同意的權限集。

> [!NOTE]
> 目前僅支援「低影響」權限分類。 只有不需要管理員同意的委派權限可以分類為「低影響」。

### <a name="classify-permissions-using-the-azure-portal"></a>使用 Azure 入口網站來分類權限

1. 以[全域管理員](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 **Azure Active Directory**  >  **企業應用程式**  >  **同意和權限**  >  **權限分類**。
1. 選擇 [新增權限]，將另一個權限分類為「低影響」。 
1. 選取 API，然後選取委派的權限。

在此範例中，我們已將單一登入所需的最小權限集合分類：

![權限分類](./media/configure-user-consent/permission-classifications.png)

> [!TIP]
> 針對 Microsoft Graph API，執行基本單一登入所需的最小權限是 `openid`、`profile`、`User.Read` 和 `offline_access`。 使用這些權限，即使使用者不再使用應用程式，應用程式可以讀取已登入使用者的設定檔詳細資料，並且可以維護此存取權。

### <a name="classify-permissions-using-powershell"></a>使用 PowerShell 來分類權限

您可以使用最新的 Azure AD PowerShell Preview 模組 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)，來分類權限。 權限分類是在 API (發佈權限) 的 **ServicePrincipal** 物件上設定。

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>若要讀取 API 目前的權限分類：

1. 擷取 API 的 **ServicePrincipal** 物件。 在這裡，我們會擷取 Microsoft Graph API 的 ServicePrincipal 物件：

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 讀取 API 的委派權限分類：

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>若要將權限分類為「低影響」：

1. 擷取 API 的 **ServicePrincipal** 物件。 在這裡，我們會擷取 Microsoft Graph API 的 ServicePrincipal 物件：

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 尋找您想要分類的委派權限：

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. 使用權限名稱和識別碼來設定權限分類：

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>若要移除委派權限分類：

1. 擷取 API 的 **ServicePrincipal** 物件。 在這裡，我們會擷取 Microsoft Graph API 的 ServicePrincipal 物件：

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 尋找您想要移除的委派權限分類：

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. 刪除權限分類：

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>設定群組擁有者同意存取群組資料的應用程式

群組擁有者可以授權應用程式 (例如由第三方廠商發佈的應用程式) 存取與群組相關聯的組織資料。 例如，Microsoft Teams 中的小組擁有者可以允許應用程式讀取小組中的所有 Teams 訊息，或列出群組成員的基本設定檔。

您可以設定允許哪些使用者同意應用程式存取其群組的資料，或者您可以停用這項功能。

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>使用 Azure 入口網站設定群組擁有者同意

1. 以[全域管理員](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 **Azure Active Directory**  >  **企業應用程式**  >  **同意和權限**  >  **使用者同意設定**。
3. 在 [群組擁有者同意存取資料的應用程式] 底下，選取您要啟用的選項。
4. 選取 [儲存] 以儲存您的設定。

在此範例中，所有群組擁有者都可以同意應用程式存取其群組的資料：

![權限分類](./media/configure-user-consent/group-owner-consent.png)

### <a name="configure-group-owner-consent-using-powershell"></a>使用 PowerShell 設定群組擁有者同意

您可以使用 Azure AD PowerShell Preview 模組 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)，啟用或停用群組擁有者同意應用程式存取其所擁有貴組織群組資料的能力。

1. 請確定您使用的是 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) 模組。 如果您已安裝 [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) 模組和 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) 模組兩者，則這個步驟很重要。

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

## <a name="configure-risk-based-step-up-consent"></a>設定以風險為基礎的升級同意

以風險為基礎的升級同意有助於降低使用者暴露於進行[非法同意要求](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)的惡意應用程式風險。 如果 Microsoft 偵測到有風險的使用者同意要求，則要求需要「升級」以進行管理員同意。 這項功能預設為啟用，但是只有在啟用使用者同意時，才會導致行為變更。

當偵測到有風險的同意要求時，同意提示會顯示一則訊息，指出需要系統管理員核准。 如果已啟用[管理員同意要求工作流程](configure-admin-consent-workflow.md)，則使用者可以將要求傳送給系統管理員，以便直接從同意提示進行進一步的檢閱。 如果未啟用，將會顯示下列訊息：

* **AADSTS90094：** &lt;clientAppDisplayName&gt; 需要只有系統管理員才能授與的權限來存取貴組織中的資源。 請先要求系統管理員授與此應用程式的權限，然後您才能使用。

在此情況下，也會記錄稽核事件，具有「ApplicationManagement」類別、「同意應用程式」的活動類型和「偵測到有風險應用程式」的狀態原因。

> [!IMPORTANT]
> 在核准要求之前，系統管理員應該先仔細[評估所有同意要求](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent)，特別是當 Microsoft 偵測到風險時。

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>使用 PowerShell 來停用或重新啟用以風險為基礎的升級同意

您可以使用 Azure AD PowerShell Preview 模組 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)，停用在 Microsoft 偵測到風險的情況下必要的升級至管理員同意，或是在先前停用時重新啟用。

您可以使用如上所述適用於[使用 PowerShell 設定群組擁有者同意](#configure-group-owner-consent-using-powershell)的相同步驟，但是以不同的設定值替代，來完成此操作。 步驟有三個差異： 

1. 了解以風險為基礎升級同意的設定值：

    | 設定       | 類型         | 描述  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  指出偵測到有風險的要求時，是否將封鎖使用者同意的旗標。 |

1. 替代步驟 3 中的下列值：

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. 替代步驟 5 中的下列其中一項：

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>後續步驟

若要深入了解：

* [設定管理員同意工作流程](configure-admin-consent-workflow.md)
* [了解如何管理對應用程式的同意及評估同意要求](manage-consent-requests.md)
* [對應用程式授與全租用戶的管理員同意](grant-admin-consent.md)
* [Microsoft 身分識別平台中的權限和同意](../develop/active-directory-v2-scopes.md)

若要取得協助或尋找您的問題解答：
* [StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
