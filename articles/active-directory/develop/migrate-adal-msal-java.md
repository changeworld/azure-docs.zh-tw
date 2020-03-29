---
title: ADAL 到 MSAL 遷移指南 （MSAL4j） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何將 Azure 活動目錄身份驗證庫 （ADAL） JAVA 應用遷移到 Microsoft 身份驗證庫 （MSAL）。
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 2d85a3a5d876d731655bb30f7d37a6f42fa5c220
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696719"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>JAVA 的 ADAL 到 MSAL 遷移指南

本文重點介紹遷移使用 Azure 活動目錄身份驗證庫 （ADAL） 使用 Microsoft 身份驗證庫 （MSAL） 的應用所需的更改。

用於 JAVA 的 Microsoft 身份驗證庫 （MSAL4J） 和用於 JAVA 的 Azure AD 身份驗證庫 （ADAL4J） 都用於對 Azure AD 實體進行身份驗證，並從 Azure AD 請求權杖。 到目前為止，大多數開發人員都使用 Azure AD 為開發人員平臺 （v1.0） 進行身份驗證 Azure AD 標識（工作帳戶和學校帳戶），通過使用 Azure AD 身份驗證庫 （ADAL） 請求權杖。

MSAL 提供以下優勢：

- 由於它使用較新的 Microsoft 標識平臺終結點，因此可以通過 Azure AD 業務對消費者 （B2C） 對更廣泛的 Microsoft 標識（如 Azure AD 標識、Microsoft 帳戶以及社交和本地帳戶）進行身份驗證。
- 您的使用者將獲得最佳的單點登錄體驗。
- 您的應用程式可以啟用增量同意，並且支援條件訪問更容易。

JAVA 的 MSAL 是我們建議您與 Microsoft 標識平臺一起使用的身份驗證庫。 ADAL4J 上不會實現任何新功能。 今後的所有努力都側重于改進MSAL。

## <a name="differences"></a>差異

