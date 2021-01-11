---
title: ADAL 至 MSAL 遷移指南 (MSAL4j) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何將 Azure Active Directory Authentication Library (ADAL) JAVA 應用程式遷移至 Microsoft 驗證程式庫 (MSAL) 。
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
ms.openlocfilehash: 0183471db274bb7fca59ed8f24aa87b2bf997fb6
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063734"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>適用于 JAVA 的 ADAL 至 MSAL 遷移指南

本文強調您需要進行的變更，以將使用 Azure Active Directory Authentication Library (ADAL) 的應用程式遷移至使用 Microsoft 驗證程式庫 (MSAL) 。

適用于 JAVA 的 Microsoft 驗證程式庫 (MSAL4J) 和 JAVA (ADAL4J) 的 Azure AD 驗證程式庫都是用來驗證 Azure AD 實體和要求 Azure AD 中的權杖。 到目前為止，大部分的開發人員都已使用 Azure AD 開發人員平臺 (v1.0) 使用 (ADAL) Azure AD 驗證程式庫來要求權杖，以驗證 Azure AD 身分識別 (公司和學校帳戶) 。

MSAL 提供下列優點：

- 因為它使用較新的 Microsoft 身分識別平臺端點，所以您可以透過 Azure AD 企業對取用者 (B2C) ，驗證一組更廣泛的 Microsoft 身分識別，例如 Azure AD 身分識別、Microsoft 帳戶，以及社交和本機帳戶。
- 您的使用者將獲得最佳的單一登入體驗。
- 您的應用程式可以啟用累加式同意，而支援的條件式存取則比較容易。

MSAL for JAVA 是我們建議您搭配 Microsoft 身分識別平臺使用的驗證程式庫。 ADAL4J 上不會執行任何新功能。 接下來的工作將著重于改善 MSAL。

## <a name="differences"></a>差異

如果您已經使用 (v1.0) 端點 (和 ADAL4J) 開發人員的 Azure AD，您可能想要閱讀 [Microsoft 身分識別平臺 (v2.0) 端點的不同之處](../azuread-dev/azure-ad-endpoint-comparison.md)。

## <a name="scopes-not-resources"></a>範圍，而非資源

ADAL4J 會取得資源的權杖，而 MSAL for JAVA 會取得範圍的權杖。 JAVA 類別的一些 MSAL 需要範圍參數。 此參數是字串清單，可宣告所需的許可權和所要求的資源。 請參閱 [Microsoft Graph 的範圍](/graph/permissions-reference) ，以查看範例範圍。

您可以將 `/.default` 範圍尾碼新增至資源，以協助將您的應用程式從 v1.0 端點 (ADAL) 遷移至 Microsoft 身分識別平臺端點 (MSAL) 。 例如，針對的資源值 `https://graph.microsoft.com` ，相等的範圍值為 `https://graph.microsoft.com/.default` 。  如果資源不是在 URL 格式中，而是表單的資源識別碼 `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` ，您仍然可以使用範圍值 `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` 。

如需不同類型的範圍的詳細資訊，請參閱 [Microsoft 身分識別平臺中的許可權和同意](./v2-permissions-and-consent.md) ，以及 [接受 V1.0 權杖的 Web API 範圍](./msal-v1-app-scopes.md) 文章。

## <a name="core-classes"></a>核心類別

在 ADAL4J 中， `AuthenticationContext` 類別代表您透過授權單位 (STS) 或授權伺服器連線至安全性權杖服務。 不過，MSAL for JAVA 是針對用戶端應用程式所設計。 它提供兩個不同的類別： `PublicClientApplication` 和 `ConfidentialClientApplication` 表示用戶端應用程式。  後者 `ConfidentialClientApplication` 代表設計來安全地維護秘密的應用程式，例如背景程式應用程式的應用程式識別碼。

下表顯示 ADAL4J 函式如何對應至 JAVA 函數的新 MSAL：

