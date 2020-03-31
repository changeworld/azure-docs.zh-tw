---
title: 安卓 MSAL 設定檔 |蔚藍
titleSuffix: Microsoft identity platform
description: Android 微軟身份驗證庫 （MSAL） 設定檔的概述，該檔表示 Azure 活動目錄中的應用程式佈建。
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 9e35ba5a3f3705a52e80262da9bbfbfda489bf83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050369"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>安卓微軟認證庫設定檔

Android Microsoft 身份驗證庫 （MSAL） 附帶一個[預設配置 JSON 檔](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)，您可以自訂該檔，以便為預設許可權、要使用的許可權等定義公共用戶端應用的行為。

本文將説明您瞭解設定檔中的各種設置以及如何指定要在基於 MSAL 的應用中使用的設定檔。

## <a name="configuration-settings"></a>組態設定

### <a name="general-settings"></a>一般設定

| 屬性 | 資料類型 | 必要 | 注意 |
|-----------|------------|-------------|-------|
| `client_id` | String | 是 | 應用程式在[應用程式註冊頁](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)中的用戶端 ID |
| `redirect_uri`   | String | 是 | 應用從[應用程式註冊頁面](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)重定向 URI |
| `authorities` | 清單\<頒發機構> | 否 | 應用所需的許可權清單 |
| `authorization_user_agent` | 授權代理（枚舉） | 否 | 可能的值： `DEFAULT` `BROWSER`， ，`WEBVIEW` |
| `http` | Http 配置 | 否 | 配置`HttpUrlConnection``connect_timeout`和`read_timeout` |
| `logging` | 日誌記錄配置 | 否 | 指定日誌記錄詳細資訊級別。 `pii_enabled`可選配置包括： ，它採用布林值，和`log_level`，它採用`ERROR` `WARNING`， `INFO` `VERBOSE`或 。 |

### <a name="client_id"></a>client_id

註冊應用程式時創建的用戶端 ID 或應用 ID。

### <a name="redirect_uri"></a>redirect_uri

