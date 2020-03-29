---
title: 等待並回應事件
description: 使用 Azure 邏輯應用，根據服務終結點的事件自動執行觸發、暫停和恢復的工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/06/2020
tags: connectors
ms.openlocfilehash: 1578ca030bc8bab971a44e1afcce1d1ab9e1d5e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674145"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>使用 Azure 邏輯應用中的 HTTP Webhook 創建和運行基於事件的自動工作流

使用[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)和內置 HTTP Webhook 連接器，可以通過構建邏輯應用來自動執行基於 HTTP 或 HTTPS 終結點發生的特定事件等待和運行的工作流。 例如，您可以創建一個邏輯應用，通過等待特定事件來監視服務終結點，然後再觸發工作流並運行指定的操作，而不是定期檢查或*輪詢*該終結點。

下面是一些基於事件的工作流示例：

* 等待專案從[Azure 事件中心](https://github.com/logicappsio/EventHubAPI)到達，然後再觸發邏輯應用運行。
* 在繼續工作流之前，請等待審批。

## <a name="how-do-webhooks-work"></a>網鉤是如何工作的？

HTTP Webhook 觸發器基於事件，它不依賴于定期檢查或輪詢新專案。 保存以 Webhook 觸發器開頭的邏輯應用時，或者當您將邏輯應用從禁用更改為已啟用時，Webhook 觸發器通過向該服務或終結點註冊*回檔 URL* *訂閱*特定服務或終結點。 然後，觸發器等待該服務或終結點調用 URL，該 URL 開始運行邏輯應用。 與[請求觸發器](connectors-native-reqres.md)類似，邏輯應用在指定事件發生時立即觸發。 如果刪除觸發器並保存邏輯應用，或者將邏輯應用從啟用更改為禁用，則觸發器將從服務或終結點*取消訂閱*。

HTTP Webhook 操作也基於事件，並通過向該服務或終結點註冊*回檔 URL*來*訂閱*特定服務或終結點。 Webhook 操作暫停邏輯應用的工作流，並等待服務或終結點調用 URL，然後邏輯應用繼續運行。 操作邏輯應用在以下情況下*取消訂閱*服務或終結點：

* Webhook 操作成功完成時
* 如果在等待回應時取消邏輯應用運行
* 邏輯應用超時之前

例如，Office 365 Outlook 連接器的[**"發送核准電子郵件**](connectors-create-api-office365-outlook.md)"操作是遵循此模式的 Webhook 操作的示例。 您可以使用 Webhook 操作將此模式擴展到任何服務。

> [!NOTE]
> 邏輯應用在接收回 HTTP Webhook 觸發器或操作時強制實施傳輸層安全性 （TLS） 1.2。 如果您看到 SSL 握手錯誤，請確保使用 TLS 1.2。 對於撥入電話，以下是支援的密碼套件：
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

* [HTTP Webhook 觸發參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhook 和訂閱](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [創建支援 Webhook 的自訂 API](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 已部署的終結點或 API 的 URL，支援[邏輯應用中 Webhook 觸發器的 Webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)訂閱和取消訂閱模式，並根據需要在[邏輯應用中操作](../logic-apps/logic-apps-create-api-app.md#webhook-actions)

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 如果您是邏輯應用的新增功能，請查看什麼是[Azure 邏輯應用？](../logic-apps/logic-apps-overview.md)

* 要在目標終結點等待特定事件的邏輯應用。 要從 HTTP Webhook 觸發器開始，[請創建一個空白邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 要使用 HTTP Webhook 操作，請使用所需的任何觸發器啟動邏輯應用。 本示例使用 HTTP 觸發器作為第一步。

## <a name="add-an-http-webhook-trigger"></a>添加 HTTP 網路掛接觸發器

此內置觸發器調用目標服務上的訂閱終結點，並將回檔 URL 註冊到目標服務。 然後，邏輯應用將等待目標服務向回檔 URL`HTTP POST`發送請求。 發生此事件時，觸發器將觸發請求中的任何資料並傳遞給工作流。

1. 登錄到 Azure[門戶](https://portal.azure.com)。 在邏輯應用設計器中打開空白邏輯應用。

1. 在設計器的搜索框中，輸入`http webhook`為篩選器。 從**觸發器**清單中選擇**HTTP Webhook**觸發器。

   ![選擇 HTTP 網路掛接觸發器](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   本示例將觸發器重命名為`HTTP Webhook trigger`，以便步驟具有更具描述性的名稱。 此外，該示例稍後添加一個 HTTP Webhook 操作，並且兩個名稱都必須是唯一的。

1. 提供要用於訂閱和取消訂閱調用的[HTTP Webhook 觸發器參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)的值。

   在此示例中，觸發器包括執行訂閱和取消訂閱操作時要使用的方法、URI 和郵件內文。

   ![輸入 HTTP Webhook 觸發器參數](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **訂閱 - 方法** | 是 | 訂閱目標終結點時使用的方法 |
   | **訂閱 - URI** | 是 | 用於訂閱目標終結點的 URL |
   | **訂閱 - 正文** | 否 | 要包含在訂閱請求中的任何郵件內文。 此示例包括通過使用`@listCallbackUrl()`運算式檢索邏輯應用的回檔 URL 來唯一標識訂閱者（即邏輯應用）的回檔 URL。 |
   | **取消訂閱 - 方法** | 否 | 從目標終結點取消訂閱時使用的方法 |
   | **取消訂閱 - URI** | 否 | 用於從目標終結點取消訂閱的 URL |
   | **取消訂閱 - 正文** | 否 | 要包含在取消訂閱請求中的可選郵件內文 <p><p>**注意**：此屬性不支援使用 函數`listCallbackUrl()`。 但是，觸發器會自動包含併發送標頭，`x-ms-client-tracking-id`以及`x-ms-workflow-operation-name`目標服務可用於唯一標識訂閱者的標頭。 |
   ||||

1. 要添加其他觸發器屬性，打開 **"添加新參數**清單"。

   ![添加更多觸發器屬性](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   例如，如果需要使用身份驗證，可以添加 **"訂閱 - 身份驗證**和**取消訂閱 - 身份驗證"屬性**。 有關可用於 HTTP Webhook 的身份驗證類型的詳細資訊，請參閱[將身份驗證添加到出站調用](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 請使用當引發觸發程序時執行的動作，繼續建置邏輯應用程式的工作流程。

1. 完成後，請記住保存邏輯應用。 在設計工具的工具列上，選取 [儲存]****。

   保存邏輯應用將在目標服務上調用訂閱終結點並註冊回檔 URL。 然後，邏輯應用將等待目標服務向回檔 URL`HTTP POST`發送請求。 發生此事件時，觸發器將觸發請求中的任何資料並傳遞給工作流。 如果此操作成功完成，觸發器將從終結點取消訂閱，並且邏輯應用將繼續剩餘的工作流。

## <a name="add-an-http-webhook-action"></a>添加 HTTP 網路掛機操作

此內置操作調用目標服務上的訂閱終結點，並將回檔 URL 註冊到目標服務。 然後，邏輯應用暫停並等待目標服務向回檔 URL 發送`HTTP POST`請求。 發生此事件時，操作會將請求中的任何資料傳遞給工作流。 如果操作成功完成，操作將從終結點取消訂閱，並且邏輯應用將繼續運行剩餘的工作流。

1. 登錄到 Azure[門戶](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟邏輯應用程式。

   本示例使用 HTTP Webhook 觸發器作為第一步。

1. 在要添加 HTTP Webhook 操作的步驟下，選擇 **"新建步驟**"。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選擇顯示的加號**+**（），然後選擇 **"添加操作**"。

1. 在設計器的搜索框中，輸入`http webhook`為篩選器。 從 **"操作"** 清單中，選擇**HTTP Webhook**操作。

   ![選擇 HTTP 網鉤操作](./media/connectors-native-webhook/select-http-webhook-action.png)

   本示例將操作重命名為"HTTP Webhook 操作"，以便步驟具有更具描述性的名稱。

1. 提供 HTTP Webhook 指令引數的值，這些參數類似于 HTTP [Webhook 觸發器參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)，用於訂閱和取消訂閱調用。

   在此示例中，該操作包括執行訂閱和取消訂閱操作時要使用的方法、URI 和郵件內文。

   ![輸入 HTTP Webhook 指令引數](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **訂閱 - 方法** | 是 | 訂閱目標終結點時使用的方法 |
   | **訂閱 - URI** | 是 | 用於訂閱目標終結點的 URL |
   | **訂閱 - 正文** | 否 | 要包含在訂閱請求中的任何郵件內文。 此示例包括通過使用`@listCallbackUrl()`運算式檢索邏輯應用的回檔 URL 來唯一標識訂閱者（即邏輯應用）的回檔 URL。 |
   | **取消訂閱 - 方法** | 否 | 從目標終結點取消訂閱時使用的方法 |
   | **取消訂閱 - URI** | 否 | 用於從目標終結點取消訂閱的 URL |
   | **取消訂閱 - 正文** | 否 | 要包含在取消訂閱請求中的可選郵件內文 <p><p>**注意**：此屬性不支援使用 函數`listCallbackUrl()`。 但是，該操作會自動包括併發送標頭，`x-ms-client-tracking-id`以及`x-ms-workflow-operation-name`目標服務可用於唯一標識訂閱者的標頭。 |
   ||||

1. 要添加其他操作屬性，打開 **"添加新參數**清單"。

   ![添加更多操作屬性](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   例如，如果需要使用身份驗證，可以添加 **"訂閱 - 身份驗證**和**取消訂閱 - 身份驗證"屬性**。 有關可用於 HTTP Webhook 的身份驗證類型的詳細資訊，請參閱[將身份驗證添加到出站調用](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 完成後，請記住保存邏輯應用。 在設計工具的工具列上，選取 [儲存]****。

   現在，當此操作運行時，邏輯應用調用目標服務上的訂閱終結點並註冊回檔 URL。 然後，邏輯應用暫停工作流並等待目標服務向回檔 URL 發送`HTTP POST`請求。 發生此事件時，操作會將請求中的任何資料傳遞給工作流。 如果操作成功完成，操作將從終結點取消訂閱，並且邏輯應用將繼續運行剩餘的工作流。

## <a name="connector-reference"></a>連接器參考

有關觸發器和指令引數（彼此相似）的詳細資訊，請參閱[HTTP Webhook 參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)。

### <a name="output-details"></a>輸出詳細資料

以下是有關 HTTP Webhook 觸發器或操作的輸出的詳細資訊，返回此資訊：

| 屬性名稱 | 類型 | 描述 |
|---------------|------|-------------|
| headers | 物件 (object) | 請求中的標頭 |
| body | 物件 (object) | JSON 物件 | 具有請求中正文內容的物件 |
| 狀態碼 | int | 請求中的狀態碼 |
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
