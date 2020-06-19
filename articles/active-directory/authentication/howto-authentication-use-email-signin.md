---
title: 使用 Azure Active Directory 的替代登入識別碼電子郵件登入
description: 了解如何設定及讓使用者以替代登入識別碼的電子郵件地址登入 Azure Active Directory (預覽)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/22/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: scottsta
ms.openlocfilehash: ed317039e683ef36054d5ace612e09ca75dfa11e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837307"
---
# <a name="sign-in-to-azure-using-email-as-an-alternate-login-id-preview"></a>使用替代登入識別碼電子郵件登入 Azure (預覽)

許多組織都希望使用者能使用和內部部署目錄環境相同的認證登入 Azure。 使用這種稱為混合式驗證的方法，使用者只需要記住一組認證。

有些組織尚未移至混合式驗證，其原因如下：

* 依據預設，Azure Active Directory (Azure AD) 使用者主體名稱 (UPN) 會設定成與內部部署目錄相同的 UPN。
* 變更 Azure AD UPN 會在內部部署與 Azure 環境之間建立不相符的項目，可能會造成某些應用程式與服務的問題。
* 由於商務或合規性的原因，組織不希望使用內部部署 UPN 登入 Azure。

為了協助您移至混合式驗證，您現在可以設定 Azure AD，讓使用者使用已驗證網域中的替代登入識別碼電子郵件登入 Azure。 例如，若將 *Contoso* 改為 *Fabrikam*，現在可以使用替代登入識別碼的電子郵件登入，不用繼續使用舊的 `balas@contoso.com` UPN 登入。 為存取應用程式或服務，使用者會使用其獲指派的電子郵件登入 Azure，例如 `balas@fabrikam.com`。

|     |
| --- |
| 使用替代登入識別碼的電子郵件登入 Azure AD，是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Azure AD 登入方法概觀

在內部部署目錄與 Azure AD 中，使用者主體名稱 (UPN) 是使用者帳戶的唯一識別碼。 目錄中的每個使用者帳戶都是以 UPN 表示，例如 `balas@contoso.com`。 依據預設，當您同步內部部署 Active Directory Domain Services (AD DS) 環境與 Azure AD 時，Azure AD UPN 和內部部署 UPN 會設成相同的。

在許多組織中，內部部署 UPN 與 Azure AD UPN 可以設成相同的。 當使用者登入 Azure 應用程式與服務時，使用的是 Azure AD UPN。 然而，由於商務原則或使用者體驗問題，某些組織無法使用相符的 UPN 登入應用程式與服務。

無法在 Azure AD 中使用相符 UPN 的組織有幾個選項：

* 其中一種方法是依據商務需求，將 Azure AD UPN 設為不同的內容，例如 `balas@fabrikam.com`。
    * 但並非所有應用程式與服務都符合規範，內部部署 UPN 與 Azure AD UPN 可以使用不同的值。
* 比較好的方法是確保將 Azure AD UPN 與內部部署 UPN 設為相同的值，並將 Azure AD 設定為讓使用者使用其替代登入識別碼的電子郵件登入 Azure。

使用替代登入識別碼的電子郵件，使用者仍然可以透過輸入 UPN 登入 Azure，但也可以使用其電子郵件登入。 若要支援此功能，您可以在內部部署目錄之使用者的 *ProxyAddresses* 屬性中，定義電子郵件地址。 此 *ProxyAddress* 屬性支援一或多個電子郵件地址。

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>將登入電子郵件地址同步至 Azure AD

傳統的 Active Directory Domain Services (AD DS) 或 Active Directory 同盟服務 (AD FS) 可以直接在網路上驗證，並由 AD DS 基礎結構處理。 使用混合式驗證時，使用者可以改為直接登入 Azure AD。

為了支援這種混合式驗證方法，您可以使用 [Azure AD Connect][azure-ad-connect] 將內部部署 AD DS 環境同步至 Azure AD，並將其設定為使用密碼雜湊同步 (PHS) 或傳遞驗證 (PTA)。

在這兩個設定選項中，使用者會將其使用者名稱與密碼提交至 Azure AD，以驗證認證並發出票證。 當使用者登入 Azure AD 時，您的組織就不需要裝載及管理 AD FS 基礎結構。

