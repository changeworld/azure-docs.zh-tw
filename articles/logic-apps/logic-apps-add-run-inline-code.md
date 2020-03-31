---
title: 使用內聯代碼添加和運行程式碼片段
description: 瞭解如何使用內聯代碼操作創建和運行程式碼片段，以便使用 Azure 邏輯應用創建的自動任務和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: f7a134fd026b42d1666b8310b3fb0c10642c7bb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453502"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>在 Azure 邏輯應用中使用內聯代碼添加和運行程式碼片段

如果要在邏輯應用中運行一段代碼，可以將內置**的內聯代碼**操作添加為邏輯應用工作流中的一個步驟。 當您要運行適合此方案的代碼時，此操作效果最佳：

* 在 JavaScript 中運行。 更多語言即將推出。
* 在五秒或五秒內完成運行。
* 處理大小高達 50 MB 的資料。
* 不需要使用[**尚未支援的變數**操作](../logic-apps/logic-apps-create-variables-store-values.md)。
* 使用 Node.js 版本 8.11.1。 有關詳細資訊，請參閱[標準內置物件](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)。 

  > [!NOTE]
  > 用於`require()`運行 JavaScript 的**內聯代碼**操作不支援該函數。

此操作運行程式碼片段，並將該程式碼片段的輸出作為名為**Result**的權杖返回，您可以在邏輯應用中的後續操作中使用該權杖。 對於要為代碼創建函數的其他方案，請嘗試在邏輯應用中[創建和調用 Azure 函數](../logic-apps/logic-apps-azure-functions.md)。

在本文中，當新電子郵件到達 Office 365 Outlook 帳戶時，示例邏輯應用將觸發。 程式碼片段提取並返回出現在電子郵件正文中的任何電子郵件地址。

