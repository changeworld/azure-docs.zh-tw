---
title: 如何執行批次測試-LUIS
titleSuffix: Azure Cognitive Services
description: 使用 Language Understanding (LUIS) 批次測試集，找出具有錯誤意圖和實體的語句。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/29/2020
ms.openlocfilehash: 2668f969076fd2b9960995fec44350d61b405740
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809409"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>使用一組範例語句的批次測試

批次測試會驗證您使用中的定型版本，以測量其預測精確度。 批次測試可協助您在使用中版本中，查看每個意圖和實體的精確度。 請參閱批次測試結果，以採取適當的動作來改善精確度，例如，如果您的應用程式經常無法識別正確意圖或標記語句中的實體，請將更多範例語句新增至意圖。

## <a name="group-data-for-batch-test"></a>批次測試的群組資料

重點是用於批次測試的語句，必須是 LUIS 中所沒有的。 如果您有一組語句資料，請將語句分割成三個集合：範例語句新增至意圖、從已發行的端點收到語句，以及語句在定型之後用來進行批次測試 LUIS。

您所使用的批次 JSON 檔案應該包含語句，其中包含標示為開始和結束位置的最上層機器學習實體。 語句不應為應用程式中既有範例的一部分。 其應為您想要正向預測意圖和實體的語句。

您可以依意圖和 (或) 實體來區分測試，或將所有測試 (最多 1000 個語句) 放在相同的檔案中。 

### <a name="common-errors-importing-a-batch"></a>匯入批次的常見錯誤

如果您在將批次檔上傳至 LUIS 時發生錯誤，請檢查下列常見的問題：

* 批次檔中的語句超過1000
* 沒有實體屬性的語句 JSON 物件。 屬性可以是空陣列。
* 在多個實體中標示的文字
* 實體標籤會在某個空間開始或結束。

## <a name="fixing-batch-errors"></a>修正批次錯誤

如果批次測試有錯誤，您可以將更多語句加入到意圖，及/或對於更多語句標示實體，幫助 LUIS 區別不同的意圖。 如果您加入並標示語句，但是測試批次仍然出現預測錯誤，請考慮加入具有特定領域詞彙的[片語清單](luis-concept-feature.md)功能，幫助 LUIS 加速學習。


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>使用 LUIS 入口網站進行批次測試 

### <a name="import-and-train-an-example-app"></a>匯入範例應用程式並加以定型

匯入接收比薩訂單 (例如 `1 pepperoni pizza on thin crust`) 的應用程式。

1.  下載並儲存[應用程式的 JSON 檔案](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true)。

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 選取 [ **新增應用程式** ] 旁邊的箭號，然後按一下 [匯 **入為 json** ]，將 JSON 匯入至新的應用程式。 為應用程式命名 `Pizza app` 。


1. 選取導覽區右上角的 [定型]，為應用程式定型。


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>批次測試檔案

範例 JSON 包含一個語句，且具有加上標籤的實體，用以說明測試檔案的結構。 在您自己的測試中，您應該會有許多標示了正確意圖和機器學習實體的語句。

1. 在文字編輯器中建立 `pizza-with-machine-learned-entity-test.json`，或[下載此項目](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)。

2. 在 JSON 格式的批次檔中，新增具有您想要在測試中預測之 **意圖** 的語句。

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>執行批次

1. 選取頂端導覽列中的 [Test] \(測試\)。

2. 選取右側面板中的 [Batch testing panel] \(批次測試面板\)

    ![批次測試連結](./media/luis-how-to-batch-test/batch-testing-link.png)

