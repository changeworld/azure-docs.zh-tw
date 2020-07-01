---
title: 教學課程：檢閱端點語句 - LUIS
description: 在本教學課程中，藉由驗證或更正透過 LUIS 不確定的 LUIS HTTP 端點所收到的語句來改善應用程式的預測。 有些語句可能會針對意圖進行驗證，而其他語句則可能需要針對實體進行驗證。
services: cognitive-services
ms.topic: tutorial
ms.date: 06/22/2020
ms.openlocfilehash: c2df8cdba3422c522aa4ccf1fe4138a510355d12
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445914"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>教學課程：檢閱端點語句以修正不確定的預測
在本教學課程中，藉由驗證或更正透過 LUIS 不確定的 LUIS HTTPS 端點所收到的語句來改善應用程式的預測。 您應該在排定的 LUIS 維護中定期檢閱端點語句。

此檢閱程序可讓 LUIS 了解您的應用程式領域。 LUIS 會選取檢閱清單中顯示的語句。 此清單是：

* 應用程式所特有的。
* 為了改善應用程式的預測準確度。
* 應定期檢閱。

藉由檢閱端點語句，您可以確認或更正語句的預測意圖。

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 匯入範例應用程式
> * 檢閱端點語句
> * 定型和發佈應用程式
> * 查詢應用程式端點來查看 LUIS JSON 回應

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="download-json-file-for-app"></a>下載應用程式的 JSON 檔案

下載並儲存[應用程式的 JSON 檔案](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true)。

## <a name="import-json-file-for-app"></a>匯入應用程式的 JSON 檔案

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>為應用程式定型以將實體變更套用至應用程式

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>發佈應用程式以便從 HTTP 端點加以存取

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>在端點新增語句

在此應用程式中，您有意圖和實體，但沒有任何端點使用量。 必須有此端點使用量，才能透過檢閱端點語句來改善應用程式。

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 移至網址列中的 URL 尾端，並以下列資料表中的語句取代 _YOUR_QUERY_HERE_。 針對每個語句提交語句，並取得結果。 然後以下一個語句取代結尾的語句。

    |端點語句|對齊的意圖|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    不論您正在編輯的版本為何，也不論在端點上發佈的應用程式版本為何，都只有一個要檢閱的語句集區。

## <a name="review-endpoint-utterances"></a>檢閱端點語句

請檢閱端點語句，以獲得正確對齊的意圖。 雖然有單一語句集區可供進行跨所有版本的檢閱，但正確對齊意圖的程序會讓語句範例只新增至目前「使用中的模型」。

1. 從入口網站的 [建置] 區段中，選取左側導覽中的 [檢閱端點語句]。 此清單已針對 **ApplyForJob** 意圖進行篩選。

    > [!div class="mx-imgBorder"]
    > ![[檢閱端點語句] 按鈕在左側導覽中的螢幕擷取畫面](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    此語句 (`I'm looking for a job with Natural Language Processing`) 的意圖不正確。

1.  若要對齊此語句，請在語句資料列上，選取 `GetJobInformation` 的正確**對齊意圖**。 選取核取記號，即可將已變更的語句新增至應用程式。

    > [!div class="mx-imgBorder"]
    > ![[檢閱端點語句] 按鈕在左側導覽中的螢幕擷取畫面](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    檢閱此意圖中的其餘語句，並視需要更正對齊的意圖。 使用本教學課程中的初始語句資料表來檢視對齊的意圖。

    [檢閱端點語句] 清單應該不會再有更正過的語句。 如果出現更多語句，請繼續瀏覽清單並更正對齊的意圖，直到清單變成空白為止。

    從 [意圖詳細資料] 頁面對齊意圖之後，便會更正實體的標籤。

1. 再次將應用程式定型並發佈。

## <a name="get-intent-prediction-from-endpoint"></a>從端點取得意圖預測

若要確認正確對齊的語句範例是否已改善應用程式的預測，請嘗試使用接近更正後語句的語句。

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 移至網址列中的 URL 尾端，並以 `Are there any natural language processing jobs in my department right now?` 取代 _YOUR_QUERY_HERE_。

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.903607249
                },
                "EmployeeFeedback": {
                    "score": 0.0312187821
                },
                "ApplyForJob": {
                    "score": 0.0230276529
                },
                "MoveEmployee": {
                    "score": 0.008322801
                },
                "Utilities.Stop": {
                    "score": 0.004480808
                },
                "FindForm": {
                    "score": 0.00425248267
                },
                "Utilities.StartOver": {
                    "score": 0.004224336
                },
                "Utilities.Help": {
                    "score": 0.00373591436
                },
                "None": {
                    "score": 0.0034621188
                },
                "Utilities.Cancel": {
                    "score": 0.00230977475
                },
                "Utilities.Confirm": {
                    "score": 0.00112078607
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2019-12-05 23:23:53"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
   ```

   既然不確定的語句已正確對齊，所預測的正確意圖就會有**高分**。

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>可否藉由新增更多語句來取代檢閱？
您可能會納悶，為什麼不新增更多的語句範例。 檢閱端點語句的目的為何？ 在真實世界的 LUIS 應用程式中，端點語句來自於使用者，而其字組選擇和排列方式皆是您未曾用過的。 如果您使用過相同的字組選擇和排列方式，則原始的預測會有較高的百分比。

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>為何語句清單上會有最高分意圖？
某些端點語句會在檢閱清單中擁有高預測分數。 您還是需要檢閱並確認這些語句。 這些語句之所以位於清單上，是因為下一個最高意圖的分數非常接近最高分意圖的分數。 您希望兩個最高意圖之間大約有 15% 差異。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已檢閱在 LUIS 不確定的端點所提交的語句。 這些語句經過驗證並移至正確的意圖作為範例語句後，LUIS 將會提高預測準確度。

> [!div class="nextstepaction"]
> [了解如何使用模式](luis-tutorial-pattern.md)