| ADAL4J 方法| MSAL4J 方法|
|------|-------|
|acquireToken (字串資源、ClientCredential 認證、>authenticationcallback 回呼)  | acquireToken (ClientCredentialParameters) |
|acquireToken (字串資源、ClientAssertion 判斷提示、>authenticationcallback 回呼) |acquireToken (ClientCredentialParameters) |
|acquireToken (字串資源、AsymmetricKeyCredential 認證、>authenticationcallback 回呼) |acquireToken (ClientCredentialParameters) |
|acquireToken (字串資源、字串 clientId、字串使用者名稱、字串密碼、>authenticationcallback 回呼) | acquireToken (UsernamePasswordParameters) |
|acquireToken (字串資源、字串 clientId、字串使用者名稱、字串密碼 = null、>authenticationcallback 回呼) |acquireToken (IntegratedWindowsAuthenticationParameters) |
|acquireToken (字串資源、UserAssertion userAssertion、ClientCredential credential、>authenticationcallback 回呼) | acquireToken (OnBehalfOfParameters) |
|acquireTokenByAuthorizationCode ( # A1 | acquireToken (AuthorizationCodeParameters)  |
| acquireDeviceCode ( # A1 和 acquireTokenByDeviceCode ( # A3| acquireToken (DeviceCodeParameters) |
|acquireTokenByRefreshToken ( # A1| acquireTokenSilently (SilentParameters) |

## <a name="iaccount-instead-of-iuser"></a>IAccount 而不是 IUser

ADAL4J 操作的使用者。 雖然使用者代表單一人或軟體代理程式，但在 Microsoft 身分識別系統中可以有一或多個帳戶。 例如，使用者可能會有數個 Azure AD、Azure AD B2C 或 Microsoft 個人帳戶。

MSAL for JAVA 會透過介面定義帳戶的概念 `IAccount` 。 這是 ADAL4J 的重大變更，但這是很好的變更，因為它會瞭解相同的使用者可以有多個帳戶，甚至是在不同的 Azure AD 目錄中。 MSAL for JAVA 在來賓案例中提供更好的資訊，因為提供了主帳戶資訊。

## <a name="cache-persistence"></a>快取持續性

ADAL4J 不支援權杖快取。
MSAL for JAVA 新增了 [權杖](msal-acquire-cache-tokens.md) 快取，以簡化權杖存留期的管理，方法是在可能的情況下自動重新整理過期的權杖，並防止使用者不必要的提示盡可能提供認證。

## <a name="common-authority"></a>一般授權單位

在1.0 版中，如果您使用 `https://login.microsoftonline.com/common` 授權單位，則使用者可以使用任何組織) 的任何 Azure Active Directory (AAD) 帳戶 (進行登入。

如果您在 v2.0 中使用 `https://login.microsoftonline.com/common` 授權單位，則使用者可以使用任何 AAD 組織登入，或甚至是 Microsoft 個人帳戶 (MSA) 。 在適用于 JAVA 的 MSAL 中，如果您想要限制登入任何 AAD 帳戶，您需要使用 `https://login.microsoftonline.com/organizations` 授權 (這與 ADAL4J) 的行為相同。 若要指定授權單位，請在 `authority` 建立類別時，于 [PublicClientApplication](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) 中設定參數 `PublicClientApplication` 。

## <a name="v10-and-v20-tokens"></a>v1.0 和 v2.0 權杖

v1.0 端點 (由 ADAL 使用) 只會發出 v1.0 權杖。

MSAL) 使用的 v2.0 端點 (可以發出 v1.0 和 v2.0 權杖。 Web API 應用程式資訊清單的屬性可讓開發人員選擇接受的權杖版本。 請參閱 `accessTokenAcceptedVersion` [應用程式資訊清單](./reference-app-manifest.md) 參考檔中的。

如需1.0 版和 v2.0 權杖的詳細資訊，請參閱 [Azure Active Directory 存取權杖](./access-tokens.md)。

## <a name="adal-to-msal-migration"></a>MSAL 至 ADAL 的移轉

在 ADAL4J 中，會公開重新整理權杖，讓開發人員可以進行快取。 然後，他們會使用 `AcquireTokenByRefreshToken()` 來啟用解決方案，例如，執行長時間執行的服務，以便在使用者不再連線時，代表使用者重新整理儀表板。

基於安全性理由，MSAL for JAVA 不會公開重新整理權杖。 相反地，MSAL 會為您處理重新整理權杖的程式。

MSAL for JAVA 有一個 API，可讓您將使用 ADAL4j 取得的重新整理權杖遷移至 ClientApplication： [acquireToken (RefreshTokenParameters) ](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-)。 使用這個方法，您可提供先前所用的重新整理權杖，以及您所需的任何範圍 (資源)。 重新整理權杖將會針對新的權杖進行交換，並快取以供您的應用程式使用。

下列程式碼片段顯示機密用戶端應用程式中的一些遷移程式碼：

```java
String rt = GetCachedRefreshTokenForSignedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

`IAuthenticationResult`會傳回存取權杖和識別碼權杖，而新的重新整理權杖則會儲存在快取中。
應用程式現在也會包含 IAccount：

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

若要使用目前在快取中的權杖，請呼叫：

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
