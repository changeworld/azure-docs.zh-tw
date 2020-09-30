---
title: 教學課程：使用批次測試找出問題 - LUIS
description: 本教學課程示範如何使用批次測試來驗證 Language Understanding (LUIS) 應用程式的品質。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2020
ms.openlocfilehash: 7dd181f8cd398dd683296b446028b398a9800b53
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298304"
---
# <a name="tutorial-batch-test-data-sets"></a>教學課程：批次測試資料集

本教學課程示範如何使用批次測試來驗證 Language Understanding (LUIS) 應用程式的品質。

批次測試可讓您使用一組已標示的已知語句和實體來驗證作用中且已定型的模型狀態。 在 JSON 格式的批次檔中新增語句，並在語句內設定您需要預測的實體標籤。

批次測試的需求：

* 每個測試最多 1000 個語句。
* 沒有重複項目。
* 允許的實體類型：僅限機器學習實體。

使用本教學課程以外的應用程式時，請「不要」使用已新增至應用程式的範例語句。

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 匯入範例應用程式
> * 建立批次測試檔案
> * 執行批次測試
> * 檢閱測試結果

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>匯入範例應用程式

匯入接收比薩訂單 (例如 `1 pepperoni pizza on thin crust`) 的應用程式。

1.  下載並儲存[應用程式的 JSON 檔案](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true)。

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 將 JSON 匯入新的應用程式中，並將應用程式命名為 `Pizza app`。


1. 選取導覽區右上角的 [定型]，為應用程式定型。

## <a name="what-should-the-batch-file-utterances-include"></a>批次檔語句應包含的內容

批次檔應包含語句，並且標示最上層的機器學習實體，包括開始和結束位置。 語句不應為應用程式中既有範例的一部分。 其應為您想要正向預測意圖和實體的語句。

您可以依意圖和 (或) 實體來區分測試，或將所有測試 (最多 1000 個語句) 放在相同的檔案中。

## <a name="batch-file"></a>批次檔

範例 JSON 包含一個語句，且具有加上標籤的實體，用以說明測試檔案的結構。 在您自己的測試中，您應該會有許多標示了正確意圖和機器學習實體的語句。

1. 在文字編輯器中建立 `pizza-with-machine-learned-entity-test.json`，或[下載此項目](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)。

2. 在 JSON 格式的批次檔中，新增具有您想要在測試中預測之**意圖**的語句。

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>執行批次

1. 選取頂端導覽列中的 [Test] \(測試\)。

2. 選取右側面板中的 [Batch testing panel] \(批次測試面板\)

3. 選取 [Import dataset] \(匯入資料集\)。

    > [!div class="mx-imgBorder"]
    > ![已醒目提示 [匯入資料集] 的 LUIS 應用程式螢幕擷取畫面](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. 選擇 `pizza-with-machine-learned-entity-test.json` 檔案的檔案位置。

5. 將資料集命名為 `pizza test`，然後選取 [完成]。

    > [!div class="mx-imgBorder"]
    > ![選取檔案](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. 選取 [執行] 按鈕。

7. 選取 [See results] \(查看結果\)。

8. 檢閱圖表和圖例中的結果。

## <a name="review-batch-results-for-intents"></a>檢閱意圖的批次結果

測試結果會以圖形顯示對使用中的版本預測測試語句的結果。

批次圖表會顯示四個象限的結果。 圖表的右邊是一個篩選條件。 篩選包含意圖和實體。 當您選取某個[圖表區段](luis-concept-batch-test.md#batch-test-results)或圖表內的某一點時，相關聯的語句即會顯示於圖表下方。

將滑鼠停留在圖表上方時，滑鼠滾輪可以放大或縮小圖表中的顯示。 當圖表上有許多點緊密聚集在一起時，這非常有用。

此圖表分成四個象限，其中兩個區段會以紅色顯示。

1. 在篩選清單中選取 **ModifyOrder** 意圖。

    > [!div class="mx-imgBorder"]
    > ![從篩選清單中選取 ModifyOrder 意圖](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    語句預測為**確判為真**，這表示語句成功符合其列於批次檔中的正向預測。

    > [!div class="mx-imgBorder"]
    > ![語句成功符合其正向預測](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    篩選器清單中出現綠色核取記號時，也表示各項意圖測試成功。 所有其他意圖都會以 1/1 的正值分數列出，因為已對每個意圖測試語句，而任何意圖的反向測試都不會列於批次測試中。

1. 選取 [確認] 意圖。 此意圖未列在批次測試中，因此這是批次測試中所列語句的反向測試。

    > [!div class="mx-imgBorder"]
    > ![針對批次檔中未列出的意圖成功將語句預測為負面](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    反向測試成功，這可以從篩選器和格線中的綠色文字看出。

## <a name="review-batch-test-results-for-entities"></a>檢閱實體的批次測試結果

ModifyOrder 實體是具有子實體的機器實體，會顯示最上層實體是否相符，並顯示預測子實體的情形。

1. 選取篩選清單中的 **ModifyOrder** 實體，然後選取格線中的圓形。

1. 實體預測會顯示在圖表下方。 在顯示畫面中，符合預期的預測會顯示為實線，不符合預期的預測則顯示為虛線。

    > [!div class="mx-imgBorder"]
    > ![在批次檔中成功預測的實體父代](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>透過批次測試尋找錯誤

本教學課程示範了如何執行測試並解讀結果。 在此並未說明測試原理或如何因應失敗的測試。

* 請務必在測試中納入正面和負面語句，包括可能對不同但相關的意圖預測的語句。
* 針對失敗的語句，請執行下列工作，然後再次執行測試：
    * 若要檢閱意圖和實體的目前範例，請驗證使用中版本的範例語句對於意圖和實體標籤而言都是正確的。
    * 新增有助於應用程式預測意圖和實體的功能
    * 新增更多正面範例語句
    * 查看範例語句在各意圖間的協調性

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>後續步驟

本教學課程使用批次測試來驗證目前的模型。

> [!div class="nextstepaction"]
> [了解模式](luis-tutorial-pattern.md)

