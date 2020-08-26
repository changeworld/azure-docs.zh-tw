---
title: Android MSAL 設定檔 |蔚藍
titleSuffix: Microsoft identity platform
description: 概述 Android Microsoft 驗證程式庫 (MSAL) 設定檔案，代表 Azure Active Directory 中的應用程式設定。
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: f5950347fff380fcfbaa89834407ff5f497a9719
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854908"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Android Microsoft 驗證程式庫設定檔案

Android Microsoft 驗證程式庫 (MSAL) 隨附預設的設定 [JSON](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) 檔案，您可以自訂此檔案來定義公用用戶端應用程式的行為，例如預設授權單位、您將使用哪些授權單位等等。

本文將協助您瞭解設定檔中的各種設定，以及如何指定要在以 MSAL 為基礎的應用程式中使用的設定檔。

## <a name="configuration-settings"></a>組態設定

### <a name="general-settings"></a>一般設定

| 屬性 | 資料類型 | 必要 | 注意 |
|-----------|------------|-------------|-------|
| `client_id` | String | 是 | [應用程式註冊頁面](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)中的應用程式用戶端識別碼 |
| `redirect_uri`   | String | 是 | [應用程式註冊頁面](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)中的應用程式重新導向 URI |
| `authorities` | 清單\<Authority> | 否 | 您應用程式所需的授權單位清單 |
| `authorization_user_agent` | AuthorizationAgent (列舉)  | 否 | 可能的值： `DEFAULT` 、 `BROWSER` 、 `WEBVIEW` |
| `http` | HttpConfiguration | 否 | 設定 `HttpUrlConnection` `connect_timeout` 和 `read_timeout` |
| `logging` | LoggingConfiguration | 否 | 指定記錄詳細資料的層級。 選擇性設定包括： `pii_enabled` （接受布林值），以及 `log_level` 接受 `ERROR` 、 `WARNING` 、 `INFO` 或的 `VERBOSE` 。 |

### <a name="client_id"></a>client_id

您註冊應用程式時所建立的用戶端識別碼或應用程式識別碼。

### <a name="redirect_uri"></a>redirect_uri

您註冊應用程式時註冊的重新導向 URI。 如果重新導向 URI 是對訊息代理程式應用程式，請參閱 [公用用戶端應用程式的重新導向 uri](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) ，以確定您針對訊息代理程式應用程式使用的是正確的重新導向 uri 格式。

### <a name="authorities"></a>當局

您已知且受信任的授權單位清單。 除了此處所列的授權單位之外，MSAL 也會查詢 Microsoft，以取得 Microsoft 已知的雲端與授權單位清單。 在這份授權清單中，指定授權單位的類型以及任何其他選擇性參數，例如 `"audience"` ，應該根據您應用程式的註冊，與您應用程式的物件相符。 以下是授權清單的範例：

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenant_id": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>將 AAD 授權單位 & 的使用者對應到 Microsoft 身分識別平臺端點

