---
title: 使用 Azure 服務匯流排交換訊息
description: 建立自動化的工作和工作流程，以使用 Azure Logic Apps 中的 Azure 服務匯流排來傳送和接收訊息
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 09/03/2020
tags: connectors
ms.openlocfilehash: 68b81fa8cf110b47581e482e7e546821d40aef62
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435145"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>使用 Azure Logic Apps 和 Azure 服務匯流排在雲端中交換訊息

使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和 [Azure 服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md) 連接器，您可以建立自動化的工作和工作流程，以傳送資料，例如銷售和採購單、日誌，以及在組織的應用程式之間進行清查移動等作業。 連接器不只會監視、傳送及管理訊息，也會執行佇列、工作階段、主題、訂用帳戶等等的動作，例如：

* 監視訊息何時在佇列、主題及主題訂用帳戶中送達 (自動完成) 或接收 (查看鎖定)。
* 傳送訊息。
* 建立和刪除主題訂用帳戶。
* 管理佇列和主題訂用帳戶中的訊息，例如，取得、延後、完成、延遲、放棄和無效信件。
* 更新佇列和主題訂用帳戶中訊息和工作階段的鎖定。
* 關閉佇列和主題中的工作階段。

您可以使用觸發程序，從服務匯流排收到回應，並且讓輸出可供邏輯應用程式中的其他動作使用。 您也可以讓其他動作使用服務匯流排動作的輸出。 如果您不熟悉服務匯流排和 Logic Apps，請參閱 [Azure 服務匯流排是什麼？](../service-bus-messaging/service-bus-messaging-overview.md) 以及 [Azure Logic Apps 是什麼](../logic-apps/logic-apps-overview.md)？

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Prerequisites

