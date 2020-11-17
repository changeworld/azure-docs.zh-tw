---
title: 設定使用者使用 Azure AD 同意應用程式的方式
description: 了解如何管理使用者如何及何時同意可存取貴組織資料的應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperfq2
ms.openlocfilehash: 1617015d6d4a026d5dadda667dcd03447a20c288
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649495"
---
# <a name="configure-how-end-users-consent-to-applications"></a>設定使用者同意應用程式的方式

您可以將應用程式與 Microsoft 身分識別平台整合，以允許使用者使用其公司或學校帳戶登入，並存取貴組織的資料，以提供豐富的資料驅動體驗。

在應用程式可以存取貴組織的資料之前，使用者必須授與應用程式權限。 不同的權限允許不同的存取層級。 根據預設，所有使用者都能同意應用程式，以取得不需要系統管理員同意的權限。 例如，根據預設，使用者可以同意允許應用程式存取其信箱，但是不能同意允許應用程式不受約束地存取，以讀取和寫入貴組織中的所有檔案。

藉由允許使用者將資料的存取權授與應用程式，使用者可以輕鬆地取得有用的應用程式並提高生產力。 不過，在某些情況下，如果未謹慎監視和控制，此設定會是一個風險。

> [!IMPORTANT]
> 若要降低惡意應用程式嘗試誘騙使用者授與貴組織資料存取權的風險，建議您只允許使用者同意由[已驗證的發行者](../develop/publisher-verification-overview.md)發佈的應用程式。

## <a name="user-consent-settings"></a>使用者同意設定

應用程式同意原則會描述必須符合才能同意應用程式的條件。 這些原則可能包含應用程式要求存取的條件，以及應用程式所要求的許可權。

藉由選擇適用于所有使用者的應用程式同意原則，您可以設定允許使用者同意應用程式的許可權，以及何時需要要求系統管理員審查和核准的限制：

* **停用使用者同意** - 使用者無法對應用程式授與權限。 使用者可以繼續登入先前已同意或由系統管理員代表他們同意的應用程式，但是不允許他們自行同意新的權限或新的應用程式。 只有被授與授與同意之目錄角色的使用者，才能夠同意新的應用程式。

* **使用者可以同意來自已驗證發行者或您組織的應用程式，但僅針對您選取的許可權，** 所有使用者只能同意已 [驗證的發行者](../develop/publisher-verification-overview.md) 和您租使用者中註冊之應用程式所發佈的應用程式。 使用者只能同意您已分類為「低影響」的許可權。 您必須 [分類許可權](configure-permission-classifications.md) ，以選取允許使用者同意的許可權。

* **使用者可以同意所有** 應用程式-此選項可讓所有使用者同意任何應用程式的任何不需要系統管理員同意的許可權。

