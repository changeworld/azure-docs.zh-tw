---
title: 常見問題： SRE 和程式碼 |Microsoft Docs
titleSuffix: Azure
description: 常見問題：瞭解 SRE 與編碼之間的關聯性
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: b8865fdd53f4947b17a3621a128fc83f3d93d3e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90089066"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>常見問題：我是否需要知道如何撰寫程式碼以與 SRE 相關？

當個人考慮到與 SRE 相關的情況下，當個人考慮採用 sre 實務時，常見的問題就是「您需要知道如何撰寫程式碼？」

簡短答案：是。 

但完整的答案更差別細微。 讓我們來看看在網站可靠性工程中的程式碼撰寫的三個地方，以及每個程式碼撰寫專長的等級。 這份清單並不完整，但這些案例是一些較常見的使用案例。

## <a name="scenario-1-removing-toil-through-automation"></a>案例1：透過 automation 移除辛勞

網站可靠性工程師和使用 SRE 實務的其他人可能會盡可能移除辛勞。 「辛勞」表示 SRE 中的特定事物。 辛勞指的是具有特定特性的人所執行的作業。 辛勞沒有長期的可兌換價值。 不會以任何有意義的方式將服務往前推進。 辛勞通常是重複的，且大部分需要手動運作 (即使有可能自動化)。 當服務或系統隨著時間變大，對該系統的要求數量也可能按比例增加，並且需要更多的手動人力。

例如，如果服務需要 SRE 小組每週重設某項內容，或是手動布建新的帳戶和磁碟空間，或是以手動方式重新開機，這就是辛勞的操作負載。 完成這些動作，並沒有使服務以任何長期且持久的方式得到改善。 這些動作很可能必須反覆執行。

SRE 不喜歡辛勞。 其會在適當的情況下，盡可能將其消除。 這是自動化在 SRE 中發揮效用的地方之一。 如果這些要求可以自動處理，這樣就可以釋出團隊來處理更多的獎勵和具影響力事。

*編碼專業知識*：自動化需要一些編碼專業知識，但不需要完整的軟體工程技能。 如果您可以撰寫小型腳本 (可能是在 PowerShell 或 Bourne shell 中) 或即使您建立 [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app) 幾乎任何程式碼的，此應用程式仍可協助排除辛勞。

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>案例2：透過 Api/網域特定語言來控制 (Dsl) /templates

雖然 SRE 工作不是絕對必要，但能夠透過 Api、Dsl 和範本來控制環境 (特別是雲端環境) 允許 Sre 擴大其工作。 布建/解除布建基礎結構、設定監視，以及整合數個服務，都能透過程式碼更有效率。

*編碼專業知識*：如同先前的案例，這需要一些編碼專業知識，但不需要完整的軟體工程技能。 除了之前提到的腳本和邏輯應用程式之外， [https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) 也可以搭配最少量的程式碼撰寫體驗使用。

## <a name="scenario-3-fixing-the-code"></a>案例3：修正程式碼

網站可靠性工程師會尋找改善系統的可靠性。 這個目標有時候需要深入探討至系統的原始程式碼、判斷問題，並經常將修正程式傳回程序代碼基底。 雖然這項工作的複雜度層級可能會根據情況而有很大的差異，但在這些情況下，編碼專業知識是明確的需求。

*編碼專業知識*：在此案例中，通常需要完整的軟體工程專業知識。


## <a name="next-steps"></a>後續步驟

有興趣深入瞭解網站可靠性工程和低程式碼的運作方式嗎？ 請參閱我們的「 [網站可靠性工程中樞](../index.yml)」，上面連結的產品檔。
