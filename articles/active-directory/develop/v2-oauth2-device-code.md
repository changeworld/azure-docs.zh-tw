---
title: OAuth 2.0 裝置程式碼流程 |Azure
titleSuffix: Microsoft identity platform
description: 不使用瀏覽器登入使用者。 使用裝置授權授與，建立內嵌和無瀏覽器的驗證流程。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a0677603f02b429c269c0f93ef348b2b1d717a9f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82689770"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft 身分識別平臺和 OAuth 2.0 裝置授權授與流程

Microsoft 身分識別平臺支援「[裝置授權授](https://tools.ietf.org/html/rfc8628)與」，可讓使用者登入受輸入限制的裝置，例如智慧型電視、IoT 裝置或印表機。  若要啟用此流程，裝置會讓使用者在其他裝置的瀏覽器中造訪網頁以進行登入。  使用者登入後，裝置就能夠視需要取得存取權杖和重新整理權杖。

本文說明如何在您的應用程式中直接針對通訊協定進行程式設計。  可能的話，建議您改為使用支援的 Microsoft 驗證程式庫 (MSAL)，以[取得權杖並呼叫受保護的 Web API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另請參閱[使用 MSAL 的範例應用程式](sample-v2-code.md)。

## <a name="protocol-diagram"></a>通訊協定圖表

整個裝置代碼流程看起來類似下圖。 我們會在本文稍後說明每個步驟。

![裝置代碼流程](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>裝置授權要求

用戶端必須先向驗證伺服器確認用來起始驗證的裝置和使用者代碼。 用戶端從 `/devicecode` 端點收集此要求。 在此要求中，用戶端也應包含需要向使用者取得的權限。 自發出要求算起，使用者只有 15 分鐘可以登入 (`expires_in` 的一般值)，因此請在使用者已準備好登入的情況下，才發出要求。

> [!TIP]
> 嘗試在 Postman 中執行這項要求！
> [![嘗試在 Postman 中執行此要求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| 參數 | 條件 | 描述 |
| --- | --- | --- |
| `tenant` | 必要 | 可以是/common、/consumers 或/organizations。  它也可以是您想要以 GUID 或易記名稱格式來要求許可權的目錄租使用者。  |
| `client_id` | 必要 | [Azure 入口網站 - 應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗指派給您應用程式的**應用程式 (用戶端) 識別碼**。 |
| `scope` | 建議 | 您要使用者同意的 [範圍](v2-permissions-and-consent.md) 空格分隔清單。  |

### <a name="device-authorization-response"></a>裝置授權回應

成功的回應會是 JSON 物件，其中包含允許使用者登入的必要資訊。

| 參數 | [格式] | 描述 |
| ---              | --- | --- |
|`device_code`     | String | 用來在用戶端與授權伺服器之間驗證工作階段的長字串。 用戶端會使用此參數來向授權伺服器要求存取權杖。 |
|`user_code`       | String | 向使用者顯示的短字串，用來識別次要裝置上的工作階段。|
|`verification_uri`| URI | 為了執行登入程序，使用者應使用 `user_code` 查看的 URI。 |
|`expires_in`      | int | `device_code` 和 `user_code` 到期之前的秒數。 |
|`interval`        | int | 在輪詢要求之間用戶端應等待的秒數。 |
| `message`        | String | 人類看得懂的字串，其中包含使用者的指示。 在 `?mkt=xx-XX` 形式的要求中加入 ** 查詢參數**、填寫適當的語言文化代碼，即可進行當地語系化。 |

> [!NOTE]
> 此時 `verification_uri_complete` 不會包含或支援 [回應] 欄位。  我們提過這是因為如果您閱讀[標準](https://tools.ietf.org/html/rfc8628)，就 `verification_uri_complete` 會將其列為裝置程式碼流程標準的選擇性部分。

## <a name="authenticating-the-user"></a>驗證使用者

收到和之後 `user_code` ， `verification_uri` 用戶端會向使用者顯示這些專案，指示他們使用其行動電話或電腦瀏覽器登入。

如果使用者使用個人帳戶（在/common 或/consumers 上）進行驗證，系統會要求他們重新登入，以便將驗證狀態傳送至裝置。  系統也會要求他們提供同意，以確保他們知道所授與的許可權。  這不適用於用於驗證的公司或學校帳戶。

使用者在 `verification_uri`進行驗證時，用戶端應使用 `device_code` 輪詢 `/token` 端點，以取得要求的權杖。

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| 參數 | 必要 | 說明|
| -------- | -------- | ---------- |
| `tenant`  | 必要 | 初始要求中使用的相同租使用者或租使用者別名。 |
| `grant_type` | 必要 | 必須是 `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | 必要 | 必須符合初始要求中使用的 `client_id`。 |
| `device_code`| 必要 | 裝置授權要求傳回的 `device_code`。  |

### <a name="expected-errors"></a>預期的錯誤

裝置程式碼流程是一種輪詢通訊協定，因此您的用戶端在使用者完成驗證之前，必須預期會收到錯誤。

| 錯誤 | 描述 | 用戶端動作 |
| ------ | ----------- | -------------|
| `authorization_pending` | 使用者尚未完成驗證，但尚未取消流程。 | 經過至少 `interval` 秒後，重複要求流程。 |
| `authorization_declined` | 終端使用者拒絕了授權要求。| 停止輪詢，並還原到未驗證的狀態。  |
| `bad_verification_code`| `device_code`無法辨識傳送至 `/token` 端點的。 | 確認用戶端是否在要求中傳送正確的 `device_code`。 |
| `expired_token` | 已經過了至少 `expires_in` 秒，`device_code` 也無法再進行驗證。 | 停止輪詢，並還原到未驗證的狀態。 |

### <a name="successful-authentication-response"></a>成功的驗證回應

成功的權杖回應看起來會像這樣：

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| 參數 | [格式] | 描述 |
| --------- | ------ | ----------- |
| `token_type` | String| 一律為承載者 (Bearer)。 |
| `scope` | 空格分隔的字串 | 如果傳回存取權杖，此參數會列出存取權杖適用的範圍。 |
| `expires_in`| int | 所含存取權杖到期前的時間 (以秒為單位)。 |
| `access_token`| 不透明字串 | 針對已要求的[範圍](v2-permissions-and-consent.md)發出。  |
| `id_token`   | JWT | 原始 `scope` 參數包含 `openid` 範圍時發出。  |
| `refresh_token` | 不透明字串 | 原始 `scope` 參數包含 `offline_access` 時發出。  |

您可以使用重新整理權杖來取得新的存取權杖，並使用[OAuth 程式碼流程檔](v2-oauth2-auth-code-flow.md#refresh-the-access-token)中記載的相同流程來重新整理權杖。
