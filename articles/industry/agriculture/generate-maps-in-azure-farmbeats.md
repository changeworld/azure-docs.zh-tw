---
title: 產生地圖
description: 本文說明如何在 Azure FarmBeats 中產生對應。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84709057"
---
# <a name="generate-maps"></a>產生地圖

使用 Azure FarmBeats，您可以使用衛星影像和感應器資料輸入來產生下列對應。 Maps 可協助您查看伺服器陣列的地理位置，並找出適合您裝置的位置。

  - **感應器放置圖**：提供有關要使用的感應器數量，以及在伺服器陣列上放置它們的位置的建議。
  - **附屬索引對應**：顯示伺服器陣列的植被指數索引和水線索引。
  - 中紙**濕度熱度圖**：透過融合衛星資料和感應器資料來顯示中料濕度分佈。

## <a name="sensor-placement-map"></a>感應器放置圖

FarmBeats 感應器放置圖可協助您放置上料濕度感應器。 地圖輸出是由感應器部署的座標清單所組成。 這些感應器的輸入會與衛星影像一起使用，以產生中料濕度熱度圖。

此對應的衍生方式是將 canopy 分割成一年的多個日期。 即使是空的料和建築也是 canopy 的一部分。 您可以移除位置上不需要的感應器。 此地圖適用于指導方針，您可以根據自己的自訂知識，稍微改變位置和數位。 新增感應器並不會回歸，因此在感應器編號減少時，可能會有病情惡化前的熱度圖精確度。

## <a name="before-you-begin"></a>開始之前

在您嘗試產生感應器放置對應之前，請先符合下列必要條件：

- 伺服器陣列大小必須是一個以上的 acre。
- 所選日期範圍內的無雲端 Sentinel 場景數目必須超過六個。
- 至少有六個無雲端的 Sentinel 場景必須有正規化差異植被指數索引 (NDVI) 大於或等於0.3。

    > [!NOTE]
    > Sentinel 只允許每個使用者有兩個平行線程。 如此一來，工作就會排入佇列，而且可能需要較長的時間才能完成。

### <a name="dependencies-on-sentinel"></a>Sentinel 的相依性

下列相依性與 Sentinel 相關：

- 我們依賴 Sentinel 效能來下載附屬映射。 檢查 Sentinel 效能狀態和維護 [活動](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)。
- Sentinel 只允許每個使用者有兩個並行 [下載執行緒](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) 。
- 精確度對應產生會受到 [Sentinel 涵蓋範圍和]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)重新流覽頻率的影響。

## <a name="create-a-sensor-placement-map"></a>建立感應器放置圖
本節將詳細說明建立感應器放置對應的程式。

> [!NOTE]
> 您可以從 [**對應**] 頁面或從 [**伺服器陣列詳細資料**] 頁面上的 [**產生精確度對應**] 下拉式功能表起始感應器放置圖。

請遵循下列步驟。

