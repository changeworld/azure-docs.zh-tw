---
title: Azure Active Directory 重大變更參考
description: 了解對 Azure AD 通訊協定所進行且可能影響您應用程式的變更。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 5/4/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 94c34e6f7cb24ff749e5de95f1c28a496700af80
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348716"
---
# <a name="whats-new-for-authentication"></a>驗證有什麼新功能？

> 將此 URL 貼入 RSS 摘要讀取器，以取得此頁面更新的通知：<br/>`https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us`

驗證系統會持續改變並新增功能，以提升安全性及標準合規性。 為了讓您隨時掌握最新開發訊息，此文章提供下列詳細資料相關資訊：

- 最新功能
- 已知問題
- 通訊協定變更
- 已被取代的功能

> [!TIP]
> 此頁面會定期更新，因此請時常瀏覽。 除非另有註明，否則這些變更僅會針對新註冊的應用程式施行。

## <a name="upcoming-changes"></a>即將推出的變更

未排定在這個時候推出任何變更。  請參閱下方，以瞭解或即將進入生產環境的變更。

## <a name="may-2020"></a>2020 年 5 月

### <a name="azure-government-endpoints-are-changing"></a>Azure Government 端點正在變更

**生效日期**：2020年6月5日 (完成)  

**受影響的端點**：全部

**受影響的通訊協定**：所有流程

自2018年6月1日起，Azure Government 的官方 Azure Active Directory (AAD) 授權單位變更 `https://login-us.microsoftonline.com` 為 `https://login.microsoftonline.us` 。 這種變更也適用于 Microsoft 365 GCC High 和 DoD，也就是 Azure Government AAD 的服務。 如果您擁有美國政府租使用者內的應用程式，則必須更新您的應用程式，以在端點上將使用者登入 `.us` 。  

從5月5日起，Azure AD 將開始強制端點變更，封鎖政府使用者使用公用端點 () 登入位於美國政府租使用者中的應用程式 `microsoftonline.com` 。  受影響的應用程式將會開始看到錯誤 `AADSTS900439`  -  `USGClientNotSupportedOnPublicEndpoint` 。 此錯誤表示應用程式正嘗試在公用雲端端點上登入美國政府使用者。 如果您的應用程式是在公用雲端租使用者中，而且想要支援美國政府使用者，您必須 [更新您的應用程式，以明確地支援它們](./authentication-national-cloud.md)。 這可能需要在美國政府雲端中建立新的應用程式註冊。 

若要強制執行這項變更，將會使用漸進式推出，並根據美國政府雲端的使用者登入的應用程式-不常使用的應用程式登入我們的應用程式-不常看到強制執行，而且美國政府使用者經常使用的應用程式將會優先套用強制套用。 我們預計在2020年6月的所有應用程式都必須完成強制。 

如需詳細資訊，請參閱 [此遷移的 Azure Government blog 文章](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)。 

## <a name="march-2020"></a>2020 年 3 月

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>使用者密碼將限制為256個字元。

**生效日期**：2020年3月13日

**受影響的端點**：全部

**受影響的通訊協定**：所有使用者流程。

密碼超過256個字元且直接登入 Azure AD (（而不是 ADFS) 的同盟 IDP）的使用者，將無法從2020年3月13日開始登入，並要求您改為重設其密碼。  系統管理員可能會收到協助重設使用者密碼的要求。

登入記錄檔中的錯誤將會是 AADSTS 50052： InvalidPasswordExceedsMaxLength

