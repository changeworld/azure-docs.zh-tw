---
title: 透過 B2B-Azure AD 的身分識別提供者進行直接同盟
description: 直接與 SAML 或 WS-送出的身分識別提供者同盟，讓來賓可以登入您的 Azure AD 應用程式
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12d87c12b84130d404eaf203fd6013f6924020f5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199444"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>來賓使用者的直接同盟與 AD FS 和協力廠商提供者（預覽）
|     |
| --- |
| 直接同盟是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文說明如何設定與其他組織的直接同盟，以進行 B2B 共同作業。 您可以使用身分識別提供者（IdP）支援 SAML 2.0 或 WS-送出通訊協定的任何組織來設定直接同盟。
當您設定與合作夥伴 IdP 的直接同盟時，來自該網域的新來賓使用者可以使用他們自己的 IdP 管理組織帳戶登入您的 Azure AD 租使用者，並開始與您共同作業。 來賓使用者不需要建立個別的 Azure AD 帳戶。
> [!NOTE]
> 直接同盟來賓使用者必須使用包含租使用者內容的連結（例如， `https://myapps.microsoft.com/?tenantid=<tenant id>` 或 `https://portal.azure.com/<tenant id>` 已驗證網域的案例）來登入 `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com` 。 應用程式和資源的直接連結只要包含租用戶內容，也可有同樣作用。 直接同盟使用者目前無法使用沒有租使用者內容的通用端點進行登入。 例如，使用 `https://myapps.microsoft.com` 、 `https://portal.azure.com` 或 `https://teams.microsoft.com` 將會導致錯誤。
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>來賓使用者何時使用直接同盟進行驗證？
在您設定與組織的直接同盟之後，您邀請的任何新來賓使用者都會使用直接同盟進行驗證。 請務必注意，設定直接同盟並不會變更已經兌換邀請之來賓使用者的驗證方法。 以下是一些範例：
 - 如果來賓使用者已經兌換您的邀請，而您接著設定與組織的直接同盟，則這些來賓使用者在設定直接同盟之前，會繼續使用他們所使用的相同驗證方法。
 - 如果您設定與夥伴組織的直接同盟，並邀請來賓使用者，然後合作夥伴組織之後移至 Azure AD，則已兌換邀請的來賓使用者將繼續使用直接同盟，只要您租使用者中的直接同盟原則存在即可。
 - 如果您刪除合作夥伴組織的直接同盟，任何目前使用直接同盟的來賓使用者將無法登入。

在上述任一案例中，您可以從目錄中刪除來賓使用者帳戶並加以 reinviting，以更新來賓使用者的驗證方法。

直接同盟會系結至網域命名空間，例如 contoso.com 和 fabrikam.com。 當您使用 AD FS 或協力廠商 IdP 建立直接同盟設定時，組織會將一或多個網域命名空間關聯至這些 Idp。 

