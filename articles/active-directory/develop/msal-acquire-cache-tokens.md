---
title: 使用 Microsoft 驗證程式庫取得 & 快取權杖 (MSAL) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用 MSAL 取得和快取權杖。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: da79a74121318993f807ec3bde101b652a8b49da
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628130"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>使用 Microsoft 驗證程式庫 (MSAL) 取得和快取權杖

[存取權杖](access-tokens.md)可讓用戶端安全地呼叫受 Azure 保護的 Web API。 使用 Microsoft 驗證程式庫 (MSAL) 取得權杖的方式有好幾種。 有些則需要使用者透過網頁瀏覽器進行互動，而有些則不需要使用者互動。 一般情況下，用來取得權杖的方法取決於應用程式是公用用戶端應用程式（例如桌面或行動裝置應用程式），還是機密用戶端應用程式（如 web 應用程式、web API 或 daemon 應用程式）。

MSAL 會在取得權杖之後加以快取。 您的應用程式程式碼應該先嘗試從快取以無訊息方式取得權杖，然後再嘗試以其他方式取得權杖。

您也可以清除權杖的快取，方法是從快取中移除帳戶。 不過，這不會移除瀏覽器中的會話 cookie。

## <a name="scopes-when-acquiring-tokens"></a>取得權杖時的範圍

[範圍](v2-permissions-and-consent.md) 是 web API 公開的許可權，可讓用戶端應用程式要求存取。 用戶端應用程式在提出驗證要求以取得 Web API 的存取權杖時，會要求使用者同意這些範圍。 MSAL 可讓您取得權杖來存取開發人員 (v1.0) 和 Microsoft 身分識別平台 (v2.0) API 的 Azure AD。 v2.0 通訊協定會使用範圍而非要求中的資源。 如需詳細資訊，請參閱 [v1.0 和 v2.0 的比較](../azuread-dev/azure-ad-endpoint-comparison.md)。 根據其所接受的 Web API 權杖版本組態，v2.0 端點會對 MSAL 傳回存取權杖。

有幾個 MSAL 的權杖取得方法需要 `scopes` 參數。 `scopes`參數是宣告所需許可權和所要求資源的字串清單。 已知的範圍是 [Microsoft Graph 許可權](/graph/permissions-reference)。

此外，也可以在 MSAL 中存取 v1.0 資源。 如需詳細資訊，請參閱 v1.0 [應用程式的範圍](msal-v1-app-scopes.md)。

### <a name="request-scopes-for-a-web-api"></a>Web API 的要求範圍

當您的應用程式需要使用資源 API 的特定許可權來要求存取權杖時，請以格式傳遞包含 API 之應用程式識別碼 URI 的範圍 `<app ID URI>/<scope>` 。

不同資源的一些範例範圍值：

- Microsoft Graph API： `https://graph.microsoft.com/User.Read`
- 自訂 web API： `api://11111111-1111-1111-1111-111111111111/api.read`

範圍值的格式會根據 (API) 接收存取權杖及其接受的宣告值的資源而有所不同 `aud` 。

僅限 Microsoft Graph， `user.read` 範圍會對應至 `https://graph.microsoft.com/User.Read` ，而且這兩個範圍格式可交替使用。

