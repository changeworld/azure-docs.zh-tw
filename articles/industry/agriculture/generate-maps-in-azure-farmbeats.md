---
title: 產生地圖
description: 本文介紹如何在 Azure FarmBeats 中生成地圖。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271768"
---
# <a name="generate-maps"></a>產生地圖

使用 Azure FarmBeats，可以使用衛星圖像和感應器資料輸入生成以下地圖。 地圖可説明您查看伺服器場的地理位置，並確定設備的適當位置。

  - **感應器放置圖**：提供有關要使用的感應器數以及將其放置在伺服器場上的位置的建議。
  - **衛星索引圖**：顯示農場的植被指數和水指數。
  - **土壤水分熱圖**：通過融合衛星資料和感應器資料來顯示土壤水分分佈。

## <a name="sensor-placement-map"></a>感應器放置圖

FarmBeats 感應器放置圖可説明您放置土壤濕度感應器。 地圖輸出由感應器部署的座標清單組成。 這些感應器的輸入與衛星圖像一起使用，以生成土壤水分熱圖。

此地圖是通過分割樹冠派生的，這些樹冠在一年中多個日期中可見。 即使是裸露的土壤和建築物也是樹冠的一部分。 您可以移除該位置不需要的感應器。 此地圖用於指導，您可以根據您的自訂知識稍微更改位置和數位。 添加感應器不會使土壤水分熱圖結果倒退，但如果減少感應器數量，熱圖精度可能會下降。

## <a name="before-you-begin"></a>開始之前

在嘗試生成感應器放置貼圖之前，滿足以下先決條件：

- 農場面積必須超過一英畝。
- 所選日期範圍的無雲哨兵場景數必須超過 6 個。
- 至少有六個無雲哨兵場景的標準化差異植被索引 （NDVI） 大於或等於 0.3。

    > [!NOTE]
    > Sentinel 只允許每個使用者使用兩個併發執行緒。 因此，作業會排隊，可能需要更長時間才能完成。

### <a name="dependencies-on-sentinel"></a>對哨兵的依賴

以下依賴項與 Sentinel 相關：

- 我們依靠哨兵性能來下載衛星圖像。 檢查哨兵性能狀態和維護[活動](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)。
- Sentinel 只允許每個使用者僅[下載兩個併發執行緒](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services)。
- 精確地圖生成受[哨兵覆蓋和重訪頻率]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)的影響。

## <a name="create-a-sensor-placement-map"></a>創建感應器放置貼圖
本節詳細介紹了創建感應器放置貼圖的過程。

> [!NOTE]
> 可以從 **"地圖"** 頁或"**生成精度地圖"** 下拉式功能表""""""""""""**移動詳細資訊"** 頁上啟動感應器放置地圖。

請遵循下列步驟。

