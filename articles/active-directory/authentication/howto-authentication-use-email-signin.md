---
title: 使用 Azure Active Directory 的替代登入識別碼電子郵件登入
description: 了解如何設定及讓使用者以替代登入識別碼的電子郵件地址登入 Azure Active Directory (預覽)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/01/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: ff6ae6ea6812397e737deb4b97bf1cd15e022c03
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743168"
---
# <a name="sign-in-to-azure-active-directory-using-email-as-an-alternate-login-id-preview"></a>使用電子郵件作為替代登入識別碼 (預覽版來登入 Azure Active Directory) 

> [!NOTE]
> 使用替代登入識別碼的電子郵件登入 Azure AD，是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

許多組織想要讓使用者使用與其內部部署目錄環境相同的認證，來登入 Azure Active Directory (Azure AD) 。 使用這種稱為混合式驗證的方法，使用者只需要記住一組認證。

有些組織尚未移至混合式驗證，其原因如下：

* 根據預設，Azure AD 的使用者主體名稱 (UPN) 會設定為與內部部署目錄相同的 UPN。
* 變更 Azure AD UPN 會在內部部署和 Azure AD 環境之間建立不相符的情況，這可能會導致某些應用程式和服務發生問題。
* 由於商務或合規性的理由，組織不會想要使用內部部署 UPN 來登入 Azure AD。

若要協助移至混合式驗證，您現在可以設定 Azure AD，讓使用者以您的已驗證網域中的電子郵件登入，作為替代登入識別碼。 例如，若將 *Contoso* 改為 *Fabrikam*，現在可以使用替代登入識別碼的電子郵件登入，不用繼續使用舊的 `balas@contoso.com` UPN 登入。 若要存取應用程式或服務，使用者會使用其指派的電子郵件（例如）登入 Azure AD `balas@fabrikam.com` 。

本文說明如何啟用和使用電子郵件作為替代登入識別碼。 這項功能可在 Azure AD Free 版和更新版本中使用。

> [!NOTE]
> 這項功能僅適用于雲端驗證的 Azure AD 使用者。

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Azure AD 登入方法概觀

若要登入 Azure AD，使用者可以輸入可唯一識別其帳戶的名稱。 在過去，您只能使用 Azure AD UPN 做為登入名稱。

對於內部部署 UPN 是使用者慣用登入電子郵件的組織來說，這種方法很好用。 這些組織會將 Azure AD UPN 設定為與內部部署 UPN 完全相同的值，而且使用者會有一致的登入體驗。

不過，在某些組織中，不會使用內部部署 UPN 做為登入名稱。 在內部部署環境中，您可以將本機 AD DS 設定為允許使用替代登入識別碼進行登入。 將 Azure AD UPN 設定為與內部部署 UPN 相同的值並無法選擇，因為 Azure AD 接著會要求使用者以該值登入。

此問題的常見因應措施是將 Azure AD UPN 設定為使用者預期用來登入的電子郵件地址。 這種方法的運作方式，雖然會導致內部部署 AD 與 Azure AD 之間的不同 Upn，但此設定與所有 Microsoft 365 工作負載不相容。

另一種方法是將 Azure AD 和內部部署 Upn 同步處理為相同的值，然後將 Azure AD 設定為允許使用者使用已驗證的電子郵件登入 Azure AD。 若要提供這項功能，請在內部部署目錄中的使用者 *ProxyAddresses* 屬性定義一或多個電子郵件地址。 然後， *ProxyAddresses* 會使用 Azure AD Connect 自動同步處理 Azure AD。

## <a name="preview-limitations"></a>預覽限制

使用電子郵件登入 Azure AD，因為 Azure AD Free edition 和更新版本提供替代登入識別碼。

在目前的預覽狀態中，當使用者以非 UPN 電子郵件登入作為替代登入識別碼時，適用下列限制：

