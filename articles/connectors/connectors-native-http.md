---
title: 使用 HTTP 或 HTTPS 呼叫服務端點
description: 從 Azure Logic Apps 將輸出 HTTP 或 HTTPS 要求傳送至服務端點
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/09/2020
tags: connectors
ms.openlocfilehash: 8c7a0ddb80ba28548fc1821cc2063e500af0fa66
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286626"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>從 Azure Logic Apps 透過 HTTP 或 HTTPS 呼叫服務端點

透過[Azure Logic Apps](../logic-apps/logic-apps-overview.md)和內建的 HTTP 觸發程式或動作，您可以建立自動化的工作和工作流程，以透過 HTTP 或 HTTPS 將要求傳送至服務端點。 例如，您可以根據特定排程來檢查該端點，以監視網站的服務端點。 當指定的事件發生在該端點時（例如您的網站停止運作），事件會觸發邏輯應用程式的工作流程，並在該工作流程中執行動作。 如果您想要改為接收和回應輸入 HTTPS 呼叫，請使用內建的[要求觸發程式或回應動作](../connectors/connectors-native-reqres.md)。

* 若要依週期性排程檢查或*輪詢*端點，請[新增 HTTP 觸發](#http-trigger)程式做為工作流程中的第一個步驟。 每次觸發程式檢查端點時，觸發程式會呼叫或傳送*要求*至端點。 端點的回應會決定是否執行邏輯應用程式的工作流程。 觸發程式會將端點回應中的任何內容傳遞至邏輯應用程式中的動作。

* 若要從工作流程中的任何其他位置呼叫端點，請[新增 HTTP 動作](#http-action)。 端點的回應會決定工作流程的剩餘動作如何執行。

本文說明如何將 HTTP 觸發程式或動作新增至邏輯應用程式的工作流程。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您想要呼叫的目標端點 URL

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 如果您不熟悉邏輯應用程式，請參閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md)？

* 您想要從中呼叫目標端點的邏輯應用程式。 若要開始使用 HTTP 觸發程式，請[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP 動作，請使用您想要的任何觸發程式來啟動邏輯應用程式。 這個範例會使用 HTTP 觸發程式做為第一個步驟。

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>新增 HTTP 觸發程式

此內建觸發程式會對指定的端點 URL 進行 HTTP 呼叫，並傳迴響應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟您的空白邏輯應用程式。

1. 在設計工具的搜尋方塊下，選取 [**內建**]。 在搜尋方塊中，輸入 `http` 作為篩選條件。 從**觸發**程式清單中，選取**HTTP**觸發程式。

   ![選取 HTTP 觸發程序](./media/connectors-native-http/select-http-trigger.png)

   這個範例會將觸發程式重新命名為「HTTP 觸發程式」，讓步驟有更具描述性的名稱。 此外，此範例稍後會新增 HTTP 動作，而且這兩個名稱都必須是唯一的。

1. 提供您想要包含在目標端點呼叫中的[HTTP 觸發程式參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)值。 針對您想要讓觸發程式檢查目標端點的頻率，設定週期。

   ![輸入 HTTP 觸發程序參數](./media/connectors-native-http/http-trigger-parameters.png)

   如果您選取 [**無**] 以外的驗證類型，驗證設定會根據您的選擇而有所不同。 如需適用于 HTTP 的驗證類型的詳細資訊，請參閱下列主題：

   * [將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [使用受控識別來驗證資源的存取權](../logic-apps/create-managed-service-identity.md)

1. 若要新增其他可用參數，開啟 [新增參數] 清單，然後選取您所需的參數。

1. 請使用當引發觸發程序時執行的動作，繼續建置邏輯應用程式的工作流程。

1. 當您完成時，請記得儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

<a name="http-action"></a>

## <a name="add-an-http-action"></a>新增 HTTP 動作

此內建動作會對指定的端點 URL 進行 HTTP 呼叫，並傳迴響應。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟邏輯應用程式。

   這個範例會使用 HTTP 觸發程式做為第一個步驟。

1. 在您要新增 HTTP 動作的步驟底下，選取 [**新增步驟**]。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選擇所顯示的加號 ( **+** )，然後選取 [新增動作]。

1. 在 [選擇動作]**** 底下，選取 [內建]****。 在搜尋方塊中，輸入 `http` 作為篩選條件。 從 [**動作**] 清單中，選取 [ **HTTP** ] 動作。

   ![選取 HTTP 動作](./media/connectors-native-http/select-http-action.png)

   這個範例會將動作重新命名為「HTTP 動作」，讓步驟有更具描述性的名稱。

1. 提供您想要包含在目標端點呼叫中的[HTTP 動作參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)值。

   ![輸入 HTTP 動作參數](./media/connectors-native-http/http-action-parameters.png)

   如果您選取 [**無**] 以外的驗證類型，驗證設定會根據您的選擇而有所不同。 如需適用于 HTTP 的驗證類型的詳細資訊，請參閱下列主題：

   * [將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [使用受控識別來驗證資源的存取權](../logic-apps/create-managed-service-identity.md)

1. 若要新增其他可用參數，開啟 [新增參數] 清單，然後選取您所需的參數。

1. 當您完成時，請記得儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

<a name="tls-support"></a>

## <a name="transport-layer-security-tls"></a>傳輸層安全性 (TLS)

根據目標端點的功能，輸出呼叫支援傳輸層安全性（TLS），先前已安全通訊端層（SSL）、版本1.0、1.1 和1.2。 Logic Apps 使用支援的最高版本來與端點協調。

例如，如果端點支援1.2，HTTP 連接器會先使用1.2。 否則，連接器會使用下一個支援的最高版本。

<a name="self-signed"></a>

## <a name="self-signed-certificates"></a>自我簽署憑證

* 對於全域、多租使用者 Azure 環境中的邏輯應用程式，HTTP 連接器不允許自我簽署的 TLS/SSL 憑證。 如果您的邏輯應用程式會對伺服器發出 HTTP 呼叫，並出示 TLS/SSL 自我簽署憑證，則 HTTP 呼叫會失敗並產生 `TrustFailure` 錯誤。

* 對於[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用程式，HTTP 連接器允許 TLS/SSL 交握的自我簽署憑證。 不過，您必須先使用 Logic Apps REST API 啟用現有 ISE 或新 ISE 的[自我簽署憑證支援](../logic-apps/create-integration-service-environment-rest-api.md#request-body)，並在該位置安裝公開憑證 `TrustedRoot` 。

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

例如，假設您有一個邏輯應用程式，它會使用支援類型的網站 API，將 Excel 檔案的 HTTP POST 要求傳送至網站 `multipart/form-data` 。 以下是此動作可能的外觀：

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

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>非同步要求-回應行為

根據預設，Azure Logic Apps 中的所有 HTTP 型動作都會遵循標準[非同步作業模式](/azure/architecture/patterns/async-request-reply)。 此模式會指定在 HTTP 動作呼叫或將要求傳送至端點、服務、系統或 API 之後，接收者會立即傳回「202已[接受](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3)」回應。 這段程式碼會確認接收者是否已接受要求，但尚未完成處理。 回應可以包含 `location` 指定 URL 的標頭，以及呼叫者可以用來輪詢或檢查非同步要求狀態的重新整理識別碼，直到接收者停止處理並傳回「 [200 確定](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1)」成功回應或其他非202回應為止。 不過，呼叫端不需要等候要求完成處理，而且可以繼續執行下一個動作。 如需詳細資訊，請參閱[非同步微服務整合會強制執行微服務自治](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging)。

* 在邏輯應用程式設計工具中，HTTP 動作（但非觸發程式）具有**非同步模式**設定，預設為啟用。 此設定指定呼叫端不會等待處理完成，而且可以移至下一個動作，但會繼續檢查狀態，直到處理停止為止。 如果停用，此設定會指定呼叫端等待處理完成，然後再繼續進行下一個動作。

  若要尋找此設定，請遵循下列步驟：

  1. 在 [HTTP 動作] 的標題列上，選取省略號（**...**）按鈕，這會開啟動作的 [設定]。

  1. 尋找**非同步模式**設定。

     ![「非同步模式」設定](./media/connectors-native-http/asynchronous-pattern-setting.png)

* HTTP 動作的基礎 JavaScript 物件標記法（JSON）定義會隱含地遵循非同步作業模式。

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>停用非同步作業

有時候，您可能會想要在特定案例中執行 HTTP 動作的非同步行為，例如，當您想要：

* [避免長時間執行之工作的 HTTP 超時](#avoid-http-timeouts)
* [停用檢查位置標頭](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>關閉**非同步模式**設定

1. 在邏輯應用程式設計工具的 [HTTP] 動作標題列上，選取省略號（**...**）按鈕，這會開啟動作的 [設定]。

1. 尋找 [**非同步模式**] 設定，將設定設為 [**關閉**] （如果啟用），然後選取 [**完成**]。

   ![停用 [非同步模式] 設定](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>停用動作的 JSON 定義中的非同步模式

在 HTTP 動作的基礎 JSON 定義中，[將作業 `"DisableAsyncPattern"` 選項新增](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)至動作的定義，讓動作會改為遵循同步作業模式。 如需詳細資訊，請參閱也要[在同步作業模式中執行動作](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern)。

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>避免長時間執行之工作的 HTTP 超時

HTTP 要求具有[超時限制](../logic-apps/logic-apps-limits-and-config.md#http-limits)。 如果您有長時間執行的 HTTP 動作因為此限制而超時，您可以使用下列選項：

* [停用 HTTP 動作的非同步作業模式](#disable-asynchronous-operations)，讓動作不會持續輪詢或檢查要求的狀態。 相反地，動作會在要求完成處理之後，等待接收者回應狀態和結果。

* 將 HTTP 動作取代為[Http Webhook 動作](../connectors/connectors-native-webhook.md)，這會在要求完成處理之後，等待接收者回應狀態和結果。

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>停用檢查位置標頭

某些端點、服務、系統或 Api 會傳回不具有標頭的「202已接受」回應 `location` 。 若要避免在標頭不存在時，HTTP 動作持續檢查要求狀態 `location` ，您可以使用下列選項：

* [停用 HTTP 動作的非同步作業模式](#disable-asynchronous-operations)，讓動作不會持續輪詢或檢查要求的狀態。 相反地，動作會在要求完成處理之後，等待接收者回應狀態和結果。

* 將 HTTP 動作取代為[Http Webhook 動作](../connectors/connectors-native-webhook.md)，這會在要求完成處理之後，等待接收者回應狀態和結果。

## <a name="known-issues"></a>已知問題

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>省略的 HTTP 標頭

如果 HTTP 觸發程式或動作包含這些標頭，Logic Apps 會從產生的要求訊息中移除這些標頭，而不會顯示任何警告或錯誤：

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

雖然 Logic Apps 不會阻止您儲存使用 HTTP 觸發程式或動作與這些標頭的邏輯應用程式，Logic Apps 會忽略這些標頭。

## <a name="connector-reference"></a>連接器參考

如需觸發程式和動作參數的詳細資訊，請參閱下列各節：

* [HTTP 觸發程式參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP 動作參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>輸出詳細資料

以下是 HTTP 觸發程式或動作輸出的詳細資訊，會傳回下列資訊：

| 屬性 | 類型 | 說明 |
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

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)