## <a name="end-user-experience"></a>使用者體驗 
透過直接同盟，來賓使用者會使用自己的組織帳戶登入您的 Azure AD 租使用者。 當他們存取共用資源並出現登入提示時，會將直接同盟使用者重新導向至其 IdP。 成功登入之後，系統會將其傳回 Azure AD 以存取資源。 直接同盟使用者的重新整理權杖有效期限為12小時，Azure AD 中傳遞重新整理[權杖的預設長度](../develop/active-directory-configurable-token-lifetimes.md#exceptions)。 如果同盟 IdP 已啟用 SSO，則使用者會遇到 SSO，而且在初始驗證之後將不會看到任何登入提示。

## <a name="limitations"></a>限制

### <a name="dns-verified-domains-in-azure-ad"></a>Azure AD 中 DNS 驗證的網域
您要與其建立同盟的網域，在 Azure AD***中不得以 DNS 方式進行驗證***。 您可以使用非受控（電子郵件驗證或「病毒」） Azure AD 的租使用者來設定直接同盟，因為它們不是經過 DNS 驗證。

### <a name="authentication-url"></a>驗證 URL
只有驗證 URL 的網域符合目標網域的原則，或驗證 URL 是其中一個允許的識別提供者（此清單可能會變更）時，才允許直接同盟：

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

例如，設定**fabrikam.com**的直接同盟時，驗證 URL `https://fabrikam.com/adfs` 會通過驗證。 例如，相同網域中的主機也會通過 `https://sts.fabrikam.com/adfs` 。 不過，驗證 URL `https://fabrikamconglomerate.com/adfs` 或 `https://fabrikam.com.uk/adfs` 適用于相同的網域將不會通過。

### <a name="signing-certificate-renewal"></a>簽署憑證更新
如果您在身分識別提供者設定中指定中繼資料 URL，Azure AD 會在簽署憑證到期時自動更新。 不過，如果憑證因任何原因而在到期時間之前旋轉，或如果您未提供中繼資料 URL，Azure AD 將無法更新它。 在此情況下，您必須手動更新簽署憑證。

### <a name="limit-on-federation-relationships"></a>同盟關聯性的限制
目前支援最多1000個同盟關聯性。 這項限制包括[內部](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)同盟和直接同盟。

### <a name="limit-on-multiple-domains"></a>多個網域的限制
我們目前不支援來自相同租使用者的直接同盟與多個網域。

## <a name="frequently-asked-questions"></a>常見問題集
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>我是否可以使用未受管理（電子郵件驗證）租使用者的網域來設定直接同盟？ 
是。 如果網域尚未經過驗證，而且租使用者尚未進行系統[管理員接管](../users-groups-roles/domains-admin-takeover.md)，您可以設定與該網域的直接同盟。 當使用者贖回 B2B 邀請或使用目前不存在的網域執行自助式註冊 Azure AD 時，會建立非受控或電子郵件驗證的租使用者。 您可以設定這些網域的直接同盟。 如果您嘗試在 Azure 入口網站或透過 PowerShell 設定與 DNS 驗證網域的直接同盟，您會看到錯誤。
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>如果同時啟用「直接同盟」和「電子郵件單次密碼驗證」，則會優先使用哪種方法？
與夥伴組織建立直接同盟時，其優先順序高於該組織的新來賓使用者的電子郵件單次密碼驗證。 如果來賓使用者在設定直接同盟之前，使用單次密碼驗證兌換邀請，它們會繼續使用單次密碼驗證。 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>是否因為部分同步的租用而導致直接同盟位址登入問題？
否，此案例中應該使用[電子郵件單次密碼](one-time-passcode.md)功能。 「部分同步處理的租用」是指合作夥伴 Azure AD 的租使用者，其中內部部署使用者身分識別不會完全同步到雲端。 其身分識別尚未存在於雲端中，但嘗試兌換您的 B2B 邀請的來賓將無法登入。 一次性密碼功能會允許此來賓登入。 「直接同盟」功能解決了來賓有自己的 IdP 管理組織帳戶的情況，但是組織完全沒有任何 Azure AD 存在。

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>步驟1：設定夥伴組織的識別提供者
首先，您的合作夥伴組織需要使用必要的宣告和信賴憑證者信任來設定其身分識別提供者。 

> [!NOTE]
> 為了說明如何設定直接同盟的識別提供者，我們將使用 Active Directory 同盟服務（AD FS）做為範例。 請參閱[使用 AD FS 設定直接同盟](direct-federation-adfs.md)一文，其中提供如何將 AD FS 設定為 SAML 2.0 或 WS-送出識別提供者的範例，以準備直接同盟。

### <a name="saml-20-configuration"></a>SAML 2.0 設定

Azure AD B2B 可以設定為與使用 SAML 通訊協定與下列特定需求的身分識別提供者同盟。 如需有關在您的 SAML 識別提供者與 Azure AD 之間設定信任的詳細資訊，請參閱[使用 saml 2.0 身分識別提供者（IdP）進行單一登入](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)。  

> [!NOTE]
> 直接同盟的目標網域不得在 Azure AD 上進行 DNS 驗證。 驗證 URL 網域必須符合目標網域，或必須是允許的識別提供者的網域。 如需詳細資訊，請參閱[限制](#limitations)一節。 

#### <a name="required-saml-20-attributes-and-claims"></a>必要的 SAML 2.0 屬性和宣告
下表顯示必須在協力廠商識別提供者上設定之特定屬性和宣告的需求。 若要設定直接同盟，必須在識別提供者的 SAML 2.0 回應中收到下列屬性。 這些屬性可以藉由連結至線上 Security Token Service XML 檔案或手動輸入來設定。

來自 IdP 之 SAML 2.0 回應的必要屬性：

|屬性  |值  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|適用對象     |`urn:federation:MicrosoftOnline`         |
|Issuer     |合作夥伴 IdP 的簽發者 URI，例如`http://www.example.com/exk10l6w90DHM0yi...`         |


IdP 所簽發的 SAML 2.0 權杖所需的宣告：

|屬性  |值  |
|---------|---------|
|NameID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-送出設定 
Azure AD B2B 可以設定為與使用 WS-ADDRESSING 通訊協定的身分識別提供者建立同盟，如下所列的特定需求。 目前，兩個 WS-ADDRESSING 提供者已通過 Azure AD 的相容性測試，包括 AD FS 和 Shibboleth。 如需在與 Azure AD 的 WS-ADDRESSING 相容提供者之間建立信賴憑證者信任的詳細資訊，請參閱[Azure AD 身分識別提供者相容性](https://www.microsoft.com/download/details.aspx?id=56843)檔中的「使用 WS 通訊協定的 STS 整合紙張」。

> [!NOTE]
> 直接同盟的目標網域不得在 Azure AD 上進行 DNS 驗證。 驗證 URL 網域必須符合允許的識別提供者的目標網域或網域。 如需詳細資訊，請參閱[限制](#limitations)一節。 

#### <a name="required-ws-fed-attributes-and-claims"></a>必要的 WS-送出屬性和宣告

下表顯示必須在協力廠商的 WS-ADDRESSING 識別提供者上設定之特定屬性和宣告的需求。 若要設定直接同盟，必須在來自識別提供者的 WS-送訊息中收到下列屬性。 這些屬性可以藉由連結至線上 Security Token Service XML 檔案或手動輸入來設定。

來自 IdP 的 WS-送出訊息中的必要屬性：
 
|屬性  |值  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|適用對象     |`urn:federation:MicrosoftOnline`         |
|Issuer     |合作夥伴 IdP 的簽發者 URI，例如`http://www.example.com/exk10l6w90DHM0yi...`         |

IdP 所簽發的 WS-送出權杖所需的宣告：

|屬性  |值  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>步驟2：在 Azure AD 中設定直接同盟 
接下來，您將使用 Azure AD 中步驟1所設定的身分識別提供者來設定同盟。 您可以使用 Azure AD 入口網站或 PowerShell。 這可能需要5-10 分鐘的時間，直接同盟原則才會生效。 在這段期間，請勿嘗試兌換直接同盟網域的邀請。 以下為必要屬性：
- 合作夥伴 IdP 的簽發者 URI
- 合作夥伴 IdP 的被動驗證端點（僅支援 HTTPs）
- 憑證

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>在 Azure AD 入口網站中設定直接同盟

1. 移至 [Azure 入口網站](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]****。 
2. 選取 [**組織關聯**性]  >  [所有**External Identities**身分**識別提供者**]  >  **All identity providers**
3. 選取 []，然後選取 [**新增 SAML/WS-送出 IdP**]。

    ![顯示新增新的 SAML 或 WS-送出 IdP 按鈕的螢幕擷取畫面](media/direct-federation/new-saml-wsfed-idp.png)

4. 在 [**新增 SAML/WS-送 IdP** ] 頁面的 [**識別提供者通訊協定**] 底下，選取 [ **SAML** ] 或 [ **WS-送**出]。

    ![顯示 [SAML 或 WS-送出 IdP] 頁面上 [剖析] 按鈕的螢幕擷取畫面](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 輸入您的夥伴組織的功能變數名稱，這將是直接同盟的目標功能變數名稱
6. 您可以上傳中繼資料檔案來填入中繼資料的詳細資訊。 如果您選擇手動輸入中繼資料，請輸入下列資訊：
   - 合作夥伴 IdP 的功能變數名稱
   - 合作夥伴 IdP 的實體識別碼
   - 合作夥伴 IdP 的被動要求者端點
   - 憑證
   > [!NOTE]
   > 中繼資料 URL 是選擇性的，但我們強烈建議您這麼做。 如果您提供中繼資料 URL，Azure AD 可以在簽署憑證到期時自動更新。 如果憑證因任何原因而在到期時間之前旋轉，或您未提供中繼資料 URL，Azure AD 將無法加以更新。 在此情況下，您必須手動更新簽署憑證。

7. 選取 [儲存]  。 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>使用 PowerShell 在 Azure AD 中設定直接同盟

1. 安裝最新版的 Azure AD PowerShell for Graph 模組 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。 （如需詳細步驟，新增來賓使用者的快速入門包含[安裝最新的 AzureADPreview 模組](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)一節）。 
2. 執行以下命令： 
   ```powershell
   Connect-AzureAD
   ```
1. 在出現登入提示時，使用受控的全域系統管理員帳戶來登入。 
2. 執行下列命令，並取代同盟中繼資料檔案中的值。 針對 AD FS Server 和 Okta，同盟檔案是 federationmetadata，例如： `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml` 。 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>步驟3：在 Azure AD 中測試直接同盟
現在，邀請新的 B2B 來賓使用者來測試您的直接同盟設定。 如需詳細資訊，請參閱[在 Azure 入口網站中新增 AZURE AD B2B](add-users-administrator.md)共同作業使用者。
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>如何? 編輯直接同盟關聯性嗎？

1. 移至 [Azure 入口網站](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]****。 
2. 選取 [**組織關聯**性] （或 [**外部**身分識別]）。
3. 選取**所有身分識別提供者**
4. 在 [ **SAML/WS-送出識別提供者**] 底下，選取提供者。
5. 在 [識別提供者詳細資料] 窗格中，更新值。
6. 選取 [儲存]  。


## <a name="how-do-i-remove-direct-federation"></a>如何? 移除直接同盟？
您可以移除直接同盟設定。 如果您這樣做，已兌換其邀請的直接同盟來賓使用者將無法登入。 但是，您可以將資源從目錄中刪除並加以 reinviting，讓他們再次存取您的資源。 若要在 Azure AD 入口網站中移除與身分識別提供者的直接同盟：

1. 移至 [Azure 入口網站](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]****。 
2. 選取 [**組織關聯**性] （或 [**外部**身分識別]）。
3. 選取 [**所有識別提供者**]。
4. 選取 [識別提供者]，然後選取 [**刪除**]。 
5. 選取 [是]**** 以確認要刪除。 

若要使用 PowerShell 移除與身分識別提供者的直接同盟：
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
