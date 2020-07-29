---
title: ADAL 至 MSAL 遷移指南（MSAL4j） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何將您的 Azure Active Directory Authentication Library （ADAL） JAVA 應用程式遷移至 Microsoft 驗證程式庫（MSAL）。
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 3b775d88409a03f6de54b9db3ab62d6988c5bddd
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87313042"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>適用于 JAVA 的 MSAL 遷移指南的 ADAL

本文重點說明您需要進行的變更，以遷移使用 Azure Active Directory Authentication Library （ADAL）的應用程式來使用 Microsoft Authentication Library （MSAL）。

適用于 java 的 Microsoft 驗證程式庫（MSAL4J）和 Azure AD 驗證 Library for JAVA （ADAL4J）都是用來驗證 Azure AD 實體，並從 Azure AD 要求權杖。 到目前為止，大部分的開發人員都已經與 Azure AD for 開發人員平臺（v1.0）合作，藉由使用 Azure AD 驗證程式庫（ADAL）來要求權杖來驗證 Azure AD 身分識別（公司和學校帳戶）。

MSAL 提供下列優點：

- 因為它使用較新的 Microsoft 身分識別平臺端點，所以您可以透過 Azure AD 企業對消費者（B2C）來驗證更廣泛的 Microsoft 身分識別，例如 Azure AD 身分識別、Microsoft 帳戶，以及社交和本機帳戶。
- 您的使用者將獲得最佳的單一登入體驗。
- 您的應用程式可以啟用累加式同意，而且支援條件式存取比較簡單。

MSAL for JAVA 是我們建議您搭配 Microsoft 身分識別平臺使用的驗證程式庫。 在 ADAL4J 上不會執行任何新功能。 未來的所有工作都著重于改善 MSAL。

## <a name="differences"></a>差異

如果您使用的是開發人員（v1.0）端點（和 ADAL4J）的 Azure AD，您可能會想要閱讀[Microsoft 身分識別平臺（v2.0）端點的不同之處？](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)。

## <a name="scopes-not-resources"></a>範圍，而非資源

