---
title: 功能評估-個人化工具
titleSuffix: Azure Cognitive Services
description: 當您從 Azure 入口網站在個人化工具資源中執行評估時，個人化工具會提供有關內容和動作有哪些功能影響模型的資訊。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: c0e47a2943cf8c934d201f76aefc41868adf0b25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87127718"
---
# <a name="feature-evaluation"></a>功能評估

當您從 [Azure 入口網站](https://portal.azure.com)在個人化工具資源中執行評估時，個人化工具會提供有關內容和動作有哪些功能影響模型的資訊。 

這在下列情況下很有用：

* 想像您可以使用的其他功能，讓模型中更重要的功能獲得靈感。
* 查看哪些功能不重要，並可能移除它們，或進一步分析可能會影響使用方式的內容。
* 提供編輯或鑒藏小組的指引，以瞭解如何將新內容或產品帶入目錄。
* 針對將功能傳送至個人化工具時所發生的常見問題和錯誤進行疑難排解。

更重要的功能在模型中具有較強的加權。 因為這些功能的權數較高，所以在個人化工具取得更高的獎勵時，它們通常會存在。

## <a name="getting-feature-importance-evaluation"></a>取得功能重要性評估

若要查看功能重要性的結果，您必須執行評估。 評估會根據評估期間所觀察到的功能名稱，建立人們可讀取的功能標籤。

有關特徵重要性的結果資訊代表目前的個人化工具 online 模型。 評估會分析在評估期間結束日期所儲存之模型的功能重要性，並在評估期間使用目前的線上學習原則完成所有定型。 

功能重要性結果不代表評估期間測試或建立的其他原則和模型。  評估期結束後，將不會包含傳送至個人化工具的功能。

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>如何解讀功能重要性評估

個人化工具會藉由建立具有類似重要性之功能的「群組」來評估功能。 您可以說一個群組的重要性比其他群組更強，但在群組中，功能的順序是依字母順序排列。

每項功能的相關資訊包括：

* 功能是否來自內容或動作。
* 功能索引鍵和值。

例如，霜淇淋商店訂購應用程式可能會看到「內容氣象：熱」是一項非常重要的功能。

個人化工具會顯示功能的相互關聯，並將其納入考慮，以產生更高的獎勵。

例如，您可能會看到「內容。天氣：作用中 *的* 動作： Menuitem： IceCream」，以及「內容。天氣：冷 *且有* 動作。 menuitem： WarmTea：

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>您可以根據功能評估採取的動作

### <a name="imagine-additional-features-you-could-use"></a>想像您可以使用的其他功能

從模型中更重要的功能取得靈感。 例如，如果您在影片行動裝置應用程式中看到 "CoNtext. MobileBattery： Low"，您可能會認為連線類型可能也會讓客戶選擇查看另一個影片剪輯，然後將連線類型和頻寬的相關功能新增至您的應用程式。

### <a name="see-what-features-are-not-important"></a>瞭解哪些功能不重要

可能會移除不重要的功能，或進一步分析可能會影響使用的內容。 基於許多原因，功能的等級可能很低。 這可能是因為真的功能不會影響使用者的行為。 但是，這也表示該功能對使用者而言並不明顯。 

例如，影片網站可能會看到「VideoResolution = 4k」是低重要性的功能，彼此矛盾使用者研究。 原因可能是應用程式甚至沒有提及或顯示影片解析度，所以使用者不會根據它來變更其行為。

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>提供編輯或鑒藏團隊的指引

提供有關新內容或產品進入目錄的指引。 個人化工具的設計是一種工具，可增強人類的深入解析和團隊。 其中一種方式是將資訊提供給編輯群組，以瞭解產品、文章或驅動行為的內容。 例如，影片應用程式案例可能會顯示一項重要功能，稱為「VideoEntities .Cat： true」，提示編輯小組導入更多貓影片。

### <a name="troubleshoot-common-problems-and-mistakes"></a>針對常見的問題和錯誤進行疑難排解

您可以藉由變更應用程式的程式碼來修正常見的問題和錯誤，使其不會將不當或格式不正確的功能傳送至個人化工具。 

傳送功能時常見的錯誤包括下列各項：

*  (PII) 傳送個人識別資訊。 一個個別 (的 PII，例如名稱、電話號碼、信用卡號碼、IP 位址) 不應與個人化工具搭配使用。 如果您的應用程式需要追蹤使用者，請使用未識別的 UUID 或其他的使用者識別碼號碼。 在大部分的情況下，這也有問題。
* 如果有大量的使用者，則每個使用者的互動都不太可能會比所有人口的互動來衡量， (因此即使非 PII) 可能會新增更多的雜訊，而不是模型的值。
* 將日期時間欄位以精確時間戳記來傳送，而不是特徵化時間值。 具有例如 CoNtext. Day = Monday 或 "CoNtext. Hour" = "13" 的功能會更有用。 每個都有最多7或24個功能值。 但是 "CoNtext. TimeStamp"： "1985-04-12T23：20： 50.52 Z" 是很精確的，因為它永遠不會發生，所以無法從中學習。

## <a name="next-steps"></a>接下來的步驟

瞭解個人化工具的調整 [能力和效能](concepts-scalability-performance.md) 。