1. 在首頁上，移至左側導覽功能表中的 [ **對應** ]。
2. 選取 [ **建立對應**]，然後從下拉式功能表中選取 [ **感應器放置** ]。

    ![選取感應器放置](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. 選取 **感應器放置**之後， **感應器放置** 時間範圍隨即出現。

    ![感應器放置時間範圍](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. 從 **伺服器** 陣列的下拉式功能表中選取伺服器陣列。
   若要搜尋並選取您的伺服器陣列，您可以在下拉式清單中，或在文字方塊中輸入伺服器陣列的名稱。
5. 若要產生上一年的地圖，請選取 [ **建議**]。
6. 若要產生自訂日期範圍的對應，請選取 [ **選取日期範圍**] 選項。 輸入您要產生感應器放置圖的開始和結束日期。
7. 選取 [ **產生對應**]。
 隨即會出現含有作業詳細資料的確認訊息。

  如需有關工作狀態的詳細資訊，請參閱「 **查看工作**」一節。 如果作業狀態顯示為 [ *失敗*]，則會在 [ *失敗* ] 狀態的工具提示上顯示詳細的錯誤訊息。 在此情況下，請重複先前的步驟，然後再試一次。

  如果問題持續發生，請參閱 [疑難排解](troubleshoot-azure-farmbeats.md) 一節，或與 [Azure FarmBeats 論壇聯繫以](https://aka.ms/FarmBeatsMSDN) 取得相關記錄的支援。

### <a name="view-and-download-a-sensor-placement-map"></a>查看和下載感應器放置圖

請遵循下列步驟。

1. 在首頁上，移至左側導覽功能表中的 [ **對應** ]。

    ![從左側導覽功能表中選取 [對應]](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 從視窗的左側導覽中選取 [ **篩選** ]。
  [ **篩選** ] 視窗會顯示搜尋準則。

    ![篩選視窗](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 從下拉式功能表中選取 [ **類型**]、[ **日期**] 和 [ **名稱** ] 值。 然後選取 **[套用]，以** 搜尋您想要查看的地圖。
  建立作業的日期會以 type_farmname_YYYY-MM-DD 的格式顯示。
4. 使用頁面結尾的導覽列，以流覽可用的地圖清單。
5. 選取您要查看的地圖。 快顯視窗會顯示所選取地圖的預覽。
6. 選取 [ **下載**]，並下載感應器座標的 GeoJSON 檔案。

    ![感應器放置地圖預覽](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>附屬索引對應

下列各節說明有關建立和查看附屬索引對應的程式。

> [!NOTE]
> 您可以從 [**對應**] 頁面或從 [**伺服器陣列詳細資料**] 頁面的 [**產生精確度對應**] 下拉式功能表中，起始附屬索引對應。

FarmBeats 可讓您產生 NDVI、增強的植被指數索引 (EVI) 和標準化的差異水索引 (NDWI) maps 的伺服器陣列。 這些索引可協助您判斷裁剪目前的成長方式、過去的成長，以及地面的代表水。


> [!NOTE]
> 產生對應的日期需要 Sentinel 映射。


## <a name="create-a-satellite-indices-map"></a>建立附屬索引對應

請遵循下列步驟。

1. 在首頁上，移至左側導覽功能表中的 [ **對應** ]。
2. 選取 [ **建立對應**]，然後從下拉式功能表中選取 [ **附屬索引** ]。

    ![從下拉式功能表中選取附屬索引](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. 選取 **附屬索引**之後，[ **附屬索引** ] 視窗隨即出現。

    ![附屬索引視窗](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. 從下拉式功能表中選取伺服器陣列。
   若要搜尋並選取您的伺服器陣列，您可以在下拉式清單中，或輸入伺服器陣列的名稱。   
5. 若要產生上一周的地圖，請選取 **本周**。
6. 若要產生自訂日期範圍的對應，請選取 [ **選取日期範圍**] 選項。 輸入您要產生附屬索引對應的開始和結束日期。
7. 選取 [ **產生對應**]。
    隨即會出現含有作業詳細資料的確認訊息。

    ![附屬索引對應確認訊息](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    如需有關工作狀態的詳細資訊，請參閱「 **查看工作**」一節。 如果作業狀態顯示為 [ *失敗*]，則會在 [ *失敗* ] 狀態的工具提示上顯示詳細的錯誤訊息。 在此情況下，請重複先前的步驟，然後再試一次。

    如果問題持續發生，請參閱 [疑難排解](troubleshoot-azure-farmbeats.md) 一節，或與 [Azure FarmBeats 論壇聯繫以](https://aka.ms/FarmBeatsMSDN) 取得相關記錄的支援。

### <a name="view-and-download-a-map"></a>查看和下載地圖

請遵循下列步驟。

1. 在首頁上，移至左側導覽功能表中的 [ **對應** ]。

    ![選取對應](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 從視窗的左側導覽中選取 [ **篩選** ]。 [ **篩選** ] 視窗會顯示搜尋準則。

    ![篩選視窗顯示搜尋準則](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 從下拉式功能表中選取 [ **類型**]、[ **日期**] 和 [ **名稱** ] 值。 然後選取 **[套用]，以** 搜尋您想要查看的地圖。
  建立作業的日期會以 type_farmname_YYYY-MM-DD 的格式顯示。

4. 使用頁面結尾的導覽列，以流覽可用的地圖清單。
5. 針對每個 **伺服器陣列名稱稱** 和 **日期**的組合，可使用下列三個對應：
    - Ndvi
    - EVI
    - NDWI
6. 選取您要查看的地圖。 快顯視窗會顯示所選取地圖的預覽。
7. 從下拉式功能表中選取 [ **下載** ]，以選取下載格式。 地圖會下載並儲存在您電腦上的本機資料夾中。

    ![選取的附屬索引對應預覽](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>中料濕度熱度圖

內建的濕度是在每個上料粒子之間的空格中所持有的水。「內料濕度」熱度圖可協助您在伺服陣列內以高解析度，以任何深度瞭解您的土濕度資料。 若要產生精確且可使用的泥土濕度熱度圖，需要一致的感應器部署。 所有感應器都必須來自相同的提供者。 不同的提供者在測量一種方式上有差異，以及校正之間的差異。 熱度圖會使用在該深度部署的感應器來產生特定深度。

### <a name="before-you-begin"></a>開始之前

在您嘗試產生中料濕度熱度圖之前，請先符合下列必要條件：

- 至少必須部署三個上料濕度感應器。 請不要嘗試在部署和與伺服器陣列相關聯的感應器之前，先建立一個泥土濕度熱度圖。
- 產生內料濕度熱度圖會受到 Sentinel 的路徑涵蓋範圍、雲端封面和雲端陰影所影響。 過去120天必須有至少一個無雲端的 Sentinel 場景，從要求的熱度圖天開始。
- 在伺服器陣列上部署的至少一半感應器必須在線上，而且資料會串流至 datahub。
- 熱度圖必須使用來自相同提供者的感應器量值來產生。


## <a name="create-a-soil-moisture-heatmap"></a>建立上料濕度熱度圖

請遵循下列步驟。

1. 在首頁上，移至左側導覽功能表中的 [ **對應** ]，以查看 [ **對應** ] 頁面。
2. 選取 [ **建立對應**]，然後從下拉式功能表中選取 [中拉出 **濕度** ]。

    ![從下拉式功能表中選取 [自上料濕度]](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. 當您選取 [上 **料濕度**] 之後，會出現 [上 **料濕度** ] 視窗。

    ![上料濕度視窗](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. 從 **伺服器** 陣列的下拉式功能表中選取伺服器陣列。
   若要搜尋並選取您的伺服器陣列，您可以從下拉式清單中選取，或在 [ **選取伺服器** 陣列] 下拉式功能表中輸入伺服器陣列的名稱。
5. 在 [ **選取上料濕度感應器] 量值** 下拉式功能表中，選取您要產生對應 (深度) 的 [土裡濕度感應器] 量值。
若要找出感應器量值，請移至 [ **感應器**]，然後選取任何的泥土濕度感應器。 然後，在 [ **感應器屬性** ] 區段下，使用 [ **量值名稱**] 中的值。
6. 若要產生 **今天** 或 **本周**的地圖，請選取其中一個選項。
7. 若要產生自訂日期範圍的對應，請選取 [ **選取日期範圍**] 選項。 輸入您要產生中料濕度熱度圖的開始和結束日期。
8. 選取 [ **產生對應**]。
 隨即會出現含有作業詳細資料的確認訊息。

   ![上料濕度對應確認訊息](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    如需有關工作狀態的詳細資訊，請參閱「 **查看工作**」一節。 如果作業狀態顯示為 [ *失敗*]，則會在 [ *失敗* ] 狀態的工具提示上顯示詳細的錯誤訊息。 在此情況下，請重複先前的步驟，然後再試一次。

    如果問題持續發生，請參閱 [疑難排解](troubleshoot-azure-farmbeats.md) 一節，或與 [Azure FarmBeats 論壇聯繫以](https://aka.ms/FarmBeatsMSDN) 取得相關記錄的支援。

### <a name="view-and-download-a-map"></a>查看和下載地圖

請遵循下列步驟。

1. 在首頁上，移至左側導覽功能表中的 [ **對應** ]。

    ![前往 Maps](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 從視窗的左側導覽中選取 [ **篩選** ]。 [ **篩選** ] 視窗隨即顯示，您可以在其中搜尋地圖。

    ![從左側導覽中選取 [篩選]](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  從下拉式功能表中選取 [ **類型**]、[ **日期**] 和 [ **名稱** ] 值。 然後選取 **[套用]，以** 搜尋您想要查看的地圖。 建立作業的日期會以 type_farmname_YYYY-MM-DD 的格式顯示。
4. 選取資料表標頭旁邊的 **排序** 圖示，以根據 **伺服器**陣列、 **日期**、 **建立時間**、 **作業識別碼**和 **作業類型**進行排序。
5. 使用頁面結尾的導覽按鈕，以流覽可用的地圖清單。
6. 選取您要查看的地圖。 快顯視窗會顯示所選取地圖的預覽。
7. 從下拉式功能表中選取 [ **下載** ]，以選取下載格式。 地圖會下載並儲存在指定的資料夾中。

    ![上料濕度熱度圖預覽](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
