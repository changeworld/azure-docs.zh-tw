---
title: 對應資料串流
description: 瞭解如何在映射數據流中設置源轉換。
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: e4f1369aa850a244128da470aee81b2efc2e09be
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413199"
---
# <a name="source-transformation-in-mapping-data-flow"></a>對應資料串流 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

源轉換為資料流配置數據源。 設計數據流時,您的第一步將始終配置源轉換。 要添加源,請單擊資料流畫布中的「**添加源**」框。

每個數據流至少需要一個源轉換,但您可以根據需要添加盡可能多的源來完成數據轉換。 您可以將這些源與聯接、尋找或聯合轉換聯接在一起。

每個源轉換都與一個數據工廠數據集關聯。 資料集定義要寫入或讀取的數據的形狀和位置。 如果使用基於文件的數據集,則可以使用源中的通配符和檔案清單一次處理多個檔。

## <a name="supported-source-connectors-in-mapping-data-flow"></a>映射資料串流中支援來源連接器

映射資料流遵循資料提取、載入、轉換 (ELT) 方法,並適用於 Azure 中所有*暫存*資料集。 目前,以下資料集可用於源轉換:
    
* [Azure Blob 儲存](connector-azure-blob-storage.md#mapping-data-flow-properties)(JSON、Avro、文本、鑲木地板)
* [Azure 資料儲存湖儲存第 1 代](connector-azure-data-lake-store.md#mapping-data-flow-properties)(JSON、Avro、文本、鑲木地板)
* [Azure 資料儲存第 2 代](connector-azure-data-lake-storage.md#mapping-data-flow-properties)(JSON、Avro、文本、鑲木地板)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure 宇宙DB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

特定於這些連接器的設置位於 **「源」選項**選項卡中。有關這些設置的資訊位於連接器文檔中。 

Azure 資料工廠可以存[取 90 多個本機連接器](connector-overview.md)。 要將數據包括來自這些其他源的數據,請使用複製活動將數據載入到受支援的過渡區域之一。

## <a name="source-settings"></a>來源設定

添加源後,請通過 **「源設置」** 選項卡進行配置。在這裡,您可以選取或創建原始點的數據集。 您還可以為數據選擇架構和採樣選項。

![來源設定選項卡](media/data-flow/source1.png "來源設定選項卡")

**測試連線:** 測試數據流的 spark 服務是否可以成功連接到源數據集中使用的連結服務。 必須啟用除錯模式才能啟用此功能。

**架構漂移:**[架構漂移](concepts-data-flow-schema-drift.md)是數據工廠在無需顯式定義列更改的情況下,在資料流中本機處理靈活架構的能力。

* 如果源列經常更改,請選中 **「允許架構漂移**框」。 此設置允許所有傳入源欄位通過轉換流向接收器。

* 選擇**推斷漂移列類型**將指示數據工廠檢測和定義發現的每個新列的數據類型。 關閉此功能後,所有漂移列都將為類型字串。

**驗證架構:** 如果選擇了驗證架構,則如果傳入源數據與數據集的已定義架構不匹配,則數據流將無法運行。

**跳過行數:** 跳過行計數位段指定數據集開始時要忽略的行數。

**抽樣:** 啟用採樣以限制源中的行數。 出於調試目的,在測試或從源中測試數據或採樣數據時,請使用此設置。

**多行行:** 如果源文本檔包含跨越多個行(即值內的新線)的字串值,請選擇多行。 此設定僅在分隔文本數據集中可用。

要驗證源配置正確,請打開除錯模式並獲取資料預覽。 有關詳細資訊,請參閱[除錯模式](concepts-data-flow-debug-mode.md)。

> [!NOTE]
> 啟用除錯模式時,除錯設定中的行限制配置將在資料預覽期間覆蓋來源中的取樣設定。

## <a name="projection"></a>投射

與資料集中的架構一樣,源中的投影從源數據定義數據列、類型和格式。 對於大多數數據集類型(如 SQL 和 Parquet),源中的投影是固定的,以反映在數據集中定義的架構。 當源檔未進行強類型時(例如,平面 csv 檔而不是 Parquet 檔),則可以在源轉換中定義每個欄位的資料類型。

![投影選項卡設定](media/data-flow/source3.png "投射")

如果文字檔沒有定義的架構,請選擇 **「檢測數據類型**」,以便數據工廠對數據類型進行採樣和推斷。 選擇 **「定義預設格式**」 以自動偵測預設資料格式。

**重置架構**將投影重置為引用的數據集中定義的內容。

您可以在下游派生列轉換中修改列數據類型。 使用選擇轉換修改列名稱。

### <a name="import-schema"></a>匯入架構

**"投影'** 選項卡上的 **'匯入架構"** 按鈕允許您使用活動除錯叢集建立架構投影。 每個源類型都可用,在此處導入架構將覆蓋數據集中定義的投影。 不會更改數據集物件。

這在支援複雜數據結構的 Avro 和 CosmosDB 等數據集中非常有用,不需要在數據集中存在架構定義。

## <a name="optimize-the-source-transformation"></a>優化來源轉換

在源轉換的 **「優化**」選項卡上,您可能會看到**源**分區類型。 僅當源是 Azure SQL 資料庫時,此選項才可用。 這是因為數據工廠嘗試使連接並行以針對 SQL 資料庫源運行大型查詢。

![來源分割區設定](media/data-flow/sourcepart3.png "分割")

不必在 SQL 資料庫源上對數據進行分區,但分區對於大型查詢很有用。 可以將分區基於列或查詢。

### <a name="use-a-column-to-partition-data"></a>使用欄對資料進行分割區

從源表中選擇要分區的列。 還設置分區數。

### <a name="use-a-query-to-partition-data"></a>使用查詢對資料進行分割區

您可以選擇根據查詢對連接進行分區。 輸入 WHERE 謂詞的內容。 例如,輸入 1980 >年份。

有關映射資料串流中的最佳資訊,請參閱[最佳化選項卡](concepts-data-flow-overview.md#optimize)。

## <a name="next-steps"></a>後續步驟

開始建立[衍生的欄位轉換](data-flow-derived-column.md)與[選擇轉換](data-flow-select.md)。
