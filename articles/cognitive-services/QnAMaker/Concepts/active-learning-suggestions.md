---
title: 主動式學習建議-QnA Maker
description: 主動式學習建議可讓您根據使用者提交，對您的問答組提出替代問題，以改善知識庫的品質。
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 56f3ab870e148c39912d4f1f5e6e7133a5df4a98
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921680"
---
# <a name="active-learning-suggestions"></a>主動式學習建議

_主動式學習建議_功能可讓您根據使用者提交，對您的問答組提出替代問題，以改善知識庫的品質。 您可以檢閱這些建議，並將其新增至現有的問題或加以拒絕。

您的知識庫不會自動變更。 為了讓變更生效，您必須接受建議。 這些建議會新增問題，但不會變更或移除現有的問題。

## <a name="what-is-active-learning"></a>什麼是主動式學習？

QnA Maker 可透過隱含和明確的意見反應學習新演變出來的問題。

* [隱含的意見](#how-qna-makers-implicit-feedback-works)反應– ranker 瞭解使用者問題有多個答案的分數非常接近，並將此視為意見反應。 您不需要執行任何動作，就能進行這種情況。
* [明確的意見](#how-you-give-explicit-feedback-with-the-train-api)反應：當分數中有小變化的多個答案從知識庫傳回時，用戶端應用程式會詢問使用者哪個問題是正確的問題。 使用者的明確意見反應會透過[訓練 API](../How-to/improve-knowledge-base.md#train-api)傳送給 QnA Maker。

這兩種方法都會提供具有叢集之類似查詢的 ranker。

## <a name="how-active-learning-works"></a>主動式學習的運作方式

主動式學習會根據 QnA Maker 所傳回的前幾個答案的分數來觸發。 如果符合查詢的 QnA 集之間的分數差異落在較小的範圍內，則查詢會被視為每個可能 QnA 配對的可能建議（做為替代問題）。 一旦您接受特定 QnA 配對的建議問題，則會拒絕其他配對。 在接受建議之後，您必須記得儲存並定型。

當端點取得合理數量和足夠類型的使用查詢時，主動式學習可提供最佳的可能建議。 當有5個或更多類似的查詢叢集化時，每隔30分鐘 QnA Maker，就會針對要接受或拒絕的知識庫設計工具建議以使用者為基礎的問題。 所有建議會依相似度叢集化在一起，並根據使用者發出特定查詢的頻率顯示替代問題的最高排名建議。

在 QnA Maker 入口網站中建議問題後，您必須複習並接受或拒絕這些建議。 沒有可管理建議的 API。

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker 的隱含意見反應如何運作

QnA Maker 的隱含意見反應會使用演算法來判斷分數鄰近性，然後進行主動式學習建議。 判定相近程度的演算法並非簡單的計算。 下列範例中的範圍不是固定的，但應該做為參考，以瞭解演算法的影響。

當問題的分數具有高信賴度時 (例如 80%)，可考慮進行主動式學習的分數範圍較廣，大約在 10% 以內。 信賴分數下降時 (例如 40%)，分數的範圍也會隨之降低，大約在 4% 以內。

在下列從查詢到 QnA Maker generateAnswer 的 JSON 回應中，A、B 和 C 的分數會接近，並視為建議。

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

QnA Maker 不知道哪一個答案是最佳解答。 使用 QnA Maker 入口網站的建議清單來選取最佳答案，然後再次訓練。


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>如何使用訓練 API 提供明確的意見反應

QnA Maker 需要明確的意見反應，以瞭解哪一個答案是最佳解答。 判斷最佳答案的方式由您決定，而且可以包括：

* 使用者意見反應，選取其中一個答案。
* 商務邏輯，例如判斷可接受的分數範圍。
* 使用者意見反應和商務邏輯的組合。

在使用者選取之後，使用[訓練 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train)將正確答案傳送至 QnA Maker。

## <a name="next-step"></a>後續步驟

> [!div class="nextstepaction"]
> [查詢知識庫](query-knowledge-base.md)