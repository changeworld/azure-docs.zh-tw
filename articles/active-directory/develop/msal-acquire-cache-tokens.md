---
title: 使用 MSAL 取得 & 快取權杖 |Azure
titleSuffix: Microsoft identity platform
description: 了解如何使用 Microsoft 驗證程式庫 (MSAL) 取得和快取權杖。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 9a9dda1bba4d587881d32d937fa0e20b68a5b383
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696555"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>使用 Microsoft 驗證程式庫取得和快取權杖（MSAL）

[存取權杖](access-tokens.md)可讓用戶端安全地呼叫受 Azure 保護的 Web API。 使用 Microsoft 驗證程式庫 (MSAL) 取得權杖的方法很多。 有些方法需要使用者透過網頁瀏覽器進行互動。 有些方法則不需要使用者互動。 一般情況下，用來取得權杖的方法會取決於應用程式是公用用戶端應用程式 (桌面或行動應用程式) 還是機密用戶端應用程式 (Web 應用程式、Web API 或精靈應用程式，如 Windows 服務)。

MSAL 會在取得權杖之後建立其快取。  應用程式程式碼應該會先試著以無訊息方式從快取中取得權杖，然後才使用其他方法取得權杖。

您也可以清除權杖的快取，方法是從快取中移除帳戶。 但這不會移除瀏覽器中的工作階段 Cookie。

## <a name="scopes-when-acquiring-tokens"></a>取得權杖時的範圍

[範圍](v2-permissions-and-consent.md)是 Web API 所公開讓用戶端應用程式要求其存取權的權限。 用戶端應用程式在提出驗證要求以取得 Web API 的存取權杖時，會要求使用者同意這些範圍。 MSAL 可讓您取得權杖來存取開發人員 (v1.0) 和 Microsoft 身分識別平台 (v2.0) API 的 Azure AD。 v2.0 通訊協定會使用範圍而非要求中的資源。 如需詳細資訊，請參閱 [v1.0 和 v2.0 的比較](active-directory-v2-compare.md)。 根據其所接受的 Web API 權杖版本組態，v2.0 端點會對 MSAL 傳回存取權杖。

某些 MSAL 取得權杖方法需要「範圍」參數。 此參數是簡單的字串清單，其宣告所需的權限和要求的資源。 知名的範圍是 [Microsoft Graph 權限](/graph/permissions-reference)。

此外，也可以在 MSAL 中存取 v1.0 資源。 如需詳細資訊，請參閱 [v1.0 應用程式的範圍](msal-v1-app-scopes.md)。

### <a name="request-specific-scopes-for-a-web-api"></a>對 Web API 要求特定範圍

當應用程式必須對資源 API 要求具有特定權限的權杖時，您必須使用下列格式傳遞包含 API 應用程式識別碼 URI 的範圍：&lt;應用程式識別碼 URI&gt;/&lt;範圍&gt;

例如，Microsoft Graph API 的範圍：`https://graph.microsoft.com/User.Read`

又例如，自訂 Web API 的範圍：`api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

僅適用於 Microsoft Graph API，範圍值 `user.read` 會對應至 `https://graph.microsoft.com/User.Read` 格式，並可交換使用。

