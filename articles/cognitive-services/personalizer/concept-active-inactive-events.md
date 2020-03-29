---
title: 活動和非活動事件 - 個人化
description: 本文討論在個人化服務中使用活動和非活動事件。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624320"
---
# <a name="active-and-inactive-events"></a>使用中和非使用中的事件

**活動**事件是任何對 Rank 的呼叫，您知道要向客戶顯示結果並確定獎勵分數。 此為預設行為。

**非活動**事件是對 Rank 的調用，您不確定使用者是否會由於業務邏輯而看到建議的操作。 這允許您放棄事件，因此個人化器不會使用預設獎勵進行培訓。 非活動事件不應調用獎勵 API。

學習迴圈知道事件的實際類型很重要。 非活動事件將沒有獎勵呼叫。 活動事件應具有獎勵調用，但如果從未發出 API 呼叫，則應用預設獎勵分數。 一旦您知道事件會影響使用者體驗，就會立即將事件的狀態從非活動更改為活動狀態。

## <a name="typical-active-events-scenario"></a>典型的活動事件方案

當應用程式調用 Rank API 時，您將收到操作，應用程式應在**rewardActionId**欄位中顯示該操作。  從那一刻起，個人介紹者期望獎勵電話的獎勵分數具有相同的事件Id。 獎勵分數用於訓練模型以進行將來的排名呼叫。 如果未收到事件 Id 的獎勵呼叫，則應用預設獎勵。 [預設獎勵](how-to-settings.md#configure-rewards-for-the-feedback-loop)設置在 Azure 門戶中的個人化程式資源上。

## <a name="other-event-type-scenarios"></a>其他事件種類方案

在某些情況下，應用程式可能需要在知道結果是否被使用或顯示給使用者之前調用 Rank。 例如，升級內容的頁面呈現被市場行銷活動覆蓋，則可能發生這種情況。 如果從未使用 Rank 調用的結果，並且使用者從未看到過，請不要發送相應的獎勵呼叫。

通常，在以下情況下發生這些方案：

* 您正在預呈現使用者可能看到或可能不會看到的 UI。
* 應用程式正在執行預測性個人化，其中 Rank 調用很少即時上下文，應用程式可能使用也可能不使用輸出。

在這些情況下，請使用"個人化器"調用 Rank，請求事件_處於非活動狀態_。 個人介紹者不會期望此活動獲得獎勵，也不會應用預設獎勵。

稍後在業務邏輯中，如果應用程式使用 Rank 調用中的資訊，只需_啟動_事件即可。 活動一旦活躍，個人化者就期望獲得活動獎勵。 如果未對獎勵 API 進行顯式調用，則個人化程式將應用預設獎勵。

## <a name="inactive-events"></a>非作用中事件

要禁用事件訓練，請使用`learningEnabled = False`調用 Rank。

對於非活動事件，如果您為事件 Id 發送獎勵或調用該事件Id 的`activate`API，則隱式啟動學習。

## <a name="next-steps"></a>後續步驟

* [瞭解如何確定獎勵分數以及應考慮哪些資料](concept-rewards.md)。
