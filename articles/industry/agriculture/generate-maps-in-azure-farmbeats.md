---
title: 產生地圖
description: 本文說明如何在 Azure FarmBeats 中產生對應。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84709057"
---
# <a name="generate-maps"></a>產生地圖

使用 Azure FarmBeats，您可以使用衛星圖像和感應器資料輸入來產生下列對應。 對應可協助您查看伺服器陣列的地理位置，並為您的裝置識別適當的位置。

  - **感應器放置圖**：提供有關要使用多少感應器以及要將它們放置在伺服器陣列上之位置的建議。
  - **衛星索引對應**：顯示伺服器陣列的 vegetation 索引和水索引。
  - **泥土濕度熱度圖**：藉由融合衛星資料和感應器資料來顯示土裡濕度分佈。

## <a name="sensor-placement-map"></a>感應器放置圖

FarmBeats 感應器放置圖可協助您放置泥土濕度感應器。 對應輸出是由感應器部署的座標清單所組成。 這些感應器的輸入會與衛星影像一起使用，以產生泥土濕度熱度圖。

這個對應的衍生方式是將 canopy 分割成一年的多個日期。 即使是空料和大樓也是 canopy 的一部分。 您可以移除位置上不需要的感應器。 此地圖適用于指導方針，您可以根據您的自訂知識，稍微改變位置和數位。 新增感應器不會回復土的濕度熱度圖結果，但如果感應器編號降低，可能會有熱度圖精確度的情形惡化。

## <a name="before-you-begin"></a>開始之前

嘗試產生感應器位置對應之前，請先符合下列必要條件：

- 伺服器陣列大小必須是一個以上的 acre。
- 在選取的日期範圍內，無雲端的 Sentinel 場景數必須超過六個。
- 至少六個無雲端的 Sentinel 場景必須具有大於或等於0.3 的正規化差異 Vegetation 索引（NDVI）。

    > [!NOTE]
    > Sentinel 只允許每個使用者有兩個平行線程。 因此，作業會排入佇列，並可能需要較長的時間才能完成。

### <a name="dependencies-on-sentinel"></a>Sentinel 上的相依性

下列相依性是 Sentinel 的相關相依性：

- 我們會依賴 Sentinel 效能來下載附屬映射。 檢查 Sentinel 效能狀態和維護[活動](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)。
- Sentinel 只允許每位使用者有兩個並行[下載執行緒](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services)。
- 精確度對應產生會受到[Sentinel 涵蓋範圍和]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)重新流覽頻率的影響。

## <a name="create-a-sensor-placement-map"></a>建立感應器放置圖
本節詳細說明建立感應器放置對應的程式。

> [!NOTE]
> 您可以從 [**對應**] 頁面或從 [**伺服器陣列詳細資料**] 頁面上的 [**產生精確度對應**] 下拉式功能表，起始感應器放置圖。

請遵循下列步驟。

