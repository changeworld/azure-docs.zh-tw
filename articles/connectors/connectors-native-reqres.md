---
title: 使用 HTTPS 接收和回應呼叫
description: 使用 Azure Logic Apps 處理來自外部服務的輸入 HTTPS 要求
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: c0e8743d78c8eeafb5bdeb6ade783d5e75991f91
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94330983"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>在 Azure Logic Apps 中接收和回應輸入 HTTPS 要求

透過 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和內建的要求觸發程式和回應動作，您可以建立可透過 HTTPS 接收輸入要求的自動化工作和工作流程。 若要改為傳送輸出要求，請使用內建的 [HTTP 觸發程式或 HTTP 動作](../connectors/connectors-native-http.md)。

例如，您可以讓邏輯應用程式：

* 接收和回應以內部部署資料庫中的資料為對象的 HTTPS 要求。

* 在發生外部 Webhook 事件時觸發工作流程。

* 接收和回應來自另一個邏輯應用程式的 HTTPS 呼叫。

本文說明如何使用要求觸發程式和回應動作，讓您的邏輯應用程式可以接收和回應傳入的呼叫。

如需對邏輯應用程式進行輸入呼叫的安全性、授權和加密的詳細資訊（例如 [傳輸層安全性 (TLS) ](https://en.wikipedia.org/wiki/Transport_Layer_Security)，之前稱為安全通訊端層 (SSL) 、使用 Azure API 管理來公開邏輯應用 [程式，或 ](../active-directory/develop/index.yml)限制發出輸入呼叫的 IP 位址，請參閱 [對要求型觸發程式進行輸入呼叫的安全存取和資料存取](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)。

## <a name="prerequisites"></a>Prerequisites

* Azure 帳戶和訂用帳戶。 如果您沒有訂用帳戶，您可以[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 如果您不熟悉邏輯應用程式，請參閱 [什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md)？

<a name="add-request"></a>

## <a name="add-request-trigger"></a>新增要求觸發程序

此內建觸發程式會建立可透過 HTTPS *只* 處理輸入要求的手動可呼叫端點。 當呼叫端將要求傳送至此端點時， [要求觸發](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) 程式會引發並執行邏輯應用程式。 如需如何呼叫此觸發程式的詳細資訊，請參閱 [在 Azure Logic Apps 中呼叫、觸發或使用 HTTPS 端點來嵌套工作流程](../logic-apps/logic-apps-http-endpoint.md)。

您的邏輯應用程式只會在 [有限的時間](../logic-apps/logic-apps-limits-and-config.md#request-limits)內將輸入要求保持開啟狀態。 假設您的邏輯應用程式包含 [回應動作](#add-response)，如果您的邏輯應用程式未在這段時間過後將回應傳回給呼叫者，則邏輯應用程式會將 `504 GATEWAY TIMEOUT` 狀態傳回給呼叫者。 如果您的邏輯應用程式未包含回應動作，您的邏輯應用程式會立即將 `202 ACCEPTED` 狀態傳回給呼叫者。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 建立空白邏輯應用程式。

1. 在邏輯應用程式設計工具開啟之後，請在 [搜尋] 方塊中輸入 `http request` 作為篩選件。 從觸發程式清單中，選取 [ **收到 HTTP 要求時** ] 觸發程式。

   ![選取要求觸發程序](./media/connectors-native-reqres/select-request-trigger.png)

   要求觸發程序會顯示這些屬性：

   ![要求觸發程序](./media/connectors-native-reqres/request-trigger.png)

   | 屬性名稱 | JSON 屬性名稱 | 必要 | 描述 |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {無} | 是 | 在您儲存邏輯應用程式之後產生的端點 URL，用於呼叫邏輯應用程式 |
   | **要求本文 JSON 結構描述** | `schema` | 否 | JSON 結構描述，描述傳入要求本文中的屬性和值 |
   |||||

1. 在 [要求本文 JSON 結構描述] 方塊中，選擇性地輸入 JSON 結構描述，以描述傳入要求中的本文，例如：

   ![JSON 結構描述範例](./media/connectors-native-reqres/provide-json-schema.png)

   設計工具會使用此結構描述，為要求中的屬性產生權杖。 如此一來，邏輯應用程式就可以剖析、取用來自要求的資料，並經過觸發程序將資料傳遞至工作流程。

   以下是結構描述範例：

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

   當您輸入 JSON 結構描述時，設計工具會提醒您在要求中包含 `Content-Type` 標頭，並將該標頭值設為 `application/json`。 如需詳細資訊，請參閱[處理內容類型](../logic-apps/logic-apps-content-type.md)。

   ![提醒要包含 "Content-type" 標頭](./media/connectors-native-reqres/include-content-type.png)

   以下是此標頭以 JSON 格式顯示的樣子：

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   若要產生以預期的承載 (資料) 為基礎的 JSON 結構描述，您可以使用 [JSONSchema.net](https://jsonschema.net) 之類的工具，也可以遵循下列步驟：

   1. 在要求觸發程序中，選取 [使用範例承載來產生結構描述]。

      ![已選取 [使用範例承載來產生架構] 的螢幕擷取畫面](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. 輸入範例承載，然後選取 [完成]。

      ![輸入範例承載以產生架構](./media/connectors-native-reqres/enter-payload.png)

      以下是承載範例：

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

1. 若要檢查輸入呼叫是否有符合指定架構的要求本文，請遵循下列步驟：

   1. 在要求觸發程式的標題列中，選取省略號按鈕 ( **...** ) 。

   1. 在觸發程式的設定中，開啟 **架構驗證** ，然後選取 [ **完成** ]。

      如果撥入電話的要求本文不符合您的架構，則觸發程式會傳回 `HTTP 400 Bad Request` 錯誤。

1. 若要指定其他屬性，請開啟 [新增參數] 清單，然後選取您要新增的參數。

   | 屬性名稱 | JSON 屬性名稱 | 必要 | 描述 |
   |---------------|--------------------|----------|-------------|
   | **方法** | `method` | 否 | 傳入要求在呼叫邏輯應用程式時必須使用的方法 |
   | **相對路徑** | `relativePath` | 否 | 參數的相對路徑，指邏輯應用程式的端點 URL 可接受的參數 |
   |||||

   此範例新增 [方法] 屬性：

   ![新增方法參數](./media/connectors-native-reqres/add-parameters.png)

   [方法] 屬性會出現在觸發程序中，讓您可以從清單中選取方法。

   ![選取方法](./media/connectors-native-reqres/select-method.png)

1. 現在，新增另一個動作，作為工作流程的下一步。 在觸發程序下，選取 [下一步]，以尋找您想要新增的動作。

   例如，您可以[新增回應動作](#add-response)來回應要求，此動作可用來傳回自訂的回應，本主題稍後會說明。

   邏輯應用程式只在[有限時間](../logic-apps/logic-apps-limits-and-config.md#request-limits)內保持開啟傳入要求。 假設邏輯應用程式工作流程包含回應動作，如果邏輯應用程式在此時間過後沒有傳回任何回應，則邏輯應用程式會將 `504 GATEWAY TIMEOUT` 傳回給呼叫者。 另外，如果邏輯應用程式不含回應動作，則邏輯應用程式會立即將 `202 ACCEPTED` 回應傳回給呼叫者。

1. 完成後，儲存邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

   此步驟會產生 URL，用以傳送要求來觸發邏輯應用程式。 若要複製此 URL，請選取 URL 旁邊的複製圖示。

   ![用於觸發邏輯應用程式的 URL](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > 如果您想要在 **#** 呼叫要求觸發程式時，在 URI 中包含雜湊或 # 符號 () ，請改用此編碼版本： `%25%23`

1. 若要觸發邏輯應用程式，請將 HTTP POST 傳送至產生的 URL。

   例如，您可以使用 [Postman](https://www.getpostman.com/) 之類的工具來傳送 HTTP POST。 如需此觸發程序的基礎 JSON 定義及如何呼叫此觸發程序的詳細資訊，請參閱這些主題：[要求觸發程序類型](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)和[在 Azure Logic Apps 中使用 HTTP 端點來呼叫、觸發或巢狀工作流程](../logic-apps/logic-apps-http-endpoint.md)。

如需對邏輯應用程式進行輸入呼叫的安全性、授權和加密的詳細資訊（例如 [傳輸層安全性 (TLS) ](https://en.wikipedia.org/wiki/Transport_Layer_Security)，之前稱為安全通訊端層 (SSL) 、使用 Azure API 管理來公開邏輯應用 [程式，或 ](../active-directory/develop/index.yml)限制發出輸入呼叫的 IP 位址，請參閱 [對要求型觸發程式進行輸入呼叫的安全存取和資料存取](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)。

## <a name="trigger-outputs"></a>觸發程序輸出

關於要求觸發程序的輸出，詳細資訊如下：

| JSON 屬性名稱 | 資料類型 | 描述 |
|--------------------|-----------|-------------|
| `headers` | Object | JSON 物件，描述要求的標頭 |
| `body` | Object | JSON 物件，描述要求的本文內容 |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>新增回應動作

當您使用要求觸發程式來處理輸入要求時，您可以使用內建的 [回應動作](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)來建立回應的模型，並將承載結果傳回給呼叫者。 您只能對要求觸發程式使用 *回應動作。* 此組合與要求觸發程式和回應動作會建立 [要求-回應模式](https://en.wikipedia.org/wiki/Request%E2%80%93response)。 除了 Foreach 迴圈和 Until 迴圈以及平行分支以外，您也可以在工作流程中的任何位置新增回應動作。

> [!IMPORTANT]
> 如果回應動作包含以下標頭，Logic Apps 會從產生的回應訊息中移除這些標頭，但不會顯示任何警告或錯誤：
>
> * `Allow`
> * `Content-*`，但這些除外：`Content-Disposition`、`Content-Encoding` 和 `Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> 如果邏輯應用程式的回應動作有這些標頭，雖然 Logic Apps 不會阻止您儲存邏輯應用程式，但 Logic Apps 會忽略這些標頭。

1. 在邏輯應用程式設計工具中，請在您要新增回應動作的步驟下，選取 [新增步驟]。

   以先前的要求觸發程序為例：

   ![新增步驟](./media/connectors-native-reqres/add-response.png)

   若要在步驟之間新增動作，請將指標移至這些步驟之間的箭頭上。 選擇出現的加號 ( **+** )，然後選取 [新增動作]。

1. 在 [ **選擇動作** ] 下的 [搜尋] 方塊中，輸入 `response` 做為篩選準則，然後選取 **回應** 動作。

   ![選取回應動作](./media/connectors-native-reqres/select-response-action.png)

   為了簡單起見，此範例中已摺疊要求觸發程序。

1. 新增回應訊息所需的任何值。

   在某些欄位中，按一下其方塊會開啟動態內容清單。 然後，您可以選取權杖，以代表工作流程中先前步驟可用的輸出。 先前範例所指定結構描述中的屬性，現在會出現在動態內容清單中。

   例如，在 [標頭] 方塊中，加入 `Content-Type` 作為索引鍵名稱，並將索引鍵值設為 `application/json`，如本主題稍早所述。 在 [本文] 方塊中，您可以從動態內容清單中選取觸發程序本文輸出。

   ![回應動作詳細資料](./media/connectors-native-reqres/response-details.png)

   若要以 JSON 格式來檢視標頭，請選取 [切換至文字檢視]。

   ![標頭 - 切換至文字檢視](./media/connectors-native-reqres/switch-to-text-view.png)

   關於您在回應動作中可設定的屬性，詳細資訊如下。

   | 屬性名稱 | JSON 屬性名稱 | 必要 | 描述 |
   |---------------|--------------------|----------|-------------|
   | **狀態碼** | `statusCode` | 是 | 要在回應中傳回的狀態碼 |
   | **標頭** | `headers` | 否 | JSON 物件，描述要包含在回應中的一個或多個標頭 |
   | **本文** | `body` | 否 | 回應本文 |
   |||||

1. 若要指定其他屬性，例如回應本文的 JSON 結構描述，請開啟 [新增參數] 清單，然後選取您要新增的參數。

1. 完成後，儲存邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

## <a name="next-steps"></a>後續步驟

* [對要求型觸發程式的撥入電話安全存取和資料存取](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [適用於 Logic Apps 的連接器](../connectors/apis-list.md)
