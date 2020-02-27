---
title: 作用中和非作用中的事件-個人化工具
description: 本文討論如何在個人化工具服務內使用作用中和非作用中的事件。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624320"
---
# <a name="active-and-inactive-events"></a>使用中和非使用中的事件

「作用中 **」事件是**任何對排名的呼叫，您知道要向客戶顯示結果，並決定報酬分數。 此為預設行為。

**非**作用中事件是對排名的呼叫，您不確定使用者是否會因為商務邏輯而看到建議的動作。 這可讓您捨棄事件，因此個人化工具不會以預設報酬進行定型。 非使用中事件不應呼叫獎勵 API。

學習迴圈必須知道實際的事件種類，這點很重要。 非使用中的事件將不會有報酬呼叫。 作用中的事件應該有報酬呼叫，但如果從未進行過 API 呼叫，則會套用預設的報酬分數。 將事件的狀態從非作用中變更為作用中，只要您知道它會影響使用者體驗。

## <a name="typical-active-events-scenario"></a>一般作用中事件案例

當您的應用程式呼叫排名 API 時，您會收到動作，應用程式應該會在 [ **rewardActionId** ] 欄位中顯示該動作。  從那一刻起，個人化工具預期會有具有相同 eventId 之報酬分數的報酬電話。 報酬分數是用來將模型定型以進行未來的排名呼叫。 如果沒有收到 eventId 的報酬電話，則會套用預設報酬。 [預設](how-to-settings.md#configure-rewards-for-the-feedback-loop)報酬會在 Azure 入口網站中的個人化工具資源上設定。

## <a name="other-event-type-scenarios"></a>其他事件種類案例

在某些情況下，應用程式可能需要先呼叫 Rank，才知道結果將使用或顯示給使用者。 例如，當行銷活動覆寫升級內容的頁面轉譯時，可能會發生這種情況。 如果從未使用過 Rank 呼叫的結果，且使用者從未看到它，則不傳送對應的報酬呼叫。

一般來說，這些案例會發生在下列情況：

* 您是可呈現的 UI，使用者可能會看到或看不到。
* 您的應用程式正在執行預測性個人化，其中的排名呼叫是以極少的即時內容進行，而且應用程式可能會或可能不會使用輸出。

在這些情況下，請使用個人化工具呼叫 Rank，要求事件為_非_使用中。 個人化工具不會預期此事件的報酬，而且不會套用預設報酬。

稍後在您的商務邏輯中，如果應用程式使用排名呼叫中的資訊，只要_啟動_事件即可。 一旦活動處於作用中狀態，個人化工具預期會有事件報酬。 如果沒有對獎勵 API 進行明確的呼叫，個人化工具會套用預設的報酬。

## <a name="inactive-events"></a>非作用中事件

若要停用事件的定型，請使用 `learningEnabled = False`呼叫 Rank。

針對非作用中事件，如果您傳送 eventId 的報酬或為該 eventId 呼叫 `activate` API，則會隱含地啟用學習。

## <a name="next-steps"></a>後續步驟

* 瞭解[如何判斷報酬分數和要考慮的資料](concept-rewards.md)。
