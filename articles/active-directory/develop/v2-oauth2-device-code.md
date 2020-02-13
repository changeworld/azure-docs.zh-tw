---
title: OAuth 2.0 裝置程式碼流程 |Azure
titleSuffix: Microsoft identity platform
description: 不使用瀏覽器登入使用者。 使用裝置授權授與，建立內嵌和無瀏覽器的驗證流程。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b45ba0c0b417be9cf308fedbb7fad2f6ad5fceaf
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159726"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft 身分識別平臺和 OAuth 2.0 裝置授權授與流程

Microsoft 身分識別平臺支援「[裝置授權授](https://tools.ietf.org/html/rfc8628)與」，可讓使用者登入受輸入限制的裝置，例如智慧型電視、IoT 裝置或印表機。  若要啟用此流程，裝置會讓使用者在其他裝置的瀏覽器中瀏覽網頁，以執行登入程序。  使用者登入後，裝置可取得所需的存取權杖和重新整理權杖。  

本文說明如何在您的應用程式中直接針對通訊協定進行程式設計。  可能的話，建議您改用支援的 Microsoft 驗證程式庫（MSAL）來[取得權杖，並呼叫受保護的 Web api](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另請參閱[使用 MSAL 的範例應用程式](sample-v2-code.md)。

> [!NOTE]
> Microsoft 身分識別平臺端點不支援所有 Azure Active Directory 案例和功能。 若要判斷您是否應該使用 Microsoft 身分識別平臺端點，請參閱[microsoft 身分識別平臺限制](active-directory-v2-limitations.md)。

## <a name="protocol-diagram"></a>通訊協定圖表

整個用戶端認證流程與下圖相似。 我們會在本文稍後說明每個步驟。

![裝置程式碼流程](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>裝置授權要求

用戶端必須先向驗證服務器檢查用來起始驗證的裝置和使用者程式碼。 用戶端從 `/devicecode` 端點收集此要求。 在此要求中，用戶端也應一併附上必須向使用者索取的權限。 自發出要求算起，使用者只有 15 分鐘可以登入 (`expires_in` 的一般值)，因此請在使用者已準備好登入的情況下，才發出要求。

> [!TIP]
> 嘗試在 Postman 中執行這項要求！
> [![嘗試在 Postman 中執行此要求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| 參數 | 條件 | 描述 |
| --- | --- | --- |
| `tenant` | 必要項 | 可以是/common、/consumers 或/organizations。  它也可以是您想要以 GUID 或易記名稱格式來要求許可權的目錄租使用者。  |
| `client_id` | 必要項 | **應用程式（用戶端）識別碼**， [Azure 入口網站](https://go.microsoft.com/fwlink/?linkid=2083908)指派給您應用程式的應用程式註冊體驗。 |
| `scope` | 建議 | 您要使用者同意的 [範圍](v2-permissions-and-consent.md) 空格分隔清單。  |

### <a name="device-authorization-response"></a>裝置授權回應

成功的回應會是一個 JSON 物件，其中包含允許使用者登入的所需資訊。  

| 參數 | [格式] | 描述 |
| ---              | --- | --- |
|`device_code`     | String | 長字串，可用於驗證用戶端與授權伺服器之間的工作階段。 用戶端會使用此參數來向授權伺服器要求存取權杖。 |
|`user_code`       | String | 向使用者顯示的簡短字串，用來識別次要裝置上的會話。|
|`verification_uri`| URI | 為了執行登入程序，使用者應使用 `user_code` 查看的 URI。 |
|`expires_in`      | int | `device_code` 和 `user_code` 到期之前的秒數。 |
|`interval`        | int | 用戶端在輪詢要求之間應等待的秒數。 |
| `message`        | String | 人類看得懂的字串，包含使用者說明。 在 **形式的要求中加入** 查詢參數`?mkt=xx-XX`、填寫適當的語言文化代碼，即可進行當地語系化。 |

> [!NOTE]
> 此時不會包含或支援 [`verification_uri_complete` 回應] 欄位。  我們提過這是因為如果您閱讀[標準](https://tools.ietf.org/html/rfc8628)，就會看到 `verification_uri_complete` 列為裝置程式碼流程標準的選擇性部分。

## <a name="authenticating-the-user"></a>驗證使用者

收到 `user_code` 並 `verification_uri`之後，用戶端會向使用者顯示這些專案，指示他們使用其行動電話或電腦瀏覽器登入。

如果使用者使用個人帳戶（在/common 或/consumers 上）進行驗證，系統會要求他們重新登入，以便將驗證狀態傳送至裝置。  系統也會要求他們提供同意，以確保他們知道所授與的許可權。  這不適用於用於驗證的公司或學校帳戶。 

使用者在 `verification_uri`進行驗證時，用戶端應使用 `/token` 輪詢 `device_code` 端點，以取得要求的權杖。

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| 參數 | 必要項 | 描述|
| -------- | -------- | ---------- |
| `tenant`  | 必要項 | 初始要求中使用的相同租使用者或租使用者別名。 | 
| `grant_type` | 必要項 | 必須是 `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | 必要項 | 必須符合初始要求中使用的 `client_id`。 |
| `device_code`| 必要項 | 裝置授權要求傳回的 `device_code`。  |

### <a name="expected-errors"></a>預期的錯誤

裝置程式碼流程是一種輪詢通訊協定，因此您的用戶端在使用者完成驗證之前，必須預期會收到錯誤。  

| 錯誤 | 描述 | 用戶端動作 |
| ------ | ----------- | -------------|
| `authorization_pending` | 使用者尚未完成驗證，但尚未取消流程。 | 經過至少 `interval` 秒後，重複要求流程。 |
| `authorization_declined` | 終端使用者拒絕了授權要求。| 停止輪詢，並還原到未驗證的狀態。  |
| `bad_verification_code`| 無法辨識傳送至 `/token` 端點的 `device_code`。 | 確認用戶端是否在要求中傳送正確的 `device_code`。 |
| `expired_token` | 已經過了至少 `expires_in` 秒，`device_code` 也無法再進行驗證。 | 停止輪詢，並還原到未驗證的狀態。 |   

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

您可以使用重新整理權杖來取得新的存取權杖，並使用[OAuth 程式碼流程檔](v2-oauth2-auth-code-flow.md#refresh-the-access-token)中記載的相同流程來重新整理權杖。  
