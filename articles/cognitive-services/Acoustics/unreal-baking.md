---
title: 聲場專案 Unreal 聲場模擬教學課程
titlesuffix: Azure Cognitive Services
description: 本文件說明使用 Unreal 編輯器擴充來提交聲場模擬的流程。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 7a868a5f9b06499e23710399733b0659d97f900d
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "68854894"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>聲場專案 Unreal 聲場模擬教學課程
本文件說明使用 Unreal 編輯器擴充來提交聲場模擬的流程。

有五個進行製作的步驟：

1. 建立或標記玩家導航網格
2. 標記聲場幾何
3. 將聲場材質屬性指派給幾何
4. 預覽探查位置
5. 製作

## <a name="open-the-project-acoustics-editor-mode"></a>開啟聲場專案編輯器模式

將聲場專案外掛程式套件匯入到您的專案中。 如需此作業的說明，請參閱 [Unreal 整合](unreal-integration.md)主題。 整合外掛程式後，請按一下新的 [Acoustics Mode] \(聲場模式\) 圖示以開啟 [Acoustics] \(聲場\) UI。

![Unreal 編輯器的 [聲場模式選項] 螢幕擷取畫面](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>標記聲場的動作項目

[Objects] \(物件\) 是開啟 [Acoustics Mode] \(聲場模式\) 時所顯示的第一個索引標籤。 請使用此索引標籤來標記關卡的動作項目，這會將 [AcousticsGeometry]  或 [AcousticsNavigation]  標籤新增至那些動作項目。

在 [World Outliner] \(世界大綱工具\) 中，選取一或多個物件，或使用 [Bulk Selection]  \(大量選取項目\) 區段來協助選取特定類別的所有物件。 選取物件之後，請使用 [Tagging]  \(標記\) 區段將所需標籤套用至選取的物件。

如果沒有 **AcousticsGeometry** 也沒有 **AcousticsNavigation** 標記，將會在模擬中忽略它。 只支援靜態網格、導航網格和地貌。 如果您標記任何其他項目，將會忽略它。

### <a name="for-reference-the-objects-tab-parts"></a>參考：[Objects] \(物件\) 索引標籤組件

![Unreal 中 [聲場物件] 索引標籤的螢幕擷取畫面](media/unreal-objects-tab-details.png)

1. 索引標籤選取按鈕 (已選取 [Objects] \(物件\)  索引標籤)。 請使用這些按鈕來逐步完成進行聲場模擬的各種步驟 (由上至下)。
2. 使用此頁面時所需執行之操作的簡短描述。
3. 可用於選取該關卡之動作項目的選取器。
4. 按一下 [Select]  \(選取\) 會選取關卡中至少符合其中一個所選動作項目類型的所有物件。
5. 按一下 [Deselect all]  \(全部取消選取\) 將會清除目前的選取項目。 這相當於按下 Esc 鍵。
6. 使用這些選項按鈕來選擇是否要將幾何或導航標籤套用至選取的動作項目。
7. 按一下 [Tag]  \(標記\) 會將選取的標籤新增至所有目前已選取的動作項目。
8. 按一下 [Untag]  \(解除標記\) 會將選取的標籤從所有目前已選取的動作項目上移除。
9. 按一下 [Select Tagged]  \(選取已標記\) 會清除目前選取項目，然後選取具目前所選之標籤的所有動作項目。
10. 這些統計資料會顯示每個標籤類型已標記多少動作項目。

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>標記聲場遮蔽和反射幾何

開啟 [Acoustics] \(聲場\) 視窗的 [Objects] \(物件\) 索引標籤。 如果物件應該遮蔽、反射或吸收聲音，請將它標記為 [Acoustics Geometry] \(聲場幾何\)。 聲場幾何可以包括地面、牆面、屋頂、窗戶和窗戶玻璃、地毯及大型家具。 對於這些物件，您可以使用任意層級的複雜度。 由於場景已在模擬之前進行體素化，因此具高度細節之網格 (例如包含許多小型葉片的樹木) 的聲場模擬成本並不會高於較簡單的物件。

請勿包括不應該影響聲場的物件，例如看不見的遮蔽網格。

在聲場模擬結果中，會修正進行探查計算 (透過下方的 [Probes] \(探查\) 索引標籤) 時的物件轉換。 如果移動場景中任何已標示的物件，將必須重做探查計算及重新製作場景。

### <a name="create-or-tag-a-navigation-mesh"></a>建立或標記導航網格

導航網格可用來放置模擬探查點。 您可以使用 Unreal 的[導航網格界線體積](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html) \(英文\)，或是自行指定導航網格。 您必須先至少將一個物件標記為 [Acoustics Navigation]  \(聲場導航\)。 如果您使用 Unreal 的導航網格，請先確定您已建置它。

### <a name="acoustics-volumes"></a>聲場體積 ###

在您的導航區域上，您可以使用 [聲場體積]  進行進一步的進階自訂。 [聲場體積]  是您可以新增至場景的動作項目，可讓您選取導航網格要包含和略過的區域。 此動作項目會公開可以在「包含」和「排除」之間切換的屬性。 「包含」體積可確保只考慮其內部導航網格的區域，而「排除」體積會將這些區域標示為略過。 「排除」體積一律會在「包含」體積之後套用。 務必透過 [物件] 索引標籤中的一般程序，將 [聲場體積]  標記為 [聲場導航]  。這些動作項目「不會」自動加上標記。

![Unreal 中聲場體積屬性的螢幕擷取畫面](media/unreal-acoustics-volume-properties.png)

「排除」體積的主要用意是要對不放入探查的位置提供細緻控制，以強化資源使用狀況。

![Unreal 中排除聲場體積的螢幕擷取畫面](media/unreal-acoustics-volume-exclude.png)

「包含」體積適合用於建立場景的手動區段，例如您想要將場景分解成多個聲場區域時。 比方說，如果您有大型場景 (多達數平方公里)，而且有兩個想要模擬聲場的感興趣區域。 您可以在場景中繪製兩個大型「包含」體積，且分別為每個體積產生 ACE 檔案。 然後在遊戲中，當播放程式接近每個圖格時，您可以使用結合藍圖呼叫的觸發程序體積來載入適當的 ACE 檔案。

[聲場體積]  只會限制導航，「不會」限制幾何。 在執行聲波模擬時，「包含」**聲場體積**內的每項探查仍會放入體積外部的所有必要幾何。 因此，遮蔽或其他聲場中不應該有任何由於播放程式從一個區段跨到另一個區段所造成的不連貫情形。

## <a name="select-acoustic-materials"></a>選取聲場材質

標記物件之後，請按一下 [Materials]  \(材質\) 按鈕以移至 [Materials] \(材質\) 索引標籤。此索引標籤將用來指定關卡中每個材質的材質屬性。 在標記任何動作項目之前，它會是空白的。

聲場材質會控制從每個表面反射回來的聲音能量多寡。 預設聲場材質的吸收率與混凝土相當。 聲場專案會根據場景材質名稱建議材質。

室內所指定材質的殘響時間與其吸收係數成反比，其中大多數材質的吸收值在 0.01 到 0.20 的範圍。 吸收係數高於此範圍的材質會具備極佳的吸收能力。 例如，如果室內音的殘響太大，請將牆面、地板或天花板的聲場材質變更成吸收性更高的材質。 聲場材質指派會套用至使用該場景材質的所有動作項目。

![顯示殘響時間與吸收係數負相關的圖形](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>參考：[Materials] (材質) 索引標籤的組件

![Unreal 中 [聲場物件] 索引標籤的螢幕擷取畫面](media/unreal-materials-tab-details.png)

1. 用來顯示此頁面的 [Materials] \(材質\)  索引標籤按鈕，。
2. 使用此頁面時所需執行之操作的簡短描述。
3. 關卡中所使用材質的清單，擷取自被標記為 [AcousticsGeometry]  的動作項目。 按一下這裡的某個材質，就會選取場景中使用該材質的所有物件。
4. 顯示作為場景材質指派對象的聲場材質。 按一下下拉式清單，即可將場景材質重新指派給不同的聲場材質。
5. 顯示在上一欄中所選材質的聲場吸收係數。 值為零時表示完全反射 (沒有任何吸收)，值為 1 時則表示完全吸收 (沒有任何反射)。 變更此值會將聲場材質 (步驟 4) 更新為 [Custom]  \(自訂\)。

若要變更場景中的材質，您必須切換 [聲場專案] 外掛程式中的索引標籤，以查看 [材質]  索引標籤中反映的變更。

## <a name="calculate-and-review-listener-probe-locations"></a>計算並檢閱聆聽者探查位置

指派材質之後，請切換至 [Probes] \(探查\)  索引標籤。

### <a name="for-reference-parts-of-the-probes-tab"></a>參考：[Probes] \(探查\) 索引標籤的組件

![Unreal 中 [聲場探查] 索引標籤的螢幕擷取畫面](media/unreal-probes-tab-details.png)

1. 用來顯示此頁面的 [Probes] \(探查\)  索引標籤按鈕
2. 使用此頁面時所需執行之操作的簡短描述
3. 使用此項目來選擇 [Coarse] \(粗略\) 或 [Fine] \(精細\) 的模擬解析度。 [Coarse] \(粗略\) 的執行速度較快，但會有所取捨。 如需詳細資料，請參閱下面的[聲場模擬解析](bake-resolution.md)。
4. 使用此欄位來選擇聲場資料檔的預期放置位置。 按一下具有 "..." 的按鈕以使用資料夾選擇器。 如需有關資料檔的詳細資訊，請參閱下面的[資料檔](#Data-Files)。
5. 此場景的資料檔將會採用這裡提供的前置詞來命名。 預設值為 "[Level Name]_AcousticsData"。
6. 按一下 [Calculate]  \(計算\) 按鈕以將場景體素化並計算探查點位置。 這會在您電腦本機完成，且必須在進行製作之前完成。 完成探查的計算之後，系統會停用上述控制項，且此按鈕將會變更為顯示 [Clear]  \(清除\)。 按一下 [Clear] \(清除\)  按鈕以清除計算並啟用控制項，以便讓您能夠使用新的設定來重新計算。

探查必須透過 [Probes]  \(探查\) 索引標籤中所提供的自動化流程來放置。


### <a name="what-the-calculate-button-calculates"></a>[Calculate] \(計算\) 按鈕會計算什麼

[Calculate]  \(計算\) 按鈕會取用您目前為止所提供的所有資料 (幾何、導航、材質及粗略/精細設定)，然後進行下列數個步驟：

1. 它會從場景網格中取用幾何，然後計算出體素體積。 體素體積是一個圍繞您整個場景的 3D 體積，由小型的立方體「體素」所組成。 體素的大小取決於 [Simulation Resolution] \(模擬解析度\)  設定所設定的模擬頻率。 每個體素都會標示為 [open air] \(室外\) 或包含場景幾何。 如果體素包含幾何，體素上就會標示指派給該幾何之材質的吸收係數。
2. 接著，它會使用導航資料來計算出玩家可能前往的聲場相關位置。 它會嘗試找出合理的一小組位置，這些位置包括較小的區域，例如門口和走廊，再到室內及開放空間。 就小型場景而言，這通常會少於 100 個位置，而大型場景則可能會有最多上千個位置。
3. 針對它所計算的每個最終聆聽者位置，它會決定一些參數，例如空間的「開放度」和所在房間的大小等。
4. 這些計算的結果會儲存在您指定位置的檔案中 (請參閱下面的[資料檔](#Data-Files))

視您場景的大小和機器的運算速度而定，這些計算可能會花費數分鐘的時間。

在這些計算完成之後，您可以預覽體素資料和探查點位置，以協助確保製作會提供您理想的結果。 諸如不良導航網格或遺漏/有多餘幾何等情況，在預覽中通常都可迅速發現，讓您能夠加以更正。


## <a name="debug-display"></a>偵錯顯示

探查計算完成之後，稱為 **AcousticsDebugRenderer** 的新動作項目會出現在 [World Outliner] \(世界大綱工具\) 中。 選取 [Render Probes]  \(轉譯探查\) 和 [Render Voxels]  \(轉譯體素\) 核取方塊，將會在編輯器檢視區內啟用偵錯顯示。

![Unreal 編輯器中顯示 [聲場偵錯轉譯器] 動作項目的螢幕擷取畫面](media/acoustics-debug-renderer.png)

如果您沒有看到任何體素或探查重疊在關卡上，請確定已在檢視區中啟用即時轉譯。

![Unreal 中即時轉譯選項的螢幕擷取畫面](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>體素

體素在場景視窗中會顯示成圍繞參與之幾何的綠色立方體。 不會顯示只含空氣的體素。 有一個大型綠色方塊會圍繞您的整個場景，代表了在模擬中將使用的完整體素體積。
請在場景中移動，並確認聲場遮蔽的幾何具有體素。 此外，請檢查非聲場物件 (例如遮蔽網格) 是否未體素化。 場景相機必須位於物件的大約 5 公尺以內，才能顯示體素。

如果您比較以 [Corse] \(粗略\) 解析度和 [Fine] \(精細\) 解析度建立的體素，將會發現粗略體素的體積是兩倍大。

![Unreal 編輯器中聲場體素預覽的螢幕擷取畫面](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>探查點

探查點相當於可能的玩家 (聆聽者) 位置。 模擬時，模擬作業會計算聲場，並將所有可能的來源位置連接到每個探查點。 在執行階段期間，聆聽者位置會插補至附近的探查點之間。

請務必檢查您預期玩家會於場景中行經的所有地方是否都有探查點。 聲場專案引擎會將探查點放置在導航網格上，且其將無法被移動或編輯，因此請檢查探查點來確定導航網格已涵蓋所有可能的玩家位置。

![Unreal 中聲場探查預覽的螢幕擷取畫面](media/unreal-probes-preview.png)

如需粗略與精細解析度的比較詳細資料，請參閱[聲場模擬解析](bake-resolution.md)。

## <a name="bake-your-level-using-azure-batch"></a>使用 Azure Batch 來對關卡進行聲場模擬

您可以使用 Azure Batch 服務搭配雲端中的計算叢集來對場景進行聲場模擬。 聲場專案 Unreal 外掛程式會直接連線至 Azure Batch，以針對每個聲場模擬具現化、管理及卸除 Azure Batch 叢集。 在 [Bake] \(聲場模擬\) 索引標籤中，選取叢集機器類型和大小，然後按一下 [Bake] \(模擬聲場\)。

### <a name="for-reference-parts-of-the-bake-tab"></a>參考：製作索引標籤的組件

![Unreal 中 [聲場製作] 索引標籤的螢幕擷取畫面](media/unreal-bake-tab-details.png)

1. 用來顯示此頁面的 [Bake] \(製作\) 索引標籤按鈕。
2. 要在此頁面上執行之操作的簡短描述。
3. 建立您的 Azure 帳戶之後，用來輸入 Azure 認證的欄位。 如需詳細資訊，請參閱[建立 Azure Batch 帳戶](create-azure-account.md)。
4. 啟動 Azure 入口網站來管理您的訂用帳戶、監視使用量和檢視帳單資訊等。 
5. 要用於計算的 Azure Batch 計算節點類型。 此節點類型必須是您 Azure 資料中心位置支援的類型。 如果不確定，請保持為 **Standard_F8s_v2**。
6. 要用於此計算的節點數目。 您在這裡輸入的數目會影響完成製作的時間，並且會受到 Azure Batch 核心配置限制。 預設配置只允許兩個 8 核心節點或一個 16 核心節點，但可以擴充。 如需有關核心配置條件約束的詳細資訊，請參閱[建立 Azure Batch 帳戶](create-azure-account.md)。
7. 選取此核取方塊，以將計算集區設定為使用[低優先順序節點](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)。 低優先順序的計算節點具有更低的成本，但它們可能無法提供使用，或可能隨時會被搶佔。
8. 您的作業在雲端執行時預期將耗用的時間長度。 這不包括節點啟動時間。 作業開始執行之後，這大約會等於取得結果所需的時間。 請注意，這只是一個預估值。
9. 執行模擬所需的運算時間量總計。 這是將在 Azure 中使用的節點計算時間量總計。 如需有關使用此值的詳細資訊，請參閱下面的[預估製作成本](#Estimating-bake-cost)。
10. 按一下 [Bake] \(製作\) 按鈕以將製作項目提交至雲端。 當作業正在執行時，這會改為顯示 [Cancel Job] \(取消作業\)  。 如果此索引標籤上有任何錯誤，或如果 [Probes]  \(探查\) 索引標籤上的工作流程尚未完成，此按鈕將會停用。
11. 您場景的探測計數，如 [Probes] \(探測\)  索引標籤上所計算。探測數目會決定需要在雲端執行的模擬次數。 您指定的節點數目不可超過探測數目。
12. 此訊息會告訴您作業目前的狀態，或是此索引標籤中是否有任何錯誤及那些錯誤的資訊。

您一律可以在 [Azure 入口網站](https://portal.azure.com)取得有關作用中作業、計算集區及儲存體的完整資訊。

當作業正在執行時，[Bake] \(製作\)  按鈕會變更為 [Cancel Job] \(取消作業\)  。 使用此按鈕即可取消進行中的作業。 取消作業並無法復原，您將無法取得任何結果，且仍需支付在取消前所使用的任何 Azure 計算時間費用。

在您開始聲場模擬之後，便可以關閉 Unreal。 視專案、節點類型與節點數目而定，雲端製作可能會花費數小時的時間。 當您重新載入專案並開啟 [Acoustics] \(聲場\) 視窗時，製作作業狀態將會更新。 如果作業已完成，系統將會下載輸出檔。

Azure 認證會安全地儲存在您的本機電腦上，並與您的 Unreal 專案建立關聯。 它們僅供用來與 Azure 建立安全連線。

### <a name="estimating-azure-bake-cost"></a><a name="Estimating-bake-cost"></a> 正在評估 Azure 製作成本

若要預估所指定製作項目的成本，請取用在 [Estimated Compute Cost] \(預估計算成本\)  顯示的值 (這是一個持續時間)，然後將其乘以您所選 [VM Node Type] \(VM 節點類型\)  的每小時成本 (採用您的當地貨幣)。 此結果將不會包含讓節點啟動並開始執行所需的節點時間。 例如，如果您選取 [Standard_F8s_v2]  作為您的節點類型，其成本為每小時 0.40 美元，而 [Estimated Compute Cost] \(預估計算成本\) 為 3 小時又 57 分鐘，則執行作業的預估成本將會是 0.40 美元 * ~4 小時 = ~1.60 美元。 實際成本可能會稍微高一點，因為讓節點啟動需要額外的時間。 您可以在 [Azure Batch 定價](https://azure.microsoft.com/pricing/details/virtual-machines/linux) 頁面上找到每小時節點成本 (針對類別選取 [計算最佳化] 或 [高效能計算])。

### <a name="reviewing-the-bake-results"></a>檢閱製作結果

在製作完成之後，請執行執行階段外掛程式，以確認體素和探查點是否都位於其預期位置。

## <a name="data-files"></a><a name="Data-Files"></a>資料檔

此外掛程式會在各種點上建立四個資料檔。 執行階段只需要其中之一，且系統會將它置於您專案的 Content/Acoustics 資料夾中，並自動新增至您專案的套件路徑。 其他三個資料檔則會被置於 Acoustics Data 資料夾內，且不會被封裝。

* **[專案]/Config/ProjectAcoustics.cfg**：此檔案會儲存您在 [Acoustics Mode] \(聲場模式\) UI 的欄位中所輸入的資料。 您無法變更此檔案的位置和名稱。 此檔案中儲存了其他會影響製作的值，但它們是供進階使用者使用的，而不應該予以變更。
* **[專案]/Content/Acoustics/[LevelName]\_AcousticsData.ace**：此檔案是在製作模擬期間建立的檔案，包含了執行階段用來轉譯您場景聲場的查閱資料。 您可以使用 [Probes] \(探查\)  索引標籤上的欄位，來變更此檔案的位置和名稱。如果您想要在建立檔案之後重新命名檔案，請從您的 Unreal 專案刪除 UAsset，在 [檔案總管] 的 Unreal 外部重新命名檔案，然後將此檔案重新匯入至 Unreal 以產生新的 UAsset。 UAsset 自行重新命名無法運作。
* **[專案]/Plugins/ProjectAcoustics/AcousticsData/[關卡名稱]\_AcousticsData.vox**：這個檔案會儲存已體素化的聲場幾何和材質屬性。 這是使用 [Probes]  \(探查\) 索引標籤上的 [Calculate]  \(計算\) 按鈕所計算。您可以使用 [Probes] \(探查\)  索引標籤上的欄位，來變更此檔案的位置和名稱。
* **[專案]/Plugins/ProjectAcoustics/AcousticsData/[關卡名稱]\_AcousticsData\_config.xml**：這個檔案會儲存使用 [Probes]  \(探查\) 索引標籤上的 [Calculate]  \(計算\) 按鈕所計算的參數。您可以使用 [Probes] \(探查\)  索引標籤上的欄位，來變更此檔案的位置和名稱。

請小心不要刪除從 Azure 下載的 *.ace 檔案。 除非重新製作場景，否則無法復原此檔案。

## <a name="next-steps"></a>後續步驟
* 瀏覽 [Unreal 的設計控制項](unreal-workflow.md)
* 瀏覽[聲場專案設計概念](design-process.md)

