---
title: 教學課程：複合實體教學課程 - LUIS
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，新增複合實體，以便將擷取的各類型資料組合為單一包含實體。 用戶端應用程式可藉由組合資料，輕鬆地擷取不同資料類型的相關資料。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: f2b2c3f52610cd9fae0845b15aebf032a088000b
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75447954"
---
# <a name="tutorial-group-and-extract-related-data"></a>教學課程：擷取將相關的資料組成群組並加以擷取
在本教學課程中，新增複合實體，以便將擷取的各類型資料組合為單一包含實體。 用戶端應用程式可藉由組合資料，輕鬆地擷取不同資料類型的相關資料。

複合實體的用途是將相關實體群組為父類別實體。 在建立複合項目之前，這些資訊會以個別實體的形式存在。

複合實體很適合用於這類資料，因為此資料：

* 彼此相關。
* 使用各種實體類型。
* 需要由用戶端應用程式當作一個資訊單位進行分組和處理。

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 匯入範例應用程式
> * 建立意圖
> * 新增複合實體
> * 定型
> * 發佈
> * 從端點取得意圖和實體

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>匯入範例應用程式

1.  從清單實體教學課程下載並儲存[應用程式 JSON 檔案](
https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json?raw=true)。

2. 使用 [LUIS 入口網站](https://www.luis.ai)，將 JSON 匯入新的應用程式中。

3. 從 [管理]  區段的 [版本]  索引標籤上，複製版本並將它命名為 `composite`。 複製是一個既可測試各種 LUIS 功能又不影響原始版本的絕佳方式。 因為版本名稱會作為 URL 路由的一部分，所以此名稱不能包含任何在 URL 中無效的字元。

## <a name="composite-entity"></a>複合實體

在此應用程式中，部門名稱會定義於**部門**清單實體中，並包含同義字。

**TransferEmployeeToDepartment** 意圖具有要求員工移至新部門的範例語句。

此意圖的範例語句包括：

|範例語句|
|--|
|move John W. Smith to the accounting department|
|transfer Jill Jones from to R&D|

移動要求應包含部門名稱和員工名稱。

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>新增預先建置的實體 PersonName 以利擷取常見的資料類型

LUIS 提供數個預先建置的實體來擷取常見的資料。

1. 從上方導覽列中選取 [建置]  ，然後從左側導覽功能表中選取 [意圖]  。

1. 選取 [管理預先建置的實體]  按鈕。

1. 從預先建置的實體清單中選取 **[PersonName](luis-reference-prebuilt-person.md)** ，然後選取 [完成]  。

    ![預先建置的實體對話方塊中已選取 number 的螢幕擷取畫面](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    此實體可協助您將名稱辨識功能新增至用戶端應用程式。

## <a name="create-composite-entity-from-example-utterances"></a>從範例語句建立複合實體

1. 從左側導覽窗格中，選取 [Intents] \(意圖\)  。

1. 從意圖清單中選取 [TransferEmployeeToDepartment]  。

1. 在語句 `place John Jackson in engineering` 中，選取 personName 實體 `John Jackson`，然後在下列語句的快顯功能表清單中選取 [包裝於複合實體中]  。

    ![在下拉式對話方塊中選取包裝複合實體的螢幕擷取畫面](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. 接著，立即選取語句中的最後一個實體 `engineering`。 系統會在所選取的文字下方繪製綠色橫條，來表示複合實體。 在快顯功能表中，輸入複合名稱 `TransferEmployeeInfo`，然後選取 Enter。

    ![在下拉式對話方塊中輸入複合名稱的螢幕擷取畫面](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. 在 [您想要建立何種類型的實體?]  中，所有所需的欄位都會位於清單中：`personName` 和 `Department`。 選取 [完成]  。 請注意，預先建置的實體 personName 會新增至複合實體。 如果您讓預先建置的實體出現在複合實體的開頭與結尾語彙基元之間，則複合實體必須包含這些預先建置的實體。 如果未包含預先建置的實體，則無法正確預測複合實體，但可正確預測每個個別的元素。

    ![在下拉式對話方塊中輸入複合名稱的螢幕擷取畫面](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>使用複合實體來標示範例語句

1. 在每個範例語句中，選取應該出現在複合中的最左邊實體。 接著，選取 [包裝於複合實體中]  。

1. 選取複合實體中的最後一個字，然後從快顯功能表中選取 [TransferEmployeeInfo]  。

1. 確認已使用複合實體來標示意圖中的所有語句。

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>訓練應用程式，因此可以測試意圖的變更

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>發佈應用程式，因此可以從端點查詢已定型的模型

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>從端點取得意圖和實體預測

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 移至位址中的 URL 結尾並輸入 `Move Jill Jones to DevOps`。 最後一個查詢字串參數是 `q`，也就是語句查詢。

    由於此測試是要確認已正確擷取複合，因此，測試可以包含現有的範例語句或新語句。 在複合實體中包含所有子實體是個很好的測試。

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   這個語句會傳回複合實體陣列。 系統會為每個實體指定類型和值。 若要針對每個子實體獲取更高的精確度，請使用來自複合陣列項目的類型和值組合，來尋找實體陣列中的對應項目。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相關資訊

* [清單實體教學課程](luis-quickstart-intents-only.md)
* [複合實體](luis-concept-entity-types.md)的概念資訊
* [如何訓練](luis-how-to-train.md)
* [發佈方法](luis-how-to-publish-app.md)
* [如何在 LUIS 入口網站中測試](luis-interactive-test.md)


## <a name="next-steps"></a>後續步驟

本教學課程建立了複合實體，以便封裝現有的實體。 這可讓用戶端應用程式在不同的資料類型中尋找一組相關資料，以便繼續交談。 此人力資源應用程式的用戶端應用程式可以詢問需要開始及結束搬遷的日期和時間。 也可以詢問搬遷的其他搬運事宜，例如實體電話。

> [!div class="nextstepaction"]
> [檢閱端點語句以修正不確定的預測](luis-tutorial-review-endpoint-utterances.md)
