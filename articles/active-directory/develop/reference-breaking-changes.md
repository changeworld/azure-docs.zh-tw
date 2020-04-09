---
title: Azure 活動目錄中斷更改參考
description: 了解對 Azure AD 通訊協定所進行且可能影響您應用程式的變更。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 52ede7d66bd657b5002272e34673b4b01c9ab1aa
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883452"
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

未排定在這個時候推出任何變更。  有關正在生產或即將投產的更改,請參閱下文。 

## <a name="march-2020"></a>2020 年 3 月 

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>用戶密碼將限制為 256 個字元。

**生效日期**:2020年3月13日

**受影響的端點**：v1.0 和 v2.0

**協議影響**:所有使用者流。 

從 2020 年 3 月 13 日起,密碼長度超過 256 個字元登錄到 Azure AD 的使用者(相對於 ADFS 等聯合 IDP)將無法登錄,並被要求重置其密碼。  管理員可能會收到幫助重置用戶密碼的請求。 

登入紀錄中的錯誤將是 AADSTS 50052:無效密碼超過最大值長度

訊息:`The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

補救：

用戶無法登錄,因為他們的密碼超過允許的最大長度。 他們應該聯繫他們的管理員重置密碼。 如果為其租戶啟用了 SSPR,則可以按照"忘記密碼"鏈接重置密碼。



## <a name="february-2020"></a>2020 年 2 月 

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>空片段將追加到從登錄終結點重定向的每個 HTTP。 

**生效日期**:2020年2月8日

**受影響的端點**：v1.0 和 v2.0

**受影響的協定**:使用response_type_查詢的 OAuth 和 OIDC 串流 - 這在某些情況下涵蓋了[授權代碼串流](v2-oauth2-auth-code-flow.md)和[隱式流](v2-oauth2-implicit-grant-flow.md)。 

當身份驗證回應通過 HTTP 重定向從 login.microsoftonline.com 發送到應用程式時,服務將附加一個空片段到答覆 URL。  這通過確保瀏覽器擦除身份驗證請求中的任何現有片段來防止一類重定向攻擊。  任何應用都不應依賴於此行為。 


## <a name="august-2019"></a>2019 年 8 月

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>POST 表單語義將更嚴格地強制 - 空格和引號將被忽略

**生效日期**:2019年9月2日

**受影響的端點**：v1.0 和 v2.0

**協定影響**:使用 POST 的任何地方([客戶端認證](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)、[授權代碼兌換](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)[、ROPC、OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)和[OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)[刷新權杖兌換](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token))

從 9/2 開始,將使用 POST 方法的身份驗證請求將使用更嚴格的 HTTP 標準進行驗證。  具體而言,空格和雙引號 (") 將不再從請求窗體值中刪除。 這些更改不會破壞任何現有用戶端,並且將確保每次可靠地處理發送到 Azure AD 的請求。 將來(見上文)我們計劃另外拒絕重複的參數,並在請求中忽略 BOM。 

範例：

今天,`?e=    "f"&g=h``?e=f&g=h`被 解析為`e` == `f`- 所以 .  通過此更改,現在將對其進行分析,以便`e` == `    "f"`- 這不太可能是一個有效的參數,並且請求現在將失敗。 


## <a name="july-2019"></a>2019 年 7 月

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>僅當資源租戶中存在用戶端應用時,才會頒發適用於單租戶應用程式的僅限應用權杖

**生效日期**:2019年7月26日

**受影響的終結點**: [v1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)和[v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**協定受影響的**:[客戶端認證(僅限應用權杖)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

安全更改於 7 月 26 日生效,該更改更改更改了僅應用權杖(通過用戶端認證授予)的頒發方式。 以前,應用程式允許令牌調用任何其他應用,而不管該應用程式同意在租戶中存在什麼角色。  此行為已更新,以便對於設置為單租戶的資源(有時稱為 Web API)(預設值),用戶端應用程式必須存在於資源租戶中。  請注意,用戶端和 API 之間的現有同意仍不需要,應用仍應執行自己的授權檢查,`roles`以確保 聲明存在並包含 API 的預期值。

此機制的錯誤訊息目前顯示: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

若要解決此問題,請使用管理同意體驗在租戶中創建用戶端應用程式服務主體,或手動創建它。  此要求可確保租戶已授予應用程式在租戶內操作的許可權。  

#### <a name="example-request"></a>範例要求

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`這個功能中,資源租戶(許可權)contoso.com,資源應用是為 Contoso 租戶呼`gateway.contoso.com/api`叫的單租戶應用,而用戶端應用`14c88eee-b3e2-4bb0-9233-f5e3053b3a28`為 。  如果用戶端應用在Contoso.com內具有服務主體,則此請求可以繼續。  但是,如果沒有,則請求將失敗,並出現上述錯誤。  

