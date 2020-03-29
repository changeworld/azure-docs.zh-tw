---
title: 與 Azure 服務匯流排交換消息
description: 創建自動任務和工作流，這些任務和工作流通過使用 Azure 邏輯應用中的 Azure 服務匯流排發送和接收郵件
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 1b38b8508dbe17d42bf191149410f5db638cf834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261614"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>使用 Azure 邏輯應用和 Azure 服務匯流排在雲中交換消息

使用[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)和[Azure 服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)連接器，可以創建自動任務和工作流來傳輸資料，例如銷售和採購訂單、日誌以及組織跨應用程式進行庫存移動。 連接器不只會監視、傳送及管理訊息，也會執行佇列、工作階段、主題、訂用帳戶等等的動作，例如：

* 監視訊息何時在佇列、主題及主題訂用帳戶中送達 (自動完成) 或接收 (查看鎖定)。
* 傳送訊息。
* 建立和刪除主題訂用帳戶。
* 管理佇列和主題訂用帳戶中的訊息，例如，取得、延後、完成、延遲、放棄和無效信件。
* 更新佇列和主題訂用帳戶中訊息和工作階段的鎖定。
* 關閉佇列和主題中的工作階段。

您可以使用觸發程序，從服務匯流排收到回應，並且讓輸出可供邏輯應用程式中的其他動作使用。 您也可以讓其他動作使用服務匯流排動作的輸出。 如果您是服務匯流排和邏輯應用的新增功能，請查看什麼是[Azure 服務匯流排？](../service-bus-messaging/service-bus-messaging-overview.md)什麼是[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)？

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 服務匯流排命名空間和傳訊實體，例如佇列。 這些專案和邏輯應用需要使用相同的 Azure 訂閱。 如果您沒有這些項目，請了解如何[建立您的服務匯流排命名空間和佇列](../service-bus-messaging/service-bus-create-namespace-portal.md)。