> [!NOTE]
> 某些 Web API，例如 Azure Resource Manager API (https://management.core.windows.net/) 預期存取權杖的對象宣告 (aud) 中要有尾端 '/'。 在此情況下，請務必以 https://management.core.windows.net//user_impersonation (請注意有雙斜線) 形式傳遞範圍，權杖在 API 中才會有效。

### <a name="request-dynamic-scopes-for-incremental-consent"></a>要求動態範圍以提供累加式同意

使用 v1.0 建置應用程式時，您必須註冊應用程式所需的一組完整權限 (靜態範圍)，讓使用者在登入時同意。 在 v2.0 中，您可以視需要使用範圍參數來要求其他權限。 這些權限稱為動態範圍，可讓使用者對範圍提供累加式同意。

例如，您可以先將使用者登入，然後拒絕其任何形式的存取。 之後，您可以藉由在取得權杖方法中要求行事曆範圍讓使用者能夠讀取其行事曆，並取得使用者的同意。

例如：`https://graph.microsoft.com/User.Read` 和 `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>以無訊息方式從快取中取得權杖

MSAL 會保有一個權杖快取 (若為機密用戶端應用程式，則有兩個快取)，並在取得權杖之後建立其快取。  在許多情況下，嘗試以無訊息方式取得權杖將會取得另一個具有多個範圍的權杖 (根據快取中的權杖而定)。 快取也可以在即將到期時重新整理權杖 (因為權杖快取也會包含重新整理權杖)。

### <a name="recommended-call-pattern-for-public-client-applications"></a>公用用戶端應用程式的建議呼叫模式

應用程式程式碼應該會先試著以無訊息方式從快取中取得權杖。  如果方法呼叫傳回「需要 UI」錯誤或例外狀況，則會嘗試透過其他方式取得權杖。 

不過，**請勿**在下列兩個流程之前嘗試以無訊息方式取得權杖：

- [用戶端認證流程](msal-authentication-flows.md#client-credentials)，這不會使用使用者的權杖快取，而會使用應用程式的權杖快取。 這個方法會先負責確認此應用程式的權杖快取，再對 STS 傳送要求。
- Web Apps 中的[授權碼流程](msal-authentication-flows.md#authorization-code)，因為此流程會兌換應用程式藉由將使用者登入、再讓其同意更多範圍而獲得的授權碼。 因為授權碼會以參數 (而非帳戶) 形式傳遞，該方法無法在兌換授權碼之前先查看快取，因為這麼做無論如何都需要對服務發出呼叫。

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>使用授權碼流程的 Web Apps 中建議的呼叫模式

對於使用 [OpenID Connect 授權碼流程](v2-protocols-oidc.md)的 Web 應用程式，控制器中的建議模式是：

- 使用自訂序列化以權杖快取具現化機密用戶端應用程式。 
- 使用授權碼流程取得權杖

## <a name="acquiring-tokens"></a>取得權杖

一般而言，取得權杖的方法取決於其為公用用戶端應用程式還是機密用戶端應用程式。

### <a name="public-client-applications"></a>公用用戶端應用程式

若為公用用戶端應用程式 (桌面或行動應用程式)，則您：
- 通常會以互動方式取得權杖，讓使用者透過 UI 或快顯視窗登入。
- 可以使用整合式 Windows 驗證 (IWA/Kerberos) 來[為已登入的使用者以無訊息方式取得權杖](msal-authentication-flows.md#integrated-windows-authentication) (如果桌面應用程式是在加入網域或 Azure 的 Windows 電腦上執行)。
- 可以在 .NET Framework 桌面用戶端應用程式中[以使用者名稱和密碼取得權杖](msal-authentication-flows.md#usernamepassword)，但不建議這麼做。 請勿在機密用戶端應用程式中使用使用者名稱/密碼。
- 可以在執行於裝置上且沒有網頁瀏覽器的應用程式中，透過[裝置代碼流程](msal-authentication-flows.md#device-code)取得權杖。 使用者會獲得 URL 和代碼，接著便能前往其他裝置上的網頁瀏覽器，然後輸入代碼並登入。  Azure AD 接著會將權杖傳回給無瀏覽器的裝置。

### <a name="confidential-client-applications"></a>機密用戶端應用程式

若為機密用戶端應用程式 (Web 應用程式、Web API 或精靈應用程式，如 Windows 服務)，則您：
- 可以使用[用戶端認證流程](msal-authentication-flows.md#client-credentials)取得**應用程式本身**而非使用者的權杖。 這可以用於同步工具，或用於會處理整體使用者而非特定使用者的工具。 
- 可以使用[代理者流程](msal-authentication-flows.md#on-behalf-of)讓 Web API 代表使用者呼叫 API。 應用程式會使用用戶端認證來識別，以根據使用者判斷提示來取得權杖 (例如 SAML，或 JWT 權杖)。 需要在服務對服務的呼叫中存取特定使用者資源的應用程式會使用此流程。
- 可以在使用者透過授權要求 URL 來登入後，於 Web 應用程式中使用[授權碼流程](msal-authentication-flows.md#authorization-code)來取得權杖。 OpenID Connect 應用程式一般會使用這個機制，這可以讓使用者使用 Open ID Connect 登入，然後代表使用者存取 Web API。

## <a name="authentication-results"></a>驗證結果

當用戶端要求存取權杖時，Azure AD 也會傳回驗證結果，其中會有一些關於存取權杖的中繼資料。 此資訊包括存取權杖的到期時間以及其有效範圍。 此資料可讓應用程式執行存取權杖的智慧型快取，而不需剖析存取權杖本身。  驗證結果會公開：

- Web API 用來存取資源的[存取權杖](access-tokens.md)。 這是一個字串，通常是以 base64 編碼的 JWT，但用戶端永遠不該查看存取權杖內部。 此格式不保證會維持穩定，並可針對資源將其加密。 根據用戶端上的存取權杖內容來撰寫程式碼的人員，是發生錯誤和用戶端邏輯破碎的最大來源之一。
- 使用者的[識別碼權杖](id-tokens.md) (這是 JWT)。
- 權杖到期日，會指出權杖的到期日期/時間。
- 租用戶識別碼包含找到使用者時其所在的租用戶。 針對來賓使用者 (Azure AD B2B 案例)，租用戶識別碼是來賓租用戶，而非唯一租用戶。 當權杖透過使用者名稱傳遞時，驗證結果也會包含此使用者的相關資訊。 對於不會向使用者要求權杖的機密用戶端流程 (針對應用程式)，此使用者資訊是 Null。
- 簽發權杖時所適用的範圍。
- 使用者的唯一識別碼。

## <a name="next-steps"></a>後續步驟

如果您使用 MSAL for JAVA，請瞭解[MSAL For java 中的自訂權杖](msal-java-token-cache-serialization.md)快取序列化。

了解如何[處理錯誤和例外狀況](msal-handling-exceptions.md)。
