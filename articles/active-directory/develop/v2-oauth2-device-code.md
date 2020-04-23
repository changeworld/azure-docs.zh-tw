---
title: OAuth 2.0 設備代碼流 |蔚藍
titleSuffix: Microsoft identity platform
description: 在沒有流覽器的情況下登錄使用者。 使用設備授權授予構建嵌入式身份驗證流和無瀏覽器身份驗證流。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 42f3ca233597d0fbc31ce656bd856875e873e3c2
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868474"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>微軟身份平台和 OAuth 2.0 裝置授權授予流

Microsoft 標識平台支援[設備授權授予](https://tools.ietf.org/html/rfc8628),它允許使用者登錄到受輸入約束的設備,如智慧電視、IoT 設備或印表機。  若要啟用此流程，裝置會讓使用者在其他裝置的瀏覽器中瀏覽網頁，以執行登入程序。  使用者登入後，裝置可取得所需的存取權杖和重新整理權杖。

本文介紹如何直接針對應用程式中的協議進行程式設計。  如果可能,我們建議您使用受支援的 Microsoft 身份驗證庫 (MSAL) 來[獲取權杖並調用安全的 Web API。](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)  也看看[使用MSAL的範例應用程式](sample-v2-code.md)。

## <a name="protocol-diagram"></a>通訊協定圖表

整個用戶端認證流程與下圖相似。 我們會在本文稍後說明每個步驟。

![裝置程式碼流程](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>裝置授權要求

用戶端必須首先與身份驗證伺服器查詢用於啟動身份驗證的設備用戶代碼。 用戶端從 `/devicecode` 端點收集此要求。 在此要求中，用戶端也應一併附上必須向使用者索取的權限。 自發出要求算起，使用者只有 15 分鐘可以登入 (`expires_in` 的一般值)，因此請在使用者已準備好登入的情況下，才發出要求。

> [!TIP]
> 嘗試在 Postman 中執行這項要求！
> [![試著在郵遞員中執行此要求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| 參數 | 條件 | 描述 |
| --- | --- | --- |
| `tenant` | 必要 | 可以是 /公共、/消費者或 /組織。  它還可以是要以 GUID 或友好名稱格式請求許可權的目錄租戶。  |
| `client_id` | 必要 | [Azure 門戶和應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗**的應用程式(用戶端)ID**分配給應用。 |
| `scope` | 建議 | 您要使用者同意的 [範圍](v2-permissions-and-consent.md) 空格分隔清單。  |

### <a name="device-authorization-response"></a>裝置授權回應

成功的回應會是一個 JSON 物件，其中包含允許使用者登入的所需資訊。

| 參數 | [格式] | 描述 |
| ---              | --- | --- |
|`device_code`     | String | 長字串，可用於驗證用戶端與授權伺服器之間的工作階段。 用戶端使用此參數從授權伺服器請求訪問權杖。 |
|`user_code`       | String | 向使用者顯示用於標識輔助設備上的作業階段的短字串。|
|`verification_uri`| URI | 為了執行登入程序，使用者應使用 `user_code` 查看的 URI。 |
|`expires_in`      | int | `device_code` 和 `user_code` 到期之前的秒數。 |
|`interval`        | int | 用戶端在輪詢要求之間應等待的秒數。 |
| `message`        | String | 人類看得懂的字串，包含使用者說明。 在 `?mkt=xx-XX` 形式的要求中加入 ** 查詢參數**、填寫適當的語言文化代碼，即可進行當地語系化。 |

> [!NOTE]
> 此時`verification_uri_complete`不包含或支援回應欄位。  我們提到這一點是因為,如果您[standard](https://tools.ietf.org/html/rfc8628)閱讀了`verification_uri_complete`作為設備代碼流標準的可選部分列出的標準。

## <a name="authenticating-the-user"></a>驗證使用者

收到`user_code``verification_uri`和 後,用戶端向用戶顯示這些,指示他們使用行動電話或PC瀏覽器登錄。

如果使用者使用個人帳戶(在 /common 或 /消費者上)進行身份驗證,系統將要求他們重新登錄,以便將身份驗證狀態傳輸到設備。  他們也將被要求提供同意,以確保他們知道被授予的許可權。  這不適用於用於進行身份驗證的工作或學校帳戶。

使用者在 `verification_uri`進行驗證時，用戶端應使用 `device_code` 輪詢 `/token` 端點，以取得要求的權杖。

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| 參數 | 必要 | 描述|
| -------- | -------- | ---------- |
| `tenant`  | 必要 | 初始請求中使用的同一租戶或租戶別名。 |
| `grant_type` | 必要 | 必須是 `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | 必要 | 必須符合初始要求中使用的 `client_id`。 |
| `device_code`| 必要 | 裝置授權要求傳回的 `device_code`。  |

### <a name="expected-errors"></a>預期的錯誤

設備代碼流是輪詢協定,因此客戶端必須在使用者完成身份驗證之前收到錯誤。

| 錯誤 | 描述 | 用戶端動作 |
| ------ | ----------- | -------------|
| `authorization_pending` | 使用者尚未完成身份驗證,但尚未取消流。 | 經過至少 `interval` 秒後，重複要求流程。 |
| `authorization_declined` | 終端使用者拒絕了授權要求。| 停止輪詢，並還原到未驗證的狀態。  |
| `bad_verification_code`| `device_code`發送到終結點`/token`的未識別。 | 確認用戶端是否在要求中傳送正確的 `device_code`。 |
| `expired_token` | 已經過了至少 `expires_in` 秒，`device_code` 也無法再進行驗證。 | 停止輪詢並恢復到未身份驗證的狀態。 |

### <a name="successful-authentication-response"></a>成功驗證回應

成功的權杖回應如下：

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
| `token_type` | String| 一律是「Bearer」。 |
| `scope` | 空格分隔的字串 | 如果傳回了存取權杖，則會列出存取權杖的有效範圍。 |
| `expires_in`| int | 包含的存取權杖須經過多久 (秒數) 才會生效。 |
| `access_token`| 不透明字串 | 針對已要求的[範圍](v2-permissions-and-consent.md)發出。  |
| `id_token`   | JWT | 原始 `scope` 參數包含 `openid` 範圍時發出。  |
| `refresh_token` | 不透明字串 | 原始 `scope` 參數包含 `offline_access` 時發出。  |

您可以使用刷新權杖使用[OAuth 代碼串流文件中](v2-oauth2-auth-code-flow.md#refresh-the-access-token)記錄的相同流獲取新的存取權杖和刷新權杖。