* Azure 帳戶和訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 服務匯流排命名空間和傳訊實體，例如佇列。 這些專案和您的邏輯應用程式需要使用相同的 Azure 訂用帳戶。 如果您沒有這些項目，請了解如何[建立您的服務匯流排命名空間和佇列](../service-bus-messaging/service-bus-create-namespace-portal.md)。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 您使用服務匯流排命名空間和訊息實體的邏輯應用程式。 您的邏輯應用程式和服務匯流排需要使用相同的 Azure 訂用帳戶。 若要使用服務匯流排觸發程式來啟動工作流程，請 [建立空白的邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要在您的工作流程中使用服務匯流排動作，請使用另一個觸發程式來啟動邏輯應用程式，例如， [迴圈觸發](../connectors/connectors-native-recurrence.md)程式。

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>請檢查權限

確認您的邏輯應用程式有權可以存取您的服務匯流排命名空間。

1. 在 [Azure 入口網站](https://portal.azure.com)中，使用您的 Azure 帳戶登入。

1. 移至您的服務匯流排「命名空間」**。 在命名空間頁面的 [設定]**** 之下，選取 [共用存取原則]****。 在 [ **宣告**] 下，檢查您是否有該命名空間的 [ **管理** ] 許可權。

   ![管理服務匯流排命名空間的許可權](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. 取得服務匯流排命名空間的連接字串。 當您在邏輯應用程式中提供連接資訊時，需要這個字串。

   1. 在 [ **共用存取原則** ] 窗格中，選取 [ **RootManageSharedAccessKey**]。

   1. 選取主要連接字串旁的 [複製] 按鈕。 儲存連接字串以供稍後使用。

      ![複製服務匯流排命名空間連接字串](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 若要確認您的連接字串，是否與您的服務匯流排命名空間或傳訊實體 (例如佇列) 相關聯，請搜尋連接字串中是否有 `EntityPath`  參數。 如果您發現這個參數，表示此連接字串適用於特定實體，但不是可用於您邏輯應用程式的正確字串。

## <a name="add-service-bus-trigger"></a>新增服務匯流排觸發程式

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後在邏輯應用程式設計工具中開啟空白邏輯應用程式。

1. 在搜尋方塊中，輸入 "azure service bus" 作為篩選準則。 從觸發程式清單中選取您想要的觸發程式。

   例如，若要在新專案傳送至「服務匯流排」佇列時觸發邏輯應用程式，請選取 [在 **佇列中收到訊息時] (自動完成) ** 觸發程式。

   ![選取服務匯流排觸發程序](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   所有服務匯流排觸發程式都是 *長時間輪詢觸發程式* 。 這項描述表示當觸發程式引發時，觸發程式會處理所有訊息，然後等候30秒讓佇列或主題訂閱中出現更多訊息。 如果在 30 秒內未出現任何訊息，就會略過觸發程序執行。 如果有，觸發程序會繼續讀取訊息，直到佇列或主題訂閱是空的。 下一次的觸發程序輪詢，會根據觸發程序的屬性指定的循環間隔。

   某些觸發程式，例如， **當一或多個訊息抵達佇列 (自動完成) 觸發程式時 ** ，可能會傳回一或多個訊息。 當這些觸發程式引發時，它們會在觸發程式的 [ **最大訊息計數** ] 屬性指定的訊息數目之間傳回。

    > [!NOTE]
    > 自動完成的觸發程式會自動完成訊息，但只有在下一個觸發程式執行時才會完成。 此行為可能會影響您的邏輯應用程式設計。 例如，如果您將自動完成觸發程式設定為每分鐘檢查一次訊息，但是在服務匯流排端，鎖定持續時間設定為30秒，則結果為完成訊息時所發生的「鎖定已過期」失敗。 您必須將鎖定持續時間設定為超過輪詢間隔的值。

1. 如果您的觸發程式第一次連接到您的服務匯流排命名空間，當邏輯應用程式設計工具提示您輸入連接資訊時，請遵循下列步驟。

   1. 請為您的連線提供名稱，然後選取您的服務匯流排命名空間。

      ![顯示提供連接名稱和選取服務匯流排命名空間的螢幕擷取畫面](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      若要改為手動輸入連接字串，請選取 [ **手動輸入連接資訊**]。 如果您還沒有連接字串，請了解[如何尋找您的連接字串](#permissions-connection-string)。

   1. 選取您的服務匯流排原則，然後選取 [ **建立**]。

      ![顯示選取服務匯流排原則的螢幕擷取畫面](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. 選取您想要的訊息實體，例如佇列或主題。 針對此範例，請選取您的服務匯流排佇列。
   
      ![顯示選取服務匯流排佇列的螢幕擷取畫面](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. 為您選取的觸發程式提供必要的資訊。 若要在動作中加入其他可用的屬性，請開啟 [ **加入新的參數** ] 清單，然後選取您想要的屬性。

   針對此範例的觸發程式，請選取輪詢間隔和檢查佇列的頻率。

   ![顯示在服務匯流排觸發程式上設定輪詢間隔的螢幕擷取畫面](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   如需可用觸發程式和屬性的詳細資訊，請參閱連接器的 [參考頁面](/connectors/servicebus/)。

1. 藉由新增您想要的動作，繼續建立邏輯應用程式。

   例如，您可以新增在新訊息送達時，會傳送電子郵件的動作。 當觸發程序檢查您的佇列並且發現新訊息時，您的邏輯應用程式會針對找到的訊息，執行已選取的動作。

## <a name="add-service-bus-action"></a>新增服務匯流排動作

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 在您要新增動作的步驟下，選取 [ **新增步驟**]。

   或者，若要在步驟之間新增動作，請將指標移至這些步驟之間的箭號。 選取顯示的加號 (**+**) ，然後選取 [ **新增動作**]。

1. 在 [ **選擇動作**] 下的 [搜尋] 方塊中，輸入 "azure service bus" 作為篩選準則。 從 [動作] 清單中選取您想要的動作。 

   在此範例中，請選取 [ **傳送訊息** ] 動作。

   ![顯示選取服務匯流排動作的螢幕擷取畫面](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. 如果您的動作第一次連線到服務匯流排命名空間，當邏輯應用程式設計工具提示您輸入連線資訊時，請遵循下列步驟。

   1. 請為您的連線提供名稱，然後選取您的服務匯流排命名空間。

      ![顯示提供連接名稱和選取服務匯流排命名空間的螢幕擷取畫面](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      若要改為手動輸入連接字串，請選取 [ **手動輸入連接資訊**]。 如果您還沒有連接字串，請了解[如何尋找您的連接字串](#permissions-connection-string)。

   1. 選取您的服務匯流排原則，然後選取 [ **建立**]。

      ![顯示選取服務匯流排原則和選取 [建立] 按鈕的螢幕擷取畫面](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. 選取您想要的訊息實體，例如佇列或主題。 針對此範例，請選取您的服務匯流排佇列。

      ![顯示選取服務匯流排佇列的螢幕擷取畫面](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. 為您選取的動作提供必要的詳細資料。 若要在動作中加入其他可用的屬性，請開啟 [ **加入新的參數** ] 清單，然後選取您想要的屬性。

   例如，選取 [ **內容** ] 和 [ **內容類型** ] 屬性，以便將它們新增至動作。 然後，指定您要傳送之訊息的內容。

   ![顯示提供訊息內容類型和詳細資料的螢幕擷取畫面](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   如需可用動作及其屬性的詳細資訊，請參閱連接器的 [參考頁面](/connectors/servicebus/)。

1. 藉由新增您想要的任何其他動作，繼續建立邏輯應用程式。

   例如，您可以新增動作來傳送電子郵件，以確認已傳送您的郵件。

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]  。

<a name="sequential-convoy"></a>

## <a name="send-correlated-messages-in-order"></a>依序傳送相互關聯的訊息

當您需要以特定順序傳送相關訊息時，可以使用[Azure 服務匯流排連接器](../connectors/connectors-create-api-servicebus.md)來使用[*連續*](/azure/architecture/patterns/sequential-convoy)的群組模式。 相互關聯的訊息具有屬性，可定義這些訊息之間的關聯性，例如服務匯流排中 [會話](../service-bus-messaging/message-sessions.md) 的識別碼。

當您建立邏輯應用程式時，您可以 **使用服務匯流排會話** 範本來選取相互關聯的依序傳遞，以實行連續的群組模式。 如需詳細資訊，請參閱依 [序傳送相關訊息](../logic-apps/send-related-messages-sequential-convoy.md)。

<a name="connector-reference"></a>

## <a name="connector-reference"></a>連接器參考

從服務匯流排，服務匯流排連接器一次最多可將1500個唯一會話儲存至連接器快取，每個 [服務匯流排訊息實體，例如訂用帳戶或主題](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)。 如果會話計數超過此限制，就會從快取中移除舊的會話。 如需詳細資訊，請參閱 [訊息會話](../service-bus-messaging/message-sessions.md)。

如需有關觸發程式、動作和限制的其他技術詳細資料（如連接器的 Swagger 說明所述），請參閱 [連接器參考頁面](/connectors/servicebus/)。 如需 Azure 服務匯流排訊息的詳細資訊，請參閱 [Azure 服務匯流排是什麼](../service-bus-messaging/service-bus-messaging-overview.md)？

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
