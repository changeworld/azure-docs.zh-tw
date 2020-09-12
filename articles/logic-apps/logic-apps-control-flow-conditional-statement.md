---
title: 將條件陳述式加入至工作流程
description: 如何建立條件以控制 Azure Logic Apps 工作流程中的動作
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 19f42270e37c42cf56bbde04f73c01027a56ff63
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657395"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>建立條件陳述式以控制 Azure Logic Apps 中的工作流程動作

若只要在傳遞指定條件後在邏輯應用程式中執行特定動作，請新增*條件陳述式*。 此控制結構會將您工作流程中的資料與特定值或欄位進行比較。 接著，您可以指定要根據資料是否符合條件來執行的各種不同動作。 您可以讓條件在彼此之中巢狀化。

例如，假設在網站的 RSS 摘要上出現新項目時，您的邏輯應用程式傳送過多電子郵件給您。 您可以新增條件陳述式，讓應用程式只在新項目包含特定字串時，才傳送電子郵件。 

> [!TIP]
> 若要根據不同的特定值執行不同步驟，可改為使用 [*Switch 陳述式*](../logic-apps/logic-apps-control-flow-switch-statement.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 若要遵循本文中的範例，請使用 Outlook.com 帳戶或工作或學校帳戶 [建立此範例邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md) 。

## <a name="add-condition"></a>新增條件

1. 在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在您想要的位置上新增條件。 

   若要在步驟之間新增條件，請將指標移至您要新增條件的箭號上。 選擇顯示的**加號** ( **+** )，然後選擇 [新增動作]。 例如：

   ![在步驟間新增動作](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   當您想要在工作流程結尾新增條件時，請在邏輯應用程式最下方，選擇 [新增步驟] > [新增動作]。

1. 在搜尋方塊中，輸入「條件」作為篩選條件。 選取此動作：**條件 - 控制項**

   ![新增條件](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. 在 [條件] 方塊中，建置您的條件。 

   1. 在左側的方塊中，指定您要比較的資料或欄位。

      當您按一下左側方塊內部時，動態內容清單隨即出現，以便您在邏輯應用程式中選取先前步驟的輸出。 
      在此範例中，選取 RSS 摘要的摘要。

      ![建立您的條件](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. 在中間的方塊中，選取要執行的作業。 
   在此範例中，請選取 "**contains**"。 

   1. 在右邊的方塊中，指定作為準則的值或欄位。 
   在此範例中，指定下列字串：**Microsoft**

   以下是完整的條件：

   ![完整條件](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   若要為您的條件新增另一個資料列，請選擇 [新增] > [新增資料列]。 
   若要新增含有子條件的群組，請選擇 [新增] > [新增群組]。 
   若要將現有的資料列組成群組，請選取那些資料列的核取方塊、選擇任一資料列的省略符號 (...)，然後選擇 [將群組設為]。

1. 在 **If true** 和 **If false** 之下，以是否符合條件為基礎，新增要執行的步驟。 例如：

   ![具有 "If true" 和 "If false" 路徑的條件](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > 您可以將現有動作拖曳到 **If true** 和 **If false** 路徑。

1. 儲存您的邏輯應用程式。

現在，此邏輯應用程式只有在 RSS 摘要中的新項目符合您的條件時，才會傳送郵件。

## <a name="json-definition"></a>JSON 定義

以下是一個條件陳述式背後的概要程式碼定義：

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", 
            "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>取得支援

* 如有任何問題，請瀏覽 [Microsoft 問答集的 Azure Logic Apps 問題頁面](/answers/topics/azure-logic-apps.html) \(英文\)。
* 若要提交或票選功能與建議，請造訪 [Azure Logic Apps 使用者意見反應網站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

* [根據不同的值 (Switch 陳述式) 執行步驟](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [執行並重複步驟 (迴圈)](../logic-apps/logic-apps-control-flow-loops.md)
* [執行或合併平行步驟 (分支)](../logic-apps/logic-apps-control-flow-branches.md)
* [根據分組的動作狀態執行步驟 (範圍)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
