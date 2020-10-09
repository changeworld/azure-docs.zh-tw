---
title: 綜合語音技術的負責任部署指導方針
titleSuffix: Azure Cognitive Services
description: Microsoft 針對使用綜合語音技術的一般設計指導方針。 這些都是由 Microsoft 與配音員、消費者及語音失常的個人所開發，以引導綜合語音的負責任開發。
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "73836772"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>綜合語音技術的負責任部署指導方針
以下是使用綜合語音技術的 Microsoft 一般設計指導方針。 這些是由 Microsoft 與配音員、取用者以及具有語音失常的個人所開發，以引導綜合語音的負責任開發。

## <a name="general-considerations"></a>一般考量
針對綜合語音技術的部署，下列指導方針適用于大部分的案例。

### <a name="disclose-when-the-voice-is-synthetic"></a>當語音為綜合時洩漏
洩漏語音是電腦產生的，不僅能將有害結果的風險降至最低，還會增加組織提供語音的信任。 深入瞭解 [如何公開](concepts-disclosure-guidelines.md)。

### <a name="select-appropriate-voice-types-for-your-scenario"></a>為您的案例選取適當的語音類型
請仔細考慮使用的內容，以及與使用綜合聲音相關聯的潛在損害。 例如，高精確度的綜合語音可能不適合高風險的案例，例如個人訊息、金融交易或需要人類適應性或理解的複雜情況。 使用者也可能對語音類型有不同的期望。 例如，當您接聽由綜合語音所讀取的機密新聞時，有些使用者偏好使用更 empathetic 且人類喜歡的新聞閱讀，有些則偏好較永垂不朽、非偏誤的聲音。 請考慮測試您的應用程式，以進一步瞭解使用者喜好設定。

### <a name="be-transparent-about-capabilities-and-limitations"></a>對功能和限制是透明的
使用高精確度的綜合語音代理程式進行互動時，使用者比較有可能具有較高的期望。 因此，當系統功能不符合這些預期時，信任可能會受到影響，而且可能會導致不愉快或甚至有害的體驗。

### <a name="provide-optional-human-support"></a>提供選擇性的人為支援
在不明確的交易案例中 (例如，呼叫支援中心) ，使用者不一定會信任電腦代理程式來適當地回應其要求。 在這些情況下，不論系統的聲音或功能的實際品質為何，都可能需要人力支援。

## <a name="considerations-for-voice-talent"></a>配音員的考慮
使用配音員（例如語音執行者）來建立綜合語音時，適用下列指導方針。

### <a name="obtain-meaningful-consent-from-voice-talent"></a>從配音員取得有意義的同意
配音員期望能夠控制其聲音音調 (使用方式和位置) 並在使用時補償。 因此系統擁有者應該從配音員取得明確的書面許可，並且在使用案例、使用持續時間、補償等上具有明確的合約規格。 某些配音員不知道這項技術的潛在惡意用途，應該由系統擁有者對技術的功能進行教育。 如需有關配音員和同意的詳細資訊，請閱讀我們 [對配音員的洩漏](https://aka.ms/disclosure-voice-talent)。


## <a name="considerations-for-those-with-speech-disorders"></a>具有語音失常的考慮
使用語音失常來建立或部署綜合語音技術時，適用下列指導方針。

### <a name="provide-guidelines-to-establish-contracts"></a>提供建立合約的指導方針
提供建立合約的指導方針，讓使用綜合語音協助的人員說話。 合約應考慮指定擁有語音、使用期間、擁有權轉移準則的合作物件、刪除語音字型的程式，以及如何防止未經授權的存取。 此外，如果該人員具有指定的許可權，請在使用者的許可下，啟用將聲音字型擁有權的合約轉讓給家庭成員。

### <a name="account-for-inconsistencies-in-speech-patterns"></a>考慮語音模式中的不一致
適用于語音失常的個人錄製自己的聲音字型、語音模式中的不一致 (slurring 或無法發音某些單字) 可能會使錄製程式變得複雜。 在這些情況下，綜合語音技術和錄製會話應 (，也就是在) 提供中斷和更多的錄製會話。

### <a name="allow-modification-over-time"></a>允許一段時間內的修改
具有語音失常的個人希望更新其綜合聲音，以反映過時的 (例如，puberty) 的子系。 個人也可能會有隨時間改變的樣式喜好設定，而且可能會想要變更音調、輔色或其他聲音特性。


## <a name="reference-docs"></a>參考文件

* [配音員的洩漏](https://aka.ms/disclosure-voice-talent)
* [管制總覽](concepts-gating-overview.md)
* [如何公開](concepts-disclosure-guidelines.md)
* [洩漏設計模式](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>後續步驟

* [配音員的洩漏](https://aka.ms/disclosure-voice-talent)
* [如何公開](concepts-disclosure-guidelines.md)
* [洩漏設計模式](concepts-disclosure-patterns.md)