3. 選取 [匯入]。 在出現的對話方塊中，選取 **[選擇** 檔案]，並找出 json 格式正確的 json 檔案，其中包含 *不超過 1000* 語句來進行測試。

    若有匯入錯誤，系統會在瀏覽器頂端的紅色通知列中回報。 當匯入發生錯誤時，將不會建立任何資料集。 如需詳細資訊，請參閱[常見錯誤](#common-errors-importing-a-batch)。

4. 選擇 `pizza-with-machine-learned-entity-test.json` 檔案的檔案位置。

5. 將資料集命名為 `pizza test`，然後選取 [完成]。

6. 選取 [執行] 按鈕。 批次測試執行之後，請選取 [ **查看結果**]。 

    > [!TIP]
    > * 選取 [ **下載** ] 將會下載您上傳的相同檔案。
    > * 如果您看到批次測試失敗，則至少有一個語句意圖不符合預測。

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>檢閱意圖的批次結果

若要檢閱批次測試結果，請選取 [查看結果]。 測試結果會以圖形顯示對使用中的版本預測測試語句的結果。

批次圖表會顯示四個象限的結果。 圖表的右邊是一個篩選條件。 篩選包含意圖和實體。 當您選取某個[圖表區段](luis-concept-batch-test.md#batch-test-results)或圖表內的某一點時，相關聯的語句即會顯示於圖表下方。

將滑鼠停留在圖表上方時，滑鼠滾輪可以放大或縮小圖表中的顯示。 當圖表上有許多點緊密聚集在一起時，這非常有用。

此圖表分成四個象限，其中兩個區段會以紅色顯示。

1. 在篩選清單中選取 **ModifyOrder** 意圖。 語句預測為 **確判為真**，這表示語句成功符合其列於批次檔中的正向預測。

    > [!div class="mx-imgBorder"]
    > ![語句成功符合其正向預測](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    篩選器清單中出現綠色核取記號時，也表示各項意圖測試成功。 所有其他意圖都會以 1/1 的正值分數列出，因為已對每個意圖測試語句，而任何意圖的反向測試都不會列於批次測試中。

1. 選取 [確認] 意圖。 此意圖未列在批次測試中，因此這是批次測試中所列語句的反向測試。

    > [!div class="mx-imgBorder"]
    > ![針對批次檔中未列出的意圖成功將語句預測為負面](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    反向測試成功，這可以從篩選器和格線中的綠色文字看出。

### <a name="review-batch-test-results-for-entities"></a>檢閱實體的批次測試結果

ModifyOrder 實體是具有子實體的機器實體，會顯示最上層實體是否相符，以及如何預測子實體。

1. 選取篩選清單中的 **ModifyOrder** 實體，然後選取格線中的圓形。

1. 實體預測會顯示在圖表下方。 在顯示畫面中，符合預期的預測會顯示為實線，不符合預期的預測則顯示為虛線。

    > [!div class="mx-imgBorder"]
    > ![在批次檔中成功預測的實體父代](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>篩選圖表結果

若要依特定意圖或實體篩選圖表，請於右側的篩選面板中選取意圖或實體。 資料點及其分佈會根據您的選取範圍在圖表中更新。

![視覺化的批次測試結果](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>圖表結果範例

LUIS 入口網站中的圖表，您可以執行下列動作：
 
#### <a name="view-single-point-utterance-data"></a>檢視單一點語句資料

在圖表中，將滑鼠暫留於資料點上，以查看其預測的確定性分數。 選取資料點以擷取它在頁面底部語句清單中的相對應語句。

![選取的語句](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>檢視區段資料

在四個區段的圖表中，選取圖表右上角的區段名稱 (例如 **False Positive**)。 該區段中的所有語句會以清單的形式顯示在圖表下方。

![依區段選取的語句](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

在上面這個影像中，語句 `switch on` 具有 TurnAllOn 意圖的標記，但接收到 None 意圖的預測。 這代表 TurnAllOn 意圖需要更多範例語句，以取得預期的預測結果。

圖表中兩個以紅色顯示的區段，表示沒有符合預期預測的語句。 這代表它們是 LUIS 需要進行更多定型的語句。

圖表中另外兩個以綠色顯示的區段則有符合預期的預測。

## <a name="batch-testing-using-the-rest-api"></a>使用 REST API 的批次測試 

LUIS 可讓您使用 LUIS 入口網站和 REST API 來進行批次測試。 以下列出 REST API 的端點。 如需使用 LUIS 入口網站進行批次測試的詳細資訊，請參閱 [教學課程：批次測試資料集](luis-tutorial-batch-testing.md)。 使用下列完整的 Url，將預留位置值取代為您自己的 LUIS 預測金鑰和端點。 

請記得將您的 LUIS 金鑰新增至 `Apim-Subscription-Id` 標頭中，並設定 `Content-Type` 為 `application/json` 。

### <a name="start-a-batch-test"></a>啟動批次測試

使用應用程式版本識別碼或發佈位置來啟動批次測試。 將 **POST** 要求傳送至下列其中一個端點格式。 將您的批次檔包含在要求的主體中。

發佈位置
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

應用程式版本識別碼
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

這些端點將會傳回您將用來檢查狀態並取得結果的作業識別碼。 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>取得進行中批次測試的狀態

從您開始的批次測試中使用作業識別碼，以從下列端點格式取得其狀態： 

發佈位置
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

應用程式版本識別碼
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>取得批次測試的結果

從您開始的批次測試中使用作業識別碼，以從下列端點格式取得其結果： 

發佈位置
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

應用程式版本識別碼
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>語句的批次檔

針對批次測試提交語句的批次檔（稱為 *資料集*）。 資料集是 JSON 格式的檔案，最多可包含1000標籤的語句。 您可以在應用程式中測試最多10個資料集。 如果您需要測試更多，請刪除資料集，然後再新增一個。 即使批次檔資料中沒有對應的實體，模型中的所有自訂實體還是都會出現在批次測試實體篩選條件中。

批次檔由語句組成。 每個語句都必須有預期的意圖預測，以及您預期會偵測到的任何 [機器學習實體](luis-concept-entity-types.md#types-of-entities) 。

### <a name="batch-syntax-template-for-intents-with-entities"></a>使用實體的意圖批次語法範本

使用下列範本來啟動您的批次檔：

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

批次檔會使用 **startPos** 和 **endPos** 屬性，來記錄實體的開頭與結尾。 值是以零為起始的，而且不應以空格作為開頭或結尾。 這與查詢記錄不同，後者使用 startIndex 與 endIndex 屬性。

如果您不想要測試實體，則可包括 `entities` 屬性並將該值設為空陣列：`[]`。

### <a name="rest-api-batch-test-results"></a>REST API 批次測試結果

API 會傳回數個物件：

* 意圖和實體模型的相關資訊，例如精確度、召回率和 F 分數。
* 實體模型的相關資訊，例如每個實體的精確度、召回和 F 分數)  
  * `verbose`您可以使用旗標來取得實體的詳細資訊，例如 `entityTextFScore` 和 `entityTypeFScore` 。
* 以預測和標示的意圖名稱提供語句
* 錯誤的正值實體清單，以及錯誤負的實體清單。

## <a name="next-steps"></a>後續步驟

若測試顯示出您的 LUIS 應用程式無法識別正確的意圖和實體，則您可以透過標示更多語句或新增功能，以提升 LUIS 應用程式的效能。

* [使用 LUIS 標示建議的語調](luis-how-to-review-endpoint-utterances.md)
* [使用功能來改善 LUIS 應用程式效能](luis-how-to-add-features.md)
* [透過此教學課程來了解批次測試](luis-tutorial-batch-testing.md)
* [了解批次測試概念](luis-concept-batch-test.md).