如果您一直在使用 Azure AD 為開發人員 （v1.0） 終結點 （和 ADAL4J）， 您可能需要閱讀[Microsoft 標識平臺 （v2.0） 終結點有何不同？](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="scopes-not-resources"></a>範圍，而非資源

ADAL4J 獲取資源的權杖，而 JAVA 的 MSAL 獲取作用域的權杖。 許多用於 JAVA 類的 MSAL 都需要作用域參數。 此參數是聲明請求的所需許可權和資源的字串清單。 請參閱[Microsoft 圖形的作用域](https://docs.microsoft.com/graph/permissions-reference)以查看示例作用域。

## <a name="core-classes"></a>核心類別

在 ADAL4J`AuthenticationContext`中，該類表示您通過頒發機構連接到安全權杖服務 （STS） 或授權伺服器。 但是，JAVA 的 MSAL 是圍繞用戶端應用程式設計的。 它提供了兩個單獨的類：`PublicClientApplication`和`ConfidentialClientApplication`表示用戶端應用程式。  後者`ConfidentialClientApplication`表示一個應用程式，它旨在安全地維護機密，如守護進程應用程式的應用程式識別碼。

下表顯示了 ADAL4J 函數如何映射到 JAVA 函數的新 MSAL：

| ADAL4J 方法| MSAL4J 方法|
|------|-------|
|獲取權杖（字串資源、用戶端憑據、身份驗證回檔回檔） | 獲取權杖（用戶端憑據參數）|
|獲取權杖（字串資源、用戶端斷言、身份驗證回檔回檔）|獲取權杖（用戶端憑據參數）|
|獲取權杖（字串資源、非對稱金鑰憑據、身份驗證回檔回檔）|獲取權杖（用戶端憑據參數）|
|獲取權杖（字串資源、字串用戶端 Id、字串使用者名、字串密碼、身份驗證回檔回檔）| 獲取權杖（使用者名密碼參數）|
|獲取權杖（字串資源、字串用戶端 Id、字串使用者名、字串密碼\null、身份驗證回檔回檔）|獲取權杖（集成Windows身份驗證參數）|
|獲取權杖（字串資源、使用者斷言使用者斷言、用戶端憑據、身份驗證回檔回檔）| 獲取權杖（代表參數）|
|獲取權杖授權代碼（） | 獲取權杖（授權代碼參數） |
| 獲取設備代碼（）和獲取權杖碼（）| 獲取權杖（設備代碼參數）|
|獲取權杖刷新權杖（）| 獲得權杖靜默（靜音參數）|

## <a name="iaccount-instead-of-iuser"></a>I帳戶，而不是IUser

ADAL4J 操縱了使用者。 儘管使用者表示單個人工或軟體代理，但它可以在 Microsoft 標識系統中具有一個或多個帳戶。 例如，使用者可能有多個 Azure AD、Azure AD B2C 或 Microsoft 個人帳戶。

JAVA 的 MSAL 通過`IAccount`介面定義了帳戶的概念。 這是 ADAL4J 的一個重大更改，但它是一個很好的更改，因為它捕獲了同一個使用者可以有多個帳戶，甚至在不同的 Azure AD 目錄中。 由於提供了主帳戶資訊，因此 JAVA 的 MSAL 在來賓方案中提供了更好的資訊。

## <a name="cache-persistence"></a>快取持續性

ADAL4J 不支援權杖緩存。
JAVA 的 MSAL 添加了[權杖緩存](msal-acquire-cache-tokens.md)，通過盡可能自動刷新過期權杖並防止使用者盡可能提供憑據的不必要的提示來簡化權杖存留期的管理。

## <a name="common-authority"></a>共同權力機構

在 v1.0 中，如果使用`https://login.microsoftonline.com/common`該許可權，使用者可以使用任何 Azure 活動目錄 （AAD） 帳戶（對於任何組織）登錄。

如果您在 v2.0 中使用許可權`https://login.microsoftonline.com/common`，使用者可以與任何 AAD 組織，甚至 Microsoft 個人帳戶 （MSA） 登錄。 在 JAVA 的 MSAL 中，如果要限制登錄到任何 AAD 帳戶，則需要使用`https://login.microsoftonline.com/organizations`該許可權（這與 ADAL4J 的行為相同）。 要指定許可權，`authority`請在創建類時在[PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html)方法中設置`PublicClientApplication`參數。

## <a name="v10-and-v20-tokens"></a>v1.0 和 v2.0 權杖

v1.0 端點 (由 ADAL 使用) 只會發出 v1.0 權杖。

v2.0 終結點（MSAL 使用）可以發出 v1.0 和 v2.0 權杖。 Web API 的應用程式資訊清單屬性可讓開發人員選擇要接受哪個版本的權杖。 請參閱`accessTokenAcceptedVersion`[應用程式清單](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)參考文檔。

有關 v1.0 和 v2.0 權杖的詳細資訊，請參閱[Azure 活動目錄訪問權杖](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)。

## <a name="adal-to-msal-migration"></a>MSAL 至 ADAL 的移轉

在 ADAL4J 中，刷新權杖被公開，允許開發人員緩存它們。 然後，它們將`AcquireTokenByRefreshToken()`用於啟用解決方案，例如實現長時間運行的服務，以便在使用者不再連接時代表使用者刷新儀表板。

出於安全原因，JAVA 的 MSAL 不會公開刷新權杖。 相反，MSAL 會為您處理刷新權杖。

JAVA 的 MSAL 具有一個 API，允許您將使用 ADAL4j 獲取的刷新權杖遷移到用戶端應用程式：[獲取權杖（刷新權杖參數）。](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-) 使用這個方法，您可提供先前所用的重新整理權杖，以及您所需的任何範圍 (資源)。 刷新權杖將交換為新的權杖，並緩存供應用程式使用。

以下程式碼片段顯示了機密用戶端應用程式中的一些遷移代碼：

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

返回`IAuthenticationResult`訪問權杖和 ID 權杖，而新的刷新權杖存儲在緩存中。 該應用程式現在還將包含一個 IAccount：

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

要使用當前緩存中的權杖，請調用：

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```
