---
title: 披露設計模式
titleSuffix: Azure Cognitive Services
description: 公開設計模式和最佳實踐。
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: 3e7d8ee2b156a30b11cda79798a8af8a8ecf4f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776617"
---
# <a name="disclosure-design-patterns"></a>公開設計模式
現在，您&#39;為您的合成語音體驗確定了正確的[披露級別](concepts-disclosure-guidelines.md#disclosure-assessment)，&#39;探索潛在設計模式的好時機。
## <a name="overview"></a>總覽
有一系列披露設計模式，您可以應用於您的合成語音體驗。 如果您的披露評估結果是"高度披露"，我們建議[**明確披露**](#explicit-disclosure)，這意味著直接傳達合成聲音的起源。 [**隱式披露**](#implicit-disclosure)包括提示和交互模式，無論所需的披露級別是高還是低，都有利於語音體驗。
![披露模式的範圍](media/responsible-ai/disclosure-patterns/affordances.png)






| 顯式披露模式                                                                                                                                                                                    | 隱式披露模式                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[透明介紹](#transparent-introduction)<br> [口頭透明介紹](#verbal-transparent-introduction)<br>  [顯式分行](#explicit-byline)<br>  [定制和校準](#customization-and-calibration)<br> [家長披露](#parental-disclosure)<br> [提供更多瞭解語音製作方式的機會](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [能力披露](#capability-disclosure)<br>[隱式提示和回饋](#implicit-cues--feedback)<br> [對話透明度](#conversational-transparency) |



使用下圖直接引用適用于合成語音的模式。 此圖表中的其他一些條件也可能適用于您的方案：<br/>



| 如果你的合成語音體驗... | 建議 | 設計模式 |
| --- | --- | --- |
| 要求高度披露  | 在前面至少使用一個顯式模式和隱式提示，以説明使用者構建關聯。 |[明確披露](#explicit-disclosure)<br>[隱式披露](#implicit-disclosure)  |
| 要求低披露 | 披露可能很少或沒有必要，但可能會受益于一些隱含的模式。 | [能力披露](#capability-disclosure)<br>[對話透明度](#conversational-transparency)  |
| 具有高度的參與性 | 長期構建，並提供多個進入點，以便使用者旅程中披露。 強烈建議您有入職體驗。 | [透明介紹](#transparent-introduction)<br>[定制和校準](#customization-and-calibration)<br>[能力披露](#capability-disclosure) |
| 將兒童作為主要目標受眾 | 將家長作為主要披露受眾，並確保他們能夠有效地向兒童傳達資訊公開。  | [家長披露](#parental-disclosure)<br>[口頭透明介紹](#verbal-transparent-introduction)<br> [隱式披露](#implicit-disclosure)<br> [對話透明度](#conversational-transparency)  |
| 包括盲人使用者或視力低下的人作為主要目標受眾  | 包容所有使用者，並確保任何形式的視覺披露具有相關的替代文本或聲音效果。 遵守對比度和顯示尺寸的協助工具標準。 使用聽覺提示傳達披露資訊。  | [口頭透明介紹](#verbal-transparent-introduction) <br>[聽覺提示](#implicit-cues--feedback)<br>[觸覺提示](#implicit-cues--feedback)<br>[對話透明度](#conversational-transparency)<br>[協助工具標準](https://www.microsoft.com/accessibility) |
| 是無螢幕、無設備還是使用語音作為主要或唯一的交互模式 | 使用聽覺提示傳達披露資訊。 | [口頭透明介紹](#verbal-transparent-introduction) <br> [聽覺提示](#implicit-cues--feedback)  |
| 可能包括多個使用者/傾聽者（例如，多個家庭的個人助理）  | 注意各種使用者背景和理解水準，並提供多種機會在使用者旅程中披露。  | [透明介紹（返回使用者）](#transparent-introduction)<br> [提供更多瞭解語音製作方式的機會](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [對話透明度](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>明確披露
如果您的合成語音體驗需要高度公開，最好使用以下至少一種顯式模式來清楚地說明合成特性。
### <a name="transparent-introduction"></a>透明介紹

在語音體驗開始之前，通過完全透明地介紹數位助手的語音來源及其功能。 使用此模式的最佳時刻是加入新使用者或向返回的使用者引入新功能時。 在介紹期間實現隱式提示可説明使用者形成有關數位代理合成性質的心理模型。

#### <a name="first-time-user-experience"></a>首次使用者體驗

![初次執行體驗期間的透明介紹](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*在輸入新使用者時引入了合成語音。*

建議
- 描述聲音是人造的（例如&quot;數位）&quot;
- 描述代理能夠執行的操作
- 顯式說明語音&#39;其來源
- 提供一個進入點，瞭解有關合成語音的更多

#### <a name="returning-user-experience"></a>返回使用者體驗

如果使用者跳超載入體驗，請繼續提供透明介紹體驗的進入點，直到使用者首次觸發語音。
<br/>

![返回使用者體驗期間的透明介紹](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*為合成語音體驗提供一致的切入點。允許使用者在使用者旅程中的任何時刻首次觸發語音時返回到載入體驗。*


### <a name="verbal-transparent-introduction"></a>口頭透明介紹

說明數位助理&#39;聲音起源的語音語音的語音語音本身非常明確，足以實現披露。 此模式最適合在語音是唯一可用的交互模式的高披露方案中。
<br/>

![口頭髮言的透明介紹](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*在使用者體驗中可能已介紹或屬性某人&#39;語音時，請使用透明介紹。*


![第一人稱口頭透明介紹](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*為了增加透明度，語音執行者可以在第一人稱中披露合成語音的來源。*

### <a name="explicit-byline"></a>顯式分行

如果使用者將與音訊播放機或互動式元件交互以觸發語音，則使用此模式。


![新聞媒體場景中的顯式分線](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*一個明確的旁線是聲音來自何處的歸屬。*

建議

- 提供進入點，瞭解有關合成語音的更多資訊

### <a name="customization-and-calibration"></a>定制和校準

為使用者提供數位助理如何回應他們（即語音聲音）。  當使用者按照自己的術語和特定目標與系統交互時，根據定義，他們已經明白&#39;它不是真人。

#### <a name="user-control"></a>使用者控制項

提供對合成語音體驗有有意義和顯著影響的選擇。

![使用者首選項](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*使用者首選項允許使用者自訂和改進其體驗。*

建議

- 允許使用者自訂語音（例如，選擇語言和語音類型）
- 為使用者提供一種教系統回應他/她獨特聲音（例如語音校準、自訂命令）的方法
- 優化使用者生成的或上下文交互（例如提醒）

#### <a name="persona-customization"></a>角色自訂

提供自訂數位助理&#39;語音的方法。 如果語音基於名人或廣為人知的人，請考慮在使用者預覽語音時同時使用視覺和語音介紹。

![語音自訂](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*提供從一組聲音中選擇的能力有助於傳達人工性質。*

建議
- 允許使用者預覽每個聲音的聲音
- 為每個語音使用真實的介紹
- 提供進入點，瞭解有關合成語音的更多資訊

### <a name="parental-disclosure"></a>家長披露

除了遵守 COPPA 法規外，如果您的主要受眾是幼兒，且您的曝光率較高，請向家長提供披露資訊。 對於敏感用途，請考慮在成人確認使用合成語音之前對體驗進行門控。 鼓勵家長向孩子傳達資訊。

![家長披露](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*針對父母的透明介紹可確保成人在孩子與聲音互動之前瞭解聲音的合成性。*

建議

- 將父母作為披露的主要受眾
- 鼓勵家長向子女傳達資訊
- 提供進入點，瞭解有關合成語音的更多資訊
- 通過向家長提出一個簡單的&quot;保障&quot;問題來證明他們已經閱讀了披露，從而將體驗蒙上大門

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>提供更多瞭解語音製作方式的機會

向頁面、快顯視窗或外部網站提供上下文相關的進入點，這些網站提供有關合成語音技術的詳細資訊。 例如，您可以在載入過程中或當使用者在對話過程中提示詳細資訊時顯示一個連結以瞭解更多資訊。

![入門點以瞭解更多資訊](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*進入點的示例，以提供瞭解有關合成語音的更多資訊的機會。*

一旦使用者要求有關合成語音的更多資訊，主要目標是教育他們有關合成語音的起源，並透明地瞭解該技術。

![為使用者提供有關合成語音的更多資訊](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*可以在外部網站説明網站中提供更多資訊。*

建議

- 簡化複雜概念，避免使用法律術語和技術術語
- 不要將此內容埋在隱私和使用條款聲明中
- 保持內容簡潔，並在可用時使用影像

## <a name="implicit-disclosure"></a>隱式披露

一致性是在整個使用者旅程中隱式實現披露的關鍵。 跨設備和交互模式一致地使用視覺和聽覺提示有助於在隱式模式和顯式披露之間建立關聯。

![隱式提示的一致性](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>隱含提示&回饋

擬人化可以以不同的方式表現出來，從代理的實際視覺表現，到聲音、聲音、光模式、彈跳形狀，甚至設備的振動。 定義角色時，利用隱含的提示和回饋模式，而不是瞄準一個非常人性化的化身。 這是儘量減少更明確披露需求的一種方式。

![視覺提示和回饋](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*這些線索有助於將代理擬化，而不會過於像人一樣。隨著時間的推移，當使用一致時，它們也可以自行成為有效的披露機制。*

在合併以下類型提示時，請考慮體驗的不同交互模式：

| 視覺提示                                                                                                                                                               | 聽覺提示                                                      | 觸覺提示 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  阿凡達 <br>回應式即時提示（例如動畫）<br> 非工具提示（例如，設備上的燈光和圖案）<br>  | 聲波（例如，一個簡短的獨特聲音，一系列音符） | 震動   |

### <a name="capability-disclosure"></a>能力披露

通過對數位助理的能力設定準確的期望，可以隱式地實現披露。 提供示例命令，以便使用者可以瞭解如何與數位助理交互，並提供上下文説明，以便在體驗的早期階段瞭解有關合成語音的更多內容。

![視覺提示和回饋](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>對話透明度

當對話處於意外路徑時，請考慮制定預設回應，以説明重置期望值、增強透明度並引導使用者走向成功路徑。 在談話中也有機會使用顯式披露。

![處理意外路徑](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
向座席提出的&quot;非&quot;任務或個人問題是提醒使用者代理的合成性並引導他們適當地參與或將他們重定向到真實人物的好時機。

![處理任務問題](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>何時披露

在整個使用者旅程中，有許多機會可供披露。 首次使用、第二次使用、第 n 次使用的設計...，但也包含&quot;未能&quot;突出顯示透明度的時刻，例如當系統出錯或使用者發現代理&#39;功能的限制時。

![在整個使用者旅程中披露機會](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

標準數位助理使用者旅程示例，突出各種披露機會。

### <a name="up-front"></a>前期

披露的最佳時機是一個人第一次與合成的聲音互動。在個人語音助理方案中，這將是在載入期間，或使用者第一次幾乎取消包裝體驗。 在其他方案中，可能是合成語音第一次讀取網站上的內容，或者使用者第一次與虛擬角色交互。

- [透明介紹](#transparent-introduction)
- [能力披露](#capability-disclosure)
- [定制和校準](#customization-and-calibration)
- [隱式提示](#implicit-cues--feedback)

### <a name="upon-request"></a>應要求

使用者應該能夠在需要時輕鬆訪問其他資訊、控制首選項和在使用者旅程中的任何時間接收透明通信。

- [提供更多瞭解語音製作方式的機會](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [定制和校準](#customization-and-calibration)
- [對話透明度](#conversational-transparency)

### <a name="continuously"></a>不斷

使用隱式設計模式，持續增強使用者體驗。

- [能力披露](#capability-disclosure)
- [隱式提示](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>當系統出現故障時

以披露為契機，優雅地失敗。

- [對話透明度](#conversational-transparency)
- [提供更多瞭解語音製作方式的機會](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [將對話遞交給人員處理](#conversational-transparency)



## <a name="additional-resources"></a>其他資源
- [微軟機器人指南](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Cortana 設計指南](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [微軟視窗UWP語音設計指南](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [微軟視窗混合現實語音指揮指南](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>參考文件

* [語音人才披露](https://aka.ms/disclosure-voice-talent)
* [負責部署合成語音技術的指導方針](concepts-guidelines-responsible-deployment-synthetic.md)
* [門控概述](concepts-gating-overview.md)
* [如何披露](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>後續步驟

* [語音人才披露](https://aka.ms/disclosure-voice-talent)
