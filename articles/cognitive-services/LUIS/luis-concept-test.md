---
title: 測試 LUIS 應用程式
titleSuffix: Azure Cognitive Services
description: 測試是一個程序，其中您會將範例語句提供給 LUIS，然後獲得 LUIS 所辨識意圖和實體的回應。
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 25b360f90a0920aad2ea5e68cda31a68be5d37a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73486677"
---
# <a name="testing-example-utterances-in-luis"></a>在 LUIS 中測試範例語句

測試是一個程序，其中您會將範例語句提供給 LUIS，然後獲得 LUIS 所辨識意圖和實體的回應。 

您可以以對話模式測試 LUIS，一次一次一個陳述，或者提供一個陳述。 在測試時，您可以將當前活動模型的預測回應與已發佈的模型的預測回應進行比較。 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>什麼是測試分數？
請參閱[預測分數](luis-concept-prediction-score.md)概念，以深入了解預測分數。

## <a name="interactive-testing"></a>互動式測試
互動式測試從 LUIS 門戶的**測試**面板完成。 您可以輸入語句來查看意圖和實體的識別與評分情況。 如果 LUIS 未按預期在測試面板中的話語中預測意圖和實體，請將其作為新陳述複製到 **"意圖"** 頁。 然後為實體標記該話語的部分，並訓練 LUIS。 

## <a name="batch-testing"></a>批次測試
如果您要一次測試多個語句，請參閱[批次測試](luis-concept-batch-test.md)。

## <a name="endpoint-testing"></a>端點測試
您可以使用[端點](luis-glossary.md#endpoint)搭配最多兩個應用程式版本來進行測試。 在將您應用程式的主要或即時版本設定為**生產**端點的情況下，將第二個版本新增至**預備**端點。 此方法會提供您三個語句版本：在 [LUIS](luis-reference-regions.md) 網站 [測試] 窗格中的目前模型，以及在兩個不同端點的兩個版本。 

所有端點測試都會計入您的使用量配額中。 

## <a name="do-not-log-tests"></a>不記錄測試
如果您對端點進行測試，而不想要記錄語句，請記得使用 `logging=false` 查詢字串設定。

## <a name="where-to-find-utterances"></a>語句尋找位置
LUIS 在查詢日誌中存儲所有已記錄的話語，可從 **"應用"** 清單頁面在 LUIS 門戶上下載，以及 LUIS[創作 API](https://go.microsoft.com/fwlink/?linkid=2092087)。 

任何 LUIS 不確定的語句都會列在 [LUIS](luis-reference-regions.md) 網站的[檢閱端點語句](luis-how-to-review-endpoint-utterances.md)**** 頁面中。 

## <a name="remember-to-train"></a>請務必定型
在您對模型進行變更之後，請務必進行 LUIS [定型](luis-how-to-train.md)。 對 LUIS 應用程式所做的變更必須等到應用程式進行定型之後，才會顯示。 

## <a name="best-practices"></a>最佳作法
了解[最佳做法](luis-concept-best-practices.md)。

## <a name="next-steps"></a>後續步驟

* 深入了解如何[測試](luis-interactive-test.md)您的語句。
