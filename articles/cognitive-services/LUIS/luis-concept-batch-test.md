---
title: 批次測試 - LUIS
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221339"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>在 LUIS 入口網站使用 1000 個語句進行批次測試

批次處理測試驗證您的主動訓練版本，以測量其預測準確性。 批次處理測試可説明您查看活動版本中每個意圖和實體的準確性，並顯示帶有圖表的結果。 查看批次處理測試結果以採取適當的操作以提高準確性，例如，如果應用經常未能識別陳述中的正確意圖或標記實體，則向意圖添加更多示例陳述。

## <a name="group-data-for-batch-test"></a>批次測試的群組資料

重點是用於批次測試的語句，必須是 LUIS 中所沒有的。 如果您有一組陳述，請將陳述分為三組：添加到意圖的示例陳述、從已發佈終結點接收的話語以及用於在訓練後批次處理測試 LUIS 的表述。 

## <a name="a-data-set-of-utterances"></a>話語的資料集

提交一個批陳述檔，稱為*資料集*，用於批次處理測試。 資料集是一個 JSON 格式的檔，最多包含 1，000 個標記為**非重複**陳述。 您可以在應用中測試多達 10 個資料集。 如果需要測試更多，請刪除資料集，然後添加新資料集。

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

LUIS 跟蹤每個資料集的最後一次測試的狀態。 這包括大小 (批次中的語句數目)、上次執行日期及最後結果 (成功預測的語句數目)。

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>批次測試結果

批次測試結果會是散佈圖，也稱為錯誤矩陣。 此圖表是對於批次檔案中的語句以及目前模型的預測意圖和實體進行的 4 向比較。 

**誤判**和**漏判**區段的資料點表示錯誤，應該加以調查。 如果所有資料點都位於 **"真實正負****"和"真實負"** 部分，則應用在此資料集上的準確性是完美的。

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
