---
title: Azure Active Directory 中斷性變更參考 | Microsoft Docs
description: 了解對 Azure AD 通訊協定所進行且可能影響您應用程式的變更。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: c6ed72e5c94191411572c6ab67533141e2fe47d6
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185810"
---
# <a name="whats-new-for-authentication"></a>驗證有什麼新功能？ 

>取得此頁面的更新通知。 只要將[此 URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us)新增至您的 RSS 摘要讀取器即可。

驗證系統會持續改變並新增功能，以提升安全性及標準合規性。 為了讓您隨時掌握最新開發訊息，此文章提供下列詳細資料相關資訊：

- 最新功能
- 已知問題
- 通訊協定變更
- 已被取代的功能

> [!TIP] 
> 此頁面會定期更新，因此請時常瀏覽。 除非另有註明，否則這些變更僅會針對新註冊的應用程式施行。  

## <a name="upcoming-changes"></a>即將推出的變更

未排定在這個時候推出任何變更。  如需或即將進入生產階段的變更，請參閱下方。 

## <a name="february-2020"></a>2020 年 2 月 

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>將會從登入端點將空的片段附加至每個 HTTP 重新導向。 

**生效日期**：2020年2月8日

**受影響的端點**：1.0 和 2.0 版

**受影響的通訊協定**：使用 response_type = 查詢的 OAuth 和 OIDC 流程-這涵蓋了在某些情況下的[授權碼流程](v2-oauth2-auth-code-flow.md)，以及[隱含流程](v2-oauth2-implicit-grant-flow.md)。 

當驗證回應透過 HTTP 重新導向從 login.microsoftonline.com 傳送至應用程式時，服務會將空的片段附加至回復 URL。  這可確保瀏覽器抹除驗證要求中任何現有的片段，藉此防止重新導向攻擊的類別。  沒有任何應用程式應該依賴此行為。 


## <a name="august-2019"></a>2019 年 8 月

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>將會更嚴格地強制執行張貼表單的語義-空格，而且將會忽略引號

**生效日期**：2019年9月2日

**受影響的端點**：1.0 和 2.0 版

**受影響的通訊協定**：使用隨處張貼（[用戶端認證](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)、[授權碼兌換](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)、 [ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)、 [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)，以及重新整理[權杖兌換](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)）

從9/2 開始，使用 POST 方法的驗證要求將會使用更嚴格的 HTTP 標準進行驗證。  具體而言，不會再從要求表單值中移除空格和雙引號（"）。 這些變更不應該中斷任何現有的用戶端，而且會確保每次都能可靠地處理傳送至 Azure AD 的要求。 在未來（請參閱上文）中，我們打算另外拒絕重複的參數，並忽略要求內的 BOM。 

範例：

目前，`?e=    "f"&g=h` 的剖析方式與 `?e=f&g=h` 相同，因此 `e` == `f`。  透過這項變更，現在會進行剖析，讓 `e` == `    "f"`-這不太可能是有效的引數，而且要求現在會失敗。 


## <a name="july-2019"></a>2019 年 7 月

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>只有在資源租使用者中有用戶端應用程式時，才會發出單一租使用者應用程式的應用程式權杖

**生效日期**：2019年7月26日

**受影響的端點**：[1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)和 [v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) 兩者