1. 在 [首頁] 頁面上，移至左側導覽功能表中的 [**對應**]。
2. 選取 [**建立對應**]，然後從下拉式功能表中選取 [**感應器放置**]。

    ![選取感應器放置](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. 選取**感應器放置**之後，[**感應器放置**] 視窗隨即出現。

    ![感應器放置視窗](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. 從 [**伺服器**陣列] 下拉式功能表中選取伺服器陣列。
   若要搜尋並選取您的伺服器陣列，您可以在下拉式清單中滾動，或在文字方塊中輸入伺服器陣列的名稱。
5. 若要產生去年的對應，請選取 [**建議**]。
6. 若要產生自訂日期範圍的對應，請選取 [**選取日期範圍**] 選項。 輸入您要產生感應器放置圖的開始和結束日期。
7. 選取 [**產生對應**]。
 包含作業詳細資料的確認訊息隨即出現。

  如需作業狀態的詳細資訊，請參閱**View Jobs**一節。 如果作業狀態顯示 [*失敗*]，則會在 [*失敗*] 狀態的工具提示上顯示詳細的錯誤訊息。 在此情況下，請重複上述步驟，然後再試一次。

  如果問題持續發生，請參閱[疑難排解](troubleshoot-azure-farmbeats.md)一節，或與[Azure FarmBeats 論壇聯絡以取得相關記錄的支援](https://aka.ms/FarmBeatsMSDN)。

### <a name="view-and-download-a-sensor-placement-map"></a>查看和下載感應器放置圖

請遵循下列步驟。

1. 在 [首頁] 頁面上，移至左側導覽功能表中的 [**對應**]。

    ![從左側導覽功能表中選取 [對應]](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 從視窗的左側導覽中選取 [**篩選**]。
  [**篩選**] 視窗會顯示搜尋準則。

    ![篩選視窗](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 從下拉式功能表中選取 [**類型**]、[**日期**] 和 [**名稱**值]。 然後選取 **[** 套用] 以搜尋您想要查看的對應。
  作業的建立日期以 type_farmname_YYYY-MM-DD 的格式顯示。
4. 使用頁面結尾的導覽列，以流覽可用的對應清單。
5. 選取您想要查看的對應。 快顯視窗會顯示所選取對應的預覽。
6. 選取 [**下載**]，然後下載感應器座標的 GeoJSON 檔案。

    ![感應器放置地圖預覽](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>衛星索引對應

下列各節說明建立和查看附屬索引對應的相關程式。

> [!NOTE]
> 您可以從 [**對應**] 頁面或從 [**伺服器陣列詳細資料**] 頁面上的 [**產生精確度對應**] 下拉式功能表，起始附屬索引對應。

FarmBeats 可讓您為伺服器陣列產生 NDVI、增強的 Vegetation 索引（EVI），以及正規化的差異水指數（NDWI）對應。 這些索引有助於判斷裁剪目前的成長，或過去的成長，以及地面的代表水層級。


> [!NOTE]
> 產生對應的日期需要 Sentinel 影像。


## <a name="create-a-satellite-indices-map"></a>建立衛星索引對應

請遵循下列步驟。

1. 在 [首頁] 頁面上，移至左側導覽功能表中的 [**對應**]。
2. 選取 [**建立對應**]，然後從下拉式功能表中選取 [**附屬索引**]。

    ![從下拉式功能表中選取 [附屬索引]](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. 選取 [**附屬索引**] 之後，[**附屬索引**] 視窗隨即出現。

    ![附屬索引視窗](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. 從下拉式功能表中選取伺服器陣列。
   若要搜尋並選取您的伺服器陣列，您可以在下拉式清單中滾動，或輸入伺服器陣列的名稱。   
5. 若要產生上一周的對應，請選取 [**本周**]。
6. 若要產生自訂日期範圍的對應，請選取 [**選取日期範圍**] 選項。 輸入您要為其產生附屬索引對應的開始和結束日期。
7. 選取 [**產生對應**]。
    包含作業詳細資料的確認訊息隨即出現。

    ![衛星索引對應確認訊息](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    如需作業狀態的詳細資訊，請參閱**View Jobs**一節。 如果作業狀態顯示 [*失敗*]，則會在 [*失敗*] 狀態的工具提示上顯示詳細的錯誤訊息。 在此情況下，請重複上述步驟，然後再試一次。

    如果問題持續發生，請參閱[疑難排解](troubleshoot-azure-farmbeats.md)一節，或與[Azure FarmBeats 論壇聯絡以取得相關記錄的支援](https://aka.ms/FarmBeatsMSDN)。

### <a name="view-and-download-a-map"></a>查看和下載地圖

請遵循下列步驟。

1. 在 [首頁] 頁面上，移至左側導覽功能表中的 [**對應**]。

    ![選取地圖](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 從視窗的左側導覽中選取 [**篩選**]。 [**篩選**] 視窗會顯示搜尋準則。

    ![篩選視窗會顯示搜尋準則](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 從下拉式功能表中選取 [**類型**]、[**日期**] 和 [**名稱**值]。 然後選取 **[** 套用] 以搜尋您想要查看的對應。
  作業的建立日期以 type_farmname_YYYY-MM-DD 的格式顯示。

4. 使用頁面結尾的導覽列，以流覽可用的對應清單。
5. 針對每個**伺服器陣列名稱稱**和**日期**的組合，可以使用下列三個對應：
    - NDVI
    - EVI
    - NDWI
6. 選取您想要查看的對應。 快顯視窗會顯示所選取對應的預覽。
7. 從下拉式功能表選取 [**下載**]，以選取下載格式。 對應會下載並儲存在您電腦上的本機資料夾中。

    ![選取的附屬索引對應預覽](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>土裡濕度熱度圖

土裡濕度是在上料粒子之間保留的水。「內料濕度」熱度圖可協助您在伺服器陣列內以高解析度的方式瞭解任何深度的泥土濕度資料。 若要產生精確且可用的內料濕度熱度圖，必須要有一致的感應器部署。 所有感應器都必須來自相同的提供者。 不同的提供者在測量泥土濕度的方式上有差異，以及校正的差異。 使用在該深度部署的感應器，為特定深度產生熱度圖。

### <a name="before-you-begin"></a>開始之前

嘗試產生泥土濕度熱度圖之前，請先符合下列必要條件：

- 至少必須部署三個上的土濕度感應器。 請不要嘗試在部署感應器並與伺服器陣列相關聯之前，建立一個土裡濕度熱度圖。
- 產生土裡的濕度熱度圖會受到 Sentinel 的路徑涵蓋範圍、雲端封面和雲端陰影的影響。 在過去120天內，至少有一個雲端免費的 Sentinel 場景必須可供使用，從這天開始，要求的是土濕度熱度圖。
- 在伺服器陣列上部署的至少一半感應器必須上線，並將資料串流處理至 datahub。
- 熱度圖必須使用來自相同提供者的感應器量值來產生。


## <a name="create-a-soil-moisture-heatmap"></a>建立土料濕度熱度圖

請遵循下列步驟。

1. 在 [首頁] 頁面上，移至左側導覽功能表中的 [**對應**]，以查看 [**對應**] 頁面。
2. 選取 [**建立對應**]，然後從下拉式功能表中選取 [**土裡濕度**]。

    ![從下拉式功能表中選取 [土裡濕度]](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. 選取 [**泥土**濕度] 之後，會出現 [**泥土濕度**] 視窗。

    ![土裡濕度視窗](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. 從 [**伺服器**陣列] 下拉式功能表中選取伺服器陣列。
   若要搜尋並選取您的伺服器陣列，您可以從下拉式清單中滾動，或在 [**選取伺服器**陣列] 下拉式功能表中輸入伺服器陣列的名稱。
5. 在 [**選取 [泥土濕度感應器量值**] 下拉式功能表中，選取您想要產生對應的 [土裡濕度感應器] 量值（深度）。
若要找出感應器量值，請移至 [**感應器**]，然後選取任何土裡濕度感應器。 然後，在 [**感應器屬性**] 區段下，使用 [**量值名稱**] 中的值。
6. 若要產生**今天**或**本周**的對應，請選取其中一個選項。
7. 若要產生自訂日期範圍的對應，請選取 [**選取日期範圍**] 選項。 輸入您要為其產生泥土濕度熱度圖的開始和結束日期。
8. 選取 [**產生對應**]。
 包含作業詳細資料的確認訊息隨即出現。

   ![泥土濕度對應確認訊息](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    如需作業狀態的詳細資訊，請參閱**View Jobs**一節。 如果作業狀態顯示 [*失敗*]，則會在 [*失敗*] 狀態的工具提示上顯示詳細的錯誤訊息。 在此情況下，請重複上述步驟，然後再試一次。

    如果問題持續發生，請參閱[疑難排解](troubleshoot-azure-farmbeats.md)一節，或與[Azure FarmBeats 論壇聯絡以取得相關記錄的支援](https://aka.ms/FarmBeatsMSDN)。

### <a name="view-and-download-a-map"></a>查看和下載地圖

請遵循下列步驟。

1. 在 [首頁] 頁面上，移至左側導覽功能表中的 [**對應**]。

    ![前往 Maps](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 從視窗的左側導覽中選取 [**篩選**]。 [**篩選**] 視窗會顯示您可以在其中搜尋地圖的位置。

    ![從左側導覽中選取 [篩選]](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  從下拉式功能表中選取 [**類型**]、[**日期**] 和 [**名稱**值]。 然後選取 **[** 套用] 以搜尋您想要查看的對應。 作業的建立日期以 type_farmname_YYYY-MM-DD 的格式顯示。
4. 選取資料表標頭旁的**排序**圖示，依據 [**伺服器**陣列]、[**日期**]、[**建立時間**]、[**工作識別碼**] 和 [**作業類型**] 排序。
5. 使用頁面結尾的瀏覽按鈕，即可流覽可用的對應清單。
6. 選取您想要查看的對應。 快顯視窗會顯示所選取對應的預覽。
7. 從下拉式功能表選取 [**下載**]，以選取下載格式。 對應會下載並儲存在指定的資料夾中。

    ![土裡濕度熱度圖預覽](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
