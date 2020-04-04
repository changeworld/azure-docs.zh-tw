---
title: 使用 HTTPS 接收及回應通話
description: 使用 Azure 邏輯應用處理來自外部服務的入站 HTTPS 請求
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 1885d7f8713b3801ce0c9846b7a8509b3864032a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656308"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>在 Azure 邏輯應用接收和回應入站 HTTPS 請求

使用[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)和內建的請求觸發器或回應操作,您可以建立自動任務和工作流來接收和回應傳入的 HTTPS 請求。 例如,您可以擁有邏輯應用:

* 接收並回應本地資料庫中的數據 HTTPS 請求。
* 當發生外部 Webhook 事件時觸發工作流。
* 從其他邏輯應用接收和回應 HTTPS 調用。

> [!NOTE]
> 請求觸發器*僅*支援傳入呼叫的傳輸層安全性 (TLS) 1.2。 傳出呼叫繼續支援 TLS 1.0、1.1 和 1.2。 有關詳細資訊,請參閱解決[TLS 1.0 問題](https://docs.microsoft.com/security/solving-tls1-problem)。
>
> 如果您看到 TLS 握手錯誤,請確保使用 TLS 1.2。 對於傳入呼叫,以下是支援的密碼套件:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果沒有訂閱,可以[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 關於[邏輯應用](../logic-apps/logic-apps-overview.md)的基本知識。 如果您是邏輯應用的新功能,請[瞭解如何建立第一個邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-request"></a>

## <a name="add-request-trigger"></a>新增要求觸發器

此內建觸發器創建一個手動呼叫的 HTTPS 終結點,該終結點*只能*接收傳入的 HTTPS 請求。 發生此事件時,觸發器將觸發並運行邏輯應用。 有關觸發器的基礎 JSON 定義以及如何呼叫此觸發器的詳細資訊,請參閱 Azure 邏輯應用中使用 HTTP 終結點的請求[觸發器類型](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)和[呼叫、觸發器或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 建立空白邏輯應用程式。

1. 邏輯應用設計器打開後,在搜尋框中,輸入"http 請求"作為篩選器。 從觸發器清單中選擇 **「何時收到 HTTP 請求**」觸發器,這是邏輯應用工作流中的第一步。

   ![選擇要求觸發器](./media/connectors-native-reqres/select-request-trigger.png)

   要求觸發器顯示以下屬性:

   ![要求觸發程序](./media/connectors-native-reqres/request-trigger.png)

   | 屬性名稱 | JSON 屬性名稱 | 必要 | 描述 |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {無} | 是 | 儲存邏輯應用程式後產生的終結點網址,用於呼叫邏輯應用 |
   | **要求正文 JSON 架構** | `schema` | 否 | 描述傳入要求正文中的屬性和值的 JSON 架構 |
   |||||

1. 在 **"請求正文 JSON 架構'** 框中,可以選擇輸入 JSON 架構,該架構描述傳入請求中的正文,例如:

   ![JSON 架構範例](./media/connectors-native-reqres/provide-json-schema.png)

   設計器使用此架構為請求中的屬性生成權杖。 這樣,邏輯應用就可以分析、使用數據,並將數據從請求傳遞到觸發器到工作流中。

   下面是範例架構:

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

   當您輸入 JSON 架構時,設計器會顯示一個`Content-Type`鬧鐘, 以在請求中包括標頭,並將`application/json`該標頭 值設定為 。 有關詳細資訊,請參閱[處理內容類型](../logic-apps/logic-apps-content-type.md)。

   ![提醒包括內容類型標頭](./media/connectors-native-reqres/include-content-type.png)

   下面是 JSON 格式的標頭外觀:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   要生成基於預期有效負載(資料)的 JSON 架構,可以使用[JSONSchema.net](https://jsonschema.net)等工具,也可以按照以下步驟操作:

   1. 在要求觸發程序中，選取 [使用範例承載來產生結構描述]****。

      ![從有效負載產生架構](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. 輸入範例有效負載,然後選擇 **"完成**"。

      ![從有效負載產生架構](./media/connectors-native-reqres/enter-payload.png)

      下面是範例有效負載:

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

1. 要指定其他屬性,請打開 **「添加新參數**清單」,然後選擇要添加的參數。

   | 屬性名稱 | JSON 屬性名稱 | 必要 | 描述 |
   |---------------|--------------------|----------|-------------|
   | **方法** | `method` | 否 | 傳入要求必須用於呼叫邏輯應用程式 |
   | **相對路徑** | `relativePath` | 否 | 邏輯應用的終結點網址 可以接受的參數的相對路徑 |
   |||||

   此範例加入**方法**屬性:

   ![新增方法參數](./media/connectors-native-reqres/add-parameters.png)

   **方法**屬性將顯示在觸發器中,以便您可以從清單中選擇方法。

   ![Select 方法](./media/connectors-native-reqres/select-method.png)

1. 現在,添加另一個操作作為工作流中的下一步。 在觸發器下,選擇 **「下一步」** 以便找到要添加的操作。

   例如,您可以通過[添加回應操作](#add-response)來回應請求,可用於返回自定義回應,本文稍後將對此進行說明。

   邏輯應用僅保持傳入請求打開一分鐘。 假設邏輯應用工作流包含回應操作,如果邏輯應用在此時間過後未返回回應,則邏輯應用將返回`504 GATEWAY TIMEOUT`給調用方。 否則,如果邏輯應用不包含回應操作,則邏輯應用會立即返回對調用方的`202 ACCEPTED`回應。

1. 完成後，儲存邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。 

   此步驟將生成用於發送觸發邏輯應用的請求的 URL。 要複製此 URL,請選擇 URL 旁邊的複製圖示。

   ![用於觸發邏輯應用的網址](./media/connectors-native-reqres/generated-url.png)

1. 要觸發邏輯應用,請向生成的 URL 發送 HTTP POST。 例如,您可以使用郵[遞](https://www.getpostman.com/)員等工具。

### <a name="trigger-outputs"></a>觸發程序輸出

以下是有關請求觸發器輸出的詳細資訊:

| JSON 屬性名稱 | 資料類型 | 描述 |
|--------------------|-----------|-------------|
| `headers` | Object | 描述要求標頭的 JSON 物件 |
| `body` | Object | 描述要求中的正文內容的 JSON 物件 |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>新增回應作業

您可以使用回應操作回應負載(資料)以回應傳入的 HTTPS 請求,但只能在由 HTTPS 請求觸發的邏輯應用中進行回應。 您可以在工作流中的任何點添加回應操作。 有關此觸發器的基礎 JSON 定義的詳細資訊,請參閱[回應操作類型](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)。

邏輯應用僅保持傳入請求打開一分鐘。 假設邏輯應用工作流包含回應操作,如果邏輯應用在此時間過後未返回回應,則邏輯應用將返回`504 GATEWAY TIMEOUT`給調用方。 否則,如果邏輯應用不包含回應操作,則邏輯應用會立即返回對調用方的`202 ACCEPTED`回應。

> [!IMPORTANT]
> 如果回應操作包含這些標頭,邏輯應用將從生成的回應消息中刪除這些標頭,而不會顯示任何警告或錯誤:
>
> * `Allow`
> * `Content-*`除這些例外情況外:`Content-Disposition``Content-Encoding`和`Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> 儘管邏輯應用不會阻止您保存具有這些標頭的回應操作的邏輯應用,但邏輯應用會忽略這些標頭。

1. 在邏輯應用設計器中,在要添加回應操作的步驟下,選擇 **「新建步驟**」。。

   例如,使用前面的請求觸發器:

   ![新增新步驟](./media/connectors-native-reqres/add-response.png)

   要在步驟之間添加操作,請將指標移到這些步驟之間的箭頭上。 選擇顯示的加號**+**(),然後選擇 **「添加操作**」。。

1. 在搜尋框中**選擇操作**,輸入"回應"作為篩選器,然後選擇 **"回應**"操作。

   ![選擇回應操作](./media/connectors-native-reqres/select-response-action.png)

   為了簡單起見,在此示例中將摺疊請求觸發器。

1. 添加回應消息所需的任何值。 

   在某些欄位中,按下其框內將打開動態內容清單。 然後,您可以選擇表示工作流中以前步驟中可用輸出的權杖。 前面示例中指定的架構中的屬性現在顯示在動態內容清單中。

   例如,對於 **"標題"** 框,`Content-Type`請作為 鍵名稱包含,並將鍵`application/json`值設置為 本主題前面所述。 對於 **「正文」** 框,您可以從動態內容清單中選擇觸發器正文輸出。

   ![回應操作詳細資訊](./media/connectors-native-reqres/response-details.png)

   要以 JSON 格式檢視標題,請選擇「**切換到文字檢視**」 。

   ![標題 - 切換到文字檢視](./media/connectors-native-reqres/switch-to-text-view.png)

   以下是有關可在回應操作中設置的屬性的詳細資訊。 

   | 屬性名稱 | JSON 屬性名稱 | 必要 | 描述 |
   |---------------|--------------------|----------|-------------|
   | **狀態代碼** | `statusCode` | 是 | 要回覆回狀態代碼 |
   | **標頭** | `headers` | 否 | 描述要包含在回應中的一個或多個標頭的 JSON 物件 |
   | **內文** | `body` | 否 | 回應本文 |
   |||||

1. 要指定其他屬性(如回應體的 JSON 架構),請打開 **「添加新參數**清單」,然後選擇要添加的參數。

1. 完成後，儲存邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。 

## <a name="next-steps"></a>後續步驟

* [適用於 Logic Apps 的連接器](../connectors/apis-list.md)
