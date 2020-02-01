---
title: 接收和回應 HTTPS 呼叫
description: 使用 Azure Logic Apps 即時處理 HTTPS 要求和事件
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 01/14/2020
tags: connectors
ms.openlocfilehash: 0949e50c5a4993dfbcc83b41ef01d2cea82350a8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900274"
---
# <a name="receive-and-respond-to-incoming-https-calls-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 接收和回應連入的 HTTPS 呼叫

透過[Azure Logic Apps](../logic-apps/logic-apps-overview.md)和內建的要求觸發程式或回應動作，您可以建立自動化的工作和工作流程，以接收和回應連入的 HTTPS 要求。 例如，您可以有邏輯應用程式：

* 接收和回應內部部署資料庫中資料的 HTTPS 要求。
* 在發生外部 webhook 事件時觸發工作流程。
* 接收並回應來自另一個邏輯應用程式的 HTTPS 呼叫。

> [!NOTE]
> 要求觸發程式*僅*支援連入呼叫的傳輸層安全性（TLS）1.2。 撥出電話會繼續支援 TLS 1.0、1.1 和1.2。 如需詳細資訊，請參閱[解決 TLS 1.0 問題](https://docs.microsoft.com/security/solving-tls1-problem)。
>
> 如果您看到 SSL 交握錯誤，請確定您使用的是 TLS 1.2。 對於傳入的呼叫，以下是支援的加密套件：
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [邏輯應用程式](../logic-apps/logic-apps-overview.md)的基本知識。 如果您不熟悉邏輯應用程式，請瞭解[如何建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-request"></a>

## <a name="add-request-trigger"></a>新增要求觸發程式

此內建觸發程式會建立可*只*接收傳入 HTTPs 要求的手動呼叫 HTTPs 端點。 當此事件發生時，觸發程式會引發並執行邏輯應用程式。 如需有關觸發程式的基礎 JSON 定義和如何呼叫此觸發程式的詳細資訊，請參閱[要求觸發程式類型](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)，以及[在 AZURE LOGIC APPS 中使用 HTTP 端點呼叫、觸發或將工作流程嵌套](../logic-apps/logic-apps-http-endpoint.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 建立空白邏輯應用程式。

1. 邏輯應用程式設計工具開啟之後，在搜尋方塊中，輸入「HTTP 要求」作為篩選準則。 從 [觸發程式] 清單中，選取 [**收到 HTTP 要求時**] 觸發程式，這是邏輯應用程式工作流程中的第一個步驟。

   ![選取要求觸發程式](./media/connectors-native-reqres/select-request-trigger.png)

   要求觸發程式會顯示這些屬性：

   ![要求觸發程序](./media/connectors-native-reqres/request-trigger.png)

   | 屬性名稱 | JSON 屬性名稱 | 必要項 | 說明 |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {無} | 是 | 在您儲存邏輯應用程式並用於呼叫邏輯應用程式之後，所產生的端點 URL |
   | **要求本文 JSON 架構** | `schema` | 否 | 描述傳入要求主體中的屬性和值的 JSON 架構 |
   |||||

1. 在 [**要求本文 Json 架構**] 方塊中，選擇性地輸入 JSON 架構來描述傳入要求中的主體，例如：

   ![範例 JSON 架構](./media/connectors-native-reqres/provide-json-schema.png)

   設計工具會使用此架構來產生要求中屬性的權杖。 如此一來，您的邏輯應用程式就可以在您的工作流程中，從要求剖析、取用及傳遞資料至您的工作流程。

   以下是範例架構：

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   當您輸入 JSON 架構時，設計工具會顯示要在要求中包含 `Content-Type` 標頭，並將該標頭值設為 `application/json`的提醒。 如需詳細資訊，請參閱[處理內容類型](../logic-apps/logic-apps-content-type.md)。

   ![要包含 "Content-type" 標頭的提醒](./media/connectors-native-reqres/include-content-type.png)

   以下是此標頭以 JSON 格式顯示的樣子：

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   若要產生以預期的承載（資料）為基礎的 JSON 架構，您可以使用像是[JSONSchema.net](https://jsonschema.net)的工具，也可以依照下列步驟執行：

   1. 在要求觸發程序中，選取 [使用範例承載來產生結構描述]。

      ![從承載產生架構](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. 輸入範例承載，然後選取 [**完成**]。

      ![從承載產生架構](./media/connectors-native-reqres/enter-payload.png)

      以下是範例承載：

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": { 
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. 若要指定其他屬性，請開啟 [**加入新的參數**] 清單，然後選取您想要新增的參數。

   | 屬性名稱 | JSON 屬性名稱 | 必要項 | 說明 |
   |---------------|--------------------|----------|-------------|
   | **方法** | `method` | 否 | 傳入要求必須用來呼叫邏輯應用程式的方法 |
   | **相對路徑** | `relativePath` | 否 | 邏輯應用程式的端點 URL 可以接受之參數的相對路徑 |
   |||||

   這個範例會新增**Method**屬性：

   ![新增方法參數](./media/connectors-native-reqres/add-parameters.png)

   [**方法**] 屬性會出現在觸發程式中，讓您可以從清單中選取方法。

   ![選取方法](./media/connectors-native-reqres/select-method.png)

1. 現在，新增另一個動作做為工作流程中的下一個步驟。 在觸發程式底下，選取 **[下一步]** ，讓您可以找到想要新增的動作。

   例如，您可以藉由[新增回應動作](#add-response)來回應要求，讓您可以用來傳回自訂的回應，並在本主題稍後加以說明。

   您的邏輯應用程式只會將傳入要求保持開啟一分鐘。 假設您的邏輯應用程式工作流程包含回應動作，如果邏輯應用程式在這段時間過後不會傳迴響應，則邏輯應用程式會將 `504 GATEWAY TIMEOUT` 傳回給呼叫者。 否則，如果邏輯應用程式未包含回應動作，則邏輯應用程式會立即將 `202 ACCEPTED` 回應傳回給呼叫者。

1. 完成後，儲存邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。 

   此步驟會產生 URL，以用來傳送觸發邏輯應用程式的要求。 若要複製此 URL，請選取 URL 旁邊的複製圖示。

   ![用於觸發邏輯應用程式的 URL](./media/connectors-native-reqres/generated-url.png)

1. 若要觸發邏輯應用程式，請將 HTTP POST 傳送至產生的 URL。 例如，您可以使用[Postman](https://www.getpostman.com/)之類的工具。

### <a name="trigger-outputs"></a>觸發程序輸出

以下是來自要求觸發程式輸出的詳細資訊：

| JSON 屬性名稱 | Data type | 說明 |
|--------------------|-----------|-------------|
| `headers` | 物件 | 描述要求標頭的 JSON 物件 |
| `body` | 物件 | JSON 物件，描述來自要求的本文內容 |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>新增回應動作

您可以使用 [回應] 動作，以將承載（資料）回應到傳入的 HTTPS 要求，但僅限於由 HTTPS 要求觸發的邏輯應用程式。 您可以在工作流程中的任何時間點新增回應動作。 如需此觸發程式之基礎 JSON 定義的詳細資訊，請參閱[回應動作類型](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)。

您的邏輯應用程式只會將傳入要求保持開啟一分鐘。 假設您的邏輯應用程式工作流程包含回應動作，如果邏輯應用程式在這段時間過後不會傳迴響應，則邏輯應用程式會將 `504 GATEWAY TIMEOUT` 傳回給呼叫者。 否則，如果邏輯應用程式未包含回應動作，則邏輯應用程式會立即將 `202 ACCEPTED` 回應傳回給呼叫者。

1. 在邏輯應用程式設計工具中，于您要新增回應動作的步驟底下，選取 [**新增步驟**]。

   例如，使用較早的要求觸發程式：

   ![新增步驟](./media/connectors-native-reqres/add-response.png)

   若要在步驟之間新增動作，請將指標移到這些步驟之間的箭號上。 選取顯示的加號（ **+** ），然後選取 [**新增動作**]。

1. 在 [**選擇動作**] 下的 [搜尋] 方塊中，輸入「回應」作為篩選準則，然後選取 [**回應**] 動作。

   ![選取 [回應] 動作](./media/connectors-native-reqres/select-response-action.png)

   為了簡單起見，在此範例中，會折迭要求觸發程式。

1. 新增回應訊息所需的任何值。 

   在某些欄位中，按一下其方塊內會開啟動態內容清單。 然後，您可以從工作流程中的先前步驟選取代表可用輸出的權杖。 在先前範例中指定的架構屬性現在會出現在動態內容清單中。

   例如，針對 [**標頭**] 方塊，將 `Content-Type` 包含為索引鍵名稱，並將金鑰值設為 `application/json`，如本主題稍早所述。 針對 [**本文**] 方塊，您可以從動態內容清單中選取觸發程式主體輸出。

   ![回應動作詳細資料](./media/connectors-native-reqres/response-details.png)

   若要以 JSON 格式來查看標頭，請選取 [**切換到文字視圖**]。

   ![標頭-切換至文字視圖](./media/connectors-native-reqres/switch-to-text-view.png)

   以下是您可以在 [回應] 動作中設定之屬性的詳細資訊。 

   | 屬性名稱 | JSON 屬性名稱 | 必要項 | 說明 |
   |---------------|--------------------|----------|-------------|
   | **狀態碼** | `statusCode` | 是 | 要在回應中傳回的狀態碼 |
   | **標頭** | `headers` | 否 | JSON 物件，描述要包含在回應中的一個或多個標頭 |
   | **本文** | `body` | 否 | 回應本文 |
   |||||

1. 若要指定其他屬性（例如回應主體的 JSON 架構），請開啟 [**加入新的參數**] 清單，然後選取您想要新增的參數。

1. 完成後，儲存邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。 

## <a name="next-steps"></a>後續步驟

* [Logic Apps 的連接器](../connectors/apis-list.md)
