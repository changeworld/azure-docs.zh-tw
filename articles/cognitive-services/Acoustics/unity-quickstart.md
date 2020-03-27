---
title: 搭配 Unity 的聲場專案快速入門
titlesuffix: Azure Cognitive Services
description: 使用範例內容對 Unity 中的聲場專案設計控制項進行實驗，並部署至 Windows 電腦。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "72243029"
---
# <a name="project-acoustics-unity-quickstart"></a>聲場專案 Unity 快速入門
使用適用於 Unity 的聲場專案範例內容對模擬所支援的設計控制項進行實驗。

軟體需求：
* 適用於 Windows 的 [Unity 2018.2+](https://unity3d.com) \(英文\)
* [聲場專案範例內容套件](https://www.microsoft.com/download/details.aspx?id=57346) \(英文\)

範例套件中包含哪些內容？
* 具有幾何、聲音來源，以及遊戲控制項的 Unity 場景
* 聲場專案外掛程式
* 適用於該場景並已完成聲場模擬的聲場資產

## <a name="import-the-sample-package"></a>匯入範例套件
將範例套件匯入至新的 Unity 專案。
1. 在 Unity 中移至 [資產]   > [匯入套件]   > [自訂套件]  。

    ![Unity 匯入套件選項](media/import-package.png)  

1. 選擇 **ProjectAcoustics.unitypackage**。

1. 選取 [匯入]  按鈕，將 Unity 套件整合到您的專案中。  
  
    ![Unity 匯入套件對話方塊](media/import-dialog.png)  

若要將套件匯入現有專案，請參閱 [Unity 整合](unity-integration.md)以取得額外步驟和注意事項。

>[!NOTE]
>匯入完成之後，主控台記錄中將會出現數個錯誤訊息。 請繼續進行下一個步驟，並重新啟動 Unity。

## <a name="restart-unity"></a>重新啟動 Unity
聲場工具組的製作部分需要 .NET 4.*x* 指令碼處理執行階段版本。 套件匯入將會更新您的 Unity 播放器設定。 請重新啟動 Unity 來讓此設定生效。 若要確認設定是否生效，請開啟 [播放器]  設定：

![Unity 專案設定功能表](media/player-settings.png)  

![Unity 播放器設定面板 (已選取 .NET 4.x)](media/net45.png)  

>[!NOTE]
>此螢幕擷取畫面取自 Unity 2018.*x*。 在新版的 Unity 中，影像可能會不一樣。

## <a name="open-the-project-acoustics-bake-window"></a>開啟聲場專案聲場模擬視窗
在 Unity 中，選取 [視窗]  功能表上的 [聲場]  。

![[視窗] 功能表上 [聲場] 選項已反白顯示的 Unity 編輯器](media/window-acoustics.png)

將會開啟浮動的 [聲場]  視窗。 此視窗是您設定聲場模擬屬性的位置。

![[聲場] 視窗已開啟的 Unity 編輯器](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>對設計控制項進行實驗
開啟 *ProjectAcousticsSample* 資料夾中的範例場景，然後選取 Unity 編輯器中的 [Play] \(播放\) 按鈕。 使用 W、A、S、D 鍵與滑鼠來四處移動。 若要比較場景音效使用原音與不使用原音時的不同，請選取 R 鍵，直到重疊文字變成紅色並說出「原音：已停用」。 若要查看鍵盤快速鍵以取得更多控制項，請選取 F1。 您也可以按一下滑鼠右鍵以選取動作，然後按一下滑鼠左鍵以執行該動作。

*AcousticsAdjust* 指令碼已附加到範例場景中的音效來源。 它啟用了每個來源的設計參數。

![Unity AcousticsAdjust 指令碼](media/acoustics-adjust.png)

下列各節探索您可以透過使用可用控制項建立的一些效果。 如需每個控制項的詳細資訊，請參閱[聲場專案 Unity 設計教學課程](unity-workflow.md)。

### <a name="modify-distance-based-attenuation"></a>修改以距離為基礎的衰減
**聲場專案** Unity 空間定位外掛程式中的音訊數位訊號處理會遵循 Unity 編輯器所內建，以每個來源距離為基礎的衰減。 距離型衰減控制項位於聲音來源之 [Inspector] \(偵測器\)  面板中 [Audio Source] \(音訊來源\)  元件的 [3D Sound Settings] \(3D 聲音設定\)  底下：

![Unity 距離衰減選項面板](media/distance-attenuation.png)

聲場專案會在以玩家位置為中心的「模擬區域」方塊中執行計算。 範例套件中的聲場資產是以玩家周圍 45 公尺的模擬區域大小進行聲場模擬。 因此，音效衰減應該設計為在大約 45 公尺的地方降到 0。

### <a name="modify-occlusion-and-transmission"></a>修改遮蔽和傳輸
* 如果 [Occlusion]  \(遮蔽\) 的乘數大於 1 (預設值為 1)，遮蔽便會被放大。 若要讓遮蔽效果更細微，請將它設定為小於 1。

* 若要啟用穿牆傳輸，請將 [Transmission (dB)]  \(傳輸 (dB)\) 滑桿從最低設定移開。

### <a name="modify-wetness-for-a-source"></a>修改來源的濕潤度
* 若要變更濕潤度隨距離變更的程度，請使用 [Perceptual Distance Warp]  \(感知距離變形\)。 透過模擬，聲場專案計算整個空間的濕潤層級，此數值會提供感知距離的線索，而且會隨著距離而緩和變化。 增加距離變形將會提升與距離相關的濕潤層級，以放大此效果。 小於 1 的變形值會使以距離為基礎的殘響變化變得更加細微。

   若要精細地調整此效果，請變更 [Wetness (dB)]  \(濕潤 (dB)\) 設定。

* 若要增加整個空間的衰減時間，請調整 [Decay Time Scale]  \(衰減時間刻度\)。 如果特定來源-聆聽者位置組的模擬結果為 1.5 秒的衰減時間，且 [Decay Time Scale]  \(衰減時間刻度\) 已設定為 2，則套用至來源的衰減時間將會是 3 秒。

## <a name="next-steps"></a>後續步驟
* 請參閱[以 Unity 為基礎的聲場專案設計控制項](unity-workflow.md)中所提供的詳細資料。
* 深入探索[設計程序](design-process.md)背後的概念。
* [建立 Azure 帳戶](create-azure-account.md)以探索聲場模擬前與聲場模擬的程序。
