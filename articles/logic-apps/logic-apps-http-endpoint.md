---
title: 呼叫、觸發或巢狀邏輯應用程式
description: 在 Azure Logic Apps 中設定 HTTPS 端點以呼叫、觸發或嵌套邏輯應用程式工作流程
services: logic-apps
ms.workload: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 7f91d8eab2e7a29163dae5ae2a4d34792ddd0cb0
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005516"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-https-endpoints-in-azure-logic-apps"></a>在 Azure Logic Apps 中使用 HTTPS 端點來呼叫、觸發或嵌套邏輯應用程式

若要讓邏輯應用程式可透過 URL 呼叫，讓邏輯應用程式可以接收來自其他服務的連入要求，您可以在該邏輯應用程式上，以原生方式公開同步 HTTPS 端點作為觸發程式。 當您設定這項功能時，您也可以將邏輯應用程式放在其他邏輯應用程式中，這可讓您建立可呼叫端點的模式。

若要設定可呼叫的端點，您可以使用任何一種觸發程式類型，讓邏輯應用程式接收連入要求：

* [要求](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* 具有[ApiConnectionWebhook 類型](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)並可接收傳入 HTTPS 要求的受管理連接器觸發程式

> [!NOTE]
> 這些範例會使用要求觸發程式，但您可以使用上一個清單中任何以 HTTPS 要求為基礎的觸發程式。 所有原則都同樣適用于這些其他觸發程式類型。

如果您不熟悉邏輯應用程式，請參閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md)和[快速入門：建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 邏輯應用程式，您想要在其中使用觸發程式來建立可呼叫的端點。 您可以從空白邏輯應用程式或您想要取代目前觸發程式的現有邏輯應用程式開始。 這個範例會從空白邏輯應用程式開始。

## <a name="create-a-callable-endpoint"></a>建立可呼叫的端點

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中建立並開啟空白邏輯應用程式。

   這個範例會使用要求觸發程式，但您可以使用任何可接收傳入 HTTPS 要求的觸發程式。 所有原則都相同，適用于這些觸發程式。 如需要求觸發程式的詳細資訊，請參閱[使用 Azure Logic Apps 接收和回應輸入 HTTPS 呼叫](../connectors/connectors-native-reqres.md)。

1. 在 [搜尋] 方塊中，選取 [**內建**]。 在搜尋方塊中，輸入 `request` 作為篩選條件。 從觸發程式清單中，選取 [**收到 HTTP 要求時**]。

   ![尋找並選取要求觸發程式](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. （選擇性）在 [**要求本文 JSON 架構**] 方塊中，您可以輸入 JSON 架構，以描述您希望觸發程式接收的承載或資料。

   設計工具會使用此架構來產生代表觸發程式輸出的權杖。 然後，您就可以在整個邏輯應用程式的工作流程中輕鬆地參考這些輸出。 深入瞭解[從 JSON 架構產生的權杖](#generated-tokens)。

   在此範例中，請輸入此架構：

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

   ![為要求動作提供 JSON 架構](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   或者，您可以藉由提供範例承載來產生 JSON 架構：

   1. 在**要求**觸發程式中，選取 [**使用範例承載來產生架構**]。

   1. 在 [**輸入或貼上範例 JSON**承載] 方塊中，輸入您的範例承載，例如：

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

   1. 當您準備好時，請選取 [**完成**]。

      [**要求本文 JSON 架構**] 方塊現在會顯示所產生的架構。

1. 儲存您的邏輯應用程式。

   [ **HTTP POST url** ] 方塊現在會顯示產生的回呼 URL，供其他服務用來呼叫和觸發邏輯應用程式。 此 URL 包含查詢參數，可指定用於驗證的共用存取簽章（SAS）金鑰。

   ![為端點產生的回呼 URL](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

1. 若要複製回呼 URL，您可以使用下列選項：

   * 在 [ **HTTP POST url** ] 方塊的右邊，選取 [**複製 URL** ] （複製檔案圖示）。

   * 進行此 POST 呼叫：

     `POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01`

   * 從邏輯應用程式的 **[總覽**] 窗格複製 [回呼 URL]。

     1. 在邏輯應用程式的功能表上，選取 **[總覽**]。

     1. 在 [**摘要**] 區段中，選取 [**查看觸發**程式歷程記錄]。

        ![從 Azure 入口網站取得端點 URL](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

     1. 在 **[回呼 url [POST]]** 底下，複製 url：

        ![從 Azure 入口網站複製端點 URL](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url-post.png)

<a name="select-method"></a>

## <a name="select-expected-request-method"></a>選取預期的要求方法

根據預設，要求觸發程式會預期 POST 要求。 您可以指定不同的方法來預期，但只能使用單一方法。

1. 在要求觸發程式中，開啟 [**加入新的參數**] 清單，然後選取 [**方法**]，這會將此屬性新增至觸發程式。

   ![將 "Method" 屬性新增至觸發程式](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. 從 [**方法**] 清單中，選取觸發程式應改為預期的方法。 或者，您也可以指定自訂的方法。

   例如，選取**GET**方法，讓您稍後可以測試端點的 URL。

   ![觸發程式預期的選取要求方法](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

<a name="endpoint-url-parameters"></a>

## <a name="pass-parameters-through-endpoint-url"></a>透過端點 URL 傳遞參數

當您想要透過端點的 URL 接受參數值時，您有下列選項：

* [透過 GET 參數](#get-parameters)或 URL 參數接受值。

  這些值會以名稱/值組的形式傳遞至端點的 URL。 針對此選項，您必須在要求觸發程式中使用 GET 方法。 在後續動作中，您可以使用運算式中的`triggerOutputs()`函數，將參數值當做觸發程式輸出來取得。

* 透過要求觸發程式中參數的[相對路徑來接受值](#relative-path)。

  這些值會透過端點 URL 中的相對路徑來傳遞。 您也必須明確地選取觸發程式預期[的方法](#select-method)。 在後續動作中，您可以直接參考這些輸出，取得參數值作為觸發程式輸出。

<a name="get-parameters"></a>

### <a name="accept-values-through-get-parameters"></a>透過 GET 參數接受值

1. 在要求觸發程式中，開啟 [**加入新的參數] 清單**，將 [**方法**] 屬性加入至觸發程式，然後選取**GET**方法。

   如需詳細資訊，請參閱[選取預期的要求方法](#select-method)。

1. 在要求觸發程式底下，新增您想要使用參數值的動作。 在此範例中，請新增 [**回應**] 動作。

   1. 在要求觸發程式下，選取 [**新增步驟** > ] [新增**動作**]。
   
   1. 在 [選擇動作]**** 底下的搜尋方塊中，輸入 `response` 作為篩選條件。 從 [動作] 清單中，選取 [**回應**] 動作。

1. 若要建立`triggerOutputs()`可抓取參數值的運算式，請遵循下列步驟：

   1. 在回應動作的 [**主體**] 屬性內按一下，以顯示動態內容清單，然後選取 [**運算式**]。

   1. 在 [**運算式**] 方塊中，輸入此運算式`parameter-name` ，並將取代為您的參數名稱，然後選取 **[確定]**。

      `triggerOutputs()['queries']['parameter-name']`

      ![將 "triggerOutputs （）" 運算式新增至觸發程式](./media/logic-apps-http-endpoint/trigger-outputs-expression.png)

      在**Body**屬性中，運算式會解析為`triggerOutputs()` token。

      ![已解析 "triggerOutputs （）" 運算式](./media/logic-apps-http-endpoint/trigger-outputs-expression-token.png)

      如果您儲存邏輯應用程式，請離開設計工具，然後回到設計工具，權杖會顯示您所指定的參數名稱，例如：

      ![參數名稱的已解析運算式](./media/logic-apps-http-endpoint/resolved-expression-parameter-token.png)

      在程式碼視圖中，[**主體**] 屬性會出現在回應動作的定義中，如下所示：

      `"body": "@{triggerOutputs()['queries']['parameter-name']}",`

      例如，假設您想要傳遞名`postalCode`為之參數的值。 **Body**屬性會指定字串， `Postal Code: `其中包含尾端空格，後面接著對應的運算式：

      ![將範例 "triggerOutputs （）" 運算式新增至觸發程式](./media/logic-apps-http-endpoint/trigger-outputs-expression-postal-code.png)

1. 若要測試可呼叫的端點，請複製要求觸發程式中的回呼 URL，然後將 URL 貼入另一個瀏覽器視窗。 在 URL 中，將問號（`?`）後面的參數名稱和值新增至下列格式的 url，然後按 enter 鍵。

   `...?{parameter-name=parameter-value}&api-version=2016-10-01...`

   `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?{parameter-name=parameter-value}&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   瀏覽器會傳回包含下列文字的回應：`Postal Code: 123456`

   ![將要求傳送至回呼 URL 的回應](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

1. 若要將參數名稱和值放在 URL 內的不同位置，請務必使用連字號（`&`）做為前置詞，例如：

   `...?api-version=2016-10-01&{parameter-name=parameter-value}&...`

   這個範例會以 URL 中不同位置的範例參數名稱和`postalCode=123456`值來顯示回呼 URL：

   * 第1個位置：`https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?postalCode=123456&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   * 第2個位置：`https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?api-version=2016-10-01&postalCode=123456&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

<a name="relative-path"></a>

### <a name="accept-values-through-a-relative-path"></a>透過相對路徑接受值

1. 在要求觸發程式中，開啟 [**加入新的參數**] 清單，然後選取 [**相對路徑**]，這會將此屬性新增至觸發程式。

   ![將 [相對路徑] 屬性新增至觸發程式](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. 在 [**相對路徑**] 屬性中，指定您要讓 URL 接受之 JSON 架構中參數的相對路徑，例如`/address/{postalCode}`。

   ![指定參數的相對路徑](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. 在要求觸發程式底下，新增您想要使用參數值的動作。 在此範例中，請新增 [**回應**] 動作。

   1. 在要求觸發程式下，選取 [**新增步驟** > ] [新增**動作**]。

   1. 在 [選擇動作]**** 底下的搜尋方塊中，輸入 `response` 作為篩選條件。 從 [動作] 清單中，選取 [**回應**] 動作。

1. 在 [回應] 動作的 [**主體**] 屬性中，包含代表您在觸發程式相對路徑中指定之參數的 token。

   例如，假設您想要傳迴響應動作`Postal Code: {postalCode}`。

   1. 在 [ **Body** ] 屬性中`Postal Code: ` ，輸入尾端空格。 將游標保持在編輯方塊內部，讓動態內容清單保持開啟。

   1. 在動態內容清單中，從 [**收到 HTTP 要求時**] 區段中，選取 [**郵遞區號**] token。

      ![將指定的參數新增至回應主體](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

      **Body**屬性現在包含選取的參數：

      ![具有參數的範例回應主體](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. 儲存您的邏輯應用程式。

   在要求觸發程式中，回呼 URL 已更新，現在包含相對路徑，例如：

   `https://prod-07.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

1. 若要測試可呼叫的端點，請從要求觸發程式複製更新的回呼 URL，將 URL 貼到另一個`{postalCode}`瀏覽器視窗中`123456`，將 url 中的取代為，然後按 enter 鍵。

   瀏覽器會傳回包含下列文字的回應：`Postal Code: 123456`

   ![將要求傳送至回呼 URL 的回應](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

## <a name="call-logic-app-through-endpoint-url"></a>透過端點 URL 呼叫邏輯應用程式

建立端點之後，您可以藉由將 HTTPS `POST`要求傳送至端點的完整 URL 來觸發邏輯應用程式。 邏輯應用程式具有直接存取端點的內建支援。

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>從架構產生的權杖

當您在要求觸發程式中提供 JSON 架構時，邏輯應用程式設計工具會針對該架構中的屬性產生權杖。 您接著可以使用這些權杖，透過邏輯應用程式工作流程來傳遞資料。

例如，如果您將更多屬性（例如`"suite"`）新增至 JSON 架構，則這些屬性的權杖可供您在後續步驟中用於邏輯應用程式。 以下是完整 JSON 結構描述︰

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

## <a name="create-nested-logic-apps"></a>建立嵌套的邏輯應用程式

您可以新增其他可接收要求的邏輯應用程式，以在邏輯應用程式中巢狀處理工作流程。 若要包含這些邏輯應用程式，請遵循下列步驟：

1. 在您要呼叫另一個邏輯應用程式的步驟中，選取 [**新增步驟** > ] [新增**動作**]。

1. 在 [選擇動作]**** 底下，選取 [內建]****。 在搜尋方塊中，輸入 `logic apps` 作為篩選條件。 從 [動作] 清單中，選取 **[選擇 Logic Apps 工作流程**]。

   ![將邏輯應用程式嵌套在目前的邏輯應用程式中](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   設計工具會顯示符合資格的邏輯應用程式供您選取。

1. 選取要從目前的邏輯應用程式呼叫的邏輯應用程式。

   ![選取要從目前的邏輯應用程式呼叫的邏輯應用程式](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>參考來自傳入要求的內容

如果傳入要求的內容類型為`application/json`，您可以參考傳入要求中的屬性。 否則，會將此內容視為單一的二進位單位，您可以將其傳遞至其他 Api。 若要在邏輯應用程式的工作流程中參考此內容，您必須先轉換該內容。

例如，如果您要傳遞具有`application/xml`類型的內容，您可以使用[ `@xpath()`運算式](../logic-apps/workflow-definition-language-functions-reference.md#xpath)來執行 XPath 解壓縮，或使用[ `@json()`運算式](../logic-apps/workflow-definition-language-functions-reference.md#json)將 XML 轉換成 JSON。 深入瞭解如何使用支援的[內容類型](../logic-apps/logic-apps-content-type.md)。

若要取得連入要求的輸出，您可以使用[ `@triggerOutputs`運算式](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs)。 例如，假設您有如下列範例所示的輸出：

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

若要明確存取`body`屬性，您可以使用[ `@triggerBody()`運算式](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody)做為快捷方式。

## <a name="respond-to-requests"></a>回應要求

有時候您會想要透過將內容傳回給呼叫者，來回應觸發邏輯應用程式的特定要求。 若要針對您的回應來建立狀態碼、標頭和本文，請使用 [回應] 動作。 這個動作可以出現在邏輯應用程式的任何位置，而不只是工作流程的結尾。 如果您的邏輯應用程式不包含回應動作，則端點會*立即*以「 **202 已接受**」狀態回應。

為了讓原始呼叫者成功取得回應，除非已觸發的邏輯應用程式被呼叫為嵌套邏輯應用程式，否則回應的所有必要步驟都必須在[要求超時限制](./logic-apps-limits-and-config.md)內完成。 如果在此限制內未傳回任何回應，傳入的要求會超時，並接收**408 用戶端超時**回應。

針對嵌套的邏輯應用程式，父邏輯應用程式會繼續等候回應，直到所有步驟都完成為止，不論需要多少時間。

### <a name="construct-the-response"></a>建構回應

在回應主體中，您可以包含多個標頭和任何類型的內容。 例如，此回應的標頭指定回應的內容類型為`application/json` ，而主體包含`town`和`postalCode`屬性的值（根據本主題中稍早針對要求觸發程式所述的 JSON 架構）。

![提供 HTTPS 回應動作的回應內容](./media/logic-apps-http-endpoint/content-for-response-action.png)

回應具有下列屬性：

| 屬性（顯示） | Property (JSON) | 描述 |
|--------------------|-----------------|-------------|
| **狀態碼** | `statusCode` | 要在傳入要求的回應中使用的 HTTPS 狀態碼。 此代碼可以是任何以 2xx、4xx 或 5xx 開頭的有效狀態碼。 但是，不允許 3xx 狀態碼。 |
| **標題** | `headers` | 要包含在回應中的一個或多個標頭 |
| **人體** | `body` | Body 物件，可以是字串、JSON 物件，甚至是上一個步驟所參考的二進位內容。 |
||||

若要查看回應動作的 JSON 定義和邏輯應用程式的完整 JSON 定義，請在邏輯應用程式設計工具工具列上選取 [程式**代碼視圖**]。

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

**答**： Azure 會使用[共用存取簽章（SAS）](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)安全地產生邏輯應用程式回呼 url。 此簽章會傳遞做為查詢參數，而且必須在邏輯應用程式可以執行之前進行驗證。 Azure 會使用每個邏輯應用程式、觸發程序名稱以及要執行作業之秘密金鑰的唯一組合來產生簽章。 因此，除非某人具有邏輯應用程式秘密金鑰的存取權，否則他們無法產生有效的簽章。

> [!IMPORTANT]
> 對於生產和較高的安全性系統，我們強烈建議您不要直接從瀏覽器呼叫邏輯應用程式，原因如下：
>
> * URL 中出現共用存取金鑰。
> * 因為跨 Azure Logic Apps 客戶的共用網域，所以無法管理安全性內容原則。

#### <a name="q-can-i-configure-callable-endpoints-further"></a>問：我可以進一步設定可呼叫的端點嗎？

**答**：是，HTTPS 端點透過[Azure API 管理](../api-management/api-management-key-concepts.md)來支援更多的設定。 此服務也可讓您透過一致的方式管理所有 API，包括邏輯應用程式、設定自訂網域名稱、使用其他驗證方法等等，例如︰

* [變更要求方法](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [變更要求的 URL 區段](../api-management/api-management-transformation-policies.md#RewriteURL)
* 在[Azure 入口網站](https://portal.azure.com/)中設定您的 API 管理網域
* 設定檢查基本驗證的原則

## <a name="next-steps"></a>後續步驟

* [使用 Azure Logic Apps 接收和回應連入的 HTTPS 呼叫](../connectors/connectors-native-reqres.md)
