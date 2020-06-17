---
title: 在 Android 應用程式中取得權杖
titleSuffix: Azure AD B2C
description: 如何建立 Android 應用程式，並使用 AppAuth 和 Azure Active Directory B2C 來管理使用者身分識別和驗證使用者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e3a38b9a02894eafd3ef6df657680d2e2a58a7e7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83638382"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用 Android 應用程式登入

Microsoft 身分識別平台會使用開放式標準，例如 OAuth2 和 OpenID Connect。 這些標準可讓您利用想要使用的任何程式庫，與 Azure Active Directory B2C 整合。 為了協助您使用其他程式庫，您可以使用這個逐步解說，它示範如何設定第三方程式庫以連線至 Microsoft 身分識別平台。 大部分實作 [RFC6749 OAuth2 規格](https://tools.ietf.org/html/rfc6749)的程式庫都能連線到 Microsoft 身分識別平台。

> [!WARNING]
> Microsoft 並不提供第三方程式庫的修正程式，也尚未審查這些程式庫。 此範例使用已藉由 Azure AD B2C 在基本案例中進行過相容性測試的第三方程式庫，稱為 AppAuth。 問題和功能要求應導向到程式庫的開放原始碼專案。 如需詳細資訊，請參閱[這篇文章](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)。
>
>

如果您是 OAuth2 或 OpenID Connect 新手，此範例組態可能諸多不太適合您。 建議您查看 [我們在此記載的通訊協定簡短概觀](protocols-overview.md)。

## <a name="get-an-azure-ad-b2c-directory"></a>取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。 目錄是適用於所有使用者、app、群組等項目的容器。 如果您還沒有此資源，請先 [建立 B2C 目錄](tutorial-create-tenant.md) 再繼續進行。

## <a name="create-an-application"></a>建立應用程式

然後，在您的 Azure AD B2C 租用戶中註冊應用程式。 這會為 Azure AD 提供其所需的資訊，使其與應用程式安全地進行通訊。

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

記錄 [應用程式 (用戶端) 識別碼]，以便在稍後的步驟中使用。

另請記錄您的自訂重新導向 URI，以便在稍後的步驟中使用。 例如： `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 。

## <a name="create-your-user-flows"></a>建立使用者流程

在 Azure AD B2C 中，使用者體驗是由[使用者流程](user-flow-overview.md)所定義，這是一組用來控制 Azure AD 行為的原則。 此應用程式需要登入和註冊使用者流程。 建立使用者流程時，請務必：

* 在使用者流程中選擇 [顯示名稱] 作為註冊屬性。
* 在每個使用者流程中，選擇 [顯示名稱] 和 [物件識別碼] 應用程式宣告。 您也可以選擇其他宣告。
* 建立每個使用者流程之後，請複製其 [名稱]。 其前置詞應該為 `b2c_1_`。  您稍後需要用到此使用者流程名稱。

建立您的使用者流程後，就可以開始建置您的應用程式。

## <a name="download-the-sample-code"></a>下載範例程式碼

我們[在 GitHub 上](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c)提供一個使用 AppAuth 和 Azure AD B2C 的操作範例。 您可以下載程式碼並執行。 您可以依照 [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) 中的指示，使用您自己的 Azure AD B2C 組態，快速開始使用您自己的應用程式。

這個範例修改自 [AppAuth](https://openid.github.io/AppAuth-Android/) 提供的範例。 請瀏覽其頁面，以深入了解 AppAuth 和相關功能。

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>修改您的應用程式來使用 Azure AD B2C 和 AppAuth

> [!NOTE]
> AppAuth 支援 Android API 16 (Jellybean) 和更新版本。 我們建議使用 API 23 和更新版本。
>

### <a name="configuration"></a>組態

您可以指定探索 URI，或指定授權端點和權杖端點 URI，以設定與 Azure AD B2C 通訊。 不論何者，您都需要下列資訊：

* 租用戶識別碼 (例如，contoso.onmicrosoft.com)
* 使用者流程名稱 (例如，B2C\_1\_SignUpIn)

如果您選擇自動探索授權和權杖端點 URI，您必須從探索 URI 擷取資訊。 取代下列 URL 中的 `<tenant-id>` 和 `<policy-name>`，即可產生探索 URI︰

```java
String mDiscoveryURI = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/v2.0/.well-known/openid-configuration";
```

然後，您可以取得授權和權杖端點 URI，並執行下列命令來建立 AuthorizationServiceConfiguration 物件︰

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

除了使用探索來取得授權和權杖端點 URI，您也可以取代下列 URL 中的 `<tenant-id>` 和 `<policy-name>`，明確加以指定︰

```java
String mAuthEndpoint = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/oauth2/v2.0/authorize";

String mTokenEndpoint = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/oauth2/v2.0/token";
```

執行下列程式碼來建立 AuthorizationServiceConfiguration 物件︰

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>授權

設定或擷取授權服務組態之後，就可以建構授權要求。 若要建立要求，您需要下列資訊︰

* 您先前記錄的用戶端識別碼 (應用程式識別碼)。 例如： `00000000-0000-0000-0000-000000000000` 。
* 您先前記錄的自訂重新導向 URI。 例如： `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 。

這兩個項目應該已在您[註冊應用程式](#create-an-application)時儲存。

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

有關如何完成此程序的其餘部分，請參閱 [AppAuth 指南](https://openid.github.io/AppAuth-Android/)。 如果您需要快速開始使用一個可操作的應用程式，請參閱[我們的範例](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c)。 請依照 [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) 中的步驟，輸入自己的 Azure AD B2C 組態。
