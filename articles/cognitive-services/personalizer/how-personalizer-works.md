---
title: 個人化工具的運作方式 - 個人化工具
description: 個人化器_迴圈_使用機器學習構建模型，預測內容的頂級操作。 模型僅針對您通過"排名"和"獎勵"呼叫發送給它的資料進行專門培訓。
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623764"
---
# <a name="how-personalizer-works"></a>個人化工具的運作方式

個人化資源，您的_學習迴圈_，使用機器學習來構建模型，預測內容的頂級操作。 模型僅針對您通過 **"排名**"和 **"獎勵**"呼叫發送給它的資料進行專門培訓。 每個迴圈都完全獨立。

## <a name="rank-and-reward-apis-impact-the-model"></a>排名和獎勵 API 影響模型

向 Rank API 發送_具有功能_和_上下文功能_的操作。 **排名** API 會決定使用：

* _利用 ：_ 當前模型，根據過去的資料決定最佳操作。
* _流覽_：選擇其他操作而不是頂部操作。 在 Azure 門戶中為個人化工具資源[配置此百分比](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt)。

確定獎勵分數並將該分數發送到獎勵 API。 **報酬** API：

* 會收集資料，藉由記錄每個排名呼叫的特性和報酬分數來將模型定型。
* 使用該資料根據_學習策略_中指定的配置更新模型。

## <a name="your-system-calling-personalizer"></a>您的系統調用個人化器

下圖顯示排名和報酬呼叫的呼叫架構流程：

![alt 文本](./media/how-personalizer-works/personalization-how-it-works.png "個人化的工作原理")

1. 向 Rank API 發送_具有功能_和_上下文功能_的操作。

    * 個人化程式決定是利用當前模型還是探索模型的新選擇。
    * 排名結果傳送至事件中樞。
1. 排名將作為_獎勵行動 ID_返回到您的系統。
    您的系統會顯示該內容，並根據您自己的商務規則確定獎勵分數。
1. 您的系統將獎勵分數返回到學習迴圈。
    * 當個人化工具收到報酬時，報酬會傳送至事件中樞。
    * 排名和報酬會相互關聯。
    * AI 模型會根據相互關聯結果來加以更新。
    * 推斷引擎會更新為新的模型。

## <a name="personalizer-retrains-your-model"></a>個人化程式重新訓練您的模型

個人化程式根據 Azure 門戶中的個人化程式資源上的**模型頻率更新**設置重新訓練模型。

個人化程式使用當前保留的所有資料，具體取決於 Azure 門戶中個人化程式資源上的 **"資料保留**"設置。

## <a name="research-behind-personalizer"></a>個人化工具背後的研究

個人化工具的依據是[增強式學習](concepts-reinforcement-learning.md)領域的尖端科學和研究，包括論文、研究活動和 Microsoft Research 中正在進行的探索領域。

## <a name="next-steps"></a>後續步驟

瞭解個人化程式[的頂級方案](where-can-you-use-personalizer.md)