**受影響的通訊協定**：[用戶端認證（僅限應用程式權杖）](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

安全性變更會在7月26日生效，變更應用程式專用權杖（透過用戶端認證授與）的發行方式。 先前，允許應用程式取得權杖以呼叫其他任何應用程式，而不論租使用者中的存在與否，或同意該應用程式的角色。  此行為已更新，因此，如果資源（有時稱為 Web Api）設為單一租使用者（預設值），則用戶端應用程式必須存在於資源租使用者內。  請注意，用戶端和 API 之間的現有同意仍不是必要的，而且應用程式仍應執行自己的授權檢查，以確保有 `roles` 宣告，並包含 API 的預期值。

此案例的錯誤訊息目前指出： 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

若要解決此問題，請使用系統管理員同意體驗，在您的租使用者中建立用戶端應用程式服務主體，或手動建立。  此需求可確保租使用者已提供應用程式許可權，以在租使用者內操作。  

#### <a name="example-request"></a>範例要求

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` 在此範例中，資源租使用者（授權單位）為 contoso.com，資源應用程式是 Contoso 租使用者稱為 `gateway.contoso.com/api` 的單一租使用者應用程式，而且用戶端應用程式會 `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`。  如果用戶端應用程式在 Contoso.com 內具有服務主體，則此要求可以繼續。  不過，如果沒有，則要求將會失敗，並出現上述錯誤。  

不過，如果 Contoso 閘道應用程式是多租使用者應用程式，則無論用戶端應用程式在 Contoso.com 內擁有服務主體，要求都會繼續。  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>重新導向 Uri 現在可以包含查詢字串參數

**生效日期**：2019 年 7 月 22 日

**受影響的端點**：1.0 和 2.0 版

**受影響的通訊協定**：所有流程

根據[RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)，Azure AD 應用程式現在可以向靜態查詢參數（例如 OAuth 2.0 要求的 https://contoso.com/oauth2?idp=microsoft) ）註冊並使用重新導向（回復） uri。  動態重新導向 Uri 仍然禁止，因為它們代表安全性風險，這不能用來在驗證要求中保留狀態資訊-因此，請使用 `state` 參數。

靜態查詢參數受限於重新導向 URI 的字串比對，如同重新導向 URI 的任何其他部分-如果未註冊符合 URI 解碼 redirect_uri 的字串，則會拒絕要求。  如果在應用程式註冊中找到 URI，則會使用整個字串來重新導向使用者，包括靜態查詢參數。 

請注意，在這段時間（2019年7月底），Azure 入口網站中的應用程式註冊 UX 仍會封鎖查詢參數。  不過，您可以手動編輯應用程式資訊清單，以加入查詢參數，並在您的應用程式中進行測試。  


## <a name="march-2019"></a>2019 年 3 月

### <a name="looping-clients-will-be-interrupted"></a>迴圈用戶端將會中斷

**生效日期**：2019年3月25日

**受影響的端點**：1.0 和 2.0 版

**受影響的通訊協定**：所有流程

用戶端應用程式有時可能會行為失常，在短時間內發出數百個相同的登入要求。  這些要求不一定會成功，但它們都是針對 IDP 帶來不良的使用者體驗和更高的工作負載，進而增加所有使用者的延遲，並降低 IDP 的可用性。  這些應用程式會在正常使用範圍外運作，而且應該更新為正確的行為。  

發出重複要求多次的用戶端將會收到 `invalid_grant` 錯誤： `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`。 

大部分的用戶端都不需要變更行為來避免這個錯誤。  只有設定不正確的用戶端（沒有權杖快取或出現提示迴圈的那些）會受到此錯誤影響。  用戶端會根據下列因素，在本機（透過 cookie）追蹤每個實例：

* 使用者提示（如果有的話）

* 要求的範圍或資源

* 用戶端識別碼

* 重新導向 URI

* 回應類型和模式

在短時間（5分鐘）內建立多個要求（15 +）的應用程式將會收到 `invalid_grant` 錯誤，說明它們正在進行迴圈。  所要求的權杖具有足夠的長時間存留期（預設為10分鐘，最低為60分鐘），因此不需要在這段時間內重複要求。  

所有應用程式都應該藉由顯示互動式提示來處理 `invalid_grant`，而不是以無訊息方式要求權杖。  為了避免此錯誤，用戶端應確保它們能夠正確地快取所接收的權杖。


## <a name="october-2018"></a>2018 年 10 月

### <a name="authorization-codes-can-no-longer-be-reused"></a>授權碼再也無法重複使用

**生效日期**：2018 年 11 月 15 日

**受影響的端點**：1.0 和 2.0 版

**受影響的通訊協定**：[程式碼流程](v2-oauth2-auth-code-flow.md)

從 2018 年 11 月 15 日起，Azure AD 將不再接受將先前使用的驗證碼用於應用程式。 這項安全性變更有助於讓 Azure AD 與 OAuth 規格一致，且將會對 v1 和 v2 端點強制執行。

如果您的應用程式重複使用授權碼取得多個資源的權杖，建議您先使用授權碼取得重新整理權杖，再使用該重新整理權杖取得其他資源的額外權杖。 授權碼只能使用一次，但重新整理權杖可跨多個資源多次使用。 在 OAuth 程式碼流程期間嘗試重複使用驗證碼的任何新應用程式，會收到 invalid_grant 錯誤。

如需關於重新整理權杖的詳細資訊，請參閱[重新整理存取權杖](v2-oauth2-auth-code-flow.md#refresh-the-access-token)。  使用使用 ADAL 或 MSAL，會由程式庫為您處理此操作 - 將 'AcquireTokenByAuthorizationCodeAsync' 的執行個體取代為 'AcquireTokenSilentAsync'。 

## <a name="may-2018"></a>2018 年 5 月

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>識別碼權杖無法用於 OBO 流程

**日期**：2018 年 5 月 1 日

**受影響的端點**：1.0 和 2.0 版

**受影響的通訊協定**：隱含流程和代理者[流程](v2-oauth2-on-behalf-of-flow.md)

在 2018 年 5 月 1 日以後，id_tokens 無法用來作為新應用程式 OBO 流程中的判斷提示。 應該改為使用存取權杖來保護 API，即使是在相同應用程式的用戶層與中介層之間也是一樣。 在 2018 年 5 月 1 日以前註冊的應用程式會持續運作，並且可以用 id_tokens 交換存取權杖；不過，此模式並不是最佳做法。

為了因應此變更，您可以執行下列操作：

1. 為您的應用程式建立 Web API，具有一或多個範圍。 這個明確的進入點可以獲得更精細的控制權和安全性。
1. 在您的應用程式資訊清單、[Azure 入口網站](https://portal.azure.com)或[應用程式註冊入口網站](https://apps.dev.microsoft.com)中，確保已允許應用程式透過隱含流程簽發存取權杖。 這可透過 `oauth2AllowImplicitFlow` 金鑰進行控制。
1. 當您的用戶端應用程式透過 `response_type=id_token` 要求 id_token 時，也會為上述建立的 Web API 要求存取權杖 (`response_type=token`)。 因此，當使用 v2.0 端點時，`scope` 參數看起來應該像 `api://GUID/SCOPE`。 在 v1.0 端點上，`resource` 參數應該是 Web API 的應用程式 URI。
1. 將此存取權杖傳遞至中介層以取代 id_token。  
