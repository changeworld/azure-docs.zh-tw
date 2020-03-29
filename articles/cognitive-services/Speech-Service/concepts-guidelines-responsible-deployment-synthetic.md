---
title: 負責部署合成語音技術的指導方針
titleSuffix: Azure Cognitive Services
description: 微軟使用合成語音技術的通用設計指南。 這些是微軟對語音人才、消費者以及有語音障礙的個人進行的研究開發的，以指導合成語音的負責任開發。
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836772"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>負責部署合成語音技術的指導方針
以下是微軟使用合成語音技術的一般設計指南。 這些是微軟對語音人才、消費者以及有語音障礙的個人進行的研究開發的，以指導合成語音的負責任發展。

## <a name="general-considerations"></a>一般考量
對於合成語音技術的部署，以下準則適用于大多數方案。

### <a name="disclose-when-the-voice-is-synthetic"></a>語音合成時披露
披露語音是電腦生成的，不僅可以將欺騙導致有害結果的風險降至最低，而且還會增加對提供語音的組織的信任。 詳細瞭解[如何披露](concepts-disclosure-guidelines.md)。

### <a name="select-appropriate-voice-types-for-your-scenario"></a>為您的方案選擇適當的語音類型
仔細考慮使用環境以及與使用合成語音相關的潛在危害。 例如，高保真合成聲音在高風險情況下可能並不合適，例如個人消息傳送、金融交易或需要人類適應性或同理心的複雜情況。 使用者也可能對語音類型有不同的期望。 例如，當聽一個合成的聲音閱讀敏感新聞時，一些使用者更喜歡更同情和人性化的新聞閱讀，而另一些使用者則更喜歡單調、公正的聲音。 請考慮測試應用程式以更好地瞭解使用者首選項。

### <a name="be-transparent-about-capabilities-and-limitations"></a>對功能和限制保持透明
當與高保真合成語音代理交互時，使用者更有可能有更高的期望。 因此，當系統功能無法滿足這些期望時，信任可能會受到影響，並可能導致不愉快甚至有害的體驗。

### <a name="provide-optional-human-support"></a>提供可選的人工支援
在不明確的事務性方案中（例如，呼叫支援中心），使用者並不總是信任電腦代理來適當回應其請求。 在這些情況下，無論系統的聲音或能力的真實品質如何，可能都有必要提供人工支援。

## <a name="considerations-for-voice-talent"></a>語音人才的注意事項
當與語音天才（如語音演員）合作創建合成語音時，以下準則適用。

### <a name="obtain-meaningful-consent-from-voice-talent"></a>獲得語音人才的有意義的同意
語音人才希望控制其語音字體（使用方式和位置），並在使用時獲得補償。 因此，系統擁有者應獲得語音人才的明確書面許可，並在使用案例、使用期限、補償等方面有明確的合同規範。 一些語音人才不知道該技術的潛在惡意使用，系統擁有者應該對技術的能力進行教育。 有關語音天賦和同意的更多內容，請閱讀我們的[語音人才披露](https://aka.ms/disclosure-voice-talent)。


## <a name="considerations-for-those-with-speech-disorders"></a>言語障礙患者的注意事項
當與有言語障礙的個人合作時，創建或部署合成語音技術時，適用以下準則。

### <a name="provide-guidelines-to-establish-contracts"></a>提供建立合同的指導原則
提供與使用合成語音協助說話的個人簽訂合同的準則。 合同應考慮指定擁有語音的各方、使用期限、擁有權轉移標準、刪除語音字體的程式以及如何防止未經授權的訪問。 此外，如果家庭成員已給予許可，則允許在死後將語音字體擁有權的合同轉讓給家庭成員。

### <a name="account-for-inconsistencies-in-speech-patterns"></a>考慮語音模式的不一致
對於有語音障礙的人，他們記錄自己的語音字體，他們的語音模式的不一致（含糊不清或無法發音某些單詞）可能會使錄製過程複雜化。 在這些情況下，合成語音技術和錄製會話應適應它們（即提供中斷和額外數量的錄製會話）。

### <a name="allow-modification-over-time"></a>允許隨時間而修改
有言語障礙的人希望更新他們的合成聲音，以反映衰老（例如，孩子進入青春期）。 個人也可能具有隨時間變化的樣式偏好，並且可能希望更改音調、口音或其他語音特徵。


## <a name="reference-docs"></a>參考文件

* [語音人才披露](https://aka.ms/disclosure-voice-talent)
* [門控概述](concepts-gating-overview.md)
* [如何披露](concepts-disclosure-guidelines.md)
* [披露設計模式](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>後續步驟

* [語音人才披露](https://aka.ms/disclosure-voice-talent)
* [如何披露](concepts-disclosure-guidelines.md)
* [披露設計模式](concepts-disclosure-patterns.md)
