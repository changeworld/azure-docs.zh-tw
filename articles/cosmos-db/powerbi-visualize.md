---
title: Azure Cosmos DB 連接器的 Power BI 教學課程
description: 使用本 Power BI 教學課程以匯入 JSON、建立具深入資訊的報告以及使用 Azure Cosmos DB 和 Power BI 連接器將資料視覺化。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: fc3ca5fdde464ba63671512a6ebecd2c314cb192
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570827"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>使用 Power BI 連接器將 Azure Cosmos DB 資料視覺化

[Power BI](https://powerbi.microsoft.com/) 是一個線上服務，您可以在其中建立及共用儀表板和報表。 Power BI Desktop 是一個報表撰寫工具，可讓您從各種資料來源擷取資料。 Azure Cosmos DB 是其中一個可與 Power BI Desktop 搭配使用的資料來源。 您可以透過適用於 Power BI 的 Azure Cosmos DB 連接器將 Power BI Desktop 連線至 Azure Cosmos DB 帳戶。  將 Azure Cosmos DB 資料匯入至 Power BI 之後，您便可以轉換它、建立報表，然後將報表發佈至 Power BI。   

此文章說明將 Azure Cosmos DB 帳戶連線至 Power BI Desktop 所需的步驟。 連線之後，您可以瀏覽至集合、擷取資料、將 JSON 資料轉換成表格格式，然後將報表發佈至 Power BI。

> [!NOTE]
> 適用於 Azure Cosmos DB 的 Power BI 連接器會連線至 Power BI Desktop。 在 Power BI Desktop 中建立的報表可以發佈至 PowerBI.com。 您無法從 PowerBI.com 直接擷取 Azure Cosmos DB 資料。 

> [!NOTE]
> 目前僅支援 Azure Cosmos DB SQL API 和 Gremlin API 帳戶的 Azure Cosmos DB 與 Power BI 連接器連線。

## <a name="prerequisites"></a>Prerequisites
在依照本 Power BI 教學課程中的指示進行之前，請先確定您可以存取下列資源：

* [下載最新版的 Power BI Desktop](https://powerbi.microsoft.com/desktop)。

* 從 GitHub 下載[範例火山資料](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json)。

* [建立 Azure Cosmos 資料庫帳戶](https://azure.microsoft.com/documentation/articles/create-account/) ，並使用 [Azure Cosmos DB 資料移轉工具](import-data.md)來匯入火山資料。 當匯入資料時，請考慮資料移轉工具中來源與目的地的下列設定：

   * **來源參數** 

       * 匯**入來源：** JSON 檔案 (s) 

   * **目標參數** 

      * **連接字串：**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **分割區索引鍵：**/Country 

      * **集合輸送量：** 1000 

若要在 PowerBI.com 上共用您的報告，您必須有 PowerBI.com 中的帳戶。  若要深入瞭解 Power BI 和 Power BI Pro，請參閱 [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing) 。

## <a name="lets-get-started"></a>現在就開始吧
在本教學課程中，我們假設您是研究世界各地火山的地質學家。 火山資料儲存在 Azure Cosmos DB 帳戶中，JSON 文件格式如下：

```json
{
    "Volcano Name": "Rainier",
        "Country": "United States",
        "Region": "US-Washington",
        "Location": {
          "type": "Point",
          "coordinates": [
            -121.758,
            46.87
          ]
        },
        "Elevation": 4392,
        "Type": "Stratovolcano",
        "Status": "Dendrochronology",
        "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

您將從 Azure Cosmos DB 帳戶擷取火山資料，然後在互動式 Power BI 報表中將資料視覺化。

1. 執行 Power BI Desktop。

2. 您可以 [取得資料]****、檢視 [最近使用的來源]****，或直接從歡迎畫面 [開啟其他報表]****。 選取右上角的 [X]，即可關閉畫面。 Power BI Desktop 的 [報告] **** 檢視隨即顯示。
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

3. 選取 [首頁]**** 功能區，然後按一下 [取得資料]****。  此時應會出現 [取得資料] **** 視窗。

4. 按一下 [Azure]****、選取 [Azure Cosmos DB (搶鮮版 (Beta))]****，然後按一下 [連線]****。 

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbigetdata.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

5. 在 [預覽版連接器]**** 頁面上，按一下 [繼續]****。 [Azure Cosmos DB]**** 視窗隨即出現。

6. 依照下列所示方式指定您要從中擷取資料的 Azure Cosmos DB 帳戶端點 URL，然後按一下 [確定]****。 若要使用您的帳戶，可以從 Azure 入口網站 [**金鑰]** 刀鋒視窗的 [URI] 方塊中擷取 URL。 您可以視需要提供資料庫名稱、集合名稱，或使用導覽器來選取資料庫和集合，以識別資料的來源。
   
7. 如果是第一次連接到此端點，系統會提示您提供帳戶金鑰。 如果是您自己的帳戶，請從 Azure 入口網站 [**唯讀金鑰]** 刀鋒視窗的 [主要金鑰]**** 方塊中擷取金鑰。 輸入適當的金鑰，然後按一下 [連接]****。
   
   建議您在建置報告時使用唯讀金鑰。 這可避免不必要的主要金鑰暴露于潛在的安全性風險。 您可以從 Azure 入口網站的 [**金鑰**] 刀鋒視窗取得唯讀金鑰。 
    
8. 順利連接帳戶後，會出現 [瀏覽器] **** 窗格。 [瀏覽器] **** 會顯示帳戶下的資料庫清單。

9. 按一下並展開作為報表資料來源的資料庫，選取 [volcanodb]**** (您的資料庫名稱可能不同)。   

10. 現在，選取包含要擷取之資料的集合，選取 [volcano1]**** (您的集合名稱可能不同)。
    
    [預覽] 窗格會顯示 [記錄] **** 項目的清單。  一份文件會顯示為 Power BI 中的 [記錄] **** 類型。 同樣地，文件內的巢狀 JSON 區塊也是 [記錄] ****。
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbinavigator.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

12. 按一下 [編輯]**** 以在新視窗中啟動 [查詢編輯器] 來轉換資料。

## <a name="flattening-and-transforming-json-documents"></a>簡維化和轉換 JSON 文件
1. 切換至 [Power BI 查詢編輯器] 視窗，中央窗格內會顯示 [文件]**** 資料行。

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

1. 按一下位於 [文件] **** 資料行標頭右側的展開器。  此時會出現含有欄位清單的內容功能表。  選取您報告所需的欄位，例如火山名稱、國家、區域、位置、高度、類型、狀態和已知的上次爆發時間。 取消核取 [使用原始資料行名稱作為前置詞]**** 方塊，然後再按一下 [確定]****。
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

1. 中央窗格會顯示所選欄位的結果預覽。
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

1. 在我們的範例中，[位置] 屬性是文件中的 GeoJSON 區塊。  如您所見，[位置] 顯示為 Power BI Desktop 中的 [記錄] **** 類型。  

1. 按一下位於 [Document.Location] 資料行標頭右側的展開器。  此時會出現含有類型和座標欄位的內容功能表。  請選取座標欄位，並確定未選取 [使用原始資料行名稱作為前置詞]****，然後按一下 [確定]****。
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

1. 中央窗格現在會顯示 [清單] **** 類型的座標資料行。  如本教學課程一開始所說明，本教學課程中的 GeoJSON 資料屬於 Point 類型，具有座標陣列中所記錄的緯度和經度值。
   
   coordinates[0] 項目代表經度，coordinates[1] 則代表緯度。

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

1. 為了將座標陣列壓平合併，請建立名為 LatLong 的 [自訂資料行]****。  選取 [新增資料行]**** 功能區，然後按一下 [自訂資料行]****。  [自訂資料行]**** 視窗會隨即出現。

1. 提供新資料行的名稱，例如 LatLong。

1. 接下來，指定新資料行的自訂公式。  在我們的範例中，我們將依照下列方式使用以下公式，串連以逗號分隔的緯度和經度值： `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`。 按一下 [確定]  。
   
   如需有關資料分析運算式 (DAX) 包括 DAX 函數的詳細資訊，請造訪 [Power BI Desktop 中的 Dax 基本概念](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics)。
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

1. 現在，中央窗格會顯示已填入值的新 LatLong 資料行。
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::
    
    如果您在新的資料行中收到錯誤，請確定在 [查詢設定] 下套用的步驟與下圖相同︰
    
    :::image type="content" source="./media/powerbi-visualize/power-bi-applied-steps.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::
    
    如果步驟不同，請刪除額外的步驟，然後再試一次新增自訂資料行。 

1. 按一下 [關閉並套用] **** 以儲存資料模型。

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicloseapply.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>建置報告

[Power BI Desktop 報告] 檢視可做為您開始建立報告以視覺化資料的起始點。  您可以將欄位拖放到 [報告] **** 畫布上，以建立報告。

:::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview2.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

在 [報告] 檢視中，您應該會看到：

1. [欄位] **** 窗格，您會在這裡看到資料模型清單，以及您可以用於報告的欄位。
1. [視覺效果] **** 窗格。 一份報告可以包含一或多個視覺效果。  請從 [視覺效果] **** 窗格中選擇適合本身需求的視覺化類型。
1. [報告] **** 畫布，您可以在此處建置報告的視覺效果。
1. [報告] **** 頁面。 您可以在 Power BI Desktop 中心增多個報告頁面。

以下說明建立簡單的互動式地圖檢視報告的基本步驟。

1. 在此處的範例中，我們將建立會顯示每個火山所在位置的地圖檢視。  在 [視覺效果] **** 窗格中，按一下地圖視覺類型，如上方的螢幕擷取畫面所強調顯示。  您應該會看到地圖視覺化類型繪製於 [報告] **** 畫布上。  [視覺效果] **** 窗格也應該會顯示一組與地圖視覺化類型相關的屬性。
1. 現在，將 LatLong 欄位從 [欄位]**** 窗格拖放到 [視覺效果]**** 窗格中的 [位置]**** 屬性上。
1. 接下來，將 [火山名稱] 欄位拖放到 [圖例] **** 屬性上。  
1. 然後，將 [高度] 欄位拖放到 [大小] **** 屬性上。  
1. 您現在應該會看到地圖視覺化顯示一組表示每個火山所在位置的泡泡，且泡泡的大小會與火山的高度相關聯。
1. 現在您已建立基本報告。  您可以新增更多視覺效果，以進一步自訂報告。  在本例中，我們新增 [火山類型] 交叉分析篩選器，讓報告更具互動性。  
   
1. 在 [檔案] 功能表上按一下 [儲存]****，並將檔案儲存為 PowerBITutorial.pbix。

## <a name="publish-and-share-your-report"></a>發佈和共用您的報告
若要共用您的報告，您必須有 PowerBI.com 中的帳戶。

1. 在 Power BI Desktop 中，按一下 [首頁] **** 功能區。
1. 按一下 [發佈] 。  系統會提示您輸入 PowerBI.com 帳戶的使用者名稱和密碼。
1. 認證經過驗證後，報告即會發佈至您選取的目的地。
1. 按一下 [開啟 Power BI 中的 'PowerBITutorial.pbix'] **** 在 PowerBI.com 上查看與共用您的報表。
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

## <a name="create-a-dashboard-in-powerbicom"></a>在 PowerBI.com 中建立儀表板
現在，您有一份報表可在 PowerBI.com 上共用。

當您從 Power BI Desktop發佈報告至 PowerBI.com 時，在您的 PowerBI.com 租用戶中會產生 [報表]**** 和 [資料集]****。 例如，您將名為 **PowerBITutorial** 的報表發佈到 PowerBI.com，會在 PowerBI.com 的 [報表]**** 和 [資料集]**** 區段看到 PowerBITutorial。

   :::image type="content" source="./media/powerbi-visualize/powerbi-reports-datasets.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

若要建立可共用的儀表板，按一下 PowerBI.com 報表上的 [釘選即時頁面] **** 按鈕。

   :::image type="content" source="./media/powerbi-visualize/power-bi-pin-live-tile.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

依照 [從報表釘選圖格](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) 的指示建立新的儀表板。 

您也可以對之前建立的儀表板進行臨機操作修改。 不過，建議您使用 Power BI Desktop 進行修改，再將報表重新發佈至 PowerBI.com。

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

:::image type="content" source="./media/powerbi-visualize/power-bi-refresh-now.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    :::image type="content" source="./media/powerbi-visualize/power-bi-schedule-refresh.png" alt-text="Power BI Desktop 報告檢視 - Power BI 連接器":::
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>後續步驟
* 若要深入了解 Power BI，請參閱 [開始使用 Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)。
* 若要深入了解 Azure Cosmos DB，請參閱 [Azure Cosmos DB 文件登陸頁面](https://azure.microsoft.com/documentation/services/cosmos-db/)。