某些 web Api （例如 Azure Resource Manager API (https://management.core.windows.net/) 預期在存取權杖 () 中，物件宣告的尾端正斜線 ( '/' ) `aud` 。 在此情況下，請將範圍傳遞為 `https://management.core.windows.net//user_impersonation` ，包括兩個正斜線 ( '//' ) 。

其他 Api 可能會要求範圍值中 *不包含任何配置或主機* ，而且只預期 (GUID) 和範圍名稱的應用程式識別碼，例如：

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> 如果下游資源不在您的控制之下，您可能需要嘗試不同的範圍值格式 (例如，搭配/沒有配置和主機) 如果您在 `401` 傳遞存取權杖至資源時收到或其他錯誤。

### <a name="request-dynamic-scopes-for-incremental-consent"></a>要求動態範圍以提供累加式同意

當您的應用程式所提供的功能或其需求變更時，您可以視需要使用 scope 參數來要求其他許可權。 這類 *動態範圍* 可讓您的使用者對範圍提供累加式同意。

例如，您可能會登入使用者，但一開始會拒絕他們存取任何資源。 稍後，您可以在取得權杖方法中要求行事曆範圍，並取得使用者的同意，讓他們能夠查看其行事曆。 例如，藉由要求 `https://graph.microsoft.com/User.Read` 和 `https://graph.microsoft.com/Calendar.Read` 範圍。

## <a name="acquiring-tokens-silently-from-the-cache"></a>以無訊息方式從快取中取得權杖

MSAL 會維護權杖快取 (或) 的機密用戶端應用程式的兩個快取，並在取得權杖之後快取權杖。 在許多情況下，嘗試以無訊息方式取得權杖將會取得另一個具有多個範圍的權杖 (根據快取中的權杖而定)。 快取也可以在即將到期時重新整理權杖 (因為權杖快取也會包含重新整理權杖)。

### <a name="recommended-call-pattern-for-public-client-applications"></a>公用用戶端應用程式的建議呼叫模式

應用程式程式碼應該會先嘗試從快取以無訊息方式取得權杖。 如果方法呼叫傳回「需要 UI」錯誤或例外狀況，則會嘗試透過其他方式取得權杖。

不過，有兩個流程，您 **不應該** 嘗試以無訊息方式取得權杖：

- [用戶端認證流程](msal-authentication-flows.md#client-credentials)，不使用使用者權杖快取，而是應用程式權杖快取。 在將要求傳送至安全性權杖服務之前，這個方法會先負責驗證應用程式權杖快取 (STS) 。
- Web 應用程式中的[授權碼流程](msal-authentication-flows.md#authorization-code)，因為它會贖回應用程式透過登入使用者所取得的程式碼，並讓他們同意更多範圍。 由於程式碼不會以參數形式傳遞，所以在兌換程式碼之前，方法無法在快取中尋找，這會叫用服務的呼叫。

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>使用授權碼流程的 web 應用程式中建議的呼叫模式

對於使用 [OpenID Connect 授權碼流程](v2-protocols-oidc.md)的 Web 應用程式，控制器中的建議模式是：

- 使用自訂序列化以權杖快取具現化機密用戶端應用程式。
- 使用授權碼流程取得權杖

## <a name="acquiring-tokens"></a>取得權杖

一般而言，取得權杖的方法取決於其為公用用戶端應用程式還是機密用戶端應用程式。

### <a name="public-client-applications"></a>公用用戶端應用程式

若為公用用戶端應用程式 (桌面或行動應用程式)，則您：

- 通常會以互動方式取得權杖，讓使用者透過 UI 或快顯視窗登入。
- 可以使用整合式 Windows 驗證 (IWA/Kerberos) 來[為已登入的使用者以無訊息方式取得權杖](msal-authentication-flows.md#integrated-windows-authentication) (如果桌面應用程式是在加入網域或 Azure 的 Windows 電腦上執行)。
- 可以在 .NET framework desktop 用戶端應用程式中 [取得具有使用者名稱和密碼的權杖](msal-authentication-flows.md#usernamepassword) (不建議) 。 請勿在機密用戶端應用程式中使用使用者名稱/密碼。
- 可以透過 [裝置程式碼流程](msal-authentication-flows.md#device-code) ，在沒有網頁瀏覽器的裝置上執行的應用程式中取得權杖。 使用者會獲得 URL 和代碼，接著便能前往其他裝置上的網頁瀏覽器，然後輸入代碼並登入。 Azure AD 接著會將權杖傳回給無瀏覽器的裝置。

### <a name="confidential-client-applications"></a>機密用戶端應用程式

針對機密用戶端應用程式 (web 應用程式、web API 或像是 Windows 服務) 的背景程式應用程式，您可以：

- 可以使用 [用戶端認證流程](msal-authentication-flows.md#client-credentials)取得 **應用程式本身** 而非使用者的權杖。 這項技術可以用來同步處理工具，或是用來處理一般使用者而非特定使用者的工具。
- 使用 web API 的代理者 [流程](msal-authentication-flows.md#on-behalf-of) 來代表使用者呼叫 API。 應用程式會使用用戶端認證來識別，以根據使用者判斷提示 (SAML 來取得權杖，例如，或) 的 JWT 權杖。 需要在服務對服務的呼叫中存取特定使用者資源的應用程式會使用此流程。
- 可以在使用者透過授權要求 URL 來登入後，於 Web 應用程式中使用[授權碼流程](msal-authentication-flows.md#authorization-code)來取得權杖。 OpenID Connect 應用程式一般會使用這個機制，這可以讓使用者使用 Open ID Connect 登入，然後代表使用者存取 Web API。

## <a name="authentication-results"></a>驗證結果

當您的用戶端要求存取權杖時，Azure AD 也會傳回驗證結果，其中包含存取權杖的相關中繼資料。 此資訊包括存取權杖的到期時間以及其有效範圍。 此資料可讓應用程式執行存取權杖的智慧型快取，而不需剖析存取權杖本身。 驗證結果會公開：

- Web API 用來存取資源的[存取權杖](access-tokens.md)。 這個字串通常是 Base64 編碼的 JWT，但用戶端永遠不會在存取權杖內查看。 格式不保證會維持穩定狀態，而且可以針對資源進行加密。 根據用戶端上的存取權杖內容撰寫程式碼的人員，是最常見的錯誤來源和用戶端邏輯破壞的其中一種。
- 使用者 (JWT) 的 [識別碼權杖](id-tokens.md) 。
- 權杖到期日，會指出權杖的到期日期/時間。
- 租用戶識別碼包含找到使用者時其所在的租用戶。 針對來賓使用者 (Azure AD B2B 案例)，租用戶識別碼是來賓租用戶，而非唯一租用戶。 當權杖透過使用者名稱傳遞時，驗證結果也會包含此使用者的相關資訊。 對於不會向使用者要求權杖的機密用戶端流程 (針對應用程式)，此使用者資訊是 Null。
- 簽發權杖時所適用的範圍。
- 使用者的唯一識別碼。

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a> (Advanced) 在背景應用程式和服務中存取使用者的快取權杖

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>後續步驟

如果您是使用 MSAL for JAVA，請瞭解 [MSAL For java 中的自訂權杖](msal-java-token-cache-serialization.md)快取序列化。

了解如何[處理錯誤和例外狀況](msal-handling-exceptions.md)。