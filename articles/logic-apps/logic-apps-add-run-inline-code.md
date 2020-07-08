---
title: 使用內嵌程式碼新增和執行程式碼片段
description: 瞭解如何使用內嵌程式碼動作來建立和執行程式碼片段，以用於您使用建立的自動化工作和工作流程 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: f7a134fd026b42d1666b8310b3fb0c10642c7bb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75453502"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>在 Azure Logic Apps 中使用內嵌程式碼來新增和執行程式碼片段

當您想要在邏輯應用程式內部執行一段程式碼時，您可以在邏輯應用程式的工作流程中新增內建的**內嵌程式碼**動作作為步驟。 當您想要執行符合此案例的程式碼時，此動作的效果最佳：

* 以 JavaScript 執行。 即將推出更多語言。
* 在5秒或更少的時間內完成執行。
* 處理大小上限為 50 MB 的資料。
* 不需要使用尚未支援的[**變數**動作](../logic-apps/logic-apps-create-variables-store-values.md)。
* 使用 Node.js 版本建議使用8.11.1。 如需詳細資訊，請參閱[標準的內建物件](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)。 

  > [!NOTE]
  > `require()`執行 JavaScript 的**內嵌程式碼**動作不支援函數。

此動作會執行程式碼片段，並將該片段的輸出當做名為**Result**的 token 傳回，以供您在邏輯應用程式的後續動作中使用。 針對您想要為程式碼建立函式的其他案例，請嘗試在您的邏輯應用程式中[建立和呼叫 Azure 函數](../logic-apps/logic-apps-azure-functions.md)。

在本文中，範例邏輯應用程式會在新的電子郵件送達 Office 365 Outlook 帳戶時觸發。 此程式碼片段會解壓縮並傳回出現在電子郵件內文中的任何電子郵件地址。