* 即使使用非 UPN 電子郵件登入，使用者仍可看到其 UPN。 可能會看到下列範例行為：
    * 當系統將使用者導向 Azure AD 登入時，系統會提示使用者使用 UPN 進行登入 `login_hint=<non-UPN email>` 。
    * 當使用者以非 UPN 電子郵件登入，並輸入不正確的密碼時，[ *輸入您的密碼]* 頁面會變更以顯示 UPN。
    * 在某些 Microsoft 網站和應用程式（例如 [https://portal.azure.com](https://portal.azure.com) 和 Microsoft Office）上，[ **帳戶管理員** ] 控制項通常會顯示在右上角，而不是用來登入的非 UPN 電子郵件。

* 某些流程目前與非 UPN 電子郵件不相容，如下所示：
    * Identity protection 目前不符合電子郵件替代登入識別碼與 *洩漏的認證* 風險偵測。 此風險偵測會使用 UPN 來比對已洩漏的認證。 如需詳細資訊，請參閱 [Azure AD Identity Protection 風險偵測和補救][identity-protection]。
    * 未完全支援傳送給替代登入識別碼電子郵件的 B2B 邀請。 接受以替代登入識別碼形式傳送至電子郵件的邀請之後，請使用替代電子郵件登入，但租使用者端點上的使用者可能無法使用。

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

套用原則之後，最多可能需要一小時的時間來傳播，讓使用者能夠使用他們的替代登入識別碼進行登入。

## <a name="test-user-sign-in-with-email"></a>使用電子郵件測試使用者登入

若要測試使用者可否使用電子郵件登入，請瀏覽至 [https://myprofile.microsoft.com][my-profile]，並依據其電子郵件地址 (例如 `balas@fabrikam.com` ) 而不是其 UPN (例如 `balas@contoso.com` )，以使用者帳戶登入。 登入體驗和 UPN 式登入事件應該完全相同。

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>啟用分段推出以測試使用電子郵件地址的使用者登入  

[分段推出][staged-rollout] 可讓租使用者系統管理員啟用特定群組的功能。 建議租使用者系統管理員使用分段推出，測試使用電子郵件地址的使用者登入。 當系統管理員準備好將此功能部署至整個租使用者時，他們應該使用主領域探索原則。  


您需要有「租用戶系統管理員」權限，才能完成下列步驟：

1. 以系統管理員身分開啟 PowerShell 會話，然後使用 [安裝模組][Install-Module]Cmdlet 來安裝 *AzureADPreview* 模組：

    ```powershell
    Install-Module AzureADPreview
    ```

    若出現提示，請選取 [Y] 來安裝 NuGet，或從未受信任的存放庫安裝。

2. 以「租用戶系統管理員」身分使用 [Connect-AzureAD][Connect-AzureAD] Cmdlet 登入您的 Azure AD 租用戶：

    ```powershell
    Connect-AzureAD
    ```

    命令會傳回您帳戶、環境與租用戶識別碼的相關資訊。

3. 使用下列 Cmdlet 列出所有現有的分段推出原則：
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

4. 如果沒有此功能的現有分段推出原則，請建立新的分段推出原則，並記下原則識別碼：

   ```powershell
   New-AzureADMSFeatureRolloutPolicy -Feature EmailAsAlternateId -DisplayName "EmailAsAlternateId Rollout Policy" -IsEnabled $true
   ```

5. 尋找要新增至分段推出原則的群組 directoryObject 識別碼。 請注意針對 *Id* 參數傳回的值，因為它將在下一個步驟中使用。
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

6. 將群組新增至分段推出原則，如下列範例所示。 將 *-Id* 參數中的值取代為步驟4中原則識別碼所傳回的值，並將 *-RefObjectId* 參數中的值取代為步驟5中所述的 *識別碼* 。 最多可能需要1小時的時間，群組中的使用者才可以使用其 proxy 位址登入。

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -RefObjectId "GROUP_OBJECT_ID"
   ```
   
針對新增至群組的新成員，最多可能需要24小時的時間，才能使用其 proxy 位址登入。

### <a name="removing-groups"></a>移除群組

若要從分段推出原則移除群組，請執行下列命令：

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -ObjectId "GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>移除原則

若要移除分段推出原則，請先停用該原則，然後將它從系統中移除：

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

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
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[staged-rollout]: /powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged-rollout
[my-profile]: https://myprofile.microsoft.com