ADAL4J 會取得資源的權杖，而 MSAL for JAVA 會取得範圍的權杖。 JAVA 類別的一些 MSAL 需要範圍參數。 這個參數是宣告所需許可權和所要求資源的字串清單。 請參閱[Microsoft Graph 的範圍](https://docs.microsoft.com/graph/permissions-reference)，以查看範例範圍。

您可以將 `/.default` 範圍尾碼新增至資源，以協助將您的應用程式從 v1.0 端點（ADAL）遷移至 Microsoft 身分識別平臺端點（MSAL）。 例如，針對的資源值 `https://graph.microsoft.com` ，對等的範圍值為 `https://graph.microsoft.com/.default` 。  如果資源不是在 URL 表單中，而是表單的資源識別碼 `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` ，您仍然可以使用範圍值做為 `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` 。

如需不同類型範圍的詳細資訊，請參閱[Microsoft 身分識別平臺中的許可權和同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)和[接受 V1.0 權杖的 Web API 範圍一](https://docs.microsoft.com/azure/active-directory/develop/msal-v1-app-scopes)文。

## <a name="core-classes"></a>核心類別

在 ADAL4J 中， `AuthenticationContext` 類別代表安全性權杖服務（STS）或授權伺服器（透過授權單位）的連接。 不過，MSAL for JAVA 是針對用戶端應用程式所設計。 它提供兩個不同的類別： `PublicClientApplication` 和 `ConfidentialClientApplication` 以代表用戶端應用程式。  後者 `ConfidentialClientApplication` 代表的應用程式是設計用來安全地維護秘密，例如背景工作應用程式的應用程式識別碼。

下表顯示 ADAL4J 函式如何對應至 JAVA 函數的新 MSAL：

| ADAL4J 方法| MSAL4J 方法|
|------|-------|
|acquireToken （字串資源、ClientCredential 認證、Authenticationcallback 傳給回呼） | acquireToken （ClientCredentialParameters）|
|acquireToken （字串資源、ClientAssertion 判斷提示、Authenticationcallback 傳給回呼）|acquireToken （ClientCredentialParameters）|
|acquireToken （字串資源、AsymmetricKeyCredential 認證、Authenticationcallback 傳給回呼）|acquireToken （ClientCredentialParameters）|
|acquireToken （字串資源，字串 clientId，字串使用者名稱，字串密碼，Authenticationcallback 傳給回呼）| acquireToken （UsernamePasswordParameters）|
|acquireToken （字串資源，字串 clientId，字串使用者名稱，字串密碼 = null，Authenticationcallback 傳給回呼）|acquireToken （IntegratedWindowsAuthenticationParameters）|
|acquireToken （字串資源，UserAssertion userAssertion，ClientCredential credential，Authenticationcallback 傳給回呼）| acquireToken （OnBehalfOfParameters）|
|acquireTokenByAuthorizationCode （） | acquireToken （AuthorizationCodeParameters） |
| acquireDeviceCode （）和 acquireTokenByDeviceCode （）| acquireToken （DeviceCodeParameters）|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount 而不是 IUser

ADAL4J 操作的使用者。 雖然使用者代表單一的人或軟體代理程式，但在 Microsoft 身分識別系統中可能會有一或多個帳戶。 例如，使用者可能會有數個 Azure AD、Azure AD B2C 或 Microsoft 個人帳戶。

MSAL for JAVA 會透過介面來定義帳戶的概念 `IAccount` 。 這是 ADAL4J 的重大變更，但這是一個很好的方式，因為它會獲得相同的使用者可以擁有數個帳戶的事實，甚至是在不同的 Azure AD 目錄中。 MSAL for JAVA 會在來賓案例中提供更好的資訊，因為提供了主帳戶資訊。

## <a name="cache-persistence"></a>快取持續性

ADAL4J 沒有權杖快取的支援。
MSAL for JAVA 會新增[權杖](msal-acquire-cache-tokens.md)快取，藉由自動重新整理過期的權杖，以簡化權杖存留期的管理，並防止使用者在可能時提供認證的不必要提示。

## <a name="common-authority"></a>一般授權

在 v1.0 中，如果您使用 `https://login.microsoftonline.com/common` 授權單位，使用者可以使用任何 Azure Active Directory （AAD）帳戶（適用于任何組織）登入。

如果您使用 v2.0 `https://login.microsoftonline.com/common` 中的授權單位，使用者可以使用任何 AAD 組織或甚至是 Microsoft 個人帳戶（MSA）登入。 在 MSAL for JAVA 中，如果您想要將登入限制為任何 AAD 帳戶，您必須使用 `https://login.microsoftonline.com/organizations` 授權單位（這與使用 ADAL4J 的行為相同）。 若要指定授權單位，請在 `authority` 建立類別時，在[PublicClientApplication](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html)方法中設定參數 `PublicClientApplication` 。

## <a name="v10-and-v20-tokens"></a>v1.0 和 v2.0 權杖

v1.0 端點 (由 ADAL 使用) 只會發出 v1.0 權杖。

V2.0 端點（由 MSAL 使用）可以發出 v1.0 和 v2.0 權杖。 Web API 的應用程式資訊清單屬性，可讓開發人員選擇接受哪個版本的權杖。 請參閱 `accessTokenAcceptedVersion` [應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)參考檔中的。

如需 v1.0 和 v2.0 權杖的詳細資訊，請參閱[Azure Active Directory 存取權杖](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)。

## <a name="adal-to-msal-migration"></a>MSAL 至 ADAL 的移轉

在 ADAL4J 中，已公開重新整理權杖--允許開發人員加以快取。 然後，他們會使用 `AcquireTokenByRefreshToken()` 來啟用一些解決方案，例如，在使用者不再連線時，會代表使用者重新整理儀表板的長時間執行服務。

基於安全考慮，MSAL for JAVA 不會公開重新整理權杖。 相反地，MSAL 會為您處理重新整理權杖。

MSAL for JAVA 有一個 API，可讓您將使用 ADAL4j 取得的重新整理權杖遷移至 ClientApplication： [acquireToken （RefreshTokenParameters）](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-)。 使用這個方法，您可提供先前所用的重新整理權杖，以及您所需的任何範圍 (資源)。 系統會針對新的重新整理權杖進行交換，並快取以供您的應用程式使用。

下列程式碼片段顯示機密用戶端應用程式中的一些遷移程式碼：

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

`IAuthenticationResult`會傳回存取權杖和識別碼權杖，而您的新重新整理權杖會儲存在快取中。
應用程式現在也會包含 IAccount：

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

若要使用目前位於快取中的權杖，請呼叫：

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
