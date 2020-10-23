---
title: 在 Azure AD 中管理應用程式同意原則
description: 瞭解如何管理內建和自訂的應用程式同意原則，以控制何時可以授與同意。
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
ms.openlocfilehash: 59e00d2bb47826bb4bfa381c42db551f44d84b71
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427605"
---
# <a name="manage-app-consent-policies"></a>管理應用程式同意原則

您可以使用 Azure AD PowerShell 來查看和管理應用程式同意原則。

應用程式同意原則是由一或多個「包含」條件集和零或多個「排除」條件集所組成。 針對應用程式同意原則中所考慮的事件，它必須 *至少* 符合一個「包含」條件集，且不能 *有許多「* 排除」條件集。

每個條件集都包含數個條件。 針對符合條件集的事件，必須符合條件集中的 *所有* 條件。

識別碼開頭為 "microsoft-" 的應用程式同意原則是內建原則。 其中一些內建原則會用於現有的內建目錄角色。 例如，應用程式 `microsoft-application-admin` 同意原則會描述允許應用程式系統管理員和雲端應用程式系統管理員角色授與租使用者系統管理員同意的條件。 內建原則可用於自訂目錄角色，以及設定使用者同意設定，但無法編輯或刪除。

## <a name="pre-requisites"></a>必要條件

1. 請確定您使用的是 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 模組。 如果您已安裝 [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true) 模組和 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 模組兩者，則這個步驟很重要。

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. 連線至 Azure AD PowerShell。

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>列出現有的應用程式同意原則

最好先熟悉您組織中現有的應用程式同意原則：

1. 列出所有應用程式同意原則：

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. 查看原則的「包含」條件集：

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -Id "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. 請參閱「排除」條件集：

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -Id "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>建立自訂應用程式同意原則

遵循下列步驟來建立自訂應用程式同意原則：

1. 建立新的空白應用程式同意原則。

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. 新增「包含」條件集。

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   重複此步驟，以新增其他「包含」條件集。

1. （選擇性）新增「排除」條件集。

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   重複此步驟，以新增額外的「排除」條件集。

一旦建立應用程式同意原則之後，您就可以 [允許使用者同意](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) 遵守此原則。

## <a name="delete-a-custom-app-consent-policy"></a>刪除自訂應用程式同意原則

1. 以下顯示如何刪除自訂應用程式同意原則。 **此動作無法復原。**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> 無法還原已刪除的應用程式同意原則。 如果您不小心刪除了自訂應用程式同意原則，則必須重新建立原則。

---

### <a name="supported-conditions"></a>支援的條件

下表提供應用程式同意原則的支援條件清單。

| 條件 | 描述|
|:---------------|:----------|
| PermissionClassification | 所授與之許可權的 [許可權分類](configure-permission-classifications.md) ，或「全部」符合任何許可權分類 (包括未分類) 的許可權。 預設值為 "all"。 |
| PermissionType | 所授與之許可權的許可權類型。 使用「應用程式」以取得應用程式許可權 (例如) 的應用程式角色或委派許可權的「委派」。 <br><br>**注意**： "delegatedUserConsentable" 值指出 API 發行者尚未設定為需要系統管理員同意的委派許可權，此值可用於內建許可權授與原則，但不能用於自訂許可權授與原則。 必要。 |
| ResourceApplication | 資源應用程式的 **AppId** (例如，將授與許可權的 API) ，或符合任何資源應用程式或 API 的「任何」。 預設值為「任何」。 |
| 權限 | 要比對之特定許可權的許可權識別碼清單，或具有單一值「全部」以符合任何許可權的清單。 預設值為單一值 "all"。 <ul><li>委派的許可權識別碼可以在 API 的 ServicePrincipal 物件的 **OAuth2Permissions** 屬性中找到。</li><li>應用程式許可權識別碼可以在 API 的 ServicePrincipal 物件的 **AppRoles** 屬性中找到。</li></ol> |
| ClientApplicationIds | 要比對之用戶端應用程式的 **AppId** 值清單，或具有單一值「全部」以符合任何用戶端應用程式的清單。 預設值為單一值 "all"。 |
| ClientApplicationTenantIds | 註冊用戶端應用程式的 Azure Active Directory 租使用者識別碼清單，或單一值為「全部」的清單，以符合在任何租使用者中註冊的用戶端應用程式。 預設值為單一值 "all"。 |
| ClientApplicationPublisherIds | Microsoft 合作夥伴網路的清單 (MPN 用戶端應用程式之 [已驗證發行者](../develop/publisher-verification-overview.md) 的) 識別碼，或單一值為「全部」的清單，以符合任何發行者的用戶端應用程式。 預設值為單一值 "all"。 |
| ClientApplicationsFromVerifiedPublisherOnly | 設定為 `$true` 只在用戶端應用程式與 [已驗證的發行者](../develop/publisher-verification-overview.md)相符。 將設定為 `$false` ，以在任何用戶端應用程式上相符，即使它沒有已驗證的發行者。 預設值為 `$false`。 |

## <a name="next-steps"></a>後續步驟

若要深入了解：

* [設定使用者同意設定](configure-user-consent.md)
* [設定管理員同意工作流程](configure-admin-consent-workflow.md)
* [了解如何管理對應用程式的同意及評估同意要求](manage-consent-requests.md)
* [對應用程式授與全租用戶的管理員同意](grant-admin-consent.md)
* [Microsoft 身分識別平台中的權限和同意](../develop/active-directory-v2-scopes.md)

若要取得協助或尋找您的問題解答：
* [StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
