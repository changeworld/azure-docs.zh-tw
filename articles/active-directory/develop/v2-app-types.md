---
title: 微軟身份平台的應用程式類型 |蔚藍
description: Microsoft 標識平臺 (v2.0) 終結點支援的應用和方案的類型。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: bdbda8bed38819ca2b4d2fb1ef3d9bf591269890
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535905"
---
# <a name="application-types-for-microsoft-identity-platform"></a>微軟識別平台的應用程式類型

Microsoft 身分識別平台 (v2.0) 端點支援各種新式應用程式架構的驗證，它們全都以產業標準通訊協定 [OAuth 2.0 或 OpenID Connect](active-directory-v2-protocols.md) 為基礎。 本文介紹可以使用 Microsoft 標識平台構建的應用類型,而不管您的首選語言或平臺如何。 這些資訊旨在説明您在[開始使用代碼](v2-overview.md#getting-started)之前瞭解進階方案。

> [!NOTE]
> Microsoft 識別平台終結點不支援所有 Azure 活動目錄 (Azure AD) 方案和功能。 要確定是否應使用 Microsoft 識別平台終結點,請閱讀有關[Microsoft 識別平臺限制](active-directory-v2-limitations.md)。

## <a name="the-basics"></a>基本概念

您必須在新的[應用註冊門戶](https://go.microsoft.com/fwlink/?linkid=2083908)中註冊使用 Microsoft 標識平台終結點的每個應用。 應用程式註冊程序會為您的應用程式收集和指派下列值：

* 唯一識別應用程式 **(用戶端)ID**
* 可用來將回應導回到應用程式的「重新導向 URI」****
* 其他一些特定於方案的值,如支援的帳戶類型

如需詳細資訊，請了解如何[註冊應用程式](quickstart-register-app.md)。

註冊應用后,應用通過向終結點發送請求與 Microsoft 標識平臺通信。 我們提供開放原始碼架構，以及可處理這些要求詳細資料的程式庫。 您也可以選擇建立對這些端點的要求，來自行實作驗證邏輯：

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>單頁應用程式 (JavaScript)

許多新式應用程式都有一個單頁應用程式前端，主要是以 JavaScript 撰寫。 通常,它是通過使用像"角"、反應或 Vue 這樣的框架編寫的。 微軟識別平台終結點使用[OAuth 2.0 隱式流](v2-oauth2-implicit-grant-flow.md)支援這些應用程式。

在此流中,應用直接從 Microsoft 標識平臺接收令牌,授權終結點,而無需任何伺服器到伺服器的交換。 所有驗證邏輯和工作階段處理都完全在 JavaScript 用戶端中進行，而不需要執行額外的頁面重新導向。

![顯示隱含式驗證串流](./media/v2-app-types/convergence-scenarios-implicit.svg)

要查看此方案的執行的操作,請嘗試[Microsoft 標識平台入門](v2-overview.md#getting-started)部分中的單頁應用代碼範例之一。

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

[存取權杖](access-tokens.md)參考與[id_token參考](id-tokens.md)中提供 Microsoft 識別平台終結點中使用的不同類型的權杖的詳細資訊

在 Web 伺服器應用程式中，登入驗證流程會採用下列概要步驟：

![顯示 Web 應用程式識別驗證串流](./media/v2-app-types/convergence-scenarios-webapp.svg)

您可以通過使用從 Microsoft 識別平台終結點接收的公共簽名密鑰驗證 ID 令牌來確保使用者的身份。 系統會設定工作階段 Cookie，這可在後續的頁面要求上用來識別使用者。

要查看此方案的執行的操作,請嘗試[Microsoft 標識平台入門](v2-overview.md#getting-started)部分中的 Web 應用登錄代碼範例之一。

除了簡易登入之外，Web 伺服器應用程式可能也需要存取其他 Web 服務，例如 REST API。 在此情況下，Web 伺服器應用程式可以使用 [OAuth 2.0 授權碼流程](active-directory-v2-protocols.md)，參與結合了 OpenID Connect 與 OAuth 2.0 的流程。 有關此方案的詳細資訊,請閱讀有關[開始使用 Web 應用與 Web API 的資訊](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)。

## <a name="web-apis"></a>Web API

您可以使用 Microsoft 識別平台終結點來保護 Web 服務,例如應用的 RESTful Web API。 Web API 可以在許多平台和語言中實現。 它們也可以使用 Azure 函數中的 HTTP 觸發器實現。 Web API 使用 OAuth 2.0 訪問權杖來保護其資料並驗證傳入請求,而不是 ID 權杖和會話 Cookie。 Web API 的呼叫者在 HTTP 要求的授權標頭中追加存取權杖,如下所示:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API 使用存取權杖來驗證 API 呼叫方的識別,並從存取權杖中編碼的聲明中提取有關調用方的資訊。 [訪問權杖](access-tokens.md)引用和[id_token](id-tokens.md)引用中提供了 Microsoft 識別平台終結點中使用的不同類型的權杖的詳細資訊。

Web API 可以允許使用者通過公開許可權(也稱為[作用域](v2-permissions-and-consent.md))來選擇加入或選擇退出特定功能或數據。 為了讓發出呼叫的應用程式取得範圍的權限，使用者必須在流程中對範圍表示同意。 Microsoft 識別平台終結點請求使用者許可權,然後在 Web API 收到的所有訪問令牌中記錄許可權。 Web API 驗證它在每個調用上接收的訪問權杖,並執行授權檢查。

Web API 可以從所有類型的應用(包括 Web 伺服器應用、桌面和行動應用、單頁應用、伺服器端守護行程,甚至其他 Web API)接收存取權杖。 Web API 的高級流如下所示:

![顯示 Web API 認證串流](./media/v2-app-types/convergence-scenarios-webapi.svg)

要瞭解如何使用 OAuth2 訪問權杖來保護 Web API,請查看[Microsoft 標識平台入門](v2-overview.md#getting-started)部分中的 Web API 代碼範例。

在許多情況下,Web API 還需要向 Microsoft 標識平台保護的其他下游 Web API 發出出站請求。 為此,Web API 可以利用 **「代表**流」,它允許 Web API 為在出站請求中使用的另一個訪問權杖交換傳入存取權杖。 有關詳細資訊,請參閱[Microsoft 標識平臺和 OAuth 2.0 代表流](v2-oauth2-on-behalf-of-flow.md)。

## <a name="mobile-and-native-apps"></a>行動和原生應用程式

設備安裝的應用(如移動和桌面應用)通常需要存取後端服務或 Web API,這些服務或 Web API 代表使用者儲存數據和執行功能。 這些應用程式可以使用 [OAuth 2.0 授權碼流程](v2-oauth2-auth-code-flow.md)，將登入和授權新增至後端服務。

在此流中,當用戶登錄時,應用會從 Microsoft 標識平台終結點接收授權代碼。 授權碼代表應用程式具備權限，可代表登入的使用者呼叫後端服務。 應用程式可以在背景中以授權碼交換 OAuth 2.0 存取權杖和重新整理權杖。 應用可以使用存取權杖在 HTTP 請求中對 Web API 進行身份驗證,並在較舊的訪問權杖過期時使用刷新權杖獲取新的存取權杖。

![顯示本機應用認證串流](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>精靈和伺服器端應用程式

具有長時間運行的進程或無需與使用者交互即可運行的應用也需要存取安全資源(如 Web API)的方法。 這些應用程式可以使用應用程式的身分識別 (而非使用者委派的身分識別) 搭配 OAuth 2.0 用戶端認證流程，來驗證及取得權杖。 您可以使用用戶端密碼或憑證來提供應用程式的身分識別。 有關詳細資訊,請參閱[.NET Core 守護行程主控台應用程式使用 Microsoft 識別平臺](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)。

在此串流中,應用直接`/token`與終結點互動以取得存取權限:

![顯示守護程式應用程式驗證串流](./media/v2-app-types/convergence-scenarios-daemon.svg)

若要建置精靈應用程式，請參閱[用戶端認證文件](v2-oauth2-client-creds-grant-flow.md)，或試試 [.NET 範例應用程式](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)。
