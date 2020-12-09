---
title: 直接與 B2B 的識別提供者同盟 - Azure AD
description: 直接與 SAML 或 WS-Fed 識別提供者同盟，讓來賓可以登入您的 Azure AD 應用程式
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/24/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5beae56a5d38c4620481c27c3f42c52602984e6b
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860621"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>直接與來賓使用者的 AD FS 和第三方提供者同盟 (預覽)

> [!NOTE]
>  直接同盟是 Azure Active Directory 的一項公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文說明如何設定與其他組織的直接同盟，以進行 B2B 共同作業。 您可以針對其識別提供者 (IdP) 可支援 SAML 2.0 或 WS-Fed 通訊協定的任何組織來設定直接同盟。
在設定與合作夥伴 IdP 的直接同盟時，來自該網域的新來賓使用者可以使用其由 IdP 管理的自有組織帳戶登入您的 Azure AD 租用戶，並開始與您共同作業。 來賓使用者不需要另外建立一個 Azure AD 帳戶。
> [!NOTE]
> 直接同盟的來賓使用者必須使用包含租用戶內容的連結 (例如 `https://myapps.microsoft.com/?tenantid=<tenant id>` 或 `https://portal.azure.com/<tenant id>`，如果是已驗證的網域，則為 `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`) 來登入。 應用程式和資源的直接連結只要包含租用戶內容，也可有同樣作用。 直接同盟使用者目前無法使用沒有租用戶內容的通用端點來登入。 例如，使用 `https://myapps.microsoft.com`、`https://portal.azure.com` 或 `https://teams.microsoft.com` 將會導致錯誤。
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>來賓使用者何時會使用直接同盟來進行驗證？
在設定與組織直接同盟之後，您所邀請的任何新來賓使用者便會使用直接同盟進行驗證。 請務必注意，若來賓使用者早已兌換您所提出的邀請，則設定直接同盟並不會變更其驗證方法。 以下是一些範例：
 - 如果來賓使用者早已兌換您所提出的邀請，而您隨後設定與其所屬組織直接同盟，則這些來賓使用者將會繼續使用您設定直接同盟之前，其原本使用的驗證方法。
 - 如果您設定與夥伴組織直接同盟並對來賓使用者提出邀請，然後夥伴組織之後移至 Azure AD，則只要您租用戶中的直接同盟原則仍存在，早已兌換邀請的來賓使用者便會繼續使用直接同盟。
 - 如果您刪除與夥伴組織的直接同盟，則任何目前使用直接同盟的來賓使用者便無法登入。

在上述任一案例中，您可以藉由從目錄中刪除來賓使用者帳戶再重新對來賓使用者提出邀請，以更新來賓使用者的驗證方法。

直接同盟會繫結至網域命名空間，例如 contoso.com 和 fabrikam.com。 組織在向 AD FS 或第三方 IdP 建立直接同盟設定時，會將一或多個網域命名空間關聯至這些 Idp。 

