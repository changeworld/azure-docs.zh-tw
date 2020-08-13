---
title: 將 API 連接器新增至自助式註冊流程-Azure AD
description: 設定要在使用者流程中使用的 Web API。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f241fd038d0d7309d8e1e5578dd77f950261b68
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165170"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>將 API 連接器新增至使用者流程

若要使用[api 連接器](api-connectors-overview.md)，您必須先建立 api 連接器，然後在使用者流程中啟用它。

## <a name="create-an-api-connector"></a>建立 API 連接器

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [Azure 服務] 底下，選取 [Azure Active Directory]。
3. 在左側功能表中，選取 [外部身分識別]。
4. 選取 [**所有 api 連接器] (預覽) **]，然後選取 [**新增 api 連接器**]。

   ![新增 API 連接器](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. 提供通話的顯示名稱。 例如，**檢查核准狀態**。
6. 提供 API 呼叫的**端點 URL** 。
7. 提供 API 的驗證資訊。

   - 目前僅支援基本驗證。 如果您想要使用沒有基本驗證的 API 來進行開發，只要輸入您的 API 可以忽略的虛擬使用者**名稱**和**密碼**即可。 若要搭配具有 API 金鑰的 Azure 函式使用，您可以在**端點 URL**中包含程式碼作為查詢參數 (例如，HTTPs []() ：//contoso.azurewebsites.net/api/endpoint<b>？ code = 0123456789</b>) 。

   ![新增 API 連接器](./media/self-service-sign-up-add-api-connector/api-connector-config.png)
8. 選取 [儲存]。

> [!IMPORTANT]
> 之前，您必須設定要傳送至 API 的使用者屬性 ( 「要傳送的宣告」 ) 以及要從 API 接受哪些使用者屬性 ( ' 宣告接收 ' ) 。 現在，如果所有使用者屬性具有值，而且 API 可在「接續」回應中傳回任何使用者屬性，則預設會傳送這些屬性。

## <a name="the-request-sent-to-your-api"></a>傳送至您 API 的要求
API 連接器會具體化為**HTTP POST**要求，將使用者屬性 ( ' 宣告 ' ) 作為 JSON 主體中的機碼值組來傳送。 屬性會序列化，類似于[Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties)使用者屬性。 

**範例要求**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

只有列出在**Azure Active Directory**  >  **外部**身分識別  >  **自訂使用者屬性**體驗中的使用者屬性和自訂屬性，才可在要求中傳送。

自訂屬性存在於目錄中的**extension_ \<extensions-app-id> _AttributeName**格式。 您的 API 應該會收到此相同序列化格式的宣告。 如需自訂屬性的詳細資訊，請參閱[定義自助式註冊流程的自訂屬性](user-flow-add-custom-attributes.md)。

此外，預設會在所有要求中傳送** ( ' ui_locales ' ) 宣告的 UI 地區**設定。 它會提供使用者的地區設定 (s) ，如其裝置上所述，可供 API 用來傳回國際化回應。

> [!IMPORTANT]
> 如果要傳送的宣告在呼叫 API 端點時沒有值，宣告將不會傳送至 API。 您的 API 應該設計成明確檢查它所預期的值。

> [!TIP] 
> 身分識別 ( 「身分識別」 [**) **](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0)和**電子郵件地址 ( 「電子郵件」 ) **宣告可供您的 API 用來在您的租使用者中擁有帳戶之前，先找出使用者。 當使用者向身分識別提供者（例如 Google 或 Facebook）進行驗證時，就會傳送 ' identity ' 宣告。 [電子郵件] 一律會傳送。

## <a name="enable-the-api-connector-in-a-user-flow"></a>在使用者流程中啟用 API 連接器

請遵循下列步驟，將 API 連接器新增至自助註冊使用者流程。

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [Azure 服務] 底下，選取 [Azure Active Directory]。
3. 在左側功能表中，選取 [外部身分識別]。
4. 選取 [**使用者流程] (預覽) **]，然後選取您想要新增 API 連接器的使用者流程。
5. 選取 [ **api 連接器**]，然後在使用者流程的下列步驟中，選取您想要叫用的 api 端點：

   - **使用身分識別提供者登入之後**
   - **建立使用者之前**

   ![將 Api 新增至使用者流程](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. 選取 [儲存]。

## <a name="after-signing-in-with-an-identity-provider"></a>使用身分識別提供者登入之後

在使用者向身分識別提供者驗證 (Google、Facebook、Azure AD) 之後，就會立即叫用註冊程式中此步驟的 API 連接器。 此步驟會在 [***屬性集合] 頁面***之前，這是向使用者呈現以收集使用者屬性的表單。 

<!-- The following are examples of API connector scenarios you may enable at this step:
- Use the email or federated identity that the user provided to look up claims in an existing system. Return these claims from the existing system, pre-fill the attribute collection page, and make them available to return in the token.
- Validate whether the user is included in an allow or deny list, and control whether they can continue with the sign-up flow. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>在此步驟傳送至 API 的範例要求
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

傳送至 API 的確切宣告取決於識別提供者所提供的資訊。 [電子郵件] 一律會傳送。

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>此步驟中 Web API 的預期回應類型

當 Web API 在使用者流程中收到來自 Azure AD 的 HTTP 要求時，它可能會傳回下列回應：

- 接續回應
- 封鎖回應

#### <a name="continuation-response"></a>接續回應

接續回應表示使用者流程應該繼續進行下一個步驟： [屬性集合] 頁面。

在接續回應中，API 可以傳回宣告。 如果 API 傳回宣告，則宣告會執行下列動作：

- 預先填入 [屬性集合] 頁面中的輸入欄位。

請參閱[接續回應](#example-of-a-continuation-response)的範例。

#### <a name="blocking-response"></a>封鎖回應

封鎖回應會結束使用者流程。 API 可以刻意發行，藉由向使用者顯示區塊頁面來停止使用者流程的接續。 [封鎖] 頁面會顯示 `userMessage` API 所提供的。

請參閱[封鎖回應](#example-of-a-blocking-response)的範例。

## <a name="before-creating-the-user"></a>建立使用者之前

在註冊程式的這個步驟中，API 連接器會在屬性集合頁面（如果有包含的話）之後叫用。 在 Azure AD 中建立使用者帳戶之前，一律會叫用此步驟。 

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>在此步驟傳送至 API 的範例要求

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
傳送至 API 的確切宣告取決於從使用者收集的資訊，或由識別提供者提供。

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>此步驟中 Web API 的預期回應類型

當 Web API 在使用者流程中收到來自 Azure AD 的 HTTP 要求時，它可能會傳回下列回應：

- 接續回應
- 封鎖回應
- 驗證回應

#### <a name="continuation-response"></a>接續回應

接續回應表示使用者流程應該繼續進行下一個步驟：在目錄中建立使用者。

在接續回應中，API 可以傳回宣告。 如果 API 傳回宣告，則宣告會執行下列動作：

- 覆寫已從 [屬性集合] 頁面指派給宣告的任何值。

請參閱[接續回應](#example-of-a-continuation-response)的範例。

#### <a name="blocking-response"></a>封鎖回應
封鎖回應會結束使用者流程。 API 可以刻意發行，藉由向使用者顯示區塊頁面來停止使用者流程的接續。 [封鎖] 頁面會顯示 `userMessage` API 所提供的。

請參閱[封鎖回應](#example-of-a-blocking-response)的範例。

### <a name="validation-error-response"></a>驗證-錯誤回應
 當 API 以驗證錯誤回應回應時，使用者流程會保留在 [屬性集合] 頁面上，並 `userMessage` 向使用者顯示。 然後，使用者可以編輯並重新提交表單。 這種類型的回應可以用於輸入驗證。

請參閱[驗證-錯誤回應](#example-of-a-validation-error-response)的範例。

## <a name="example-responses"></a>範例回應

### <a name="example-of-a-continuation-response"></a>接續回應的範例

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| 參數                                          | 類型              | 必要 | 描述                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                                            | String            | 是      | API 的版本。                                                                                                                                                                                                                                                                |
| 動作                                             | String            | 是      | 值必須為 `Continue`。                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | 否       | 如果這些值選取為要在 API 連接器設定中**接收**的宣告，以及使用者流程的**使用者屬性**，則可以儲存在目錄中。 如果選取做為**應用程式**宣告，則會在權杖中傳回值。                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | 否       | 傳回的宣告不需要包含 `_<extensions-app-id>_` 。 如果這些值選取為要在 API 連接器設定中**接收**的宣告，以及使用者流程的**使用者屬性**，則會儲存在目錄中。 無法在權杖中傳回自訂屬性。 |

### <a name="example-of-a-blocking-response"></a>封鎖回應的範例

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| 參數   | 類型   | 必要 | 描述                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | 是      | API 的版本。                                                    |
| 動作      | String | 是      | 值必須是`ShowBlockPage`                                              |
| userMessage | String | 是      | 要向使用者顯示的訊息。                                            |
| code        | String | 否       | 錯誤碼。 可以用於偵錯工具。 不向使用者顯示。 |

**具有封鎖回應的終端使用者體驗**

![範例區塊頁面](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>驗證錯誤回應的範例

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| 參數   | 類型    | 必要 | 描述                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | 是      | API 的版本。                                                    |
| 動作      | String  | 是      | 值必須為 `ValidationError`。                                           |
| status      | 整數 | 是      | 必須是 `400` ValidationError 回應的值。                        |
| userMessage | String  | 是      | 要向使用者顯示的訊息。                                            |
| code        | String  | 否       | 錯誤碼。 可以用於偵錯工具。 不向使用者顯示。 |

**具有驗證錯誤回應的終端使用者體驗**

![範例驗證頁面](./media/api-connectors-overview/validation-error-postal-code.png)

## <a name="using-azure-functions"></a>使用 Azure Functions
您可以使用 Azure Functions 中的 HTTP 觸發程式，做為建立 API 端點以搭配 API 連接器使用的簡單方式。 [例如](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)，您可以使用 Azure 函式來執行驗證邏輯，並限制對特定網域的登入。 您也可以從 Azure 函式呼叫和叫用其他 web Api、使用者存放區和其他雲端服務，以進行廣泛的案例。

## <a name="next-steps"></a>後續步驟

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- 瞭解如何[將自訂核准工作流程新增至自助式註冊](self-service-sign-up-add-approvals.md)
- 開始使用我們的[Azure 函數快速入門範例](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)。
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
