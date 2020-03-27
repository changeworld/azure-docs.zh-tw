---
title: 搭配 Unreal 的聲場專案快速入門
titlesuffix: Azure Cognitive Services
description: 使用範例內容對 Unreal 和 Wwise 中的聲場專案設計控制項進行實驗，並部署至 Windows 電腦。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "72242912"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>聲場專案 Unreal/Wwise 快速入門
在此快速入門中，您將會使用適用於 Unreal Engine 和 Wwise 的範例內容，對聲場專案設計控制項進行實驗。

使用範例內容的軟體需求：
* [Unreal Engine](https://www.unrealengine.com/) 4.22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>下載範例套件
下載[聲場專案 Unreal 與 Wwise 範例套件](https://www.microsoft.com/download/details.aspx?id=58090) \(英文\)。 範例套件包含：
- Unreal Engine 專案
- 適用於 Unreal 專案的 Wwise 專案
- 聲場專案 Wwise 外掛程式

## <a name="set-up-the-project-acoustics-sample-project"></a>設定聲場專案範例專案
首先，將聲場專案外掛程式安裝到 Wwise。 接著，將 Wwise 二進位檔案部署到 Unreal 專案。 接著，調整 Wwise Unreal 外掛程式以支援聲場專案。

### <a name="install-the-project-acoustics-wwise-plug-in"></a>安裝聲場專案 Wwise 外掛程式
開啟 Wwise Launcher。 在 [Plugins]  \(外掛程式\) 索引標籤的 [Install New Plugins]  \(安裝新外掛程式\) 下，選取 [Add from directory]  \(從目錄新增\)。 選擇包含在您下載之套件中的 *AcousticsWwisePlugin\ProjectAcoustics* 目錄。

![在 Wwise Launcher 中安裝 Wwise 外掛程式的選項](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>將 Wwise 二進位檔新增至聲場專案 Unreal 範例專案
1. 在 Wwise Launcher 中，選取 [Unreal Engine]  索引標籤。 
1. 選取 [Recent Unreal Engine Projects]  \(最近的 Unreal Engine 專案\) 旁的 [漢堡] \(圖示\) 功能表，然後選取 [Browse for project]  \(瀏覽專案\)。 開啟套件 *AcousticsSample\AcousticsGame\AcousticsGame.uproject* 中的範例 Unreal 專案 *.uproject* 檔案。

   ![Wwise Launcher 中的 [Unreal] 索引標籤](media/wwise-unreal-tab.png)

3. 選取聲場專案範例專案旁邊的 [Integrate Wwise in Project]  \(在專案中整合 Wwise\)。

   ![Wwise Launcher 會顯示 Acoustics Game Unreal 專案，並反白顯示 [整合] 選項。](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>擴充 Wwise 的 Unreal 外掛程式功能
聲場專案 Unreal 外掛程式需要從 Wwise Unreal 外掛程式 API 公開額外的行為。 請執行聲場專案 Unreal 外掛程式隨附的批次檔，以將這些修改作業自動化。
* 在 *AcousticsGame\Plugins\ProjectAcoustics\Resources* 內，執行 *PatchWwise.bat*。

    ![該指令碼會修補在 Windows 檔案總管視窗中反白顯示的 Wwise 專案](media/patch-wwise-script.png)

* 若您尚未安裝 DirectX SDK：根據您使用的 Wwise 版本，您可能需要將 *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* 中包含 `DXSDK_DIR` 的那一行註解化：

    ![顯示已將 DXSDK 註解化的程式碼編輯器](media/directx-sdk-comment.png)

* 若您使用 Visual Studio 2019 來編譯：若要因應 Wwise 的連結錯誤，請手動將 *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* 中的預設值 *VSVersion* 變更為 **vc150**：

    ![VSVersion 已變更為 "vc150" 的程式碼編輯器](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>開啟 Unreal 專案 
當您開啟 Unreal 專案時，它將會提示您重建模組。 選取 [是]  。

如果開啟專案因建置失敗而失敗，請檢查是否已將聲場專案 Wwise 外掛程式安裝至和用於聲場專案範例專案相同的 Wwise 版本。

若您使用的 [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 比 2019.1 版還舊，您將無法使用聲場專案範例專案產聲音效庫。 您必須將 Wwise 2019.1 版整合到範例專案中。

## <a name="experiment-with-project-acoustics-design-controls"></a>對聲場專案設計控制項進行實驗
選取 Unreal 編輯器中的 [Play] \(播放\) 按鈕來聆聽場景聲音。 使用 W、A、S、D 鍵與滑鼠來四處移動。 若要查看額外控制項的鍵盤快速鍵，請選取 F1。

下列資訊描述可嘗試的一些設計活動。

### <a name="modify-occlusion-and-transmission"></a>修改遮蔽和傳輸
每個 Unreal 聲音動作項目上都有每個來源的聲場專案設計控制項。

![Unreal 編輯器聲場設計控制項](media/demo-scene-sound-source-design-controls.png)

如果 [Occlusion]  \(遮蔽\) 的乘數大於 1 (預設值為 1)，遮蔽便會被放大。 小於 1 的設定會使遮蔽效果更細微。

若要啟用穿牆傳輸，請將 [Transmission (dB)]  \(傳輸 (dB)\) 滑桿從其最低位置移開。

### <a name="modify-wetness-for-a-source"></a>修改來源的濕潤度
若要變更濕潤度隨距離變更的程度，請使用 [Perceptual Distance Warp]  \(感知距離變形\)。 聲場專案會透過模擬在整個空間計算濕潤層級。 層級會隨著距離緩和地變化，並提供感知距離提示。若要放大此效果，請增加距離相關濕潤層級以增加距離變形。 小於 1 的變形值會使以距離為基礎的殘響變化變得更加細微。 您也可以透過 [Wetness (dB)]  \(濕潤 (dB)\) 來精細地調整此效果。

若要增加整個空間的衰減時間，請調整 [Decay Time Scale]  \(衰減時間刻度\)。 讓我們以模擬結果為 1.5 秒的衰減時間作為例子。 將 [Decay Time Scale]  \(衰減時間刻度\) 設定為 2 會使系統將 3 秒的衰減時間套用至來源。

### <a name="modify-distance-based-attenuation"></a>修改以距離為基礎的衰減
聲場專案 Wwise 混音器外掛程式會遵循 Wwise 所內建，以每個來源距離為基礎的衰減。 變更此曲線將會變更乾燥路徑層級。 聲場專案外掛程式將會調整濕潤層級，以維持模擬和設計控制項所指定的濕潤-乾燥混音。

![Wwise 衰減曲線面板，其中顯示在模擬界限前朝向 0 的衰減](media/demo-sounds-attenuation.png)

聲場專案會在以每個模擬玩家位置為中心的「模擬區域」方塊中執行計算。 範例套件中的聲場資產是以 45 公尺的模擬區域半徑進行聲場模擬。 衰減設計為在抵達 45 公尺之前便會降至 0。 雖然此降低並非嚴格的要求，您應該留意到只有位於聆聽者 45 公尺內的幾何才會遮蔽聲音。

## <a name="next-steps"></a>後續步驟
* [整合聲場專案](unreal-integration.md)外掛程式至您的 Unreal 專案。
* 為您自己的製作項目[建立 Azure 帳戶](create-azure-account.md)。
