---
title: 常見問題： SRE 和編碼 |Microsoft Docs
titleSuffix: Azure
description: 常見問題：瞭解 SRE 和程式碼之間的關聯性
author: dnblankedelman
manager: efreeman
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 06/19/2020
ms.author: dnb
ms.openlocfilehash: 7003844878fcd23b20957c393ac2ed011f5f5946
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85834915"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>常見問題：我需要知道如何撰寫程式碼才能與 SRE 相關嗎？

當個人考慮在 SRE 和團隊中納入 SRE 實務的考慮時，常見的問題是「您需要知道如何撰寫程式碼？」

簡短回答：是。 

但完整的答案有點差別細微。 讓我們來看一下在網站可靠性工程中，撰寫程式碼的三個地方，以及各自所需的編碼專業知識層級。 這份清單不完整，但這些案例是一些較常見的使用案例。

## <a name="scenario-1-removing-toil-through-automation"></a>案例1：透過自動化移除辛勞

網站可靠性工程師和使用 SRE 實務的其他人可以在任何可能的情況下移除辛勞。 「辛勞」表示 SRE 中的特定事物。 辛勞是指具有特定特性的人所執行的作業。 辛勞沒有長期的可兌換價值。 不會以任何有意義的方式將服務往前推進。 辛勞通常是重複的，且大部分需要手動運作 (即使有可能自動化)。 當服務或系統隨著時間變大，對該系統的要求數量也可能按比例增加，並且需要更多的手動人力。

例如，如果服務要求 SRE 小組每週重設某個專案，或手動布建新的帳戶和磁碟空間，或手動重新開機它，這就是辛勞的操作負載。 完成這些動作，並沒有使服務以任何長期且持久的方式得到改善。 這些動作很可能必須反覆執行。

SRE 不喜歡辛勞。 其會在適當的情況下，盡可能將其消除。 這是自動化在 SRE 中發揮效用的地方之一。 如果這些要求可以自動處理，則會釋出小組以因應更多的獎勵和影響力事項。

*編碼專業知識*：自動化需要一些編碼專業知識，但不需要完整的軟體工程技能。 如果您可以撰寫小型腳本（也許是在 PowerShell 或 Bourne shell 中），或即使只是建立 [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app) 幾乎任何程式碼，此應用程式仍可協助消除辛勞。

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>案例2：透過 Api/網域特定語言（Dsl）/templates 的控制

雖然 SRE 工作不是絕對必要，但是能夠透過 Api、Dsl 和範本（尤其是雲端環境）控制環境，讓 Sre 能夠相應增加其工作。 布建/解除布建基礎結構、設定監視，以及整合數個服務，都能透過編碼更有效率。

*編碼專業知識*：如同前一個案例，這需要一些編碼專業知識，但不需要完整的軟體工程技能。 除了之前提到的腳本和邏輯應用程式之外， [https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) 也可以搭配最少的程式碼撰寫經驗來使用。

## <a name="scenario-3-fixing-the-code"></a>案例3：修正程式碼

網站可靠性工程師會尋求改善系統的可靠性。 此目標有時需要深入探討到系統的原始程式碼、判斷問題，而且通常會對程式碼基底造成修正。 雖然這項工作的複雜程度可能會根據情況而有很大的差異，但在這些情況下，撰寫程式碼的專業能力也是明確的需求。

*編碼專業知識*：在此案例中，通常需要完整的軟體工程專長。


## <a name="next-steps"></a>後續步驟

有興趣深入瞭解網站可靠性工程和低程式碼工作嗎？ 查看我們的[網站可靠性工程中樞](../index.yml)，這是上面所連結的產品檔集。
