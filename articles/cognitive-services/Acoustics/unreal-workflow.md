---
title: 聲場專案 Unreal 設計教學課程
titlesuffix: Azure Cognitive Services
description: 本教學課程描述 Unreal 和 Wwise 中聲場專案的設計工作流程。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 817a11171c5b4b4ef205e5fbb04f9b6d6d85b248
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "68854253"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>聲場專案 Unreal/Wwise 設計教學課程
本教學課程描述 Unreal 和 Wwise 中聲場專案的設計設定與工作流程。

軟體必要條件：
* 具有聲場專案 Wwise 和 Unreal 外掛程式的 Unreal 專案

若要取得具有聲場專案的 Unreal 專案，您可以：
* 請遵循[聲場專案 Unreal 整合](unreal-integration.md)指示，將聲場專案新增至您的 Unreal 專案
* 或是，使用[聲場專案範例專案](unreal-quickstart.md)。

## <a name="setup-project-wide-wwise-properties"></a>設定整個專案的 Wwise 屬性
Wwise 有通用的障礙物和遮擋曲線，會影響聲場專案外掛程式驅動 Wwise 音訊 DSP 的方式。

### <a name="design-wwise-occlusion-curves"></a>設計 Wwise 遮擋曲線
當聲場專案在作用中時，它會回應您在 Wwise 中設定的遮擋音量、低通濾器 (LPF) 和高通濾器 (HPF) 曲線。 我們建議將音量曲線類型設定為線性，當遮擋值為 100 時其值為 -100 dB。

使用此設定之後，如果聲場專案模擬計算 -18 dB 的遮擋，將會輸入至下方曲線位於 X=18 的位置，而相對應的 Y 值就是所套用的衰減。 若要進行半遮擋，請將端點設定為 -50 dB (而非 -100 dB)，或設定為 -200 dB 以加大遮擋。 您可以調整和微調任何曲線以符合遊戲需求。
 
