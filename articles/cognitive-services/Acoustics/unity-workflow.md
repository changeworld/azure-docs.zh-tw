---
title: 聲場專案 Unity 設計教學課程
titlesuffix: Azure Cognitive Services
description: 本教學課程描述 Unity 中聲場專案的設計工作流程。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fd00e4105ce4edae9d014df2a83c5ae3aaf778da
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "68854271"
---
# <a name="project-acoustics-unity-design-tutorial"></a>聲場專案 Unity 設計教學課程
本教學課程描述 Unity 中聲場專案的設計工具與工作流程。

必要條件：
* 適用於 Windows 的 Unity 2018.2+
* 具有已完成行聲場模擬之聲場資產的 Unity 場景

針對此教學課程，您可以透過兩種方式取得具有已完成聲場模擬之聲場資產的 Unity 場景：
* [將聲場專案新增至 Unity 專案](unity-integration.md)，並[取得 Azure Batch 帳戶](create-azure-account.md)，然後[對 Unity 場景進行聲場模擬](unity-baking.md)
* 或者，使用[聲場專案 Unity 範例內容](unity-quickstart.md)。

## <a name="review-design-process-concepts"></a>檢閱設計程序概念
聲場專案會使用常見的音訊數位訊號處理 (DSP) 方法來處理您的來源，並給予您對熟悉聲場屬性的控制，其中包括遮蔽、濕潤/乾燥混音，以及殘響結尾長度 (RT60)。 但核心的[聲場專案設計程序概念](design-process.md)並非直接設定這些屬性，而是讓您控制要如何將模擬結果用來驅動這些屬性。 每個控制項的預設設定皆代表實際的精準聲場。

## <a name="design-acoustics-for-each-source"></a>針對每個來源設計聲場
聲場專案提供數個來源特定的聲場設計控制項。 這可讓您透過強調某些來源並不加以強調其他來源的方式，控制場景中的混音。

### <a name="adjust-distance-based-attenuation"></a>調整以距離為基礎的衰減
**聲場專案** Unity 空間定位外掛程式所提供的音訊 DSP，會遵循 Unity 編輯器所內建，以每個來源距離為基礎的衰減。 距離型衰減控制項位於聲音來源之 [Inspector] \(偵測器\)  面板中 [Audio Source] \(音訊來源\)  元件的 [3D Sound Settings] \(3D 聲音設定\)  底下：

![Unity 距離衰減選項面板的螢幕擷取畫面](media/distance-attenuation.png)

「模擬區域」方塊中的聲場效能計算著重於播放器的位置。 如果音效來源距離播放器較遠，位於模擬區域之外，則只有方塊中的幾何形狀會影響音效傳播 (例如造成阻隔)，特別是當阻隔器位於播放器鄰近區域內時效果最好。 不過，如果播放器位於開放空間，但阻隔器的位置靠近遠端音效來源，則播放器可能無法清楚辨識音效。 我們建議的因應措施是確保這種情況下的音效衰減在約 45 公尺時降低至 0，也就是播放器到方塊邊緣的預設水平距離。

