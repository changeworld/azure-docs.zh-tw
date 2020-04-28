---
title: 詢問可回答的問題資料
titleSuffix: ML Studio (classic) - Azure
description: 了解如何在「適用於初學者的資料科學」影片 3 中制訂明確的資料科學問題。 包含分類和迴歸問題的比較。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 26837337b49d79a26404fd6709b036f6907720f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73838846"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>詢問您可以使用資料回答的問題
## <a name="video-3-data-science-for-beginners-series"></a>影片 3：適用於初學者的資料科學系列
了解如何將在資料科學問題制訂至「適用於初學者的資料科學」影片 3 中的問題。 這個影片包含適用於分類和迴歸演算法的問題比較。

若要充分運用這系列影片，請觀賞所有影片。 [瀏覽影片清單](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>系列中的其他影片
** 是一個資料科學的快速簡介，包含五個簡短影片。

* 影片 1：[資料科學可以回答的 5 個問題](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 分 14 秒)*
* 影片 2： [您的資料已經可以進行資料科學了嗎？](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 分 56 秒)*
* 影片 3：詢問您可以使用資料回答的問題
* 影片 4：[利用簡單模型預測答案](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 分 42 秒)*
* 影片 5：[複製其他人的工作進行資料科學](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 分 18 秒)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>文字記錄：詢問您可以使用資料回答的問題
歡迎觀賞「適用於初學者的資料科學」系列的第三個影片。  

您可以從這個影片中獲得一些秘訣，在制訂可以使用資料回答的問題時使用。

如果您已先觀賞過本系列中的前兩個影片，則可從這個影片中獲得更多資訊：「5 個資訊科學可以回答的問題」和「已備妥資料來進行資料科學嗎？」

## <a name="ask-a-sharp-question"></a>詢問明確的問題
我們已經討論過資料科學是一個程序，可使用名稱 (亦稱為類別或標籤) 和數字來預測問題的答案， 但不能只是任意的問題，它必須是「明確的問題」**。

含糊不清的問題不需要使用名稱或數字來回答。 必須是明確的問題。

假設您找到一個神燈，其中的精靈將如實回答您詢問的任何問題。 但這是一個 mischievous 的 genie，他們會試著將它們的答案設為不清楚，而令人困惑。 您想要將其釘選到有問題的無懈可擊，讓他們無法協助，但告訴您想要知道的內容。

如果您詢問了含糊不清的問題，例如「我的股票會發生什麼事？」，精靈可能會回答「價格將有所變化」。 這是如實的回答，但不是很有幫助。

但如果您詢問明確的問題，例如「我的股票下週售價是多少？」，精靈就不得不給您一個特定的答案並預測銷售價格。

## <a name="examples-of-your-answer-target-data"></a>您的答案範例︰目標資料
一旦制定問題之後，請檢查資料中是否有答案的範例。

如果問題是「我的股票下週售價是多少？ 」，則我們必須確定資料包含股票價格記錄。

如果問題是「在我的車隊中，哪一部車會先故障？ 」，則我們必須確定資料包含先前故障的相關資訊。

![目標資料 - 您的答案範例。 制訂資料科學問題。](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

這些答案範例就稱為目標。 目標就是我們嘗試預測關於未來時間點的資訊，而不論它是類別或數字。

如果您沒有任何目標資料，就必須取得一些資料。 沒有這類資料，您將無法回答問題。

## <a name="reformulate-your-question"></a>重新制訂您的問題
有時候您可以重寫問題，以取得更有用的答案。

「這個資料點是 A 或 B？ 」的問題會預測某個項目的類別 (或名稱或標籤)。 我們使用「分類演算法」 ** 來回答問題。

「多少？ 」或「有多少？ 」這類問題會預測數量。 我們使用「迴歸演算法」 ** 來回答問題。

為了解我們如何轉換這些問題，讓我們看看下列問題：「這位讀者對哪一個新聞報導最感興趣？ 」 它要求從多個可能性中預測單一選擇 (換句話說，就是「這是 A 或 B 或 C 或 D？ 」)，並會使用分類演算法。

但是，如果您將它重寫為「這位讀者對於此清單上每篇報導的感興趣程度有多少？」，則這個問題可能比較容易回答。 現在您可以為每篇文章提供一個數值的分數，然後很容易就能找出分數最高的文章。 這會將分類問題改換措辭，使用迴歸問題或「多少？」來表述。

![重新制訂您的問題。 分類問題與迴歸問題。](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

您詢問問題的方式就是哪一個演算法可為您提供答案的線索。

您將會發現特定系列的演算法 (就像我們新聞報導範例中的演算法) 會密切相關。 您可以重新制訂問題，使用演算法來提供最實用的答案。

但最重要的是，詢問明確的問題 - 您可以使用資料回答的問題。 同時確定有正確的資料可回答問題。

我們談論了一些基本原則，讓您在詢問可使用資料回答的問題時使用。

請務必查看「適用于初學者的資料科學」的其他影片，Microsoft Azure Machine Learning Studio （傳統）。

## <a name="next-steps"></a>後續步驟
* [嘗試使用 Machine Learning Studio 的第一個資料科學實驗（傳統）](create-experiment.md)
* [在 Microsoft Azure 上取得 Machine Learning 簡介](/azure/machine-learning/preview/overview-what-is-azure-ml)
