---
title: 主動式學習建議-QnA Maker
description: 主動式學習建議可讓您根據使用者提交的問題和答案配對建議替代問題，以改善知識庫的品質。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: e8cdb1d471ceef2aef790c426424b9224faac5ec
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777032"
---
# <a name="active-learning-suggestions"></a>主動式學習建議

_主動式學習建議_功能可讓您根據使用者提交的問題和答案配對建議替代問題，來改善知識庫的品質。 您可以檢閱這些建議，並將其新增至現有的問題或加以拒絕。

您的知識庫不會自動變更。 為了讓變更生效，您必須接受建議。 這些建議會新增問題，但不會變更或移除現有的問題。

## <a name="what-is-active-learning"></a>何謂主動式學習？

QnA Maker 可透過隱含和明確的意見反應學習新演變出來的問題。

* [隱含意見](#how-qna-makers-implicit-feedback-works) 反應– ranker 瞭解使用者問題有多個答案，其中分數非常接近，並將此視為意見反應。 您不需要執行任何動作，就會發生這種情況。
* [明確的意見](#how-you-give-explicit-feedback-with-the-train-api) 反應：當知識庫中的多個答案（分數稍微變化）從知識庫傳回時，用戶端應用程式會詢問使用者哪個問題是正確的問題。 使用者的明確意見反應會透過 [定型 API](../How-to/improve-knowledge-base.md#train-api)傳送給 QnA Maker。

這兩種方法都會提供已叢集化類似查詢的 ranker。

## <a name="how-active-learning-works"></a>主動式學習的運作方式

主動式學習會根據 QnA Maker 所傳回的前幾個答案的分數來觸發。 如果符合查詢的 QnA 配對之間的分數差異落在一個小範圍內，則會將查詢視為可能的建議 (作為每個可能 QnA 配對的替代問題) 。 一旦您接受特定 QnA 配對的建議問題，就會拒絕其他配對的建議。 在接受建議之後，您必須記得儲存和定型。

當端點取得合理數量和足夠類型的使用查詢時，主動式學習可提供最佳的可能建議。 當您將5個或更多類似的查詢叢集化時，每隔30分鐘就會 QnA Maker 為知識庫設計工具提供接受或拒絕的使用者問題。 所有建議會依相似度叢集化在一起，並根據使用者發出特定查詢的頻率顯示替代問題的最高排名建議。

在 QnA Maker 入口網站中建議問題之後，您必須檢查並接受或拒絕這些建議。 沒有可管理建議的 API。

## <a name="turn-on-active-learning"></a>開啟主動式學習

主動學習預設為 **關閉**。
若要使用主動式學習：
* 您必須 [開啟主動式學習](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) ，讓 QnA Maker 為您的知識庫收集替代問題。
* 若要查看建議的替代問題，請使用 [編輯] 頁面上的 [ [視圖選項](../How-To/improve-knowledge-base.md#view-suggested-questions) ]。

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker 的隱含回饋如何運作

QnA Maker 的隱含意見反應會使用演算法來判斷分數鄰近性，然後提供主動式學習建議。 判定相近程度的演算法並非簡單的計算。 下列範例中的範圍並不是固定的，但應該作為指南，以瞭解演算法的影響。

當問題的分數具有高信賴度時 (例如 80%)，可考慮進行主動式學習的分數範圍較廣，大約在 10% 以內。 信賴分數下降時 (例如 40%)，分數的範圍也會隨之降低，大約在 4% 以內。

在下列從查詢到 QnA Maker generateAnswer 的 JSON 回應中，A、B 和 C 的分數已接近，並會被視為建議。

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

QnA Maker 不知道哪個答案是最佳答案。 使用 QnA Maker 入口網站的建議清單來選取最佳答案，並再次定型。


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>如何使用定型 API 提供明確的意見反應

QnA Maker 需要明確的意見反應，指出哪些答案是最佳答案。 判斷最佳答案的方式是由您決定，而且可以包括：

* 使用者意見反應，選取其中一個答案。
* 商務邏輯，例如判斷可接受的分數範圍。
* 使用者意見反應和商務邏輯的組合。

使用 [定型 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) ，在使用者選取之後，將正確的答案傳送至 QnA Maker。

## <a name="next-step"></a>後續步驟

> [!div class="nextstepaction"]
> [查詢知識庫](query-knowledge-base.md)