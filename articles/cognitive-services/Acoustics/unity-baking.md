---
title: 聲場專案 Unity 聲場模擬教學課程
titlesuffix: Azure Cognitive Services
description: 本教學課程描述在 Unity 中運用聲場專案進行聲場模擬。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 310decf8053ea16ba46250ba3aabe81c9c254e5e
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "72243115"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>聲場專案 Unity 聲場模擬教學課程
此教學課程描述在 Unity 中透過運用聲場專案進行聲場模擬。

軟體需求：
* 適用於 Windows 或 MacOS 的 [Unity 2018.2+](https://unity3d.com)
* [Unity 專案中整合的聲場專案外掛程式](unity-integration.md)或[聲場專案 Unity 範例內容](unity-quickstart.md)
* *選擇性：* [Azure Batch 帳戶](create-azure-account.md)，透過使用雲端運算以加快聲場模擬速度

## <a name="open-the-project-acoustics-bake-window"></a>開啟聲場專案聲場模擬視窗
在 Unity 中，選取 [視窗]  功能表上的 [聲場]  。

![[視窗] 功能表上 [聲場] 選項已反白顯示的 Unity 編輯器](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>建立導航網格
聲場專案會使用導航網格放置聆聽者探查點以進行模擬。 您可以使用 Unity [導航網格工作流程](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) \(英文\)。 或者，您可以使用不同的 3D 模型套件來設計您自己的網格。

## <a name="mark-acoustic-scene-objects"></a>標示聲場場景物件
聲場專案依賴兩種類型的場景物件進行模擬：
- 在模擬中反射並遮蔽音效的物件
- 在模擬中限制接聽程式探查點的播放機導航網格

系統會使用 [Objects] \(物件\)  索引標籤標示這兩種物件類型。

由於標記物件只會將 *AcousticsGeometry* 或 *AcousticsNavigation* 元件新增至物件，因此您也可以使用[標準 Unity 元素工作流程](https://docs.unity3d.com/Manual/UsingComponents.html)標示或取消標示物件。 您只能標記網格渲染器和地形。 所有其他物件類型都會被忽略。 核取方塊會標示或取消標示所有受影響的物件。

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>標示聲場遮蔽和反射幾何
開啟 [Acoustics] \(聲場\)  視窗的 [Objects] \(物件\)  索引標籤。 如果物件應該遮蔽、反射或吸收聲音，請將它標示為 [Acoustics Geometry] (聲場幾何)  。 Acoustics Geometry (聲場幾何) 可以包括地面、牆面、屋頂、窗戶和窗戶玻璃、地毯及大型家具。 對於這些物件，您可以使用任意層級的複雜度。 由於場景已在模擬之前進行體素化，因此具高度細節之網格 (例如包含許多葉片的樹木) 的聲場模擬成本並不會高於較簡單的物件。

請勿包括不應該影響聲場的物件，例如看不見的遮蔽網格。

在聲場模擬結果中，會修正探查計算 (透過 [Probes]  \(探查\) 索引標籤) 期間的物件轉換。 如果稍後移動場景中任何標示的物件，則必須重做探查計算和聲場模擬。

### <a name="mark-the-navigation-mesh"></a>標示導航網格
聲場系統將會採用透過 Unity 工作流程建立的導航網格。 若要使用您自己的網格，請從 [Objects] \(物件\)  索引標籤標示它們。

### <a name="for-reference-the-objects-tab-parts"></a>參考：[Objects] \(物件\) 索引標籤組件
索引標籤頁面的各個組件 (如描述後圖示)：

1. 索引標籤選取按鈕 (已選取 [Objects] \(物件\)  索引標籤)。 請使用這些按鈕來逐步瀏覽進行聲場製作的各種步驟 (由左至右)。
1. 使用此索引標籤可執行之動作的簡短描述。
1. 階層視窗的可用篩選。 請使用這些選項將階層視窗篩選成顯示所指定類型的物件，以便輕鬆地標示這些物件。 如果您尚未針對聲場標示任何物件，則選取最後兩個選項將不會顯示任何內容。 不過，這些選項可協助您在標記物件之後，加以尋找它們。
1. 未選取任何物件時，此區段會顯示場景中所有物件的狀態。
    * 總計：作用中、非隱藏物件總數。
    * Ignored (已忽略)：不是 Mesh Renderer (網格轉譯器) 或 Terrain (地形) 的物件數目。
    * Mesh (網格)：Mesh Renderer (網格轉譯器) 物件數目。
    * Terrain (地形)：地形物件的數目。
    * Geometry (幾何)：標示為 [Acoustics Geometry]  \(聲場幾何\) 的 Mesh (網格) 或 Terrain (地形) 物件數目。
    * Navigation (導航)：標示為 [Acoustics Navigation]  \(聲場導航\) 的 Mesh (網格) 或 Terrain (地形) 物件數目。 此數目不包含 Unity NavMesh。
1. 顯示場景中「可標示的」物件 (僅限 Mesh Renderer (網格轉譯器) 和 Terrain (地形)) 總數。 使用這些核取方塊，將這些物件標示為 (為其新增適當的元件) 用於聲場的幾何或導航。
1. 未選取任何物件時，如有需要，此附註會提醒您選取物件來進行標示。 您也可以選取一個或同時選取兩個核取方塊來標示場景中的所有物件。
1. 已選取物件時，此區段只會顯示所選物件的狀態。
1. 「可標示的」所選物件總數。 選取或清除核取方塊只會標示或取消標示選取的物件。

如果您的場景中沒有任何選取的物件，[Objects]  \(物件\) 索引標籤看起來就會類似下圖。

![未選取任何內容的 [Acoustics Objects] \(聲場物件\) 索引標籤](media/objects-tab-no-selection-detail.png)

如果您的場景中有已選取的物件，則索引標籤看起來會類似下圖。

![包含選取項目的 [Acoustics Objects] \(聲場物件\) 索引標籤](media/objects-tab-selection-detail.png)

如果有些物件已標示而有些未標示，則適當的核取方塊會顯示「混合」值，如下圖所示。

![醒目提示混合選取圖示的 [Acoustics Objects] \(聲場物件\) 索引標籤](media/mixed-object-selection-detail.png)

選取核取方塊以標示所有項目。 取消選取核取方塊以取消標示所有物件。

您可以將物件標示成用於幾何和導航。

## <a name="select-acoustic-materials"></a>選取聲場材質
標示物件之後，請選取 [Materials]  \(材質\) 按鈕。 指派聲場材質。 聲場專案的材質系統會繫結至 Unity 視覺材質系統。 若要讓兩個物件擁有不同的聲場材質，它們必須有不同的視覺材質。

聲場材質選取項目會決定從每個表面反射回來的聲音能量多寡。 預設聲場材質的吸收率與鋼材相當。 聲場專案會根據視覺材質名稱建議材質。 您也可以將聲場材質「自訂」  指派給材質，以啟用可調整的吸收率係數滑桿。

室內所指定材質的殘響時間與其吸收係數成反比。 其中大多數材質的吸收值在 0.01 到 0.20 的範圍。 材質的吸收係數若是在此範圍外，則極具吸收性。

![顯示殘響時間與吸收係數負相關的圖形。](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>參考：[Materials] (材質) 索引標籤的組件
![Unity 中的 [Acoustics Materials] \(聲場材質\) 索引標籤](media/materials-tab-detail.png)
1. [Materials]  \(材質\) 按鈕會顯示此索引標籤。
2. 使用此索引標籤可執行之動作的簡短描述。
3. 如果選取此核取方塊，則只會列出標示為 [Acoustics Geometry]  \(聲場幾何\) 之物件所使用的材質。 否則，會列出場景中所使用的所有材質。
4. 您可以使用這些選項來變更功能表在 **Acoustics** \(聲場\) 資料行 (#6) 中的選項順序。 依**名稱**或**吸收率** (從低至高) 來排序聲場材質。
5. 場景中所使用的材質清單，依字母順序排序。 如果選取 [Show Marked Only] \(只顯示已標示的物件\)  (#3)，便只會列出標示為 [Acoustics Geometry] \(聲場幾何\)  之物件所使用的材質。 選取這裡的某個材質，以選取場景中使用該材質的所有物件。
6. 作為場景材質指派對象的聲場材質。 選取任何項目，以變更指派給該場景材質的聲場材質。 若要變更這些功能表的排序次序，請使用 [Sort Acoustics By] \(依聲場排序\)  選項 (#4)。
7. 左側欄 (#6) 中所選材質的聲場吸收係數。 值為 0 時表示完全反射 (沒有任何吸收)，而 1 則表示完全吸收 (沒有任何反射)。 除非所選材質為 [Custom]  \(自訂\)，否則無法變更吸收係數。
8. 針對標示為 [Custom]  \(自訂\) 的材質，會啟用滑桿。 您可以移動滑桿或輸入值來指派吸收係數。

## <a name="calculate-and-review-listener-probe-locations"></a>計算並檢閱聆聽者探查位置
在您指派材質之後，請切換至 [Probes]  \(探查\) 索引標籤。選取 [Calculate]  \(計算\) 以放置聆聽者探查點進行模擬。

### <a name="what-the-calculate-button-does"></a>[Calculate] \(計算\) 按鈕的功用是什麼
[Calculate]  \(計算\) 按鈕會使用您選取的聲場場景幾何來準備模擬場景：

- 它會從場景網格中取用幾何，然後計算出「體素體積」  。 體素體積是整個場景的體積，由小型立方體「體素」所組成。 體素大小取決於 [Simulation Resolution] \(模擬解析度\)  設定所控制的模擬頻率。 每個體素都會標示為 [open air] \(室外\) 或包含場景幾何。 如果體素包含幾何，體素上就會標示指派給該幾何之材質的吸收係數。
- 它會使用導航網格放置聆聽者探查點。 此演算法會平衡空間涵蓋範圍和模擬時間與檔案大小的競爭考慮。 它的目的是要確保窄的走廊和小型空間一律有部分涵蓋範圍。 典型的探查點計算範圍是從 100 (小型場景) 或至數千 (大型場景)。

視您場景的大小和機器的運算速度而定，這些計算可能會花費數分鐘的時間。

### <a name="review-voxel-and-probe-placement"></a>檢閱體素和探查位置
預覽體素資料和探查點位置，以確保您能夠準備好為場景進行聲場模擬。 不完整的導航網格或遺漏/有多餘聲場幾何等情況，通常很容易在預覽中發現。 您可以使用 **Gizmos** 功能表啟用或停用體素與探查位置。

![Unity 中的 Gizmos 功能表](media/gizmos-menu.png)

包含聲場幾何的體素會顯示為綠色立方體。 請探索您的場景，並確認應該是幾何的每個項目都有體素。 場景相機必須位於物件的大約 5 公尺以內，才能顯示體素。

如果您比較以 [Corse] \(粗略\) 解析度和 [Fine] \(精細\) 解析度建立的體素，將會發現粗略體素的體積是兩倍大。

![Unity 編輯器中的粗略體素預覽](media/voxel-cubes-preview.png)

模擬結果會在執行階段插入至聆聽者探查點位置之間。 請檢查您預期播放機會於場景中進入的所有地方附近是否都有探查點。

![Unity 編輯器中的探查預覽](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>重新命名場景時請謹慎
場景名稱可用來將場景連接至儲存探查點位置和體素化設定的檔案。 如果您在計算出探查點之後將場景重新命名，就會遺失材質指派和位置資料，而應該重新執行。

### <a name="for-reference-parts-of-the-probes-tab"></a>參考：[Probes] \(探查\) 索引標籤的組件
![Unity 中的 [Acoustics Probes] \(聲場探查\) 索引標籤](media/probes-tab-detail.png)

1. [Probes]  \(探查\) 按鈕會顯示此索引標籤。
2. 此索引標籤上可執行之動作的簡短描述。
3. 使用這些選項來設定粗略或精細模擬解析度。 [Coarse] \(粗略\) 的執行速度較快，但會有所取捨。 如需詳細資料，請參閱[聲場模擬解析](bake-resolution.md)。
4. 指定放置聲場資料檔案的位置。 選取 [...]  按鈕以存取資料夾選擇器。 預設位置為 *Assets/AcousticsData*。 此位置還會建立一個 *Editor* 子資料夾。 如需詳細資訊，請參閱此文章稍後的[聲場模擬中新增的資料檔案](#Data-Files)。
5. 此處指定的前置詞是用來命名此場景的資料檔案。 預設值為「Acoustics_[場景名稱]  」。
6. 在計算探查之後，我們剛才描述的控制項就會停用。 按一下 [Clear]  \(清除\) 按鈕以清除計算並啟用控制項，以便您能夠使用新的設定來重新計算。
7. 選取 [Calculate]  \(計算\) 以將場景體素化並計算探查點位置。 計算是在您的本機電腦上完成。 您必須先完成此動作，才能進行聲場模擬。

在此版本的聲場專案中，無法手動放置探查。 在 [Probes]  \(探查\) 索引標籤上使用自動化程序。

如需粗略與精細解析度的詳細資訊，請參閱[聲場模擬解析](bake-resolution.md)。

## <a name="bake-your-scene-by-using-azure-batch"></a>使用 Azure Batch 來對場景進行聲場模擬
您可以使用 Azure Batch 服務，在雲端中的計算叢集上對場景進行聲場模擬。 聲場專案 Unity 外掛程式會直接連線至 Azure Batch，以針對每個聲場模擬具現化、管理及卸除 Azure Batch 叢集。 在 [Bake]  \(聲場模擬\) 索引標籤上，輸入您的 Azure 認證、選取叢集機器類型和大小，然後選取 [Bake]  \(聲場模擬\)。

### <a name="for-reference-parts-of-the-bake-tab"></a>參考：[Bake] (聲場模擬) 索引標籤的組件
![Unity 中的 [Acoustics Bake] \(聲場模擬\) 索引標籤](media/bake-tab-details.png)

1. [Bake]  \(聲場模擬\) 按鈕會顯示此索引標籤。
2. 此頁面上可執行之動作的簡短描述。
3. 建立您的 Azure 帳戶之後，在這些欄位中輸入您的 Azure 認證。 如需詳細資訊，請參閱[建立 Azure Batch 帳戶](create-azure-account.md)。
4. 適用於聲場工具組的 Docker 影像標籤欄位。
5. 開啟 Azure 入口網站來管理您的訂用帳戶、監視使用量和檢視帳單資訊。
6. 指定要用於計算的 Azure Batch 計算節點類型。 此節點類型必須是您 Azure 資料中心位置支援的類型。 如果不確定，請保持為 **Standard_F8s_v2**。
7. 要用於此計算的節點數目。 這個數目會影響聲場模擬時間。 它受限於您的 Azure Batch 核心配置。 預設配置只允許兩個 8 核心節點或一個 16 核心節點，但配置可以擴充。 如需有關核心配置條件約束的詳細資訊，請參閱[建立 Azure Batch 帳戶](create-azure-account.md)。
8. 選取此核取方塊，以將計算集區設定為使用[低優先順序節點](https://docs.microsoft.com/azure/batch/batch-low-pri-vms) \(部分機器翻譯\)。 低優先順序計算節點的成本更低。 但它們不一定隨時可用，或可能隨時被搶占。
9. 您場景的探測計數，如 [Probes] \(探測\)  索引標籤上所計算。探查數目會決定必須在雲端執行的模擬次數。 您指定的節點數目不可超過探測數目。
10. 您的作業在雲端中執行所花的時間預估，不包括節點啟動時間。 在作業開始執行之後，此欄位會顯示直到您取回結果為止的估計時間。
11. 執行模擬所需的運算時間量總計。 此值是將在 Azure 中使用的節點計算時間量總計。 如需詳細資訊，請參閱此文章稍後的[預估 Azure 聲場模擬成本](#Estimating-bake-cost)。
12. 此訊息會告訴您在作業結束之後，聲場模擬結果的儲存位置。
13. (僅限進階使用：)  此按鈕會強制 Unity 忘記您所提交的聲場模擬。 例如，如果您使用另一部電腦來下載結果，請選取 [Clear State]  \(清除狀態\) 按鈕，以忘記該作業。 結果檔案準備就緒後，將「不會」  下載。 這與取消作業的方式不同。  作業如果正在執行，將會繼續在雲端執行。
14. 選取此按鈕以將聲場模擬提交至雲端。 當作業正在執行時，此按鈕會變為 [Cancel Job] \(取消作業\)  。
15. 選取此按鈕，以準備[在電腦上處理聲場模擬](#Local-bake)。
16. 此區域會顯示製作狀態。 當聲場模擬完成時，它會顯示「已下載」。

您一律可以在 [Azure 入口網站](https://portal.azure.com)中取得有關作用中作業、計算集區及儲存體的完整資訊。

當作業正在執行時，[Bake] \(聲場模擬\)  按鈕標籤會變更為 [Cancel Job] \(取消作業\)  。 使用此按鈕即可取消進行中的作業。 系統將提示您確認。 取消作業無法復原。 當您取消時，將無法取得任何結果，且您仍然需支付任何已使用的 Azure 計算時間費用。

在您開始進行聲場模擬之後，即可關閉 Unity。 視專案、節點類型與節點數目而定，雲端製作可能會花費數小時的時間。 當您重新載入專案並開啟 [Acoustics] \(聲場\) 視窗時，製作作業狀態將會更新。 如果作業完成，系統將會下載輸出檔。

基於安全性，Azure 認證會儲存在您的本機電腦上，並與您的 Unity 編輯器建立關聯。 它們僅供用來與 Azure 建立安全連線。

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>在 Azure 入口網站上尋找執行中作業的狀態

1. 在 [Bake]  \(聲場模擬\) 索引標籤上尋找製作作業識別碼。

    ![[Bake] \(聲場模擬\) 索引標籤上反白顯示的 Unity 聲場模擬作業識別碼](media/unity-job-id.png)  

2. 開啟 [Azure 入口網站](https://portal.azure.com)，移至用於聲場模擬的 Batch 帳戶，然後選取 [作業]  。

    ![Azure 入口網站中的 [作業] 連結](media/azure-batch-jobs.png)  

3. 在作業清單中搜尋作業識別碼。

   ![Azure 入口網站中反白顯示的聲場模擬作業狀態](media/azure-bake-job-status.png)  

4. 選取作業識別碼，以查看相關工作的狀態和整體作業狀態。

   ![聲場模擬工作狀態](media/azure-batch-task-state.png)  


### <a name="estimate-azure-bake-cost"></a><a name="Estimating-bake-cost"></a> 評估 Azure 聲場模擬成本

若要評估聲場模擬的成本，請從 [預估的計算費用]  值開始，這是持續時間。 針對您選取的 [VM 節點類型]  ，將該值乘以當地貨幣的每小時成本。 請注意，此結果將不會包含讓節點啟動並開始執行所需的節點時間。

例如，假設您針對節點類型選取 **Standard_F8s_v2**，其成本為每小時 0.40 美元。 如果 [預估的計算費用]  是 3 小時和 57 分鐘，則執行作業的預估成本會是 0.40 美元 * ~ 4 小時 = ~ 1.60 美元。 實際成本可能會稍微高一點，因為讓節點啟動需要額外的時間。

在 [Azure Batch 定價](https://azure.microsoft.com/pricing/details/virtual-machines/linux)尋找每小時節點成本。 (選取 [計算最佳化]  或 [高效能計算]  作為類別目錄)。

## <a name="bake-your-scene-on-your-pc"></a><a name="Local-bake"></a> 在電腦上針對場景進行聲場模擬
您也可以在電腦上針對場景進行聲場模擬。 此方法很適合用於在建立 Azure Batch 帳戶之前，實驗小型場景的聲場。 但請注意，根據場景的大小而定，本機聲場模擬可能需要很長的時間。

### <a name="minimum-hardware-requirements"></a>最低硬體需求
* 具備至少 8 個核心和 32 GB RAM 的 x86-64 處理器
* [已啟用 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)，以執行 Docker

例如，在 Intel Xeon E5-1660 @ 3 GHz 和 32 GB RAM 的 8 核心電腦上測試：
* 具有 100 個探查的小型場景，大約需要 2 小時進行粗略聲場模擬，或需要 32 小時進行精細聲場模擬。
* 具有 1,000 個探查的中型場景，大約需要 20 小時進行粗略聲場模擬，或需要 21 天進行精細聲場模擬。

### <a name="set-up-docker"></a>設定 Docker
在將會處理模擬的電腦上安裝和設定 Docker：
1. [安裝 Docker Desktop](https://www.docker.com/products/docker-desktop) \(英文\)。
2. 開啟 Docker 設定，移至 [進階]  ，並設定至少 8 GB RAM 的資源。 您可以配置給 Docker 的 CPU 越多，聲場模擬完成的速度就越快。  
![Docker 設定範例](media/docker-settings.png)
1. 移至 [共用磁碟機]  ，然後開啟用於處理之磁碟機的共用。  
![Docker 共用磁碟機選項](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>執行本機聲場模擬
1. 在 [Bake]  \(聲場模擬\) 索引標籤上，選取 [Prepare Local Bake]  \(準備本機聲場模擬\) 按鈕。然後，選取要儲存輸入檔和執行指令碼的資料夾位置。 然後，您可以在任何電腦上執行聲場模擬，只要它符合最低硬體需求，並藉由將資料夾複製到該電腦來安裝 Docker。
2. 若要啟動模擬，請在 Windows 上執行 *runlocalbake.bat* 指令碼，或在 MacOS 上執行 *runlocalbake.sh* 指令碼。 該指令碼會使用模擬處理所需的工具組擷取聲場專案 Docker 映像，並啟動模擬。
3. 模擬完成後，請將產生的 *.ace* 檔案複製回您的 Unity 專案。 若要確定 Unity 會將它辨識為二進位檔案，請將 ".bytes" 附加到檔案副檔名 (例如，"Scene1.ace.bytes")。 模擬的詳細記錄會儲存在 *AcousticsLog.txt* 中。 如果您遇到任何問題，請檢查此檔案以協助診斷問題。

## <a name="data-files-added-by-the-bake-process"></a><a name="Data-Files"></a> 由聲場模擬程序新增的資料檔案

聲場模擬程序進行期間會建立下列四個資料檔。 其中包含模擬結果並隨附於您的標題。 其他存放區 Unity 編輯器相關資料。

模擬結果：
* *Assets/AcousticsData/Acoustics\_[SceneName].ace.bytes*：此檔案是執行階段查閱資料表。 其中包含模擬結果和體素化聲場場景元素。 您可以在 [探查]  索引標籤上變更此檔案的名稱和位置。

   請注意不要刪除模擬結果檔案。  除非針對場景重新進行聲場模擬，否則無法復原此檔案。

編輯器資料檔：
* *Assets/Editor/[SceneName]\_AcousticsParameters.asset*：這個檔案會儲存您在聲場 UI 欄位中輸入的資料。 您無法變更此檔案的名稱和位置。
* *Assets/AcousticsData/Editor/Acoustics_[SceneName].vox*：此檔案會儲存當您選取 [Probes]  \(探查\) 索引標籤上的 [Calculate]  \(計算\) 按鈕時所計算的體素化聲場幾何和材質屬性。您可以在 [探查]  索引標籤上變更此檔案的名稱和位置。
* *Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml*：當您選取 [Calculate]  \(計算\) 時，這個檔案會儲存所計算的模擬參數。 您可以在 [探查]  索引標籤上變更此檔案的名稱和位置。

## <a name="set-up-the-acoustics-lookup-table"></a>設定聲場查閱資料表
將 [Project Acoustics]  \(聲場專案\) 預製項目從專案面板拖放到您的場景中：

![Unity 中的 [Acoustics] \(聲場\) 預製項目](media/acoustics-prefab.png)

選取 [ProjectAcoustics]  遊戲物件以前往其偵測器面板。 指定聲場模擬結果的位置 (.ace 檔案，在 *Assets/AcousticsData* 中)：將它拖曳到聲場管理員指令碼中，或選取文字方塊旁邊的圓形按鈕。

![Unity 中的 [聲場管理員] 預製項目](media/acoustics-manager.png)

## <a name="next-steps"></a>後續步驟
* 探索 [Unity 設計控制項](unity-workflow.md)。
* 探索[聲場專案設計概念](design-process.md)。
