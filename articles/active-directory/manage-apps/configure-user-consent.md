---
title: 設定終端使用者使用 Azure AD 同意應用程式的方式
description: 瞭解如何管理使用者如何及何時同意可存取您組織資料的應用程式。
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
ms.openlocfilehash: 42337fe958a881ee263d16c866dda69f13fe09c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519615"
---
# <a name="configure-how-end-users-consent-to-applications"></a>設定終端使用者同意應用程式的方式

應用程式可以與 Microsoft 身分識別平臺整合，以允許使用者使用其在 Azure Active Directory （Azure AD）中的工作或學校帳戶登入，並存取您組織的資料，以提供豐富的資料驅動體驗。 不同的許可權可讓應用程式存取您的使用者和組織資料的不同層級。

根據預設，使用者可以同意存取貴組織資料的應用程式，但僅適用于某些許可權。 例如，根據預設，使用者可以同意允許應用程式存取自己的信箱或使用者擁有之小組的小組交談，但無法同意允許應用程式自動存取讀取和寫入您組織中的所有 SharePoint 網站。 雖然允許使用者自行同意，可以讓使用者輕鬆地取得與 Microsoft 365、Azure 及其他服務整合的實用應用程式，如果未小心使用和監視，則可能會代表風險。

Microsoft 建議停用未來的使用者同意作業，以協助降低您的介面區並降低此風險。 若已停用使用者同意，仍然會接受先前的同意授與，但所有未來的同意作業都必須由系統管理員執行。 使用者可以透過整合式系統管理員[同意要求工作流程](configure-admin-consent-workflow.md)，或透過您自己的支援流程，來要求租使用者的系統管理員同意。 如需詳細資訊，請參閱[五個步驟來保護您的身分識別基礎結構](../../security/fundamentals/steps-secure-identity.md)。

## <a name="configure-user-consent-to-applications"></a>設定應用程式的使用者同意
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>停用或啟用 Azure 入口網站的使用者同意

您可以使用 Azure 入口網站來停用或啟用使用者同意存取貴組織資料之應用程式的能力：

