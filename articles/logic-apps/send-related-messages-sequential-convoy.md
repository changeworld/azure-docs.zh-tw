---
title: 使用連續群組以依序傳送相互關聯的訊息
description: 使用 Azure Logic Apps 中的順序群組模式，透過 Azure 服務匯流排來傳送相關訊息
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 8c00d2e4f622bcfad7b2468013336f0d936e318c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87048671"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>使用 Azure Logic Apps 中的順序群組，以搭配 Azure 服務匯流排來傳送相關訊息

當您需要以特定順序傳送相互關聯的訊息時，可以在使用[Azure Logic Apps](../logic-apps/logic-apps-overview.md)時，使用[Azure 服務匯流排連接器](../connectors/connectors-create-api-servicebus.md)來遵循[*連續*](/azure/architecture/patterns/sequential-convoy)的群組模式。 相互關聯的訊息具有屬性，可定義這些訊息之間的關聯性，例如服務匯流排中 [會話](../service-bus-messaging/message-sessions.md) 的識別碼。

例如，假設您有一個名為「Session 1」的會話的10則訊息，而且您有5則訊息，代表名為「會話2」的會話，全部都會傳送至相同的 [服務匯流排佇列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)。 您可以建立邏輯應用程式來處理佇列中的訊息，讓來自「會話1」的所有訊息都是由單一觸發程式執行處理，而「會話2」中的所有訊息都是由下一個觸發程式執行所處理。

