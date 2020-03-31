---
title: 使用 Azure 宇宙資料庫、Azure 分析服務和 Power BI 創建即時儀表板
description: 瞭解如何使用 Azure Cosmos DB 和 Azure 分析服務在 Power BI 中創建即時天氣儀表板。
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70376589"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>使用 Azure Cosmos DB 和 Power BI 創建即時儀表板

本文介紹使用 Azure Cosmos DB 和 Azure 分析服務在 Power BI 中創建即時天氣儀表板所需的步驟。 Power BI 儀表板將顯示圖表，以顯示有關區域溫度和降雨量的即時資訊。

## <a name="reporting-scenarios"></a>報告方案

有多種方法可以設置 Azure Cosmos DB 中存儲的資料的報告儀表板。 根據陳舊要求和資料大小，下表描述了每種方案的報告設置：


|狀況 |安裝程式 |
|---------|---------|
|1. 生成臨時報告（不刷新）    |  [使用導入模式為 BI Azure 宇宙資料庫連接器供電](powerbi-visualize.md)       |
|2. 生成定期刷新的專案報告   |  [使用導入模式為 BI Azure Cosmos DB 連接器供電（計畫定期刷新）](powerbi-visualize.md)       |
|3. 報告大型資料集（< 10 GB）     |  通過增量刷新為 BI Azure 宇宙資料庫連接器供電       |
|4. 對大型資料集進行即時報告    |  使用直接查詢和 Azure 分析服務（Azure Cosmos DB 連接器）為 BI Azure 分析服務連接器供電       |
|5. 使用聚合報告即時資料     |  [電源 BI 火花連接器與直接查詢 + Azure 資料塊 + 宇宙 DB 火花連接器。](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. 報告大型資料集上具有聚合的即時資料   |  使用直接查詢 + Azure 分析服務 + Azure 資料塊 + 宇宙 DB Spark 連接器為 BI Azure 分析服務連接器供電。       |

可以使用 Azure Cosmos DB Power BI 連接器輕鬆設置方案 1 和 2。 本文介紹了方案 3 和 4 的設置。

### <a name="power-bi-with-incremental-refresh"></a>使用增量刷新為 BI 供電

Power BI 具有可配置增量刷新的模式。 此模式無需創建和管理 Azure 分析服務分區。 可以設置增量刷新，以便僅篩選大型資料集中的最新更新。 但是，此模式僅適用于資料集限制為 10 GB 的 Power BI 高級服務。

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>電源 BI Azure 分析連接器 + Azure 分析服務

Azure 分析服務提供完全託管的平臺，作為在雲中託管企業級資料模型的服務。 可以從 Azure Cosmos DB 載入到 Azure 分析服務中。 為了避免一直查詢整個資料集，可以將資料集細分為 Azure 分析服務分區，這些分區可以以不同頻率獨立刷新。

## <a name="power-bi-incremental-refresh"></a>電源 BI 增量刷新

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>將天氣資料引入 Azure 宇宙資料庫

設置引入管道以將[天氣資料](https://catalog.data.gov/dataset/local-weather-archive)載入到 Azure Cosmos DB。 您可以設置 Azure[資料工廠 （ADF）](../data-factory/connector-azure-cosmos-db.md)作業，以便使用 HTTP 源和 Cosmos DB 接收器定期將最新的天氣資料載入到 Azure Cosmos DB 中。


### <a name="connect-power-bi-to-azure-cosmos-db"></a>將 POWER BI 連接到 Azure 宇宙資料庫

1. **將 Azure Cosmos 帳戶連接到 Power BI** - 打開 Power BI 桌面並使用 Azure Cosmos DB 連接器選擇正確的資料庫和容器。

   ![Azure Cosmos DB Power BI 連接器](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **配置增量刷新**- 使用 Power BI 文章按照[增量刷新](/power-bi/service-premium-incremental-refresh)中的步驟為資料集配置增量刷新。 添加**RangeStart**和**RangeEnd**參數，如以下螢幕截圖所示：

   ![配置範圍參數](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   由於資料集具有文本形式的日期列，因此應轉換**RangeStart**和**RangeEnd**參數以使用以下篩選器。 在 **"高級編輯器"** 窗格中，修改查詢添加以下文本以根據 RangeStart 和 RangeEnd 參數篩選行：

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   根據源資料集中存在哪些列和資料類型，可以相應地更改 RangeStart 和 RangeEnd 欄位

   
   |屬性  |資料類型  |Filter  |
   |---------|---------|---------|
   |_ts     |   數值      |  [_ts] >持續時間.總秒數（範圍開始 - #datetime（1970，1，1，0，0））和[_ts] <持續時間。總秒數（範圍結束 - #datetime（1970，1，0，0））       |
   |日期（例如：- 2019-08-19）     |   String      | [文檔.date]>日期時間.到文本（範圍開始，"yyyy-MM-dd"）和[文檔.date] <日期時間。到文本（Range 結束，"yyyy-MM-dd"）        |
   |日期（例如：- 2019-08-11 12：00：00）   |  String       |  [文檔.date]>日期時間.至文本（範圍開始），yyyy-mm-dd HH：mm：ss"）和[文檔.date] <日期時間。到Text（Rangeend，"yyy-mm-dd HH：mm：ss"）       |


1. **定義刷新策略**- 通過導航到表**的上下文**功能表上的**增量刷新**選項卡來定義刷新策略。 將刷新策略設置為**每天**刷新並存儲上個月的資料。

   ![定義刷新策略](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   忽略警告，指出*無法確認 M 查詢已折疊*。 Azure Cosmos DB 連接器折疊篩選器查詢。

1. **載入資料並生成報告**- 通過使用之前載入的資料，創建圖表以報告溫度和降雨量。

   ![載入資料並生成報告](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **將報表發佈到 Power BI 高級**- 由於增量刷新僅是高級功能，因此發佈對話方塊只允許選擇高級容量上的工作區。 第一次重新整理可能需要較長的時間才能匯入歷程資料。 後續資料刷新要快得多，因為它們使用增量刷新。


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>電源 BI Azure 分析連接器 + Azure 分析服務 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>將天氣資料引入 Azure 宇宙資料庫 

設置引入管道以將[天氣資料](https://catalog.data.gov/dataset/local-weather-archive)載入到 Azure Cosmos DB。 您可以設置 Azure 資料工廠 （ADF） 作業，以便使用 HTTP 源和 Cosmos DB 接收器定期將最新的天氣資料載入到 Azure Cosmos DB 中。

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>將 Azure 分析服務連接到 Azure 宇宙帳戶

1. **創建新的 Azure 分析服務群集** - 創建 Azure[分析服務實例](../analysis-services/analysis-services-create-server.md)，該實例位於與 Azure Cosmos 帳戶和 Databricks 群集相同的區域中。

1. **在視覺化工作室** -  中創建新的分析服務表格專案[安裝 SQL 伺服器資料工具 （SSDT），](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)並在視覺化工作室中創建分析服務表格專案。

   ![創建 Azure 分析服務專案](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   選擇**集成工作區**實例，並將相容性級別設置為 SQL **Server 2017 / Azure 分析服務 （1400）**

   ![Azure 分析服務表格模型設計器](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **添加 Azure Cosmos 資料庫資料來源**- 導航到**模型**> **資料來源** > **新資料來源**，並添加 Azure Cosmos DB 資料來源，如下圖所示：

   ![添加宇宙資料庫資料來源](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   通過提供**帳戶 URI、****資料庫名稱**和**容器名稱**連接到 Azure Cosmos DB。 現在，您可以看到 Azure Cosmos 容器中的資料導入到 Power BI 中。

   ![預覽 Azure 宇宙資料庫資料](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **構造分析服務模型**- 打開查詢編輯器，執行所需的操作以優化載入的資料集：

   * 僅提取與天氣相關的列（溫度和降雨量）

   * 從表中提取月份資訊。 如下一節所述，此資料在創建分區時非常有用。

   * 將溫度列轉換為數字

   生成的 M 運算式如下所示：

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

   此外，將溫度列的資料類型更改為十進位，以確保這些值可以在 Power BI 中繪製。

1. **創建 Azure 分析分區**- 在 Azure 分析服務中創建分區，將資料集劃分為邏輯分區，這些分區可以獨立刷新，頻率不同。 在此示例中，您將創建兩個分區，將資料集劃分為最近一個月的資料和其他所有內容。

   ![創建分析服務分區](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   在 Azure 分析服務中創建以下兩個分區：

   * **最近一個月** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **歷史** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **將模型部署到 Azure 分析伺服器**- 按右鍵 Azure 分析服務專案並選擇 **"部署**"。 在 **"部署伺服器"屬性**窗格中添加伺服器名稱。

   ![部署 Azure 分析服務模型](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **配置分區刷新和合併**- Azure 分析服務允許獨立處理分區。 由於我們希望使用最新資料不斷更新 **"最近月份"** 分區，因此將刷新間隔設置為 5 分鐘。 不需要刷新歷史分區中的資料。 此外，您需要編寫一些代碼，以將最近月分區合併到歷史分區並創建新的最近月份分區。


## <a name="connect-power-bi-to-analysis-services"></a>將 POWER BI 連接到分析服務

1. **使用 Azure 分析服務資料庫連接器連接到 Azure 分析伺服器**- 選擇**即時模式**並連接到 Azure 分析服務實例，如以下螢幕截圖所示：

   ![從 Azure Analysis Services 取得資料](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **載入資料並生成報告**- 通過使用之前載入的資料，創建圖表以報告溫度和降雨量。 由於您要創建即時連接，因此應在上一步驟中部署的 Azure 分析服務模型中的資料上執行查詢。 新資料載入到 Azure Cosmos DB 後，溫度圖表將在五分鐘內更新。

   ![載入資料並生成報告](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Power BI，請參閱 [開始使用 Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)。

* [將 Qlik Sense 連線到 Azure Cosmos DB 並將資料視覺化](visualize-qlik-sense.md)