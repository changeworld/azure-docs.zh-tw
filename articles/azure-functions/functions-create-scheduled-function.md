---
title: 在 Azure 中建立會按照排程執行的函式
description: 了解如何在 Azure 中建立函式，並使其按照您定義的排程來執行。
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: be539efdb66b0a9bda583960484f40fae1e18235
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123379"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>在 Azure 中建立由計時器觸發的函式

了解如何使用 Azure Functions 來建立[無伺服器](https://azure.microsoft.com/solutions/serverless/)函式，並使其按照您定義的排程來執行。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

+ 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-function-app"></a>建立 Azure 函數應用程式

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

您的新函數應用程式已就緒，可供使用。 接下來，您將會在新的函數應用程式中建立函式。

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="已成功建立函數應用程式。" border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>建立由計時器觸發的函式

1. 在您的函式應用程式下，選取 [函數]，然後選取 [+ 新增]。 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="在 Azure 入口網站中新增函式。" border="true":::

1. 選取 [計時器觸發程序] 範本。 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="在 Azure 入口網站中選取計時器觸發程序。" border="true":::

1. 使用影像下方表格中指定的設定來設定新觸發程序，然後選取 [建立函式]。

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="在 Azure 入口網站中選取計時器觸發程序。" border="true":::
    
    | 設定 | 建議的值 | 描述 |
    |---|---|---|
    | **名稱** | 預設 | 定義計時器觸發函式的名稱。 |
    | **[排程]** | 0 \*/1 \* \* \* \* | 含有六個欄位的 [CRON 運算式](functions-bindings-timer.md#ncrontab-expressions)，它會將函式排程為每分鐘執行一次。 |

## <a name="test-the-function"></a>測試函式

1. 在您的函式中，選取 [程式碼 + 測試]，然後展開記錄。

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="在 Azure 入口網站中測試計時器觸發程序。" border="true":::

1. 檢視寫入到記錄的資訊以確認執行情形。

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="在 Azure 入口網站中檢視計時器觸發程序。" border="true":::

現在，您可以變更函式的排程，使其每小時執行一次，而非每分鐘執行一次。

## <a name="update-the-timer-schedule"></a>更新計時器排程

1. 在您的函式中，選取 [整合]。 在這裡，您可以定義函式的輸入與輸出繫結，以及設定排程。 

1. 選取 [計時器 (myTimer)]。

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="在 Azure 入口網站中更新計時器排程。" border="true":::

1. 將 [排程] 值更新為 `0 0 */1 * * *`，然後選取 [儲存]。  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="函式便會在 Azure 入口網站中更新計時器排程。" border="true":::

您現在已擁有每小時整點執行一次的函式。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>後續步驟

您已建立會根據排程執行的函式。 如需有關計時器觸發程序的詳細資訊，請參閱[使用 Azure Functions 排程程式碼執行](functions-bindings-timer.md)。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
