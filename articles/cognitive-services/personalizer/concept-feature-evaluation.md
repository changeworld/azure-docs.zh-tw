---
title: 功能評估-個人化工具
titleSuffix: Azure Cognitive Services
description: 當您從 Azure 入口網站執行個人化工具資源中的評估時，個人化工具會提供內容和動作哪些功能影響模型的相關資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31243b5e9da55aafbc376fa416c1b00a4499c116
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "70242420"
---
# <a name="feature-evaluation"></a>功能評估

當您從[Azure 入口網站](https://portal.azure.com)執行個人化工具資源中的評估時，個人化工具會提供內容和動作哪些功能影響模型的相關資訊。 

這適用于：

* 想像一下您可以使用的其他功能，讓模型中的哪些功能更重要。
* 查看哪些功能並不重要，而且可能會移除它們，或進一步分析可能會影響使用量的專案。
* 為編輯或鑒藏團隊提供有關新內容或需要帶入目錄之產品的指引。
* 針對傳送功能至個人化工具時所發生的常見問題和錯誤進行疑難排解。

較重要的功能在模型中具有更強的權數。 因為這些功能的權數較強，所以當個人化工具取得更高的報酬時，它們通常會出現。

## <a name="getting-feature-importance-evaluation"></a>取得功能重要性評估

若要查看功能重要性的結果，您必須執行評估。 此評估會根據評估期間觀察到的功能名稱，建立人類可讀的功能標籤。

功能重要性的產生資訊代表目前的個人化工具 online 模型。 此評估會分析在評估期間結束日期之後所儲存之模型的功能重要性，並使用目前的線上學習原則在評估期間完成所有訓練。 

功能重要性結果並不代表在評估期間測試或建立的其他原則和模型。  評估期間將不會包含在評估期結束後傳送至個人化工具的功能。

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>如何解讀功能重要性評估

個人化工具會建立具有類似重要性的「群組」功能，以評估功能。 一個群組可以說是具有比其他專案更強的重要性，但是在群組中，功能的順序是依字母順序排列。

每項功能的相關資訊包括：

* 此功能是否來自內容或動作。
* 功能索引鍵和值。

例如，霜淇淋商店訂購應用程式可能會看到「CoNtext. 氣象：熱」，這是非常重要的功能。

個人化工具會顯示功能的相互關聯，並將其納入考慮，以產生更高的報酬。

例如，您可能會看到「IceCream」，以及「CoNtext：*具有**動作的*「內容」。 menuitem： WarmTea：

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>您可以根據功能評估採取的動作

### <a name="imagine-additional-features-you-could-use"></a>想像您可以使用的其他功能

從模型中較重要的功能取得靈感。 例如，如果您在影片行動裝置應用程式中看到「MobileBattery： Low」，您可能會認為連線類型也會讓客戶選擇查看另一個影片剪輯，然後將連線類型和頻寬的功能新增至您的應用程式。

### <a name="see-what-features-are-not-important"></a>查看哪些功能不重要

可能會移除不重要的功能，或進一步分析可能影響使用的內容。 基於許多原因，功能可能會排名低。 其中一個原因可能是真的功能不會影響使用者行為。 但它也可能表示該功能對使用者而言並不明顯。 

例如，影片網站可能會看到「Action. VideoResolution = 4k」是低重要性的功能，彼此矛盾使用者研究。 原因可能是應用程式甚至不會提及或顯示影片解析度，因此使用者不會根據它來變更其行為。

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>提供適用于編輯或鑒藏小組的指引

提供有關新內容或值得帶入目錄之產品的指引。 個人化工具是設計成可增強人為深入解析和小組的工具。 其中一種方式是將資訊提供給編輯群組，以瞭解產品、文章或驅動行為的內容。 例如，影片應用程式案例可能會顯示有一個稱為「Action. VideoEntities： true」的重要功能，並提示編輯小組帶入更多的貓影片。

### <a name="troubleshoot-common-problems-and-mistakes"></a>針對常見的問題和錯誤進行疑難排解

您可以藉由變更應用程式代碼來修正常見的問題和錯誤，使其不會傳送不適當或格式不正確的功能給個人化工具。 

傳送功能時的常見錯誤包括下列各項：

* 傳送個人識別資訊（PII）。 某個人專用的 PII （例如名稱、電話號碼、信用卡號碼、IP 位址）不應該與個人化工具搭配使用。 如果您的應用程式需要追蹤使用者，請使用未識別的 UUID 或其他的 UserID 號碼。 在大部分情況下，這也會造成問題。
* 有大量的使用者時，每個使用者的互動不太可能會比所有擴展的互動來衡量，因此傳送使用者識別碼（即使是非 PII）可能會增加比模型值更多的干擾。
* 以精確時間戳記的形式傳送日期時間欄位，而不是特徵化時間值。 具有如 CoNtext 之類的功能：日期 = 星期一或 "CoNtext. 時間戳記. 小時" = "13" 會更有用。 每個最多會有7或24個功能值。 但是 "CoNtext. TimeStamp"： "1985-04-12T23：20： 50.52 Z" 很精確，因為它不會再次發生，所以沒有辦法從中學習。

## <a name="next-steps"></a>後續步驟

瞭解個人化工具的擴充[性和效能](concepts-scalability-performance.md)。