1. 以[全域管理員](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)身分登入[Azure 入口網站](https://portal.azure.com)。
2. 依序選取 [ **Azure Active Directory**]、[**企業應用程式**] 和 [**使用者設定**]。
3. 使用標示為 [**使用者可同意應用程式代表他們存取公司資料**] 的控制項，啟用或停用使用者同意。
4. 選擇性設定[管理員同意要求工作流程](configure-admin-consent-workflow.md)，以確保不允許同意應用程式的使用者可以要求核准。

> [!TIP]
> 若要讓使用者要求系統管理員審查不允許使用者同意的應用程式（例如，因為已停用使用者同意，或是應用程式要求使用者不允許授與許可權），請考慮設定[管理員同意工作流程](configure-admin-consent-workflow.md)。

### <a name="disable-or-enable-user-consent-using-powershell"></a>使用 PowerShell 停用或啟用使用者同意

您可以使用 Azure AD PowerShell v1 模組（[MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)），來啟用或停用使用者同意存取貴組織資料之應用程式的能力。

1. 執行下列 Cmdlet 來登入您的組織：

    ```powershell
    Connect-MsolService
    ```

2. 藉由執行下列 Cmdlet 來檢查是否已啟用使用者同意：

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. 啟用或停用使用者同意。 例如，若要停用使用者同意，請執行此 Cmdlet：

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>設定群組擁有者同意存取群組資料的應用程式

> [!IMPORTANT]
> 下列資訊適用于即將推出的功能，可讓群組擁有者授與應用程式對其群組資料的存取權。 釋放這項功能時，預設會啟用。 雖然這項功能尚未廣泛發行，但您可以使用這些指示，在其發行前停用此功能。

群組擁有者可以授權應用程式（例如，由協力廠商發行的應用程式）存取與群組相關聯的組織資料。 例如，小組擁有者（身為小組之 Office 365 群組的擁有者）可以允許應用程式讀取小組中的所有小組訊息，或列出群組成員的基本設定檔。

> [!NOTE]
> 與這項設定無關，群組擁有者一律可以直接將其他使用者或應用程式新增為群組擁有者。

### <a name="configure-group-owner-consent-using-powershell"></a>使用 PowerShell 設定群組擁有者同意

您可以使用 Azure AD PowerShell Preview 模組（[AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)），來啟用或停用群組擁有者同意應用程式存取其所擁有群組之資料的能力。

1. 請確定您使用的是[AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)模組（如果您已安裝[AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0)模組和[AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)模組，此步驟很重要）。

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. 連接到 Azure AD PowerShell。

   ```powershell
   Connect-AzureAD
   ```

3. 在您的租使用者中，取得*同意原則設定*目錄設定的目前值。 這需要檢查是否已建立這項功能的目錄設定，如果不是，則使用對應的目錄設定範本中的值。

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

4. 瞭解設定值。 有兩個設定值可定義哪些使用者可以允許應用程式存取其群組的資料：

    | 設定       | 類型         | 描述  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean |  表示是否允許群組擁有者授與群組特定許可權的旗標。 |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | 如果_EnableGroupSpecificConsent_設定為 "True"，且此值設定為群組的物件識別碼，則識別之群組的成員會獲得授權，將群組特有的許可權授與他們所擁有的群組。 |

5. 更新所需設定的設定值：

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

6. 儲存設定。

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>設定以風險為基礎的步驟同意

以風險為基礎的逐步執行同意可協助減少使用者暴露于惡意應用程式，而造成[違法的同意要求](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)。 如果 Microsoft 偵測到有風險的使用者同意要求，則要求必須改為「逐步執行」以進行系統管理員同意。 這項功能預設為啟用，但只有在啟用使用者同意時，才會產生行為變更。

當偵測到有風險的同意要求時，同意提示會顯示一則訊息，指出需要管理員核准。 如果已啟用系統[管理員同意要求工作流程](configure-admin-consent-workflow.md)，則使用者可以將要求傳送給系統管理員，以便直接從同意提示進行進一步的審查。 如果未啟用，則會顯示下列訊息：

* **AADSTS90094：** &lt;clientAppDisplayName&gt;需要許可權才能存取您組織中只有系統管理員可以授與的資源。 請先要求管理員授與此應用程式的權限，您才能使用它。

在此情況下，也會記錄「ApplicationManagement」類別、「同意應用程式的活動」和「偵測到具風險的應用程式」的狀態原因的「audit 事件」。

> [!IMPORTANT]
> 在核准之前，系統管理員應該仔細[評估所有同意要求](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent)，特別是當 Microsoft 偵測到風險時。

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>使用 PowerShell 來停用或重新啟用以風險為基礎的步驟同意

您可以使用 Azure AD PowerShell Preview 模組（[AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)），停用在 Microsoft 偵測到風險或重新啟用（如果先前已停用）時，所需的系統管理員同意步驟。

這可以使用上述的相同步驟來完成，以[使用 PowerShell 來設定群組擁有者同意](#configure-group-owner-consent-using-powershell)，但替代不同的設定值。 步驟有三個差異： 

1. 瞭解以風險為基礎之步驟同意的設定值：

    | 設定       | 類型         | 描述  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  指出偵測到有風險的要求時，是否將封鎖使用者同意的旗標。 |

2. 取代步驟3中的下列值：

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
3. 在步驟5中，以下列其中一項取代：

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>後續步驟

[設定管理員同意工作流程](configure-admin-consent-workflow.md)

[瞭解如何管理對應用程式的同意及評估同意要求](manage-consent-requests.md)

[對應用程式授與全租使用者系統管理員同意](grant-admin-consent.md)

[Microsoft 身分識別平臺中的許可權和同意](../develop/active-directory-v2-scopes.md)

[StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
