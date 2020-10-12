---
title: Microsoft 身分識別平臺上的 OAuth 2.0 用戶端認證流程 |蔚藍
description: 使用 Microsoft 身分識別平臺實施 OAuth 2.0 驗證通訊協定來建立 web 應用程式。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 7/27/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e5fe8e751077bc04850879d27827c197767a81c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87759065"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft 身分識別平臺和 OAuth 2.0 用戶端認證流程

您可以使用 RFC 6749 中指定的 [OAuth 2.0 用戶端認證授與](https://tools.ietf.org/html/rfc6749#section-4.4) (有時稱為「雙方 OAuth」**，透過使用應用程式識別碼來存取 Web 主控資源。 這種類型的授與通常用於必須在背景中執行的伺服器對伺服器互動，無須與使用者即時互動。 這些類型的應用程式通常稱為「 *守護* 程式」或「 *服務帳戶*」。

本文說明如何在您的應用程式中直接針對通訊協定進行程式設計。 可能的話，建議您改為使用支援的 Microsoft 驗證程式庫 (MSAL)，以[取得權杖並呼叫受保護的 Web API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另請參閱[使用 MSAL 的範例應用程式](sample-v2-code.md)。

OAuth 2.0 用戶端認證授與流程可允許 Web 服務 (機密用戶端) 在呼叫其他 Web 服務時，使用自己的認證來驗證，而不是藉由模擬使用者。 在此案例中，用戶端通常是中介層 Web 服務、精靈服務或網站。 對於較高層級的保證，Microsoft 身分識別平台也可讓呼叫服務使用憑證 (而非共用密碼) 做為認證。

在用戶端認證流程中，系統管理員會直接將許可權授與應用程式本身。 當應用程式向資源出示權杖時，資源會強制應用程式本身擁有執行動作的授權，因為沒有任何使用者參與驗證。  本文涵蓋 [授權應用程式呼叫 api](#application-permissions)所需的兩個步驟，以及 [如何取得呼叫該 api 所需的權杖](#get-a-token)。

## <a name="protocol-diagram"></a>通訊協定圖表

整個用戶端認證流程看起來類似下圖。 我們會在本文稍後說明每個步驟。

![顯示用戶端認證流程的圖表](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>取得直接授權

應用程式通常會收到直接授權，而可透過下列兩種方式之一存取資源：

* [在資源中透過存取控制清單 (ACL)](#access-control-lists)
* [在 Azure AD 中透過應用程式權限指派](#application-permissions)

這兩種方法是 Azure AD 中最常見的方法，並建議用於執行用戶端認證流程的用戶端和資源。 資源也可以選擇以其他方法授權其用戶端。 每個資源伺服器都可以選擇最適合其應用程式的方法。

### <a name="access-control-lists"></a>存取控制清單

資源提供者可能會根據它所知的應用程式 (用戶端) 識別碼清單，強制執行授權檢查並授與特定層級的存取權。 當資源接收來自 Microsoft 身分識別平臺端點的權杖時，它可以將權杖解碼，並從和宣告中解壓縮用戶端的應用程式識別碼 `appid` `iss` 。 然後，它會將應用程式與它所保有的存取控制清單 (ACL) 進行比較。 各資源之間的 ACL 細微性及方法可能有極大的差異。

常見的使用案例是使用 ACL 來針對 Web 應用程式或 Web API 執行測試。 Web API 可能只會將完整權限中的一部分授與特定用戶端。 若要在 API 上執行端對端測試，請建立測試用戶端，以從 Microsoft 身分識別平臺端點取得權杖，然後將它們傳送至 API。 接著，API 會檢查 ACL 是否有測試用戶端的應用程式識別碼，以便提供 API 完整功能的完整存取權。 如果您使用這類 ACL，請務必不要只驗證呼叫者的 `appid` 值，還要驗證權杖的 `iss` 值是否可信任。

對於需要存取具有個人 Microsoft 帳戶之取用者使用者所擁有資料的精靈和服務帳戶來說，這種授權相當常見。 對於組織所擁有的資料，建議您透過應用程式權限取得必要的授權。

> [!NOTE]
> 為了啟用此以 ACL 為基礎的授權模式，Azure AD 不會要求應用程式取得其他應用程式的權杖，因此，不需要宣告即可發出僅限應用程式的權杖 `roles` 。 公開 Api 的應用程式必須執行許可權檢查，才能接受權杖。

### <a name="application-permissions"></a>應用程式權限

您可以使用 Api 來公開一組 **應用程式許可權**，而不是使用 acl。 應用程式權限是由組織的系統管理員授與應用程式，並且只能用來存取該組織及其員工所擁有的資料。 例如，Microsoft Graph 會公開數個可執行下列操作的應用程式權限︰

* 讀取所有信箱中的郵件
* 讀取和寫入所有信箱中的郵件
* 以任何使用者身分傳送郵件
* 讀取目錄資料

若要搭配使用應用程式許可權與您自己的 API (而不是 Microsoft Graph) ，您必須先在 Azure 入口網站的 API 應用程式註冊中定義範圍，以 [公開 api](quickstart-configure-app-expose-web-apis.md) 。 然後，在用戶端應用程式的應用程式註冊中選取這些許可權， [以設定 API 的存取權](quickstart-configure-app-access-web-apis.md) 。 如果您尚未在 API 的應用程式註冊中公開任何範圍，您將無法在 Azure 入口網站的用戶端應用程式註冊中，指定該 API 的應用程式許可權。

以應用程式進行驗證時， (與使用者) 相反，您無法使用使用者所授與的 *委派許可權* 範圍。 您必須使用應用程式的系統管理員所授與的應用程式許可權（也稱為角色），或透過 web API 預先授權。

如需應用程式許可權的詳細資訊，請參閱 [許可權與同意](v2-permissions-and-consent.md#permission-types)。

#### <a name="recommended-sign-the-user-into-your-app"></a>建議：將使用者登入您的應用程式

通常，當您建置使用應用程式權限的應用程式時，應用程式會需要一個可供系統管理員核准應用程式權限的頁面或檢視。 此頁面可以是應用程式登入流程的一部分、應用程式設定的一部分，或是專用的「連接」流程。 在許多情況下，應用程式只在使用者利用工作或學校 Microsoft 帳戶登入之後顯示此「連接」檢視是很合理的。

如果您將使用者登入您的應用程式，您可以先識別使用者所屬的組織，然後再要求使用者核准應用程式許可權。 雖然這並非絕對必要，但這麼做可協助您為使用者建立更直覺式的體驗。 若要將使用者登入，請遵循我們的 [Microsoft 身分識別平臺通訊協定教學](active-directory-v2-protocols.md)課程。

#### <a name="request-the-permissions-from-a-directory-admin"></a>向目錄管理員要求權限

當您準備好向組織的系統管理員要求許可權時，您可以將使用者重新導向至 Microsoft 身分識別平臺系統 *管理員同意端點*。

> [!TIP]
> 嘗試在 Postman 中執行這項要求！  (使用您自己的應用程式識別碼來獲得最佳結果，教學課程應用程式不會要求有用的許可權。 ) [ ![ 嘗試在 Postman 中執行此要求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

Pro 提示：請嘗試在瀏覽器中貼上下列要求。

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| 參數 | 條件 | 描述 |
| --- | --- | --- |
| `tenant` | 必要 | 您想要要求權限的目錄租用戶。 這可以採用 GUID 或易記名稱格式。 如果您不知道使用者屬於哪個租用戶，而想要讓他們以任何租用戶登入，請使用 `common`。 |
| `client_id` | 必要 | [Azure 入口網站 - 應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗指派給您應用程式的**應用程式 (用戶端) 識別碼**。 |
| `redirect_uri` | 必要 | 您想要傳送回應以供應用程式處理的重新導向 URI。 它必須與您在入口網站中註冊的其中一個重新導向 URI 完全相符，只是它必須是採用 URL 編碼，並且可以有額外的路徑區段。 |
| `state` | 建議 | 要求中包含的值，也會隨權杖回應傳回。 它可以是您所想要內容中的字串。 此狀態用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |

此時，Azure AD 會強制只有租使用者系統管理員可以登入完成要求。 系統會請系統管理員核准您在應用程式註冊入口網站中，為您應用程式要求的所有直接應用程式權限。

##### <a name="successful-response"></a>成功的回應

如果系統管理員為您的應用程式核准權限，則成功的回應看起來會像這樣︰

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 參數 | 描述 |
| --- | --- |
| `tenant` | 將應用程式所要求的權限授與應用程式的目錄租用戶 (採用 GUID 格式)。 |
| `state` | 一個包含在要求中而將一併在權杖回應中傳回的值。 它可以是您所想要內容中的字串。 此狀態用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |
| `admin_consent` | 設定為 [True]****。 |

##### <a name="error-response"></a>錯誤回應

如果系統管理員沒有為您的應用程式核准權限，則失敗的回應看起來會像這樣︰

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 參數 | 描述 |
| --- | --- |
| `error` | 您可用來分類錯誤類型並對錯誤做出反應的錯誤碼字串。 |
| `error_description` | 可協助您識別錯誤根本原因的特定錯誤訊息。 |

從應用程式佈建端點收到成功回應後，您的應用程式便已取得它所要求的直接應用程式權限。 現在，您可以針對您想要的資源要求權杖。

## <a name="get-a-token"></a>取得權杖

取得應用程式的必要授權後，請繼續取得 API 的存取權杖。 若要使用用戶端認證授與來取得權杖，請將 POST 要求傳送至 `/token` Microsoft 身分識別平臺端點：

> [!TIP]
> 嘗試在 Postman 中執行這項要求！  (使用您自己的應用程式識別碼來獲得最佳結果，教學課程應用程式不會要求有用的許可權。 ) [ ![ 嘗試在 Postman 中執行此要求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>第一個案例：含有共用祕密的存取權杖要求

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```Bash
# Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| 參數 | 條件 | 描述 |
| --- | --- | --- |
| `tenant` | 必要 | 應用程式預期要對其執行作業的目錄租用戶 (以 GUID 或網域名稱格式)。 |
| `client_id` | 必要 | 指派給應用程式的應用程式識別碼。 您可以在用來註冊應用程式的入口網站中找到這項資訊。 |
| `scope` | 必要 | 在這個要求中針對 `scope` 參數傳遞的值應該是您所要資源的資源識別碼 (應用程式識別碼 URI)，並附加 `.default` 尾碼。 就 Microsoft Graph 範例而言，值為 `https://graph.microsoft.com/.default`。 <br/>此值會告訴 Microsoft 身分識別平臺端點，其中包含您已為應用程式設定的所有直接應用程式許可權，端點應該會針對與您想要使用之資源相關聯的應用程式，發出權杖。 若要深入了解有關 `/.default` 範圍，請參閱[同意文件](v2-permissions-and-consent.md#the-default-scope)。 |
| `client_secret` | 必要 | 您在應用程式註冊入口網站中為應用程式產生的用戶端密碼。 用戶端密碼必須在傳送之前先進行 URL 編碼。 |
| `grant_type` | 必要 | 必須設為 `client_credentials`。 |

### <a name="second-case-access-token-request-with-a-certificate"></a>第二個案例：含有憑證的存取權杖要求

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| 參數 | 條件 | 描述 |
| --- | --- | --- |
| `tenant` | 必要 | 應用程式預期要對其執行作業的目錄租用戶 (以 GUID 或網域名稱格式)。 |
| `client_id` | 必要 |指派給應用程式的應用程式 (用戶端) 識別碼。 |
| `scope` | 必要 | 在這個要求中針對 `scope` 參數傳遞的值應該是您所要資源的資源識別碼 (應用程式識別碼 URI)，並附加 `.default` 尾碼。 就 Microsoft Graph 範例而言，值為 `https://graph.microsoft.com/.default`。 <br/>此值會通知 Microsoft 身分識別平臺端點，其中包含您已為應用程式設定的所有直接應用程式許可權，它應該會針對與您想要使用之資源相關聯的應用程式，發出權杖。 若要深入了解有關 `/.default` 範圍，請參閱[同意文件](v2-permissions-and-consent.md#the-default-scope)。 |
| `client_assertion_type` | 必要 | 此值必須設為 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`。 |
| `client_assertion` | 必要 | 您必須建立判斷提示 (JSON Web 權杖)，並使用註冊的憑證來簽署，以作為應用程式的認證。 請參閱[憑證認證](active-directory-certificate-credentials.md)，以了解如何註冊您的憑證與判斷提示的格式。|
| `grant_type` | 必要 | 必須設為 `client_credentials`。 |

請注意，在透過共用密碼要求的情況中，參數幾乎相同，不同之處在於使用下列兩個參數來取代 client_secret 參數：client_assertion_type 和 client_assertion。

### <a name="successful-response"></a>成功的回應

成功的回應看起來會像這樣：

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| 參數 | 描述 |
| --- | --- |
| `access_token` | 所要求的存取權杖。 應用程式可以使用此權杖來對受保護的資源（例如 web API）進行驗證。 |
| `token_type` | 表示權杖類型值。 Microsoft 身分識別平台唯一支援的類型是 `bearer`。 |
| `expires_in` | 存取權杖的有效時間長度 (以秒為單位)。 |

### <a name="error-response"></a>錯誤回應

錯誤回應看起來像這樣：

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 參數 | 描述 |
| --- | --- |
| `error` | 您可用來分類發生的錯誤類型並對錯誤做出反應的錯誤碼字串。 |
| `error_description` | 可協助您識別驗證錯誤根本原因的特定錯誤訊息。 |
| `error_codes` | 可協助進行診斷的 STS 特定錯誤碼清單。 |
| `timestamp` | 錯誤發生時間。 |
| `trace_id` | 可協助進行診斷的要求唯一識別碼。 |
| `correlation_id` | 可協助跨元件進行診斷的要求唯一識別碼。 |

## <a name="use-a-token"></a>使用權杖

現在您已取得權杖，可使用權杖對資源提出要求。 當權杖到期時，只要重複執行對 `/token` 端點的要求，即可取得全新的存取權杖。

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```bash
# Pro tip: Try the following command! (Replace the token with your own.)

curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>程式碼範例和其他文件

讀取 Microsoft 驗證程式庫中的[用戶端認證概觀文件](https://aka.ms/msal-net-client-credentials)

| 範例 | 平台 |描述 |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET core 2.1 主控台 | 一個簡單的.NET Core 應用程式，它會顯示使用應用程式的身分識別查詢 Microsoft Graph 之租用戶的使用者，而不是代表使用者。 該範例也會說明使用憑證進行驗證的變化。 |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | 一個 Web 應用程式，它使用應用程式的身分識別同步處理 Microsoft Graph 中的資料，而不是代表使用者。 |
