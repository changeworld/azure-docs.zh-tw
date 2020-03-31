---
title: 與 B2B - Azure AD 的標識提供程式直接聯合
description: 與 SAML 或 WS-Fed 標識提供程式直接聯合，以便來賓可以登錄到 Azure AD 應用
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b99a80a90df8fcfc5efe6dfa0c2cd7e8e5e04e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050890"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>與 AD FS 和協力廠商供應商直接聯合，供來賓使用者使用（預覽版）
|     |
| --- |
| 直接聯合是 Azure 活動目錄的公共預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文介紹如何與其他組織建立 B2B 協作的直接聯合。 您可以與標識提供程式 （IdP） 支援 SAML 2.0 或 WS-Fed 協定的任何組織建立直接聯合。
與合作夥伴的 IdP 建立直接聯合時，來自該域的新來賓使用者可以使用自己的 IdP 託管組織帳戶登錄到 Azure AD 租戶並開始與您協作。 來賓使用者無需創建單獨的 Azure AD 帳戶。
> [!NOTE]
> 直接聯合來賓使用者必須使用包含租戶上下文的連結（例如，`https://myapps.microsoft.com/?tenantid=<tenant id>`或`https://portal.azure.com/<tenant id>`，或 ，或在已驗證域的情況下，）`https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`登錄。 應用程式和資源的直接連結只要包含租用戶內容，也可有同樣作用。 直接聯合使用者當前無法使用沒有租戶上下文的公用終結點登錄。 例如，使用`https://myapps.microsoft.com`或`https://portal.azure.com``https://teams.microsoft.com`將導致錯誤。
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>來賓使用者何時通過直接聯合進行身份驗證？
與組織建立直接聯合後，您邀請的任何新來賓使用者都將使用直接識別身分同盟。 請務必注意，設置直接聯合不會更改已兌換您邀請的來賓使用者的身份驗證方法。 以下是一些範例：
 - 如果來賓使用者已兌換您的邀請，並且您隨後與其組織建立了直接聯合，則這些來賓使用者將繼續使用他們在設置直接聯合之前使用的相同身份驗證方法。
 - 如果與合作夥伴組織建立直接聯合並邀請來賓使用者，然後合作夥伴組織稍後移動到 Azure AD，則已兌換邀請的來賓使用者將繼續使用直接聯合，只要直接租戶中存在聯合策略。
 - 如果刪除與合作夥伴組織的直接聯合，當前使用直接聯合的任何來賓使用者將無法登錄。

在上述任何方案中，您可以通過從目錄中刪除來賓使用者帳戶並重新邀請來賓使用者的身份驗證方法來更新它們。

直接聯合綁定到功能變數名稱空間，如contoso.com和fabrikam.com。 當使用 AD FS 或協力廠商 IdP 建立直接聯合配置時，組織會將一個或多個功能變數名稱空間與這些 IdP 相關聯。 

