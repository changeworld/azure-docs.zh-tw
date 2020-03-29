---
title: 專案聲學統一集成和部署
titlesuffix: Azure Cognitive Services
description: 本文介紹如何將專案聲學 Unity 外掛程式集成到 Unity 專案中。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243004"
---
# <a name="project-acoustics-unity-integration"></a>專案聲學統一集成
本文介紹如何將專案聲學 Unity 外掛程式集成到 Unity 專案中。

軟體需求：
* 適用于 Windows[的 Unity 2018.2+](https://unity3d.com)
* [專案聲學統一包](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>導入外掛程式
1. 將聲學 UnityPackage 導入您的專案。 
 在 Unity 中，轉到**資產** > **導入包** > **自訂包**。

    ![統一導入包功能表](media/import-package.png)  

1. 選擇 **ProjectAcoustics.unitypackage**。

1. 選取 [匯入]**** 按鈕，將 Unity 套件整合到您的專案中。

    ![Unity 匯入套件對話方塊](media/import-dialog.png)  

如果要將外掛程式導入現有專案，則專案可能已在專案根目錄中具有*mcs.rsp*檔。 此檔指定 C# 編譯器的選項。 將該檔的內容與專案聲學外掛程式附帶的 mcs.rsp 檔合併。

## <a name="enable-the-plug-in"></a>啟用外掛程式
聲學工具組的烘焙部分需要 .NET 4。*x*腳本執行階段版本。 包導入更新您的 Unity 播放機設置。 請重新啟動 Unity 來讓此設定生效。

!["統一播放機設置"面板](media/player-settings.png)

![選擇 .NET 4.5 的 Unity 播放機設置面板](media/net45.png)

## <a name="set-up-audio-dsp"></a>設置音訊 DSP
專案聲學包括音訊運行時 DSP，集成到 Unity 音訊引擎空間儀框架中。 它既包括基於 HRTF 和平移空間化。 要啟用專案聲學 DSP，請轉到 **"編輯** > **專案設置** > **音訊**"以打開 Unity 音訊設置。 選擇**專案聲學**作為專案**的空間外掛程式**。 確保**DSP 緩衝區大小**設置為*最佳性能*。

![Unity 專案設定功能表](media/project-settings.png)  

![選擇"專案聲學空間器"的 Unity 空間設置面板](media/choose-spatializer.png)

接下來，打開音訊混合器 （**視窗** > **音訊混合器**）。 確保至少有一個混合器，一個組。 如果沒有，請選擇**+****混合器**右側的按鈕。 在效果區段中聲道控制排的底部上按一下滑鼠右鍵，然後新增 [Microsoft Acoustics Mixer] \(Microsoft Acoustics 混音器\)**** 效果。 一次只支援一個專案聲學混合器。

![託管專案聲學混音器的 Unity 音訊混音器](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>在聲源上啟用聲學
創建音訊源：選擇"音訊源"檢查面板底部的 **"空間化**"核取方塊。 確保**空間混合**設置為完全*3D*。  

![Unity 音訊源面板](media/audio-source.png)

## <a name="enable-acoustic-design"></a>啟用聲學設計
將*聲學調整*腳本附加到場景中的聲源，以啟用其他源設計參數：選擇 **"添加元件"** 並選擇 **"腳本** > **聲學調整**"。

![Unity AcousticsAdjust 指令碼](media/acoustics-adjust.png)

## <a name="next-steps"></a>後續步驟
* [烘焙你的場景與團結專案聲學](unity-baking.md)。
* [創建 Azure 批次處理帳戶](create-azure-account.md)以在雲中烘焙場景。
* 探索[專案聲學統一設計過程](unity-workflow.md)。
