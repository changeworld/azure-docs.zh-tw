---
title: 個人化工具的運作方式 - 個人化工具
description: 個人化工具_迴圈_會使用機器學習來建立模型，以預測內容的最上層動作。 模型會以獨佔方式針對您使用排名和報酬呼叫傳送給它的資料進行定型。
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623764"
---
# <a name="how-personalizer-works"></a>個人化工具的運作方式

個人化工具資源（您的_學習迴圈_）會使用機器學習來建立模型，以預測內容的最上層動作。 模型會以獨佔方式針對您使用**排名**和**報酬呼叫傳送**給它的資料進行定型。 每個迴圈都完全獨立。

## <a name="rank-and-reward-apis-impact-the-model"></a>排名和獎勵 Api 會影響模型

您可以_使用功能_和_內容功能_，將動作傳送至排名 API。 **排名** API 會決定使用：

* _惡意_探索：根據過去的資料，決定最佳動作的目前模型。
* _探索_：選取不同的動作，而不是 top 動作。 您會在 Azure 入口網站中設定個人化工具資源的[此百分比](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt)。

您可以決定報酬分數，並將該分數傳送給獎勵 API。 **報酬** API：

* 會收集資料，藉由記錄每個排名呼叫的特性和報酬分數來將模型定型。
* 會根據_學習原則_中指定的設定，使用該資料來更新模型。

## <a name="your-system-calling-personalizer"></a>您的系統呼叫個人化工具

下圖顯示排名和報酬呼叫的呼叫架構流程：

![替代文字](./media/how-personalizer-works/personalization-how-it-works.png "個人化的運作方式")

1. 您可以_使用功能_和_內容功能_，將動作傳送至排名 API。

    * 個人化工具會決定是要利用目前的模型，還是探索新的模型選擇。
    * 排名結果傳送至事件中樞。
1. 排名最高的排名會傳回您的系統作為_獎勵動作識別碼_。
    您的系統會顯示該內容，並根據您自己的商務規則來決定報酬分數。
1. 您的系統會將獎勵分數傳回給學習迴圈。
    * 當個人化工具收到報酬時，報酬會傳送至事件中樞。
    * 排名和報酬會相互關聯。
    * AI 模型會根據相互關聯結果來加以更新。
    * 推斷引擎會更新為新的模型。

## <a name="personalizer-retrains-your-model"></a>個人化工具重新定型您的模型

個人化工具會根據您在 Azure 入口網站中個人化工具資源的**模型頻率更新**設定，重新定型您的模型。

個人化工具會使用目前保留的所有資料，根據您在 Azure 入口網站中個人化工具資源的天數內的**資料保留**設定。

## <a name="research-behind-personalizer"></a>個人化工具背後的研究

個人化工具的依據是[增強式學習](concepts-reinforcement-learning.md)領域的尖端科學和研究，包括論文、研究活動和 Microsoft Research 中正在進行的探索領域。

## <a name="next-steps"></a>後續步驟

瞭解個人化工具的[熱門案例](where-can-you-use-personalizer.md)