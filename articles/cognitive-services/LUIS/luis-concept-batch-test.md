---
title: 批次測試-LUIS
titleSuffix: Azure Cognitive Services
description: 使用批次測試持續調整您的應用程式，改良應用程式及其語言理解能力。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: e9ad7c52af20762633c710b39a64fbebf0cf6213
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "79221339"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>在 LUIS 入口網站使用 1000 個語句進行批次測試

批次測試會驗證您的有效定型版本，以測量其預測準確度。 批次測試可協助您查看使用中版本中每個意圖和實體的精確度，並以圖表顯示結果。 請檢查批次測試結果，採取適當的動作來改善精確度，例如，如果您的應用程式經常無法識別正確意圖或標記語句中的實體，則將更多範例語句新增至意圖。

## <a name="group-data-for-batch-test"></a>批次測試的群組資料

重點是用於批次測試的語句，必須是 LUIS 中所沒有的。 如果您有一組語句的資料，請將語句分成三個集合：新增至意圖的範例語句、從已發行的端點接收的語句，以及在定型後用來進行批次測試語句的 LUIS。 

## <a name="a-data-set-of-utterances"></a>語句的資料集

提交語句的批次檔（稱為*資料集*）以進行批次測試。 資料集是 JSON 格式的檔案，包含最多1000標記為**非重複**的語句。 您可以在應用程式中測試最多10個資料集。 如果您需要測試更多項，請先刪除資料集，然後再新增一個。

|**規則**|
|--|
|*無重複的語句|
|1000 個語句或更少|

*重複項目會被視為完全相同的相符字串項目，而不是先權杖化的相符項目。 

## <a name="entities-allowed-in-batch-tests"></a>批次測試中允許的實體

即使批次檔資料中沒有對應的實體，模型中的所有自訂實體還是都會出現在批次測試實體篩選條件中。

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>批次檔格式

批次檔由語句組成。 每個語句都必須有預期的意圖預測，以及您想要偵測的[機器學習實體](luis-concept-entity-types.md#types-of-entities)。 

## <a name="batch-syntax-template-for-intents-with-entities"></a>使用實體的意圖批次語法範本

使用下列範本來啟動您的批次檔：

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

批次檔會使用 **startPos** 和 **endPos** 屬性，來記錄實體的開頭與結尾。 值是以零為起始的，而且不應以空格作為開頭或結尾。 這與查詢記錄不同，後者使用 startIndex 與 endIndex 屬性。 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>不使用實體的意圖批次語法範本

使用下列範本來啟動沒有實體的批次檔：

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

如果您不想要測試實體，則可包括 `entities` 屬性並將該值設為空陣列：`[]`。


## <a name="common-errors-importing-a-batch"></a>匯入批次的常見錯誤

常見錯誤包括： 

> * 超過 1000 個語句
> * 沒有實體屬性的語句 JSON 物件。 屬性可以是空陣列。
> * 在多個實體中標示的文字
> * 實體標籤以空格開始或結束。

## <a name="batch-test-state"></a>批次測試狀態

LUIS 會追蹤每個資料集最後一項測試的狀態。 這包括大小 (批次中的語句數目)、上次執行日期及最後結果 (成功預測的語句數目)。

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>批次測試結果

批次測試結果會是散佈圖，也稱為錯誤矩陣。 此圖表是對於批次檔案中的語句以及目前模型的預測意圖和實體進行的 4 向比較。 

**誤判**和**漏判**區段的資料點表示錯誤，應該加以調查。 如果所有資料點都位於**真肯定**和**真否定**區段，則您的應用程式精確度在此資料集上是最佳的。

![圖表的 4 個區段](./media/luis-concept-batch-test/chart-sections.png)

這個圖表有助於您找到 LUIS 依照其目前訓練內容而錯誤預測的語句。 結果會依區域顯示在圖表上。 選取圖表上各點來檢閱語句資訊，或選取區域名稱檢閱該區域中的語句結果。

![批次測試](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>結果中的錯誤

批次測試中顯示錯誤，表示並未預測出如批次檔中所述之意圖。 圖表的兩個紅色區段會顯示錯誤。 

誤判區段顯示語句對應到不應該對應的意圖或實體。 漏判區段顯示語句未對應到應該對應的意圖或實體。 

## <a name="fixing-batch-errors"></a>修正批次錯誤

如果批次測試有錯誤，您可以將更多語句加入到意圖，及/或對於更多語句標示實體，幫助 LUIS 區別不同的意圖。 如果您加入並標示語句，但是測試批次仍然出現預測錯誤，請考慮加入具有特定領域詞彙的[片語清單](luis-concept-feature.md)功能，幫助 LUIS 加速學習。 

## <a name="next-steps"></a>後續步驟

* 了解如何[測試批次](luis-how-to-batch-test.md)
