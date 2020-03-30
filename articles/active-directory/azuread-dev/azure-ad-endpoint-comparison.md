---
title: 為什麼更新到微軟身份平臺 （v2.0） |蔚藍
description: 瞭解 Microsoft 標識平臺 （v2.0） 終結點和 Azure 活動目錄 （Azure AD） v1.0 終結點之間的差異，並瞭解更新到 v2.0 的好處。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 67a54a2cd4fa071fd47bcebb9aa53fd11fefd61e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154911"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>為何要更新至 Microsoft 身分識別平台 (v2.0)？

開發新應用程式時，瞭解 Microsoft 標識平臺 （v2.0） 和 Azure 活動目錄 （v1.0） 終結點之間的差異非常重要。 本文介紹了端點與 Microsoft 標識平臺的一些現有限制之間的主要區別。

> [!NOTE]
> Microsoft 標識平臺終結點不支援所有 Azure AD 方案和功能。 要確定是否應使用 Microsoft 標識平臺終結點，請閱讀有關[Microsoft 標識平臺限制](#limitations)。

## <a name="who-can-sign-in"></a>誰可以登入

![誰可以登入 v1.0 與 v2.0 端點](media/azure-ad-endpoint-comparison/who-can-signin.svg)

* v1.0 端點只允許使用公司和學校帳戶登入您的應用程式 (Azure AD)
* Microsoft 標識平臺終結點允許 Azure AD 和個人 Microsoft 帳戶 （MSA）的工作帳戶和學校帳戶（如hotmail.com、outlook.com和msn.com）登錄。
* 兩個終結點還接受 Azure AD 目錄的*[來賓使用者的](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* 登錄，用於配置為*[單租戶](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)* 的應用程式或配置為指向租戶特定終結點 （）`https://login.microsoftonline.com/{TenantId_or_Name}`*的多租戶*應用程式。

Microsoft 標識平臺終結點允許您編寫接受來自個人 Microsoft 帳戶的登錄以及工作和學校帳戶的應用。 這可讓您撰寫完全無從驗證帳戶的應用程式。 例如，如果您的應用程式呼叫 [Microsoft Graph](https://graph.microsoft.io) \(英文\)，則公司帳戶可取得一些額外的功能和資料，例如他們的 SharePoint 網站或目錄資料。 但在許多動作 (例如[讀取使用者的郵件](https://docs.microsoft.com/graph/api/user-list-messages?view=graph-rest-1.0)) 中，相同的程式碼可以存取個人及公司和學校帳戶的電子郵件。

對於 Microsoft 標識平臺終結點，您可以使用 Microsoft 身份驗證庫 （MSAL） 訪問消費者、教育和企業世界。 Azure AD v1.0 端點只接受來自公司和學校帳戶的登入。

## <a name="incremental-and-dynamic-consent"></a>增量和動態同意

使用 Azure AD v1.0 端點的應用程式必須事先指定其所需的 OAuth 2.0 權限，例如：

![顯示許可權註冊 UI 的示例](./media/azure-ad-endpoint-comparison/app-reg-permissions.png)

直接在應用程式註冊上設定的權限是**靜態的**。 雖然在 Azure 入口網站中定義應用程式的靜態使用權限，可讓程式碼保持簡潔，但它可能為開發人員帶來一些問題：

* 應用程式必須在使用者第一次登入時要求可能會需要的所有使用權限。 這可能會是一長串的使用權限，因此讓使用者不願意在初始登入時核准應用程式的存取。

* 應用程式必須事先知道可能會存取的所有資源。 很難建立能夠存取任意數目的資源的應用程式。

使用 Microsoft 標識平臺終結點，可以忽略 Azure 門戶中應用註冊資訊中定義的靜態許可權，而是逐步請求許可權，這意味著預先請求一組最低限度的許可權隨著客戶使用其他應用功能，並隨著時間的推移而增長。 若要這樣做，您隨時可以在要求存取權杖時於 `scope` 參數中納入新的範圍，以指定您應用程式所需的範圍，而不需要在應用程式註冊資訊中預先定義它們。 如果使用者尚未同意新增至要求的新範圍，則系統只會提示他們同意新的使用權限。 若要深入了解，請參閱[使用權限、同意和範圍](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

允許應用程式透過 `scope` 參數動態要求權限，讓開發人員可以完全掌控使用者的體驗。 您也可以選擇將您的同意體驗提前，並在一個初始授權要求中要求所有使用權限。 如果您的應用程式需要大量的使用權限，您可以在他們嘗試使用應用程式的某些功能時，以累加的方式逐漸向使用者收集這些使用權限。

代表組織進行的管理員同意，仍然需要為應用程式註冊靜態使用權限，因此如果您需要系統管理員代表整個組織提供同意，您應在應用程式註冊入口網站中，設定應用程式的那些使用權限。 這可減少組織系統管理員設定應用程式所需的週期。

## <a name="scopes-not-resources"></a>範圍，而非資源

若為使用 v1.0 端點的應用程式，應用程式能以**資源**或權杖收件者的方式運作。 資源可以定義它所了解的許多**範圍**或 **oAuth2Permissions**，讓用戶端應用程式得以針對特定一組範圍向該資源要求權杖。 將 Microsoft 圖形 API 視為資源的示例：

* 資源識別碼，或 `AppID URI`：`https://graph.microsoft.com/`
* 範圍，或 `oAuth2Permissions`：`Directory.Read`、`Directory.Write` 等。

對於 Microsoft 標識平臺終結點，這一點是正確的。 應用程式仍可作為資源、定義範圍並依據 URI 識別。 用戶端應用程式仍可要求存取這些範圍。 但是，用戶端請求這些許可權的方式已更改。

對於 v1.0 端點，Azure AD 的 OAuth 2.0 授權要求可能如下所示：

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.microsoft.com/
...
```

其中 **resource** 參數指出用戶端應用程式要求授權的資源。 Azure AD 根據 Azure 入口網站中的靜態設定計算應用程式所需的權限，並據以發出權杖。

對於使用 Microsoft 標識平臺終結點的應用程式，相同的 OAuth 2.0 授權請求如下所示：

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.microsoft.com/directory.read%20https://graph.microsoft.com/directory.write
...
```

其中 **scope** 參數指出用戶端應用程式要求授權的資源和使用權限。 所需的資源仍存在於要求中，它包含在 scope 參數的每個值中。 以這種方式使用範圍參數可使 Microsoft 標識平臺終結點更符合 OAuth 2.0 規範，並更符合常見的行業慣例。 它還使應用能夠執行[增量同意](#incremental-and-dynamic-consent)- 僅當應用程式需要許可權時請求許可權，而不是預先請求許可權。

## <a name="well-known-scopes"></a>知名的範圍

### <a name="offline-access"></a>離線存取

使用 Microsoft 標識平臺終結點的應用可能需要使用新的已知應用許可權 -`offline_access`範圍。 如果應用程式需要長期代表使用者存取資源，則所有應用程式都需要要求此權限，即使使用者可能不會主動使用此應用程式亦然。 在同意對話方塊中， `offline_access` 範圍會對使用者顯示為 [隨時存取您的資料]****，而使用者必須加以同意。 請求該`offline_access`許可權將使 Web 應用能夠從 Microsoft 標識平臺終結點接收 OAuth 2.0 refresh_tokens。 重新整理權杖屬於長效權杖，可用來交換新的 OAuth 2.0 存取權杖以延長存取期間。

如果應用不請求作用域，`offline_access`它將不會收到刷新權杖。 這表示當您在 OAuth 2.0 授權碼流程中兌換授權碼時，只會從 `/token` 端點接收一個存取權杖。 該存取權杖會短時間維持有效 (通常是一小時)，但最後終將過期。 屆時，您的應用程式必須將使用者重新導向回到 `/authorize` 端點以擷取新的授權碼。 在此重新導向期間，視應用程式的類型而定，使用者或許不需要再次輸入其認證或重新同意使用權限。

要瞭解有關 OAuth 2.0`refresh_tokens`和`access_tokens`， 請瞭解有關 ， 請查看 Microsoft[標識平臺協定引用](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

### <a name="openid-profile-and-email"></a>OpenID、設定檔和電子郵件

從歷史上看，與 Microsoft 標識平臺一起構建最基本的 OpenID Connect 登錄流將在生成的*id_token*中提供有關使用者的資訊。 id_token 中的宣告可以包含使用者的名稱、慣用使用者名稱、電子郵件地址、物件識別碼等等。

現在會限制 `openid` 範圍可提供應用程式存取權的資訊。 `openid` 範圍只允許您的應用程式將使用者登入，並接收使用者的應用程式特定識別碼。 如果您想要取得有關應用程式中使用者的個人資料，您的應用程式便需要向使用者要求其他使用權限。 兩個新範圍 (`email` 和 `profile`) 可讓您要求其他使用權限。

* 該`email`範圍允許應用通過id_token`email`中的聲明訪問使用者的主電子郵件地址，前提是使用者具有可定址的電子郵件地址。
* 該`profile`作用域使應用可以訪問id_token中有關使用者的所有其他基本資訊，如使用者姓名、首選使用者名、物件識別碼 等。

這可讓您以最低洩漏的方式撰寫應用程式，所以您只可以向使用者要求應用程式執行其作業所需的資訊集。 有關這些作用域的詳細資訊，請參閱 Microsoft[標識平臺作用域引用](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

## <a name="token-claims"></a>權杖宣告

預設情況下，Microsoft 標識平臺終結點在其權杖中發出一組較小的聲明，以保持有效負載較小。 如果 v1.0 權杖中依賴于特定聲明的應用和服務，預設情況下在 Microsoft 標識平臺權杖中不再提供，請考慮使用[可選聲明](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)功能來包括該聲明。

> [!IMPORTANT]
> v1.0 和 v2.0 權杖可以同時由 v1.0 和 v2.0 終結點頒發！ id_tokens*始終*與請求的終結點匹配，並且訪問權杖*始終*與用戶端將使用該權杖調用的 Web API 預期的格式匹配。  因此，如果應用使用 v2.0 終結點獲取權杖來調用 Microsoft Graph（它期望 v1.0 格式訪問權杖），則應用將收到 v1.0 格式的權杖。  

## <a name="limitations"></a>限制

使用 Microsoft 標識平臺時，需要注意一些限制。

構建與 Microsoft 標識平臺集成的應用程式時，需要確定 Microsoft 標識平臺終結點和身份驗證協定是否滿足您的需要。 v1.0 終結點和平臺仍然完全支援，在某些方面，它比 Microsoft 標識平臺功能更豐富。 但是，Microsoft 標識平臺為開發人員[帶來了顯著的好處](azure-ad-endpoint-comparison.md)。

以下是開發人員的簡化建議：

* 如果您希望或需要在應用程式中支援個人 Microsoft 帳戶，或者正在編寫新應用程式，請使用 Microsoft 標識平臺。 但在這樣做之前，請務必了解此文章中討論的限制。
* 如果要遷移或更新依賴于 SAML 的應用程式，則不能使用 Microsoft 標識平臺。 相反，請參閱[Azure AD v1.0 指南](v1-overview.md)。

Microsoft 標識平臺終結點將不斷發展，以消除此處列出的限制，因此您只需使用 Microsoft 標識平臺終結點。 同時，使用本文確定 Microsoft 標識平臺終結點是否適合您。 我們將繼續更新本文以反映 Microsoft 標識平臺終結點的目前狀態。 請返回以根據 Microsoft 標識平臺功能重新評估您的要求。

### <a name="restrictions-on-app-registrations"></a>應用程式註冊的限制

對於要與 Microsoft 標識平臺終結點集成的每個應用，可以在 Azure 門戶中的新[**應用註冊**體驗](https://aka.ms/appregistrations)中創建應用註冊。 現有 Microsoft 帳戶應用及閘戶不相容，但所有 Azure AD 應用均處於相容位置或註冊時間。

支援公司和學校帳戶及個人帳戶的應用程式註冊有下列注意事項：

* 每個應用程式識別碼只允許兩個應用程式密碼。
* 沒有在租用戶中註冊的應用程式，只能由註冊該應用程式的帳戶管理。 它無法與其他開發人員共用。 在應用程式註冊入口網中使用個人 Microsoft 帳戶註冊的大部分應用程式都是此情況。 如果要與多個開發人員共用應用註冊，請使用 Azure 門戶的新**應用註冊**部分在租戶中註冊應用程式。
* 所允許的重新導向 URL 的格式有幾項限制。 如需有關重新導向 URL 的詳細資訊，請參閱下一節。

### <a name="restrictions-on-redirect-urls"></a>重新導向 URL 的限制

為 Microsoft 標識平臺註冊的應用僅限於一組有限的重定向 URL 值。 Web 應用程式和服務的重新導向 URL 必須以配置 `https` 開頭，而且所有重新導向 URL 值必須共用單一 DNS 網域。  註冊系統會將現有重新導向 URL 的整個 DNS 名稱與您要新增之重新導向 URL 的 DNS 名稱做比較。 系統也支援將 `http://localhost` 作為重新導向 URL。  

如果下列任一條件成立，新增 DNS 名稱的要求就會失敗︰  

* 新重新導向 URL 的整個 DNS 名稱與現有重新導向 URL 的 DNS 名稱不相符。
* 新重新導向 URL 的整個 DNS 名稱不是現有重新導向 URL 的子網域。

#### <a name="example-1"></a>範例 1

如果應用程式的重新導向 URL 是 `https://login.contoso.com`，您可以新增 DNS 名稱完全相符的重新導向 URL，如下列範例所示：

`https://login.contoso.com/new`

或者，您可以參照 login.contoso.com 的 DNS 子網域，如下列範例所示：

`https://new.login.contoso.com`

#### <a name="example-2"></a>範例 2

如果您希望應用程式以 `login-east.contoso.com` 和 `login-west.contoso.com` 作為重新導向 URL，您必須依下列順序新增這些重新導向 URL︰

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

您可以添加後兩個，因為它們是第一個重定向 URL 的子域，contoso.com。

對於特定應用程式，只能有 20 個回復 URL - 此限制適用于註冊支援的所有應用類型（單頁應用程式 （SPA）、本機用戶端、Web 應用和服務）。  

要瞭解如何註冊應用以與 Microsoft 標識平臺一起使用，請參閱[使用新的應用註冊體驗註冊應用](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

### <a name="restrictions-on-libraries-and-sdks"></a>程式庫和 SDK 的限制

目前，對 Microsoft 標識平臺終結點的庫支援有限。 如果要在生產應用程式中使用 Microsoft 標識平臺終結點，可以使用以下選項：

* 如果要構建 Web 應用程式，則可以安全地使用通常可用的伺服器端中介軟體執行登錄和權杖驗證。 這些包括了適用於 ASP.NET 的 OWIN OpenID Connect 中介軟體和 Node.js Passport 外掛程式。 有關使用 Microsoft 中介軟體的代碼示例，請參閱[Microsoft 標識平臺入門](../develop/v2-overview.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#getting-started)部分。
* 如果要構建桌面或移動應用程式，則可以使用 Microsoft 身份驗證庫 （MSAL） 之一。 這些庫通常可用或位於生產支援的預覽版中，因此在生產應用程式中使用它們是安全的。 您可以在[驗證程式庫參考](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)中，閱讀更多和預覽條款與可用程式庫相關的詳細資訊。
* 對於 Microsoft 庫未涵蓋的平臺，您可以通過直接在應用程式代碼中發送和接收協定消息與 Microsoft 標識平臺終結點集成。 OpenID 連接和 OAuth 協定[被顯式記錄](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)，以説明您進行此類集成。
* 最後，您可以使用開源 OpenID Connect 和 OAuth 庫與 Microsoft 標識平臺終結點集成。 Microsoft 標識平臺終結點應與許多開源協定庫相容，無需更改。 這類的程式庫的可用性會依語言和平台而有所不同。 [OpenID Connect](https://openid.net/connect/) \(英文\) 和 [OAuth 2.0](https://oauth.net/2/) \(英文\) 網站保有一份常用的實作清單。 有關詳細資訊，請參閱[Microsoft 標識平臺和身份驗證庫](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)，以及使用 Microsoft 標識平臺終結點測試的開源用戶端庫和示例的清單。
* 作為參考，Microsoft`.well-known`標識平臺公共終結點的終結點為`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`。 使用您的租用戶識別碼來取代 `common`，以取得您租用戶特有的資料。  

### <a name="protocol-changes"></a>通訊協定變更

Microsoft 標識平臺終結點不支援 SAML 或 WS-聯合;它僅支援 OpenID 連接和 OAuth 2.0。  對於 1.0 版端點 OAuth 2.0 通訊協定的重大變更為： 

* 如果設定選擇性宣告**或**在要求中指定 scope=email，則會傳回 `email` 宣告。 
* 現已支援代替 `resource` 參數的 `scope` 參數。  
* 已修改許多回應，使它們更符合 OAuth 2.0 的規範，例如正確地將 `expires_in` 傳回為 Int 而不是字串。  

要更好地瞭解 Microsoft 標識平臺終結點中支援的協定功能範圍，請參閱[OpenID 連接和 OAuth 2.0 協定引用](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

#### <a name="saml-restrictions"></a>SAML 限制

如果在 Windows 應用程式中使用了活動目錄身份驗證庫 （ADAL），則可能已利用 Windows 集成身份驗證，該身份驗證使用安全斷言標記語言 （SAML） 斷言授予。 有了此授與之後，同盟 Azure AD 租用戶的使用者便可向其內部部署 Active Directory 執行個體以無訊息方式進行驗證，而不需輸入認證。 Microsoft 標識平臺終結點不支援 SAML 斷言授予。
