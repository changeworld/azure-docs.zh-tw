---
title: 洩漏設計模式
titleSuffix: Azure Cognitive Services
description: 公開的設計模式和最佳作法。
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: fe4f0cdcdb863582560b0eba3a22e95b93ff858b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150936"
---
# <a name="disclosure-design-patterns"></a>公開設計模式
現在&#39;您已為您的綜合語音體驗決定適當 [的洩漏層級](concepts-disclosure-guidelines.md#disclosure-assessment) ，&#39;s 是探索潛在設計模式的好時機。
## <a name="overview"></a>概觀
您可以將各種公開的設計模式套用至您的綜合語音體驗。 如果您的洩漏評量結果為「高洩漏」，建議您 [**明確洩漏**](#explicit-disclosure)，這表示會直接傳達綜合語音的來源。 [**隱含洩漏**](#implicit-disclosure) 包含提示和互動模式，可讓您在需要的洩漏層級為高或低時，受益于語音體驗。
![洩漏模式系列](media/responsible-ai/disclosure-patterns/affordances.png)






| 明確的洩漏模式                                                                                                                                                                                    | 隱含洩漏模式                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[透明簡介](#transparent-introduction)<br> [口頭透明簡介](#verbal-transparent-introduction)<br>  [明確署名](#explicit-byline)<br>  [自訂和校正](#customization-and-calibration)<br> [家長洩漏](#parental-disclosure)<br> [提供機會來瞭解如何進行語音](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [功能洩漏](#capability-disclosure)<br>[隱含提示和意見反應](#implicit-cues--feedback)<br> [對話式透明度](#conversational-transparency) |



使用下圖來直接參考適用于綜合聲音的模式。 此圖表中的某些其他條件也可能適用于您的案例：<br/>



| 如果您的綜合語音體驗 .。。 | 建議 | 設計模式 |
| --- | --- | --- |
| 需要高度洩漏  | 請事先使用至少一個明確模式和隱含提示，以協助使用者建立關聯。 |[明確洩漏](#explicit-disclosure)<br>[隱含洩漏](#implicit-disclosure)  |
| 需要低洩漏 | 洩漏可能很短或不必要，但可受益于某些隱含模式。 | [功能洩漏](#capability-disclosure)<br>[對話式透明度](#conversational-transparency)  |
| 具有高層級的互動 | 長期打造，並提供多個進入點以在使用者旅程圖中洩漏。 強烈建議您在上架體驗。 | [透明簡介](#transparent-introduction)<br>[自訂和校正](#customization-and-calibration)<br>[功能洩漏](#capability-disclosure) |
| 包含子系作為主要目標物件 | 將父系的目標設為主要的洩漏物件，並確保它們可以有效地與兒童洩漏。  | [家長洩漏](#parental-disclosure)<br>[口頭透明簡介](#verbal-transparent-introduction)<br> [隱含洩漏](#implicit-disclosure)<br> [對話式透明度](#conversational-transparency)  |
| 包含盲人使用者或視力為主要目標物件的人  | 包含所有使用者，並確保任何形式的視覺洩漏都有相關聯的替代文字或音效效果。 遵循協助工具標準的對比比例和顯示大小。 使用聽覺提示來傳達洩漏。  | [口頭透明簡介](#verbal-transparent-introduction) <br>[聽覺提示](#implicit-cues--feedback)<br>[Haptic 提示](#implicit-cues--feedback)<br>[對話式透明度](#conversational-transparency)<br>[協助工具標準](https://www.microsoft.com/accessibility) |
| 無螢幕、無裝置，或使用語音作為主要或唯一的互動模式 | 使用聽覺提示來傳達洩漏。 | [口頭透明簡介](#verbal-transparent-introduction) <br> [聽覺提示](#implicit-cues--feedback)  |
| 可能包含多個使用者/接聽程式 (例如，在多個家庭中的個人助理)   | 請留意各種使用者內容和瞭解程度，並提供在使用者旅程圖中洩漏的多個機會。  | [ (傳回使用者) 的透明簡介 ](#transparent-introduction)<br> [提供機會來瞭解如何進行語音](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [對話式透明度](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>明確洩漏
如果您的綜合語音體驗需要高度洩漏，最好至少使用下列其中一個明確模式來清楚陳述綜合本質。
### <a name="transparent-introduction"></a>透明簡介

開始使用語音體驗之前，請先透過完全透明的方式來介紹其聲音及其功能的來源。 使用此模式的最佳時機是在登入新使用者時，或為傳回的使用者推出新功能時。 在簡介期間執行隱含提示，可協助使用者形成有關數位代理程式綜合本質的精神模型。

#### <a name="first-time-user-experience"></a>首次使用者體驗

![首次執行體驗期間的透明簡介](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*當新使用者上線時，就會引進綜合語音。*

建議
- 描述語音是人工 (例如 &quot; 數位 &quot;) 
- 描述代理程式能夠執行的工作
- 明確陳述語音&#39;的來源
- 提供可深入瞭解綜合語音的進入點

#### <a name="returning-user-experience"></a>返回使用者體驗

如果使用者略過上線體驗，請在使用者第一次觸發語音之前，繼續提供透明簡介體驗的進入點。
<br/>

![返回使用者體驗期間的透明簡介](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*提供綜合語音體驗的一致進入點。允許使用者在使用者旅程圖中的任何時間點首次觸發語音時，返回上線體驗。*


### <a name="verbal-transparent-introduction"></a>口頭透明簡介

指出數位助理&#39;s 語音的原始提示會明確地足以達成洩漏。 此模式最適用于高洩漏案例，其中語音是唯一可用的互動模式。
<br/>

![Verbally 朗讀的透明簡介](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*當使用者經驗中有一些時間，而您可能已在這段時間內&#39;的語音引進或屬性時，請使用透明的簡介。*


![第一個人的 Verbally 朗讀透明簡介](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*針對其他透明度，語音動作專案可以在第一個人公開綜合聲音的來源。*

### <a name="explicit-byline"></a>明確署名

如果使用者將與音訊播放機或互動式元件互動以觸發語音，請使用此模式。


![新聞媒體案例中的明確署名](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*明確的署名是語音來源的屬性。*

建議

- 深入瞭解合成語音的供應專案點

### <a name="customization-and-calibration"></a>自訂和校正

讓使用者可以控制數字助理如何回應 (例如，語音音效) 的方式。  當使用者以自己的詞彙與系統互動，並以特定的目標為考慮時，根據定義，他們已經瞭解它&#39;不是真正的人。

#### <a name="user-control"></a>使用者控制項

提供對綜合語音體驗有意義且明顯影響的供應專案選擇。

![使用者喜好設定](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*使用者喜好設定可讓使用者自訂和改善其體驗。*

建議

- 允許使用者自訂語音 (例如，選取語言和語音類型) 
- 為使用者提供一種方法來讓系統回應其獨特的語音 (例如語音校正、自訂命令) 
- 針對使用者產生或內容相關的互動進行優化 (例如提醒) 

#### <a name="persona-customization"></a>角色自訂

提供自訂數位助理&#39;語音的方法。 如果語音是以名人或廣泛辨識的人為基礎，當使用者預覽語音時，請考慮使用視覺和語音簡介。

![語音自訂](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*提供從一組語音中選取的功能，可協助傳達人工本質。*

建議
- 允許使用者預覽每個語音的音效
- 使用每個語音的真實簡介
- 提供可深入瞭解合成語音的進入點

### <a name="parental-disclosure"></a>家長洩漏

除了遵守 COPPA 法規之外，如果您的主要目標物件是年輕兒童，而您的曝光層級很高，請提供家長的洩漏。 針對敏感性用途，請考慮在成人確認使用綜合語音之前，先管制體驗。 鼓勵家長將訊息傳達給其子系。

![家長的洩漏](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*針對父系優化的透明簡介可確保成人在子系與其互動之前，知道語音的綜合性質。*

建議

- 以父系作為主要物件來公開
- 鼓勵家長將洩漏傳達給其子系
- 提供可深入瞭解合成語音的進入點
- 藉由要求家長提供簡單的 &quot; 防護 &quot; 問題來顯示他們已閱讀洩漏

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>提供機會來瞭解如何進行語音

提供網頁、快顯視窗或外部網站的內容相關進入點，以提供綜合語音技術的詳細資訊。 例如，您可以在上線期間或當使用者在交談期間提示您提供詳細資訊時，呈現連結以深入瞭解。

![深入瞭解的進入點](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*提供機會以深入瞭解合成語音的進入點範例。*

一旦使用者要求有關綜合語音的詳細資訊，主要目標就是要教育他們有關綜合聲音的來源，並清楚瞭解這項技術。

![提供使用者有關綜合語音的詳細資訊](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*您可以在外部網站說明網站中提供詳細資訊。*

建議

- 簡化複雜的概念，並避免使用 legalese 和技術術語
- 請勿活埋生人隱私權和使用條款聲明中的內容
- 將內容保持簡潔，並在可用時使用影像

## <a name="implicit-disclosure"></a>隱含洩漏

一致性是在整個使用者旅程中隱含洩漏的關鍵。 跨裝置和互動模式的視覺和聽覺提示一致使用，有助於建立隱含模式與明確洩漏之間的關聯。

![隱含提示的一致性](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>& 意見反應的隱含提示

Anthropomorphism 可以不同的方式呈現，從代理程式實際的視覺標記法到語音、音效、燈光模式、跳動圖形，甚至是裝置振動。 在定義您的角色時，請利用隱含的提示和意見反應模式，而不是針對非常類似人的頭像。 這是將更明確的洩漏需求降至最低的其中一種方式。

![視覺提示和意見反應](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*這些提示可協助您 anthropomorphize 代理程式，而不需要太像人類一樣。它們也可以在一段時間內一致地使用時，自行成為有效的洩漏機制。*

當您併入下列類型的提示時，請考慮您體驗的不同互動模式：

| 視覺提示                                                                                                                                                               | 聽覺提示                                                      | Haptic 提示 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  虛擬人偶 <br>回應即時提示 (例如動畫) <br> 非工具提示 (例如裝置上的燈光和模式) <br>  | Sonicon (例如，簡單的獨特音效，也就是一系列的音樂筆記)  | 震動   |

### <a name="capability-disclosure"></a>功能洩漏

您可以藉由設定數位助理所能提供的精確預期，來隱含地達成洩漏。 提供範例命令，讓使用者可以瞭解如何與數位助理互動，並提供內容相關的說明，以在體驗的初期階段深入瞭解綜合語音。

![視覺提示和意見反應](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>對話式透明度

當交談落在非預期的路徑時，請考慮製作預設回應，以協助重設預期、強化透明度，以及引導使用者前往成功的路徑。 也有機會在對話中使用明確的洩漏。

![處理非預期的路徑](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
&quot; &quot; 導向至代理程式的工作或個人問題，是提醒使用者其綜合本質，並引導他們適當地與其互動或將其重新導向至真實人員的好時機。

![處理工作問題](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>公開的時機

在整個使用者旅程圖中，有許多機會可以洩漏。 第一次使用的設計，第二次使用，第 n 次使用 ...，但也 &quot; 可採用失敗的 &quot; 時間來反白顯示透明度，例如系統發生錯誤或使用者發現代理程式&#39;的限制。

![整個使用者旅程的洩漏機會](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

標準數位助理使用者旅程圖的範例，強調各種洩漏機會。

### <a name="up-front"></a>前期

最理想的洩漏時機是人員第一次與綜合語音互動。在個人語音助理案例中，這會在上線期間，或在使用者第一次 unboxes 體驗時發生。 在其他情況下，它可能是第一次在網站上或使用者第一次與虛擬字元互動時，第一次讀取網站上的內容時。

- [透明簡介](#transparent-introduction)
- [功能洩漏](#capability-disclosure)
- [自訂和校正](#customization-and-calibration)
- [隱含提示](#implicit-cues--feedback)

### <a name="upon-request"></a>要求時

使用者應該能夠在使用者旅程期間的任何時間點，輕鬆地存取額外的資訊、控制喜好設定，並接收透明通訊。

- [提供機會來瞭解如何進行語音](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [自訂和校正](#customization-and-calibration)
- [對話式透明度](#conversational-transparency)

### <a name="continuously"></a>不斷

使用隱含的設計模式，以持續增強使用者體驗。

- [功能洩漏](#capability-disclosure)
- [隱含提示](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>當系統失敗時

將洩漏視為正常失敗的機會。

- [對話式透明度](#conversational-transparency)
- [提供機會來瞭解如何進行語音](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [將對話遞交給人員處理](#conversational-transparency)



## <a name="additional-resources"></a>其他資源
- [Microsoft Bot 指導方針](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Cortana 設計指導方針](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Microsoft Windows UWP 語音設計指導方針](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Microsoft Windows Mixed Reality 語音命令指導方針](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>參考文件

* [配音員的洩漏](https://aka.ms/disclosure-voice-talent)
* [綜合語音技術的負責任部署指導方針](concepts-guidelines-responsible-deployment-synthetic.md)
* [管制總覽](concepts-gating-overview.md)
* [如何公開](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>後續步驟

* [配音員的洩漏](https://aka.ms/disclosure-voice-talent)