* **自訂應用程式同意原則** ：若要更多選項來管理使用者同意的條件，您可以 [建立自訂應用程式同意原則](manage-app-consent-policies.md#create-a-custom-app-consent-policy)，並將其設定為適用于使用者同意。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

若要透過 Azure 入口網站設定使用者同意設定：

1. 以[全域管理員](../roles/permissions-reference.md#global-administrator--company-administrator)身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 **Azure Active Directory**  >  **企業應用程式**  >  **同意和權限**  >  **使用者同意設定**。
1. 在 [應用程式的使用者同意] 底下，選取您要為所有使用者設定的同意設定。
1. 選取 [儲存] 以儲存您的設定。

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="使用者同意設定":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

您可以使用最新的 Azure AD PowerShell Preview 模組 [AzureADPreview](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview)，選擇哪個應用程式同意原則會控管應用程式的使用者同意。

#### <a name="disable-user-consent"></a>停用使用者同意

若要停用使用者同意，請將管理使用者同意的同意原則設為空白：

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>允許使用者同意遵守應用程式同意原則

若要允許使用者同意，請選擇要將同意授與應用程式的應用程式同意原則應管理使用者的授權：

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

將取代 `{consent-policy-id}` 為您想要套用的原則識別碼。 您可以選擇您所建立的 [自訂應用程式同意原則](manage-app-consent-policies.md#create-a-custom-app-consent-policy) ，也可以從下列內建原則中選擇：

| ID | 描述 |
|:---|:------------|
| microsoft-使用者-預設-低 | **針對選取的許可權，允許來自已驗證發行者的應用程式使用者同意**<br /> 僅限已驗證的發行者和您租使用者中註冊之應用程式的應用程式，以及您分類為「低影響」的許可權，才允許限制使用者同意。  (別忘了將 [許可權分類](configure-permission-classifications.md) ，以選取允許使用者同意的許可權。 )  |
| microsoft-使用者-預設-舊版 | **允許使用者同意應用程式**<br /> 此選項可讓所有使用者同意任何應用程式的任何不需要系統管理員同意的許可權 |
  
例如，若要讓使用者同意受制于內建原則 `microsoft-user-default-low` ：

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [啟用管理員同意工作流程](configure-admin-consent-workflow.md) ，讓使用者可以要求系統管理員對使用者的評論和核准，要求使用者同意，例如，當使用者同意已停用或應用程式要求使用者不允許授與許可權時。

## <a name="risk-based-step-up-consent"></a>以風險為基礎的逐步同意

以風險為基礎的升級同意有助於降低使用者暴露於進行[非法同意要求](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)的惡意應用程式風險。 如果 Microsoft 偵測到有風險的使用者同意要求，則要求需要「升級」以進行管理員同意。 這項功能預設為啟用，但是只有在啟用使用者同意時，才會導致行為變更。

當偵測到有風險的同意要求時，同意提示會顯示一則訊息，指出需要系統管理員核准。 如果已啟用[管理員同意要求工作流程](configure-admin-consent-workflow.md)，則使用者可以將要求傳送給系統管理員，以便直接從同意提示進行進一步的檢閱。 如果未啟用，將會顯示下列訊息：

* **AADSTS90094：** &lt;clientAppDisplayName&gt; 需要只有系統管理員才能授與的權限來存取貴組織中的資源。 請先要求系統管理員授與此應用程式的權限，然後您才能使用。

在此情況下，也會記錄稽核事件，具有「ApplicationManagement」類別、「同意應用程式」的活動類型和「偵測到有風險應用程式」的狀態原因。

> [!IMPORTANT]
> 在核准要求之前，系統管理員應該先仔細[評估所有同意要求](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent)，特別是當 Microsoft 偵測到風險時。

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>使用 PowerShell 來停用或重新啟用以風險為基礎的升級同意

您可以使用 Azure AD PowerShell Preview 模組 [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)，停用在 Microsoft 偵測到風險的情況下必要的升級至管理員同意，或是在先前停用時重新啟用。

1. 請確定您使用的是 [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) 模組。 如果您已安裝 [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) 模組和 [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) 模組兩者，則這個步驟很重要。

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. 連線至 Azure AD PowerShell。

   ```powershell
   Connect-AzureAD
   ```

1. 擷取您租用戶中 **同意原則設定** 目錄設定的目前值。 這需要檢查是否已建立這項功能的目錄設定，如果未建立，則使用對應目錄設定範本中的值。

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. 瞭解設定值：

    | 設定       | 類型         | 描述  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  指出偵測到有風險的要求時，是否將封鎖使用者同意的旗標。 |

1. 更新所需設定的設定值：

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
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

## <a name="next-steps"></a>後續步驟

若要深入了解：

* [設定使用者同意設定](configure-user-consent.md)
* [管理應用程式同意原則](manage-app-consent-policies.md)
* [設定管理員同意工作流程](configure-admin-consent-workflow.md)
* [了解如何管理對應用程式的同意及評估同意要求](manage-consent-requests.md)
* [對應用程式授與全租用戶的管理員同意](grant-admin-consent.md)
* [Microsoft 身分識別平台中的權限和同意](../develop/v2-permissions-and-consent.md)

若要取得協助或尋找您的問題解答：
* [StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)