消息： `The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

補救：

因為使用者的密碼超過允許的最大長度，所以無法登入。 他們應該洽詢其系統管理員以重設密碼。 如果已針對其租使用者啟用 SSPR，他們可以遵循 [忘記密碼] 連結來重設其密碼。



## <a name="february-2020"></a>2020 年 2 月

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>空白片段會附加至登入端點的每個 HTTP 重新導向。

**生效日期**：2020年2月8日

**受影響的端點**：v1.0 和 v2.0

**受影響的通訊協定**：使用 response_type = query 的 OAUTH 和 OIDC 流程-這涵蓋了在某些情況下的 [授權碼流程](v2-oauth2-auth-code-flow.md) ，以及 [隱含流程](v2-oauth2-implicit-grant-flow.md)。

當驗證回應透過 HTTP 重新導向從 login.microsoftonline.com 傳送至應用程式時，服務會將空的片段附加至回復 URL。  這可確保瀏覽器抹除驗證要求中的任何現有片段，以防止重新導向攻擊類別。  沒有任何應用程式應該相依于這項行為。


## <a name="august-2019"></a>2019 年 8 月

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>POST 表單語義將會強制執行更嚴格的空格，而且會忽略引號

**生效日期**：2019年9月2日

**受影響的端點**：v1.0 和 v2.0

**受影響的通訊協定**：使用隨處張貼 ([用戶端認證](./v2-oauth2-client-creds-grant-flow.md)、 [授權碼兌換](./v2-oauth2-auth-code-flow.md)、 [ROPC](./v2-oauth-ropc.md)、 [OBO](./v2-oauth2-on-behalf-of-flow.md)和重新整理 [權杖兌換](./v2-oauth2-auth-code-flow.md#refresh-the-access-token)) 

從9/2 的周開始，使用 POST 方法的驗證要求將會使用更嚴格的 HTTP 標準進行驗證。  具體而言，空格和雙引號 ( ") 將不再從要求表單值中移除。 這些變更不需要中斷任何現有的用戶端，並可確保每次都能可靠地處理傳送至 Azure AD 的要求。 在未來 (查看上述) 我們打算另外拒絕重複的參數，並忽略要求內的 BOM。

範例：

今天， `?e=    "f"&g=h` 會以相同 `?e=f&g=h` 的方式剖析 `e`  ==  `f` 。  透過這項變更，現在將會剖析它，如此 `e`  ==  `    "f"` 一來，這不太可能是有效的引數，而且要求現在會失敗。


## <a name="july-2019"></a>2019 年 7 月

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>只有在資源租使用者中有用戶端應用程式時，才會發出單一租使用者應用程式的僅限應用程式權杖

**生效日期**：2019年7月26日

**受影響的端點**： [1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) [版](./v2-oauth2-client-creds-grant-flow.md)和 v2。0

**受影響的通訊協定**： [用戶端認證 (僅限應用程式權杖)](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)

安全性變更已于7月26日推出，這會變更透過用戶端認證授與) 發出的僅限應用程式權杖 (方式。 之前，允許應用程式取得權杖來呼叫任何其他應用程式，而不論租使用者中是否有同意該應用程式的存在。  此行為已更新，因此資源 (有時也稱為 web Api) 設定為單一租使用者 (預設) ，用戶端應用程式必須存在於資源租使用者中。  請注意，用戶端與 API 之間的現有同意仍不是必要的，而且應用程式仍應執行自己的授權檢查，以確保宣告 `roles` 存在且包含 API 的預期值。

此案例的錯誤訊息目前狀態為：

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

若要解決此問題，請使用系統管理員同意體驗，在您的租使用者中建立用戶端應用程式服務主體，或手動建立。  此需求可確保租使用者已將應用程式許可權提供給租使用者中的操作。

#### <a name="example-request"></a>範例要求

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` 在此範例中，資源租使用者 (授權單位) 是 contoso.com，資源應用程式是針對 Contoso 租使用者呼叫的單一租使用者應用程式， `gateway.contoso.com/api` 而用戶端應用程式是 `14c88eee-b3e2-4bb0-9233-f5e3053b3a28` 。  如果用戶端應用程式在 Contoso.com 內有服務主體，則此要求可以繼續。  但是，如果不是，則要求將會失敗，並出現上述錯誤。

但是，如果 Contoso 閘道應用程式是多租使用者應用程式，則不論用戶端應用程式在 Contoso.com 內有服務主體，要求都會繼續。

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>重新導向 Uri 現在可以包含查詢字串參數

**生效日期**：2019年7月22日

**受影響的端點**：v1.0 和 v2.0

**受影響的通訊協定**：所有流程

根據 [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)，Azure AD 的應用程式現在可以註冊並使用重新導向 (回復) uri 和靜態查詢參數 (例如 `https://contoso.com/oauth2?idp=microsoft` OAuth 2.0 要求的) 。  因為動態重新導向 Uri 代表安全性風險，所以不能用來跨驗證要求保留狀態資訊，而是使用 `state` 參數。

靜態查詢參數必須符合重新導向 uri 的字串比對，例如重新導向 URI 的任何其他部分-如果未註冊符合 URI 解碼 redirect_uri 的字串，則會拒絕要求。  如果在應用程式註冊中找到 URI，則會使用整個字串來重新導向使用者，包括靜態查詢參數。

請注意，目前 (于2019年7月) 結束時，Azure 入口網站中的應用程式註冊 UX 仍會封鎖查詢參數。  不過，您可以手動編輯應用程式資訊清單，以新增查詢參數，並在您的應用程式中進行測試。


## <a name="march-2019"></a>2019 年 3 月

### <a name="looping-clients-will-be-interrupted"></a>迴圈用戶端將會中斷

