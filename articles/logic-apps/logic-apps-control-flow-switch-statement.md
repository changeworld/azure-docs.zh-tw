---
title: 將 switch 陳述式新增至工作流程
description: 如何建立 Switch 陳述式，以根據 Azure Logic Apps 中的特定值控制工作流程動作
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 62f147104cf9e0e1605b02a420cb6d20190361b8
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657448"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>建立 Switch 陳述式，以根據 Azure Logic Apps 中的特定值執行工作流程動作

若要根據物件、運算式或權杖的值執行特定動作，請新增 *Switch* 陳述式。 此結構會評估物件、運算式或權杖，然後選擇符合結果的案例，並只針對該案例執行特定動作。 Switch 陳述式執行時，應該只會有一個案例符合結果。

例如，假設您想要讓邏輯應用程式根據電子郵件中所選的選項，採取不同的步驟。 在此範例中，邏輯應用程式會檢查網站的 RSS 摘要是否有新內容。 當 RSS 摘要中出現新項目時，邏輯應用程式會傳送電子郵件給核准者。 根據核准者選取「核准」或「拒絕」，邏輯應用程式會遵循不同的步驟。

> [!TIP]
> 如同所有的程式設計語言，Switch 陳述式僅支援等號比較運算子。 如果您需要其他關係運算子 (例如，「大於」)，請使用[條件陳述式](../logic-apps/logic-apps-control-flow-conditional-statement.md)。
> 若要確認確定性執行行為，案例必須包含唯一且靜態的值，而不是動態的權杖或運算式。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 若要遵循本文中的範例，請使用 Outlook.com 帳戶或工作或學校帳戶 [建立此範例邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md) 。

  1. 當您新增要傳送電子郵件的動作時，請改為尋找並選取下列動作：**傳送核准電子郵件**

     ![選取 [傳送核准電子郵件]](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. 提供必要的欄位，例如核准電子郵件收件者的電子郵件地址。 
  在 [使用者選項] 下，輸入「核准、拒絕」。

     ![輸入電子郵件詳細資料](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>新增 Switch 陳述式

1. 例如，將 Switch 陳述式結尾新增至您的範例工作流程結尾。 在最後一個步驟之後，選擇 [新增步驟]。

   如果您要在步驟之間新增 Switch 陳述式，請將指標移您要新增 Switch 陳述式的箭號上。 選擇顯示的**加號** ( **+** )，然後選擇 [新增動作]。

1. 在搜尋方塊中，輸入 "switch" 作為篩選條件。 選取此動作：**Switch - 控制**

   ![新增 Switch](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Switch 陳述式會顯示一個案例和預設案例。 
   根據預設，Switch 陳述式需要至少一個案例加上預設案例。 

   ![空的預設 Switch 陳述式](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. 在 [依據] 方塊內按一下，動態內容清單隨即顯示。 從該清單中，選取 [SelectedOption] 欄位，其輸出會決定要執行的動作。 

   ![選取 [SelectedOption]](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. 若要處理核准者選取 `Approve` 或 `Reject` 的這類案例，可在 [案例] 和 [預設] 之間新增另一個案例。 

   ![新增另一個案例](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. 將這些動作新增至對應的案例：

   | 案例 | **SelectedOption** | 動作 |
   |--------|--------------------|--------|
   | 案例 1 | **核准** | 新增 Outlook **傳送電子郵件**動作，只在核准者選取**核准**時傳送 RSS 項目的詳細資料。 |
   | 案例 2 | **拒絕** | 新增 Outlook **傳送電子郵件**動作，通知其他核准者 RSS 項目遭到拒絕。 |
   | 預設 | None | 不需採取任何動作。 在此範例中，[預設] 案例為空白，因為 **SelectedOption** 只有兩個選項。 |
   |||

   ![已完成 Switch 陳述式](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. 儲存您的邏輯應用程式。 

   若要手動測試此範例，請選擇 [執行]，直到邏輯應用程式找到新 RSS 項目並傳送核准電子郵件為止。 
   選取 [核准] 來觀察結果。

## <a name="json-definition"></a>JSON 定義

現在，您已使用 Switch 陳述式建立邏輯應用程式，讓我們看看 switch 陳述式後面的程式碼定義概觀。

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {}
         },
         "case" : "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| 標籤 | 描述 |
|-------|-------------|
| `"Switch"`         | Switch 陳述式的名稱，您可以將它重新命名以增加可讀性 |
| `"type": "Switch"` | 指出動作是 Switch 陳述式 |
| `"expression"`     | 在此範例中用來指定核准者選取的選項，並且會針對稍後在定義中宣告的每個案例進行評估 |
| `"cases"` | 定義任意數目的案例。 針對每個案例，`"Case_*"` 是該案例的預設名稱，您可以將它重新命名以增加可讀性 |
| `"case"` | 指定案例的值，必須為常數且唯一的值，Switch 運算式會比對此值。 如果沒有案例符合 Switch 運算式的結果，則會執行 `"default"` 中的動作。 | 
| | | 

## <a name="get-support"></a>取得支援

* 如有任何問題，請瀏覽 [Azure Logic Apps 的 Microsoft 問與答頁面](/answers/topics/azure-logic-apps.html)。
* 若要提交或票選功能與建議，請造訪 [Azure Logic Apps 使用者意見反應網站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

* [根據條件 (條件陳述式) 執行步驟](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [執行並重複步驟 (迴圈)](../logic-apps/logic-apps-control-flow-loops.md)
* [執行或合併平行步驟 (分支)](../logic-apps/logic-apps-control-flow-branches.md)
* [根據分組的動作狀態執行步驟 (範圍)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
