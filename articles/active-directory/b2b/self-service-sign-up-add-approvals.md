---
title: 將自訂核准新增至自助式註冊流程-Azure AD
description: 在外部身分識別中新增自訂核准工作流程的 API 連接器自助式註冊-Azure Active Directory （Azure AD）
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d1a4495b1d637b1cf8592f8c17e63ad456ea3c4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027456"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>新增自訂核准工作流程以進行自助式註冊

透過[API 連接器](api-connectors-overview.md)，您可以使用自助式註冊來與您自己的自訂核准工作流程整合，以便管理您的租使用者中所建立的來賓使用者帳戶。

本文提供如何與核准系統整合的範例。 在此範例中，自助註冊使用者流程會在註冊程式期間收集使用者資料，並將其傳遞給您的核准系統。 然後，核准系統可以：

- 自動核准使用者，並允許 Azure AD 建立使用者帳戶。
- 觸發手動審查。 如果要求經過核准，則核准系統會使用 Microsoft Graph 來布建使用者帳戶。 核准系統也可以通知使用者他們的帳戶已建立。

## <a name="register-an-application-for-your-approval-system"></a>為您的核准系統註冊應用程式

您必須將您的核准系統註冊為 Azure AD 租使用者中的應用程式，以便向 Azure AD 進行驗證，並具有建立使用者的許可權。 深入瞭解[Microsoft Graph 的驗證和授權基本概念](https://docs.microsoft.com/graph/auth/auth-concepts)。

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [Azure 服務] 底下，選取 [Azure Active Directory]。
3. 在左側功能表中，選取 [**應用程式註冊**]，然後選取 [**新增註冊**]。
4. 輸入應用程式的**名稱**，例如_註冊核准_。

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. 選取 [註冊]。 您可以將其他欄位保留為預設值。

   ![註冊應用程式的頁面](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. 在左側功能表中的 [**管理**] 底下，選取 [ **API 許可權**]，然後選取 [**新增許可權**]。
7. 在 [**要求 API 許可權**] 頁面上，選取 [ **Microsoft Graph**]，然後選取 [**應用程式許可權**]。
8. 在 [**選取許可權**] 底下，展開 [**使用者**]，然後選取 [ **user. ReadWrite** ] 核取方塊。 此許可權可讓核准系統在核准時建立使用者。 然後選取 [新增權限]。

   ![註冊應用程式的頁面](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. 在 [ **API 許可權**] 頁面上，選取 [授與系統**管理員同意（您的租使用者名稱）**]，然後選取 **[是]**。
10. 在左側功能表中的 [**管理**] 底下，選取 [**憑證 & 密碼**]，然後選取 [**新增用戶端密碼**]。
11. 輸入密碼的**描述**，例如 [_核准用戶端密碼_]，然後選取用戶端密碼**到期**的持續時間。 然後選取 [新增]。
12. 複製 [用戶端密碼] 的值。

    ![複製要在核准系統中使用的用戶端密碼](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. 將您的核准系統設定為使用 [**應用程式識別碼**] 作為用戶端識別碼，以及您所產生用來驗證 Azure AD 的**用戶端密碼**。

## <a name="create-the-api-connectors"></a>建立 API 連接器

接下來，您將為自助註冊使用者流程[建立 API 連接器](self-service-sign-up-add-api-connector.md#create-an-api-connector)。 您的核准系統 API 需要兩個連接器和對應的端點，如下列範例所示。 這些 API 連接器會執行下列動作：

- **檢查核准狀態**。 在使用者登入身分識別提供者之後，立即傳送對核准系統的呼叫，以檢查使用者是否有現有的核准要求，或是否已遭到拒絕。 如果您的核准系統僅執行自動核准決策，可能就不需要此 API 連接器。 「檢查核准狀態」 API 連接器的範例。

  ![檢查核准狀態 API 連接器設定](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **要求核准**-在使用者完成 [屬性集合] 頁面後，但在建立使用者帳戶之前，傳送對核准系統的呼叫，以要求核准。 核准要求可以自動授與或手動審核。 「要求核准」 API 連接器的範例。 選取**要傳送**核准系統所需的任何宣告，以做出核准決策。

  ![要求核准 API 連接器設定](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

若要建立這些連接器，請遵循[建立 API 連接器](self-service-sign-up-add-api-connector.md#create-an-api-connector)中的步驟。

## <a name="enable-the-api-connectors-in-a-user-flow"></a>在使用者流程中啟用 API 連接器

現在，您會使用下列步驟，將 API 連接器新增至自助註冊使用者流程：

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [Azure 服務] 底下，選取 [Azure Active Directory]。
3. 在左側功能表中，選取 [外部身分識別]。
4. 選取 [**使用者流程（預覽）**]，然後選取您想要啟用 API 連接器的使用者流程。
5. 選取 [ **api 連接器**]，然後在使用者流程的下列步驟中，選取您想要叫用的 api 端點：

   - **使用身分識別提供者登入之後**：選取您的核准狀態 API 連接器，例如 [_檢查核准狀態_]。
   - **建立使用者之前**：請選取您的核准要求 API 連接器，例如_要求核准_。

   ![將 Api 新增至使用者流程](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. 選取 [儲存]。

## <a name="control-the-sign-up-flow-with-api-responses"></a>使用 API 回應控制註冊流程

您的核准系統可以使用來自兩個 API 端點的[api 回應類型](self-service-sign-up-add-api-connector.md#expected-response-types-from-the-web-api)來控制註冊流程。

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>「檢查核准狀態」 API 連接器的要求和回應

API 從「檢查核准狀態」 API 連接器收到的要求範例：

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "ui_locales":"en-US"
}
```

#### <a name="continuation-response-for-check-approval-status"></a>「檢查核准狀態」的接續回應

**檢查核准狀態**API 端點應會傳回接續回應，如下所示：

- 使用者先前尚未要求核准。

接續回應的範例：

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>「檢查核准狀態」的封鎖回應

**檢查核准狀態**API 端點應會在下列情況傳回封鎖回應：

- 使用者核准已暫止。
- 使用者遭到拒絕，不應允許再次要求核准。

以下是封鎖回應的範例：

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-PENDING"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-DENIED"
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>「要求核准」 API 連接器的要求和回應

API 從「要求核准」 API 連接器收到的 HTTP 要求範例：

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

#### <a name="continuation-response-for-request-approval"></a>「要求核准」的接續回應

**要求核准**API 端點應該會在下列情況傳回接續回應：

- 使用者可以**_自動核准_**。

接續回應的範例：

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> 如果收到接續回應，Azure AD 會建立使用者帳戶，並將使用者導向至應用程式。

#### <a name="blocking-response-for-request-approval"></a>「要求核准」的封鎖回應

**要求核准**API 端點應該會在下列情況傳回封鎖回應：

- 已建立使用者核准要求，且現在已擱置。
- 已自動拒絕使用者核准要求。

以下是封鎖回應的範例：

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-REQUESTED"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-AUTO-DENIED"
}
```

`userMessage`回應中的會向使用者顯示，例如：

![等待核准頁面範例](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>手動核准後建立使用者帳戶

取得手動核准之後，自訂核准系統會使用[Microsoft Graph](https://docs.microsoft.com/graph/use-the-api)建立[使用者](https://docs.microsoft.com/graph/azuread-users-concept-overview)帳戶。 您的核准系統布建使用者帳戶的方式，取決於使用者所使用的身分識別提供者。

### <a name="for-a-federated-google-or-facebook-user"></a>適用于同盟 Google 或 Facebook 使用者

> [!IMPORTANT]
> 核准系統應該明確地檢查 `identities` ， `identities[0]` 和 `identities[0].issuer` 是否存在，以及是否 `identities[0].issuer` 等於 ' facebook ' 或 ' google ' 以使用此方法。

如果您的使用者已使用 Google 或 Facebook 帳戶登入，您可以使用[使用者建立 API](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0&tabs=http)。

1. 核准系統會接收來自使用者流程的 HTTP 要求。

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. 核准系統會使用 Microsoft Graph 來建立使用者帳戶。

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| 參數                                           | 必要 | 描述                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName                                   | 是      | 可以藉由接受 `email` 傳送至 API 的宣告、將字元取代為 `@` `_` ，並將它預先擱置到，來產生 `#EXT@<tenant-name>.onmicrosoft.com` 。 |
| accountEnabled                                      | 是      | 必須設為 `true`。                                                                                                                                                 |
| mail                                                | 是      | 相當於 `email` 傳送至 API 的宣告。                                                                                                               |
| userType                                            | 是      | 必須是 `Guest`。 將此使用者指定為來賓使用者。                                                                                                                 |
| 身分識別                                          | 是      | 同盟身分識別資訊。                                                                                                                                    |
| \<otherBuiltInAttribute>                            | 否       | 其他內建屬性，例如 `displayName` 、 `city` 和其他屬性。 參數名稱與 API 連接器所傳送的參數相同。                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | 否       | 使用者的自訂屬性。 參數名稱與 API 連接器所傳送的參數相同。                                                            |

### <a name="for-a-federated-azure-active-directory-user"></a>針對同盟 Azure Active Directory 使用者

如果使用者使用同盟 Azure Active Directory 帳戶登入，您就必須使用[邀請 api](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0)來建立使用者，然後再選擇[使用者更新 api](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0) ，將更多屬性指派給使用者。

1. 核准系統會接收來自使用者流程的 HTTP 要求。

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. 核准系統會使用 `email` API 連接器所提供的來建立邀請。

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress":"johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

回應的範例：

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. 核准系統會使用受邀使用者的識別碼，以所收集的使用者屬性來更新使用者的帳戶（選擇性）。

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>接下來的步驟

- 開始使用我們的[Azure 函數快速入門範例](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)。
- [使用手動核准範例來簽出來賓使用者的自助式註冊](code-samples-self-service-sign-up.md#custom-approval-workflows)。 