1. 在主頁上，從左側導航功能表轉到 **"地圖**"。
2. 選擇 **"創建地圖**"，然後從下拉式功能表中選擇 **"感應器放置**"。

    ![選擇感應器放置](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. 選擇 **"感應器放置**"後，將顯示 **"感應器放置"** 視窗。

    ![感應器放置視窗](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. 從 **"伺服器場**"下拉式功能表中選擇伺服器場。
   要搜索和選擇伺服器場，可以在下拉清單中滾動或在文字方塊中輸入伺服器場的名稱。
5. 要生成去年地圖，請選擇 **"推薦**"。
6. 要為自訂日期範圍生成地圖，請選擇 **"選擇日期範圍"** 選項。 輸入要為其生成感應器放置貼圖的開始日期和結束日期。
7. 選擇 **"生成地圖**"。
 將顯示一條帶有作業詳細資訊的確認訊息。

  有關作業狀態的資訊，請參閱 **"查看作業**"部分。 如果作業狀態顯示 *"失敗"，* 則 *"失敗"* 狀態的工具提示上將顯示一條詳細的錯誤訊息。 在這種情況下，請重複前面的步驟，然後重試。

  如果問題仍然存在，請參閱[疑難排解](troubleshoot-azure-farmbeats.md)部分或與 Azure [FarmBeats 論壇聯繫，以尋求](https://aka.ms/FarmBeatsMSDN)相關日誌的支援。

### <a name="view-and-download-a-sensor-placement-map"></a>查看和下載感應器放置地圖

請遵循下列步驟。

1. 在主頁上，從左側導航功能表轉到 **"地圖**"。

    ![從左側導航功能表中選擇"地圖"](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 從視窗的左側導航中選擇 **"篩選器**"。
  **"篩選器"** 視窗顯示搜尋條件。

    ![篩檢程式視窗](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 從下拉式功能表中選擇 **"鍵入**"、"**日期**"和 **"名稱"** 值。 然後選擇 **"應用"** 以搜索要查看的地圖。
  創建作業的日期以type_farmname_YYYY-MM-DD 格式顯示。
4. 使用頁面末尾的巡覽列滾動流覽可用的地圖清單。
5. 選擇要查看的地圖。 快顯視窗顯示所選地圖的預覽。
6. 選擇 **"下載**"，然後下載感應器座標的 GeoJSON 檔。

    ![感應器放置貼圖預覽](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>衛星指數地圖

以下各節介紹創建和查看衛星索引地圖所涉及的過程。

> [!NOTE]
> 可以從 **"地圖"** 頁或"**生成精度地圖**"下拉式功能表""""""""""""""**計畫詳細資訊"** 頁上啟動衛星索引地圖。

FarmBeats 使您能夠為農場生成 NDVI、增強植被指數 （EVI） 和標準化差差水指數 （NDWI） 地圖。 這些指數有助於確定作物目前如何生長，或在過去增長，以及具有代表性的地面水位。


> [!NOTE]
> 生成地圖的天數需要哨兵圖像。


## <a name="create-a-satellite-indices-map"></a>創建衛星索引地圖

請遵循下列步驟。

1. 在主頁上，從左側導航功能表轉到 **"地圖**"。
2. 選擇 **"創建地圖**"，然後從下拉式功能表中選擇 **"衛星索引**"。

    ![從下拉式功能表中選擇衛星索引](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. 選擇 **"衛星索引**"後，將顯示 **"衛星索引"** 視窗。

    ![衛星索引視窗](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. 從下拉式功能表中選擇伺服器場。
   要搜索和選擇伺服器場，可以在下拉清單中滾動或輸入伺服器場的名稱。   
5. 要生成上周的地圖，請選擇 **"本周**"。
6. 要為自訂日期範圍生成地圖，請選擇 **"選擇日期範圍"** 選項。 輸入要為其生成衛星索引地圖的開始日期和結束日期。
7. 選擇 **"生成地圖**"。
    將顯示一條帶有作業詳細資訊的確認訊息。

    ![衛星索引地圖確認訊息](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    有關作業狀態的資訊，請參閱 **"查看作業**"部分。 如果作業狀態顯示 *"失敗"，* 則 *"失敗"* 狀態的工具提示上將顯示一條詳細的錯誤訊息。 在這種情況下，請重複前面的步驟，然後重試。

    如果問題仍然存在，請參閱[疑難排解](troubleshoot-azure-farmbeats.md)部分或與 Azure [FarmBeats 論壇聯繫，以尋求](https://aka.ms/FarmBeatsMSDN)相關日誌的支援。

### <a name="view-and-download-a-map"></a>查看和下載地圖

請遵循下列步驟。

1. 在主頁上，從左側導航功能表轉到 **"地圖**"。

    ![選擇地圖](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 從視窗的左側導航中選擇 **"篩選器**"。 **"篩選器"** 視窗顯示搜尋條件。

    ![篩選器視窗顯示搜尋條件](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 從下拉式功能表中選擇 **"鍵入**"、"**日期**"和 **"名稱"** 值。 然後選擇 **"應用"** 以搜索要查看的地圖。
  創建作業的日期以type_farmname_YYYY-MM-DD 格式顯示。

4. 使用頁面末尾的巡覽列滾動流覽可用的地圖清單。
5. 對於**伺服器場名稱**和**日期**的每個組合，可以使用以下三個映射：
    - Ndvi
    - EVI
    - NDWI
6. 選擇要查看的地圖。 快顯視窗顯示所選地圖的預覽。
7. 選擇"從下拉式功能表**下載**"以選擇下載格式。 地圖將下載並存儲在電腦上的本地資料夾中。

    ![選定的衛星索引地圖預覽](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>土壤水分熱圖

土壤水分是土壤顆粒之間的空間中持有的水。土壤水分熱圖可説明您瞭解任何深度的土壤水分資料，在農場內以高解析度瞭解土壤水分資料。 要生成準確且可用的土壤水分熱圖，需要統一部署感應器。 所有感應器必須來自同一供應商。 不同的供應商在土壤濕度的測量方式上存在差異，以及校準的差異。 使用部署于該深度的感應器，為特定深度生成熱圖。

### <a name="before-you-begin"></a>開始之前

在嘗試生成土壤水分熱圖之前，滿足以下先決條件：

- 必須至少部署三個土壤水分感應器。 在部署感應器並與伺服器場關聯之前，不要嘗試創建土壤水分熱圖。
- 生成土壤水分熱圖受哨兵的路徑覆蓋範圍、雲覆蓋和雲陰影的影響。 從要求土壤水分熱圖的那天起，至少至少一個無雲哨兵場景必須在過去 120 天內可用。
- 伺服器場上部署的至少一半的感應器必須處於線上狀態，並且資料必須資料流到資料庫。
- 熱圖必須使用來自同一提供程式的感應器度量來生成。


## <a name="create-a-soil-moisture-heatmap"></a>創建土壤水分熱圖

請遵循下列步驟。

1. 在主頁上，從左側導航功能表轉到 **"地圖**"以查看 **"地圖"** 頁。
2. 選擇 **"創建地圖**"，然後從下拉式功能表中選擇 **"土壤水分**"。

    ![從下拉式功能表中選擇土壤水分](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. 選擇**土壤水分**後，將顯示 **"土壤水分"** 視窗。

    ![土壤水分視窗](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. 從 **"伺服器場**"下拉式功能表中選擇伺服器場。
   要搜索和選擇伺服器場，可以從下拉清單中滾動或在 **"選擇伺服器場**下拉式功能表"中輸入伺服器場的名稱。
5. 在 **"選擇土壤水分感應器測量**下拉式功能表"中，選擇要為其生成地圖的土壤水分感應器度量（深度）。
要查找感應器測量值，請轉到**感應器**，然後選擇任何土壤濕度感應器。 然後，在 **"感應器屬性"** 部分下，使用**度量值名稱**中的值。
6. 要生成 **"今天**"或"**本周"** 的地圖，請選擇其中一個選項。
7. 要為自訂日期範圍生成地圖，請選擇 **"選擇日期範圍"** 選項。 輸入要為其生成土壤水分熱圖的開始日期和結束日期。
8. 選擇 **"生成地圖**"。
 將顯示一條帶有作業詳細資訊的確認訊息。

   ![土壤水分圖確認訊息](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    有關作業狀態的資訊，請參閱 **"查看作業**"部分。 如果作業狀態顯示 *"失敗"，* 則 *"失敗"* 狀態的工具提示上將顯示一條詳細的錯誤訊息。 在這種情況下，請重複前面的步驟，然後重試。

    如果問題仍然存在，請參閱[疑難排解](troubleshoot-azure-farmbeats.md)部分或與 Azure [FarmBeats 論壇聯繫，以尋求](https://aka.ms/FarmBeatsMSDN)相關日誌的支援。

### <a name="view-and-download-a-map"></a>查看和下載地圖

請遵循下列步驟。

1. 在主頁上，從左側導航功能表轉到 **"地圖**"。

    ![轉到地圖](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 從視窗的左側導航中選擇 **"篩選器**"。 **"篩選"** 視窗顯示可以從中搜索地圖。

    ![從左側導航中選擇"篩選器"](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  從下拉式功能表中選擇 **"鍵入**"、"**日期**"和 **"名稱"** 值。 然後選擇 **"應用"** 以搜索要查看的地圖。 創建作業的日期以type_farmname_YYYY-MM-DD 格式顯示。
4. 選擇表標題旁邊的**排序**圖示，以便根據 **"農場**"、**日期**、**創建時間**、**作業 ID**和**作業類型**進行排序。
5. 使用頁面末尾的導覽按鈕滾動流覽可用的地圖清單。
6. 選擇要查看的地圖。 快顯視窗顯示所選地圖的預覽。
7. 選擇"從下拉式功能表**下載**"以選擇下載格式。 地圖將下載並存儲在指定的資料夾中。

    ![土壤水分熱圖預覽](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
