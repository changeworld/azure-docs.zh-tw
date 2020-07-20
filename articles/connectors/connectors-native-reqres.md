---
title: 使用 HTTPS 接收和回應呼叫
description: 使用 Azure Logic Apps 處理來自外部服務的輸入 HTTPS 要求
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
tags: connectors
ms.openlocfilehash: 25aafee59c7f5f7ae59aa2fd7871de8926907f68
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261373"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>在 Azure Logic Apps 中接收和回應輸入 HTTPS 要求

您可以透過 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 及內建的要求觸發程序和回應動作，建立自動化工作和工作流程，以接收和回應傳入的 HTTPS 要求。 例如，您可以讓邏輯應用程式：

* 接收和回應以內部部署資料庫中的資料為對象的 HTTPS 要求。

* 在發生外部 Webhook 事件時觸發工作流程。

* 接收和回應來自另一個邏輯應用程式的 HTTPS 呼叫。

要求觸發程序支援 [Azure Active Directory 開放式驗證](/azure/active-directory/develop/) (Azure AD OAuth)，以授權對邏輯應用程式的輸入呼叫。 如需啟用此驗證的詳細資訊，請參閱[在 Azure Logic Apps 中保護存取和資料 - 啟用 Azure AD OAuth 驗證](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [邏輯應用程式](../logic-apps/logic-apps-overview.md)的基本知識。 如果您不熟悉邏輯應用程式，請了解[如何建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="tls-support"></a>

## <a name="transport-layer-security-tls"></a>傳輸層安全性 (TLS)

* 輸入呼叫*僅*支援 TLS) 1.2 (傳輸層安全性。 如果收到 TLS 交握錯誤，請確定您使用 TLS 1.2。 如需詳細資訊，請參閱[解決 TLS 1.0 問題](https://docs.microsoft.com/security/solving-tls1-problem)。 輸出呼叫支援以目標端點的功能為基礎的 TLS 1.0、1.1 和1.2。

* 輸入呼叫支援下列加密套件：

  * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384

  * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

  * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384

  * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256

  * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384

  * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256

  * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

  * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

<a name="add-request"></a>

## <a name="add-request-trigger"></a>新增要求觸發程序

這個內建的觸發程序會建立可手動呼叫的 HTTPS 端點，而此端點「只」能接收傳入的 HTTPS 要求。 當此事件發生時，這個觸發程序就會引發並執行邏輯應用程式。 如需此觸發程序的基礎 JSON 定義及如何呼叫此觸發程序的詳細資訊，請參閱[要求觸發程序類型](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)和[在 Azure Logic Apps 中使用 HTTPS 端點來呼叫、觸發或巢狀工作流程](../logic-apps/logic-apps-http-endpoint.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 建立空白邏輯應用程式。

1. 在邏輯應用程式設計工具開啟之後，請在 [搜尋] 方塊中輸入 `http request` 作為篩選件。 從觸發程序清單中，選取 [收到 HTTP 要求時] 觸發程序，這是邏輯應用程式工作流程中的第一個步驟。

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

      ![從承載產生結構描述](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. 輸入範例承載，然後選取 [完成]。

      ![從承載產生結構描述](./media/connectors-native-reqres/enter-payload.png)

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

1. 若要檢查撥入電話是否有符合您指定之架構的要求主體，請遵循下列步驟：

   1. 在要求觸發程式的標題列中，選取省略號按鈕 (**...** ]) 。

   1. 在觸發程式的 [設定] 中，開啟 [**架構驗證**]，然後選取 [**完成**]。

      如果撥入電話的要求主體不符合您的架構，觸發程式會傳回 `HTTP 400 Bad Request` 錯誤。

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
   > 如果您想要在 **#** 呼叫要求觸發程式時，在 URI 中包含 hash 或井字型大小 () ，請改為使用此編碼版本：`%25%23`

1. 若要觸發邏輯應用程式，請將 HTTP POST 傳送至產生的 URL。

   例如，您可以使用 [Postman](https://www.getpostman.com/) 之類的工具來傳送 HTTP POST。 如果您[啟用 Azure Active Directory 開啟式驗證](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) (Azure AD OAuth)，以授權對要求觸發程序的輸入呼叫，請使用[共用存取簽章 (SAS) URL](../logic-apps/logic-apps-securing-a-logic-app.md#sas) 或驗證權杖來呼叫觸發程序，但兩者不能同時使用。 驗證權杖必須在授權標頭中指定 `Bearer` 類型。 如需詳細資訊，請參閱[在 Azure Logic Apps 中保護存取和資料 - 存取以要求為基礎的觸發程序](../logic-apps/logic-apps-securing-a-logic-app.md#secure-triggers)。

如需此觸發程序的基礎 JSON 定義及如何呼叫此觸發程序的詳細資訊，請參閱這些主題：[要求觸發程序類型](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)和[在 Azure Logic Apps 中使用 HTTP 端點來呼叫、觸發或巢狀工作流程](../logic-apps/logic-apps-http-endpoint.md)。

### <a name="trigger-outputs"></a>觸發程序輸出

關於要求觸發程序的輸出，詳細資訊如下：

| JSON 屬性名稱 | 資料類型 | 描述 |
|--------------------|-----------|-------------|
| `headers` | Object | JSON 物件，描述要求的標頭 |
| `body` | Object | JSON 物件，描述要求的本文內容 |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>新增回應動作

您可以使用回應動作，以承載 (資料) 來回應傳入的 HTTPS 要求，但僅限於由 HTTPS 要求觸發的邏輯應用程序。 您可以在工作流程中的任何地方新增回應動作。 關於此觸發程序的基礎 JSON 定義，如需詳細資訊，請參閱[回應動作類型](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)。

邏輯應用程式只在[有限時間](../logic-apps/logic-apps-limits-and-config.md#request-limits)內保持開啟傳入要求。 假設邏輯應用程式工作流程包含回應動作，如果邏輯應用程式在此時間過後沒有傳回任何回應，則邏輯應用程式會將 `504 GATEWAY TIMEOUT` 傳回給呼叫者。 另外，如果邏輯應用程式不含回應動作，則邏輯應用程式會立即將 `202 ACCEPTED` 回應傳回給呼叫者。

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

1. 在 [選擇動作] 下的搜尋方塊中，輸入「回應」作為篩選條件，然後選取 [回應] 動作。

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

* [適用於 Logic Apps 的連接器](../connectors/apis-list.md)
