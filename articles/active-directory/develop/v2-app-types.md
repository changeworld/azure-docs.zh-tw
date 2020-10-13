---
title: Microsoft 身分識別平台的應用程式類型 | Azure
description: Microsoft 身分識別平台 (v2.0) 端點支援的應用程式類型與案例。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, fasttrack-edit
ms.openlocfilehash: c468ecb390a3ad321f9fe0619204994dfbf3fbb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91256751"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Microsoft 身分識別平台的應用程式類型

Microsoft 身分識別平台 (v2.0) 端點支援各種新式應用程式架構的驗證，它們全都以產業標準通訊協定 [OAuth 2.0 或 OpenID Connect](active-directory-v2-protocols.md) 為基礎。 本文描述您可以使用 Microsoft 身分識別平台建置的應用程式類型，不論您慣用的語言或平台是哪一種。 這些資訊是設計來協助您在[開始使用程式碼](v2-overview.md#getting-started)之前先瞭解概要的案例。

## <a name="the-basics"></a>基本概念

您必須在 Azure 入口網站[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)中註冊每個使用 Microsoft 身分識別平台端點的應用程式。 應用程式註冊程序會為您的應用程式收集和指派下列值：

* 可唯一識別應用程式的**應用程式 (用戶端) 識別碼**
* 可用來將回應導回到應用程式的「重新導向 URI」
* 幾個其他案例特定的值，例如支援的帳戶類型

如需詳細資訊，請了解如何[註冊應用程式](quickstart-register-app.md)。

註冊應用程式之後，應用程式即會向端點傳送要求以與 Microsoft 身分識別平台通訊。 我們提供開放原始碼架構，以及可處理這些要求詳細資料的程式庫。 您也可以選擇建立對這些端點的要求，來自行實作驗證邏輯：

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>單頁應用程式 (JavaScript)

許多新式的應用程式都有單頁應用程式前端，主要是以 JavaScript 撰寫，而且通常有 Angular、React 或 Vue 之類的架構。 Microsoft 身分識別平臺端點支援使用 [OpenID Connect](v2-protocols-oidc.md) 通訊協定進行驗證，以及 [oauth 2.0 隱含授與流程](v2-oauth2-implicit-grant-flow.md) 或較新的 [OAUTH 2.0 授權碼 + PKCE 流程](v2-oauth2-auth-code-flow.md) 來進行授權 (請參閱以下) 。

下流程圖示範 OAuth 2.0 授權碼授與 (，其中包含 PKCE 省略) 的詳細資料，其中應用程式會從 Microsoft 身分識別平臺端點接收程式碼 `authorize` ，並使用跨網站 web 要求贖回權杖和重新整理權杖。 重新整理權杖會每 24小時到期，而且應用程式必須要求另一個代碼。 除了存取權杖之外， `id_token` 表示用戶端應用程式登入使用者的通常也會透過相同的流程和/或個別的 OpenID Connect 要求（ (此處未顯示) ）要求。

![SPA 應用程式的程式碼流程](media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.png)

若要查看此案例的執行狀況，請參閱[教學課程：使用授權碼流程來登入使用者，並從 JavaScript SPA 呼叫 Microsoft Graph API](tutorial-v2-javascript-auth-code.md)。

### <a name="authorization-code-flow-vs-implicit-flow"></a>授權碼流程與隱含流程的比較

以大部分的 OAuth 2.0 歷程記錄來說，[隱含流程](v2-oauth2-implicit-grant-flow.md)原本是建立單頁應用程式的建議方式。 由於移除了[協力廠商 Cookie](reference-third-party-cookies-spas.md) 且[更加注重](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)隱含流程中的安全性疑慮慮，單頁應用程式已改用授權碼流程。

為了確保您在 Safari 和其他隱私權瀏覽器中的應用程式相容性，我們不再建議使用隱含流程，而改為建議授權碼流程。

## <a name="web-apps"></a>Web 應用程式

針對使用者透過瀏覽器存取的 Web 應用程式 (.NET、PHP、Java、Ruby、Python、Node)，您可以使用 [OpenID Connect](active-directory-v2-protocols.md) 來執行使用者登入。 在 OpenID Connect 中，Web 應用程式會收到識別碼權杖。 識別碼權杖是一個安全性權杖，可驗證使用者的身分識別並以宣告形式提供使用者的相關資訊：

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

如需 Microsoft 身分識別平台端點中所用不同權杖類型的詳細說明，請參閱[存取權杖](access-tokens.md)參考和 [id_token 參考](id-tokens.md)

在 Web 伺服器應用程式中，登入驗證流程會採用下列概要步驟：

![顯示 Web 應用程式驗證流程](./media/v2-app-types/convergence-scenarios-webapp.svg)

您可以使用接收自 Microsoft 身分識別平台端點的公開簽署金鑰來驗證識別碼權杖，來確保使用者的身分識別。 系統會設定工作階段 Cookie，這可在後續的頁面要求上用來識別使用者。

若要查看此案例的實際運作情形，請在 [Microsoft 身分識別平台開始使用](v2-overview.md#getting-started)一節的 Web 應用程式登入程式碼範例中擇一試用。

除了簡易登入之外，Web 伺服器應用程式可能也需要存取其他 Web 服務，例如 REST API。 在此情況下，Web 伺服器應用程式可以使用 [OAuth 2.0 授權碼流程](v2-oauth2-auth-code-flow.md)，參與結合了 OpenID Connect 與 OAuth 2.0 的流程。 如需有關此案例的詳細資訊，請參閱[開始使用 Web 應用程式和 Web API](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIDConnect-DotNet)。


## <a name="web-apis"></a>Web API

您可以使用 Microsoft 身分識別平台端點來保護 Web 服務，例如應用程式的 RESTful Web API。 Web API 能夠在多個平台以及使用多種語言實作。 也可以使用 Azure Functions 中的 HTTP 觸發程序來實作。 Web API 使用 OAuth 2.0 存取權杖來保護其資料及驗證連入要求，而不是使用識別碼權杖和工作階段 Cookie。 Web API 的呼叫端會在 HTTP 要求的授權標題中附加存取權杖，就像這樣：

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API 會使用這個存取權杖來驗證 API 呼叫端的身分識別，並從存取權杖中所編碼的宣告擷取呼叫端的相關資訊。 如需 Microsoft 身分識別平台端點中所用不同權杖類型的詳細說明，請參閱[存取權杖](access-tokens.md)參考和 [id_token 參考](id-tokens.md)。

Web API 可透過公開權限的方式 (亦稱為[範圍](v2-permissions-and-consent.md))，讓使用者能夠選擇加入或退出特定的功能或資料。 為了讓發出呼叫的應用程式取得範圍的權限，使用者必須在流程中對範圍表示同意。 Microsoft 身分識別平台端點會向使用者要求權限，然後將這些權限記錄在 Web API 接收的所有存取權杖中。 Web API 會驗證它在每次呼叫所收到的存取權杖，並執行授權檢查。

Web API 可以從所有類型的應用程式接收存取權杖，包括 Web 伺服器應用程式、傳統型應用程式和行動應用程式、單頁應用程式、伺服器端精靈，甚至是其他的 Web API。 Web API 的概要流程看起來像這樣：

![顯示 Web API 驗證流程](./media/v2-app-types/convergence-scenarios-webapi.svg)

若要瞭解如何使用 OAuth2 存取權杖來保護 Web API，請查看 [Microsoft 身分識別平台開始使用](v2-overview.md#getting-started)一節中的 Web API 程式碼範例。

在許多情況下，Web API 也需要對受 Microsoft 身分識別平台保護的其他下游 Web API 發出傳出要求。 若要這樣做，Web API 可以利用「代理者」流程，它能允許 Web API 將傳入存取權杖交換為要在傳出要求中使用的另一個存取權杖。 如需詳細資訊，請參閱 [Microsoft 身分識別平台和 OAuth 2.0 代理者流程](v2-oauth2-on-behalf-of-flow.md)。

## <a name="mobile-and-native-apps"></a>行動和原生應用程式

裝置安裝的應用程式 (例如行動應用程式和傳統型應用程式) 通常需要存取儲存資料及代表使用者執行功能的後端服務或 Web API。 這些應用程式可以使用 [OAuth 2.0 授權碼流程](v2-oauth2-auth-code-flow.md)，將登入和授權新增至後端服務。

在此流程中，應用程式會在使用者登入時，從 Microsoft 身分識別平台端點接收授權碼。 授權碼代表應用程式具備權限，可代表登入的使用者呼叫後端服務。 應用程式可以在背景中以授權碼交換 OAuth 2.0 存取權杖和重新整理權杖。 應用程式可以使用存取權杖在 HTTP 要求中向 Web API 進行驗證，以及在舊存取權杖到期時，使用重新整理權杖來取得新的存取權杖。

![顯示原生應用程式驗證流程](./media/v2-app-types/convergence-scenarios-native.svg)

> [!NOTE]
> 如果應用程式使用預設的系統網頁程式碼，請參閱 [Azure AD authentication 和授權錯誤碼](reference-aadsts-error-codes.md)中的「確認我的登入」功能和錯誤碼 AADSTS50199 的相關資訊。

## <a name="daemons-and-server-side-apps"></a>精靈和伺服器端應用程式

應用程式如果含有長時間執行的程序，或其運作方式不需要與使用者互動，就也需要一個存取受保護資源 (例如 Web API) 的方法。 這些應用程式可以使用應用程式的身分識別 (而非使用者委派的身分識別) 搭配 OAuth 2.0 用戶端認證流程，來驗證及取得權杖。 您可以使用用戶端密碼或憑證來提供應用程式的身分識別。 如需詳細資訊，請參閱[使用 Microsoft 身分識別平台的 .NET Core 精靈主控台應用程式](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)。

在此流程中，應用程式會直接與 `/token` 端點互動來取得存取權：

![顯示精靈應用程式驗證流程](./media/v2-app-types/convergence-scenarios-daemon.svg)

若要建置精靈應用程式，請參閱[用戶端認證文件](v2-oauth2-client-creds-grant-flow.md)，或試試 [.NET 範例應用程式](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)。

## <a name="next-steps"></a>後續步驟

現在您已熟悉 Microsoft 身分識別平台所支援的應用程式類型，請深入瞭解 [OAuth 2.0 和 OpenID Connect](active-directory-v2-protocols.md)，以瞭解不同案例所使用的通訊協定元件。