![Azure AD 混合式身分識別與密碼雜湊同步圖](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Azure AD 混合式身分識別與傳遞驗證圖](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Azure AD Connect 自動同步的其中一個使用者屬性是 *ProxyAddresses*。 若使用者在內部部署 AD DS 環境中，將電子郵件地址定義為 *ProxyAddresses* 屬性的一部分，其就會自動同步至 Azure AD。 此電子郵件地址就可直接作為 Azure AD 登入過程中的替代登入識別碼使用。

> [!IMPORTANT]
> 只有租用戶已驗證網域中的電子郵件會同步至 Azure AD。 每個 Azure AD 租用戶都有一或多個您已證明具有擁有權的已驗證網域，而且會唯一繫結到您的租用戶。
>
> 如需詳細資訊，請參閱[在 Azure AD 中新增及驗證自訂網域名稱][verify-domain]。

如需詳細資訊，請參閱[針對 Azure AD 混合式身分識別解決方案選擇正確的驗證方法][hybrid-auth-methods]。

## <a name="enable-user-sign-in-with-an-email-address"></a>允許使用者使用電子郵件地址登入

一旦套用 *ProxyAddresses* 屬性的使用者，使用 Azure AD Connect 同步至 Azure AD，您就必須啟用讓使用者使用您租用戶替代登入識別碼電子郵件登入的功能。 此功能會通知 Azure AD 登入伺服器，不只要檢查 UPN 值的登入名稱，還要檢查電子郵件地址的 *ProxyAddresses* 值。

在預覽期間，您目前只能使用 PowerShell 啟用以替代登入識別碼電子郵件登入的功能。 您需要有「租用戶系統管理員」權限，才能完成下列步驟：

1. 以系統管理員身分開啟 PowerShell 工作階段，然後使用 [Install-Module][Install-Module] Cmdlet 安裝 *AzureADPreview* 模組：

    ```powershell
    Install-Module AzureADPreview
    ```

    若出現提示，請選取 [Y] 來安裝 NuGet，或從未受信任的存放庫安裝。

1. 以「租用戶系統管理員」身分使用 [Connect-AzureAD][Connect-AzureAD] Cmdlet 登入您的 Azure AD 租用戶：

    ```powershell
    Connect-AzureAD
    ```

    命令會傳回您帳戶、環境與租用戶識別碼的相關資訊。

1. 使用 [Get-AzureADPolicy][Get-AzureADPolicy] Cmdlet 檢查 *HomeRealmDiscoveryPolicy* 原則是否已存在於您的租用戶中，如下所示：

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. 若目前未設定任何原則，此命令就不會傳回任何內容。 若傳回原則，請略過此步驟，並繼續下一個步驟以更新現有的原則。

    若要將 *HomeRealmDiscoveryPolicy* 原則新增至租用戶，請使用 [New-AzureADPolicy][New-AzureADPolicy] Cmdlet，並將 *AlternateIdLogin* 屬性設為 *"Enabled": true*，如下列範例所示：

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    當成功建立原則之後，此命令會傳回原則識別碼，如下列範例輸出所示：

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. 若已經有設定的原則，請檢查是否已啟用  *AlternateIdLogin*   屬性，如下列範例原則輸出所示：

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    若原則存在，但 *AlternateIdLogin* 屬性不存在或未啟用，或者您想要保留的原則上有其他屬性，請使用 [Set-AzureADPolicy][Set-AzureADPolicy] Cmdlet 來更新現有的原則。

    > [!IMPORTANT]
    > 當您更新原則時，請確定您包含任何舊的設定與新的  *AlternateIdLogin* 屬性。

    下列範例會新增  *AlternateIdLogin* 屬性，並保留可能已經設定的  *AllowCloudPasswordValidation* 屬性：

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    確認更新的原則顯示您的變更，而且已啟用 *AlternateIdLogin* 屬性：

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="test-user-sign-in-with-email"></a>使用電子郵件測試使用者登入

若要測試使用者可否使用電子郵件登入，請瀏覽至 [https://myprofile.microsoft.com][my-profile]，並依據其電子郵件地址 (例如 `balas@fabrikam.com` ) 而不是其 UPN (例如 `balas@contoso.com` )，以使用者帳戶登入。 登入體驗和 UPN 式登入事件應該完全相同。

## <a name="troubleshoot"></a>疑難排解

如果使用者的使用電子郵件地址登入事件發生問題，請檢閱下列疑難排解步驟：

1. 確定使用者帳戶已在內部部署 AD DS 環境的 *ProxyAddresses* 屬性中設定其電子郵件地址。
1. 驗證已設定 Azure AD Connect，並成功將使用者帳戶從內部部署 AD DS 環境同步至 Azure AD。
1. 確認 Azure AD *HomeRealmDiscoveryPolicy* 原則已將 *AlternateIdLogin* 屬性設為 *"Enabled": true*：

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>後續步驟

若要深入了解混合式身分識別，例如 Azure AD App Proxy 或 Azure AD Domain Services，請參閱[用於存取及管理內部部署工作負載的 Azure AD 混合式身分識別][hybrid-overview]。

如需混合式身分識別作業的詳細資訊，請參閱[如何執行密碼雜湊同步][phs-overview]或[傳遞驗證][pta-overview]同步工作。

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