![示例概述](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 要添加程式碼片段（包括觸發器）的邏輯應用。 如果沒有邏輯應用，請參閱[快速入門：創建第一個邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

   本主題中的示例邏輯應用使用此 Office 365 Outlook 觸發器：**當新電子郵件到達時**

* 連結到邏輯應用的[集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

  > [!NOTE]
  > 請確保使用適合您的用例或方案的集成帳戶。 例如，[自由層](../logic-apps/logic-apps-pricing.md#integration-accounts)集成帳戶僅用於探索性方案和工作負載，而不是生產方案，在使用和輸送量方面受到限制，並且不受服務等級協定 （SLA） 的支援。 其他層會產生成本，但包括 SLA 支援、提供更多輸送量且具有更高的限制。 詳細瞭解集成帳戶[層](../logic-apps/logic-apps-pricing.md#integration-accounts)、[定價](https://azure.microsoft.com/pricing/details/logic-apps/)和[限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。

## <a name="add-inline-code"></a>添加內聯代碼

1. 如果尚未在[Azure 門戶](https://portal.azure.com)中，請在邏輯應用設計器中打開邏輯應用。

1. 在設計器中，在邏輯應用工作流中所需的位置添加**內聯代碼**操作。

   * 要在工作流末尾添加操作，請選擇 **"新建步驟**"。

   * 要在現有步驟之間添加操作，請將滑鼠指標移到連接這些步驟的箭頭上。 選擇加號 （），**+** 然後選擇 **"添加操作**"。

   本示例在 Office 365 Outlook 觸發器下添加**內聯代碼**操作。

   ![添加新步驟](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. 在搜索框中**選擇操作**，在搜索框中輸入"內聯代碼"作為篩選器。 從動作清單中選擇此操作：**執行 JavaScript 代碼**

   ![選擇"執行 JavaScript 代碼"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   該操作將顯示在設計器中，並包含一些預設示例代碼，包括返回語句。

   ![帶有預設示例代碼的內聯代碼操作](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. 在 **"代碼"** 框中，刪除示例代碼，然後輸入要運行的代碼。 編寫放入方法的代碼，但不定義方法簽名。 

   鍵入識別關鍵字時，將顯示自動完成清單，以便您可以從可用關鍵字中選擇，例如：

   ![關鍵字自動完成清單](./media/logic-apps-add-run-inline-code/auto-complete.png)

   此示例程式碼片段首先創建一個變數，該變數存儲*正則運算式*，該變數指定要在輸入文本中匹配的模式。 然後，代碼創建一個變數，用於存儲觸發器中的電子郵件正文資料。

   ![建立變數](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   為了使觸發器和以前操作的結果更易於引用，動態內容清單會在游標位於 **"代碼"** 框中時顯示。 在此示例中，清單顯示觸發器的可用結果，包括 **"正文**"權杖，您現在可以選擇該結果。

   選擇**Body**權杖後，內聯代碼操作將權杖解析為引用電子郵件`workflowContext``Body`屬性值的物件：

   ![選擇結果](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   在 **"代碼"** 框中，程式碼片段可以使用只`workflowContext`讀物件作為輸入。 此物件具有子屬性，使代碼可以訪問工作流中觸發器和以前操作的結果。
   有關詳細資訊，請參閱本主題後面的本節：[代碼中的引用觸發器和操作結果](#workflowcontext)。

   > [!NOTE]
   >
   > 如果程式碼片段引用使用 dot （.） 運算子的操作名稱，則必須將這些操作名稱添加到[**操作**參數](#add-parameters)。 這些引用還必須將操作名稱與方括弧 （*） 和引號括在一起，例如：
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   內聯代碼操作不需要語句`return`，但`return`語句的結果可在以後的操作中通過**Result**權杖進行參考。 
   例如，程式碼片段通過調用`match()`函數返回結果，該函數在電子郵件正文中找到與正則運算式的匹配項。 **"組合"** 操作使用**結果**權杖引用內聯代碼操作的結果並創建單個結果。

   ![完成的邏輯應用程式](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. 完成後，儲存邏輯應用程式。

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>引用觸發器和操作會導致代碼

該`workflowContext`物件具有此結構，其中包括`actions`、`trigger`和`workflow`子屬性：

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

此表包含有關這些子屬性的詳細資訊：

| 屬性 | 類型 | 描述 |
|----------|------|-------|
| `actions` | 物件集合 | 從程式碼片段運行之前運行的操作中生成物件。 每個物件都有一個*鍵值*對，其中鍵是操作的名稱，該值等效于使用`@actions('<action-name>')`調用[操作（）函數](../logic-apps/workflow-definition-language-functions-reference.md#actions)。 操作的名稱使用的基礎工作流定義中使用的操作名稱，該名稱將操作名稱中的空格（""）替換為底線 （*）。 此物件提供對當前工作流實例運行中的操作屬性值的訪問。 |
| `trigger` | Object | 觸發器的結果物件，等效于調用[觸發器（） 函數](../logic-apps/workflow-definition-language-functions-reference.md#trigger)。 此物件提供從當前工作流實例運行中觸發屬性值的訪問。 |
| `workflow` | Object | 工作流物件，等效于調用[工作流（） 函數](../logic-apps/workflow-definition-language-functions-reference.md#workflow)。 此物件提供從當前工作流實例運行對工作流屬性值（如工作流名稱、運行 ID 等）的訪問。 |
|||

在本主題的示例中，`workflowContext`物件具有代碼可以訪問的這些屬性：

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

在某些情況下，您可能需要顯式要求**內聯代碼**操作包括觸發器的結果或代碼引用為依賴項的特定操作的結果，通過添加**觸發器**或**操作**參數。 此選項對於在運行時找不到引用結果的方案很有用。

> [!TIP]
> 如果計畫重用代碼，請使用**Code**框添加對屬性的引用，以便代碼包含解析的權杖引用，而不是將觸發器或操作添加為顯式依賴項。

例如，假設您有代碼引用 Office 365 Outlook 連接器的 **"發送核准電子郵件**操作"中的 **"選定選項**"結果。 在創建時，邏輯應用引擎會分析代碼，以確定您是否引用了任何觸發器或操作結果，並自動包含這些結果。 在運行時，如果收到引用的觸發器或操作結果在指定`workflowContext`物件中不可用的錯誤，則可以將該觸發器或操作添加為顯式依賴項。 在此示例中，添加 **"操作"** 參數，並指定**內聯代碼**操作顯式包含 **"發送核准電子郵件**"操作的結果。

要添加這些參數，請打開 **"添加新參數**清單"，然後選擇所需的參數：

   ![加入參數](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | 參數 | 描述 |
   |-----------|-------------|
   | **動作** | 包括以前操作的結果。 請參閱[包括操作結果](#action-results)。 |
   | **觸發** | 包括觸發器的結果。 請參閱[包括觸發器結果](#trigger-results)。 |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>包括觸發器結果

如果選擇**觸發器**，系統將提示您是否包括觸發器結果。

* 在 **"觸發"** 清單中，選擇 **"是**"。

<a name="action-results"></a>

### <a name="include-action-results"></a>包括操作結果

如果選擇 **"操作"，** 系統將提示您要添加的操作。 但是，在開始添加操作之前，您需要邏輯應用的基礎工作流定義中顯示的操作名稱的版本。

* 此功能不支援變數、迴圈和反覆運算索引。

* 邏輯應用工作流定義中的名稱使用底線 （*），而不是空格。

* 對於使用點運算子 （.） 的操作名稱，包括這些運算子，例如：

  `My.Action.Name`

1. 在設計器工具列上，選擇 **"代碼"視圖**，並在屬性`actions`內搜索操作名稱。

   例如，`Send_approval_email_`是 **"發送核准電子郵件**操作"的 JSON 名稱。

   ![在 JSON 中查找操作名稱](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. 要返回到設計器視圖，在代碼視圖工具列上，請選擇 **"設計器**"。

1. 要添加第一個操作，請在 **"操作項 - 1"** 框中輸入操作的 JSON 名稱。

   ![輸入第一個操作](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. 要添加其他操作，請選擇"**添加新項**"。

## <a name="reference"></a>參考資料

有關使用工作流定義語言在邏輯應用的基礎工作流定義中**執行 JavaScript 代碼**操作的結構和語法的詳細資訊，請參閱此操作的[參考部分](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)。

## <a name="next-steps"></a>後續步驟

瞭解有關[Azure 邏輯應用連接器](../connectors/apis-list.md)的更多資訊
