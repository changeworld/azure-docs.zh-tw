---
title: 主動學習建議 - QnA 製造商
description: 主動學習建議允許您根據使用者提交向問答對推薦其他問題，從而提高知識庫的品質。
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: af4f6b399bfd537b38ea741d03e59371ee81e588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053146"
---
# <a name="active-learning-suggestions"></a>主動學習建議

_"主動學習建議"_ 功能允許您根據使用者提交向問答對推薦其他問題，從而提高知識庫的品質。 您可以檢閱這些建議，並將其新增至現有的問題或加以拒絕。

您的知識庫不會自動變更。 為了使任何更改生效，您必須接受這些建議。 這些建議會新增問題，但不會變更或移除現有的問題。

## <a name="what-is-active-learning"></a>什麼是主動學習？

QnA Maker 可透過隱含和明確的意見反應學習新演變出來的問題。

* [隱式回饋](#how-qna-makers-implicit-feedback-works)– 當使用者問題有多個答案且分數非常接近，並將其視為回饋時，排名者會理解。 你不需要做任何事情來做到這一點。
* [明確回饋](#how-you-give-explicit-feedback-with-the-train-api)– 當從知識庫中返回分數變化不大的多個答案時，用戶端應用程式會詢問使用者哪個問題是正確的問題。 使用者的顯式回饋將隨[訓練 API](../How-to/improve-knowledge-base.md#train-api)發送給 QnA 製造商。

這兩種方法都為排名者提供群集的類似查詢。

## <a name="how-active-learning-works"></a>主動式學習的運作方式

活動學習根據 QnA Maker 返回的前幾個答案的分數觸發。 如果匹配查詢的 QnA 集之間的分數差異位於一個小範圍內，則查詢將被視為每個可能的 QnA 對的可能建議（作為備用問題）。 接受特定 QnA 對的建議問題後，其他對將拒絕該問題。 在接受建議後，您需要記住保存和訓練。

當端點取得合理數量和足夠類型的使用查詢時，主動式學習可提供最佳的可能建議。 當 5 個或更多類似的查詢被群集時，每 30 分鐘一次，QnA Maker 會向知識庫設計人員建議基於使用者的問題接受或拒絕。 所有建議會依相似度叢集化在一起，並根據使用者發出特定查詢的頻率顯示替代問題的最高排名建議。

在 QnA Maker 門戶中提出問題後，您需要查看並接受或拒絕這些建議。 沒有用於管理建議的 API。

## <a name="turn-on-active-learning"></a>開啟主動式學習

預設情況下，活動學習**處於關閉狀態**。
要使用主動學習：
* 您需要[打開主動學習](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions)，以便 QnA Maker 為您的知識庫收集備用問題。
* 要查看建議的備用問題，請使用"編輯"頁上[的"查看"選項](../How-To/improve-knowledge-base.md#view-suggested-questions)。

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA 製造商的隱式回饋的工作原理

QnA Maker 的隱式回饋使用演算法來確定分數接近程度，然後提出主動學習建議。 判定相近程度的演算法並非簡單的計算。 以下示例中的範圍不是要固定的，但應用作僅瞭解演算法影響的指南。

當問題的分數具有高信賴度時 (例如 80%)，可考慮進行主動式學習的分數範圍較廣，大約在 10% 以內。 信賴分數下降時 (例如 40%)，分數的範圍也會隨之降低，大約在 4% 以內。

在以下 JSON 回應中，從查詢到 QnA Maker 的生成應答，A、B 和 C 的分數接近，將被視為建議。

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA 製造商不知道哪個答案是最好的答案。 使用 QnA Maker 門戶的建議清單選擇最佳答案並再次訓練。


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>如何使用培訓 API 提供顯式回饋

QnA Maker 需要明確回饋，瞭解哪些答案是最佳答案。 如何確定最佳答案取決於您，可能包括：

* 使用者回饋，選擇其中一個答案。
* 業務邏輯，例如確定可接受的分數範圍。
* 使用者回饋和業務邏輯的組合。

使用[訓練 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train)在使用者選擇後向 QnA 製造商發送正確的答案。

## <a name="next-step"></a>後續步驟

> [!div class="nextstepaction"]
> [查詢知識庫](query-knowledge-base.md)