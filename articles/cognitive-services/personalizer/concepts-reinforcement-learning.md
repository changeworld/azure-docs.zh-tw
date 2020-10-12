---
title: 增強式學習 - 個人化工具
titleSuffix: Azure Cognitive Services
description: 個人化工具會使用動作和目前內容的相關資訊來提出更好的排名建議。 這些動作和內容的相關資訊是稱為特性的屬性或內容。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: 8b97221de4921e06ddfab610618f37683b990181
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132733"
---
# <a name="what-is-reinforcement-learning"></a>增強式學習是什麼？

增強式學習是一種機器學習方法，會藉由從使用過程取得回饋以學習行為模式。
 
增強式學習的運作方式是：

* 提供制定行為的機會或自由度，例如進行決策或選擇。
* 提供環境和選擇的內容資訊。
* 提供該行為達成多少特定目標的意見反應。

雖然增強式學習有許多子類型和樣式，但以下是個人化工具中適用的概念：

* 應用程式會提供機會來顯示替代項目清單中的一段內容。
* 應用程式會提供關於每一種替代項目與使用者內容的資訊。
* 應用程式會計算「報酬分數」__。

不同於增強式學習的一些方法，個人化工具不需要有模擬環境就能工作。 其學習演算法的設計目的是要對外界做出回應 (而非對其進行控制)，並從每個資料點進行學習，因為其了解這是需要花費時間和金錢才能建立的獨特機會，如果效能未達到最佳狀況，將可能造成遺憾 (損失可能的報酬)。

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>個人化工具使用何種類型的增強式學習演算法？

個人化工具的目前版本會使用**內容相關吃角子老虎機**，這是根據要在給定內容下於不連續動作之間做出決策或選擇的情形所制定的一種增強式學習方法。

「決策記憶體」__ 是為了在給定內容下擷取最佳可能決策而定型的模型，其會使用一組線性模型。 這些經過實證的方法會一再顯示商務結果，部分原因是其可以非常快速地從現實世界中學習，而不需要完成多階段的訓練，另外部分原因則是其可以輔助監督式學習模型和深度神經網路模型。

探索/利用流量會遵循針對探索所設定的百分比來隨機配置，而且用於探索的預設演算法是 epsilon-greedy。

### <a name="history-of-contextual-bandits"></a>內容相關吃角子老虎機的歷史

John Langford 創造了「內容相關吃角子老虎機」(Contextual Bandits) 這個名詞 (Langford and Zhang [2007])，以便描述容易處理的增強式學習子集，並且他曾寫出六篇論文來讓我們更加了解如何在此範例中進行學習：

* Beygelzimer et al. [2011]
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer and Langford [2009]
* Li et al. [2010]

John 先前也曾提供數個關於各項主題的教學課程，例如 Joint Prediction (ICML 2015)、Contextual Bandit Theory (NIPS 2013)、Active Learning (ICML 2009) 和 Sample Complexity Bounds (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>個人化工具使用何種機器學習架構？

個人化工具目前使用 [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) 來作為機器學習的基礎。 此架構可在進行個人化排名和使用所有事件來將模型定型時，產生最大的輸送量和最低的延遲。

## <a name="references"></a>參考

* [在低技術債務下做出內容相關決策](https://arxiv.org/abs/1606.03966)
* [公平分類的縮減方法](https://arxiv.org/abs/1803.02453)
* [在非穩定世界中的有效率內容相關吃角子老虎機](https://arxiv.org/abs/1708.01799)
* [剩餘遺失預測：增強式：不含累加回饋的學習](https://openreview.net/pdf?id=HJNMYceCW)
* [使用增強式學習來將指示和視覺化觀察對應至動作](https://arxiv.org/abs/1704.08795)
* [學習成為比老師更加優秀的搜尋者](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>接下來的步驟

[離線評估](concepts-offline-evaluation.md) 