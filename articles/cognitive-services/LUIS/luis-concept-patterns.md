---
title: 模式説明預測 - LUIS
titleSuffix: Azure Cognitive Services
description: 模式可讓您取得更精確的意圖，而不需提供更多的語句。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6c1b548de25369c162b4a08dfa20fce62c17f99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75890277"
---
# <a name="patterns-improve-prediction-accuracy"></a>模式可改善預測精確度
模式設計用來改善數個語句非常類似時的精確度。  模式可讓您取得更精確的意圖，而不需提供更多的語句。 

## <a name="patterns-solve-low-intent-confidence"></a>模式可解決低意圖信賴度
請考慮使用人力資源應用程式，該應用程式會報告與員工相關的組織圖。 若指定員工的名稱和關係，LUIS 就會傳回涉及的員工。 請考慮使用員工 Tom，其經理名字為 Alice，且其下小組名字為：Michael、Rebecca 和 Carl。

![組織圖的影像](./media/luis-concept-patterns/org-chart.png)

|表達方式|預測的意圖|意圖分數|
|--|--|--|
|Who is Tom's subordinate?|GetOrgChart|.30|
|Who is the subordinate of Tom?|GetOrgChart|.30|

如果應用程式含有的 10 到 20 個語句具有不同的句子長度、不同的字組順序，甚至不同的字組 ("subordinate"、"manage"、"report" 的同義字)，LUIS 可能會傳回很低的信賴分數。 創建一個模式來説明 LUIS 瞭解單詞順序的重要性。 

模式可解決下列狀況： 

* 意圖分數較低
* 正確的意圖不是最高分，而是太接近最高分。 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>模式不是意圖保證
模式會混合使用多個預測技術。 在模式中設定範本語句的意圖並不是意圖預測的保證，但卻是很強的示意訊號。 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>模式不能改進機器學習的實體檢測

模式主要用於説明預測意圖和角色。 _模式.任何_實體用於提取自由格式實體。 當模式使用實體時，模式無助于檢測機器學習的實體。  

如果將多個語句摺疊成單一模式，則不要期望看到改善的實體預測。 要觸發簡單實體，您需要添加陳述或使用清單實體，否則模式不會觸發。

## <a name="patterns-use-entity-roles"></a>模式可使用實體角色
如果模式中有兩個以上的實體與內容相關，則模式會使用實體[角色](luis-concept-roles.md)來擷取關於實體的內容資訊。  

## <a name="prediction-scores-with-and-without-patterns"></a>使用和不使用模式的預測分數
假設具有足夠的範例語句，LUIS 就能夠在不使用模式的情況下提高預測信賴度。 模式可提高信賴分數，而不需要提供許多語句。  

## <a name="pattern-matching"></a>模式比對
模式比對會先偵測模式內的實體，然後驗證其餘字組和模式的字組順序。 模式中必須要有實體，才能比對模式。 模式會套用到權杖層級，不是字元層級。 

## <a name="pattern-only-apps"></a>僅限模式的應用
只要每個意圖都有模式，就可以構建具有沒有示例陳述的意向的應用。 對於僅模式應用，模式不應包含機器學習的實體，因為這些實體確實需要示例陳述。 

## <a name="best-practices"></a>最佳作法
了解[最佳做法](luis-concept-best-practices.md)。

## <a name="pattern-syntax"></a>模式語法

從[模式語法引用](reference-pattern-syntax.md)中學習模式語法。 

## <a name="next-steps"></a>後續步驟

瞭解有關模式的更多詳細資訊：

* [如何添加模式](luis-how-to-model-intent-pattern.md)
* [如何添加模式. 任何實體](luis-how-to-add-entities.md#add-a-patternany-entity)
* [模式語法](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [了解如何實作本教學課程中的模式](luis-tutorial-pattern.md)
