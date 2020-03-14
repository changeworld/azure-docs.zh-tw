---
title: 新增迴圈以重複動作
description: 在 Azure Logic Apps 中建立會重複工作流程動作或處理陣列的迴圈
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: 5f6c04c9a57dc8c250d99f2fa944203d2d73c404
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270572"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>在 Azure Logic Apps 中建立會重複工作流程動作或處理陣列的迴圈

若要處理邏輯應用程式中的陣列，您可以建立 ["Foreach" 迴圈](#foreach-loop)。 這個迴圈會對陣列中的每個項目重複執行一或多個動作。 如需 "Foreach" 迴圈可處理的陣列項目數上限，請參閱[限制和設定](../logic-apps/logic-apps-limits-and-config.md)。 

若要重複執行動作直到條件符合或狀態改變，您可以建立 ["Until" 迴圈](#until-loop)。 您的邏輯應用程式會先執行迴圈內的所有動作，然後再檢查條件或狀態。 如果符合條件，則迴圈會停止。 否則，迴圈會重複。 如需一個邏輯應用程式回合中可擁有的 "Until" 迴圈數上限，請參閱[限制和設定](../logic-apps/logic-apps-limits-and-config.md)。 

> [!TIP]
> 如果您的觸發程序接收到陣列，並想要針對每個陣列項目執行工作流程，您可以使用SplitOn[ 觸發屬性**將該陣列「解除批次」** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)。 

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" 迴圈

"Foreach" 迴圈會對每個陣列項目重複執行一或多個動作，且只能在陣列上運作。 "Foreach" 迴圈中的反覆項目會平行執行。 不過，您可以設定[循序的 "Foreach" 迴圈](#sequential-foreach-loop)，一次執行一個反覆項目。 

以下是使用 "Foreach" 迴圈時的一些考量：

* 在巢狀迴圈中，反覆項目一律會循序執行，而不會平行執行。 若要針對巢狀迴圈中的項目平行執行作業，請建立和[呼叫子系邏輯應用程式](../logic-apps/logic-apps-http-endpoint.md)。

* 若要在每個迴圈反覆項目的執行期間，從針對變數所執行的作業獲得可預測的結果，請循序執行這些迴圈。 例如，當並行執行的迴圈結束時，遞增、遞減和附加至變數的作業會傳回可預測的結果。 不過，在並行執行迴圈中的每個反覆項目執行期間，這些作業可能會傳回無法預測的結果。 

* "Foreach" 迴圈中的動作會使用[`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
要參考並處理陣列中每個專案的運算式。 如果您指定的資料不是在陣列中，則邏輯應用程式工作流程將會失敗。 

此範例邏輯應用程式會傳送網站 RSS 摘要的每日摘要。 此應用程式會使用 "Foreach" 迴圈，針對每個新項目傳送電子郵件。

1. 使用 Outlook.com 或 Office 365 Outlook 帳戶來[建立此範例邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

2. 在 RSS 觸發程序和傳送電子郵件動作之間，新增 "Foreach" 迴圈。 

   1. 若要在步驟之間新增迴圈，請將滑鼠指標移至這些步驟之間的箭號上。 
   選擇所顯示的**加號** ( **+** )，然後選取 [新增動作]。

      ![選取 [新增動作]](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. 在搜尋方塊下方，選擇 [全部]。 在搜尋方塊中，輸入 "for each" 作為篩選條件。 從 [動作] 清單中，選取此動作： **For Each 控制項**

      ![新增 "For each" 迴圈](media/logic-apps-control-flow-loops/select-for-each.png)

3. 現在建置迴圈。 當**新增動態內容**清單出現後，在 [從上一個步驟中選取輸出] 下，選取 [摘要連結] 陣列，這是來自 RSS 觸發程序的輸出。 

   ![從動態內容清單選取](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > 您「只」可以選取上一個步驟中的陣列輸出。

   選取的陣列現在會出現在這裡：

   ![選取陣列](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. 若要對每個陣列項目執行動作，請將 [傳送電子郵件] 動作拖曳至迴圈。 

   您的邏輯應用程式看起來可能就像下面這個範例︰

   ![將步驟新增至 "Foreach" 迴圈](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. 儲存您的邏輯應用程式。 若要手動測試邏輯應用程式，在設計工具的工具列上，選擇 [執行]。

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach" 迴圈定義 (JSON)

如果您是在邏輯應用程式的程式碼檢視中進行作業，您可以改為以邏輯應用程式的 JSON 定義來定義 `Foreach` 迴圈，例如：

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach" 迴圈：循序

根據預設，"Foreach" 迴圈中的循環會平行執行。 若要以循序方式執行每個循環，請設定迴圈的 [循序] 選項。 如果您在預期要有可預測結果的迴圈內部具有巢狀迴圈或變數，"Foreach" 迴圈就必須循序執行。 

1. 在迴圈的右上角，選擇**省略符號** ( **...** ) > [設定]。

   ![在 "Foreach" 迴圈上，選擇 [...] > [設定]](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. 在 [並行控制] 底下，將 [並行控制] 設定改為 [開啟]。 將 [平行處理原則的程度] 滑桿移至 **1**，然後選擇 [完成]。

   ![開啟並行控制](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

如果您要處理邏輯應用程式的 JSON 定義，您可以藉由新增 `Sequential` 參數來使用 `operationOptions` 選項，例如：

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Until" 迴圈
  
若要執行並重複動作直到符合條件或狀態變更，請將這些動作放在 "Until" 迴圈中。 您的邏輯應用程式會先在迴圈內執行任何和所有動作，然後再檢查條件或狀態。 如果符合條件，則迴圈會停止。 否則，迴圈會重複。

以下是可以使用 "Until" 迴圈的一些常見案例：

* 呼叫端點，直到您獲得想要的回應。

* 在資料庫中建立記錄。 等到該記錄中的特定欄位獲得核准。 繼續處理。 

從每天上午 8:00 開始，此範例邏輯應用程式會遞增變數，直到變數的值等於 10。 接著，此邏輯應用程式會傳送用來確認目前值的電子郵件。 

> [!NOTE]
> 這些步驟會使用 Office 365 Outlook 來進行，但您也可以使用 Logic Apps 支援的任何電子郵件提供者。 
> [請參考這裡的連接器清單](https://docs.microsoft.com/connectors/)。 如果您使用其他電子郵件帳戶，整體步驟將維持不變，但您的 UI 外觀可能會略有不同。 

1. 建立空白邏輯應用程式。 在邏輯應用程式設計工具中的搜尋方塊底下，選擇 [全部]。 搜尋「週期」。 
   從觸發程式清單中，選取此觸發**程式：週期-排程**

   ![新增「週期 - 排程」觸發程序](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. 透過設定時間間隔、頻率和一天的小時來指定觸發程序引發的時間。 若要設定小時，請選擇 [顯示進階選項]。

   ![設定週期排程](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | 屬性 | 值 |
   | -------- | ----- |
   | **間隔** | 1 | 
   | **頻率** | Day |
   | **在這幾小時內** | 8 |
   ||| 

1. 在觸發程序下方，選擇 [新增步驟]。 
   搜尋「變數」，然後選取此動作：**初始化變數**變數

   ![新增「初始化變數 - 變數」動作](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. 使用下列值來設定您的變數：

   ![設定變數屬性](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | 屬性 | 值 | 描述 |
   | -------- | ----- | ----------- |
   | **名稱** | 限制 | 變數的名稱 | 
   | **型別** | 整數 | 變數的資料類型 | 
   | **ReplTest1** | 0 | 變數的起始值 | 
   |||| 

1. 在 [初始化變數] 動作下，選擇 [新增步驟]。 

1. 在搜尋方塊下方，選擇 [全部]。 搜尋 "until"，然後選取此動作： **until-Control**

   ![新增 "Until" 迴圈](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. 選取**限制**變數和**等於**運算子，可建置迴圈的結束條件。 
   輸入 **10** 作為比較值。

   ![建置停止迴圈的結束條件](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. 在迴圈中，選擇 [新增動作]。 

1. 在搜尋方塊下方，選擇 [全部]。 搜尋「變數」，然後選取此動作：**遞增變數-變數**

   ![新增遞增變數的動作](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. 針對 [名稱]，選取**限制**變數。 針對 [值]，輸入 "1"。 

     ![以 1 遞增 [限制]](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. 在迴圈外部底下，選擇 [新增步驟]。 

1. 在搜尋方塊下方，選擇 [全部]。 
     尋找並新增會傳送電子郵件的動作，例如： 

     ![新增傳送電子郵件的動作](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. 如果出現提示，請登入您的電子郵件帳戶。

1. 設定電子郵件動作的屬性。 在主旨上新增**限制**變數。 這樣一來，您可以確認變數的目前值符合您指定的條件，例如：

      ![設定電子郵件內容](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | 屬性 | 值 | 描述 |
      | -------- | ----- | ----------- | 
      | **若要** | *\<電子郵件地址\@網域 >* | 收件者的電子郵件地址。 若要進行測試，請使用自己的電子郵件地址。 | 
      | **主旨** | [限制] 目前的值是**限制** | 指定電子郵件主旨。 在此範例中，請確定您已包含**限制**變數。 | 
      | **本文** | <*email-content*> | 指定您想要傳送的電子郵件訊息內容。 針對此範例，輸入任何您喜歡的文字。 | 
      |||| 

1. 儲存您的邏輯應用程式。 若要手動測試邏輯應用程式，在設計工具的工具列上，選擇 [執行]。

      當您的邏輯開始執行之後，您會收到電子郵件及您指定的內容：

      ![收到的電子郵件](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>避免無止盡的迴圈

"Until" 迴圈具有預設的限制，就是達到下列任何條件時，便會停止執行：

| 屬性 | 預設值 | 描述 | 
| -------- | ------------- | ----------- | 
| **Count** | 60 | 迴圈結束前可執行的最高迴圈數。 預設為 60 個循環。 | 
| **逾時** | PT1H | 迴圈結束前可執行迴圈的最大時間。 預設值是一小時，並以 ISO 8601 格式指定。 <p>逾時值的評估會以每個迴圈循環為基礎。 如果迴圈中有任何動作所花費的時間超過逾時限制，目前的循環並不會停止。 但不會啟動下一個循環，因為不符合限制條件。 | 
|||| 

若要變更這些預設限制，請選擇迴圈動作圖形中的 [顯示進階選項]。

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Until" 定義 (JSON)

如果您是在邏輯應用程式的程式碼檢視中進行作業，您可以改為以邏輯應用程式的 JSON 定義來定義 `Until` 迴圈，例如：

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

此範例 "Until" 迴圈會呼叫 HTTP 端點，這會建立資源。 當 HTTP 回應本文傳回且狀態為 `Completed` 時，迴圈就會停止。 若要避免無止盡迴圈，迴圈也會在達到下列任一條件時停止：

* 迴圈已依 `count` 屬性所指定的值執行了 10 次。 預設值為 60 次。 

* 迴圈已依 `timeout` 屬性所指定的值 (ISO 8601 格式) 執行了兩個小時。 預設值是一小時。
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交或票選功能與建議，請造訪 [Azure Logic Apps 使用者意見反應網站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

* [根據條件 (條件陳述式) 執行步驟](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [根據不同的值 (Switch 陳述式) 執行步驟](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [執行或合併平行步驟 (分支)](../logic-apps/logic-apps-control-flow-branches.md)
* [根據分組的動作狀態執行步驟 (範圍)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
