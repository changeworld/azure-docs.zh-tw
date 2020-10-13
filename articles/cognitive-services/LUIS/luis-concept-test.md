---
title: 測試 LUIS 應用程式
titleSuffix: Azure Cognitive Services
description: 測試是一個程序，其中您會將範例語句提供給 LUIS，然後獲得 LUIS 所辨識意圖和實體的回應。
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: ef63bf68fec592b768986a1329663508443c514b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535843"
---
# <a name="testing-example-utterances-in-luis"></a>在 LUIS 中測試範例語句

測試是一個程序，其中您會將範例語句提供給 LUIS，然後獲得 LUIS 所辨識意圖和實體的回應。 

您可以互動方式測試 LUIS，一次一個語句，或提供一組語句。 在測試過程中，您可以將目前使用中模型的預測回應與已發行之模型的預測回應進行比較。 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>什麼是測試分數？
請參閱[預測分數](luis-concept-prediction-score.md)概念，以深入了解預測分數。

## <a name="interactive-testing"></a>互動式測試
互動式測試會從 LUIS 入口網站的 [ **測試** ] 面板中完成。 您可以輸入語句來查看意圖和實體的識別與評分情況。 如果 LUIS 未在測試面板中的語句預測意圖和實體，請將它複製到 **意圖** 頁面作為新的語句。 然後為實體標記該語句的元件，並將 LUIS 定型。 

## <a name="batch-testing"></a>批次測試
如果您要一次測試多個語句，請參閱[批次測試](luis-concept-batch-test.md)。

## <a name="endpoint-testing"></a>端點測試
您可以使用[端點](luis-glossary.md#endpoint)搭配最多兩個應用程式版本來進行測試。 在將您應用程式的主要或即時版本設定為**生產**端點的情況下，將第二個版本新增至**預備**端點。 此方法會提供您三個語句版本：在 [LUIS](luis-reference-regions.md) 網站 [測試] 窗格中的目前模型，以及在兩個不同端點的兩個版本。 

所有端點測試都會計入您的使用量配額中。 

## <a name="do-not-log-tests"></a>不記錄測試
如果您對端點進行測試，而不想要記錄語句，請記得使用 `logging=false` 查詢字串設定。

## <a name="where-to-find-utterances"></a>語句尋找位置
LUIS 會將所有已記錄的語句儲存在查詢記錄中，可從 **應用程式** 清單頁面下載到 LUIS 入口網站，以及 LUIS [撰寫 api](https://go.microsoft.com/fwlink/?linkid=2092087)。 

任何 LUIS 不確定的語句都會列在 [LUIS](luis-reference-regions.md) 網站的[檢閱端點語句](luis-how-to-review-endpoint-utterances.md)**** 頁面中。 

## <a name="remember-to-train"></a>請務必定型
在您對模型進行變更之後，請務必進行 LUIS [定型](luis-how-to-train.md)。 對 LUIS 應用程式所做的變更必須等到應用程式進行定型之後，才會顯示。 

## <a name="best-practices"></a>最佳作法
了解[最佳做法](luis-concept-best-practices.md)。

## <a name="next-steps"></a>後續步驟

* 深入了解如何[測試](luis-interactive-test.md)您的語句。