註冊應用程式時註冊的重定向 URI。 如果重定向 URI 是代理應用，請參閱[公共用戶端應用重定向 URI](msal-client-application-configuration.md#redirect-uri-for-public-client-apps)以確保對代理應用使用正確的重定向 URI 格式。

### <a name="authorities"></a>當局

您知道和信任的頒發機構清單。 除了此處列出的許可權外，MSAL 還詢問 Microsoft 獲取 Microsoft 已知的雲和許可權清單。 在此許可權清單中，指定許可權的類型和任何其他可選參數，如`"audience"`，這些參數應根據應用的註冊與應用的受眾保持一致。 以下是許可權的示例清單：

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken or acquireTokenSilent call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenantId": "contoso.com" // Provide your specific tenant ID here
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

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>將 AAD 許可權&訪問到 Microsoft 標識平臺終結點

| 類型 | 適用對象 | 租用戶識別碼 | Authority_Url | 生成的終結點 | 注意 |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADand個人微軟帳戶 | | | `https://login.microsoftonline.com/common` | `common`是帳戶所在的租戶別名。 例如特定的 Azure 活動目錄租戶或 Microsoft 帳戶系統。 |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | 只有contoso.com中的帳戶才能獲取權杖。 任何已驗證的域或租戶 GUID 都可以用作租戶 ID。 |
| AAD | AzureAD多路組織 | | | `https://login.microsoftonline.com/organizations` | 只有 Azure 活動目錄帳戶可以使用此終結點。 微軟帳戶可以是組織的成員。 要使用 Microsoft 帳戶獲取組織中的資源的權杖，請指定需要權杖的組織租戶。 |
| AAD | 個人微軟帳戶 | | | `https://login.microsoftonline.com/consumers` | 只有 Microsoft 帳戶才能使用此終結點。 |
| B2C | | | 請參閱結果終結點 | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | 只有contoso.onmicrosoft.com租戶中的帳戶才能獲取權杖。 在此示例中，B2C 策略是授權 URL 路徑的一部分。 |

> [!NOTE]
> 在 MSAL 中無法啟用和禁用許可權驗證。
> 授權已知為開發人員，通過配置指定或 Microsoft 通過中繼資料知道。
> 如果 MSAL 收到向未知機構發出權杖的請求，則`MsalClientException`會導致類型`UnknownAuthority`結果。

#### <a name="authority-properties"></a>許可權屬性

| 屬性 | 資料類型  | 必要 | 注意 |
|-----------|-------------|-----------|--------|
| `type` | String | 是 | 鏡像受眾或帳戶鍵入應用目標。 可能的值： `AAD``B2C` |
| `audience` | Object | 否 | 僅適用于類型*`AAD`。 指定應用的目標標識。 使用應用註冊中的值 |
| `authority_url` | String | 是 | 僅在類型 *`B2C`時才需要。 指定應用應使用的許可權 URL 或策略  |
| `default` | boolean | 是 | 指定一`"default":true`個或多個頒發機構時，需要單個。 |

#### <a name="audience-properties"></a>受眾屬性

| 屬性 | 資料類型  | 必要 | 注意 |
|-----------|-------------|------------|-------|
| `type` | String | 是 | 指定應用要定位的受眾。 可能的值： `AzureADandPersonalMicrosoftAccount` `PersonalMicrosoftAccount`、 `AzureADMultipleOrgs`、 、`AzureADMyOrg` |
| `tenant_id` | String | 是 | 僅在 時`"type":"AzureADMyOrg"`需要。 其他`type`值可選。 這可以是租戶域，如`contoso.com`， 或租戶 ID，如`72f988bf-86f1-41af-91ab-2d7cd011db46` |

### <a name="authorization_user_agent"></a>authorization_user_agent

指示在登錄帳戶或授權訪問資源時，是使用嵌入 Webview 還是設備上的預設瀏覽器。

可能的值：
- `DEFAULT`：首選系統瀏覽器。 如果設備上沒有瀏覽器，則使用嵌入的 Web 視圖。
- `WEBVIEW`：使用嵌入式 Web 視圖。
- `BROWSER`：使用設備上的預設瀏覽器。

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

對於支援多個國家雲的用戶端，請`true`指定 。 然後，Microsoft 標識平臺將在授權和權杖兌換期間自動重定向到正確的國家雲。 您可以通過檢查與 關聯的許可權來確定登錄帳戶的國家雲`AuthenticationResult`。 請注意，`AuthenticationResult`不提供請求權杖的資源的特定于雲的國家終結點位址。

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

一個布林，指示您是否使用相容 Microsoft 標識代理的代理重定向 URI。 如果不想`false`在應用中使用代理，請設置為"已設置為"

如果您使用的 AAD 頒發機構將訪問物件設置為`"MicrosoftPersonalAccount"`，則不會使用代理。

### <a name="http"></a>http

為 HTTP 超時配置全域設置，例如：

| 屬性 | 資料類型 | 必要 | 注意 |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | 否 | 以毫秒為單位的時間 |
| `read_timeout` | int | 否 | 以毫秒為單位的時間 |

### <a name="logging"></a>logging

以下全域設置用於日誌記錄：

| 屬性 | 資料類型  | 必要 | 注意 |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | 否 | 是否發出個人資料 |
| `log_level`   | boolean | 否 | 要輸出的日誌消息 |
| `logcat_enabled` | boolean | 否 | 除了日誌記錄介面之外，是否輸出到日誌 cat |

### <a name="account_mode"></a>account_mode

指定一次可以在應用中使用多少個帳戶。 可能的值包括：

- `MULTIPLE` (預設值)
- `SINGLE`

使用`PublicClientApplication`與此設置不匹配的帳戶模式構造將導致異常。

有關單個或多個帳戶之間的差異的詳細資訊，請參閱[單個或多個帳戶應用](single-multi-account.md)。

### <a name="browser_safelist"></a>browser_safelist

與 MSAL 相容的瀏覽器的允許清單。 這些瀏覽器正確處理重定向到自訂意圖。 您可以添加到此清單。 預設值在下面所示的預設配置中提供。
``
## <a name="the-default-msal-configuration-file"></a>預設 MSAL 設定檔

MSAL 附帶的預設 MSAL 配置如下所示。 您可以在[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)上看到最新版本。

此配置由您提供的值進行補充。 您提供的值將覆蓋預設值。

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
## <a name="example-basic-configuration"></a>基本配置示例

下面的示例演示了指定用戶端 ID、重定向 URI、是否註冊了代理重定向以及許可權清單的基本配置。

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

1. 創建設定檔。 我們建議您在 中`res/raw/auth_config.json`創建自訂設定檔。 但是你可以把它放在任何你想去的地方。
2. 在構造 時告訴 MSAL 在何處查找配置`PublicClientApplication`。 例如：

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
