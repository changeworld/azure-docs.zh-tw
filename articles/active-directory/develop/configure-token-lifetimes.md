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
ms.date: 12/14/2020
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e663cdd3846e804d1dcf96076c07b9a3db84272c
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507739"
---
# <a name="configure-token-lifetime-policies-preview"></a> (預覽版設定權杖存留期原則) 
當您為應用程式、服務主體及您整體組織建立及管理權杖存留期時，在 Azure AD 中許多案例都是可能的。  

> [!IMPORTANT]
> 5月2020之後，租使用者將無法再設定重新整理和會話權杖存留期。  Azure Active Directory 將會在2021年1月30日之後停止接受現有的重新整理和會話權杖設定。 您仍然可以在淘汰之後設定存取權杖存留期。  若要深入瞭解，請閱讀 [Microsoft 身分識別平臺中可設定的權杖存留期](active-directory-configurable-token-lifetimes.md)。
> 我們已在 Azure AD 條件式存取中實行 [驗證會話管理功能](../conditional-access/howto-conditional-access-session-lifetime.md)   。 您可以使用這項新功能，藉由設定 [登入頻率] 來設定重新整理權杖存留期。


在本節中，我們將逐步解說一些常見的原則案例，這些案例可以協助您強制實行下列各項的新規則：

* 權杖存留期
* 權杖最大閒置時間
* 權杖最大壽命

在範例中，您可以了解如何︰

* 管理組織的預設原則
* 為 Web 登入建立原則
* 針對呼叫 Web API 的原生應用程式建立原則
* 管理進階原則

## <a name="prerequisites"></a>Prerequisites
在下列範例中，您建立、更新連結，並刪除應用程式、服務主體和您整體組織的原則。 如果您不熟悉 Azure AD，建議您先瞭解 [如何取得 Azure AD 租](quickstart-create-new-tenant.md) 使用者，然後再繼續進行這些範例。  

若要開始使用，請執行下列步驟：

1. 下載最新的 [Azure AD PowerShell 模組公開預覽版本](https://www.powershellgallery.com/packages/AzureADPreview)。
2. 執行 `Connect` 命令以登入您的 Azure AD 管理帳戶。 您每次啟動新的工作階段時執行此命令。

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. 若要查看在組織中建立的所有原則，請執行下列命令。 在下列案例中的大多數操作之後，執行此命令。 執行命令也會協助您取得原則的 ** **。

    ```powershell
    Get-AzureADPolicy
    ```

## <a name="manage-an-organizations-default-policy"></a>管理組織的預設原則
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

## <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>針對呼叫 Web API 的原生應用程式建立原則
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

## <a name="manage-an-advanced-policy"></a>管理進階原則
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