| 類型 | 對象 | 租用戶識別碼 | Authority_Url | 產生的端點 | 備註 |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common` 這是帳戶所在的租使用者別名。 例如，特定的 Azure Active Directory 租使用者或 Microsoft 帳戶系統。 |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | 只有存在於 contoso.com 中的帳戶可以取得權杖。 任何已驗證的網域或租使用者 GUID 都可作為租使用者識別碼使用。 |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | 只有 Azure Active Directory 帳戶可以與此端點搭配使用。 Microsoft 帳戶可以是組織的成員。 若要使用組織內資源的 Microsoft 帳戶取得權杖，請指定您想要權杖的組織租使用者。 |
| AAD | PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | 只有 Microsoft 帳戶可以使用此端點。 |
| B2C | | | 查看結果端點 | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | 只有存在於 contoso.onmicrosoft.com 租使用者中的帳戶才能取得權杖。 在此範例中，B2C 原則是授權單位 URL 路徑的一部分。 |

> [!NOTE]
> 無法在 MSAL 中啟用和停用授權驗證。
> 您可以透過設定來指定授權單位，或透過中繼資料從 Microsoft 得知授權單位。
> 如果 MSAL 接收到未知授權單位的權杖要求，則為 `MsalClientException` 類型的 `UnknownAuthority` 結果。

#### <a name="authority-properties"></a>授權屬性

| 屬性 | 資料類型  | 必要 | 注意 |
|-----------|-------------|-----------|--------|
| `type` | String | 是 | 鏡像您的應用程式目標物件或帳戶類型。 可能的值： `AAD` 、 `B2C` |
| `audience` | Object | 否 | 只有在 type = 時才適用 `AAD` 。 指定應用程式的目標身分識別。 使用您的應用程式註冊中的值 |
| `authority_url` | String | 是 | 只有在 type = 時才需要 `B2C` 。 指定您的應用程式應使用的授權單位 URL 或原則  |
| `default` | boolean | 是 | `"default":true`當指定一個或多個授權單位時，需要單一的。 |

#### <a name="audience-properties"></a>物件屬性

| 屬性 | 資料類型  | 必要 | 注意 |
|-----------|-------------|------------|-------|
| `type` | String | 是 | 指定您的應用程式要設為目標的物件。 可能的值： `AzureADandPersonalMicrosoftAccount` 、 `PersonalMicrosoftAccount` 、 `AzureADMultipleOrgs` 、 `AzureADMyOrg` |
| `tenant_id` | String | 是 | 只有在時才需要 `"type":"AzureADMyOrg"` 。 其他值的選擇性 `type` 。 這可以是租使用者網域，例如 `contoso.com` ，或租使用者識別碼，例如 `72f988bf-86f1-41af-91ab-2d7cd011db46`)  |

### <a name="authorization_user_agent"></a>authorization_user_agent

指出當您登入帳戶或授與資源的存取權時，是要使用內嵌的 web 程式，還是裝置上的預設瀏覽器。

可能的值：
- `DEFAULT`：優先于系統瀏覽器。 如果裝置上無法使用瀏覽器，則會使用內嵌的 web view。
- `WEBVIEW`：使用內嵌的 web view。
- `BROWSER`：使用裝置上的預設瀏覽器。

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

針對支援多國雲端的用戶端，請指定 `true` 。 然後，Microsoft 身分識別平臺會在授權和權杖兌換期間自動重新導向至正確的國家/地區雲端。 您可以藉由檢查與相關聯的授權單位，判斷已登入之帳戶的國家/地區雲端 `AuthenticationResult` 。 請注意， `AuthenticationResult` 不會提供您要求權杖之資源的國家/地區雲端特定端點位址。

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

布林值，指出您是否使用 Microsoft Identity broker 相容的 broker 重新導向 URI。 `false`如果您不想要在應用程式中使用訊息代理程式，請將設定為。

如果您使用的「AAD 授權單位」設定為「物件」 `"MicrosoftPersonalAccount"` ，則不會使用訊息代理程式。

### <a name="http"></a>http

設定 HTTP 超時的全域設定，例如：

| 屬性 | 資料類型 | 必要 | 備註 |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | 否 | 時間（毫秒） |
| `read_timeout` | int | 否 | 時間（毫秒） |

### <a name="logging"></a>logging

下列全域設定適用于記錄：

| 屬性 | 資料類型  | 必要 | 備註 |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | 否 | 是否要發出個人資料 |
| `log_level`   | 字串 | No | 要輸出的記錄訊息。 支援的記錄層級包括 `ERROR` 、、 `WARNING` `INFO` 和 `VERBOSE` 。 |
| `logcat_enabled` | boolean | 否 | 是否除了記錄介面外，還會輸出到記錄貓 |

### <a name="account_mode"></a>account_mode

指定可在應用程式中一次使用的帳戶數目。 可能的值包括：

- `MULTIPLE` (預設值)
- `SINGLE`

`PublicClientApplication`使用不符合這項設定的帳戶模式來建立時，將會導致例外狀況。

如需單一和多個帳戶之間差異的詳細資訊，請參閱 [單一和多個帳戶應用程式](single-multi-account.md)。

### <a name="browser_safelist"></a>browser_safelist

與 MSAL 相容的瀏覽器允許清單。 這些瀏覽器會正確處理重新導向至自訂意圖。 您可以新增至此清單。 預設會在以下顯示的預設設定中提供。
``
## <a name="the-default-msal-configuration-file"></a>預設 MSAL 設定檔

MSAL 隨附的預設 MSAL 設定如下所示。 您可以在 [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)上看到最新版本。

這項設定是由您提供的值所補充。 您提供的值會覆寫預設值。

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>基本設定範例

下列範例說明指定用戶端識別碼、重新導向 URI、是否註冊 broker 重新導向的基本設定，以及授權清單。

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>如何使用設定檔

1. 建立設定檔。 建議您在中建立自訂的設定檔 `res/raw/auth_config.json` 。 但您可以將它放在您想要的任何位置。
2. 告訴 MSAL 在您建立時要在哪裡尋找您的設定 `PublicClientApplication` 。 例如：

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
