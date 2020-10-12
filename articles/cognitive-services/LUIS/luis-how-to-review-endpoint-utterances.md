---
title: 複習使用者語句-LUIS
titleSuffix: Azure Cognitive Services
description: 查看主動式學習所語句的專案，以選取意圖，並標記實體以進行讀取世界語句;接受變更、定型和發佈。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: 9777e4f9b2205d2f6cdf6158b035e2a18403d69f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541453"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>如何藉由檢查端點語句來改善 LUIS 應用程式

檢查端點語句以進行正確預測的程式稱為「 [主動學習](luis-concept-review-endpoint-utterances.md)」。 主動式學習會捕捉端點查詢，並選取不確定的使用者端點語句。 您可以複習這些語句來選取意圖，並標記這些真實世界語句的實體。 在您的範例語句中接受這些變更，然後定型和發佈。 LUIS 接著會更精確地識別語句。

## <a name="enable-active-learning"></a>啟用主動式學習

若要啟用主動式學習，您必須記錄使用者查詢。 這可透過使用 querystring 參數和值呼叫 [端點查詢](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) 來完成 `log=true` 。

使用 LUIS 入口網站來建立正確的端點查詢。

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 移至 [ **管理** ] 區段，然後選取 [ **Azure 資源**]。
1. 針對指派的預測資源，請選取 [ **變更查詢參數**]。

    > [!div class="mx-imgBorder"]
    > ![螢幕擷取畫面：顯示 [變更查詢參數] 連結。](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. 切換 [ **儲存記錄** ]，然後選取 [ **完成**] 以儲存。

    > [!div class="mx-imgBorder"]
    > ![使用 LUIS 入口網站來儲存主動學習所需的記錄。](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     此動作會藉由新增 `log=true` 查詢字串參數來變更範例 URL。 對執行階段端點進行預測查詢時，請複製並使用已變更的範例查詢 URL。

## <a name="correct-intent-predictions-to-align-utterances"></a>更正意圖預測以對齊語句

每個意圖都有建議的意圖顯示在 [一致的意圖]**** 資料行中。

> [!div class="mx-imgBorder"]
> [![檢查 LUIS 不確定的端點語句](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

如果您同意該意圖，請選取核取記號。 如果您不同意該建議，請從一致的意圖下拉式清單中選取正確的意圖，然後選取一致的意圖右邊的核取記號。 選取核取記號之後，語句就會移至意圖，並從 [ **審核端點語句** ] 清單中移除。

> [!TIP]
> 請務必移至 [意圖詳細資料] 頁面，以檢查並更正從 **審核端點語句** 清單中語句的所有範例的實體預測。

## <a name="delete-utterance"></a>刪除語句

您可以從檢閱清單中刪除每個語句。 刪除後，它就不再出現於清單中。 即使使用者從端點輸入相同意圖，情況也是如此。

如果您不確定是否應該刪除語句，請將它移至 [無] 意圖，或建立新的意圖（例如）， `miscellaneous` 並將語句移至該意圖。

## <a name="disable-active-learning"></a>停用主動式學習

若要停用主動式學習，請不要記錄使用者查詢。 這是藉由使用 querystring 參數和值設定 [端點查詢](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) `log=false` ，或不使用 querystring 值來完成，因為預設值是 false。

## <a name="next-steps"></a>後續步驟

若要在您標示建議的語句之後，測試效能有何改善，您可以選取頂端面板中的 [測試]**** 來存取測試主控台。 如需有關如何使用測試主控台來測試應用程式的指示，請參閱[訓練和測試您的應用程式](luis-interactive-test.md)。
