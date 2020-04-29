---
title: 綜合語音技術的責任部署指導方針
titleSuffix: Azure Cognitive Services
description: Microsoft 對於使用綜合語音技術的一般設計方針。 這些是在 Microsoft 與語音 disorders 共同進行的研究中開發，以引導綜合語音的負責開發人員。
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73836772"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>綜合語音技術的責任部署指導方針
以下是使用綜合語音技術的 Microsoft 一般設計方針。 這些是在 Microsoft 與配音人員、取用者以及具有語音 disorders 的個人進行的研究中開發，以引導綜合語音的負責開發。

## <a name="general-considerations"></a>一般考量
針對綜合語音技術的部署，下列指導方針適用于大部分的案例。

### <a name="disclose-when-the-voice-is-synthetic"></a>在語音已綜合時公開
公開語音是電腦產生的不只是將有害結果從欺騙的風險降到最低，也會增加組織提供語音的信任。 深入瞭解[如何公開](concepts-disclosure-guidelines.md)。

### <a name="select-appropriate-voice-types-for-your-scenario"></a>針對您的案例選取適當的語音類型
請仔細考慮使用的內容，以及與使用綜合語音相關聯的潛在損害。 例如，高精確度的綜合語音可能不適用於高風險的情況，例如個人訊息處理、財務交易，或需要人工適應性或理解的複雜情況。 對於語音類型，使用者可能也會有不同的預期。 例如，接聽綜合語音所讀取的機密新聞時，有些使用者偏好更共鳴且人類類似的新聞閱讀，而有些則偏好更永垂不朽、非偏誤的聲音。 請考慮測試您的應用程式，以進一步瞭解使用者喜好設定。

### <a name="be-transparent-about-capabilities-and-limitations"></a>對於功能和限制是透明的
在與高精確度綜合語音代理程式互動時，使用者較可能會有更高的期望。 因此，當系統功能不符合這些預期時，信任可能會受到影響，而且可能會導致不愉快或甚至有害的體驗。

### <a name="provide-optional-human-support"></a>提供選擇性的人工支援
在不明確的交易案例中（例如，呼叫支援中心），使用者不一定會信任電腦代理程式來適當地回應其要求。 無論系統的語音或功能的實際品質為何，在這些情況下都可能需要人力支援。

## <a name="considerations-for-voice-talent"></a>配音人才的考慮
使用語音配音員（例如語音執行者）來建立綜合語音時，適用下列指導方針。

### <a name="obtain-meaningful-consent-from-voice-talent"></a>向配音人才取得有意義的同意
配音員希望能夠控制其聲音音調（使用方式和位置），並在使用時隨時補償。 因此，系統擁有者應該會向配音人員取得明確的書面許可權，並針對使用案例、使用期間、補償等等提供清楚的合約規格。 有些配音員並不知道可能的惡意使用技術，而且應該由系統擁有者對技術的功能進行教育。 如需有關配音員和同意的詳細資訊，請閱讀我們[的配音人員洩漏](https://aka.ms/disclosure-voice-talent)。


## <a name="considerations-for-those-with-speech-disorders"></a>使用語音 disorders 的考慮
搭配語音 disorders 使用個人時，若要建立或部署綜合語音技術，請遵守下列指導方針。

### <a name="provide-guidelines-to-establish-contracts"></a>提供建立合約的指導方針
提供建立合約的指導方針，讓使用綜合語音以取得協助的人員說話。 合約應考慮指定擁有語音的合作物件、使用期間、擁有權轉移準則、刪除聲音字型的程式，以及如何防止未經授權的存取。 此外，如果該人員已授與家庭成員的許可，請在停止後將語音字型擁有權傳送給家人。

### <a name="account-for-inconsistencies-in-speech-patterns"></a>考慮語音模式中的不一致
針對具有語音 disorders 的個人錄製自己的聲音字型，語音模式的不一致（slurring 或無法發音特定字組）可能會使錄製程式變得複雜。 在這些情況下，綜合語音技術和錄製會話應可配合（也就是，提供中斷和額外的錄製會話數）。

### <a name="allow-modification-over-time"></a>允許在一段時間後修改
具有語音 disorders 的個人想要對其綜合語音進行更新，以反映過時的情況（例如，子系到達 puberty）。 個人可能也會有隨時間變更的樣式喜好設定，而且可能會想要變更音調、強調或其他語音特性。


## <a name="reference-docs"></a>參考文件

* [對配音人才的洩漏](https://aka.ms/disclosure-voice-talent)
* [管制總覽](concepts-gating-overview.md)
* [如何公開](concepts-disclosure-guidelines.md)
* [公開設計模式](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>後續步驟

* [對配音人才的洩漏](https://aka.ms/disclosure-voice-talent)
* [如何公開](concepts-disclosure-guidelines.md)
* [公開設計模式](concepts-disclosure-patterns.md)
