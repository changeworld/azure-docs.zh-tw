---
title: 設定權杖的存留期
titleSuffix: Microsoft identity platform
description: 瞭解如何設定 Microsoft 身分識別平臺所發出的權杖存留期。 瞭解如何管理組織的預設原則、建立 web 登入原則、為呼叫 web API 的原生應用程式建立原則，以及管理 advanced 原則。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/04/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 2529c6c3b0f9d188e1ce8062c05f62f3e980ef50
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805227"
---
# <a name="configure-token-lifetime-policies-preview"></a> (預覽版設定權杖存留期原則) 
您可以指定 Microsoft 身分識別平臺所發出的存取、SAML 或識別碼權杖的存留期。 不論是針對組織中所有的應用程式、針對多租用戶 (多組織) 應用程式，還是針對組織中特定的服務主體，都可以設定權杖存留期。 如需詳細資訊，請參閱可設定的 [權杖存留期](active-directory-configurable-token-lifetimes.md)。

在本節中，我們會逐步解說可協助您對權杖存留期強加新規則的常見原則案例。 在此範例中，您會瞭解如何建立原則，要求使用者在您的 web 應用程式中更頻繁地進行驗證。

## <a name="get-started"></a>開始使用
若要開始使用，請執行下列步驟：

1. 下載最新的 [Azure AD PowerShell 模組公開預覽版本](https://www.powershellgallery.com/packages/AzureADPreview)。
1. 執行 `Connect` 命令以登入您的 Azure AD 管理帳戶。 您每次啟動新的工作階段時執行此命令。

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. 若要查看在您的組織中建立的所有原則，請執行 [new-azureadpolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) Cmdlet。  具有已定義屬性值（不同于上述預設值）的任何結果都在淘汰範圍內。

    ```powershell
    Get-AzureADPolicy -All
    ```

1. 若要查看哪些應用程式和服務主體連結到您所識別的特定原則，請將 **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** 取代為任何原則識別碼來執行下列 [>get-azureadpolicyappliedobject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) Cmdlet。 然後您可以決定是否要設定條件式存取登入頻率，或保留 Azure AD 預設值。

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

如果您的租使用者具有可定義重新整理和會話權杖設定屬性之自訂值的原則，Microsoft 建議您將這些原則更新為反映上述預設值的值。 如果未進行任何變更，Azure AD 將會自動接受預設值。

## <a name="create-a-policy-for-web-sign-in"></a>為 Web 登入建立原則

在此範例中，您建立會要求使用者提高驗證頻率來登入 Web 應用程式的原則。 此原則會為 Web 應用程式的服務主體設定存取權杖/識別碼權杖的存留期及多重要素工作階段權杖的最大壽命。

1. 建立權杖存留期原則。

    這個 Web 登入原則會將存取權杖/識別碼權杖的存留期及單一要素工作階段權杖最大壽命設定為 2 小時。

    1. 若要建立原則，請執行 [new-azureadpolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) Cmdlet：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 若要查看您的新原則，並取得原則 **ObjectId**，請執行 [new-azureadpolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) Cmdlet：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 將原則指派給服務主體。 您也需要取得服務主體的 **ObjectId** 。

    1. 使用 [get-azureadserviceprincipal 指令程式](/powershell/module/azuread/get-azureadserviceprincipal) 可查看您組織的所有服務主體或單一服務主體。
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. 當您有服務主體時，請執行 [AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) Cmdlet：
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-token-lifetime-policies-for-refresh-and-session-tokens"></a>建立重新整理和會話權杖的權杖存留期原則
> [!IMPORTANT]
> 從2020到5月，新租使用者無法設定重新整理和會話權杖存留期。  具有現有設定的租使用者可以修改重新整理和會話權杖原則，直到2021年1月30日為止。  Azure Active Directory 將會在2021年1月30日之後停止接受現有的重新整理和會話權杖設定。 您仍然可以在淘汰之後設定存取、SAML 和識別碼權杖存留期。
>
> 如果您需要在要求使用者再次登入之前，繼續定義時間週期，請在條件式存取中設定登入頻率。 若要深入瞭解條件式存取，請參閱 [使用條件式存取設定驗證會話管理](../conditional-access/howto-conditional-access-session-lifetime.md)。
>
> 如果您不想在停用日期之後使用條件式存取，您的重新整理和會話權杖將會在該日期設定為 [預設](active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties-after-the-retirement) 設定，而且您將無法再變更其存留期。

### <a name="manage-an-organizations-default-policy"></a>管理組織的預設原則
在此範例中，您會建立一個原則，讓您的使用者在整個組織中的登入頻率較低。 若要這樣做，請建立單一要素重新整理權杖的權杖存留期原則，此原則會套用至整個組織。 此原則套用至您組織中的每個應用程式，以及每個尚未設定原則的服務主體。

1. 建立權杖存留期原則。

    1. 將單一要素重新整理權杖設定為「直到撤銷為止」。 權杖不會過期直到存取權被撤銷。 建立下列原則定義︰

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. 若要建立原則，請執行 [new-azureadpolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) Cmdlet：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 若要移除任何空白，請執行 [new-azureadpolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) Cmdlet：

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. 若要查看您的新原則並取得原則的 **ObjectId**，請執行下列命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 更新原則。

    您可能會決定您在此範例中設定的第一個原則不若您的服務所需的那樣嚴格。 若要將您的單一要素重新整理權杖設為在兩天內到期，請執行下列命令：

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>針對呼叫 Web API 的原生應用程式建立原則
在此範例中，您建立會要求使用者減少驗證頻率的原則。 原則也會延長使用者必須重新驗證之前，可以是非使用中的時間量。 原則會套用到 Web API。 當原生應用程式要求 Web API 做為資源時，會套用此原則。

1. 建立權杖存留期原則。

    1. 若要為 web API 建立嚴格的原則，請執行 [new-azureadpolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) Cmdlet：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 若要查看您的新原則，請執行下列命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 將原則指派給 Web API。 您也需要取得應用程式的 **ObjectId**。 使用 [get-azureadapplication](/powershell/module/azuread/get-azureadapplication) 指令程式來尋找您的應用程式 **ObjectId**，或使用 [Azure 入口網站](https://portal.azure.com/)。

    取得應用程式的 **ObjectId** 並指派原則：

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="manage-an-advanced-policy"></a>管理進階原則
在此範例中，您會建立幾個原則，以瞭解優先順序系統的運作方式。 您也會瞭解如何管理套用至數個物件的多個原則。

1. 建立權杖存留期原則。

    1. 若要建立將單一要素重新整理權杖存留期設定為30天的組織預設原則，請執行 [new-azureadpolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) Cmdlet：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 若要查看您的新原則，請執行 [new-azureadpolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) Cmdlet：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 將原則指派給服務主體。

    現在，您具有原則，該原則套用到整個組織。 您可能想要針對特定的服務主體保留這個 30 天原則，但是將組織預設原則變更為上限「直到撤銷為止」。

    1. 若要查看您組織的所有服務主體，請使用 [get-azureadserviceprincipal](/powershell/module/azuread/get-azureadserviceprincipal) Cmdlet。

    1. 當您有服務主體時，請執行 [AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) Cmdlet：

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. 將 `IsOrganizationDefault` 旗標設為 false：

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. 建立新的組織預設原則：

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    現在，原始原則已連結至您的服務主體，且新原則已設定為您的組織預設原則。 請務必記住，套用至服務主體的原則優先順序會高於組織預設原則。

## <a name="next-steps"></a>後續步驟
瞭解 Azure AD 條件式存取中的 [驗證會話管理功能](../conditional-access/howto-conditional-access-session-lifetime.md) 。
