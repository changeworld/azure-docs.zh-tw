---
title: 將 API 連接器新增至自助式註冊流程-Azure AD
description: 設定要在使用者流程中使用的 Web API。
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
ms.openlocfilehash: 88270d51bf50b2b175d9d8761685a8a2a8ae19b1
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87428270"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>將 API 連接器新增至使用者流程

若要使用[api 連接器](api-connectors-overview.md)，您必須先建立 api 連接器，然後在使用者流程中啟用它。

## <a name="create-an-api-connector"></a>建立 API 連接器

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [Azure 服務] 底下，選取 [Azure Active Directory]。
3. 在左側功能表中，選取 [外部身分識別]。
4. 選取 **[所有 api 連接器（預覽）**]，然後選取 [**新增 api 連接器**]。

   ![新增 API 連接器](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. 提供通話的顯示名稱。 例如，**檢查核准狀態**。
6. 提供 API 呼叫的**端點 URL** 。
7. 提供 API 的驗證資訊。

   - 目前僅支援基本驗證。 如果您想要使用沒有基本驗證的 API 來進行開發，只要輸入您的 API 可以忽略的虛擬使用者**名稱**和**密碼**即可。 若要搭配具有 API 金鑰的 Azure 函式使用，您可以在**端點 URL**中包含程式碼做為查詢參數（例如，HTTPs：/ []() /contoso.azurewebsites.net/api/endpoint<b>？ code = 0123456789</b>）。

   ![新增 API 連接器](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. 選取您想要傳送至 API 的宣告。
9. 選取您計畫要從 API 接收回的任何宣告。

   <!-- ![Set API connector claims](./media/self-service-sign-up-add-api-connector/api-connector-claims.png) -->

10. 選取 [儲存]。

### <a name="selection-of-claims-to-send-and-claims-to-receive"></a>選取 [要傳送的宣告] 和 [要接收的宣告]
> [!IMPORTANT]
> 您可能會看到依預設選取的所有宣告，如下所示。 所有 API 連接器都會更新為以這種方式運作。 您的 API 將會接收所有可用的宣告，並可傳回任何支援的宣告，而不需要在 API 連接器定義中進行設定。 

![設定 API 連接器宣告](./media/self-service-sign-up-add-api-connector/api-connector-claims-new.png)


## <a name="enable-the-api-connector-in-a-user-flow"></a>在使用者流程中啟用 API 連接器

請遵循下列步驟，將 API 連接器新增至自助註冊使用者流程。

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [Azure 服務] 底下，選取 [Azure Active Directory]。
3. 在左側功能表中，選取 [外部身分識別]。
4. 選取 [**使用者流程（預覽）**]，然後選取您想要新增 API 連接器的使用者流程。
5. 選取 [ **api 連接器**]，然後在使用者流程的下列步驟中，選取您想要叫用的 api 端點：

   - **使用身分識別提供者登入之後**
   - **建立使用者之前**

   ![將 Api 新增至使用者流程](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. 選取 [儲存]。

深入瞭解[您可以在使用者流程中啟用 API 連接器的位置](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow)。

## <a name="request-sent-to-the-api"></a>已將要求傳送至 API

API 連接器具體化為 HTTP POST 要求，將選取的宣告當做 JSON 主體中的機碼值組來傳送。 回應也應該有 HTTP 標頭 `Content-Type: application/json` 。 屬性會序列化，類似于 Microsoft Graph 使用者屬性。 <!--# TODO: Add link to MS Graph or create separate reference.-->

### <a name="example-request"></a>範例要求

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
 "postalCode": "33971",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

**使用者介面地區設定（' ui_locales '）** 宣告預設會在所有要求中傳送。 它會提供使用者的地區設定，並可供 API 用來傳回國際化回應。 它不會出現在 [API 設定] 窗格中。

如果要傳送的宣告在呼叫 API 端點時沒有值，宣告將不會傳送至 API。

您可以使用**extension_ \<extensions-app-id> _AttributeName**格式，為使用者建立自訂屬性。 您的 API 應該會收到此相同序列化格式的宣告。 您的 API 可以傳回具有或不含的宣告 `<extensions-app-id>` 。 如需自訂屬性的詳細資訊，請參閱[定義自助式註冊流程的自訂屬性](user-flow-add-custom-attributes.md)。

> [!TIP] 
> 身分識別（「身分識別」 [**）**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0)和**電子郵件地址（「電子郵件」）** 宣告可用來在您的租使用者中擁有帳戶之前，先找出使用者。 當使用者使用 Google 或 Facebook 進行驗證，且一律傳送「電子郵件」時，會傳送「身分識別」宣告。

## <a name="expected-response-types-from-the-web-api"></a>來自 Web API 的預期回應類型

當 Web API 在使用者流程中收到來自 Azure AD 的 HTTP 要求時，它可能會傳回下列回應：

- [接續回應](#continuation-response)
- [封鎖回應](#blocking-response)
- [驗證-錯誤回應](#validation-error-response)

### <a name="continuation-response"></a>接續回應

接續回應表示使用者流程應該繼續進行下一個步驟。 在接續回應中，API 可以傳回宣告。

如果 API 傳回宣告，並選取做為**要接收**的宣告，則宣告會執行下列動作：

- 如果在顯示頁面之前叫用 API 連接器，請在 [屬性集合] 頁面中預先填入輸入欄位。
- 覆寫已指派給宣告的任何值。
- 將值指派給宣告（如果它先前為 null）。

#### <a name="example-of-a-continuation-response"></a>接續回應的範例

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

### <a name="blocking-response"></a>封鎖回應

封鎖回應會結束使用者流程。 API 可以刻意發行，藉由向使用者顯示區塊頁面來停止使用者流程的接續。 [封鎖] 頁面會顯示 `userMessage` API 所提供的。

封鎖回應的範例：

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
| 代碼        | String | 否       | 錯誤碼。 可以用於偵錯工具。 不向使用者顯示。 |

#### <a name="end-user-experience-with-a-blocking-response"></a>具有封鎖回應的終端使用者體驗

![範例區塊頁面](./media/api-connectors-overview/blocking-page-response.png)

### <a name="validation-error-response"></a>驗證-錯誤回應

在屬性集合頁面之後叫用的 API 呼叫可能會傳回驗證錯誤回應。 當您這麼做時，使用者流程會保留在 [屬性集合] 頁面上，並 `userMessage` 向使用者顯示。 然後，使用者可以編輯並重新提交表單。 這種類型的回應可以用於輸入驗證。

#### <a name="example-of-a-validation-error-response"></a>驗證錯誤回應的範例

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
| 代碼        | String  | 否       | 錯誤碼。 可以用於偵錯工具。 不向使用者顯示。 |

#### <a name="end-user-experience-with-a-validation-error-response"></a>具有驗證錯誤回應的終端使用者體驗

![範例驗證頁面](./media/api-connectors-overview/validation-error-postal-code.png)

### <a name="integration-with-azure-functions"></a>與 Azure Functions 整合
您可以使用 Azure Functions 中的 HTTP 觸發程式，做為建立 API 以搭配 API 連接器使用的簡單方式。 [例如](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)，您可以使用 Azure 函式來執行驗證邏輯，並限制對特定網域的登入。 您也可以呼叫和叫用其他 web Api、使用者存放區和其他雲端服務。

## <a name="next-steps"></a>後續步驟

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- 瞭解如何[將自訂核准工作流程新增至自助式註冊](self-service-sign-up-add-approvals.md)
- 開始使用我們的[Azure 函數快速入門範例](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)。
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
