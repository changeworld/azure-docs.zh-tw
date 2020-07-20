---
title: 公開設計模式
titleSuffix: Azure Cognitive Services
description: 公開的設計模式和最佳作法。
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: 3e7d8ee2b156a30b11cda79798a8af8a8ecf4f64
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "74776617"
---
# <a name="disclosure-design-patterns"></a>公開設計模式
現在您&#39;已經決定綜合語音體驗的正確[洩漏層級](concepts-disclosure-guidelines.md#disclosure-assessment)，它&#39;是探索潛在設計模式的好時機。
## <a name="overview"></a>總覽
您可以將各種公開的設計模式套用到您的綜合語音體驗。 如果您的洩漏評估結果是「高洩漏」，我們建議[**明確洩漏**](#explicit-disclosure)，這表示會立即溝通綜合語音的來源。 [**隱含洩漏**](#implicit-disclosure)包含提示和互動模式，無論所需的洩漏層級是高或低，都能受益于語音體驗。
![洩漏模式種類](media/responsible-ai/disclosure-patterns/affordances.png)






| 明確洩漏模式                                                                                                                                                                                    | 隱含的洩漏模式                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[透明簡介](#transparent-introduction)<br> [口頭透明簡介](#verbal-transparent-introduction)<br>  [明確署名](#explicit-byline)<br>  [自訂和校正](#customization-and-calibration)<br> [家長洩漏](#parental-disclosure)<br> [提供機會來深入瞭解語音的建立方式](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [功能洩漏](#capability-disclosure)<br>[隱含提示和意見反應](#implicit-cues--feedback)<br> [對話式透明度](#conversational-transparency) |



使用下列圖表直接參考適用于您綜合語音的模式。 此圖表中的某些其他條件可能也適用于您的案例：<br/>



| 如果您的綜合語音體驗 .。。 | 建議 | 設計模式 |
| --- | --- | --- |
| 需要高洩漏  | 請先使用至少一個明確模式和隱含提示，以協助使用者建立關聯。 |[明確洩漏](#explicit-disclosure)<br>[隱含洩漏](#implicit-disclosure)  |
| 需要低洩漏 | 洩漏可能是最小或不需要，但可能受益于某些隱含模式。 | [功能洩漏](#capability-disclosure)<br>[對話式透明度](#conversational-transparency)  |
| 具有高層級的互動 | 長期建立，並提供多個進入點，以在使用者旅程圖中洩漏。 強烈建議您提供上線體驗。 | [透明簡介](#transparent-introduction)<br>[自訂和校正](#customization-and-calibration)<br>[功能洩漏](#capability-disclosure) |
| 包含子系做為主要的目標物件 | 以父系作為主要的洩漏物件，並確保他們可以有效地將洩漏傳達給子系。  | [家長洩漏](#parental-disclosure)<br>[口頭透明簡介](#verbal-transparent-introduction)<br> [隱含洩漏](#implicit-disclosure)<br> [對話式透明度](#conversational-transparency)  |
| 包括盲人使用者或具有低願景的人，作為主要目標物件  | 包含所有使用者，並確保任何形式的視覺洩漏都有相關聯的替代文字或音效效果。 遵循對比比例和顯示大小的協助工具標準。 使用聽覺提示來傳達洩漏。  | [口頭透明簡介](#verbal-transparent-introduction) <br>[聽覺提示](#implicit-cues--feedback)<br>[Haptic 提示](#implicit-cues--feedback)<br>[對話式透明度](#conversational-transparency)<br>[協助工具標準](https://www.microsoft.com/accessibility) |
| 無螢幕、無裝置，或使用語音作為主要或唯一模式的互動 | 使用聽覺提示來傳達洩漏。 | [口頭透明簡介](#verbal-transparent-introduction) <br> [聽覺提示](#implicit-cues--feedback)  |
| 可能包含多個使用者/接聽程式（例如，多家家庭的個人助理）  | 請留意各種使用者內容和瞭解層級，並在使用者旅程圖中提供多個洩漏的機會。  | [透明簡介（Return User）](#transparent-introduction)<br> [提供機會來深入瞭解語音的建立方式](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [對話式透明度](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>明確洩漏
如果您的綜合語音體驗需要高度洩漏，最好至少使用下列其中一個明確的模式，以清楚陳述綜合特性。
### <a name="transparent-introduction"></a>透明簡介

在語音體驗開始之前，請先以完全透明的方式，介紹其聲音及其功能的來源，以引進數位助理。 使用此模式的最佳時機是將新的使用者上線，或引進新功能給傳回的使用者。 在簡介期間執行隱含提示，可協助使用者形成有關數位代理程式綜合特性的心理模型。

#### <a name="first-time-user-experience"></a>首次使用者體驗

![第一次執行體驗期間的透明簡介](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*上架新使用者時，會引進綜合語音。*

建議
- 描述語音是人工的（例如&quot;數位）&quot;
- 描述代理程式能夠執行的作業
- 明確陳述語音&#39;的原始來源
- 提供進入點以深入瞭解綜合語音

#### <a name="returning-user-experience"></a>返回使用者體驗

如果使用者略過上線體驗，請繼續提供透明簡介體驗的進入點，直到使用者第一次觸發語音為止。
<br/>

![返回使用者體驗期間的透明簡介](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*提供一致的進入點給綜合語音體驗。允許使用者在使用者旅程圖中的任何時間點第一次觸發語音時，返回上線體驗。*


### <a name="verbal-transparent-introduction"></a>口頭透明簡介

說出數位助理的來源的語音提示&#39;s 聲音是明確的，以達成洩漏。 此模式最適用于高洩漏案例，其中 voice 是唯一可用的互動模式。
<br/>

![Verbally 語音透明簡介](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*在使用者經驗中，如果您可能已經&#39;s 語音引進或屬性人員，請使用透明的簡介。*


![Verbally 在第一人說的透明簡介](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*為了增加透明度，語音執行者可以在第一人公開綜合語音的來源。*

### <a name="explicit-byline"></a>明確署名

如果使用者將與音訊播放機或互動式元件互動以觸發語音，請使用此模式。


![新聞媒體案例中的明確署名](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*明確的署名是語音來自何處的屬性。*

建議

- 提供進入點以深入瞭解合成的語音

### <a name="customization-and-calibration"></a>自訂和校正

讓使用者控制數字助理的回應方式（也就是語音聲音的方式）。  當使用者以自己的詞彙與系統互動，並以特定目標為考慮時，則根據定義，他們已經瞭解它&#39;不是真正的人。

#### <a name="user-control"></a>使用者控制項

提供對綜合語音體驗有意義且顯著影響的選擇。

![使用者喜好設定](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*使用者喜好設定可讓使用者自訂及改善他們的體驗。*

建議

- 允許使用者自訂語音（例如，選取 [語言] 和 [語音類型]）
- 提供使用者一種方法，讓系統能夠回應他的獨特語音（例如，語音校正、自訂命令）
- 為使用者產生或內容相關的互動優化（例如提醒）

#### <a name="persona-customization"></a>自訂角色

提供自訂數位助理&#39;s 語音的方式。 如果語音是以名人或廣為辨識的人員為基礎，請考慮在使用者預覽語音時，同時使用視覺和語音簡介。

![語音自訂](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*提供從一組語音中選取的功能，有助於傳達人工性質。*

建議
- 允許使用者預覽每個語音的音效
- 針對每個語音使用真實的簡介
- 提供進入點以深入瞭解合成的語音

### <a name="parental-disclosure"></a>家長洩漏

除了遵守保護法 CHILDREN COPPA 法規以外，如果您的主要目標物件是年輕兒童，而您的曝光程度很高，則提供給家長的洩漏。 針對機密用途，請考慮管制經驗，直到成人認可使用綜合語音為止。 鼓勵家長將訊息傳達給其子系。

![父系的洩漏](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*針對父系優化的「透明簡介」可確保成人在子系與它互動之前，已知道聲音的綜合特性。*

建議

- 作為主要物件的目標家長
- 鼓勵家長將洩漏傳達給其子系
- 提供進入點以深入瞭解合成的語音
- 藉由向家長詢問一個簡單&quot;的防護&quot;問題，以顯示他們已閱讀洩漏的經驗，來閘道

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>提供機會來深入瞭解語音的建立方式

提供網頁、快顯或外部網站的內容相關進入點，以提供有關綜合語音技術的詳細資訊。 例如，您可以在上架期間或當使用者在對話期間提示您提供詳細資訊時，呈現連結以深入瞭解。

![深入瞭解的進入點](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*進入點的範例，讓您有機會可以深入瞭解合成的聲音。*

一旦使用者要求有關綜合語音的詳細資訊，主要目標就是要教育他們關於綜合語音的來源，並對技術透明化。

![為使用者提供綜合語音的詳細資訊](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*您可以在外部網站說明網站中提供詳細資訊。*

建議

- 簡化複雜的概念，並避免使用 legalese 和技術術語
- 請勿在隱私權和使用規定聲明中活埋生人此內容
- 將內容保持簡潔並使用影像（若有的話）

## <a name="implicit-disclosure"></a>隱含洩漏

一致性是在整個使用者旅程中隱含洩漏的關鍵。 跨裝置和互動模式一致使用視覺和聽覺提示有助於建立隱含模式與明確洩漏之間的關聯。

![隱含提示的一致性](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>& 意見反應的隱含提示

Anthropomorphism 可以用不同的方式來資訊清單，從代理程式的實際視覺表示，到語音、音效、光線模式、跳動的圖形，甚至裝置的震動。 定義您的角色時，請利用隱含提示和意見反應模式，而不要瞄準非常類似人為的頭像。 這是將更明確洩漏的需求降至最低的其中一種方式。

![視覺提示和意見反應](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*這些提示可協助 anthropomorphize 代理程式，而不會有太多人贊。它們也可以在一段時間內一致使用時，自行成為有效的洩漏機制。*

納入下列類型的提示時，請考慮各種不同的體驗互動模式：

| 視覺提示                                                                                                                                                               | 聽覺提示                                                      | Haptic 提示 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  阿凡達 <br>回應式即時提示（例如動畫）<br> 非工具提示（例如裝置上的燈和模式）<br>  | Sonicon （例如，簡短的特殊音效，一系列音樂便箋） | 震動   |

### <a name="capability-disclosure"></a>功能洩漏

藉由為數字助理提供正確的預期，可以明確地實現洩漏。 提供範例命令，讓使用者可以瞭解如何與數位助理互動，並提供內容相關的說明，以深入瞭解體驗初期階段的綜合語音。

![視覺提示和意見反應](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>對話式透明度

當交談落在非預期的路徑時，請考慮製作預設回應，以協助重設預期、加強透明度，以及引導使用者成功完成路徑。 也有機會在交談中使用明確的洩漏。

![處理非預期的路徑](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
針對代理程式所&quot;導向&quot;的工作或個人問題，是為使用者提供代理程式綜合性質的好時機，並可讓他們適當地進行互動，或將其重新導向至真實人員。

![處理關閉工作問題](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>公開的時機

在使用者旅程圖中，有許多機會要洩漏。 第一次使用的設計，第二次使用，第 n 個使用 ...，同時也&quot;採用&quot;失敗的時間來反白顯示透明度，例如當系統發生錯誤時，或使用者發現代理程式的限制&#39;s 功能時。

![使用者旅程圖中的洩漏機會](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

標準數位助理使用者旅程圖的範例，著重于各種洩漏機會。

### <a name="up-front"></a>前置

洩漏的最佳時機是使用者第一次與綜合語音互動。在個人語音助理案例中，這會在上架期間，或使用者第一次 unboxes 體驗時。 在其他案例中，可能是第一次綜合語音讀取網站上的內容，或是使用者第一次與虛擬字元互動時。

- [透明簡介](#transparent-introduction)
- [功能洩漏](#capability-disclosure)
- [自訂和校正](#customization-and-calibration)
- [隱含提示](#implicit-cues--feedback)

### <a name="upon-request"></a>要求時

使用者應該能夠在使用者旅程的任何時間點，輕鬆地存取其他資訊、控制喜好設定，並接收透明的通訊。

- [提供機會來深入瞭解語音的建立方式](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [自訂和校正](#customization-and-calibration)
- [對話式透明度](#conversational-transparency)

### <a name="continuously"></a>穩定

使用隱含的設計模式，持續增強使用者體驗。

- [功能洩漏](#capability-disclosure)
- [隱含提示](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>當系統失敗時

使用洩漏作為正常失敗的機會。

- [對話式透明度](#conversational-transparency)
- [提供機會來深入瞭解語音的建立方式](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [將對話遞交給人員處理](#conversational-transparency)



## <a name="additional-resources"></a>其他資源
- [Microsoft Bot 指導方針](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Cortana 設計指導方針](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Microsoft Windows UWP 語音設計指導方針](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Microsoft Windows Mixed Reality 語音命令指導方針](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>參考文件

* [對配音人才的洩漏](https://aka.ms/disclosure-voice-talent)
* [綜合語音技術的責任部署指導方針](concepts-guidelines-responsible-deployment-synthetic.md)
* [管制總覽](concepts-gating-overview.md)
* [如何公開](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>後續步驟

* [對配音人才的洩漏](https://aka.ms/disclosure-voice-talent)