## <a name="end-user-experience"></a>使用者體驗 
透過直接同盟，來賓使用者會使用自己的組織帳戶登入您的 Azure AD 租用戶。 直接同盟使用者在存取共用資源並收到登入提示時，會由系統重新導向至其 IdP。 成功登入之後，則會返回 Azure AD 來存取資源。 直接同盟使用者的重新整理權杖有效期限為 12 小時 (這是 Azure AD 中[傳遞重新整理權杖的預設長度](../develop/active-directory-configurable-token-lifetimes.md#exceptions))。 如果同盟 IdP 已啟用 SSO，則使用者會遇到 SSO，而且不會在初始驗證後看到任何登入提示。

## <a name="limitations"></a>限制

### <a name="dns-verified-domains-in-azure-ad"></a>Azure AD 中的 DNS 驗證網域
您要與同盟的網域，在 Azure AD **not** 中必須以 DNS 驗證。 您可以設定與非受控 (透過電子郵件驗證或「病毒式」) Azure AD 租用戶直接同盟，因為這些租用戶未進行過 DNS 驗證。

### <a name="authentication-url"></a>驗證 URL
驗證 URL 的網域符合目標網域的原則，或驗證 URL 是其中一個所允許識別提供者 (此清單可能會變更) 的原則時，才允許直接同盟：

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

例如，設定 _ * fabrikam * * 的直接同盟時，驗證 URL `https://fabrikam.com/adfs` 會通過驗證。 相同網域中的主機也會通過，例如 `https://sts.fabrikam.com/adfs`。 不過，相同網域的驗證 URL `https://fabrikamconglomerate.com/adfs` 或 `https://fabrikam.com.uk/adfs` 則不會通過。

### <a name="signing-certificate-renewal"></a>簽署憑證更新
如果您在識別提供者設定中指定中繼資料 URL，Azure AD 便會在簽署憑證到期時自動予以更新。 不過，如果憑證因任何原因而在到期之前輪替，或如果您未提供中繼資料 URL，Azure AD 便無法加以更新。 在此情況下，您必須手動更新簽署憑證。

### <a name="limit-on-federation-relationships"></a>同盟關聯性的限制
目前支援的同盟關聯性上限為 1,000 個。 這項限制包括[內部同盟](/powershell/module/msonline/set-msoldomainfederationsettings)和直接同盟。

### <a name="limit-on-multiple-domains"></a>多個網域的限制
我們目前不支援與來自相同租用戶的多個網域直接同盟。

## <a name="frequently-asked-questions"></a>常見問題集
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>是否可以設定與存在非受控 (透過電子郵件驗證) 租用戶的網域直接同盟？ 
是。 如果網域尚未經過驗證，而且租用戶尚未經歷[系統管理員接管](../enterprise-users/domains-admin-takeover.md)，便可以設定與該網域直接同盟。 當使用者兌換 B2B 邀請或使用目前不存在的網域執行自助式 Azure AD 註冊時，系統就會建立非受控 (或透過電子郵件驗證) 的租用戶。 您可以設定與這些網域直接同盟。 如果您嘗試在 Azure 入口網站或透過 PowerShell 設定與 DNS 驗證網域直接同盟，則會看到錯誤。
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>如果同時啟用直接同盟和電子郵件單次密碼驗證，則會優先使用哪種方法？
如果已與夥伴組織建立直接同盟，則來自該組織的新來賓使用者會優先採用直接同盟，而非電子郵件單次密碼驗證。 如果在您設定直接同盟之前，來賓使用者早已使用單次密碼驗證兌換了邀請，則會繼續使用單次密碼驗證。 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>直接同盟能解決登入問題，是不是因為其採用部分同步租用？
不是，在此案例中應該使用[電子郵件單次密碼](one-time-passcode.md)功能。 「部分同步租用」是指內部部署使用者身分識別不會完全同步到雲端的合作夥伴 Azure AD 租用戶。 來賓的身分識別若未存在於雲端中，卻嘗試兌換 B2B 邀請，則無法登入。 單次密碼功能會讓此來賓能夠登入。 直接同盟功能則解決了來賓有 IdP 所管理的自有組織帳戶，但組織內卻完全沒有任何 Azure AD 的情況。

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>步驟 1:設定夥伴組織的識別提供者
首先，夥伴組織需要使用所需的宣告和信賴憑證者信任來設定其識別提供者。 

> [!NOTE]
> 為了說明如何設定要直接同盟的識別提供者，我們會使用 Active Directory 同盟服務 (AD FS) 作為範例。 請參閱[設定與 AD FS 直接同盟](direct-federation-adfs.md)一文，以取得如何將 AD FS 設定為 SAML 2.0 或 WS-Fed 識別提供者的範例，來為直接同盟預做準備。

### <a name="saml-20-configuration"></a>SAML 2.0 設定

Azure AD B2B 可設定為與使用 SAML 通訊協定的識別提供者同盟，但有下列特定需求。 如需如何在 SAML 識別提供者與 Azure AD 之間設定信任的詳細資訊，請參閱[使用 SAML 2.0 識別提供者 (IdP) 進行單一登入](../hybrid/how-to-connect-fed-saml-idp.md)。  

> [!NOTE]
> 直接同盟的目標網域不得是 Azure AD 上的 DNS 驗證網域。 驗證 URL 網域必須符合目標網域，或者必須是所允許識別提供者的網域。 如需詳細資訊，請參閱[限制](#limitations)一節。 

#### <a name="required-saml-20-attributes-and-claims"></a>所需的 SAML 2.0 屬性和宣告
下表顯示必須在第三方識別提供者上設定的特定屬性和宣告需求。 若要設定直接同盟，則必須在來自識別提供者的 SAML 2.0 回應中收到下列屬性。 若要設定這些屬性，請連結至線上 Security Token Service XML 檔案，或者您也可以手動輸入這些屬性。

來自 IdP 的 SAML 2.0 回應所需的屬性：

|屬性  |值  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|適用對象     |`urn:federation:MicrosoftOnline`         |
|簽發者     |合作夥伴 IdP 的簽發者 URI，例如 `http://www.example.com/exk10l6w90DHM0yi...`         |


IdP 所簽發 SAML 2.0 權杖所需的宣告：

|屬性  |值  |
|---------|---------|
|NameID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed 設定 
Azure AD B2B 可設定為與使用 WS-Fed 通訊協定的識別提供者同盟，但有下列特定需求。 目前已通過 Azure AD 相容性測試的兩個 WS-Fed 提供者包括 AD FS 和 Shibboleth。 如需如何在 WS-Fed 相容提供者與 Azure AD 之間建立信賴憑證者信任的詳細資訊，請參閱 [Azure AD 識別提供者相容性文件](https://www.microsoft.com/download/details.aspx?id=56843)中的＜使用 WS 通訊協定的 STS 整合論文＞。

> [!NOTE]
> 直接同盟的目標網域不得是 Azure AD 上的 DNS 驗證網域。 驗證 URL 網域必須符合目標網域，或者是所允許識別提供者的網域。 如需詳細資訊，請參閱[限制](#limitations)一節。 

#### <a name="required-ws-fed-attributes-and-claims"></a>所需的 WS-Fed 屬性和宣告

下表顯示必須在第三方 WS-Fed 識別提供者上設定的特定屬性和宣告需求。 若要設定直接同盟，則必須在來自識別提供者的 WS-Fed 訊息中收到下列屬性。 若要設定這些屬性，請連結至線上 Security Token Service XML 檔案，或者您也可以手動輸入這些屬性。

來自 IdP 的 WS-Fed 訊息中所需的屬性：
 
|屬性  |值  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|適用對象     |`urn:federation:MicrosoftOnline`         |
|簽發者     |合作夥伴 IdP 的簽發者 URI，例如 `http://www.example.com/exk10l6w90DHM0yi...`         |

IdP 所簽發 WS-Fed 權杖所需的宣告：

|屬性  |值  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>步驟 2:在 Azure AD 中設定直接同盟 
接下來，您將在 Azure AD 中設定與步驟 1 所設定的識別提供者同盟。 您可以使用 Azure AD 入口網站或 PowerShell。 可能需要5 到 10 分鐘的時間，直接同盟原則才會生效。 在這段時間內，請勿嘗試兌換直接同盟網域的邀請。 以下為必要屬性：
- 合作夥伴 IdP 的簽發者 URI
- 合作夥伴 IdP 的被動驗證端點 (僅支援 https)
- 憑證

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>在 Azure AD 入口網站中設定直接同盟

1. 移至 [Azure 入口網站](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]。 
2. 選取 [外部身分識別] > [所有識別提供者]。
3. 選取，然後選取 [ **新增 SAML/WS-饋送 IdP**]。

    ![螢幕擷取畫面：顯示用來新增 SAML 或 WS-Fed IdP 的按鈕](media/direct-federation/new-saml-wsfed-idp.png)

4. 在 [新增 SAML/WS-Fed IdP] 頁面上的 [識別提供者通訊協定] 底下，選取 [SAML] 或 [WS-Fed]。

    ![螢幕擷取畫面：顯示 SAML 或 WS-Fed IdP 頁面上的剖析按鈕](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 輸入您夥伴組織的網域名稱，這將是要直接同盟的目標網域名稱
6. 您可以上傳中繼資料檔案來填入中繼資料的詳細資料。 如果您選擇手動輸入中繼資料，請輸入下列資訊：
   - 合作夥伴 IdP 的網域名稱
   - 合作夥伴 IdP 的實體識別碼
   - 合作夥伴 IdP 的被動要求者端點
   - 憑證
   > [!NOTE]
   > 中繼資料 URL 是選擇性的，但強烈建議您輸入此資訊。 如果您提供中繼資料 URL，Azure AD 就可以在簽署憑證到期時自動予以更新。 如果憑證因任何原因而在到期之前輪替，或如果您未提供中繼資料 URL，Azure AD 便無法加以更新。 在此情況下，您必須手動更新簽署憑證。

7. 選取 [儲存]。 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>使用 PowerShell 在 Azure AD 中設定直接同盟

1. 安裝最新版的 Azure AD PowerShell for Graph 模組 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。 (如需詳細步驟，用來新增來賓使用者的快速入門包含[安裝最新的 AzureADPreview 模組](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)一節)。 
2. 執行以下命令： 
   ```powershell
   Connect-AzureAD
   ```
1. 在出現登入提示時，使用受控的全域系統管理員帳戶來登入。 
2. 執行下列命令，並取代同盟中繼資料檔案中的值。 針對 AD FS Server 和 Okta，同盟檔案是 federationmetadata.xml，例如：`https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`。 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>步驟 3：在 Azure AD 中測試直接同盟
現在，請邀請新的 B2B 來賓使用者來測試您的直接同盟設定。 如需詳細資訊，請參閱[在 Azure 入口網站中新增 Azure AD B2B 共同作業使用者](add-users-administrator.md)。
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>要如何編輯直接同盟關聯性？

1. 移至 [Azure 入口網站](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]。 
2. 選取 [外部身分識別]。
3. 選取 [所有識別提供者]
4. 在 [SAML/WS-Fed 識別提供者] 底下選取提供者。
5. 在 [識別提供者詳細資料] 窗格中更新值。
6. 選取 [儲存]。


## <a name="how-do-i-remove-direct-federation"></a>要如何移除直接同盟？
您可以移除直接同盟設定。 如果您這麼做，已兌換其邀請的直接同盟來賓使用者就無法登入。 但是，您可以藉由先將來賓使用者從目錄中刪除再重新邀請的方式，向來賓使用者再次提供您資源的存取權。 若要在 Azure AD 入口網站中移除與識別提供者的直接同盟：

1. 移至 [Azure 入口網站](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]。 
2. 選取 [外部身分識別]。
3. 選取 [所有識別提供者]。
4. 選取識別提供者，然後選取 [刪除]。 
5. 選取 [是] 以確認要刪除。 

若要使用 PowerShell 來移除與識別提供者的直接同盟：
1. 安裝最新版的 Azure AD PowerShell for Graph 模組 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 執行以下命令： 
   ```powershell
   Connect-AzureAD
   ```
3. 在出現登入提示時，使用受控的全域系統管理員帳戶來登入。 
4. 輸入下列命令：
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>後續步驟

深入瞭解當外部使用者以各種身分識別提供者登入時的 [邀請兌換體驗](redemption-experience.md) 。
