---
title: 教學課程：安裝 Azure FXT Edge Filer 實體裝置
description: 如何打開包裝、裝設機架並連接 Microsoft Azure FXT Edge Filer hy 混合式儲存體快取實體裝置元件的纜線
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 7c77e25d0dcabc49f2e6672645c6bc41e8662ec8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75551024"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>教學課程：安裝 Azure FXT Edge Filer 

此教學課程說明如何安裝 Azure FXT Edge Filer 混合式儲存體快取的硬體節點。 您必須安裝至少三個硬體節點，才能建立 Azure FXT Edge Filer 叢集。

安裝程序包括打開包裝並將裝置掛接到機架，然後連接纜線整理支架 (CMA) 與前邊框。 另有教學課程說明如何連接網路線並連接電源。 

安裝 Azure FXT Edge Filer 節點需要大約一小時。 

此教學課程包含這些設定步驟： 

> [!div class="checklist"]
> * 打開裝置包裝
> * 將裝置掛接到機架
> * 安裝前邊框 (選用)

## <a name="installation-prerequisites"></a>安裝先決條件 

在開始之前，請確定您即將使用的資料中心和機架具有下列特色：

* 您要掛接裝置的機架上有可用的 1U 插槽。
* AC 電源供應器和冷卻系統符合 Azure FXT Edge Filer 的需求。 (如需協助規劃安裝和調整大小的說明，請參閱[電源和熱溫規格](fxt-specs.md#power-and-thermal-specifications)。)  

  > [!NOTE] 
  > 若要充分利用兩部備援電源供應器 (PSU) 裝置，連接 AC 電源時，請在兩個不同的分支電路上使用配電裝置。 如需詳細資訊，請參閱[連接電源線](fxt-network-power.md#connect-power-cables)。  

## <a name="unpack-the-hardware-node"></a>打開硬體節點包裝 

每個 Azure FXT Edge Filer 節點都會以單箱出貨。 完成下列步驟來打開裝置包裝。

1. 將箱子放置在平坦的表面上。

2. 檢查箱子及包裝發泡材料有無損毀、割痕、浸水，或任何其他明顯損傷。 如果箱子或包裝嚴重損毀，請不要打開它。 請連絡 Microsoft 支援服務，以協助您評估裝置是否正常運作。

3. 打開箱子。 確定箱中包含下列各項：
   * 一部單一機箱 FXT 裝置
   * 兩條電源線
   * 一個前邊框與鑰匙
   * 一個滑軌套件組件
   * 一個纜線整理支架 (CMA)
   * CMA 安裝指示手冊
   * 機架安裝指示手冊
   * 安全、環境和法規資訊手冊

如果您未收到上面所列的所有項目，請連絡裝置廠商尋求支援。 

在安裝或開啟電源之前，請確定裝置有足夠的時間達到室溫。 如果您注意到裝置的任何部分有凝結的水珠，請等待至少 24 小時後，再進行安裝。

下一步是利用機架掛接裝置。

## <a name="rack-the-device"></a>安裝裝置機架

Azure FXT Edge Filer 裝置必須安裝在標準 19 吋機架中。 

Azure FXT Edge Filer 混合式儲存體快取是由三部以上的 Azure FXT Edge Filer 裝置所組成。 針對身為系統一部分的每部裝置，重複進行機架安裝步驟。 

### <a name="rack-install-prerequisites"></a>機架安裝先決條件

* 開始之前，請閱讀隨附於裝置的「安全、環境和法規資訊手冊」中的安全指示。

  > [!NOTE]
  > 安裝在機架或從機架移除時，請一律由兩個人抬起節點。 

* 識別和您的設備機架搭配使用的滑軌安裝類型。 
  * 針對方形孔或圓形孔嵌入式機架，請依照免工具滑軌指示進行。
  * 針對已穿孔的機架，請依照工具式滑軌指示進行。 
  
    安裝工具式滑軌時，您需要提供八個螺絲：類型 10-32、12-24、M5 或 M6。 螺絲頭直徑必須小於 10 公釐 (0.4 吋)。

### <a name="identify-the-rail-kit-contents"></a>找出滑軌套件內容

找出用於安裝滑軌套件組件的元件：

1. 兩個 A7 Dell ReadyRails II 滑軌組件 (1)
1. 兩個黏扣帶 (2)

![滑軌套件內容的編號圖解](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>滑軌組件 - 免工具滑軌 (方形孔或圓形孔機架)

針對使用嵌入式方形孔或圓形孔的機架，請依照此程序來組合並安裝滑軌。 

1. 將標示為 **FRONT** 的左邊和右邊滑軌端件朝內放置。 將每個端件安裝到垂直機架凸緣前端的孔洞中。 (1)

2. 每個端件都對齊機架中要掛接空間下方和上方的孔洞。 (2)

3. 嚙合滑軌後端，直到它完全裝在垂直機架凸緣且閂鎖卡入定位為止。 重複上述步驟，將前端件安置在垂直機架凸緣上。 (3)

> [!TIP]
> 若要移除滑軌，請拉起端件中央的閂鎖釋放按鈕 (4)，並取下每個滑軌。

![安裝和移除免工具滑軌的步驟編號圖](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>滑軌組件 - 工具式滑軌 (已穿孔的機架)

針對使用已穿孔的機架，請依照此程序來組合並安裝滑軌。

1. 使用一字型螺絲起子，從前端和後端安裝托架移除定位梢。 (1)
1. 拉開並旋轉滑軌閂鎖子組件，從安裝托架將其移除。 (2)
1. 使用兩組螺絲，將左右兩側的安裝滑軌固定到前端垂直機架凸緣。 (3)
1. 靠著尾端垂直機架凸緣，將左右兩邊的背面托架向前滑動，並使用兩組螺絲加以固定。 (4)

![安裝和移除工具式滑軌的步驟編號圖](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>在機架中安裝系統

請依照下列步驟，在機架掛接 Azure FXT Edge Filer 裝置。

1. 從機架中拉出內部滑軌，直到它們卡入定位為止。 (1)
1. 找到裝置每一側的尾端滑軌螺柱，將其降低放入滑動組件上的尾端 J 形槽。 (2) 
1. 向下轉動裝置，直到所有滑軌螺柱裝入 J 形槽為止。 (3)
1. 將裝置向內推，直到鎖桿卡入定位為止。
1. 按住兩個滑軌上的滑動解鎖按鈕 (4)，並將裝置滑入機架。

![在機架安裝系統的步驟編號圖](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>從機架中移除系統

若要從機架移除裝置，請依照此程序進行。 

1. 找到內部滑軌側邊的鎖桿 (1)。
2. 將每個鎖桿旋轉至其解除位置，將其解除鎖定 (2)。
3. 牢牢抓住系統的側邊，將它向前推，直到滑軌螺柱位於 J 形槽前端為止。 提起系統並從機架取出，將它放在平面上 (3)。

![從機架移除系統的步驟編號插圖](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>嚙合閉合閂鎖

1. 面向前方，找到系統任一側的閉合閂鎖 (1)。
2. 閂鎖會在系統推入機架時自動嚙合。 

在移除系統時，向上拉起 (2) 即可釋放閂鎖。

所提供可牢固裝配的選用螺絲，可在運送或處於其他不穩定的環境時，將系統固定在機架。 找出每個閂鎖底下的螺絲，並使用 2 號十字型螺絲起子鎖緊 (3)。

![嚙合及釋放閉合閂鎖的編號插圖](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>安裝纜線整理支架 

隨 FXT Edge Filer 提供的選用纜線整理支架 (CMA)。 包裝中會提供印刷的安裝指示。 

1. 打開包裝，並識別纜線整理支架套件的元件：
   * CMA 匣 (1)
   * CMA (2)
   * 尼龍纜線束帶 (3)
   * CMA 連接托架 (4)
   * 狀態指示器纜線 (5) 

   > [!TIP] 
   > 若要在運送時將 CMA 固定在機架中，請將束帶繞住筐與匣並繫牢。 以這種方式固定 CMA，也可在不穩定的環境中固定您的系統。

   ![CMA 零件插圖](media/fxt-install/cma-kit-400.png)

2. 安裝 CMA 匣。

   CMA 匣提供支撐，並可作為 CMA 的定位器。 

   1. 以滑軌內緣的容器托架對齊並嚙合每一匣面。 
   1. 將匣向前推，直到卡入定位為止。 (1)
   1. 若要將匣移除，請向中央擠壓閂鎖釋放按鈕，然後將匣從容器托架 (2) 拉出。

   ![CMA 匣安裝插圖](media/fxt-install/cma-tray-install-400.png)

3. 安裝 CMA 連接托架。 

   > [!NOTE]
   >
   > * 您可以將 CMA 連接到右邊或左邊的掛接滑軌，視您想要如何從系統配置纜線而定。 
   > * 為了方便起見，請在電源供應器的 (A 側) 對面的那端掛接 CMA。 如果掛接在 B 側，必須將 CMA 中斷連線，以移除外部電源供應器。 
   > * 請一律先將匣移除，再移除電源供應器。 

   ![CMA 托架安裝插圖](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. 為您要掛接 CMA (B 側或 A 側) 的那側，選取適當的 CMA 連接托架。
   1. 根據滑軌背面對應的 A 側或 B 側標記，安裝 CMA 連接托架。
   1. 將托架上的孔洞與滑軌上的定位銷對齊。 將托架向下推，直到卡入定位為止。 

4. 安裝 CMA。

   1. 在系統背面，將閂鎖推到滑軌組件最深處托架的 CMA 前端，直到閂鎖嚙合為止 (1)。 
   1. 在托架最外邊那端，安裝另一個閂鎖，直到閂鎖嚙合為止 (2)。 
   1. 若要移除 CMA，請按壓閂鎖殼內側與外側頂端的 CMA 釋放按鈕，打開這兩個閂鎖 (3)。

   ![主要 CMA 安裝插圖](media/fxt-install/cma-install-400.png)

   將 CMA 轉離系統，以供進行維修。 在絞接端，從匣中提起並取下 CMA (1)。 從匣中取下之後，將 CMA 轉離系統 (2)。

   ![轉開 CMA 進行維修的插圖](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>安裝前邊框 (選用)

此節說明如何安裝及移除 Azure FXT Edge Filer 硬體的前邊框 (面板)。 

安裝前邊框： 

1. 找到並取出邊框鑰匙，它隨附於邊框包裝。 
1. 對齊邊框與底座前端，然後將邊框左側的定位銷插入節點右側機架掛接輪緣的孔洞。 
1. 將邊框的左端裝入底座。 按壓邊框，直到左側按鈕卡入定位為止。
1. 使用鑰匙鎖住邊框。

取下前邊框： 
1. 使用邊框鑰匙，為邊框開鎖。
1. 按壓左側的釋放按鈕，並將邊框左端拉離底座。
1. 解開右端，並取下邊框。
   
   ![顯示邊框左側的釋放按鈕，還有如何從左側往外拉來移除的影像](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>後續步驟

完成打開包裝並掛接裝置之後，請繼續進行連接網路線以及連接 AC 電源與 Azure FXT Edge Filer 的步驟。

> [!div class="nextstepaction"]
> [連接網路線並提供電源](fxt-network-power.md)