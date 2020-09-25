---
title: 個人化工具的運作方式 - 個人化工具
description: 個人化工具 _迴圈_ 會使用機器學習來建立模型，以預測內容的最佳動作。 模型會根據您使用排名和報酬呼叫傳送給它的資料，以獨佔方式定型。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: cfbe5cf8c19bfafb38f6149391e09350785ebf9c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91303602"
---
# <a name="how-personalizer-works"></a>個人化工具的運作方式

個人化工具資源（您的 _學習迴圈_）使用機器學習來建立模型，以預測內容的最高動作。 模型會根據您使用**排名****和報酬**呼叫傳送給它的資料，以獨佔方式定型。 每個迴圈都是完全獨立的。

## <a name="rank-and-reward-apis-impact-the-model"></a>排名和獎勵 Api 會影響模型

您可以 _使用功能_ 和 _內容功能_ 將動作傳送給排名 API。 **排名** API 會決定使用：

* _惡意_探索：目前的模型，以根據過去的資料來決定最佳的動作。
* _探索_：選取不同的動作，而不是最重要的動作。 您可以在 Azure 入口網站中為您的個人化工具資源 [設定此百分比](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) 。

您可以決定獎勵分數，並將該分數傳送給獎勵 API。 **報酬** API：

* 會收集資料，藉由記錄每個排名呼叫的特性和報酬分數來將模型定型。
* 會根據 _學習原則_中指定的設定，使用該資料來更新模型。

## <a name="your-system-calling-personalizer"></a>您的系統呼叫個人化工具

下圖顯示排名和報酬呼叫的呼叫架構流程：

![替代文字](./media/how-personalizer-works/personalization-how-it-works.png "個人化的運作方式")

1. 您可以 _使用功能_ 和 _內容功能_ 將動作傳送給排名 API。

    * 個人化工具會決定是否要利用目前的模型，或探索模型的新選擇。
    * 排名結果傳送至事件中樞。
1. 排名排名會傳回給您的系統作為 _獎勵動作識別碼_。
    您的系統會提供該內容，並根據您自己的商務規則來決定獎勵分數。
1. 您的系統會將獎勵分數傳回學習迴圈。
    * 當個人化工具收到報酬時，報酬會傳送至事件中樞。
    * 排名和報酬會相互關聯。
    * AI 模型會根據相互關聯結果來加以更新。
    * 推斷引擎會更新為新的模型。

## <a name="personalizer-retrains-your-model"></a>個人化工具重新定型您的模型

個人化工具會根據您在 Azure 入口網站中個人化工具資源上的 **模型頻率更新** 設定來重新定型模型。

個人化工具會根據您在 Azure 入口網站中個人化工具資源的 **資料保留期** 設定，使用目前保留的所有資料。

## <a name="research-behind-personalizer"></a>個人化工具背後的研究

個人化工具的依據是[增強式學習](concepts-reinforcement-learning.md)領域的尖端科學和研究，包括論文、研究活動和 Microsoft Research 中正在進行的探索領域。

## <a name="next-steps"></a>後續步驟

瞭解個人化工具的[熱門案例](where-can-you-use-personalizer.md)