但是,如果 Contoso 閘道應用是多租戶應用程式,則無論用戶端應用在Contoso.com內具有服務主體,請求都將繼續。  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>重定向 URI 現在可以包含查詢字串參數

**生效日期**:2019年7月22日

**受影響的端點**：v1.0 和 v2.0

**協定影響**:所有流

根據[RFC 6749,Azure](https://tools.ietf.org/html/rfc6749#section-3.1.2)AD 應用程式現在可以註冊和使用具有靜態`https://contoso.com/oauth2?idp=microsoft`查詢參數(如 ) 的重定向(回復)URI的 OAuth 2.0 請求。  動態重定向 URI 仍然被禁止,因為它們表示安全風險,並且不能用於跨身份驗證請求保留狀態資訊 - 為此`state`,請使用 參數 。

靜態查詢參數與重定向 URI 的任何其他部分一樣,需要重新定向 URI 的字串匹配 - 如果未註冊與 URI 解碼redirect_uri匹配的字串,則請求將被拒絕。  如果在應用註冊中找到URI,則整個字串將用於重定向使用者,包括靜態查詢參數。 

請注意,此時(2019年7月底),Azure 門戶中的應用註冊 UX 仍阻止查詢參數。  但是,您可以手動編輯應用程式清單以添加查詢參數並在應用中測試此參數。  


## <a name="march-2019"></a>2019 年 3 月

### <a name="looping-clients-will-be-interrupted"></a>循環用戶端將中斷

**生效日期**:2019年3月25日

**受影響的端點**：v1.0 和 v2.0

**協定影響**:所有流

用戶端應用程式有時會行為不端,在短時間內發出數百個相同的登錄請求。  這些請求可能成功,也可能不成功,但它們都會導致 IDP 使用者體驗不佳和工作負載增加,增加所有使用者的延遲並降低 IDP 的可用性。  這些應用程式在正常使用範圍之外運行,應更新以正確運行。  

多次送出重複要求的客戶端送出錯誤`invalid_grant` `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`: 。 

大多數用戶端不需要更改行為以避免此錯誤。  只有配置錯誤的用戶端(沒有權杖快取的用戶端或已顯示提示循環的用戶端)才會受到此錯誤的影響。  用戶端依本地(透過 Cookie)本地追蹤客戶端:以下因素:

* 使用者提示(如果有)

* 要求的範圍或資源

* 用戶端識別碼

* 重新導向 URI

* 回應類型與模式

在短時間內(5 分鐘)發出多個請求 (15+) 的應用將收到`invalid_grant`一個錯誤 ,說明它們正在迴圈。  請求的權杖具有足夠長的存留期(預設情況下最少 10 分鐘,預設值為 60 分鐘),因此在此時間段內重複請求是不必要的。  

所有應用都應`invalid_grant`通過顯示互動式提示符來處理,而不是默默請求令牌。  為了避免此錯誤,客戶端應確保正確快取接收的權杖。


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

**受影響的協定**:隱式流和[代表流](v2-oauth2-on-behalf-of-flow.md)

在 2018 年 5 月 1 日以後，id_tokens 無法用來作為新應用程式 OBO 流程中的判斷提示。 應該改為使用存取權杖來保護 API，即使是在相同應用程式的用戶層與中介層之間也是一樣。 在 2018 年 5 月 1 日以前註冊的應用程式會持續運作，並且可以用 id_tokens 交換存取權杖；不過，此模式並不是最佳做法。

為了因應此變更，您可以執行下列操作：

1. 為您的應用程式建立 Web API，具有一或多個範圍。 這個明確的進入點可以獲得更精細的控制權和安全性。
1. 在您的應用程式資訊清單、[Azure 入口網站](https://portal.azure.com)或[應用程式註冊入口網站](https://apps.dev.microsoft.com)中，確保已允許應用程式透過隱含流程簽發存取權杖。 這可透過 `oauth2AllowImplicitFlow` 金鑰進行控制。
1. 當您的用戶端應用程式透過 `response_type=id_token` 要求 id_token 時，也會為上述建立的 Web API 要求存取權杖 (`response_type=token`)。 因此，當使用 v2.0 端點時，`scope` 參數看起來應該像 `api://GUID/SCOPE`。 在 v1.0 端點上，`resource` 參數應該是 Web API 的應用程式 URI。
1. 將此存取權杖傳遞至中介層以取代 id_token。  
