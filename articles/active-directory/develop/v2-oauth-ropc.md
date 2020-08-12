---
title: 使用資源擁有者密碼認證授與來登入 | Azure
titleSuffix: Microsoft identity platform
description: 使用資源擁有者密碼認證 (ROPC) 授與支援無瀏覽器的驗證流程。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 24d50635efb4d7fe18db9836311cf0a85dfcc734
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118615"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Microsoft 身分識別平台和 OAuth 2.0 資源擁有者密碼認證授與

Microsoft 身分識別平台支援 [OAuth 2.0 資源擁有者密碼認證 (ROPC) 授與](https://tools.ietf.org/html/rfc6749#section-4.3) \(英文\)，可讓應用程式藉由直接處理其密碼來登入使用者。  此文章說明如何在您的應用程式中直接針對通訊協定進行程式設計。  可能的話，建議您改為使用支援的 Microsoft 驗證程式庫 (MSAL)，以[取得權杖並呼叫受保護的 Web API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另請參閱[使用 MSAL 的範例應用程式](sample-v2-code.md)。

> [!WARNING]
> Microsoft 建議您「不要」使用 ROPC 流程。 在大部分的情況下，有更安全的可用替代方案，也建議使用這些方案。 此流程在應用程式中需要非常高的信任度，而且帶有未在其他流程中出現的風險。 您應該只在其他更安全的流程都無法使用時，才使用此流程。

> [!IMPORTANT]
>
> * Microsoft 身分識別平台端點僅針對 Azure AD 租用戶支援 ROPC，而不會針對個人帳戶提供支援。 因此您必須使用租用戶特定端點 (`https://login.microsoftonline.com/{TenantId_or_Name}`) 或 `organizations` 端點。
> * 受邀加入 Azure AD 租用戶的個人帳戶無法使用 ROPC。
> * 沒有密碼的帳戶無法透過 ROPC 登入。 針對此案例，建議您改用不同的應用程式流程。
> * 如果使用者必須使用[多重要素驗證 (MFA)](../authentication/concept-mfa-howitworks.md) 來登入應用程式，則會遭到封鎖。
> * [混合式身分識別同盟](../hybrid/whatis-fed.md)案例 (例如，用來驗證內部部署帳戶的 Azure AD 和 ADFS) 中不支援 ROPC。 如果以整頁方式將使用者重新導向至內部部署身分識別提供者，Azure AD 就無法對該身分識別提供者測試使用者名稱和密碼。 不過，ROPC 支援[傳遞驗證](../hybrid/how-to-connect-pta.md)。

## <a name="protocol-diagram"></a>通訊協定圖表

下圖顯示 ROPC 流程。

![顯示資源擁有者密碼認證流程的圖表](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>授權要求

ROPC 流程是單一要求：其會將用戶端識別碼和使用者的認證傳送至 IDP，然後接收傳回的權杖。 用戶端這麼做之前，必須先要求使用者的電子郵件地址 (UPN) 和密碼。 要求成功之後，用戶端應該就會立即從記憶體安全地釋出使用者的認證。 用戶端永遠不得儲存這些認證。

> [!TIP]
> 嘗試在 Postman 中執行這項要求！
> [![嘗試在 Postman 中執行此要求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```HTTP
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
| `client_id` | 必要 | [Azure 入口網站 - 應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面指派給您應用程式的應用程式 (用戶端) 識別碼。 |
| `grant_type` | 必要 | 必須設為 `password`。 |
| `username` | 必要 | 使用者的電子郵件地址。 |
| `password` | 必要 | 使用者的密碼。 |
| `scope` | 建議 | 以空格分隔的[範圍](v2-permissions-and-consent.md)清單或應用程式所需的權限。 在互動式流程中，管理員或使用者必須事先同意這些範圍。 |
| `client_secret`| 有時需要 | 如果您的應用程式是公用用戶端，則不能包含 `client_secret` 或 `client_assertion`。  如果應用程式是機密用戶端，則必須包含其。 |
| `client_assertion` | 有時需要 | 不同形式的 `client_secret` (使用憑證產生)。  如需詳細資訊，請參閱[憑證認證](active-directory-certificate-credentials.md)。 |

### <a name="successful-authentication-response"></a>成功驗證回應

下列範例顯示成功的權杖回應：

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
| `invalid_grant` | 驗證失敗 | 認證不正確，或用戶端沒有同意所要求的範圍。 如果未授與範圍，將會傳回 `consent_required` 錯誤。 如果發生這種情況，用戶端應使用 WebView 或瀏覽器將使用者傳送至互動式提示。 |
| `invalid_request` | 要求未正確建構 | `/common` 或 `/consumers` 驗證內容不支援授與類型。  請改為使用 `/organizations` 或租用戶識別碼。 |

## <a name="learn-more"></a>深入了解

* 使用[範例主控台應用程式](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)自行試用 ROPC。
* 若要判斷您是否應該使用 v2.0 端點，請參閱 [Microsoft 身分識別平台限制](../azuread-dev/azure-ad-endpoint-comparison.md)。