![Unity SpeakerMode 選項面板的螢幕擷取畫面](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>調整遮蔽和傳輸
將 **AcousticsAdjust** 指令碼附加至來源可為該來源啟用調整參數。 若要附加指令碼，請按一下 [Inspector] \(偵測器\)  面板底部的 [Add Component] \(新增元件\)  ，然後瀏覽至 [Scripts] \(指令碼\) > [Acoustics Adjust] \(聲場調整\)  。 指令碼具有六個控制項：

![Unity AcousticsAdjust 指令碼的螢幕擷取畫面](media/acoustics-adjust.png)

* **Enable Acoustics** \(啟用聲場\) - 控制是否要將聲場套用至此來源。 若未核取，來源會與 HRTF 或移動瀏覽進行空間定位，但不會有任何聲場。 這表示其中沒有障礙物、遮蔽或動態殘響參數，例如等級和衰變時間。 系統仍會套用具有固定等級和衰變時間的殘響。
* **阻隔** - 將乘數套用至由聲場系統計算的阻隔 db 等級。 如果此乘數大於 1，阻隔會增強；如果值小於 1，阻擋的效果則會降低；值為 0 時則沒有阻隔。
* **傳輸 (dB)** - 設應由幾何傳輸造成的衰減 (單位為 dB)。 將滑桿設定至最低等級以停止傳輸。 聲場會在初始乾燥音訊抵達場景幾何 (傳送) 時進行空間定位。 傳輸會提供在視野方向中進行空間定位的額外乾燥抵達。 請注意，來源的距離衰減曲線也一併適用。

### <a name="adjust-reverberation"></a>調整殘響
* **濕潤度 (dB)** - 根據與來源的距離調整殘響功率 (單位為 dB)。 正數值會讓聲音有較多殘響，負數值則會讓聲音較乾淨清楚。 按一下曲線控制項 (綠線) 即可顯示曲線編輯器。 如要調整曲線，請按一下滑鼠左鍵來新增調整點，並拖曳這些點以構成您想要的形狀。 X 軸是與來源的距離，Y 軸則是殘響調整值 (單位為 dB)。 如需如何編輯曲線的詳細資訊，請參閱此 [Unity Manual](https://docs.unity3d.com/Manual/EditingCurves.html)。 若要將曲線重設回預設值，請在 [Wetness] \(濕潤度\)  按一下滑鼠右鍵，然後選取  [Reset] \(重設\)。
* **Decay Time Scale** \(衰變時間縮放\) - 調整衰變時間的倍數。 例如，如果製作結果指定的衰變時間為 750 毫秒，但此值設定為 1.5，則套用至來源的衰變時間就會是 1,125 毫秒。
* **阻隔度** - 聲場系統的累加調整值，能預估來源上殘響的「戶外」效果。 將此值設定為 1 可讓來源一律聽起來像來自戶外，設定為 -1 則可讓來源聽起來完全像來自室內。

將 **AcousticsAdjustExperimental** 指令碼附加至來源可為該來源啟用額外實驗調整參數。 若要附加指令碼，請按一下 [Inspector] \(偵測器\)  面板底部的 [Add Component] \(新增元件\)  ，然後瀏覽至 [Scripts] \(指令碼\) > [Acoustics Adjust Experimental] \(實驗性聲場調整\)  。 目前具有一個實驗性控制項：

![Unity AcousticsAdjustExperimental 指令碼的螢幕擷取畫面](media/acoustics-adjust-experimental.png)

* **感知距離變形** - 將指數變形功能套用至距離，以計算乾燥濕潤比。 聲場系統會計算空間濕潤度，此數值會因距離和提供的感知距離變形而變化。變形值大於 1 會放大該效果，提高與距離相關殘響等級，讓音效聽起來較「遙遠」。 變形值小於 1 則會減少殘響變化，讓音效更有「臨場感」。

## <a name="design-acoustics-for-all-sources"></a>針對所有來源設計聲場
若要調整所有來源的參數，請按一下 Unity [Audio Mixer] \(音訊混音器\)  中的聲道控制排，然後調整 [Project Acoustics Mixer] \(專案聲場混音器\)  效果上的參數。

![聲場專案 Unity 混音器自訂面板的螢幕擷取畫面](media/mixer-parameters.png)

* [Wetness Adjust] \(溼潤度調整\)  - 根據來源與聆聽者相隔的距離，來對場景中的所有來源調整殘響功率 (單位為 dB)。 正數值會讓聲音有較多殘響，負數值則會讓聲音較乾淨清楚。
* [RT60 Scale] \(RT60 縮放\)  - 殘響時間的乘法純量。
* [Use Panning] \(使用移動瀏覽\)  - 控制要將音訊輸出為雙聲道 (0) 還是多聲道移動瀏覽 (1)。 1 以外的任何值代表雙聲道。 雙聲道輸出會與 HRTF 進行空間定位以便用於耳機，多聲道輸出則會與 VBAP 進行空間定位以便用於多聲道環繞喇叭系統。 如果使用多聲道移動瀏覽器，請務必選取符合您裝置設定 (可在 [Project Settings] \(專案設定\)   > [Audio] \(音訊\)  下找到) 的喇叭模式。

## <a name="check-proper-sound-source-placement"></a>檢查適當的聲音來源位置
系統不會對置於被佔用體素內的聲音來源進行聲場處理。 因為體素可延伸並超過可見的場景幾何，所以就算來源看起來似乎沒有被可見幾何所遮蔽，其仍有可能已被置於體素內。 您可以透過切換 [Scene]  \(場景\) 檢視右上角 [Gizmos]  \(小工具\) 功能表中的體素格線核取方塊，來檢視聲場專案體素。

![Unity Gizmos 功能表的螢幕擷取畫面](media/gizmos-menu.png)  

體素顯示也能協助您判斷遊戲中的視覺元件是否已套用轉換。 如果已套用轉換，請將相同的轉換套用至裝載 **Acoustics Manager** \(聲場管理員\) 的 GameObject。

### <a name="bake-time-vs-run-time-voxels"></a>聲場模擬時間與執行階段體素
您可以在遊戲設計階段的編輯器視窗中，以及在執行階段的遊戲視窗中檢視體素。 這些檢視中的體素大小將會不同。 這是因為聲場執行階段內插補點會使用更精細的體素格線，以產生更平順的內插補點結果。 您應使用執行階段體素來確認聲音來源位置。

設計階段體素：

![在設計階段的聲場專案體素螢幕擷取畫面](media/voxels-design-time.png)

執行階段體素：

![在執行階段的聲場專案體素螢幕擷取畫面](media/voxels-runtime.png)

## <a name="next-steps"></a>後續步驟
* 探索能示範[設計程序](design-process.md)背後之概念的個案研究

