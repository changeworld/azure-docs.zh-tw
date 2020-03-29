---
title: 專案聲學虛幻和 Wwise 集成
titlesuffix: Azure Cognitive Services
description: 本文介紹了專案聲學虛幻和 Wwise 外掛程式集成到專案中。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243051"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>專案聲學虛幻和 Wwise 集成
本文介紹如何將專案聲學外掛程式包集成到現有的虛幻遊戲和 Wwise 遊戲專案中。

軟體需求：
* [虛幻引擎](https://www.unrealengine.com/)4.20°
* [音訊動力學 Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1
* [虛幻外掛程式](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  如果您使用的是 Wwise SDK 的直接集成，而不是 Wwise 虛幻外掛程式，請參閱專案聲學虛幻外掛程式並調整 Wwise API 呼叫。

要將專案聲學與 Wwise 以外的音訊引擎一起使用，請在[專案聲學論壇](https://github.com/microsoft/ProjectAcoustics/issues)中提出增強請求。 您可以使用"專案聲學虛幻外掛程式"查詢聲學資料，並對引擎進行 API 呼叫。

## <a name="download-project-acoustics"></a>下載專案聲學
如果您尚未下載["專案聲學虛幻"和"Wwise"外掛程式包](https://www.microsoft.com/download/details.aspx?id=58090)。

包裝中包括虛幻引擎外掛程式和 Wwise 混合器外掛程式。 虛幻外掛程式提供編輯器和運行時集成。 在遊戲過程中，專案聲學虛幻外掛程式計算參數，如每個幀的每個遊戲物件的遮擋。 這些參數被轉換為 Wwise API 呼叫。

## <a name="integration-steps"></a>整合步驟

按照以下步驟安裝包並將其部署到遊戲中。

### <a name="install-the-project-acoustics-mixer-plug-in"></a>安裝專案聲學混合器外掛程式
1. 打開Wwise發射器。 在**外掛程式**選項卡上，在 **"安裝新外掛程式**"下，選擇 **"從目錄添加**"。

    ![在 Wwise 發射器中安裝外掛程式](media/wwise-install-new-plugin.png)

1. 選擇下載包中的*聲學WwisePlugin_Project聲學目錄*。 它包含 Wwise 混合器外掛程式捆綁包。

   Wwise 將安裝該外掛程式。 專案聲學應出現在 Wwise 中已安裝的外掛程式清單中。  
![專案聲學安裝後 Wwise 安裝的外掛程式清單](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>將 Wwise 部署到您的遊戲中
重新部署 Wwise 到您的遊戲，即使您已經集成了 Wwise。 此步驟集成了專案聲學 Wwise 外掛程式。

   > [!NOTE]
   > **發動機外掛程式：** 如果您在虛幻C++專案中將 Wwise 作為遊戲外掛程式安裝，請跳過此步驟。 如果它作為引擎外掛程式安裝，例如，由於您的虛幻專案僅藍圖，使用混合器外掛程式進行 Wwise 部署則更為複雜。 創建虛擬空虛C++專案。 如果虛幻編輯器打開，請關閉該編輯器，然後按照其餘過程將 Wwise 部署到虛擬專案中。 然後，複製已部署的 Wwise 外掛程式。
 
1. 從 Wwise 啟動器中，選擇**虛幻引擎**選項卡。選擇 **"最近虛幻引擎專案**"旁邊的"漢堡包"（圖示）功能表，然後選擇 **"流覽專案**"。 打開遊戲的虛幻專案 *.project*檔。

    ![Wwise 啟動器虛幻選項卡](media/wwise-unreal-tab.png)

1. 選擇"**在專案中集成 Wwise"** 或在**專案中修改 Wwise。** 此步驟將 Wwise 二進位檔案集成到您的專案中，包括專案聲學混合器外掛程式。

   > [!NOTE]
   > **發動機外掛程式：** 如果您使用 Wwise 作為引擎外掛程式，並且創建了虛擬專案，請複製 Wwise 部署的資料夾 *：[DummyUProject]_Plugins_Wwise*。 粘貼在 *[UESource]\引擎\外掛程式\Wwise*。 *[DummyUProject]* 是空的虛C++專案路徑 *，[UESource]* 是安裝虛幻引擎源的位置。 複製資料夾後，可以刪除虛擬專案。

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>將專案聲學虛幻外掛程式添加到您的遊戲
 
1. 複製外掛程式包中的*虛幻\專案聲學*資料夾。 創建新資料夾 *[UProjectDir]_Plugins_ProjectAcoustics*，其中 *[UProjectDir]* 是包含 *.uproject*檔的遊戲專案資料夾。

   > [!NOTE]
   > **發動機外掛程式**：如果您使用 Wwise 作為發動機外掛程式，則還應使用"專案聲學"作為虛幻引擎外掛程式。 使用 *[UESource]\引擎\外掛程式\專案聲學*，而不是前面引用的目標目錄。

1. 確認在 *"專案聲學"* 資料夾旁邊看到*Wwise*資料夾。 它包含 Wwise 外掛程式以及您前面部署的混合器外掛程式的二進位檔案。

### <a name="extend-wwise-unreal-plug-in-functionality"></a>擴充 Wwise 的 Unreal 外掛程式功能
專案聲學虛幻外掛程式需要根據[這些準則](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)從 Wwise 虛幻外掛程式 API 中公開的其他行為。 我們已包含一個批次檔，用於自動執行修補過程。

* 外掛程式內部*\專案聲學\資源*，運行*PatchWwise.bat*。 以下示例圖像使用我們的聲學遊戲示例專案。

    ![具有修補 Wwise 突出顯示的腳本的 Windows 資源管理器視窗](media/patch-wwise-script.png)

* 如果您沒有安裝 DirectX SDK：根據您使用的 Wwise 版本，您可能需要注釋掉`DXSDK_DIR`*聲學遊戲_Plugins_Wwise_Source_AkAudio_AkAudio.build.cs*中包含的行：

    ![顯示"DXSDK"的代碼編輯器注釋了](media/directx-sdk-comment.png)

* 如果使用 Visual Studio 2019 編譯：要解決 Wwise 的連結錯誤，請手動更改`VSVersion`*聲學遊戲_Plugins_Wwise_Source_AkAudio_AkAudio.Build.cs*到**vc150 中的**預設值：

    ![顯示"VSVersion"的代碼編輯器更改為"vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>構建遊戲並檢查 Python 是否已啟用

1. 編譯您的遊戲，並確保它構建正確。 如果無法生成，請在繼續之前仔細檢查前面的步驟。

1. 在虛幻編輯器中打開專案。

    > [!NOTE]
    > **發動機外掛程式：** 如果您使用 ProjectAcoustics 作為引擎外掛程式，請確保它在"內置"外掛程式下啟用。

    您應該會看到一個新模式，該模式表示專案聲學已集成。

    ![聲學模式在虛幻中充滿](media/acoustics-mode-full.png)

1. 確認啟用了虛幻的 Python 外掛程式，以便編輯器集成正常工作。

    ![啟用虛幻編輯器中的 Python 擴展](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>設置 Wwise 專案以使用專案聲學

示例下載中包括 Wwise 專案。 我們建議您查看它以及這些說明。 本文後面的螢幕截圖來自此專案。

#### <a name="bus-setup"></a>匯流排設置
專案聲學虛幻外掛程式將查找匯流排上具有確切名稱`Project Acoustics Bus`的相關混合器外掛程式。 創建具有相同名稱的新音訊匯流排。 混合器外掛程式可在各種配置下工作。 但目前，我們假設它將僅用於混響處理。 此匯流排將攜帶使用聲學的所有源的混合混響信號。 它可以將上游混合到任何匯流排混合結構中。 示例下載中包括的 Wwise 示例專案在此處顯示了一個示例。

![顯示專案聲學巴士的 Wwise 巴士](media/acoustics-bus.png)

1. 將匯流排上的通道配置設置為*1.0*、2.0、4.0、5.1 或*2.0* *7.1*。 *4.0* *5.1* 任何其他設置都將導致匯流排上沒有輸出。

    ![專案聲學匯流排的通道配置選項](media/acoustics-bus-channel-config.png)

1. 進入專案聲學匯流排詳細資訊，並確保您可以看到**混合器外掛程式**選項卡。

    ![啟用了"專案聲學匯流排的混音器外掛程式"選項卡](media/mixer-tab-enable.png)

1. 轉到**混合器外掛程式**選項卡，並將專案聲學混合器外掛程式添加到匯流排。

    ![如何將專案聲學混合器外掛程式添加到 Wwise 匯流排的圖表](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>參與者-混合器層次結構設置
為了獲得最佳性能，專案聲學將音訊數位信號處理同時應用於所有源。 因此，外掛程式必須作為混合器外掛程式運行。 Wwise 要求混合器外掛程式位於輸出匯流排上，儘管輸出匯流排通常帶有幹輸出信號。 專案聲學要求幹信號通過輔助匯流排路由，而濕信號則在 上進行`Project Acoustics Bus`。 以下過程支援逐漸遷移到此信號流。

假設你有一個現有的專案，一個演員-混音器層次結構，其中包含*腳步*，*武器*，和其他在頂層。 每個都有相應的輸出匯流排，用於其幹混合。 假設您要遷移足跡以使用聲學。 首先，創建相應的輔助匯流排來承載作為足跡輸出匯流排子級的幹子混合。 例如，我們使用下圖中的"Dry"首碼來組織匯流排，儘管確切名稱並不重要。 你對腳步巴士的任何儀錶或效果仍將像以前那樣起作用。

![推薦的 Wwise 幹混合設置](media/wwise-dry-mix-setup.png)

接下來，修改足跡參與者-混合器的匯流排輸出結構，如下所示，*將專案聲學匯流排*設置為**輸出匯流排***，Dry_Footsteps*設置為使用者定義的輔助匯流排。

![推薦的 Wwise 演員混合匯流排設置](media/actor-mixer-bus-settings.png)

現在，所有的腳步聲都得到聲學處理，並在專案聲學匯流排上輸出其混響。 幹信號通過Dry_Footsteps路由，並像往常一樣進行空間化。

"聲學專案"僅適用于世界上具有 3D 位置的聲音。 在[Wwise 文檔](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)之後，必須按所示設置定位屬性。 **3D 空間化**設置可以根據需要*定位*或*位置 + 方向*。

![推薦的 Wwise 參與者定位設置](media/wwise-positioning.png)

你不能將**輸出匯流排**設置為其他匯流排，這些匯流排會混入專案*聲學匯流排*。 Wwise 對混合器外掛程式施加此要求。

如果希望足跡角色混合器層次結構中的兒童不使用聲學，則可以使用"覆蓋父級"來退出宣告。

如果您在遊戲中的任何演員混頻器上使用遊戲定義或使用者定義的發送來混響，請將其關閉，以避免應用混響兩次。

#### <a name="spatialization"></a>空間
預設情況下，專案聲學 Wwise 混合器外掛程式應用卷積混響，讓 Wwise 執行平移空間化。 在此預設僅混響配置中使用 Project 聲學時，可以在幹混合中使用任何通道配置和空間化方法。 因此，您可以將幾乎任何空間調整器與專案聲學混響混合和匹配。 您的選項包括[基於 Ambisonics 的雙體空間器](https://www.audiokinetic.com/products/ambisonics-in-wwise/)和[Windows 聲波](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)。
 
專案聲學包括一個可選的空間器，支援基於物件的高解析度 HRTF 渲染和平移。 在混合器外掛程式設置中選擇"**執行空間化**"核取方塊，並在*HRTF*或*平移*之間進行選擇。 此外，禁用使用者定義的輔助發送到所有幹匯流排，以避免通過專案聲學混合器外掛程式和 Wwise 進行兩次空間化。 空間化模式無法即時更改，因為它需要聲音庫再生。 重新開機虛幻，然後在選擇播放以集成混頻器外掛程式配置更改（如 **"執行空間化"** 核取方塊設置）之前重新生成聲庫。

![Wwise 混合器外掛程式空間化設置](media/mixer-spatial-settings.png)

遺憾的是，當前不支援其他基於物件的空間器外掛程式。 它們作為混合器外掛程式實現，Wwise 不允許將多個混合器外掛程式分配給單個執行元件混合器。  

### <a name="audio-setup-in-unreal"></a>虛幻中的音訊設置
1. 首先，你需要烘焙你的遊戲水準，以產生一個聲學資產，這將放置在*Content_聲學*。 請參閱[虛幻烘焙教程](unreal-baking.md)。 示例包中包括一些預烘焙的級別。

1. 在場景中創建聲學空間參與者。 僅在一個級別中創建這些參與者之一，因為它表示整個級別的聲學。

    ![在虛幻編輯器中創建聲學空間角色](media/create-acoustics-space.png)

1. 將烘焙聲學資料資產分配給聲學空間元件上的聲學資料插槽。 你的場景現在有音響！

    ![虛幻編輯器中的聲學資產分配](media/acoustics-asset-assign.png)

1. 添加空執行元件。 按如下方式配置它。

    ![虛幻編輯器顯示空參與者中的聲學元件使用方式](media/acoustics-component-usage.png)

       
    <sup>1</sup>向執行元件添加聲學音訊元件。 此元件將專案聲學功能添加到 Wwise 音訊元件。
        
    <sup>2</sup>預設情況下選擇"**在開始時播放"** 框。 此設置在級別啟動時觸發關聯的 Wwise 事件。</li>
         
    <sup>3</sup>使用 **"顯示聲學參數"** 核取方塊列印有關源的螢幕調試資訊。

    ![啟用調試值的聲源上的虛幻編輯器聲學面板](media/debug-values.png)

    <sup>4</sup>根據通常的 Wwise 工作流分配 Wwise 事件。
       
    <sup>5</sup>確保關閉 **"使用空間音訊**"。 如果對特定音訊元件使用"專案聲學"，則不能同時使用 Wwise 的空間音訊引擎進行聲學。</li>
       
您全都準備好了。 移動周圍的場景，探索聲學效果！

## <a name="next-steps"></a>後續步驟
* 嘗試[專案聲學不真實/Wwise設計教程](unreal-workflow.md)。
* 瞭解如何為遊戲場景[進行烘焙](unreal-baking.md)。