![範例總覽](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您想要在其中新增程式碼片段的邏輯應用程式，包括觸發程式。 如果沒有邏輯應用程式，請參閱[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

   本主題中的範例邏輯應用程式會使用此 Office 365 Outlook 觸發程式：**當新的電子郵件送達時**

* 連結至邏輯應用程式的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

  > [!NOTE]
  > 請確定您使用的是適用于您的使用案例或案例的整合帳戶。 例如，[免費層](../logic-apps/logic-apps-pricing.md#integration-accounts)整合帳戶僅適用于探索案例和工作負載，而非生產案例僅限於使用量和輸送量，而且不受服務等級協定（SLA）的支援。 其他層則會產生成本，但包含 SLA 支援、提供更多的輸送量，而且有更高的限制。 深入瞭解整合帳戶[層](../logic-apps/logic-apps-pricing.md#integration-accounts)、[定價](https://azure.microsoft.com/pricing/details/logic-apps/)和[限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。

## <a name="add-inline-code"></a>加入內嵌程式碼

1. 如果您還沒有這麼做，請在 [ [Azure 入口網站](https://portal.azure.com)中，于邏輯應用程式設計工具中開啟邏輯應用程式。

1. 在設計工具中，于您想要的邏輯應用程式工作流程中的位置新增**內嵌程式碼**動作。

   * 若要在工作流程結尾處新增動作，請選擇 [**新增步驟**]。

   * 若要在現有步驟之間新增動作，請將滑鼠指標移到連接這些步驟的箭號上。 選擇加號（ **+** ），然後選取 [**新增動作**]。

   這個範例會在 Office 365 Outlook 觸發程式下加入**內嵌程式碼**動作。

   ![新增步驟](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. 在 [**選擇動作**] 下的 [搜尋] 方塊中，輸入「內嵌程式碼」作為篩選準則。 從 [動作] 清單中，選取此動作：**執行 JavaScript 程式碼**

   ![選取 [執行 JavaScript 程式碼]](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   此動作會出現在設計工具中，並包含一些預設的範例程式碼，包括 return 語句。

   ![具有預設範例程式碼的內嵌程式碼動作](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. 在 [程式**代碼**] 方塊中，刪除範例程式碼，然後輸入您想要執行的代碼。 撰寫您放在方法中的程式碼，但不定義方法簽章。 

   當您輸入可辨識的關鍵字時，自動完成清單會隨即出現，讓您可以從可用的關鍵字中選取，例如：

   ![關鍵字自動完成清單](./media/logic-apps-add-run-inline-code/auto-complete.png)

   這個範例程式碼片段會先建立一個變數來儲存*正則運算式*，以指定要在輸入文字中比對的模式。 然後，程式碼會建立一個變數，以儲存來自觸發程式的電子郵件內文資料。

   ![建立變數](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   若要讓觸發程式的結果和先前的動作更容易參考，動態內容清單會在游標位於 [**代碼**] 方塊內時出現。 在此範例中，清單會顯示來自觸發程式的可用結果，包括**主體**權杖，您現在可以選取此功能。

   當您**選取 [內**文] token 之後，內嵌程式碼動作會將 token 解析成 `workflowContext` 參考該電子郵件 `Body` 屬性值的物件：

   ![選取結果](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   在 [程式**代碼**] 方塊中，您的程式碼片段可以使用唯讀 `workflowContext` 物件做為輸入。 這個物件具有子屬性，可讓您的程式碼存取來自觸發程式的結果，以及工作流程中的先前動作。
   如需詳細資訊，請參閱本主題稍後的此節：[在您的程式碼中參考觸發程式和動作結果](#workflowcontext)。

   > [!NOTE]
   >
   > 如果您的程式碼片段參考使用點（.）運算子的動作名稱，則必須將這些動作名稱新增至[**動作**參數](#add-parameters)。 這些參考也必須用方括弧（[]）和引號括住動作名稱，例如：
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   內嵌程式碼動作不需要 `return` 語句，但是 `return` 可以透過**結果**token，在後續動作中參考語句的結果。 
   例如，程式碼片段會藉由呼叫函式來傳回結果，該函式會 `match()` 在電子郵件內文中尋找符合正則運算式的相符專案。 **撰寫**動作會使用**結果**token 來參考內嵌程式碼動作的結果，並建立單一結果。

   ![完成的邏輯應用程式](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. 完成後，儲存邏輯應用程式。

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>程式碼中的參考觸發程式和動作結果

`workflowContext`物件具有這個結構，其中包括 `actions` 、和子 `trigger` `workflow` 屬性：

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

此資料表包含這些子屬性的詳細資訊：

| 屬性 | 類型 | Description |
|----------|------|-------|
| `actions` | 物件集合 | 在您的程式碼片段執行之前執行之動作的結果物件。 每個物件都有索引*鍵/值*組，其中索引鍵是動作的名稱，而值相當於呼叫[actions （）](../logic-apps/workflow-definition-language-functions-reference.md#actions)函式 `@actions('<action-name>')` 。 動作的名稱會使用在基礎工作流程定義中使用的相同動作名稱，這會以底線（_）取代動作名稱中的空格（""）。 這個物件可讓您從目前的工作流程實例執行存取動作屬性值。 |
| `trigger` | Object | 來自觸發程式的結果物件，相當於呼叫[trigger （）函數](../logic-apps/workflow-definition-language-functions-reference.md#trigger)。 這個物件可讓您從目前的工作流程實例執行，存取觸發程式的屬性值。 |
| `workflow` | Object | 工作流程物件，相當於呼叫[workflow （）函數](../logic-apps/workflow-definition-language-functions-reference.md#workflow)。 這個物件可讓您從目前的工作流程實例執行，存取工作流程屬性值，例如工作流程名稱、執行識別碼等等。 |
|||

在本主題的範例中， `workflowContext` 物件具有您的程式碼可以存取的下列屬性：

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>加入參數

在某些情況下，您可能必須明確要求內嵌程式**代碼**動作包含觸發程式的結果，或您的程式碼藉由新增**trigger**或**actions**參數來參考為相依性的特定動作。 此選項適用于在執行時間找不到參考結果的案例。

> [!TIP]
> 如果您打算重複使用您的程式碼，請使用 [程式**代碼**] 方塊加入屬性的參考，讓您的程式碼包含已解析的 token 參考，而不是將觸發程式或動作新增為明確相依性。

例如，假設您的程式碼會從 Office 365 Outlook 連接器的 [**傳送核准電子郵件**] 動作中參考**SelectedOption**結果。 在建立期間，Logic Apps 引擎會分析您的程式碼，以判斷您是否已參考任何觸發程式或動作結果，並自動包含這些結果。 在執行時間，如果您在指定的物件中無法取得參考的觸發程式或動作結果 `workflowContext` ，您可以將該觸發程式或動作當做明確的相依性來加入。 在此範例中，您會新增 [**動作**] 參數，並指定**內嵌程式碼**動作明確包含 [**傳送核准電子郵件**] 動作的結果。

若要新增這些參數，請開啟 [**加入新的參數**] 清單，然後選取您想要的參數：

   ![加入參數](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | 參數 | 描述 |
   |-----------|-------------|
   | **動作** | 包含先前動作的結果。 請參閱[包含動作結果](#action-results)。 |
   | **觸發程序** | 包含來自觸發程式的結果。 請參閱[包含觸發程式結果](#trigger-results)。 |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>包含觸發程式結果

如果您選取 [**觸發**程式]，系統會提示您是否要包含觸發程式結果。

* 從**觸發**程式清單中，選取 **[是]**。

<a name="action-results"></a>

### <a name="include-action-results"></a>包含動作結果

如果您選取 [**動作**]，系統就會提示您輸入要新增的動作。 不過，在您開始新增動作之前，您需要出現在邏輯應用程式基礎工作流程定義中的動作名稱版本。

* 這項功能不支援變數、迴圈和反復專案索引。

* 邏輯應用程式的工作流程定義中的名稱使用底線（_），而不是空格。

* 如需使用點運算子（.）的動作名稱，請包含這些運算子，例如：

  `My.Action.Name`

1. 在設計工具工具列上，選擇 [程式**代碼視圖**]，然後在屬性內搜尋 `actions` 動作名稱。

   例如， `Send_approval_email_` 是**傳送核准電子郵件**動作的 JSON 名稱。

   ![尋找 JSON 中的動作名稱](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. 若要返回設計師視圖，請在程式碼視圖工具列上，選擇 [**設計**工具]。

1. 若要新增第一個動作，請在 [**動作專案-1** ] 方塊中，輸入動作的 JSON 名稱。

   ![輸入第一個動作](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. 若要新增另一個動作，請選擇 [**加入新專案**]。

## <a name="reference"></a>參考

如需使用工作流程定義語言的邏輯應用程式基礎工作流程定義中，**執行 JavaScript 程式碼**動作結構和語法的詳細資訊，請參閱此動作的[參考一節](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)。

## <a name="next-steps"></a>後續步驟

深入瞭解[Azure Logic Apps 的連接器](../connectors/apis-list.md)
