---
title: 使用順序群組依序傳送相互關聯的訊息
description: 在 Azure Logic Apps 中使用順序群組模式，依序傳送相關訊息 Azure 服務匯流排
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 8c00d2e4f622bcfad7b2468013336f0d936e318c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87048671"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>在 Azure Logic Apps 中使用順序群組，以 Azure 服務匯流排的方式傳送相關訊息

當您需要以特定順序傳送相互關聯的訊息時，您可以在使用[Azure Logic Apps](../logic-apps/logic-apps-overview.md)時，使用[Azure 服務匯流排連接器](../connectors/connectors-create-api-servicebus.md)來遵循[*順序*群組模式](/azure/architecture/patterns/sequential-convoy)。 相互關聯的訊息具有屬性，可定義這些訊息之間的關聯性，例如服務匯流排中的[會話](../service-bus-messaging/message-sessions.md)識別碼。

例如，假設您有10個名為 "Session 1" 的訊息，而且有5則訊息代表名為「會話2」的會話全部都傳送至相同的[服務匯流排佇列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)。 您可以建立一個邏輯應用程式，以處理來自佇列的訊息，使「會話1」中的所有訊息都是由單一觸發程式執行處理，而「會話2」的所有訊息則是由下一個觸發程式執行處理。

![一般順序群組模式](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

本文說明如何使用「**服務匯流排會話**」範本來建立邏輯應用程式，以利用相互關聯的依序傳遞來執行此模式。 此範本會定義一個邏輯應用程式工作流程，它會在**佇列（查看鎖定）觸發程式中收到訊息時**，以服務匯流排連接器為開頭，這會從[服務匯流排佇列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)接收訊息。 以下是此邏輯應用程式執行的高階步驟：

* 根據觸發程式從服務匯流排佇列讀取的訊息來初始化會話。

* 在目前的工作流程執行期間，讀取和處理來自佇列中相同會話的所有訊息。

若要檢查此範本的 JSON 檔案，請參閱[GitHub：上的 service-bus-sessions.js](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json)。

如需詳細資訊，請參閱[順序群組模式-Azure 架構雲端設計模式](/azure/architecture/patterns/sequential-convoy)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 服務匯流排命名空間和[服務匯流排佇列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)，這是您將在邏輯應用程式中使用的訊息實體。 這些專案和您的邏輯應用程式都必須使用相同的 Azure 訂用帳戶。 請確定您在建立佇列時選取 [**啟用會話**]。 如果您沒有這些專案，請瞭解[如何建立您的服務匯流排命名空間和佇列](../service-bus-messaging/service-bus-create-namespace-portal.md)。

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* 如何建立邏輯應用程式的基本知識。 如果您不熟悉 Azure Logic Apps，請嘗試快速入門，以[建立您的第一個自動化工作流程](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>檢查服務匯流排命名空間的存取權

如果您不確定邏輯應用程式是否有許可權可以存取您的服務匯流排命名空間，請確認這些許可權。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 尋找並選取您的服務匯流排*命名空間*。

1. 在 [命名空間] 功能表的 [**設定**] 底下，選取 [**共用存取原則**]。 在 [**宣告**] 底下，確認您有該命名空間的 [**管理**] 許可權。

   ![管理服務匯流排命名空間的許可權](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. 現在取得服務匯流排命名空間的連接字串。 稍後當您從邏輯應用程式建立命名空間的連線時，可以使用這個字串。

   1. 在 [**共用存取原則**] 窗格的 [**原則**] 底下，選取 [ **RootManageSharedAccessKey**]。
   
   1. 在您的主要連接字串旁，選取 [複製] 按鈕。 儲存連接字串以供稍後使用。

      ![複製服務匯流排命名空間連接字串](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > 若要確認您的連接字串，是否與您的服務匯流排命名空間或傳訊實體 (例如佇列) 相關聯，請搜尋連接字串中是否有 `EntityPath`  參數。 如果您發現這個參數，表示此連接字串適用於特定實體，但不是可用於您邏輯應用程式的正確字串。

## <a name="create-logic-app"></a>建立邏輯應用程式

在本節中，您會使用「**服務匯流排會話**」範本來建立邏輯應用程式（使用「依序傳遞」的相互關聯），其中包含用來執行此工作流程模式的觸發程式和動作。 您也會建立服務匯流排命名空間的連線，並指定您想要使用之服務匯流排佇列的名稱。

1. 在 [Azure 入口網站](https://portal.azure.com) 中，建立空白的邏輯應用程式。 從 Azure 首頁選取 [**建立資源**] [整合] [  >  **Integration**  >  **邏輯應用程式**]。

1. 範本資源庫出現之後，請在影片和一般觸發程式區段上滾動。 從 [**範本**] 區段中，選取 [**使用服務匯流排會話相互關聯的依序傳遞**] 範本。

   ![選取 [使用服務匯流排會話相互關聯的順序傳遞] 範本](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. 當確認方塊出現時，請選取 [**使用此範本**]。

1. 在邏輯應用程式設計工具的 [**服務匯流排**] 圖形中，選取 [**繼續**]，然後選取 **+** 出現在圖形中的加號（）。

   ![選取 [繼續] 以連線至 Azure 服務匯流排](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. 現在，請選擇下列任一選項來建立服務匯流排連接：

   * 若要使用您稍早從服務匯流排命名空間複製的連接字串，請遵循下列步驟：

     1. 選取 [**手動輸入連接資訊**]。

     1. 針對 [連線**名稱**]，提供連線的名稱。 針對 [**連接字串**]，貼上您的命名空間連接字串，然後選取 [**建立**]，例如：

        ![輸入連接名稱和服務匯流排連接字串](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > 如果您沒有此連接字串，請瞭解如何[尋找並複製服務匯流排命名空間連接字串](#permissions-connection-string)。

   * 若要從目前的 Azure 訂用帳戶中選取服務匯流排命名空間，請遵循下列步驟：

     1. 針對 [連線**名稱**]，提供連線的名稱。 針對**服務匯流排命名**空間，請選取您的服務匯流排命名空間，例如：

        ![輸入連接名稱，然後選取服務匯流排命名空間](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. 當下一個窗格出現時，請選取您的服務匯流排原則，然後選取 [**建立**]。

        ![選取服務匯流排原則，然後按一下 [建立]](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. 當您完成時，請選取 [**繼續**]。

   邏輯應用程式設計工具現在會**使用服務匯流排會話**範本來顯示相互關聯的依序傳遞，其中包含具有觸發程式和動作的預先填入工作流程，包括兩個範圍，會執行遵循模式的錯誤處理 `Try-Catch` 。

現在您可以深入瞭解範本中的觸發程式和動作，或直接跳至[提供邏輯應用程式範本的值](#complete-template)。

<a name="template-summary"></a>

## <a name="template-summary"></a>範本摘要

以下是折迭詳細資料時，**使用服務匯流排會話**範本，在相互關聯的依序傳遞中的最上層工作流程：

![範本的最上層工作流程](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| 名稱 | 說明 |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | 根據指定的週期，此服務匯流排觸發程式會檢查指定的服務匯流排佇列中是否有任何訊息。 如果訊息存在於佇列中，則會引發觸發程式，以建立和執行工作流程實例。 <p><p>「*查看鎖定*」一詞表示觸發程式會傳送要求，以從佇列中取出訊息。 如果訊息存在，則觸發程式會抓取並鎖定訊息，直到鎖定期限到期為止，該訊息上不會進行其他處理。 如需詳細資訊，請[初始化會話](#initialize-session)。 |
| **`Init isDone`** | 這個 [ [**初始化變數**] 動作](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable)會建立設定為的布林值變數 `false` ，並指出下列條件是否成立： <p><p>-會話中沒有其他訊息可供讀取。 <br>-不再需要更新會話鎖定，即可完成目前的工作流程實例。 <p><p>如需詳細資訊，請參閱[初始化會話](#initialize-session)。 |
| **`Try`** | 此[**範圍**動作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)包含執行來處理訊息的動作。 如果範圍內發生問題 `Try` ，則後續的 `Catch` **範圍**動作會處理該問題。 如需詳細資訊，請參閱「[嘗試」範圍](#try-scope)。 |
| **`Catch`**| 此[**範圍**動作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)包含在上述範圍發生問題時執行的動作 `Try` 。 如需詳細資訊，請參閱「 [Catch」範圍](#catch-scope)。 |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>「嘗試」範圍

以下是折迭 `Try` 詳細資料時，[範圍動作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)中的最上層流程：

![「嘗試」範圍動作工作流程](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| 名稱 | 說明 |
|------|-------------|
| **`Send initial message to topic`** | 您可以使用您想要處理佇列中會話第一個訊息的任何動作來取代此動作。 會話識別碼會指定會話。 <p><p>針對此範本，服務匯流排動作會將第一個訊息傳送至服務匯流排主題。 如需詳細資訊，請參閱[處理初始訊息](#handle-initial-message)。 |
| （平行分支） | 這個[平行分支動作](../logic-apps/logic-apps-control-flow-branches.md)會建立兩個路徑： <p><p>-Branch #1：繼續處理訊息。 如需詳細資訊，請參閱[Branch #1：完成佇列中的初始訊息](#complete-initial-message)。 <p><p>-Branch #2：如果發生錯誤，則會放棄訊息，另一個觸發程式執行則會發行 pickup。 如需詳細資訊，請參閱[Branch #2：放棄佇列中的初始訊息](#abandon-initial-message)。 <p><p>這兩個路徑稍後會在**佇列中加入關閉會話，並**在下一個資料列中說明成功動作。 |
| **`Close a session in a queue and succeed`** | 此服務匯流排動作會聯結先前所述的分支，並在發生下列任一事件之後關閉佇列中的會話： <p><p>-工作流程已完成處理佇列中的可用訊息。 <br>-工作流程會放棄初始訊息，因為發生錯誤。 <p><p>如需詳細資訊，請參閱[關閉佇列中的會話並成功](#close-session-succeed)。 |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>分支 #1：在佇列中完成初始訊息

| 名稱 | 說明 |
|------|-------------|
| `Complete initial message in queue` | 此服務匯流排動作會將成功抓取的訊息標示為已完成，並將訊息從佇列中移除，以避免重新處理。 如需詳細資訊，請參閱[處理初始訊息](#handle-initial-message)。 |
| `While there are more messages for the session in the queue` | 在訊息存在或一小時過後， [ **Until**迴圈](../logic-apps/logic-apps-control-flow-loops.md#until-loop)會繼續取得訊息。 如需此迴圈中動作的詳細資訊，請參閱[佇列中的會話有更多訊息](#while-more-messages-for-session)。 |
| **`Set isDone = true`** | 當沒有其他訊息存在時，此[**設定變數**動作](../logic-apps/logic-apps-create-variables-store-values.md#set-variable)會將設定 `isDone` 為 `true` 。 |
| **`Renew session lock until cancelled`** | 這個[ **Until**迴圈](../logic-apps/logic-apps-control-flow-loops.md#until-loop)可確保會話鎖定是由這個邏輯應用程式持有，而訊息存在，或直到一小時後才會通過。 如需此迴圈中動作的詳細資訊，請參閱[更新會話鎖定直到取消為止](#renew-session-while-messages-exist)。 |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>分支 #2：放棄佇列中的初始訊息

如果處理第一則訊息的動作失敗，服務匯流排動作會**放棄佇列中的初始訊息**，則會釋出另一個工作流程實例的訊息，以供執行並進行處理。 如需詳細資訊，請參閱[處理初始訊息](#handle-initial-message)。

<a name="catch-scope"></a>

### <a name="catch-scope"></a>「Catch」範圍

如果範圍中的動作 `Try` 失敗，則邏輯應用程式仍然必須關閉會話。 範圍動作會在 `Catch` [scope action](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) `Try` 範圍動作產生狀態、、或時執行 `Failed` `Skipped` `TimedOut` 。 範圍會傳回錯誤訊息，其中包含發生問題的會話識別碼，並終止邏輯應用程式。

以下是折迭 `Catch` 詳細資料時，範圍動作中的最上層流程：

![「Catch」範圍動作工作流程](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| 名稱 | 說明 |
|------|-------------|
| **`Close a session in a queue and fail`** | 此服務匯流排動作會關閉佇列中的會話，讓會話鎖定不會保持開啟狀態。 如需詳細資訊，請參閱[關閉佇列中的會話並失敗](#close-session-fail)。 |
| **`Find failure msg from 'Try' block`** | 此[**篩選陣列**動作](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action)會 `Try` 根據指定的準則，從範圍內的所有動作中建立陣列。 在此情況下，此動作會傳回產生狀態之動作的輸出 `Failed` 。 如需詳細資訊，請參閱[從 ' Try ' 區塊尋找失敗](#find-failure-message)訊息。 |
| **`Select error details`** | 此[**選取**動作](../logic-apps/logic-apps-perform-data-operations.md#select-action)會根據指定的準則，建立包含 JSON 物件的陣列。 這些 JSON 物件是由上一個動作所建立之陣列中的值所建立 `Find failure msg from 'Try' block` 。 在此情況下，此動作會傳回陣列，其中包含從上一個動作傳回的錯誤詳細資料所建立的 JSON 物件。 如需詳細資訊，請參閱[選取錯誤詳細資料](#select-error-details)。 |
| **`Terminate`** | 這個[**終止**動作](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action)會停止執行工作流程、取消任何進行中的動作、略過任何剩餘的動作，並從動作傳回指定的狀態、會話識別碼和錯誤結果 `Select error details` 。 如需詳細資訊，請參閱[終止邏輯應用程式](#terminate-logic-app)。 |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>完成範本

若要在 [**使用服務匯流排會話的相互關聯順序傳遞**] 範本中提供觸發程式和動作的值，請遵循下列步驟。 您必須提供所有必要值（以星號（ **\*** ）標示），才能儲存邏輯應用程式。

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>初始化工作階段

* 針對 [**在佇列中收到訊息時（查看鎖定）** ] 觸發程式，請提供這項資訊，讓範本可以使用 [**會話識別碼**] 屬性來初始化會話，例如：

  ![服務匯流排「在佇列中收到訊息時（查看鎖定）」的觸發詳細資料](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > 一開始，輪詢間隔會設定為3分鐘，讓邏輯應用程式的執行頻率不會超過預期，並導致無法預測的計費費用。 在理想的情況下，請將間隔和頻率設定為30秒，讓邏輯應用程式在訊息抵達時立即觸發。

  | 屬性 | 此案例的必要 | 值 | 說明 |
  |----------|----------------------------|-------|-------------|
  | **佇列名稱** | 是 | <*佇列-名稱*> | 您先前建立服務匯流排佇列的名稱。 這個範例會使用「Fabrikam-服務匯流排-佇列」。 |
  | **佇列類型** | 是 | **主要** | 您的主要服務匯流排佇列 |
  | **會話識別碼** | 是 | **下一個可用** | 此選項會根據服務匯流排佇列中訊息的會話識別碼，取得每個觸發程式執行的會話。 會話也會鎖定，讓其他邏輯應用程式或其他用戶端無法處理與此會話相關的訊息。 工作流程的後續動作會處理與該會話相關聯的所有訊息，如本文稍後所述。 <p><p>以下是其他**會話識別碼**選項的詳細資訊： <p>- **None**：預設選項，這會導致沒有會話，而且無法用來執行連續的群組模式。 <p>- **輸入自訂值**：當您知道想要使用的會話識別碼，而且您一律想要執行該會話識別碼的觸發程式時，請使用此選項。 <p>**注意**：服務匯流排連接器可以一次儲存有限數目的唯一會話，從 Azure 服務匯流排到連接器快取。 如果會話計數超過此限制，則會從快取中移除舊的會話。 如需詳細資訊，請參閱[使用 Azure Logic Apps 和 Azure 服務匯流排在雲端中交換訊息](../connectors/connectors-create-api-servicebus.md#connector-reference)。 |
  | **間隔** | 是 | <*間隔數*> | 檢查訊息之前，週期之間的時間單位數。 |
  | **頻率** | 是 | **秒**、**分鐘**、**小時**、**天**、**週**或**月** | 檢查是否有訊息時，所要使用之週期的時間單位。 <p>**提示**：若要新增**時區**或**開始時間**，請從 [**加入新的參數**] 清單中選取這些屬性。 |
  |||||

  如需詳細的觸發程式資訊，請參閱[服務匯流排-在佇列中收到訊息時（查看鎖定）](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock))。 觸發程式會輸出[ServiceBusMessage](/connectors/servicebus/#servicebusmessage)。

初始化會話之後，工作流程會使用 [**初始化變數**] 動作來建立一個初始設定為的布林值變數， `false` 並指出下列條件是否成立： 

* 會話中沒有其他訊息可供讀取。

* 會話鎖定不再需要更新，因此目前的工作流程實例可以完成。

!["Init 作業" 的「初始化變數」動作詳細資料](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

接下來，在**Try**區塊中，工作流程會在讀取的第一個訊息上執行動作。

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>處理初始訊息

第一個動作是一個預留位置服務匯流排動作，**將初始訊息傳送至主題**，您可以使用您想要處理佇列中會話第一個訊息的任何其他動作來取代。 會話識別碼會指定訊息來源的會話。

預留位置服務匯流排動作會將第一個訊息傳送至 [**會話識別碼**] 屬性所指定的服務匯流排主題。 如此一來，與特定會話相關聯的所有訊息都會移至相同的主題。 此範本中後續動作的所有**會話識別碼**屬性都會使用相同的會話識別碼值。

![[將初始訊息傳送至主題] 服務匯流排動作詳細資料](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. 在 [服務匯流排] 動作中，**完成 [佇列] 中的初始訊息**，提供服務匯流排佇列的名稱，並將所有其他的預設屬性值保留在動作中。

   ![服務匯流排「佇列中的完整初始訊息」的動作詳細資料](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. 在 [服務匯流排] 動作中，**放棄佇列中的初始訊息**、提供服務匯流排佇列的名稱，並將所有其他的預設屬性值保留在動作中。

   ![服務匯流排「放棄來自佇列的初始訊息」的動作詳細資料](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

接下來，您將針對 [**佇列中的完整初始訊息**] 動作，提供所需動作的必要資訊。 **當佇列迴圈中的會話有更多訊息時**，您將從中的動作開始。

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>佇列中的會話會有更多訊息

這個[ **Until**迴圈](../logic-apps/logic-apps-control-flow-loops.md#until-loop)會在訊息存在於佇列中，或直到一小時過後才執行這些動作。 若要變更迴圈的時間限制，請編輯迴圈的**Timeout**屬性值。

* 當訊息存在時，從佇列取得其他訊息。

* 檢查剩餘的訊息數目。 如果訊息仍然存在，請繼續處理訊息。 如果不再有任何訊息，則工作流程會將 `isDone` 變數設定為 `true` ，並結束迴圈。

![直到佇列中的迴圈處理訊息](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. 在 [服務匯流排] 動作中，**從 [會話] 取得其他訊息**，並提供服務匯流排佇列的名稱。 否則，請保留動作中所有其他的預設屬性值。

   > [!NOTE]
   > 根據預設，訊息的最大數目會設定為 `175` ，但此限制會受到服務匯流排中的 [訊息大小] 和 [最大訊息大小] 屬性的影響。 如需詳細資訊，請參閱[佇列的訊息大小](../service-bus-messaging/service-bus-quotas.md)。

   ![服務匯流排動作-「從會話取得其他訊息」](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   接下來，工作流程會分割成下列平行分支：

   * 如果檢查額外訊息時發生錯誤或失敗，請將 `isDone` 變數設定為 `true` 。

   * **如果有任何**條件檢查剩餘訊息的數目是否為零，則處理訊息。 如果有 false 和更多訊息存在，請繼續處理。 如果為 true，且不再有訊息，則工作流程會將 `isDone` 變數設定為 `true` 。

   ![條件-處理訊息（如果有的話）](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   在**If false**區段中， **For each**迴圈會以先進先出順序（FIFO）處理每個訊息。 在迴圈的 [**設定**] 中，[**並行控制**] 設定設為 `1` ，因此一次只會處理一則訊息。

   !["For each" 迴圈-一次處理一個訊息](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. 針對服務匯流排動作，請**完成佇列中的訊息**，並**放棄佇列中的訊息**，並提供服務匯流排佇列的名稱。

   ![服務匯流排動作-「完成佇列中的訊息」和「放棄佇列中的訊息」](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   當**佇列中的會話有更多訊息完成時**，工作流程會將 `isDone` 變數設定為 `true` 。

接下來，您將在「**更新會話鎖定**」中提供動作所需的資訊，直到取消的迴圈為止。

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>更新會話鎖定直到取消

這個[ **Until**迴圈](../logic-apps/logic-apps-control-flow-loops.md#until-loop)可確保會話鎖定是由這個邏輯應用程式持有，而訊息存在於佇列中，或直到一小時後執行這些動作。 若要變更迴圈的時間限制，請編輯迴圈的**Timeout**屬性值。

* 延遲25秒或小於所處理佇列之鎖定超時時間的時間量。 最小的鎖定持續時間為30秒，因此預設值就足夠了。 不過，您可以藉由適當調整來優化迴圈執行的次數。

* 檢查變數是否 `isDone` 設定為 `true` 。

  * 如果未 `isDone` 設定為 `true` ，工作流程仍在處理訊息，因此工作流程會在佇列中更新會話的鎖定，然後再次檢查迴圈條件。

    您必須在 [服務匯流排] 動作中提供服務匯流排佇列的名稱、在[**佇列中更新會話的鎖定**](#renew-lock-on-session)。

  * 如果 `isDone` 設定為 `true` ，則工作流程不會在佇列中更新會話的鎖定，並結束迴圈。

  ![直到迴圈-「在取消前更新會話鎖定」](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>更新佇列中會話的鎖定

此服務匯流排動作會在工作流程仍在處理訊息時，更新佇列中會話的鎖定。

* 在 [服務匯流排] 動作中，**更新佇列中會話的鎖定**，提供服務匯流排佇列的名稱。

  ![服務匯流排動作-「更新佇列中的會話鎖定」](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

接下來，您將提供服務匯流排動作的必要資訊、**關閉佇列中的會話並成功**。

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>關閉佇列中的會話並成功

這個服務匯流排動作會在工作流程完成佇列中所有可用的訊息之後，關閉佇列中的會話，否則工作流程會放棄初始訊息。

* 在 [服務匯流排] 動作中，**關閉佇列中的會話並成功，並**提供服務匯流排佇列的名稱。

  ![服務匯流排動作-「關閉佇列中的會話並成功」](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

下列各節描述區段中的動作 `Catch` ，這會處理工作流程中發生的錯誤和例外狀況。

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>關閉佇列中的會話並失敗

此服務匯流排動作一律會當做範圍中的第一個動作執行 `Catch` ，並關閉佇列中的會話。

* 在 [服務匯流排] 動作中，**關閉佇列中的會話並失敗，並**提供您服務匯流排佇列的名稱。

  ![服務匯流排動作-「關閉佇列中的會話並失敗」](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

接下來，工作流程會建立陣列，其中包含範圍中所有動作的輸入和輸出， `Try` 讓邏輯應用程式可以存取發生的錯誤或失敗的相關資訊。

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>從 ' Try ' 區塊尋找失敗訊息

此[**篩選陣列**動作](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action)會根據指定的準則，使用函式來建立一個陣列，其中包含範圍內所有動作的輸入和輸出 `Try` 。 [ `result()` ](../logic-apps/workflow-definition-language-functions-reference.md#result) 在此情況下，此動作會使用函式和函式來傳回具有狀態之動作的輸出 `Failed` 。 [ `equals()` ](../logic-apps/workflow-definition-language-functions-reference.md#equals) [ `item()` ](../logic-apps/workflow-definition-language-functions-reference.md#item)

![篩選陣列動作-「從 ' Try ' 區塊尋找失敗訊息」](./media/send-related-messages-sequential-convoy/find-failure-message.png)

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

接下來，工作流程會使用 JSON 物件建立陣列，其中包含從動作傳回之陣列中的錯誤資訊 `Find failure msg from 'Try' block` 。

<a name="select-error-details"></a>

### <a name="select-error-details"></a>選取錯誤詳細資料

此[**選取**動作](../logic-apps/logic-apps-perform-data-operations.md#select-action)會根據從上一個動作輸出的輸入陣列，建立包含 JSON 物件的陣列 `Find failure msg from 'Try' block` 。 具體而言，此動作會傳回陣列，其中只有陣列中每個物件的指定屬性。 在此情況下，陣列會包含 [動作名稱] 和 [錯誤結果] 屬性。

![選取動作-[選取錯誤詳細資料]](./media/send-related-messages-sequential-convoy/select-error-details.png)

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

接下來，工作流程會停止邏輯應用程式執行，並傳回執行狀態以及有關錯誤或發生失敗的詳細資訊。

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>終止邏輯應用程式執行

此[**終止**動作](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action)會停止邏輯應用程式執行，並傳回 `Failed` 作為邏輯應用程式執行的狀態，以及會話識別碼和動作的錯誤結果 `Select error details` 。

![終止動作以停止邏輯應用程式執行](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

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

若要測試您的邏輯應用程式，請將訊息傳送至服務匯流排的佇列。 

## <a name="next-steps"></a>後續步驟

* 深入瞭解[服務匯流排連接器的觸發程式和動作](/connectors/servicebus/)
