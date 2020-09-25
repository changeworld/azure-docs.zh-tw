---
title: 作用中和非作用中事件-個人化工具
description: 本文討論在個人化工具服務中使用作用中和非作用中的事件。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 7d1044d02ceba1f3d0996b1fe1c8a9a44b31049b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91253644"
---
# <a name="active-and-inactive-events"></a>使用中和非使用中的事件

作用 **中事件是** 對排名的任何呼叫，您知道您要向客戶顯示結果並決定獎勵分數。 此為預設行為。

**非**作用中事件是對排名的呼叫，您不確定使用者是否會看到建議的動作，因為商務邏輯。 這可讓您捨棄事件，因此個人化工具不會以預設獎勵定型。 非作用中事件不應呼叫獎勵 API。

學習迴圈必須知道實際的事件種類是很重要的。 非作用中的事件將不會有獎勵通話。 使用中的事件應該有獎勵呼叫，但如果從未進行過 API 呼叫，則會套用預設獎勵分數。 一旦您知道事件將會影響使用者體驗，請立即將事件的狀態從非使用狀態變更為「作用中」。

## <a name="typical-active-events-scenario"></a>一般活動事件案例

當您的應用程式呼叫排名 API 時，您會收到動作，應用程式應該會在 [ **rewardActionId** ] 欄位中顯示該動作。  從那時起，個人化工具預期獎勵分數具有相同的 eventId。 報酬分數是用來將模型定型以進行未來的排名呼叫。 如果未收到 eventId 的報酬電話，則會套用預設獎勵。 [預設獎勵](how-to-settings.md#configure-rewards-for-the-feedback-loop) 是在 Azure 入口網站中的個人化工具資源上設定的。

## <a name="other-event-type-scenarios"></a>其他事件種類案例

在某些情況下，應用程式可能需要先呼叫排名，才能得知結果將會使用或向使用者顯示。 例如，當行銷活動覆寫升級內容的頁面轉譯時，可能會發生這種情況。 如果從未使用過排名呼叫的結果，且使用者從未看到過，請不要傳送對應的獎勵通話。

一般而言，這些案例會在下列情況發生：

* 您是以系統呈現的 UI，使用者可能會或可能不會看到。
* 您的應用程式正在進行預測性個人化，其中的排名呼叫會以較少的即時內容進行，而應用程式可能會或可能不會使用輸出。

在這些情況下，請使用個人化工具呼叫 Rank，要求事件為 _非_使用中。 個人化工具不會預期此事件的獎勵，也不會套用預設獎勵。

之後在您的商務邏輯中，如果應用程式使用排名呼叫的資訊，只要 _啟動_ 事件即可。 當活動為使用中時，個人化工具就會預期事件獎勵。 如果沒有對獎勵 API 進行明確呼叫，個人化工具會套用預設獎勵。

## <a name="inactive-events"></a>非作用中事件

若要停用事件的定型，請使用來呼叫排名 `learningEnabled = False` 。

針對非使用中的事件，如果您傳送 eventId 的獎勵或呼叫該 eventId 的 API，則會隱含地啟用學習 `activate` 。

## <a name="next-steps"></a>後續步驟

* 瞭解 [如何判斷獎勵分數以及應考慮的資料](concept-rewards.md)。