![Wwise 阻擋曲線編輯器的螢幕擷取畫面](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>停用 Wwise 障礙物曲線
Wwise 障礙物曲線會影響隔離的乾燥層級，但聲場專案會使用設計控制項和模擬強制執行濕潤/乾燥比例。 我們建議停用障礙物音量曲線。 若要設計濕潤度，請使用稍後說明的濕潤度調整控制項。
 
如果您要將障礙物 LPF/HPF 曲線用於其他用途，請確定已將它們設定為當 X=0 時 Y=0 (亦即，當沒有障礙物時，就沒有任何 LPF 或 HPF)。

![Wwise 障礙物曲線編輯器的螢幕擷取畫面](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>設計聲場專案混音器參數
您可以前往聲場專案匯流排的混音器外掛程式索引標籤，以控制全域殘響器屬性。 按兩下 [Project Acoustics Mixer (Custom)] \(聲場專案混音器 (自訂)\)，開啟混音器外掛程式的設定面板。

您也可以看到混音器外掛程式有 [Perform Spatialization] \(執行空間化\) 選項。 如果您想要使用聲場專案內建的空間化功能，請勾選 [Perform Spatialization] \(執行空間化\) 核取方塊，並選擇 [HRTF] 或 [Panning] \(移動瀏覽\)。 請務必停用已設定的任何乾燥 Aux 匯流排，否則您將聽到兩次直接路徑。 使用 [Wetness Adjust] \(濕潤度調整\) 和 [Reverb Time Scale Factor] \(殘響器時間比例因素\) 進行殘響器混音全域控制練習。 請注意，您必須重新啟動 Unreal，然後先重新產生音庫之後才按下 [Play] \(播放\)，以取用混音器外掛程式組態變更，如 [Perform Spatialization] \(執行空間化\) 核取方塊。

![聲場專案 Wwise 混音器外掛程式選項的螢幕擷取畫面](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>在 Wwise 動作項目混音器階層中設定聲場專案設計控制項
若要控制個別動作項目混音器的參數，請按兩下動作項目混音器，然後按一下其 [Mixer Plug-in] \(混音器外掛程式\) 索引標籤。您可以在這裡變更每個音效層級的任何參數。 這些值會與從 Unreal 端設定的幾個值結合 (如下所述)。 例如，如果聲場專案 Unreal 外掛程式將物件上的 [Outdoorness Adjustment] \(戶外調整\) 設為 0.5，而 Wwise 將它設為 0.25，會導致套用到該音效的戶外調整為 0.25。

![Wwise 動作項目混音器階層中每個音效混音器設定的螢幕擷取畫面](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>請確定 Aaux 匯流排具備乾燥傳送功能，且輸出匯流排具備濕潤傳送功能
請記住，必要的動作項目混音器設定會和 Wwise 中一般的乾燥和濕潤路由交換。 它會在動作項目混音器的輸出匯流排 (設為聲場專案匯流排) 上產生殘響器信號，以及沿著使用者定義的 Aux 匯流排產生乾燥訊號。 基於聲場專案 Wwise 外掛程式所使用 Wwise 混音器外掛程式 API 的功能，此路由是必要的。

![Wwise 編輯器的螢幕擷取畫面，其中顯示聲場專案的聲音設計指導方針](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>設定距離衰減曲線
請確定使用聲場專案的動作項目混音器所使用的任何衰減曲線，已將使用者定義的 Aux 傳送設定為 [output bus volume] \(輸出匯流排音量\)。 Wwise 會預設對新建立的衰減曲線進行此設定。 如果正在移轉現有專案，請檢查您的曲線設定。

根據預設，聲場專案模擬範圍為玩家所在位置周圍半徑 45 公尺內的區域。 我們通常會建議將該距離周圍區域的衰減曲線設定為 -200 dB。 這個距離並非永久的條件約束。 針對某些聲音 (例如武器的聲音)，您可以設定較大的半徑範圍。 在此情況下，需要注意的是只會涵蓋玩家所在位置 45 公尺範圍內的地理位置。 如果玩家是在室內，且音效來源位於室外 100 公尺之外，聲音就會適當地受到遮擋。 如果來源是在室內，而玩家位於室外 100 公尺之外，聲音就不會適當地受到遮擋。

![Wwise 衰減曲線的螢幕擷取畫面](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>後期混音器等化 ###
 您可能還想要新增後期混音器等化器。 您可以將聲場專案匯流排視為典型的殘響器匯流排 (處於預設殘響器模式)，並加上濾器來進行等化。 您可以聲場專案 Wwise 範例專案中看到此種範例。

![Wwise 後期混音器 EQ 的螢幕擷取畫面](media/wwise-post-mixer-eq.png)

例如，高通濾器可協助處理來自近場錄音 (會產生不真實的轟隆殘響) 的低音。 您也可透過 RTPC 調整 EQ 以達到更多的後期聲場模擬控制，進而讓您在遊戲時變更殘響器的音色。

## <a name="set-up-scene-wide-project-acoustics-properties"></a>設定整個場景的聲場專案屬性

聲場空間動作項目會揭露許多控制項，它們會修改系統的行為，並且可用於偵錯。

![Unreal 聲場空間控制項的螢幕擷取畫面](media/acoustics-space-controls.png)

* **聲場資料：** 必須從 Content/Acoustics 目錄指派聲場模擬的聲場資產給此欄位。 聲場專案外掛程式會自動將 Content/Acoustics 目錄加到您專案的已封裝目錄。
* **磚大小：** 您想要將聲場資料載入 RAM 之收聽者的周圍擴充區域。 只要玩家周圍的收聽者探查都立即載入，結果就會與載入所有探查的聲場資料相同。 較大的磚會使用更多 RAM，但會降低磁碟 I/O
* **自動資料流：** 啟用時，會在新的磚中自動載入，因為收聽者涵蓋範圍會達到載入區域的邊緣。 停用時，您將需要透過程式碼或藍圖，以手動方式載入新的磚
* **快取規模：** 可控制用於聲場查詢的快取大小。 較小的快取會使用較少的 RAM，但可能會提高每個查詢的 CPU 使用量。
* **已啟用聲場：** 可進行快速聲場模擬 A/B 切換的偵錯控制項。 隨附組態中會忽略此控制項。 此控制項很好用，可用於尋找源自聲場計算的特定音訊 Bug，或尋找 Wwise 專案中的其他問題。
* **更新距離：** 如果想要用於查詢距離的預先聲場模擬聲場資訊，請使用此選項。 這些查詢類似於光線轉換 (ray cast)，但它們已預先計算，因此耗用的 CPU 資源更少。 使用方式範例是用於從最靠近收聽者的表面進行離散反射。 若要充分運用此功能，您將需要使用程式碼或藍圖來查詢距離。
* **繪製統計資料：** 雖然 UE 的 `stat Acoustics` 可為您提供 CPU 資訊，但此狀態會在畫面左上角顯示目前載入的 ACE 檔案、RAM 使用量，以及其他狀態資訊。
* **繪製體素化項目：** 靠近收聽者、顯示執行階段插補期間所使用體素化網格的重疊體素化項目。 如果執行階段體素化項目內部有放射器，聲場查詢將會失敗。
* **繪製探查：** 顯示此場景的所有探查。 視載入狀態不同，它們的顏色也會不一樣。
* **繪製距離：** 如果已啟用更新距離功能，這會在接聽者周圍的可量化方向，且最接近收聽者的介面中顯示一個方塊。

## <a name="actor-specific-acoustics-design-controls"></a>動作項目特定聲場設計控制項
這些設計控制項限定為 Unreal 中一個獨立音訊元件。

![Unreal 音訊元件控制項的螢幕擷取畫面](media/audio-component-controls.png)

* **遮擋乘數：** 可控制遮擋效果。 值 > 1 會增強遮擋效果。 值 < 1 會將遮擋效果降到最低。
* **溼潤度調整：** 額外的殘響器 dB
* **衰減時間乘數：** 可依據聲場模擬輸出控制 RT60 乘法
* **戶外調整：** 可控制戶外殘響狀況。 接近 0 的值較類似室內效果，接近 1 則較類似戶外效果。 這項調整是加總的，因此將它設定為 -1 會強制為室內效果，設定為 +1 則會強制為戶外效果。
* **傳輸分貝：** 透過將此音量加強和以視野為基礎的距離衰減結合，轉譯額外的穿牆音效。
* **濕潤比例距離變形：** 在不會影響直接路徑的情況下，依據較接近或較偏離來源的殘響特性進行調整。
* **開始時播放：** 指定是否應該在場景開始時自動播放聲音的開關。 預設為啟用。
* **顯示聲場參數：** 直接在遊戲中的元件上方顯示偵錯資訊。 (僅適用於非隨附組態)

## <a name="blueprint-functionality"></a>藍圖功能
聲場空間動作項目可透過藍圖存取，它可以提供像是透過層級指令碼載入對應或修改設定這樣的功能。 我們在這裡提供兩個範例。

### <a name="add-finer-grained-control-over-streaming-load"></a>在串流載入中加入更精細的控制項
若要自行管理聲場資料串流，而非依據玩家位置自動串流處理，您可以使用強制載入磚藍圖函式：

![Unreal 中藍圖串流選項的螢幕擷取畫面](media/blueprint-streaming.png)

* **目標：** AcousticsSpace 動作項目
* **置中位置：** 需載入資料的區域中央位置
* **卸載磚外部的探查：** 若選取，不在新區域中的所有探查將都會從 RAM 卸載。 如果取消選取，新的區域就會載入至記憶體，同時保留已載入記憶體的現有探查
* **在完成時封鎖：** 可讓磚載入同步作業

必須在呼叫強制載入磚之前就設定好磚大小。 例如，您可以執行類似此類的作業，以載入 ACE 檔案、設定磚大小，以及在區域中進行串流：

![Unreal 中串流設定選項的螢幕擷取畫面](media/streaming-setup.png)

此範例中使用的「載入聲場資料」藍圖函式具有下列參數：

* **目標：** AcousticsSpace 動作項目。
* **新的聲場模擬：** 要載入的聲場資料資產。 將此選項保留為空白/設為 Null，將會卸載目前的聲場模擬而不載入新的。

### <a name="optionally-query-for-surface-proximity"></a>選擇性查詢表面鄰近性
如果您要查看某一收聽者周圍特定方向的表面距離靠近狀況，可以使用查詢距離函式。 此函式很有用，可用於驅動方向性延遲反射，或用於由表面鄰近性驅動的其他遊戲邏輯。 查詢的成本低於光線轉換，因為是從聲場查閱資料表取得結果。

![藍圖距離查詢範例的螢幕擷取畫面](media/distance-query.png)

* **目標：** AcousticsSpace 動作項目
* **觀看方向：** 查詢方向，以收聽者為中心
* **距離：** 如果查詢成功，這就是與最接近表面之間的距離
* **傳回值：** 布林值 - 查詢成功為 true，否則為 false

## <a name="next-steps"></a>後續步驟
* 探索[設計程序](design-process.md)背後的概念
* [建立 Azure 帳戶](create-azure-account.md)來模擬您自己場景的聲場


