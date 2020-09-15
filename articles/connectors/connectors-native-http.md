---
title: 使用 HTTP 或 HTTPS 呼叫服務端點
description: 從 Azure Logic Apps 將輸出 HTTP 或 HTTPS 要求傳送至服務端點
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/14/2020
tags: connectors
ms.openlocfilehash: 5f6328144760b3c55c55fbef13917359fa9e1a62
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526736"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>從 Azure Logic Apps 透過 HTTP 或 HTTPS 呼叫服務端點

使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和內建的 HTTP 觸發程式或動作，您可以建立自動化的工作和工作流程，以透過 HTTP 或 HTTPS 將輸出要求傳送至其他服務和系統上的端點。 若要改為接收及回應輸入的 HTTPS 呼叫，請使用內建的 [要求觸發程式和回應動作](../connectors/connectors-native-reqres.md)。

例如，您可以藉由檢查特定排程上的端點，來監視網站的服務端點。 當指定的事件發生在該端點時（例如您的網站停止運作），事件會觸發邏輯應用程式的工作流程，並在該工作流程中執行動作。

* 若要依週期性排程檢查或 *輪詢* 端點，請 [新增 HTTP 觸發](#http-trigger) 程式作為工作流程中的第一個步驟。 每次觸發程式檢查端點時，觸發程式就會呼叫或將 *要求* 傳送至端點。 端點的回應會決定是否執行邏輯應用程式的工作流程。 觸發程式會將任何內容從端點的回應傳遞至邏輯應用程式中的動作。

* 若要從工作流程中的其他任何位置呼叫端點，請 [新增 HTTP 動作](#http-action)。 端點的回應會決定工作流程的剩餘動作如何執行。

本文說明如何使用 HTTP 觸發程式和 HTTP 動作，讓您的邏輯應用程式可以將輸出呼叫傳送給其他服務和系統。

如需從邏輯應用程式進行輸出呼叫的加密、安全性和授權的相關資訊（例如 [傳輸層安全性 (TLS) ](https://en.wikipedia.org/wiki/Transport_Layer_Security)，之前稱為安全通訊端層 (SSL) 、自我簽署的憑證，或 Azure Active Directory [ (的開放驗證 Azure AD ](../active-directory/develop/index.yml)，請參閱 [安全存取和資料存取，以存取其他服務和系統的輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)。

## <a name="prerequisites"></a>Prerequisites

* Azure 帳戶和訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您想要呼叫之目標端點的 URL

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 如果您不熟悉邏輯應用程式，請參閱 [什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md)？

* 您想要從中呼叫目標端點的邏輯應用程式。 若要開始使用 HTTP 觸發程式，請 [建立空白的邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP 動作，請使用您想要的任何觸發程式來啟動邏輯應用程式。 此範例會使用 HTTP 觸發程式作為第一個步驟。

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>新增 HTTP 觸發程式

此內建觸發程式會對端點的指定 URL 進行 HTTP 呼叫，並傳迴響應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟您的空白邏輯應用程式。

1. 在設計工具的搜尋方塊底下，選取 [ **內建**]。 在搜尋方塊中，輸入 `http` 作為篩選條件。 從 **觸發** 程式清單中，選取 **HTTP** 觸發程式。

   ![選取 HTTP 觸發程序](./media/connectors-native-http/select-http-trigger.png)

   此範例會將觸發程式重新命名為「HTTP 觸發程式」，讓步驟有更具描述性的名稱。 此外，此範例稍後會新增 HTTP 動作，而這兩個名稱必須是唯一的。

1. 提供要包含在目標端點呼叫中的 [HTTP 觸發程式參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) 值。 針對您想要讓觸發程式檢查目標端點的頻率，設定週期。

   ![輸入 HTTP 觸發程序參數](./media/connectors-native-http/http-trigger-parameters.png)

   如果您選取 [ **無**] 以外的驗證類型，則驗證設定會根據您的選取專案而有所不同。 如需有關 HTTP 可用驗證類型的詳細資訊，請參閱下列主題：

   * [將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [使用受控識別來驗證資源的存取權](../logic-apps/create-managed-service-identity.md)

1. 若要新增其他可用參數，開啟 [新增參數] 清單，然後選取您所需的參數。

1. 請使用當引發觸發程序時執行的動作，繼續建置邏輯應用程式的工作流程。

1. 當您完成時，請記得儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]  。

<a name="http-action"></a>

## <a name="add-an-http-action"></a>新增 HTTP 動作

此內建動作會對端點的指定 URL 進行 HTTP 呼叫，並傳迴響應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟邏輯應用程式。

   此範例會使用 HTTP 觸發程式作為第一個步驟。

1. 在您要新增 HTTP 動作的步驟下，選取 [ **新增步驟**]。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選擇所顯示的加號 ( **+** )，然後選取 [新增動作]。

1. 在 [選擇動作] 底下，選取 [內建]。 在搜尋方塊中，輸入 `http` 作為篩選條件。 從 [ **動作** ] 清單中選取 [ **HTTP** ] 動作。

   ![選取 HTTP 動作](./media/connectors-native-http/select-http-action.png)

   此範例會將動作重新命名為「HTTP 動作」，讓步驟有更具描述性的名稱。

1. 提供要包含在目標端點呼叫中的 [HTTP 動作參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) 值。

   ![輸入 HTTP 動作參數](./media/connectors-native-http/http-action-parameters.png)

   如果您選取 [ **無**] 以外的驗證類型，則驗證設定會根據您的選取專案而有所不同。 如需有關 HTTP 可用驗證類型的詳細資訊，請參閱下列主題：

   * [將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [使用受控識別來驗證資源的存取權](../logic-apps/create-managed-service-identity.md)

1. 若要新增其他可用參數，開啟 [新增參數] 清單，然後選取您所需的參數。

1. 當您完成時，請記得儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]  。

## <a name="trigger-and-action-outputs"></a>觸發程式和動作輸出

以下是 HTTP 觸發程式或動作之輸出的詳細資訊，會傳回下列資訊：

| 屬性 | 類型 | 描述 |
|----------|------|-------------|
| `headers` | JSON 物件 | 要求的標頭 |
| `body` | JSON 物件 | 具有來自要求之本文內容的物件 |
| `status code` | 整數 | 要求的狀態碼 |
|||

| 狀態碼 | 描述 |
|-------------|-------------|
| 200 | [確定] |
| 202 | 已接受 |
| 400 | 不正確的要求 |
| 401 | 未經授權 |
| 403 | 禁止 |
| 404 | 找不到 |
| 500 | 內部伺服器錯誤。 發生未知錯誤。 |
|||

## <a name="content-with-multipartform-data-type"></a>具有多部分/表單資料類型的內容

若要處理 `multipart/form-data` 在 HTTP 要求中具有類型的內容，您可以使用此格式，將包含和屬性的 JSON 物件新增 `$content-type` `$multipart` 至 HTTP 要求的主體。

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

例如，假設您有一個邏輯應用程式，使用該網站的 API （支援此類型），將 Excel 檔案的 HTTP POST 要求傳送至網站 `multipart/form-data` 。 以下是此動作可能的外觀：

![多部分表單資料](./media/connectors-native-http/http-action-multipart.png)

以下是在基礎工作流程定義中顯示 HTTP 動作之 JSON 定義的相同範例：

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="content-with-applicationx-www-form-urlencoded-type"></a>Content with application/x-www-表單 urlencoded 類型

若要在主體中提供 urlencoded 的資料給 HTTP 要求，您必須指定資料的 `application/x-www-form-urlencoded` 內容類型。 在 HTTP 觸發程式或動作中，新增 `content-type` 標頭。 將標頭值設定為 `application/x-www-form-urlencoded` 。

例如，假設您有一個邏輯應用程式，它會將 HTTP POST 要求傳送至支援此類型的網站 `application/x-www-form-urlencoded` 。 以下是此動作可能的外觀：

![顯示「content-type」標頭設定為 ' application/x-www-urlencoded ' 之 HTTP 要求的螢幕擷取畫面](./media/connectors-native-http/http-action-urlencoded.png)

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>非同步要求-回應行為

根據預設，Azure Logic Apps 中的所有 HTTP 型動作都會遵循標準的 [非同步作業模式](/azure/architecture/patterns/async-request-reply)。 這個模式會指定在 HTTP 動作呼叫或將要求傳送至端點、服務、系統或 API 之後，接收者會立即傳回「 [202 接受](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) 」回應。 這段程式碼會確認接收者已接受要求，但尚未完成處理。 回應可以包含 `location` 指定 URL 和重新整理識別碼的標頭，呼叫者可以用來輪詢或檢查非同步要求的狀態，直到接收者停止處理並傳回「 [200 正常](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) 」成功回應或其他非202回應。 不過，呼叫端不需要等待要求完成處理，而且可以繼續執行下一個動作。 如需詳細資訊，請參閱 [非同步微服務整合強制執行微服務自主性](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging)。

* 在邏輯應用程式設計工具中，HTTP 動作（而非觸發程式）的 **非同步模式** 設定預設為啟用。 這種設定會指定呼叫端不等候處理完成，而且可以繼續執行下一個動作，但會繼續檢查狀態，直到處理停止為止。 如果停用，則此設定會指定呼叫端等候處理完成，然後再繼續進行下一個動作。

  若要尋找此設定，請遵循下列步驟：

  1. 在 HTTP 動作的標題列上，選取省略號 (**...**) 按鈕，這會開啟動作的設定。

  1. 尋找 **非同步模式** 設定。

     ![「非同步模式」設定](./media/connectors-native-http/asynchronous-pattern-setting.png)

* HTTP 動作的基礎 JavaScript 物件標記法 (JSON) 定義會隱含地遵循非同步作業模式。

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>停用非同步作業

有時，您可能會想要在特定情況下，HTTP 動作的非同步行為，例如，當您想要：

* [避免長時間執行之工作的 HTTP 超時](#avoid-http-timeouts)
* [停用檢查位置標頭](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>關閉 **非同步模式** 設定

1. 在邏輯應用程式設計工具的 HTTP 動作標題列上，選取省略號 (**...**) 按鈕，這會開啟動作的設定。

1. 尋找 [ **非同步模式** ] 設定，並在啟用時將設定轉換為 [ **關閉** ]，然後選取 [ **完成**]。

   ![停用 [非同步模式] 設定](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>停用動作的 JSON 定義中的非同步模式

在 HTTP 動作的基礎 JSON 定義中， [將作業 `"DisableAsyncPattern"` 選項新增](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) 至動作的定義，讓動作改為遵循同步操作模式。 如需詳細資訊，請參閱 [在同步操作模式中執行動作](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern)。

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>避免長時間執行之工作的 HTTP 超時

HTTP 要求有 [超時限制](../logic-apps/logic-apps-limits-and-config.md#http-limits)。 如果您有長時間執行的 HTTP 動作，但因為這項限制，您可以使用下列選項：

* [停用 HTTP 動作的非同步作業模式](#disable-asynchronous-operations) ，讓動作不會持續輪詢或檢查要求的狀態。 相反地，此動作會等候接收者在要求完成處理之後，回應狀態和結果。

* 以 [Http Webhook 動作](../connectors/connectors-native-webhook.md)取代 HTTP 動作，這會在要求完成處理之後，等候接收者回應狀態和結果。

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>停用檢查位置標頭

某些端點、服務、系統或 Api 會傳回不含標頭的「202已接受」回應 `location` 。 當標頭不存在時，若要避免 HTTP 動作持續檢查要求狀態 `location` ，您可以使用下列選項：

* [停用 HTTP 動作的非同步作業模式](#disable-asynchronous-operations) ，讓動作不會持續輪詢或檢查要求的狀態。 相反地，此動作會等候接收者在要求完成處理之後，回應狀態和結果。

* 以 [Http Webhook 動作](../connectors/connectors-native-webhook.md)取代 HTTP 動作，這會在要求完成處理之後，等候接收者回應狀態和結果。

## <a name="known-issues"></a>已知問題

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>省略的 HTTP 標頭

如果 HTTP 觸發程式或動作包含這些標頭，Logic Apps 會將這些標頭從產生的要求訊息中移除，而不會顯示任何警告或錯誤：

* `Accept-*`
* `Allow`
* `Content-*`，但這些除外：`Content-Disposition`、`Content-Encoding` 和 `Content-Type`
* `Cookie`
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

雖然 Logic Apps 不會阻止您儲存使用 HTTP 觸發程式或動作與這些標頭的邏輯應用程式，但 Logic Apps 會忽略這些標頭。

## <a name="connector-reference"></a>連接器參考

如需有關觸發程式和動作參數的詳細資訊，請參閱下列各節：

* [HTTP 觸發程式參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP 動作參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

## <a name="next-steps"></a>後續步驟

* [針對其他服務和系統的輸出呼叫保護存取和資料存取](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)
* [適用於 Logic Apps 的連接器](../connectors/apis-list.md)
