---
title: 微軟身份平臺的應用程式類型 |蔚藍
description: Microsoft 標識平臺 （v2.0） 終結點支援的應用和方案的類型。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 94cddf097f2a9e51f061909f6bdd3dcd82f18bfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262525"
---
# <a name="application-types-for-microsoft-identity-platform"></a>微軟標識平臺的應用程式類型

Microsoft 身分識別平台 (v2.0) 端點支援各種新式應用程式架構的驗證，它們全都以產業標準通訊協定 [OAuth 2.0 或 OpenID Connect](active-directory-v2-protocols.md) 為基礎。 本文介紹可以使用 Microsoft 標識平臺構建的應用類型，而不管您的首選語言或平臺如何。 這些資訊旨在説明您在[開始使用代碼](v2-overview.md#getting-started)之前瞭解高級方案。

> [!NOTE]
> Microsoft 標識平臺終結點不支援所有 Azure 活動目錄 （Azure AD） 方案和功能。 要確定是否應使用 Microsoft 標識平臺終結點，請閱讀有關[Microsoft 標識平臺限制](active-directory-v2-limitations.md)。

## <a name="the-basics"></a>基本概念

您必須在新的[應用註冊門戶](https://go.microsoft.com/fwlink/?linkid=2083908)中註冊使用 Microsoft 標識平臺終結點的每個應用。 應用程式註冊程序會為您的應用程式收集和指派下列值：

* 唯一標識應用**的應用程式（用戶端）ID**
* 可用來將回應導回到應用程式的「重新導向 URI」****
* 其他一些特定于方案的值，如支援的帳戶類型

如需詳細資訊，請了解如何[註冊應用程式](quickstart-register-app.md)。

註冊應用後，應用通過向終結點發送請求與 Microsoft 標識平臺通信。 我們提供開放原始碼架構，以及可處理這些要求詳細資料的程式庫。 您也可以選擇建立對這些端點的要求，來自行實作驗證邏輯：

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>單頁應用程式 (JavaScript)

許多新式應用程式都有一個單頁應用程式前端，主要是以 JavaScript 撰寫。 通常，它是通過使用像"角"、反應或 Vue 這樣的框架編寫的。 微軟標識平臺終結點使用[OAuth 2.0 隱式流](v2-oauth2-implicit-grant-flow.md)支援這些應用程式。

在此流中，應用直接從 Microsoft 標識平臺接收權杖，授權終結點，而無需任何伺服器到伺服器的交換。 所有驗證邏輯和工作階段處理都完全在 JavaScript 用戶端中進行，而不需要執行額外的頁面重新導向。

![顯示隱式身份驗證流](./media/v2-app-types/convergence-scenarios-implicit.svg)

要查看此方案的執行的操作，請嘗試[Microsoft 標識平臺入門](v2-overview.md#getting-started)部分中的單頁應用代碼示例之一。

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

[訪問權杖](access-tokens.md)引用和[id_token引用](id-tokens.md)中提供了 Microsoft 標識平臺終結點中使用的不同類型的權杖的詳細資訊

在 Web 伺服器應用程式中，登入驗證流程會採用下列概要步驟：

![顯示 Web 應用身份驗證流](./media/v2-app-types/convergence-scenarios-webapp.svg)

您可以通過使用從 Microsoft 標識平臺終結點接收的公共簽名金鑰驗證 ID 權杖來確保使用者的身份。 系統會設定工作階段 Cookie，這可在後續的頁面要求上用來識別使用者。

要查看此方案的執行的操作，請嘗試[Microsoft 標識平臺入門](v2-overview.md#getting-started)部分中的 Web 應用登錄代碼示例之一。

除了簡易登入之外，Web 伺服器應用程式可能也需要存取其他 Web 服務，例如 REST API。 在此情況下，Web 伺服器應用程式可以使用 [OAuth 2.0 授權碼流程](active-directory-v2-protocols.md)，參與結合了 OpenID Connect 與 OAuth 2.0 的流程。 如需有關此案例的詳細資訊，請參閱[開始使用 Web 應用程式和 Web API](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)。

## <a name="web-apis"></a>Web API

您可以使用 Microsoft 身分識別平台端點來保護 Web 服務，例如應用程式的 RESTful Web API。 Web API 可以在許多平臺和語言中實現。 它們也可以使用 Azure 函數中的 HTTP 觸發器實現。 Web API 使用 OAuth 2.0 存取權杖來保護其資料及驗證連入要求，而不是使用識別碼權杖和工作階段 Cookie。 Web API 的呼叫端會在 HTTP 要求的授權標頭中附加存取權杖，就像這樣：

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API 會使用這個存取權杖來驗證 API 呼叫端的身分識別，並從存取權杖中所編碼的宣告擷取呼叫端的相關資訊。 [訪問權杖](access-tokens.md)引用和[id_token](id-tokens.md)引用中提供了 Microsoft 標識平臺終結點中使用的不同類型的權杖的詳細資訊。

Web API 可透過公開權限的方式 (亦稱為[範圍](v2-permissions-and-consent.md))，讓使用者能夠選擇加入或退出特定的功能或資料。 為了讓發出呼叫的應用程式取得範圍的權限，使用者必須在流程中對範圍表示同意。 Microsoft 標識平臺終結點請求使用者許可權，然後在 Web API 收到的所有訪問權杖中記錄許可權。 Web API 會驗證它在每次呼叫所收到的存取權杖，並執行授權檢查。

Web API 可以從所有類型的應用程式接收存取權杖，包括 Web 伺服器應用程式、傳統型應用程式和行動應用程式、單頁應用程式、伺服器端精靈，甚至是其他的 Web API。 Web API 的概要流程看起來像這樣：

![顯示 Web API 身份驗證流](./media/v2-app-types/convergence-scenarios-webapi.svg)

要瞭解如何使用 OAuth2 訪問權杖來保護 Web API，請查看[Microsoft 標識平臺入門](v2-overview.md#getting-started)部分中的 Web API 代碼示例。

在許多情況下，Web API 還需要向 Microsoft 標識平臺保護的其他下游 Web API 發出出站請求。 為此，Web API 可以利用 **"代表**流"，它允許 Web API 為在出站請求中使用的另一個訪問權杖交換傳入訪問權杖。 有關詳細資訊，請參閱[Microsoft 標識平臺和 OAuth 2.0 代表流](v2-oauth2-on-behalf-of-flow.md)。

## <a name="mobile-and-native-apps"></a>行動和原生應用程式

裝置安裝的應用程式 (例如行動應用程式和傳統型應用程式) 通常需要存取儲存資料及代表使用者執行功能的後端服務或 Web API。 這些應用程式可以使用 [OAuth 2.0 授權碼流程](v2-oauth2-auth-code-flow.md)，將登入和授權新增至後端服務。

在此流中，當使用者登錄時，應用會從 Microsoft 標識平臺終結點接收授權代碼。 授權碼代表應用程式具備權限，可代表登入的使用者呼叫後端服務。 應用程式可以在背景中以授權碼交換 OAuth 2.0 存取權杖和重新整理權杖。 應用程式可以使用存取權杖在 HTTP 要求中向 Web API 進行驗證，以及在舊存取權杖到期時，使用重新整理權杖來取得新的存取權杖。

![顯示本機應用身份驗證流](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>精靈和伺服器端應用程式

應用程式如果含有長時間執行的程序，或其運作方式不需要與使用者互動，就也需要一個存取受保護資源 (例如 Web API) 的方法。 這些應用程式可以使用應用程式的身分識別 (而非使用者委派的身分識別) 搭配 OAuth 2.0 用戶端認證流程，來驗證及取得權杖。 您可以使用用戶端密碼或憑證來提供應用程式的身分識別。 有關詳細資訊，請參閱[.NET Core 守護進程主控台應用程式使用 Microsoft 標識平臺](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)。

在此流中，應用直接與`/token`終結點交互以獲取存取權限：

![顯示守護進程應用身份驗證流](./media/v2-app-types/convergence-scenarios-daemon.svg)

若要建置精靈應用程式，請參閱[用戶端認證文件](v2-oauth2-client-creds-grant-flow.md)，或試試 [.NET 範例應用程式](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)。
