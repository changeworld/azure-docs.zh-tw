---
title: 查看使用者話語 - LUIS
titleSuffix: Azure Cognitive Services
description: 查看通過主動學習捕獲的話語，以選擇意圖並為閱讀世界陳述標記實體;接受更改、培訓和發佈。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221303"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>如何通過查看終結點陳述來改進 LUIS 應用

查看端點陳述以進行正確預測的過程稱為["活動學習](luis-concept-review-endpoint-utterances.md)"。 活動學習捕獲終結點查詢並選擇使用者不確定的終結點陳述。 查看這些陳述以選擇這些讀取世界話語的意圖和標記實體。 接受這些更改到您的示例陳述中，然後培訓和發佈。 然後，LUIS 更準確地識別陳述。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>啟用主動學習

要啟用活動學習，必須記錄使用者查詢。 這是通過使用查詢字串參數和值調用[終結點查詢](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint)來實現的`log=true`。

使用 LUIS 門戶構造正確的終結點查詢。

1. 在[預覽 LUIS 門戶](https://preview.luis.ai/)中，從應用清單中選擇你的應用。
1. 轉到 **"管理**"部分，然後選擇**Azure 資源**。
1. 對於分配的預測資源，選擇 **"更改查詢參數**"。

    > [!div class="mx-imgBorder"]
    > ![使用 LUIS 門戶保存日誌，這是活動學習所必需的。](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. 切換 **"保存"日誌**，然後通過選擇 **"完成"** 進行保存。

    > [!div class="mx-imgBorder"]
    > ![使用 LUIS 門戶保存日誌，這是活動學習所必需的。](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     此動作會藉由新增 `log=true` 查詢字串參數來變更範例 URL。 對執行階段端點進行預測查詢時，請複製並使用已變更的範例查詢 URL。

## <a name="correct-intent-predictions-to-align-utterances"></a>更正意圖預測以對齊陳述

每個意圖都有建議的意圖顯示在 [一致的意圖]**** 資料行中。

> [!div class="mx-imgBorder"]
> [![查看 LUIS 不確定的終結點陳述](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

如果您同意該意圖，請選擇核取記號。 如果您不同意該建議，請從一致的意圖下拉式清單中選取正確的意圖，然後選取一致的意圖右邊的核取記號。 在核取記號上選擇後，陳述將移動到意圖，並從 **"查看終結點陳述"** 清單中刪除。

> [!TIP]
> 請務必轉到"意向詳細資訊"頁，以查看和更正 **"查看終結點內容**"清單中所有示例陳述中的實體預測。

## <a name="delete-utterance"></a>刪除語句

您可以從檢閱清單中刪除每個語句。 刪除後，它就不再出現於清單中。 即使使用者從端點輸入相同意圖，情況也是如此。

如果您不確定是否應刪除陳述，請將其移動到"無"意圖，或創建一個新意圖，例如`miscellaneous`，並將陳述移動到該意圖。

## <a name="disable-active-learning"></a>禁用活動學習

要禁用活動學習，請不要記錄使用者查詢。 這是通過使用`log=false`查詢字串參數和值設置[終結點查詢](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)來實現的，或者不使用查詢字串值，因為預設值為 false。

## <a name="next-steps"></a>後續步驟

若要在您標示建議的語句之後，測試效能有何改善，您可以選取頂端面板中的 [測試]**** 來存取測試主控台。 如需有關如何使用測試主控台來測試應用程式的指示，請參閱[訓練和測試您的應用程式](luis-interactive-test.md)。
