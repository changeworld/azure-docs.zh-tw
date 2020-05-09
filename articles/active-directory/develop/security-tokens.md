---
title: 安全性權杖 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解 Microsoft 身分識別平臺（v2.0）中安全性權杖的基本概念。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 1161575104efe8cfc797f84c109a12116f723cad
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926573"
---
# <a name="security-tokens"></a>安全性權杖

集中式身分識別提供者特別適用于使用者位於全球且不一定從商業網路登入的應用程式。 Microsoft 身分識別平臺會驗證使用者，並提供安全性權杖（例如[存取權杖](developer-glossary.md#access-token)、重新整理[權杖](developer-glossary.md#refresh-token)和[識別碼權杖](developer-glossary.md#id-token)），讓[用戶端應用程式](developer-glossary.md#client-application)能夠存取[資源伺服器](developer-glossary.md#resource-server)上受保護的資源。

**存取權杖**是[授權伺服器](developer-glossary.md#authorization-server)在[OAuth 2.0](active-directory-v2-protocols.md)流程中所發出的安全性權杖。 其中包含使用者和應用程式的相關資訊，其適用于權杖;可用來存取 web Api 和其他受保護的資源。 若要深入瞭解 Microsoft 身分識別平臺如何發行存取權杖，請參閱[存取權杖](access-tokens.md)。

存取權杖只會在短時間內有效，因此授權伺服器有時會在發出存取權杖時發出重新整理**權杖**。 然後，用戶端應用程式可以在需要時，為新的存取權杖交換此重新整理權杖。 若要深入瞭解 Microsoft 身分識別平臺如何使用重新整理權杖來撤銷許可權，請參閱[權杖撤銷](access-tokens.md#token-revocation)。

**識別碼權杖**會當做[OpenID connect](v2-protocols-oidc.md)流程的一部分傳送給用戶端應用程式。 這些權杖可以一併傳送或代替存取權杖，並由用戶端用來驗證使用者。 若要深入瞭解 Microsoft 身分識別平臺如何發出識別碼權杖，請參閱[識別碼權杖](id-tokens.md)。

> [!NOTE]
> 本文討論 OAuth2 和 OpenID Connect 通訊協定的安全性權杖。 許多企業應用程式都使用 SAML 來驗證使用者。 如需 SAML 判斷提示的詳細資訊，請參閱[AZURE AD saml 權杖參考](reference-saml-tokens.md)。

## <a name="validating-security-tokens"></a>驗證安全性權杖

它是由已產生權杖的應用程式、登入使用者的 web 應用程式，或呼叫的 Web API 來驗證權杖。 權杖是由**安全性權杖伺服器（STS）** 使用私密金鑰來簽署。 STS 會發佈對應的公開金鑰。 若要驗證權杖，應用程式會使用 STS 公用金鑰來驗證簽章，以確認簽章是使用私密金鑰所建立。

權杖只在有限的時間內有效。 通常，STS 會提供一組權杖：

* 存取應用程式或受保護資源的存取權杖，以及
* 當存取權杖接近過期時，用來重新整理存取權杖的重新整理權杖。

存取權杖會傳遞至 Web API 做為`Authorization`標頭中的持有人權杖。 應用程式可以為 STS 提供重新整理權杖，如果使用者存取應用程式時未撤銷，則會取得新的存取權杖和新的重新整理權杖。 這就是處理離開企業之人員案例的方式。 當 STS 收到重新整理權杖時，如果使用者不再獲得授權，就不會發出另一個有效的存取權杖。

## <a name="json-web-tokens-jwts-and-claims"></a>JSON Web 權杖（Jwt）和宣告

Microsoft 身分識別平臺會將安全性權杖實作為包含**宣告**的**JSON Web 權杖（jwt）** 。

宣告[會提供有關](developer-glossary.md#claim)某個實體（例如用戶端應用程式或[資源擁有](developer-glossary.md#resource-owner)者）到另一個實體（例如資源伺服器）的判斷提示。 宣告也可以稱為 JWT 宣告或 JSON Web 權杖宣告。

宣告是轉送權杖主體相關事實的名稱/值配對。 例如，宣告可能包含授權伺服器所驗證之安全性主體的相關事實。 指定權杖中的宣告取決於許多專案，包括權杖類型、用來驗證主體的認證類型、應用程式設定等等。

應用程式可以使用宣告來執行各種工作，例如：

* 驗證權杖
* 識別權杖主體的[租](developer-glossary.md#tenant)使用者
* 顯示使用者資訊
* 判斷主體的授權

宣告是由索引鍵/值組所組成，可提供下列資訊：

* 產生權杖的安全性權杖伺服器
* 產生權杖的日期
* 主旨（例如使用者--守護程式除外）
* 物件，也就是產生權杖的應用程式
* 要求權杖的應用程式（用戶端）。 在 web 應用程式的案例中，這可能與物件相同

若要深入瞭解 Microsoft 身分識別平臺如何實行權杖和宣告資訊，請參閱[存取權杖](access-tokens.md)和[識別碼權杖](id-tokens.md)。

## <a name="how-each-flow-emits-tokens-and-codes"></a>每個流程如何發出權杖和代碼

視用戶端的建立方式而定，它可以使用 Microsoft 身分識別平臺所支援的一或多個驗證流程。 這些流程可能會產生各種權杖（識別碼權杖、重新整理權杖、存取權杖）以及授權碼，而且需要不同的權杖才能使其運作。 此圖表提供總覽：

|Flow | 具備 | 識別碼權杖 | 存取權杖 | 重新整理權杖 | 授權碼 |
|-----|----------|----------|--------------|---------------|--------------------|
|[授權碼流程](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[隱含流程](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合式 OIDC 流程](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[重新整理權杖兌換](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 重新整理權杖 | x | x | x| |
|[代理者流程](v2-oauth2-on-behalf-of-flow.md) | 存取權杖| x| x| x| |
|[用戶端認證](v2-oauth2-client-creds-grant-flow.md) | | | x （僅限應用程式）| | |

透過隱含模式發行的權杖有長度限制，因為透過 URL 傳遞回瀏覽器（其中`response_mode`是`query`或`fragment`）。  有些瀏覽器對於可以放在瀏覽器列中的 URL 大小有限制，而且太長時也會失敗。  因此，這些權杖不具有`groups`或`wids`宣告。

## <a name="next-steps"></a>後續步驟

其他涵蓋驗證和授權基本概念的主題：

* 若要瞭解 Microsoft 身分識別平臺中驗證和授權的基本概念，請參閱[驗證與授權](authentication-vs-authorization.md)。
* 請參閱[應用程式模型](application-model.md)，以瞭解註冊應用程式以與 Microsoft 身分識別平臺整合的過程。
* 請參閱[應用程式登入流程](app-sign-in-flow.md)，以瞭解 Microsoft 身分識別平臺中 web、桌面和行動應用程式的登入流程。
