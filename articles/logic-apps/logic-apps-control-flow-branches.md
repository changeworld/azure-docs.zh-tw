---
title: 為工作流程中的動作建立或加入平行分支
description: 了解如何在 Azure Logic Apps 中為獨立的工作流程動作建立或合併平行執行的分支
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: f857449d4511b6ae0a5a25bf7aca9e1abc1ae7c0
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83833688"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>在 Azure Logic Apps 中建立或加入工作流程動作的平行分支

根據預設，邏輯應用程式中的動作會依序執行。 若要同時執行獨立的動作，您可以建立[平行分支](#parallel-branches)，並在之後[將這些分支加入](#join-branches)您的流程中。 

> [!TIP] 
> 如果您的觸發程序接收到陣列，並想要針對每個陣列項目執行工作流程，您可以使用 [**SplitOn** 觸發屬性](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)將該陣列「解除批次」。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>新增平行分支

若要同時執行獨立的步驟，您可以在現有的步驟旁新增平行分支。 

![平行執行步驟](media/logic-apps-control-flow-branches/parallel.png)

邏輯應用程式會等到所有分支完成後，再繼續進行工作流程。 平行分支會在其 `runAfter` 屬性值符合完成的父步驟狀態時才執行。 例如，`branchAction1` 和 `branchAction2` 皆設定為在 `parentAction` 完成且狀態為 `Succeeded` 時，才會執行。

> [!NOTE]
> 開始之前，應用程式邏輯必須已有可新增平行分支的步驟。

1. 在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 將滑鼠指標移到您要新增平行分支的步驟上方的箭號。 選擇所顯示的**加號** ( **+** )，然後選擇 [新增平行分支]。 

   ![新增平行分支](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. 在搜尋方塊中，尋找並選取您想要的動作。

   ![尋找並選取您想要的動作](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   您選取的動作現在會出現在平行分支中，例如：

   ![尋找並選取您想要的動作](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. 立即在每個平行分支中，新增您想要的步驟。 若要將另一個動作新增至分支，請將滑鼠指標移至您要新增循序動作的動作下方。 選擇所顯示的**加號** ( **+** )，然後選取 [新增動作]。

   ![將循序動作新增至平行分支](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. 在搜尋方塊中，尋找並選取您想要的動作。

   ![尋找並選取循序動作](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   您選取的動作現在會出現目前的分支中，例如：

   ![尋找並選取循序動作](media/logic-apps-control-flow-branches/added-sequential-action.png)

若要將分支合併回去，則[加入您的平行分支](#join-branches)。 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>平行分支定義 (JSON)

如果您是在程式碼檢視中進行作業，可以改為以邏輯應用程式的 JSON 定義來定義平行結構，例如：

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>加入平行分支

若要將平行分支合併在一起，只需在底部 (所有分支下方) 新增步驟。 此步驟會在所有平行分支完成執行後再執行。

![加入平行分支](media/logic-apps-control-flow-branches/join.png)

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，尋找並開啟邏輯應用程式。 

1. 在您想要聯結的平行分支下方，選擇 [新增步驟]。 

   ![新增要聯結的步驟](media/logic-apps-control-flow-branches/add-join-step.png)

1. 在搜尋方塊中，尋找並選取您想要的動作，作為聯結分支的步驟。

   ![尋找並選取可聯結平行分支的步驟](media/logic-apps-control-flow-branches/join-steps.png)

   平行分支現在已合併。

   ![聯結的分支](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>加入定義 (JSON)

如果您是在程式碼檢視中進行作業，您可以改為以邏輯應用程式的 JSON 定義來定義加入結構，例如：

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>取得支援

* 如有任何問題，請瀏覽 [Azure Logic Apps 的 Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-logic-apps.html)。
* 若要提交或票選功能與建議，請造訪 [Azure Logic Apps 使用者意見反應網站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

* [根據條件 (條件陳述式) 執行步驟](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [根據不同的值 (Switch 陳述式) 執行步驟](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [執行並重複步驟 (迴圈)](../logic-apps/logic-apps-control-flow-loops.md)
* [根據分組的動作狀態執行步驟 (範圍)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
