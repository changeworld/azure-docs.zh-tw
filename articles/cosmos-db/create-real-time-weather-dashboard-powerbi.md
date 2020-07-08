---
title: 使用 Azure Cosmos DB、Azure Analysis Services 和 Power BI 建立即時儀表板
description: 瞭解如何使用 Azure Cosmos DB 和 Azure Analysis Services 在 Power BI 中建立即時氣象儀表板。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/04/2019
ms.reviewer: sngun
ms.openlocfilehash: eda3ee3e9e170469ffb0b9b0e1d7dede181fe3f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261999"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>使用 Azure Cosmos DB 和 Power BI 建立即時儀表板

本文說明使用 Azure Cosmos DB 和 Azure Analysis Services 在 Power BI 中建立即時氣象儀表板所需的步驟。 [Power BI] 儀表板會顯示圖表，以顯示區域中溫度和降雨量的即時資訊。

## <a name="reporting-scenarios"></a>報告案例

有多種方式可設定報表儀表板，以儲存在 Azure Cosmos DB 中的資料。 根據過期需求和資料大小，下表說明每個案例的報告設定：


|狀況 |安裝程式 |
|---------|---------|
|1. 產生臨機操作報表（無重新整理）    |  [使用匯入模式 Power BI Azure Cosmos DB 連接器](powerbi-visualize.md)       |
|2. 使用定期重新整理來產生特定報表   |  [使用匯入模式 Power BI Azure Cosmos DB 連接器（排定的定期重新整理）](powerbi-visualize.md)       |
|3. 報告大型資料集（< 10 GB）     |  具有累加式重新整理的 Power BI Azure Cosmos DB 連接器       |
|4. 報告大型資料集的即時時間    |  使用直接查詢 Power BI Azure Analysis Services 連接器 + Azure Analysis Services （Azure Cosmos DB 連接器）       |
|5. 使用匯總報告即時資料     |  [Power BI Spark 連接器與直接查詢 + Azure Databricks + Cosmos DB Spark 連接器。](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. 以大型資料集上的匯總報告即時資料   |  使用直接查詢 Power BI Azure Analysis Services 連接器 + Azure Analysis Services + Azure Databricks + Cosmos DB Spark 連接器。       |

案例1和2可以使用 Azure Cosmos DB Power BI 連接器輕鬆設定。 本文說明案例3和4的各項設置。

### <a name="power-bi-with-incremental-refresh"></a>具有累加式重新整理的 Power BI

Power BI 具有可設定累加式重新整理的模式。 此模式可讓您不需要建立和管理 Azure Analysis Services 的磁碟分割。 累加式重新整理可以設定為僅篩選大型資料集的最新更新。 不過，此模式只適用于資料集限制為 10 GB 的 Power BI Premium 服務。

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure 分析連接器 + Azure Analysis Services

Azure Analysis Services 提供完全受控的平臺即服務，可在雲端中裝載企業級的資料模型。 您可以從 Azure Cosmos DB 將大量資料集載入至 Azure Analysis Services。 若要避免隨時查詢整個資料集，您可以將資料集細分為 Azure Analysis Services 分割區，這可以在不同的頻率獨立重新整理。

## <a name="power-bi-incremental-refresh"></a>Power BI 累加式重新整理

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>將氣象資料內嵌到 Azure Cosmos DB

設定內嵌管線，以將[氣象資料](https://catalog.data.gov/dataset/local-weather-archive)載入 Azure Cosmos DB。 您可以設定[Azure Data Factory （ADF）](../data-factory/connector-azure-cosmos-db.md)作業，以使用 HTTP 來源和 Cosmos DB 接收，定期將最新的氣象資料載入 Azure Cosmos DB。


### <a name="connect-power-bi-to-azure-cosmos-db"></a>將 Power BI 連接到 Azure Cosmos DB

1. **將 Azure Cosmos 帳戶連線至 Power BI** -開啟 Power BI Desktop，並使用 Azure Cosmos DB 連接器來選取正確的資料庫和容器。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png" alt-text="Azure Cosmos DB Power BI 連接器":::

1. **設定**累加式重新整理-遵循累加[式重新整理與 Power BI](/power-bi/service-premium-incremental-refresh)一文中的步驟，為資料集設定累加式重新整理。 新增**RangeStart**和**到 rangeend**參數，如下列螢幕擷取畫面所示：

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png" alt-text="設定範圍參數":::

   因為資料集具有文字格式的日期資料行，所以應該將**RangeStart**和**到 rangeend**參數轉換成使用下列篩選準則。 在 [**進階編輯器**] 窗格中，修改您的查詢新增下列文字，以根據 RangeStart 和到 rangeend 參數篩選資料列：

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   根據源資料集中出現的資料行和資料類型而定，您可以據以變更 RangeStart 和到 rangeend 欄位

   
   |屬性  |資料類型  |Filter  |
   |---------|---------|---------|
   |_ts     |   數值      |  [_ts] > Duration. TotalSeconds （RangeStart-#datetime （1970，1，1，0，0，0））和 [_ts] < Duration. TotalSeconds （到 rangeend-#datetime （1970，1，1，0，0，0）））       |
   |日期（例如：-2019-08-19）     |   String      | [Document. date] > DateTime. ToText （RangeStart，"yyyy-mm-dd"）和 [Document. date] < DateTime. ToText （到 rangeend，"yyyy-mm-dd"）        |
   |日期（例如：-2019-08-11 12:00:00）   |  String       |  [Document. date] > ToText （RangeStart，"yyyy-mm-dd HH： mm： ss"）和 [Document. date] < DateTime. ToText （到 rangeend，"yyyy-mm-dd HH： mm： ss"）       |


1. **定義**重新整理原則-流覽至資料表**內容**功能表上的 [累加**式**重新整理] 索引標籤，以定義重新整理原則。 將重新整理原則設定為**每天**重新整理，並儲存上個月的資料。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png" alt-text="定義重新整理原則":::

   忽略指出*無法將 M 查詢確認為折*迭的警告。 Azure Cosmos DB 連接器會折迭篩選查詢。

1. **載入資料並產生報告**-使用您稍早載入的資料，建立圖表以報告溫度和降雨量。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="載入資料並產生報告":::

1. 將**報表發行至 Power BI premium** -由於累加式重新整理是僅限 premium 的功能，因此 [發行] 對話方塊只允許在 premium 容量上選取工作區。 第一次重新整理可能需要較長的時間才能匯入歷程資料。 後續資料重新整理的速度會更快，因為它們會使用累加式重新整理。


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure 分析連接器 + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>將氣象資料內嵌到 Azure Cosmos DB 

設定內嵌管線，以將[氣象資料](https://catalog.data.gov/dataset/local-weather-archive)載入 Azure Cosmos DB。 您可以設定 Azure Data Factory （ADF）作業，以使用 HTTP 來源和 Cosmos DB 接收，定期將最新的氣象資料載入 Azure Cosmos DB。

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>將 Azure Analysis Services 連線至 Azure Cosmos 帳戶

1. **建立新的 Azure Analysis Services**  -  叢集在與 Azure Cosmos 帳戶和 Databricks 叢集相同的區域中，[建立 Azure Analysis services 的實例](../analysis-services/analysis-services-create-server.md)。

1. **在 Visual Studio**  -   中建立新的 Analysis Services 表格式專案[安裝 SQL Server Data Tools （SSDT）](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) ，並在 Visual Studio 中建立 Analysis Services 表格式專案。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png" alt-text="建立 Azure Analysis Services 專案":::

   選擇**整合式工作區**實例，並將相容性層級設定為**SQL Server 2017/Azure Analysis Services （1400）**

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png" alt-text="Azure Analysis Services 表格式模型設計師":::

1. **新增 Azure Cosmos DB 資料來源**-導覽至 [**模型**] [資料來源] [ >  **Data Sources**  >  **新增資料來源**]，然後加入 Azure Cosmos DB 資料來源，如下列螢幕擷取畫面所示：

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/add-data-source.png" alt-text="新增 Cosmos DB 資料來源":::

   藉由提供**帳戶 URI**、**資料庫名稱**和**容器名稱**，連接到 Azure Cosmos DB。 您現在可以看到 Azure Cosmos 容器中的資料已匯入 Power BI。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png" alt-text="預覽 Azure Cosmos DB 資料":::

1. **建立 Analysis Services 模型**-開啟 [查詢編輯器]，執行必要的作業，以將載入的資料集優化：

   * 僅解壓縮氣象相關資料行（溫度和降雨量）

   * 從資料表中解壓縮月份資訊。 這項資料在建立分割區時非常有用，如下一節所述。

   * 將溫度資料行轉換成數位

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

   此外，將 [溫度] 資料行的 [資料類型] 變更為 [十進位]，以確保這些值可以在 Power BI 中繪製。

1. **建立 Azure 分析**資料分割-在 Azure Analysis Services 中建立分割區，將資料集分成可獨立重新整理並以不同頻率重新整理的邏輯磁碟分割。 在此範例中，您會建立兩個分割區，將資料集分成最近一個月的資料，以及其他專案。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png" alt-text="建立 analysis services 資料分割":::

   在 Azure Analysis Services 中建立下列兩個磁碟分割：

   * **最新月份** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **歷程記錄** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. 將**模型部署至 azure Analysis Server** -在 azure Analysis Services 專案上按一下滑鼠右鍵，然後選擇 [**部署**]。 在 [**部署伺服器屬性**] 窗格中新增伺服器名稱。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png" alt-text="部署 Azure Analysis Services 模型":::

1. **設定磁碟分割重新整理和合併**-Azure Analysis Services 允許獨立處理資料分割。 因為我們想要以最新的資料持續更新**最新的月份**分割，請將重新整理間隔設定為5分鐘。 您可以使用[REST API](../analysis-services/analysis-services-async-refresh.md)、 [Azure 自動化](../analysis-services/analysis-services-refresh-azure-automation.md)或[邏輯應用程式](../analysis-services/analysis-services-refresh-logic-app.md)來重新整理資料。 您不需要重新整理歷程記錄分割中的資料。 此外，您還需要撰寫一些程式碼，將最新的月份資料分割合併到歷程資料分割，並建立新的最新月份資料分割。

## <a name="connect-power-bi-to-analysis-services"></a>將 Power BI 連接到 Analysis Services

1. **使用 azure Analysis Services 資料庫連接器連接到 azure Analysis Server** -選擇 [**即時] 模式**並聯機至 azure Analysis Services 實例，如下列螢幕擷取畫面所示：

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png" alt-text="從 Azure Analysis Services 取得資料":::

1. **載入資料並產生報告**-使用您稍早載入的資料，建立圖表以報告溫度和降雨量。 由於您正在建立即時連線，因此應該在您于上一個步驟中部署的 Azure Analysis Services 模型中的資料上執行查詢。 將新資料載入 Azure Cosmos DB 之後，會在五分鐘內更新溫度圖表。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="載入資料並產生報告":::

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Power BI，請參閱 [開始使用 Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)。

* [將 Qlik Sense 連線到 Azure Cosmos DB 並將資料視覺化](visualize-qlik-sense.md)