**生效日期**：2019年3月25日

**受影響的端點**：v1.0 和 v2.0

**受影響的通訊協定**：所有流程

用戶端應用程式有時可能會行為失常，在短時間內發出數百個相同的登入要求。  這些要求可能會成功，但這些要求可能會導致 IDP 的使用者體驗不佳和工作負載提高，而增加所有使用者的延遲，並減少 IDP 的可用性。  這些應用程式會在正常使用範圍之外運作，而且應該更新以正確運作。

發出重複要求多次的用戶端將會收到 `invalid_grant` 錯誤： `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` 。

大部分的用戶端不需要變更行為以避免此錯誤。  只有設定不正確的用戶端 (沒有權杖快取的用戶端，或已) 的提示迴圈可能會受到此錯誤影響。  用戶端會根據下列因素，在每個實例上以個別實例為基礎來追蹤 () ：

* 使用者提示（如果有的話）

* 要求的範圍或資源

* 用戶端識別碼

* 重新導向 URI

* 回應類型和模式

在短時間內提出多個要求 (15 +) 的應用程式 (5 分鐘) 將會收到 `invalid_grant` 錯誤，說明它們正在迴圈中。  要求的權杖有足夠長時間存留的存留期 (10 分鐘的最小值（60分鐘），預設) ，因此不需要在這段時間內重複要求。

所有應用程式應藉 `invalid_grant` 由顯示互動式提示來處理，而不是以無訊息方式要求權杖。  為了避免發生此錯誤，用戶端應該確保它們能夠正確地快取其所接收的權杖。


## <a name="october-2018"></a>2018 年 10 月

### <a name="authorization-codes-can-no-longer-be-reused"></a>授權碼再也無法重複使用

**生效日期**：2018 年 11 月 15 日

**受影響的端點**：v1.0 和 v2.0

**受影響的通訊協定**：[碼流程](v2-oauth2-auth-code-flow.md)

從 2018 年 11 月 15 日起，Azure AD 將不再接受將先前使用的驗證碼用於應用程式。 這項安全性變更有助於讓 Azure AD 與 OAuth 規格一致，且將會對 v1 和 v2 端點強制執行。

如果您的應用程式重複使用授權碼取得多個資源的權杖，建議您先使用授權碼取得重新整理權杖，再使用該重新整理權杖取得其他資源的額外權杖。 授權碼只能使用一次，但重新整理權杖可跨多個資源多次使用。 在 OAuth 程式碼流程期間嘗試重複使用驗證碼的任何新應用程式，會收到 invalid_grant 錯誤。

如需關於重新整理權杖的詳細資訊，請參閱[重新整理存取權杖](v2-oauth2-auth-code-flow.md#refresh-the-access-token)。  使用使用 ADAL 或 MSAL，會由程式庫為您處理此操作 - 將 'AcquireTokenByAuthorizationCodeAsync' 的執行個體取代為 'AcquireTokenSilentAsync'。

## <a name="may-2018"></a>2018 年 5 月

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>識別碼權杖無法用於 OBO 流程

**日期**：2018 年 5 月 1 日

**受影響的端點**：v1.0 和 v2.0

**受影響的通訊協定**：隱含流程和代理者 [流程](v2-oauth2-on-behalf-of-flow.md)

在 2018 年 5 月 1 日以後，id_tokens 無法用來作為新應用程式 OBO 流程中的判斷提示。 應該改為使用存取權杖來保護 API，即使是在相同應用程式的用戶層與中介層之間也是一樣。 在 2018 年 5 月 1 日以前註冊的應用程式會持續運作，並且可以用 id_tokens 交換存取權杖；不過，此模式並不是最佳做法。

為了因應此變更，您可以執行下列操作：

1. 為您的應用程式建立一個或多個範圍的 web API。 這個明確的進入點可以獲得更精細的控制權和安全性。
1. 在您的應用程式資訊清單、[Azure 入口網站](https://portal.azure.com)或[應用程式註冊入口網站](https://apps.dev.microsoft.com)中，確保已允許應用程式透過隱含流程簽發存取權杖。 這可透過 `oauth2AllowImplicitFlow` 金鑰進行控制。
1. 當您的用戶端應用程式透過要求 id_token 時 `response_type=id_token` ，也會 `response_type=token` 針對上面建立的 web API，要求存取權杖 () 。 因此，當使用 v2.0 端點時，`scope` 參數看起來應該像 `api://GUID/SCOPE`。 在 v1.0 端點上，`resource` 參數應該是 Web API 的應用程式 URI。
1. 將此存取權杖傳遞至中介層以取代 id_token。