## <a name="end-user-experience"></a>使用者體驗 
使用直接聯合，來賓使用者使用自己的組織帳戶登錄到 Azure AD 租戶。 當他們訪問共用資源並提示他們登錄時，直接聯合使用者將重定向到其 IdP。 成功登錄後，它們將返回到 Azure AD 以訪問資源。 直接聯合使用者的刷新權杖的有效期為 12 小時，這是 Azure AD 中[傳遞刷新權杖的預設長度](../develop/active-directory-configurable-token-lifetimes.md#exceptions)。 如果聯合 IdP 啟用了 SSO，則使用者將遇到 SSO，並在初始身份驗證後看不到任何登錄提示。

## <a name="limitations"></a>限制

### <a name="dns-verified-domains-in-azure-ad"></a>Azure AD 中的 DNS 驗證域
要聯合的域***不得***在 Azure AD 中進行 DNS 驗證。 允許您設置與未託管（電子郵件驗證或"病毒"）Azure AD 租戶的直接聯合，因為它們未經過 DNS 驗證。

### <a name="authentication-url"></a>身份驗證 URL
僅當身份驗證 URL 的域與目標域匹配或身份驗證 URL 是這些允許的標識提供程式之一的策略時，才允許直接聯合（此清單可能會更改）：
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

例如，在為**fabrikam.com**設置直接聯合時，身份驗證 URL`https://fabrikam.com/adfs`將通過驗證。 同一域中的主機也將通過，例如`https://sts.fabrikam.com/adfs`。 但是，身份驗證 URL `https://fabrikamconglomerate.com/adfs` `https://fabrikam.com.uk/adfs`或同一域不會通過。

### <a name="signing-certificate-renewal"></a>簽章憑證續訂
如果在標識提供程式設置中指定中繼資料 URL，Azure AD 將在簽章憑證過期時自動續訂。 但是，如果證書在過期時間之前由於任何原因進行旋轉，或者未提供中繼資料 URL，則 Azure AD 將無法續訂它。 在這種情況下，您需要手動更新簽章憑證。

### <a name="limit-on-federation-relationships"></a>限制聯合關係
目前，最多支援 1，000 個聯合關係。 此限制包括[內部聯合和](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)直接聯合。

### <a name="limit-on-multiple-domains"></a>限制多個域
我們目前不支援與來自同一租戶的多個域的直接聯合。

## <a name="frequently-asked-questions"></a>常見問題集
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>我能否與存在非託管（電子郵件驗證）租戶的域建立直接聯合？ 
是。 如果域尚未驗證，並且租戶尚未進行[管理員接管](../users-groups-roles/domains-admin-takeover.md)，則可以與該域建立直接聯合。 當使用者兌換 B2B 邀請或使用當前不存在的域對 Azure AD 執行自助服務註冊時，將創建未託管或電子郵件驗證的租戶。 您可以設置與這些域的直接聯合。 如果嘗試在 Azure 門戶或通過 PowerShell 與 DNS 驗證的域設置直接聯合，您將看到錯誤。
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>如果同時啟用了直接聯合和電子郵件一次性密碼身份驗證，則哪種方法優先？
當與合作夥伴組織建立直接聯合時，它優先于來自該組織的新來賓使用者的電子郵件一次性密碼身份驗證。 如果在您設置直接聯合之前使用一次性密碼身份驗證兌換了邀請，他們將繼續使用一次性密碼身份驗證。 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>由於部分同步的租約，直接識別身分同盟位址登錄問題是否？
否，在這種情況下應使用[電子郵件一次性密碼](one-time-passcode.md)功能。 "部分同步租約"是指本地使用者標識未完全同步到雲的合作夥伴 Azure AD 租戶。 其身份尚不存在的訪客，但嘗試兌換 B2B 邀請的客人將無法登錄。 一次性密碼功能將允許此來賓登錄。 直接聯合功能解決了來賓擁有自己的 IdP 託管組織帳戶但組織根本沒有 Azure AD 狀態的情況。

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>第 1 步：配置合作夥伴組織的標識提供程式
首先，您的合作夥伴組織需要配置其標識提供程式與所需的聲明和依賴方信任。 

> [!NOTE]
> 為了說明如何為直接聯合配置標識提供程式，我們將使用 Active 目錄聯合服務 （AD FS） 作為示例。 請參閱文章["配置與 AD FS 的直接聯合](direct-federation-adfs.md)"，其中提供了如何將 AD FS 配置為 SAML 2.0 或 WS-Fed 標識提供程式以準備直接聯合的示例。

### <a name="saml-20-configuration"></a>SAML 2.0 設定

Azure AD B2B 可以配置為與使用 SAML 協定且具有下面列出的特定要求的標識提供程式聯合。 有關在 SAML 標識提供程式和 Azure AD 之間設置信任的詳細資訊，請參閱[使用 SAML 2.0 標識提供程式 （IdP） 進行單一登入](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)。  

> [!NOTE]
> 直接聯合的目標域不得在 Azure AD 上進行 DNS 驗證。 身份驗證 URL 域必須與目標域匹配，或者必須是允許的標識提供程式的域。 有關詳細資訊，請參閱[限制](#limitations)部分。 

#### <a name="required-saml-20-attributes-and-claims"></a>所需的 SAML 2.0 屬性和聲明
下表顯示了必須在協力廠商標識提供程式配置的特定屬性和聲明的要求。 要設置直接聯合，必須在標識提供程式的 SAML 2.0 回應中接收以下屬性。 可以通過連結到線上安全權杖服務 XML 檔或手動輸入它們來配置這些屬性。

來自 IdP 的 SAML 2.0 回應所需的屬性：

|屬性  |值  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|適用對象     |`urn:federation:MicrosoftOnline`         |
|簽發者     |合作夥伴 IdP 的頒發者 URI，例如`http://www.example.com/exk10l6w90DHM0yi...`         |


IdP 頒發的 SAML 2.0 權杖所需的聲明：

|屬性  |值  |
|---------|---------|
|名稱 ID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed 配置 
Azure AD B2B 可以配置為與使用 WS-Fed 協定且具有某些特定要求的標識提供程式聯合，如下所示。 目前，兩個 WS-Fed 提供程式已測試與 Azure AD 的相容性，包括 AD FS 和 Shibboleth。 有關使用 Azure AD 在符合 WS-Fed 的提供程式之間建立依賴方信任的詳細資訊，請參閱[Azure AD 標識提供程式相容性文檔](https://www.microsoft.com/download/details.aspx?id=56843)中提供的"使用 WS 協定的 STS 集成檔"。

> [!NOTE]
> 直接聯合的目標域不得在 Azure AD 上進行 DNS 驗證。 身份驗證 URL 域必須與目標域或允許的標識提供程式的域匹配。 有關詳細資訊，請參閱[限制](#limitations)部分。 

#### <a name="required-ws-fed-attributes-and-claims"></a>所需的 WS-Fed 屬性和聲明

下表顯示了必須在協力廠商 WS-Fed 標識提供程式配置的特定屬性和聲明的要求。 要設置直接聯合，必須在來自標識提供程式的 WS-Fed 消息中接收以下屬性。 可以通過連結到線上安全權杖服務 XML 檔或手動輸入它們來配置這些屬性。

來自 IdP 的 WS-Fed 消息中所需的屬性：
 
|屬性  |值  |
|---------|---------|
|被動要求者端點     |`https://login.microsoftonline.com/login.srf`         |
|適用對象     |`urn:federation:MicrosoftOnline`         |
|簽發者     |合作夥伴 IdP 的頒發者 URI，例如`http://www.example.com/exk10l6w90DHM0yi...`         |

IdP 頒發的 WS-Fed 權杖所需的聲明：

|屬性  |值  |
|---------|---------|
|不可改變ID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>第 2 步：在 Azure AD 中配置直接聯合 
接下來，您將配置與 Azure AD 中步驟 1 中配置的標識提供程式的聯合。 您可以使用 Azure AD 門戶或 PowerShell。 可能需要 5-10 分鐘才能使直接聯合策略生效。 在此期間，不要嘗試為直接聯合域兌換邀請。 以下為必要屬性：
- 合作夥伴 IdP 的頒發者 URI
- 合作夥伴 IdP 的被動身份驗證終結點（僅支援 HTTPs）
- 憑證

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>在 Azure AD 門戶中配置直接聯合

1. 轉到[Azure 門戶](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]****。 
2. 選取 [組織關係]****。
3. 選擇**標識提供程式**，然後選擇新的**SAML/WS-Fed IdP**。

    ![螢幕截圖顯示用於添加新 SAML 或 WS-Fed IdP 的按鈕](media/direct-federation/new-saml-wsfed-idp.png)

4. 在新**SAML/WS-Fed IdP**頁面上，在**標識提供程式協定**下，選擇**SAML**或**WS-FED**。

    ![在 SAML 或 WS-Fed IdP 頁面上顯示解析按鈕的螢幕截圖](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 輸入合作夥伴組織的功能變數名稱，該功能變數名稱將成為直接聯合的目標功能變數名稱
6. 您可以上載元資料檔案以填充中繼資料詳細資訊。 如果選擇手動輸入中繼資料，請輸入以下資訊：
   - 合作夥伴 IdP 的功能變數名稱
   - 合作夥伴 IdP 的實體 ID
   - 合作夥伴 IdP 的被動請求器終結點
   - 憑證
   > [!NOTE]
   > 中繼資料 URL 是可選的，但我們強烈建議它。 如果提供中繼資料 URL，Azure AD 可以在簽章憑證過期時自動續訂。 如果在過期時間之前出於任何原因旋轉證書，或者未提供中繼資料 URL，則 Azure AD 將無法續訂它。 在這種情況下，您需要手動更新簽章憑證。

7. 選取 [儲存]****。 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>使用 PowerShell 在 Azure AD 中配置直接聯合

1. 安裝最新版的 Azure AD PowerShell for Graph 模組 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。 （如果需要詳細步驟，添加來賓使用者的快速入門包括[安裝最新的 AzureAD 預覽模組](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)的部分。 
2. 執行以下命令： 
   ```powershell
   Connect-AzureAD
   ```
1. 在出現登入提示時，使用受控的全域系統管理員帳戶來登入。 
2. 運行以下命令，替換聯合元資料檔案中的值。 對於 AD FS 伺服器和 Okta，聯合檔為聯合中繼資料.xml，`https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`例如： 。 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>第 3 步：在 Azure AD 中測試直接聯合
現在，通過邀請新的 B2B 來賓使用者來測試直接聯合設置。 有關詳細資訊，請參閱[在 Azure 門戶中添加 Azure AD B2B 協作使用者](add-users-administrator.md)。
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>如何編輯直接聯合關係？

1. 轉到[Azure 門戶](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]****。 
2. 選取 [組織關係]****。
3. 選擇**標識提供程式**
4. 在**SAML/WS-Fed 標識提供程式**下，選擇提供程式。
5. 在標識提供程式詳細資訊窗格中，更新值。
6. 選取 [儲存]****。


## <a name="how-do-i-remove-direct-federation"></a>如何刪除直接聯合？
您可以刪除直接聯合設置。 如果兌換了，則已兌換其邀請的直接聯合來賓使用者將無法登錄。 但是，您可以通過從目錄中刪除資源並重新邀請資源來再次授予他們對資源的存取權限。 要刪除與 Azure AD 門戶中的標識提供程式的直接聯合：：

1. 轉到[Azure 門戶](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]****。 
2. 選取 [組織關係]****。
3. 選擇**標識提供程式**。
4. 選擇標識提供程式，然後選擇 **"刪除**"。 
5. 選取 [是]**** 以確認要刪除。 

要使用 PowerShell 刪除與標識提供程式的直接聯合：
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