![一般連續群組模式](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

本文說明如何使用「服務匯流排會話」範本的相互 **關聯順序傳遞** 來建立邏輯應用程式，以實行此模式。 此範本會定義邏輯應用程式工作流程，此工作流程會在 **佇列中收到訊息時 ** 以服務匯流排連接器為開頭 (查看) 觸發程式，以接收來自 [服務匯流排佇列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)的訊息。 以下是此邏輯應用程式所執行的高階步驟：

* 根據觸發程式從服務匯流排佇列讀取的訊息，初始化會話。

* 在目前的工作流程執行期間，從佇列中的相同會話讀取並處理所有訊息。

若要檢查此範本的 JSON 檔案，請參閱 [GitHub： service-bus-sessions.js開啟](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json)。

如需詳細資訊，請參閱 [連續群組模式-Azure 架構雲端設計模式](/azure/architecture/patterns/sequential-convoy)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 服務匯流排命名空間和 [服務匯流排佇列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)，這是您將在邏輯應用程式中使用的訊息實體。 這些專案和您的邏輯應用程式需要使用相同的 Azure 訂用帳戶。 當您建立佇列時，請確定選取 [ **啟用會話** ]。 如果您沒有這些專案，請瞭解 [如何建立服務匯流排命名空間和佇列](../service-bus-messaging/service-bus-create-namespace-portal.md)。

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* 如何建立邏輯應用程式的基本知識。 如果您不熟悉 Azure Logic Apps，請嘗試快速入門， [建立您的第一個自動化工作流程](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>檢查服務匯流排命名空間的存取權

如果您不確定邏輯應用程式是否有存取服務匯流排命名空間的許可權，請確認這些許可權。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 尋找並選取您的服務匯流排 *命名空間*。

1. 在 [命名空間] 功能表的 [ **設定**] 底下，選取 [ **共用存取原則**]。 在 [ **宣告**] 下，檢查您是否有該命名空間的 [ **管理** ] 許可權。

   ![管理服務匯流排命名空間的許可權](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. 現在取得服務匯流排命名空間的連接字串。 稍後當您從邏輯應用程式建立命名空間的連線時，可以使用這個字串。

   1. 在 [ **共用存取原則** ] 窗格的 [ **原則**] 底下，選取 [ **RootManageSharedAccessKey**]。
   
   1. 選取主要連接字串旁的 [複製] 按鈕。 儲存連接字串以供稍後使用。

      ![複製服務匯流排命名空間連接字串](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > 若要確認您的連接字串，是否與您的服務匯流排命名空間或傳訊實體 (例如佇列) 相關聯，請搜尋連接字串中是否有 `EntityPath`  參數。 如果您發現這個參數，表示此連接字串適用於特定實體，但不是可用於您邏輯應用程式的正確字串。

## <a name="create-logic-app"></a>建立邏輯應用程式

在本節中，您會使用 **服務匯流排會話** 範本來建立邏輯應用程式，其中包含用來執行此工作流程模式的觸發程式和動作。 您也會建立與服務匯流排命名空間的連線，並指定您想要使用的服務匯流排佇列名稱。

1. 在 [Azure 入口網站](https://portal.azure.com) 中，建立空白的邏輯應用程式。 從 Azure 首頁選取 [**建立資源**  >  **整合**  >  **邏輯應用程式**]。

1. 範本資源庫出現之後，請在影片和一般觸發程式區段上方滾動。 從 [ **範本** ] 區段中，選取範本， **使用服務匯流排會話依序傳遞相互關聯**。

   ![選取 [使用服務匯流排會話的相互關聯順序傳遞] 範本](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. 當確認方塊出現時，請選取 [ **使用此範本**]。

1. 在 [邏輯應用程式設計工具] 的 [ **服務匯流排** ] 圖形中選取 [ **繼續**]，然後選取 **+** 出現在圖形中的加號 () 。

   ![選取 [繼續] 以連線至 Azure 服務匯流排](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. 現在請選擇其中一個選項來建立服務匯流排連接：

   * 若要使用您稍早從服務匯流排命名空間複製的連接字串，請遵循下列步驟：

     1. 選取 [ **手動輸入連接資訊**]。

     1. 針對 [連線 **名稱**]，提供連接的名稱。 針對 [ **連接字串**]，貼上您的命名空間連接字串，然後選取 [ **建立**]，例如：

        ![輸入連接名稱和服務匯流排連接字串](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > 如果您沒有此連接字串，請瞭解如何 [尋找和複製服務匯流排命名空間連接字串](#permissions-connection-string)。

   * 若要從目前的 Azure 訂用帳戶選取服務匯流排命名空間，請遵循下列步驟：

     1. 針對 [連線 **名稱**]，提供連接的名稱。 針對 **服務匯流排命名空間**，請選取您的服務匯流排命名空間，例如：

        ![輸入連線名稱，然後選取服務匯流排命名空間](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. 當下一個窗格出現時，請選取您的服務匯流排原則，然後選取 [ **建立**]。

        ![選取 [服務匯流排原則]，然後選取 [建立]](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. 當您完成時，請選取 [ **繼續**]。

   邏輯應用程式設計工具現在會顯示 **使用服務匯流排會話範本的相互關聯順序傳遞** ，其中包含預先填入的工作流程與觸發程式和動作，包括兩個可執行遵循模式之錯誤處理的範圍 `Try-Catch` 。

現在您可以深入瞭解範本中的觸發程式和動作，或向前跳至 [提供邏輯應用程式範本的值](#complete-template)。

<a name="template-summary"></a>

## <a name="template-summary"></a>範本摘要

以下是折迭詳細資料時， **使用服務匯流排會話範本的相關依序傳遞** 中的最上層工作流程：

![範本的最上層工作流程](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| 名稱 | 說明 |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | 根據指定的週期，此服務匯流排觸發程式會檢查指定的服務匯流排佇列中是否有任何訊息。 如果訊息存在於佇列中，則會引發觸發程式，以建立並執行工作流程實例。 <p><p>「 *查看鎖定* 」一詞表示觸發程式傳送要求以從佇列中取出訊息。 如果訊息存在，觸發程式就會取出並鎖定訊息，以便在鎖定期限到期之前，不會在該訊息上進行其他處理。 如需詳細資料，請 [將會話初始化](#initialize-session)。 |
| **`Init isDone`** | 這個 [ [**初始化變數** ] 動作](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable) 會建立設定為的布林值變數， `false` 並指出下列條件是否成立： <p><p>-會話中沒有其他訊息可供讀取。 <br>-會話鎖定不再需要更新，因此目前的工作流程實例可以完成。 <p><p>如需詳細資訊，請參閱 [初始化會話](#initialize-session)。 |
| **`Try`** | 此 [**範圍** 動作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) 包含執行以處理訊息的動作。 如果領域中發生問題 `Try` ，則後續的 `Catch` **範圍** 動作會處理該問題。 如需詳細資訊，請參閱「 [Try」範圍](#try-scope)。 |
| **`Catch`**| 此 [**範圍** 動作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) 包含先前範圍發生問題時所執行的動作 `Try` 。 如需詳細資訊，請參閱「 [Catch」範圍](#catch-scope)。 |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>"Try" 範圍

以下是折迭 `Try` 詳細資料時， [範圍動作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) 中的頂層流程：

![「試用」範圍動作工作流程](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| 名稱 | 說明 |
|------|-------------|
| **`Send initial message to topic`** | 您可以將此動作取代為您想要在佇列中處理第一個訊息的任何動作。 會話識別碼會指定會話。 <p><p>針對此範本，服務匯流排動作會將第一則訊息傳送至服務匯流排主題。 如需詳細資訊，請參閱 [處理初始訊息](#handle-initial-message)。 |
|  (平行分支)  | 此 [平行分支動作](../logic-apps/logic-apps-control-flow-branches.md) 會建立兩個路徑： <p><p>-分支 #1：繼續處理訊息。 如需詳細資訊，請參閱 [分支 #1：在佇列中完成初始訊息](#complete-initial-message)。 <p><p>-分支 #2：如果發生錯誤，請放棄訊息，然後再由另一個觸發程式執行發行。 如需詳細資訊，請參閱 [分支 #2：放棄佇列中的初始訊息](#abandon-initial-message)。 <p><p>這兩個路徑稍後會在 **佇列中的關閉會話** 中進行聯結，並成功執行下一列所述的動作。 |
| **`Close a session in a queue and succeed`** | 此服務匯流排動作會聯結先前描述的分支，並在發生下列任一事件之後關閉佇列中的會話： <p><p>-工作流程已完成處理佇列中的可用訊息。 <br>-工作流程會放棄初始訊息，因為發生錯誤。 <p><p>如需詳細資訊，請參閱 [關閉佇列中的會話，並成功](#close-session-succeed)。 |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>分支 #1：在佇列中完成初始訊息

| 名稱 | 說明 |
|------|-------------|
| `Complete initial message in queue` | 此服務匯流排動作會將已成功取出的訊息標示為已完成，並從佇列中移除訊息以防止重新處理。 如需詳細資訊，請參閱 [處理初始訊息](#handle-initial-message)。 |
| `While there are more messages for the session in the queue` | 這個[ **Until**迴圈](../logic-apps/logic-apps-control-flow-loops.md#until-loop)會在訊息存在或經過一小時之前，繼續取得訊息。 如需此迴圈中動作的詳細資訊，請參閱 [佇列中的會話有多個訊息](#while-more-messages-for-session)。 |
| **`Set isDone = true`** | 當沒有其他訊息存在時，此 [**集合變數** 動作](../logic-apps/logic-apps-create-variables-store-values.md#set-variable) 會設定 `isDone` 為 `true` 。 |
| **`Renew session lock until cancelled`** | 這個[ **Until**迴圈](../logic-apps/logic-apps-control-flow-loops.md#until-loop)可確保此邏輯應用程式會在訊息存在時或在一小時之後，保留會話鎖定。 如需此迴圈中動作的詳細資訊，請參閱 [更新會話鎖定直到取消為止](#renew-session-while-messages-exist)。 |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>分支 #2：放棄佇列中的初始訊息

如果處理第一個訊息的動作失敗，則服務匯流排動作會 **從佇列中放棄初始訊息**，然後釋放訊息，以供另一個工作流程實例執行以收取和處理。 如需詳細資訊，請參閱 [處理初始訊息](#handle-initial-message)。

<a name="catch-scope"></a>

### <a name="catch-scope"></a>「Catch」範圍

如果範圍中的動作 `Try` 失敗，則邏輯應用程式仍然必須關閉會話。 `Catch` [scope action](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)當 `Try` 範圍動作產生狀態、、或時，就會執行範圍動作 `Failed` `Skipped` `TimedOut` 。 範圍會傳回錯誤訊息，其中包含發生問題的會話識別碼，並終止邏輯應用程式。

以下是折迭 `Catch` 詳細資料時，範圍動作中的頂層流程：

![「Catch」範圍動作工作流程](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| 名稱 | 說明 |
|------|-------------|
| **`Close a session in a queue and fail`** | 此服務匯流排動作會關閉佇列中的會話，讓會話鎖定不會保持開啟。 如需詳細資訊，請參閱 [關閉佇列中的會話並](#close-session-fail)使其失敗。 |
| **`Find failure msg from 'Try' block`** | 此 [**篩選陣列** 動作](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) 會根據指定的準則，從範圍內所有動作的輸入和輸出建立陣列 `Try` 。 在此情況下，此動作會傳回產生狀態之動作的輸出 `Failed` 。 如需詳細資訊，請參閱 [尋找來自 ' Try ' 區塊的失敗](#find-failure-message)訊息。 |
| **`Select error details`** | 此 [**選取** 動作](../logic-apps/logic-apps-perform-data-operations.md#select-action) 會根據指定的準則建立包含 JSON 物件的陣列。 這些 JSON 物件是從上一個動作所建立之陣列中的值所建立 `Find failure msg from 'Try' block` 。 在此情況下，此動作會傳回陣列，其中包含從上一個動作傳回的錯誤詳細資料所建立的 JSON 物件。 如需詳細資訊，請參閱 [選取錯誤詳細資料](#select-error-details)。 |
| **`Terminate`** | 這個 [**終止** 動作](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) 會停止執行工作流程，取消任何進行中的動作，並略過任何剩餘的動作，然後從動作傳回指定的狀態、會話識別碼和錯誤結果 `Select error details` 。 如需詳細資訊，請參閱 [終止邏輯應用程式](#terminate-logic-app)。 |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>完成範本

若要 **使用服務匯流排會話** 範本來提供觸發程式和動作在相互關聯的順序傳遞中的值，請遵循下列步驟。 您必須提供所有必要值（以星號 (**\***) ），才能儲存邏輯應用程式。

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>初始化工作階段

* 在 **佇列中收到訊息 (查看) ** 觸發程式時，請提供此資訊，讓範本可以使用 **會話識別碼** 屬性來初始化會話，例如：

  ![「在佇列中收到訊息時 (查看鎖定) 」的服務匯流排觸發程式詳細資料](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > 一開始，輪詢間隔設定為三分鐘，因此邏輯應用程式的執行頻率會比您預期的還要高，因此會導致非預期的計費費用。 在理想的情況下，請將間隔和頻率設定為30秒，讓邏輯應用程式在訊息抵達時立即觸發。

  | 屬性 | 此案例的必要 | 值 | 說明 |
  |----------|----------------------------|-------|-------------|
  | **佇列名稱** | 是 | <*佇列名稱*> | 先前建立的服務匯流排佇列名稱。 此範例使用「Fabrikam-服務-匯流排-佇列」。 |
  | **佇列類型** | 是 | **主要** | 您的主要服務匯流排佇列 |
  | **會話識別碼** | 是 | **下一個可用** | 此選項會根據服務匯流排佇列中訊息的會話識別碼，取得每個觸發程式執行的會話。 會話也會被鎖定，讓其他邏輯應用程式或其他用戶端無法處理與此會話相關的訊息。 工作流程的後續動作會處理與該會話相關聯的所有訊息，如本文稍後所述。 <p><p>以下是其他 **會話識別碼** 選項的詳細資訊： <p>- **None**：預設選項，這會導致沒有會話，且無法用來執行連續的群組模式。 <p>- **輸入自訂值**：當您知道您想要使用的會話識別碼，且一律想要執行該會話識別碼的觸發程式時，請使用此選項。 <p>**注意**：服務匯流排連接器一次只能從 Azure 服務匯流排到連接器快取，儲存有限數目的唯一會話。 如果會話計數超過此限制，就會從快取中移除舊的會話。 如需詳細資訊，請參閱 [雲端中的 Exchange 訊息與 Azure Logic Apps 和 Azure 服務匯流排](../connectors/connectors-create-api-servicebus.md#connector-reference)。 |
  | **間隔** | 是 | <*間隔數*> | 檢查訊息之前的週期之間的時間單位數。 |
  | **頻率** | 是 | **秒**、**分鐘**、**小時**、**天**、**週**或**月** | 檢查訊息時，週期所使用的時間單位。 <p>**秘訣**：若要加入 **時區** 或 **開始時間**，請從 [ **加入新的參數** ] 清單中選取這些屬性。 |
  |||||

  如需更多觸發程式資訊，請參閱 [服務匯流排-在佇列中收到訊息時 (查看鎖定) ](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock))。 觸發程式會輸出 [ServiceBusMessage](/connectors/servicebus/#servicebusmessage)。

初始化會話之後，工作流程會使用 [ **初始化變數** ] 動作來建立一開始設定為的布林值變數， `false` 並指出下列條件是否成立： 

* 無法讀取會話中的訊息。

* 不再需要更新會話鎖定，即可完成目前的工作流程實例。

!["Init 作業" 的「初始化變數」動作詳細資料](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

接下來，在 **Try** 區塊中，工作流程會在所讀取的第一個訊息上執行動作。

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>處理初始訊息

第一個動作是預留位置服務匯流排動作， **將初始訊息傳送至主題**，您可以將它取代為您想要在佇列中處理第一個訊息的任何其他動作。 會話識別碼會指定訊息來源的會話。

預留位置服務匯流排動作會將第一則訊息傳送至 **會話識別碼** 屬性所指定的服務匯流排主題。 如此一來，與特定會話相關聯的所有訊息都會移至相同主題。 此範本中後續動作的所有 **會話識別碼** 屬性都使用相同的會話識別碼值。

![「傳送初始訊息至主題」的服務匯流排動作詳細資料](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. 在服務匯流排動作中， **完成佇列中的初始訊息**，提供服務匯流排佇列的名稱，並將所有其他預設屬性值保留在動作中。

   ![「佇列中的完整初始訊息」的服務匯流排動作詳細資料](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. 在服務匯流排動作中， **放棄佇列中的初始訊息**，提供服務匯流排佇列的名稱，並將所有其他預設屬性值保留在動作中。

   ![「從佇列放棄初始訊息」的服務匯流排動作詳細資料](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

接下來，您將針對 **佇列動作中完成初始訊息** 之後的動作提供必要的資訊。 **當佇列迴圈中有更多訊息可供會話使用時**，您將從中的動作開始。

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>佇列中的會話有更多訊息

這個[ **Until**迴圈](../logic-apps/logic-apps-control-flow-loops.md#until-loop)會在訊息存在於佇列中或在一小時過後，執行這些動作。 若要變更迴圈的時間限制，請編輯迴圈的 **Timeout** 屬性值。

* 當訊息存在時，從佇列取得額外的訊息。

* 檢查剩餘的訊息數目。 如果訊息仍然存在，請繼續處理訊息。 如果不再有訊息，工作流程會將 `isDone` 變數設為 `true` ，並結束迴圈。

![Until 迴圈-在佇列中處理訊息](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. 在服務匯流排動作中， **從會話取得額外的訊息**，並提供服務匯流排佇列的名稱。 否則，請在動作中保留所有其他的預設屬性值。

   > [!NOTE]
   > 根據預設，訊息數目上限會設定為 `175` ，但此限制會受到服務匯流排中 [訊息大小] 和 [訊息大小上限] 屬性的影響。 如需詳細資訊，請參閱 [佇列的訊息大小](../service-bus-messaging/service-bus-quotas.md)。

   ![服務匯流排動作-「從會話取得其他訊息」](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   接下來，工作流程會分割成這些平行分支：

   * 如果檢查額外的訊息時，發生錯誤或失敗，請將 `isDone` 變數設定為 `true` 。

   * **如果有任何**條件會檢查剩餘的訊息數目是否為零，則處理訊息。 如果有 false 且有更多訊息存在，請繼續處理。 如果為 true 且不存在其他訊息，則工作流程會將 `isDone` 變數設定為 `true` 。

   ![條件-處理訊息（如果有的話）](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   在 [ **如果為 false** ] 區段中， **for each** 迴圈會以先進先出的連續處理每個訊息， (FIFO) 。 在迴圈的 **設定**中， **並行控制** 設定是設定為 `1` ，因此一次只會處理一則訊息。

   !["For each" 迴圈-一次處理一個訊息](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. 針對服務匯流排動作，請 **完成佇列中的訊息** ，並 **放棄佇列中的訊息**，並提供服務匯流排佇列的名稱。

   ![服務匯流排動作-「完成佇列中的訊息」和「放棄佇列中的訊息」](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   之後， **當佇列中的會話有更多訊息完成時** ，工作流程就會將 `isDone` 變數設為 `true` 。

接下來，您將在 [ **更新會話鎖定** ] 中提供動作的必要資訊，直到取消迴圈為止。

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>更新會話鎖定直到取消

這個[ **Until**迴圈](../logic-apps/logic-apps-control-flow-loops.md#until-loop)可確保此邏輯應用程式會在訊息存在於佇列中時，或在一小時內執行這些動作之前，保留會話鎖定。 若要變更迴圈的時間限制，請編輯迴圈的 **Timeout** 屬性值。

* 延遲25秒或小於所處理佇列之鎖定超時時間的時間量。 最小的鎖定持續時間為30秒，因此預設值已足夠。 不過，您可以藉由適當地調整來優化迴圈執行的次數。

* 檢查變數是否 `isDone` 設定為 `true` 。

  * 如果未 `isDone` 設定為 `true` ，工作流程仍在處理訊息，因此工作流程會更新佇列中會話的鎖定，然後再次檢查迴圈條件。

    您必須在服務匯流排動作中提供服務匯流排佇列的名稱，在 [**佇列中更新會話的鎖定**](#renew-lock-on-session)。

  * 如果 `isDone` 設定為 `true` ，工作流程就不會更新佇列中會話的鎖定，並結束迴圈。

  ![Until 迴圈-「更新會話鎖定直到取消」](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>更新佇列中的會話鎖定

當工作流程仍在處理訊息時，此服務匯流排動作會更新佇列中會話的鎖定。

* 在服務匯流排動作中， **更新佇列中會話的鎖定**，提供服務匯流排佇列的名稱。

  ![服務匯流排動作-「更新佇列中的會話時鎖定」](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

接下來，您將提供服務匯流排動作的必要資訊、 **關閉佇列中的會話，然後成功**。

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>關閉佇列中的會話並成功

當工作流程完成處理佇列中的所有可用訊息，或工作流程放棄初始訊息之後，此服務匯流排動作會關閉佇列中的會話。

* 在服務匯流排動作中， **關閉佇列中的會話並成功，並**提供服務匯流排佇列的名稱。

  ![服務匯流排動作-「關閉佇列中的會話並成功」](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

下列各節描述區段中的動作 `Catch` ，此動作會處理工作流程中發生的錯誤和例外狀況。

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>關閉佇列中的會話並失敗

此服務匯流排動作一律會作為範圍中的第一個動作執行 `Catch` ，並關閉佇列中的會話。

* 在 [服務匯流排] 動作中， **關閉佇列中的會話並失敗**，提供服務匯流排佇列的名稱。

  ![服務匯流排動作-「關閉佇列中的會話並失敗」](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

接下來，工作流程會建立陣列，其中包含範圍中所有動作的輸入和輸出， `Try` 讓邏輯應用程式可以存取發生錯誤或失敗的相關資訊。

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>從 ' Try ' 區塊尋找失敗訊息

此[**篩選陣列**動作](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action)會 `Try` 根據指定[ `result()` 的](../logic-apps/workflow-definition-language-functions-reference.md#result)準則使用函式，建立一個陣列，其中包含範圍內所有動作的輸入和輸出。 在此情況下，此動作會使用函式和函式傳回具有狀態之動作的輸出 `Failed` 。 [ `equals()` ](../logic-apps/workflow-definition-language-functions-reference.md#equals) [ `item()` ](../logic-apps/workflow-definition-language-functions-reference.md#item)

![篩選陣列動作-「尋找來自 ' Try ' block ' 的失敗訊息」](./media/send-related-messages-sequential-convoy/find-failure-message.png)

以下是此動作的 JSON 定義：

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

接下來，工作流程會建立具有 JSON 物件的陣列，其中包含從動作傳回的陣列中的錯誤資訊 `Find failure msg from 'Try' block` 。

<a name="select-error-details"></a>

### <a name="select-error-details"></a>選取錯誤詳細資料

此 [**選取** 動作](../logic-apps/logic-apps-perform-data-operations.md#select-action) 會根據從上一個動作輸出的輸入陣列，建立包含 JSON 物件的陣列 `Find failure msg from 'Try' block` 。 具體來說，這個動作會傳回陣列，其中只包含陣列中每個物件的指定屬性。 在此情況下，陣列包含動作名稱和錯誤結果屬性。

![選取動作-「選取錯誤詳細資料」](./media/send-related-messages-sequential-convoy/select-error-details.png)

以下是此動作的 JSON 定義：

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

接下來，工作流程會停止邏輯應用程式的執行，並傳回執行狀態以及發生錯誤或失敗的詳細資訊。

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>終止邏輯應用程式執行

此 [**終止** 動作](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) 會停止邏輯應用程式的執行，並傳回 `Failed` 做為邏輯應用程式執行的狀態，以及該動作的會話識別碼和錯誤結果 `Select error details` 。

![停止邏輯應用程式執行的終止動作](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

以下是此動作的 JSON 定義：

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>儲存並執行邏輯應用程式

完成範本之後，您現在可以儲存邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

若要測試您的邏輯應用程式，請將訊息傳送至服務匯流排佇列。 

## <a name="next-steps"></a>接下來的步驟

* 深入瞭解 [服務匯流排連接器的觸發程式和動作](/connectors/servicebus/)
