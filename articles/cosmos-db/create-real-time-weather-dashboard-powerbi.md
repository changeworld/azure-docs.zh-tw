---
title: 使用 Azure Cosmos DB、Azure Analysis Services 和 Power BI 來建立即時儀表板
description: 瞭解如何使用 Azure Cosmos DB 和 Azure Analysis Services，在 Power BI 中建立即時氣象儀表板。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 681929928e6e6b28c7950c8aeeadc8b181491f46
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91804124"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>使用 Azure Cosmos DB 和 Power BI 建立即時儀表板

本文說明使用 Azure Cosmos DB 和 Azure Analysis Services 在 Power BI 中建立即時氣象儀表板所需的步驟。 Power BI 儀表板會顯示圖表，以顯示區域中溫度和降雨量的即時資訊。

## <a name="reporting-scenarios"></a>報表案例

有多種方式可針對儲存在 Azure Cosmos DB 中的資料設定報告儀表板。 根據過期需求和資料大小，下表描述每個案例的報告設定：


|狀況 |安裝程式 |
|---------|---------|
|1. 產生臨機操作報表 (沒有重新整理)     |  [使用匯入模式 Power BI Azure Cosmos DB 連接器](powerbi-visualize.md)       |
|2. 使用定期重新整理來產生特定報表   |  [使用匯入模式 Power BI Azure Cosmos DB 連接器 (排程的定期更新) ](powerbi-visualize.md)       |
|3. 報告大型資料集 ( # A0 10 GB)      |  具有累加式更新的 Power BI Azure Cosmos DB 連接器       |
|4. 報告大型資料集的即時時間    |  使用直接查詢來 Power BI Azure Analysis Services 連接器 + Azure Analysis Services (Azure Cosmos DB 連接器)        |
|5. 使用匯總報告即時資料     |  [使用 direct query + Azure Databricks + Cosmos DB Spark 連接器 Power BI Spark 連接器。](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. 使用大型資料集的匯總來報告即時資料   |  使用 direct query Power BI Azure Analysis Services 連接器 + Azure Analysis Services + Azure Databricks + Cosmos DB Spark 連接器。       |

您可以使用 Azure Cosmos DB Power BI 連接器，輕鬆地設定案例1和2。 本文說明案例3和4的設置。

### <a name="power-bi-with-incremental-refresh"></a>具有累加式重新整理的 Power BI

Power BI 具有可設定累加式重新整理的模式。 這種模式不需要建立及管理 Azure Analysis Services 資料分割。 累加式重新整理可設定為只篩選大型資料集中的最新更新。 不過，此模式只適用于資料集限制為 10 GB 的 Power BI Premium 服務。

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis connector + Azure Analysis Services

Azure Analysis Services 提供完全受控的平臺即服務，可裝載雲端中的企業級資料模型。 您可以從 Azure Cosmos DB 將大量資料集載入至 Azure Analysis Services。 為了避免隨時查詢整個資料集，您可以將資料集細分為 Azure Analysis Services 分割區，而這些資料分割可在不同的頻率獨立重新整理。

## <a name="power-bi-incremental-refresh"></a>Power BI 累加式更新

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>將天氣資料內嵌至 Azure Cosmos DB

設定可將 [天氣資料](https://catalog.data.gov/dataset/local-weather-archive) 載入 Azure Cosmos DB 的內嵌管線。 您可以設定 [Azure Data Factory (ADF) ](../data-factory/connector-azure-cosmos-db.md) 作業，以使用 HTTP 來源和 Cosmos DB 接收，定期將最新的氣象資料載入 Azure Cosmos DB。


### <a name="connect-power-bi-to-azure-cosmos-db"></a>將 Power BI 連接到 Azure Cosmos DB

1. **將 Azure Cosmos 帳戶連線到 Power BI** -開啟 Power BI Desktop，然後使用 Azure Cosmos DB 連接器來選取正確的資料庫和容器。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png" alt-text="Azure Cosmos DB Power BI 連接器":::

1. **設定累加式** 重新整理：依照 Power BI 文章的累加 [式](/power-bi/service-premium-incremental-refresh) 重新整理中的步驟，設定資料集的累加式重新整理。 新增 **RangeStart** 和 **到 rangeend** 參數，如下列螢幕擷取畫面所示：

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png" alt-text="Azure Cosmos DB Power BI 連接器" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   視源資料集中存在的資料行和資料類型而定，您可以據此變更 RangeStart 和到 rangeend 欄位

   
   |屬性  |資料類型  |Filter  |
   |---------|---------|---------|
   |_ts     |   數值      |  [_ts] > 持續時間。 TotalSeconds (RangeStart-#datetime (1970、1、1、0、0、0) # A3 和 [_ts] < 持續時間。 TotalSeconds (到 rangeend-#datetime (1970，1，1，0，0，0) # A7 # A8       |
   |日期 (例如：-2019-08-19)      |   字串      | [Document. date] > DateTime. Duration.totext (RangeStart，"yyyy-mm-dd" ) 和 [Document. date] < DateTime. Duration.totext (到 rangeend，"yyyy-mm-dd" )         |
   |日期 (例如：-2019-08-11 12:00:00)    |  字串       |  [Document. date] > DateTime. Duration.totext (RangeStart，"yyyy-mm-dd HH： mm： ss" ) 和 [Document. date] < DateTime. Duration.totext (到 rangeend，"yyyy-mm-dd HH： mm： ss" )        |


1. **定義**重新整理原則：流覽至資料表**內容**功能表上的 [累加**式**重新整理] 索引標籤，以定義重新整理原則。 將重新整理原則設定為 **每天重新整理，並儲存** 上個月的資料。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png" alt-text="Azure Cosmos DB Power BI 連接器":::

   略過警告，指出 *無法確認 M 查詢是否要折*迭。 Azure Cosmos DB 連接器折迭篩選查詢。

1. **載入資料並產生報表** -使用您先前載入的資料，建立要報告溫度和降雨量的圖表。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Azure Cosmos DB Power BI 連接器":::

1. 將**報表發行至 Power BI premium** -因為累加式重新整理是僅限 premium 的功能，所以 [發佈] 對話方塊只允許選取 premium 容量上的工作區。 第一次重新整理可能需要較長的時間才能匯入歷程資料。 後續資料重新整理的速度會更快，因為它們會使用累加式重新整理。


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis connector + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>將天氣資料內嵌至 Azure Cosmos DB 

設定可將 [天氣資料](https://catalog.data.gov/dataset/local-weather-archive) 載入 Azure Cosmos DB 的內嵌管線。 您可以設定 Azure Data Factory (ADF) 作業，以使用 HTTP 來源和 Cosmos DB 接收，定期將最新的氣象資料載入 Azure Cosmos DB。

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>將 Azure Analysis Services 連線到 Azure Cosmos 帳戶

1. **建立新的 Azure Analysis Services**  -  叢集在與 Azure Cosmos 帳戶和 Databricks 叢集相同的區域中，[建立 Azure Analysis services 的實例](../analysis-services/analysis-services-create-server.md)。

1. **在 Visual Studio**  -   中建立新的 Analysis Services 表格式專案[安裝 SQL Server Data Tools (SSDT) ](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017&preserve-view=true) ，並在 Visual Studio 中建立 Analysis Services 表格式專案。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png" alt-text="Azure Cosmos DB Power BI 連接器":::

   選擇 **整合式工作區** 實例，並將相容性層級設定為 **SQL Server 2017/Azure Analysis Services (1400) **

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png" alt-text="Azure Cosmos DB Power BI 連接器":::

1. **加入 Azure Cosmos DB 資料來源**-流覽至**模型** >  **資料來源**的  >  **新資料來源**，並加入 Azure Cosmos DB 資料來源，如下列螢幕擷取畫面所示：

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/add-data-source.png" alt-text="Azure Cosmos DB Power BI 連接器":::

   提供 **帳戶 URI**、 **資料庫名稱**和 **容器名稱**，以連接到 Azure Cosmos DB。 您現在可以看到 Azure Cosmos 容器中的資料匯入 Power BI。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png" alt-text="Azure Cosmos DB Power BI 連接器":::

1. **建立 Analysis Services 模型** -開啟查詢編輯器，執行必要的作業以優化載入的資料集：

   * 只解壓縮氣象相關資料行 (溫度和降雨量) 

   * 從資料表中解壓縮月份資訊。 這項資料在建立資料分割時相當有用，如下一節所述。

   * 將溫度資料行轉換為數字

   產生的 M 運算式如下所示：

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   此外，請將溫度資料行的資料類型變更為 Decimal，以確保這些值可以在 Power BI 中繪製。

1. **建立 Azure 分析** 資料分割-在 Azure Analysis Services 中建立資料分割，以將資料集分割成可獨立和不同頻率重新整理的邏輯分割區。 在此範例中，您會建立兩個分割區，將資料集分成最近月份的資料，以及其他所有資料。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png" alt-text="Azure Cosmos DB Power BI 連接器" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **歷史** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. 將**模型部署到 azure Analysis Server** -在 azure Analysis Services 專案上按一下滑鼠右鍵，然後選擇 [**部署**]。 在 [ **部署伺服器屬性** ] 窗格中加入伺服器名稱。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png" alt-text="Azure Cosmos DB Power BI 連接器":::

1. **設定資料分割重新整理和合併** -Azure Analysis Services 允許獨立處理資料分割。 因為我們想要以最新的資料持續更新 **最新的月份** 資料分割，所以請將重新整理間隔設定為5分鐘。 您可以使用 [REST API](../analysis-services/analysis-services-async-refresh.md)、 [Azure 自動化](../analysis-services/analysis-services-refresh-azure-automation.md)或 [邏輯應用程式](../analysis-services/analysis-services-refresh-logic-app.md)來重新整理資料。 不需要重新整理歷程記錄分割區中的資料。 此外，您還需要撰寫一些程式碼，將最新的月份資料分割合併到歷程記錄分割區，然後建立新的最新月份資料分割。

## <a name="connect-power-bi-to-analysis-services"></a>將 Power BI 連接到 Analysis Services

1. **使用 azure Analysis Services Database Connector 連線到 azure Analysis Server** -選擇 **即時模式** 並聯機到 azure Analysis Services 實例，如下列螢幕擷取畫面所示：

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png" alt-text="Azure Cosmos DB Power BI 連接器":::

1. **載入資料並產生報表** -使用您先前載入的資料，建立可報告溫度和降雨量的圖表。 因為您要建立即時連線，所以應針對您在上一個步驟中部署的 Azure Analysis Services 模型中的資料執行查詢。 將新資料載入 Azure Cosmos DB 後的五分鐘內，就會更新溫度圖表。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Azure Cosmos DB Power BI 連接器":::

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Power BI，請參閱 [開始使用 Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)。

* [將 Qlik Sense 連線到 Azure Cosmos DB 並將資料視覺化](visualize-qlik-sense.md)
