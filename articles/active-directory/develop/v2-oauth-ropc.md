---
title: 使用資源擁有者密碼認證認證的授予登入 |蔚藍
titleSuffix: Microsoft identity platform
description: 使用資源擁有者密碼認證 (ROPC) 授予支援無瀏覽器身份驗證流。
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
ms.openlocfilehash: 828bdab26684b29d664ea42d0b36f475c7872a80
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309455"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>微軟識別平臺和 OAuth 2.0 資源擁有者密碼認證

Microsoft 身份平臺支援[OAuth 2.0 資源擁有者密碼認證認證 (ROPC) 授予](https://tools.ietf.org/html/rfc6749#section-4.3),它允許應用程式透過直接處理使用者的密碼來登入使用者。  本文介紹如何直接針對應用程式中的協議進行程式設計。  如果可能,我們建議您使用受支援的 Microsoft 身份驗證庫 (MSAL) 來[獲取權杖並調用安全的 Web API。](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)  也看看[使用MSAL的範例應用程式](sample-v2-code.md)。

> [!WARNING]
> Microsoft 建議您_不要_使用 ROPC 流。 在大多數情況下,可以使用和推薦更安全的替代方案。 此流要求對應用程式高度信任,並承擔其他流中不存在的風險。 僅當無法使用其他更安全的流時,才應使用此流。

> [!IMPORTANT]
>
> * Microsoft 識別平台終結點僅支援 Azure AD 租戶的 ROPC,不支援個人帳戶。 因此您必須使用租用戶特定端點 (`https://login.microsoftonline.com/{TenantId_or_Name}`) 或 `organizations` 端點。
> * 受邀加入 Azure AD 租用戶的個人帳戶無法使用 ROPC。
> * 沒有密碼的帳戶無法透過 ROPC 登入。 針對此案例，建議您改用不同的應用程式流程。
> * 如果使用者必須使用多重要素驗證 (MFA) 來登入應用程式，則會遭到封鎖。
> * [混合識別聯合](/azure/active-directory/hybrid/whatis-fed)方案(例如,用於驗證本地帳戶的 Azure AD 和 ADFS)不支援 ROPC。 如果使用者的整頁重定向到本地標識提供者,Azure AD將無法針對該標識提供程式測試使用者名和密碼。 但是,ROPC 支援[直通身份驗證](/azure/active-directory/hybrid/how-to-connect-pta)。

## <a name="protocol-diagram"></a>通訊協定圖表

下圖顯示 ROPC 流程。

![顯示資源擁有者密碼認證串流的圖表](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>授權要求

ROPC 流是單個請求:它將客戶端標識和使用者的憑據發送到 IDP,然後接收權杖作為回報。 用戶端這麼做之前，必須先要求使用者的電子郵件地址 (UPN) 和密碼。 要求成功之後，用戶端應該就會立即從記憶體安全地釋出使用者的認證。 用戶端永遠不得儲存這些認證。

> [!TIP]
> 嘗試在 Postman 中執行這項要求！
> [![試著在郵遞員中執行此要求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required. 

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| 參數 | 條件 | 描述 |
| --- | --- | --- |
| `tenant` | 必要 | 您想要將使用者登入的目標目錄租用戶。 這可以採用 GUID 或易記名稱格式。 此參數無法設為 `common` 或 `consumers`，但可設定為 `organizations`。 |
| `client_id` | 必要 | Azure 門戶的應用程式(用戶端)ID - 分配給應用[的應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁。 | 
| `grant_type` | 必要 | 必須設為 `password`。 |
| `username` | 必要 | 使用者的電子郵件地址。 |
| `password` | 必要 | 使用者的密碼。 |
| `scope` | 建議 | 以空格分隔的[範圍](v2-permissions-and-consent.md)清單或應用程式所需的權限。 在互動式流中,管理員或用戶必須提前同意這些作用域。 |
| `client_secret`| 有時需要 | 如果應用是公共用戶端,則無法包括`client_secret``client_assertion`或。  如果應用是機密用戶端,則必須包括它。 | 
| `client_assertion` | 有時需要 | 使用憑證產生的不同`client_secret`形式的 。  有關詳細資訊,請參閱[憑證認證](active-directory-certificate-credentials.md)。 | 

### <a name="successful-authentication-response"></a>成功驗證回應

下面的範例顯示了成功的權杖回應:

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
| `token_type` | String | 一律設定為 `Bearer`。 |
| `scope` | 空格分隔的字串 | 如果傳回了存取權杖，此參數會列出存取權杖的有效範圍。 |
| `expires_in`| int | 所含存取權杖的有效時間長度 (秒數)。 |
| `access_token`| 不透明字串 | 針對已要求的[範圍](v2-permissions-and-consent.md)發出。 |
| `id_token` | JWT | 原始 `scope` 參數包含 `openid` 範圍時發出。 |
| `refresh_token` | 不透明字串 | 原始 `scope` 參數包含 `offline_access` 時發出。 |

您可以使用 [OAuth 程式碼流程文件](v2-oauth2-auth-code-flow.md#refresh-the-access-token)中所述的同一個流程，透過重新整理權杖來取得新的存取權杖和重新整理權杖。

### <a name="error-response"></a>錯誤回應

如果使用者未提供正確的使用者名稱或密碼，或用戶端未收到所要求的同意，驗證將會失敗。

| 錯誤 | 描述 | 用戶端動作 |
|------ | ----------- | -------------|
| `invalid_grant` | 驗證失敗 | 認證不正確，或用戶端沒有同意所要求的範圍。 如果未授予作用的功能,將傳`consent_required`回錯誤 。 如果發生這種情況，用戶端應使用 WebView 或瀏覽器將使用者傳送至互動式提示。 |
| `invalid_request` | 要求未正確建構 | 在`/common`或`/consumers`身份驗證上下文中不支援授予類型。  而是`/organizations`使用或租戶 ID。 |

## <a name="learn-more"></a>深入了解

* 使用[範例主控台應用程式](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)自行試用 ROPC。
* 要確定是否應使用 v2.0 終結點,請閱讀有關[Microsoft 識別平臺限制](active-directory-v2-limitations.md)。
