---
title: 安全性權杖 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解 Microsoft 身分識別平臺中安全性權杖的基本概念。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6d9f5538d377be1414089e591559344bde4f381a
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795634"
---
# <a name="security-tokens"></a>安全性權杖

集中式身分識別提供者特別適用于使用者位於全球各地且不需要從商業網路登入的應用程式。 Microsoft 身分識別平臺會驗證使用者並提供安全性權杖，例如 [存取權杖](developer-glossary.md#access-token)、重新整理 [權杖](developer-glossary.md#refresh-token)和 [識別碼權杖](developer-glossary.md#id-token)。 安全性權杖可讓 [用戶端應用程式](developer-glossary.md#client-application) 存取 [資源伺服器](developer-glossary.md#resource-server)上的受保護資源。

**存取權杖**：存取權杖是 [授權伺服器](developer-glossary.md#authorization-server) 在 [OAuth 2.0](active-directory-v2-protocols.md) 流程中所發出的安全性權杖。 它包含使用者的相關資訊，以及用於權杖的資源。 此資訊可以用來存取 web Api 和其他受保護的資源。 存取權杖會由資源進行驗證，以授與用戶端應用程式的存取權。 若要深入瞭解 Microsoft 身分識別平臺如何發行存取權杖，請參閱 [存取權杖](access-tokens.md)。

重新整理 **權杖**：因為存取權杖只在短時間內有效，所以授權伺服器有時會在發出存取權杖時發出重新整理權杖。 然後，用戶端應用程式可以在需要時，為新的存取權杖交換此重新整理權杖。 若要深入瞭解 Microsoft 身分識別平臺如何使用重新整理權杖來撤銷許可權，請參閱 [權杖撤銷](access-tokens.md#token-revocation)。

**識別碼權杖**：識別碼權杖會作為 [OpenID Connect](v2-protocols-oidc.md) 流程的一部分傳送給用戶端應用程式。 您可以連同存取權杖一起傳送。 用戶端會使用識別碼權杖來驗證使用者。 若要深入瞭解 Microsoft 身分識別平臺如何發出識別碼權杖，請參閱 [識別碼權杖](id-tokens.md)。

> [!NOTE]
> 本文討論 OAuth2 和 OpenID Connect 通訊協定所使用的安全性權杖。 許多企業應用程式會使用 SAML 來驗證使用者。 如需 SAML 判斷提示的詳細資訊，請參閱 [AZURE ACTIVE DIRECTORY saml 權杖參考](reference-saml-tokens.md)。

## <a name="validate-security-tokens"></a>驗證安全性權杖

它是由產生權杖的應用程式、已登入使用者的 web 應用程式，或呼叫來驗證權杖的 web API 所組成。 權杖是由具有私密金鑰的授權伺服器所簽署。 授權伺服器會發佈對應的公開金鑰。 若要驗證權杖，應用程式會使用授權伺服器公開金鑰來驗證簽章，以驗證簽章是使用私密金鑰所建立。

權杖的有效時間僅限一段時間。 授權伺服器通常會提供一組權杖，例如：

* 存取權杖，可存取應用程式或受保護的資源。
* 重新整理權杖，用來在存取權杖接近到期時重新整理存取權杖。

存取權杖會傳遞至 web API 作為標頭中的持有人權杖 `Authorization` 。 應用程式可提供重新整理權杖給授權伺服器。 如果未撤銷使用者對應用程式的存取權，則會取得新的存取權杖和新的重新整理權杖。 這是處理企業中某位公司的案例的方式。 當授權伺服器收到重新整理權杖時，如果使用者已不再獲得授權，則不會發出另一個有效的存取權杖。

## <a name="json-web-tokens-and-claims"></a>JSON Web 權杖和宣告

Microsoft 身分識別平臺會將安全性權杖實作為 JSON Web 權杖， (Jwt 包含 *宣告* 的) 。 由於 Jwt 是用來做為安全性權杖，因此這種形式的驗證有時也稱為 *JWT 驗證*。

宣告 [會提供一個](developer-glossary.md#claim) 實體（例如用戶端應用程式或 [資源擁有](developer-glossary.md#resource-owner)者）給另一個實體（例如資源伺服器）的判斷提示。 宣告也可以稱為 JWT 宣告或 JSON Web 權杖宣告。

宣告是一組名稱或值組，可轉送有關權杖主體的事實。 例如，宣告可能包含授權伺服器所驗證之安全性主體的相關事實。 特定權杖中的宣告取決於許多專案，例如權杖的類型、用來驗證主體的認證類型，以及應用程式設定。

應用程式可以使用宣告來執行各種工作，例如：

* 驗證權杖。
* 識別權杖主體的 [租](developer-glossary.md#tenant)使用者。
* 顯示使用者資訊。
* 判斷主體的授權。

宣告是由提供資訊的索引鍵/值組所組成，如下所示：

* 產生權杖的安全性權杖伺服器。
* 產生權杖的日期。
* 主體 (例如使用者--) 的守護程式除外。
* 物件，也就是產生權杖的應用程式。
* 應用程式 (要求權杖的用戶端) 。 在 web 應用程式的案例中，此應用程式可能與物件相同。

若要深入瞭解 Microsoft 身分識別平臺如何實行權杖和宣告資訊，請參閱 [存取權杖](access-tokens.md) 和 [識別碼權杖](id-tokens.md)。

## <a name="how-each-flow-emits-tokens-and-codes"></a>每個流程如何發出權杖和授權碼

視用戶端的建置方式而定，其可使用 Microsoft 身分識別平台所支援的一或多個驗證流程。 這些流程可以產生各種權杖， (識別碼權杖、重新整理權杖、存取權杖) 和授權碼。 它們需要不同的權杖，才能讓它們正常運作。 下表提供總覽。

|Flow | 需要 | 識別碼權杖 | 存取權杖 | 重新整理權杖 | 授權碼 |
|-----|----------|----------|--------------|---------------|--------------------|
|[授權碼流程](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[隱含流程](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合式 OIDC 流程](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[重新整理權杖兌換](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 重新整理權杖 | x | x | x| |
|[代理者流程](v2-oauth2-on-behalf-of-flow.md) | 存取權杖| x| x| x| |
|[用戶端認證](v2-oauth2-client-creds-grant-flow.md) | | | x (僅限應用程式) | | |

透過隱含模式發出的權杖會有長度限制，因為它們會透過 URL 傳遞回瀏覽器，其中 `response_mode` 是 `query` 或 `fragment` 。 某些瀏覽器對於可放在瀏覽器列中的 URL 大小有限制，當時間太長時，就會失敗。 因此，這些權杖沒有 `groups` 或 `wids` 宣告。

## <a name="next-steps"></a>後續步驟

如需有關 Microsoft 身分識別平臺中的驗證和授權的詳細資訊，請參閱下列文章：

* 若要瞭解驗證與授權的基本概念，請參閱 [驗證與授權](authentication-vs-authorization.md)的比較。
* 若要瞭解如何註冊您的應用程式以進行整合，請參閱 [應用程式模型](application-model.md)。
* 若要瞭解 web、desktop 和 mobile apps 的登入流程，請參閱 [應用程式登入流程](app-sign-in-flow.md)。
