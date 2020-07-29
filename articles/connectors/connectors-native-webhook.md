---
title: 等候並回應事件
description: 使用 Azure Logic Apps，根據服務端點上的事件來自動觸發、暫停和繼續工作流程
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/06/2020
tags: connectors
ms.openlocfilehash: 0a3fb9a8a72b384d2af4af38bdc382e541ddf535
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80656288"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>在 Azure Logic Apps 中使用 HTTP webhook，建立並執行自動化事件型工作流程

使用[Azure Logic Apps](../logic-apps/logic-apps-overview.md)和內建的 HTTP Webhook 連接器，您可以建立邏輯應用程式，根據在 HTTP 或 HTTPS 端點上發生的特定事件，將等候並執行的工作流程自動化。 例如，您可以建立邏輯應用程式，藉由在觸發工作流程並執行指定的動作之前等候特定事件來監視服務端點，而不是定期檢查或*輪詢*該端點。

以下是一些以事件為基礎的工作流程範例：

* 在觸發邏輯應用程式執行之前，等待專案抵達[Azure 事件中樞](https://github.com/logicappsio/EventHubAPI)。
* 等待核准，再繼續工作流程。

## <a name="how-do-webhooks-work"></a>Webhook 如何發揮作用？

HTTP webhook 觸發程式是以事件為基礎，不需要定期檢查或輪詢新的專案。 當您儲存以 webhook 觸發程式開頭的邏輯應用程式，或將邏輯應用程式從停用變更為啟用時，webhook 觸發程式會向該服務或端點註冊*回呼 URL* ，以*訂閱*特定服務或端點。 觸發程式接著會等待該服務或端點呼叫 URL，以開始執行邏輯應用程式。 類似于[要求觸發](connectors-native-reqres.md)程式，邏輯應用程式會在指定的事件發生時立即引發。 如果您移除觸發程式並儲存邏輯應用程式，或將邏輯應用程式從 [啟用] 變更為 [已停用]，則觸發程式會從服務或端點取消*訂閱*。

HTTP webhook 動作也是以事件為基礎，並且會藉由向該服務或端點註冊*回呼 URL* ，*訂閱*特定服務或端點。 Webhook 動作會暫停邏輯應用程式的工作流程，並等到服務或端點呼叫 URL 之後，邏輯應用程式才會繼續執行。 在這些情況下，動作邏輯應用程式會從服務或端點取消*訂閱*：

* 當 webhook 動作成功完成時
* 如果在等候回應時取消邏輯應用程式執行
* 邏輯應用程式超時之前

例如，Office 365 Outlook 連接器的 [[**傳送核准電子郵件**](connectors-create-api-office365-outlook.md)] 動作是遵循此模式的 webhook 動作範例。 您可以使用 webhook 動作，將此模式延伸至任何服務。

> [!NOTE]
> Logic Apps 在收到 HTTP webhook 觸發程式或動作的回呼時，會強制執行傳輸層安全性（TLS）1.2。 如果您看到 TLS 交握錯誤，請確定您使用的是 TLS 1.2。 對於傳入呼叫，以下是支援的加密套件：
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

如需詳細資訊，請參閱下列主題：

* [HTTP Webhook 觸發程式參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhook 和訂閱](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [建立支援 webhook 的自訂 Api](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 已部署的端點或 API 的 URL，可支援[邏輯應用程式中](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)的 webhook 的 webhook 訂閱和取消訂閱模式，或[邏輯應用程式](../logic-apps/logic-apps-create-api-app.md#webhook-actions)中適當的 webhook 動作

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)

* 您想要在目標端點等候特定事件的邏輯應用程式。 若要開始使用 HTTP Webhook 觸發程式，請[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP Webhook 動作，請使用您想要的任何觸發程式來啟動邏輯應用程式。 這個範例會使用 HTTP 觸發程式做為第一個步驟。

## <a name="add-an-http-webhook-trigger"></a>新增 HTTP Webhook 觸發程式

這個內建的觸發程式會呼叫目標服務上的訂閱端點，並向目標服務註冊回呼 URL。 然後，您的邏輯應用程式會等候目標服務將 `HTTP POST` 要求傳送至回呼 URL。 當此事件發生時，觸發程式就會引發，並將要求中的任何資料連同工作流程一起傳遞。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟您的空白邏輯應用程式。

1. 在設計工具的搜尋方塊中，輸入 `http webhook` 做為您的篩選準則。 從**觸發**程式清單中，選取 [ **HTTP Webhook** ] 觸發程式。

   ![選取 HTTP Webhook 觸發程式](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   這個範例會將觸發程式重新命名為， `HTTP Webhook trigger` 讓步驟有更具描述性的名稱。 此外，此範例稍後會新增 HTTP Webhook 動作，而且這兩個名稱都必須是唯一的。

1. 提供您想要用於訂閱和取消訂閱呼叫的[HTTP Webhook 觸發程式參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)值。

   在此範例中，觸發套裝程式含執行訂閱和取消訂閱作業時所要使用的方法、Uri 和訊息內文。

   ![輸入 HTTP Webhook 觸發程式參數](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | 屬性 | 必要 | 說明 |
   |----------|----------|-------------|
   | **訂用帳戶方法** | Yes | 訂閱目標端點時要使用的方法 |
   | **訂閱-URI** | Yes | 要用來訂閱目標端點的 URL |
   | **訂閱-主體** | No | 要包含在訂閱要求中的任何訊息本文。 這個範例包含使用 `@listCallbackUrl()` 運算式來抓取邏輯應用程式的回呼 url，以唯一識別訂閱者的回呼 url，也就是您的邏輯應用程式。 |
   | **取消訂閱-方法** | No | 取消訂閱目標端點時要使用的方法 |
   | **取消訂閱-URI** | No | 要用來從目標端點取消訂閱的 URL |
   | **取消訂閱-本文** | No | 要包含在取消訂閱要求中的選擇性訊息主體 <p><p>**注意**：這個屬性不支援使用 `listCallbackUrl()` 函數。 不過，此觸發程式會自動包含和傳送標頭和， `x-ms-client-tracking-id` 而 `x-ms-workflow-operation-name` 目標服務可以使用此標頭來唯一識別訂閱者。 |
   ||||

1. 若要加入其他觸發程式屬性，請開啟 [**加入新的參數**] 清單。

   ![新增更多觸發程式屬性](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   例如，如果您需要使用驗證，您可以新增**訂閱驗證**和**取消訂閱驗證**屬性。 如需適用于 HTTP Webhook 之驗證類型的詳細資訊，請參閱[將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 請使用當引發觸發程序時執行的動作，繼續建置邏輯應用程式的工作流程。

1. 完成後，請記得儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

   儲存邏輯應用程式會呼叫目標服務上的訂閱端點，並註冊回呼 URL。 然後，您的邏輯應用程式會等候目標服務將 `HTTP POST` 要求傳送至回呼 URL。 當此事件發生時，觸發程式就會引發，並將要求中的任何資料連同工作流程一起傳遞。 如果此作業成功完成，則會從端點取消訂閱，而邏輯應用程式會繼續剩餘的工作流程。

## <a name="add-an-http-webhook-action"></a>新增 HTTP Webhook 動作

這個內建動作會呼叫目標服務上的訂閱端點，並向目標服務註冊回呼 URL。 然後，您的邏輯應用程式會暫停並等候目標服務將 `HTTP POST` 要求傳送至回呼 URL。 當此事件發生時，此動作會將要求中的任何資料連同工作流程一起傳遞。 如果作業順利完成，動作會從端點取消訂閱，而您的邏輯應用程式會繼續執行剩餘的工作流程。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟邏輯應用程式。

   這個範例會使用 HTTP Webhook 觸發程式作為第一個步驟。

1. 在您要新增 HTTP Webhook 動作的步驟中，選取 [**新增步驟**]。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選擇所顯示的加號 ( **+** )，然後選取 [新增動作]。

1. 在設計工具的搜尋方塊中，輸入 `http webhook` 做為您的篩選準則。 從 [**動作**] 清單中，選取 [ **HTTP Webhook** ] 動作。

   ![選取 HTTP Webhook 動作](./media/connectors-native-webhook/select-http-webhook-action.png)

   這個範例會將動作重新命名為「HTTP Webhook 動作」，讓步驟有更具描述性的名稱。

1. 提供 HTTP Webhook 動作參數的值，這類似于您想要用於訂閱和取消訂閱呼叫的[Http webhook 觸發程式參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)。

   在此範例中，動作包含執行訂閱和取消訂閱作業時所要使用的方法、Uri 和訊息內文。

   ![輸入 HTTP Webhook 動作參數](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | 屬性 | 必要 | Description |
   |----------|----------|-------------|
   | **訂用帳戶方法** | Yes | 訂閱目標端點時要使用的方法 |
   | **訂閱-URI** | Yes | 要用來訂閱目標端點的 URL |
   | **訂閱-主體** | No | 要包含在訂閱要求中的任何訊息本文。 這個範例包含使用 `@listCallbackUrl()` 運算式來抓取邏輯應用程式的回呼 url，以唯一識別訂閱者的回呼 url，也就是您的邏輯應用程式。 |
   | **取消訂閱-方法** | No | 取消訂閱目標端點時要使用的方法 |
   | **取消訂閱-URI** | No | 要用來從目標端點取消訂閱的 URL |
   | **取消訂閱-本文** | No | 要包含在取消訂閱要求中的選擇性訊息主體 <p><p>**注意**：這個屬性不支援使用 `listCallbackUrl()` 函數。 不過，此動作會自動包含和傳送標頭、 `x-ms-client-tracking-id` 和 `x-ms-workflow-operation-name` ，而目標服務可以使用此標頭來唯一識別訂閱者。 |
   ||||

1. 若要新增其他動作屬性，請開啟 [**加入新的參數**] 清單。

   ![新增更多動作屬性](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   例如，如果您需要使用驗證，您可以新增**訂閱驗證**和**取消訂閱驗證**屬性。 如需適用于 HTTP Webhook 之驗證類型的詳細資訊，請參閱[將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 完成時，請記得儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

   現在，當此動作執行時，您的邏輯應用程式會呼叫目標服務上的訂閱端點，並註冊回呼 URL。 接著，邏輯應用程式會暫停工作流程，並等候目標服務將 `HTTP POST` 要求傳送至回呼 URL。 當此事件發生時，此動作會將要求中的任何資料連同工作流程一起傳遞。 如果作業順利完成，動作會從端點取消訂閱，而您的邏輯應用程式會繼續執行剩餘的工作流程。

## <a name="connector-reference"></a>連接器參考

如需觸發程式和動作參數的詳細資訊（類似于彼此），請參閱[HTTP Webhook 參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)。

### <a name="output-details"></a>輸出詳細資料

以下是 HTTP Webhook 觸發程式或動作的輸出的詳細資訊，其會傳回下列資訊：

| 屬性名稱 | 類型 | Description |
|---------------|------|-------------|
| headers | 物件 (object) | 要求的標頭 |
| body | 物件 (object) | JSON 物件 | 具有來自要求之本文內容的物件 |
| 狀態碼 | int | 要求的狀態碼 |
|||

| 狀態碼 | Description |
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