* [有關如何創建邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 使用服務匯流排命名空間和消息傳遞實體的邏輯應用。 邏輯應用和服務匯流排需要使用相同的 Azure 訂閱。 要使用服務匯流排觸發器啟動工作流，[請創建一個空白邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 要在工作流中使用服務匯流排操作，請使用另一個觸發器啟動邏輯應用，例如["重複觸發器](../connectors/connectors-native-recurrence.md)"。

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>檢查權限

確認您的邏輯應用程式有權可以存取您的服務匯流排命名空間。

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 移至您的服務匯流排「命名空間」**。 在命名空間頁面的 [設定]**** 之下，選取 [共用存取原則]****。 在 **"聲明"** 下，檢查您是否具有該命名空間**的管理**許可權。

   ![管理服務匯流排命名空間的許可權](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. 取得服務匯流排命名空間的連接字串。 當您在邏輯應用中提供連接資訊時，需要此字串。

   1. 在"**共用訪問策略"** 窗格中，選擇 **"根管理共用訪問金鑰**"。
   
   1. 在主連接字串旁邊，選擇複製按鈕。 儲存連接字串以供稍後使用。

      ![複製服務匯流排命名空間連接字串](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 若要確認您的連接字串，是否與您的服務匯流排命名空間或傳訊實體 (例如佇列) 相關聯，請搜尋連接字串中是否有 `EntityPath`  參數。 如果您發現這個參數，表示此連接字串適用於特定實體，但不是可用於您邏輯應用程式的正確字串。

## <a name="add-service-bus-trigger"></a>添加服務匯流排觸發器

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登錄到 Azure[門戶](https://portal.azure.com)，並在邏輯應用設計器中打開空白邏輯應用。

1. 在搜索框中，輸入"azure 服務匯流排"作為篩選器。 從觸發器清單中選擇所需的觸發器。

   例如，要在新專案發送到服務匯流排佇列時觸發邏輯應用，請選擇佇列 **（自動完成）觸發器中收到消息時**。

   ![選取服務匯流排觸發程序](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   所有服務匯流排觸發器都是*長輪詢*觸發器。 此說明意味著當觸發器觸發時，觸發器處理所有消息，然後等待 30 秒，以等待更多消息出現在佇列或主題訂閱中。 如果在 30 秒內未出現任何訊息，就會略過觸發程序執行。 如果有，觸發程序會繼續讀取訊息，直到佇列或主題訂閱是空的。 下一次的觸發程序輪詢，會根據觸發程序的屬性指定的循環間隔。

   某些觸發器（如**一個或多個消息到達佇列（自動完成）觸發器時**，可以返回一個或多個消息。 當這些觸發器觸發時，它們在觸發器的 **"最大消息計數**"屬性指定的 1 和消息數之間返回。

1. 如果您的觸發器首次連接到服務匯流排命名空間，請按照邏輯應用設計器提示您提供連接資訊時執行以下步驟。

   1. 請為您的連線提供名稱，然後選取您的服務匯流排命名空間。

      ![建立服務匯流排連線 (第一部分)](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      要手動輸入連接字串，請選擇 **"手動輸入連接資訊**"。 如果您還沒有連接字串，請了解[如何尋找您的連接字串](#permissions-connection-string)。

   1. 選擇服務匯流排策略，然後選擇 **"創建**"。

      ![建立服務匯流排連線 (第二部分)](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. 選擇所需的消息傳遞實體，如佇列或主題。 在此示例中，請選擇服務匯流排佇列。
   
      ![選取服務匯流排佇列](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. 為所選觸發器提供必要的資訊。 要向操作添加其他可用屬性，請打開 **"添加新參數**清單"，然後選擇所需的屬性。

   對於此示例的觸發器，選擇輪詢間隔和頻率以檢查佇列。

   ![設定輪詢間隔](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   有關可用觸發器和屬性的詳細資訊，請參閱連接器的[參考頁](/connectors/servicebus/)。

1. 通過添加所需的操作，繼續構建邏輯應用。

   例如，您可以新增在新訊息送達時，會傳送電子郵件的動作。 當觸發程序檢查您的佇列並且發現新訊息時，您的邏輯應用程式會針對找到的訊息，執行已選取的動作。

## <a name="add-service-bus-action"></a>添加服務匯流排操作

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登錄到 Azure[門戶](https://portal.azure.com)，並在邏輯應用設計器中打開邏輯應用。

1. 在要添加操作的步驟下，選擇 **"新建步驟**"。

   或者，要在步驟之間添加操作，請將指標移到這些步驟之間的箭頭上。 選擇顯示的加號**+**（），然後選擇 **"添加操作**"。

1. 在搜索框中**的"選擇操作**"下，輸入"azure 服務匯流排"作為篩選器。 從動作清單中選擇所需的操作。 

   在此示例中，選擇 **"發送消息**"操作。

   ![選取服務匯流排動作](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. 如果操作首次連接到服務匯流排命名空間，請按照邏輯應用設計器提示您提供連接資訊時執行以下步驟。

   1. 請為您的連線提供名稱，然後選取您的服務匯流排命名空間。

      ![建立服務匯流排連線 (第一部分)](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      要手動輸入連接字串，請選擇 **"手動輸入連接資訊**"。 如果您還沒有連接字串，請了解[如何尋找您的連接字串](#permissions-connection-string)。

   1. 選擇服務匯流排策略，然後選擇 **"創建**"。

      ![建立服務匯流排連線 (第二部分)](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. 選擇所需的消息傳遞實體，如佇列或主題。 在此示例中，請選擇服務匯流排佇列。

      ![選取服務匯流排佇列](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. 為您選取的動作提供必要的詳細資料。 要向操作添加其他可用屬性，請打開 **"添加新參數**清單"，然後選擇所需的屬性。

   例如，選擇**內容和****內容類型**屬性，以便將它們添加到操作中。 然後，指定要發送的消息的內容。

   ![提供訊息內容和詳細資料](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   有關可用操作及其屬性的詳細資訊，請參閱連接器的[參考頁](/connectors/servicebus/)。

1. 通過添加所需的任何其他操作，繼續構建邏輯應用。

   例如，您可以添加發送電子郵件以確認郵件已發送的操作。

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

## <a name="connector-reference"></a>連接器參考

服務匯流排連接器一次可保存多達 1，500 個唯一會話，從服務匯流排到連接器緩存。 如果會話計數超過此限制，則從緩存中刪除舊會話。 有關詳細資訊，請參閱[消息會話](../service-bus-messaging/message-sessions.md)。

有關觸發器、操作和限制的其他技術詳細資訊（由連接器的 OpenAPI（以前斯瓦格）描述，請查看連接器的[參考頁](/connectors/servicebus/)。 有關 Azure 服務匯流排消息傳遞的資訊，請參閱[什麼是 Azure 服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)？

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
