---
title: Azure 監視器活頁簿地圖視覺效果
description: 瞭解 Azure 監視器活頁簿地圖視覺效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: 9b79efeeb90627bee6096c9142d8180896150295
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439653"
---
# <a name="map-visualization"></a>地圖視覺效果

地圖視覺效果可協助特定區域中的指標指標問題，並藉由提供匯總所有對應到每個位置/國家/地區之資料的功能，來顯示監視資料的高階匯總視圖。

下列螢幕擷取畫面顯示不同儲存體帳戶的交易總計和端對端延遲。 這裡的大小取決於交易總數，而地圖下方的色彩度量會顯示端對端延遲。 第一次觀察時， **美國西部** 區域中的交易數目很小（相較于 **美國東部** 區域），但 **美國西部** 區域的端對端延遲高於 **美國東部** 區域。 這提供了錯誤之處給 **美國西部** 的初步見解。

![Azure 位置對應的螢幕擷取畫面](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>新增對應

如果基礎資料/計量具有緯度/經度資訊、Azure 資源資訊、Azure 位置資訊或國家/地區、名稱或國家/地區代碼，則可以將地圖視覺化。

### <a name="using-azure-location"></a>使用 Azure 位置

1. 選取 [ **編輯** ] 工具列專案，將活頁簿切換至編輯模式。
2. 選取 [ **加入** ]，然後按一下 [ *加入查詢*]。
3. 將 *資料來源* 變更為， `Azure Resource Graph` 然後挑選具有儲存體帳戶的任何訂用帳戶。
4. 輸入下列查詢，以進行分析和選取 **執行查詢**。

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. 將 *大小* 設定為 `Large` 。
6. 將 *視覺效果* 設定為 `Map` 。
7. 所有設定都將會自動填入。 在 [自訂設定] 中，選取 [ **對應設定** ] 按鈕以開啟 [設定] 窗格。
8. 以下是地圖視覺效果的螢幕擷取畫面，其中顯示所選訂用帳戶的每個 Azure 區域的儲存體帳戶。

![具有上述查詢之 Azure 位置對應的螢幕擷取畫面](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>使用 Azure 資源

1. 選取 [ **編輯** ] 工具列專案，將活頁簿切換至編輯模式。
2. 選取 [ **新增** ]，然後 *新增度量*。
3. 使用具有儲存體帳戶的訂用帳戶。
4. 將資源 *類型* 變更為 `storage account` 資源，並在 *資源* 中選取多個儲存體帳戶。
5. 選取 [ **新增度量** ] 並新增交易計量。
    1. 命名空間：`Account`
    2. 度量： `Transactions`
    3. 聚集： `Sum`
    
    ![交易計量的螢幕擷取畫面](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. 選取 [ **新增度量** ]，並新增成功的 E2E 延遲計量。
    1. 命名空間：`Account`
    1. 度量： `Success E2E Latency`
    1. 聚集： `Average`
    
    ![成功端對端延遲計量的螢幕擷取畫面](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. 將 *大小* 設定為 `Large` 。
1. 將 *視覺效果* 大小設定為 `Map` 。
1. 在 [ **對應設定** ] 中，設定下列設定：
    1. 位置資訊使用： `Azure Resource`
    1. Azure 資源欄位： `Name`
    1. 大小依據： `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. 位置的匯總： `Sum of values`
    1. 著色類型： `Heatmap`
    1. 色彩依據： `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. 色彩的匯總： `Sum of values`
    1. 調色板： `Green to Red`
    1. 最小值： `0`
    1. 度量值： `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. 匯總其他計量的方式： `Sum of values`
    1. 選取 [ **自訂格式** ] 方塊
    1. 單位： `Milliseconds`
    1. 風格： `Decimal`
    1. 最大小數位數： `2`

### <a name="using-countryregion"></a>使用國家/地區

1. 選取 [ **編輯** ] 工具列專案，將活頁簿切換至編輯模式。
2. 選取 [*新增*]，然後 *加入 [查詢*]。
3. 將 *資料來源* 變更為 `Log` 。
4. 選取 [ *資源類型* ] `Application Insights` ，然後挑選任何有 pageViews 資料的 Application Insights 資源。
5. 使用查詢編輯器來輸入您分析的 KQL，然後選取 [ **執行查詢**]。

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. 將大小值設定為 `Large` 。
7. 將視覺效果設定為 `Map` 。
8. 所有設定都將會自動填入。 針對 [自訂設定]，選取 [ **對應設定**]。

### <a name="using-latitudelocation"></a>使用緯度/位置

1. 選取 [ **編輯** ] 工具列專案，將活頁簿切換至編輯模式。
2. 選取 [*新增*]，然後 *加入 [查詢*]。
3. 將 *資料來源* 變更為 `JSON` 。
1. 在 [查詢編輯器] 中輸入以下的 JSON 資料，然後選取 [ **執行查詢**]。
1. 將 *大小* 值設定為 `Large` 。
1. 將 *視覺效果* 設定為 `Map` 。
1. 在 [計量設定] 下的 [ **對應** ] 設定中，將計量 *標籤* 設定為， `displayName` 然後選取 [ **儲存後關閉**]。

下方的地圖視覺效果會顯示每個緯度和經度位置的使用者，以及針對度量所選取的標籤。

![地圖視覺效果的螢幕擷取畫面，其中顯示具有所選度量標籤的每個緯度和經度位置的使用者](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>地圖設定

### <a name="layout-settings"></a>版面配置設定

| 設定 | 說明 |
|:-------------|:-------------|
| `Location info using` | 選取一種方式來取得地圖上所顯示專案的位置。 <br> **緯度/經度**：如果有包含緯度和經度資訊的資料行，請選取此選項。 具有緯度和經度資料的每個資料列都會顯示為地圖上的相異專案。 <br> **Azure 位置**：如果有具有 Azure 位置的資料行 (eastus、westeurope、centralindia 等 ) 資訊，請選取此選項。 指定該資料行，它會針對每個 Azure 位置提取對應的緯度和經度，根據指定的匯總) ，將相同的位置資料列 (在一起，以顯示地圖上的位置。 <br> **Azure 資源**：如果有資料行有 (儲存體帳戶、cosmosdb 帳戶等 ) 的 azure 資源資訊，請選取此選項。 指定該資料行，它會針對每個 Azure 資源提取對應的緯度和經度， (Azure 位置) 資料列中的相同位置， (根據) 一起指定的匯總來顯示地圖上的位置。 <br> **國家/地區**：如果有一個資料行具有國家/地區名稱/程式碼 (美國、美國、印度、印度、CN、中國) 資訊，請選取此選項。 指定該資料行，它會針對每個國家/地區/程式碼提取對應的緯度和經度，然後將這些資料列與相同的 Country-Region 程式碼/國家/地區名稱一起分組，以顯示地圖上的位置。 國家/地區名稱和國家/地區代碼不會群組在一起，成為地圖上的單一實體。
| `Latitude/Longitude` | 如果位置資訊域值為：緯度/經度，則會顯示這兩個選項。 在 [緯度] 欄位中選取具有緯度的資料行，並分別選取 [經度] 欄位中的經度。 |
| `Azure location field` | 如果 [位置資訊] 域值為： Azure 位置，則會顯示此選項。 選取 Azure 位置資訊的資料行。 |
| `Azure resource field` | 如果位置資訊域值為： Azure 資源，則會顯示此選項。 選取 Azure 資源資訊的資料行。 |
| `Country/Region field` | 如果位置資訊域值是：國家或地區，則會顯示此選項。 選取國家/地區資訊的資料行。 |
| `Size by` | 此選項會控制地圖上所顯示專案的大小。 大小取決於使用者指定的資料行中的值。 目前，圓形的半徑與資料行值的平方根是直接成正比。 If ' None ... '選取此選項時，所有圓形都會顯示預設的區域大小。|
| `Aggregation for location` | 此欄位會指定如何依具有相同 Azure 位置/Azure 資源/國家/地區的資料行來匯總 **大小** 。 |
| `Minimum region size` | 此欄位指定地圖上所顯示專案的最小半徑。 當大小依資料行的值有顯著差異時，就會使用這種方式，因此地圖上幾乎不會顯示較小的專案。 |
| `Maximum region size` | 此欄位指定地圖上所顯示專案的最大半徑。 當 size by 資料行的值非常大，而且它們涵蓋地圖的大型區域時，就會使用此值。|
| `Default region size` | 此欄位指定地圖上所顯示專案的預設半徑。 當 Size By 資料行是「無 ...」時，會使用預設半徑或值為0。|
| `Minimum value` | 用來計算區域大小的最小值。 如果未指定，則最小值會是匯總之後的最小值。 |
| `Maximum value` | 用來計算區域大小的最大值。 如果未指定，則最大值會是匯總之後的最大值。|
| `Opacity of items on Map` | 此欄位會指定在地圖上顯示專案的透明程度。 不透明度1表示沒有透明度，其中0表示不透明度，專案不會顯示在地圖上。 如果對應中有太多專案，則不透明度可以設定為 [低] 值，以便顯示所有重迭的專案。|

### <a name="color-settings"></a>色彩設定

| 色彩類型 | 說明 |
|:------------- |:-------------|
| `None` | 所有節點都有相同的色彩。 |
| `Thresholds` | 在此類型中，資料格色彩是由閾值規則設定 (例如， _cpu > 90% => 紅色、60% > cpu > 90% => 黃色，cpu < 60% => 綠色_) 。 <ul><li> **色彩依據**：臨界值/熱度圖邏輯會使用此資料行的值。</li> <li>**色彩的匯總**：此欄位會指定如何依具有相同 azure 位置/azure 資源/國家/地區的資料行來匯總 **色彩** 。 </li> <ul> |
| `Heatmap` | 在此類型中，儲存格會根據 [色彩選擇區] 和 [依欄位色彩] 來著色。 這樣也會有相同的 **色彩** ，並將 **色彩選項匯總** 為臨界值。 |

### <a name="metric-settings"></a>度量設定
| 設定 | 說明 |
|:------------- |:-------------|
| `Metric Label` | 如果位置資訊域值為：緯度/經度，則會顯示此選項。 使用這項功能，使用者可以選擇要針對地圖下方顯示的度量來顯示的標籤。 |
| `Metric Value` | 此欄位會指定要顯示在地圖下方的度量值。 |
| `Create 'Others' group after` | 此欄位會指定「其他」群組建立之前的限制。 |
| `Aggregate 'Others' metrics by` | 此欄位會指定「其他」群組使用的匯總（如果有顯示的話）。 |
| `Custom formatting` | 您可以使用此欄位來設定數位值的單位、樣式和格式化選項。 這等同于 [方格的自訂格式](workbooks-grid-visualizations.md#custom-formatting)。|

## <a name="next-steps"></a>後續步驟

- 瞭解如何在活頁 [簿中建立 honey 合併數十億視覺效果](workbooks-honey-comb.md)。