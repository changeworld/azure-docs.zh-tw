---
title: 使用離線評估方法-個人化工具
titleSuffix: Azure Cognitive Services
description: 本文將說明如何使用離線評估來測量應用程式的有效性，並分析您的學習迴圈。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: f8ceef5e80bf15f0ba52a9c289e617018febfb5c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623596"
---
# <a name="offline-evaluation"></a>離線評估

離線評估是可讓您測試及評估個人化工具服務效用的方法，而這不會變更您的程式碼或影響使用者體驗。 離線評估會使用過去的資料，從您的應用程式傳送至排名和獎勵 Api，以比較不同的排名執行方式。

離線評估會根據日期範圍來執行。 該範圍的最終時間可以是目前的時間。 範圍起點不能超過[資料保留](how-to-settings.md)的指定天數。

離線評估可協助您回答下列問題：

* 個人化工具排名對成功的個人化有何效果？
    * 個人化工具線上機器學習原則能達到的平均獎勵有哪些？
    * 個人化工具如何與應用程式預設執行的功能成效做比較？
    * 隨機個人化選擇的比較成效為何？
    * 針對手動指定的不同學習原則，其比較成效為何？
* 哪些情境特性對成功的個人化有較多或較少貢獻？
* 哪些動作特性對成功的個人化有較多或較少貢獻？

此外，離線評估可用來探索最佳化程度更高的學習原則，未來可讓個人化工具用來改善結果。

離線評估不會針對用於探索的事件百分比提供指導。

## <a name="prerequisites-for-offline-evaluation"></a>離線評估的必要條件

代表性離線評估的重要考量如下：

* 有足夠的資料。 建議的最小值必須至少為 50,000 個事件。
* 從具有代表性使用者行為與流量的週期中收集資料。

## <a name="discovering-the-optimized-learning-policy"></a>探索最佳化的學習原則

個人化工具可使用離線評估程序來自動探索更好的學習原則。

執行離線評估之後，您可以看到新原則與目前線上原則相比之下，個人化工具所具有的比較成效。 接著，您可以套用該學習原則，讓它在個人化工具中立即生效，方法是下載它，然後在 [模型和原則] 面板中上傳它。 您也可以下載它以供日後分析或使用。

評估中包含的目前原則：

| 學習設定 | 目的|
|--|--|
|**線上原則**| 個人化工具中目前使用的學習原則 |
|**底線**|應用程式的預設值（由順位呼叫中所傳送的第一個動作所決定）|
|**隨機原則**|虛構排名行為，其一律會從所提供的動作傳回隨機選擇的動作。|
|**自訂原則**|開始評估時上傳的額外學習原則。|
|**優化的原則**|若開始評估時採用探索最佳化原則的選項，則也會比較最佳化原則，而且您能夠下載該原則或使其成為線上學習原則，並取代目前的學習原則。|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>了解離線評估結果的相關性

當您執行離線評估時，務必要分析結果的「信賴界限」。 如果界限很寬，表示您的應用程式未收到足夠的資料，無法準確或有效地預估獎勵。 由於系統會累積更多資料，而且您會對更長的期間執行離線評估，因此信賴區間會變得愈來愈窄。

## <a name="how-offline-evaluations-are-done"></a>離線評估的運作方式

離線評估會使用名為**反事實評估**的方法來進行。

個人化工具的基礎假設是使用者行為 (和獎勵) 無法回溯預測 (如果對使用者顯示的內容個與他們看到的內容不同，則個人化工具無法得知會發生什麼事)，並且只能從預估的獎勵中了解。

這是用於評估的概念程序：

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call

        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.

    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

離線評估只會使用觀察到的使用者行為。 此程序會捨棄大量的資料，特別是如果您的應用程式執行具有大量動作的排名呼叫。


## <a name="evaluation-of-features"></a>特性評估

離線評估可讓您了解有多少特定動作特性或情境特性在針對更高的獎勵而進行權衡。 資訊會使用評估來計算，並以指定期間和資料為依據，而且可能會因為時間不同而有所差異。

我們建議您查看特性評估並詢問：

* 您的應用程式或系統可提供什麼其他更有效果的額外特性？
* 可以移除哪些效果低的特性？ 效果低的特性會加重機器學習中的「干擾」。
* 是否有任何意外加入的特性？ 範例如下：使用者標識資訊、重複的識別碼等等。
* 是否有任何不可用的特性，也就是因為法規或責任考量而不應該用來個人化的特性？ 是否有可以取代不可用特性的特性 (也就是即近似或相關的特性)？


## <a name="next-steps"></a>後續步驟

[設定個人化工具](how-to-settings.md)
[執行離線評估](how-to-offline-evaluation.md)瞭解[個人化工具的運作方式](how-personalizer-works.md)
