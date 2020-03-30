---
title: 呼叫、觸發或巢狀邏輯應用程式
description: 設置 HTTP 終結點以在 Azure 邏輯應用中調用、觸發或嵌套邏輯應用工作流
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d5b5a69c7927d07c0ae6b3b56ec97b6551e5d46b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191340"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>使用 Azure 邏輯應用中的 HTTP 終結點調用、觸發或嵌套邏輯應用

要使邏輯應用能夠通過 URL 進行調用，以便邏輯應用可以接收來自其他服務的傳入請求，可以在本機上公開同步 HTTP 終結點作為該邏輯應用上的觸發器。 設置此功能時，還可以將邏輯應用嵌套在其他邏輯應用中，從而創建可調用終結點的模式。

要設置 HTTP 終結點，可以使用以下任一觸發器類型，使邏輯應用能夠接收傳入請求：

* [請求](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* 託管連接器觸發器具有[ApiConnectionWebhook 類型](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)，可以接收傳入的 HTTP 要求

> [!NOTE]
> 這些示例使用請求觸發器，但您可以使用上一清單中的任何基於 HTTP 要求的觸發器。 所有原則都同樣適用于這些其他觸發器類型。

如果您對邏輯應用是新應用，請參閱[什麼是 Azure 邏輯應用](../logic-apps/logic-apps-overview.md)和[快速入門：創建第一個邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 要將 HTTP 終結點設置為觸發器的邏輯應用。 可以從空白邏輯應用或要替換當前觸發器的現有邏輯應用開始。 此示例從空白邏輯應用開始。

## <a name="create-a-callable-endpoint"></a>創建可調用終結點

1. 登錄到 Azure[門戶](https://portal.azure.com)。 在邏輯應用設計器中創建並打開空白邏輯應用。

   此示例使用請求觸發器，但您可以使用可以接收傳入 HTTP 要求的任何觸發器。 所有原則都同樣適用于這些觸發器。 有關請求觸發器的詳細資訊，請參閱[使用 Azure 邏輯應用接收和回應傳入的 HTTPS 調用](../connectors/connectors-native-reqres.md)。

1. 在搜索框下，選擇 **"內置**"。 在搜尋方塊中，輸入 `request` 作為篩選條件。 從觸發器清單中，選擇**何時收到 HTTP 要求**。

   ![查找並選擇請求觸發器](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. 或者，在 **"請求正文 JSON 架構"** 框中，可以輸入 JSON 架構，描述您希望觸發器接收的有效負載或資料。

   設計器使用此架構生成表示觸發器輸出的權杖。 然後，您可以在邏輯應用的工作流中輕鬆引用這些輸出。 詳細瞭解從[JSON 架構生成的權杖](#generated-tokens)。

   在此示例中，請輸入此架構：

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![為請求操作提供 JSON 架構](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   或者，您可以通過提供示例負載來生成 JSON 架構：

   1. 在**請求**觸發器中，選擇**使用示例有效負載生成架構**。

   1. **在"輸入或粘貼 JSON 有效負載示例**"框中，輸入示例有效負載，例如：

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. 準備就緒後，選擇 **"完成**"。

      **"請求正文 JSON 架構"** 框現在顯示生成的架構。

1. 儲存您的邏輯應用程式。

   **此 URL 的 HTTP POST**框現在顯示生成的回檔 URL，其他服務可用於調用和觸發邏輯應用。 此 URL 包括共用訪問簽名 （SAS） 金鑰，用於身份驗證，用於查詢參數，例如：

   ![為端點產生的回呼 URL](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   您還可以從邏輯應用的 **"概述"** 窗格中獲取 HTTP 終結點 URL。

   1. 在邏輯應用的功能表上，選擇 **"概述**"。

   1. 在 **"摘要"** 部分中，選擇 **"查看觸發器歷史記錄**"。

      ![從 Azure 入口網站取得 HTTP 端點 URL](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. 在**回撥 URL [POST]** 下，複製 URL：

      ![從 Azure 門戶複製 HTTP 終結點 URL](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      或者，您可以進行這個呼叫來取得 URL：

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>設置預期的 HTTP 方法

根據預設，要求觸發程序會預期 HTTP POST 要求。 但是，您可以指定一個需要期待的不同方法，但只能指定一種方法。

1. 在"請求"觸發器中，打開 **"添加新參數**清單"，然後選擇**方法**，將此屬性添加到觸發器中。

   ![添加要觸發的"方法"屬性](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. 從**方法**清單中，選擇觸發器期望的另一種方法。 或者，您可以指定自訂方法。

   例如，選擇**GET**方法，以便以後可以測試 HTTP 終結點的 URL。

   ![選擇要用於觸發器的 HTTP 方法](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>接受終結點 URL 中的參數

當希望終結點 URL 接受參數時，請在觸發器中指定相對路徑。 您還需要顯式設置 HTTP 要求所需的[方法](#set-method)。

1. 在"請求"觸發器中，打開 **"添加新參數**清單"，然後選擇 **"相對路徑**"，這將此屬性添加到觸發器中。

   ![添加要觸發的"相對路徑"屬性](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. 在**相對路徑**屬性中，指定 JSON 架構中您希望 URL 接受的參數的相對路徑，例如。 `address/{postalCode}`

   ![指定參數的相對路徑](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. 要使用 參數，請查找**回應操作並將其**添加到邏輯應用。

   1. 在"請求觸發器"下，選擇 **"新建步驟** > **添加操作**"。

   1. 在 [選擇動作]**** 底下的搜尋方塊中，輸入 `response` 作為篩選條件。

   1. 從動作清單中選擇 **"回應**"操作。

1. 在回應操作的**Body**屬性中，包括表示在觸發器相對路徑中指定的參數的權杖。

   例如，假設您希望回應操作返回`Postal Code: {postalCode}`。

   在**Body**屬性中`Postal Code: `，使用尾隨空格輸入。 從顯示的動態內容清單中，選擇**郵遞區號**權杖。

   ![將指定的參數添加到回應正文](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   **"正文"** 屬性現在包括所選參數：

   ![帶參數的示例回應正文](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. 儲存您的邏輯應用程式。

    HTTP 端點 URL 現在包括相對路徑，例如︰

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. 若要測試 HTTP 端點，請複製更新過的 URL，並將其貼入另一個瀏覽器視窗中，但將 `{postalCode}` 取代為 `123456`，然後按 Enter 鍵。

   您的瀏覽器顯示以下文本：`Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>通過 HTTP 終結點調用邏輯應用

創建 HTTP 終結點後，可以通過向終結點的完整 URL 發送 HTTP`POST`請求來觸發邏輯應用。 邏輯應用程式具有直接存取端點的內建支援。

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>從架構生成的權杖

在請求觸發器中提供 JSON 架構時，邏輯應用設計器會為該架構中的屬性生成權杖。 您接著可以使用這些權杖，透過邏輯應用程式工作流程來傳遞資料。

例如，如果向 JSON 架構添加更多屬性`"suite"`（如 ），則這些屬性的權杖可用於邏輯應用的後續步驟。 以下是完整 JSON 結構描述︰

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>創建嵌套邏輯應用

您可以新增其他可接收要求的邏輯應用程式，以在邏輯應用程式中巢狀處理工作流程。 要包括這些邏輯應用，請按照以下步驟操作：

1. 在要調用另一個邏輯應用的步驟下，選擇 **"新建步驟** > **添加操作**"。

1. 在 [選擇動作]**** 底下，選取 [內建]****。 在搜尋方塊中，輸入 `logic apps` 作為篩選條件。 從動作清單中選擇 **"選擇邏輯應用"工作流**。

   ![在當前邏輯應用內嵌套邏輯應用](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   設計器顯示要選擇的合格邏輯應用。

1. 選擇要從當前邏輯應用調用的邏輯應用。

   ![選擇邏輯應用從當前邏輯應用調用](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>參考來自傳入要求的內容

如果傳入請求的內容類型為`application/json`，則可以引用傳入請求中的屬性。 否則，此內容將被視為單個二進位單元，您可以將其傳遞給其他 API。 要在邏輯應用的工作流中引用此內容，您需要首先轉換該內容。

例如，如果要傳遞具有`application/xml`類型的內容，則可以使用[`@xpath()`運算式](../logic-apps/workflow-definition-language-functions-reference.md#xpath)執行 XPath 提取，或使用[`@json()`運算式](../logic-apps/workflow-definition-language-functions-reference.md#json)將 XML 轉換為 JSON。 瞭解有關使用受支援[內容類型](../logic-apps/logic-apps-content-type.md)的更多內容。

要從傳入請求獲取輸出，可以使用[`@triggerOutputs`運算式](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs)。 例如，假設您的輸出如下所示， 示例：

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

要專門訪問屬性`body`，可以使用[`@triggerBody()`運算式](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody)作為快捷方式。

## <a name="respond-to-requests"></a>回應要求

有時，您希望通過將內容返回調用方來回應觸發邏輯應用的某些請求。 要為回應構造狀態碼、標頭和正文，請使用回應操作。 這個動作可以出現在邏輯應用程式的任何位置，而不只是工作流程的結尾。 如果邏輯應用不包含回應操作，HTTP 終結點*將立即*回應**202 接受**狀態。

對於原始調用方要成功獲取回應，回應的所有必需步驟必須在[請求超時限制](./logic-apps-limits-and-config.md)內完成，除非將觸發的邏輯應用稱為嵌套邏輯應用。 如果在此限制內未返回回應，則傳入請求超時並接收**408 用戶端超時**回應。

對於嵌套邏輯應用，父邏輯應用將繼續等待回應，直到完成所有步驟，無論需要多少時間。

### <a name="construct-the-response"></a>建構回應

在回應正文中，可以包含多個標頭和任何類型的內容。 例如，此回應的標頭指定回應的內容類型為`application/json`，正文包含`town`和`postalCode`屬性的值，具體取決於本主題前面所述的"請求觸發器"的 JSON 架構。

![為 HTTP 回應操作提供回應內容](./media/logic-apps-http-endpoint/content-for-response-action.png)

回應具有下列屬性：

| 屬性（顯示） | Property (JSON) | 描述 |
|--------------------|-----------------|-------------|
| **狀態碼** | `statusCode` | 要在傳入請求的回應中使用 HTTP 狀態碼。 此代碼可以是任何以 2xx、4xx 或 5xx 開頭的有效狀態碼。 但是，不允許 3xx 狀態碼。 |
| **標頭** | `headers` | 要包含在回應中的一個或多個標頭 |
| **內文** | `body` | 可以是字串、JSON 物件，甚至是從上一步引用的二進位內容的正文物件 |
||||

要查看回應操作的 JSON 定義和邏輯應用的完整 JSON 定義，請在邏輯應用設計器工具列上選擇 **"代碼"視圖**。

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>問答集

#### <a name="q-what-about-url-security"></a>問︰URL 安全性如何？

**A**： Azure 使用[共用訪問簽名 （SAS）](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)安全地生成邏輯應用回檔 URL。 此簽名作為查詢參數傳遞，必須先驗證邏輯應用才能運行。 Azure 會使用每個邏輯應用程式、觸發程序名稱以及要執行作業之秘密金鑰的唯一組合來產生簽章。 因此，除非某人具有邏輯應用程式秘密金鑰的存取權，否則他們無法產生有效的簽章。

> [!IMPORTANT]
> 對於生產和更高的安全系統，出於以下原因，我們強烈建議不要直接從瀏覽器調用邏輯應用：
>
> * URL 中出現共用存取金鑰。
> * 由於 Azure 邏輯應用客戶之間的共用域，因此無法管理安全內容策略。

#### <a name="q-can-i-configure-http-endpoints-further"></a>問︰我可以進一步設定 HTTP 端點嗎？

**答**：是的，HTTP 終結點支援通過 Azure API 管理進行更高級[的配置](../api-management/api-management-key-concepts.md)。 此服務也可讓您透過一致的方式管理所有 API，包括邏輯應用程式、設定自訂網域名稱、使用其他驗證方法等等，例如︰

* [變更要求方法](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [變更要求的 URL 區段](../api-management/api-management-transformation-policies.md#RewriteURL)
* 在[Azure 門戶](https://portal.azure.com/)中設置 API 管理域
* 設定檢查基本驗證的原則

## <a name="next-steps"></a>後續步驟

* [使用 Azure 邏輯應用接收和回應傳入的 HTTPS 調用](../connectors/connectors-native-reqres.md)