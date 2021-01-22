---
title: 儲存體總覽-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: 瞭解 Azure 時間序列深入解析 Gen2 中的資料儲存體。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 748eaca93eaee5ec858ea43261995111cef8ceda
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676846"
---
# <a name="data-storage"></a>資料儲存體

本文說明 Azure 時間序列深入解析 Gen2 中的資料儲存體。 其中涵蓋暖和冷、資料可用性和最佳作法。

## <a name="provisioning"></a>佈建

當您建立 Azure 時間序列深入解析 Gen2 環境時，您有下列選項：

* 冷資料存放區：
  * 在您為環境選擇的訂用帳戶和區域中，建立新的 Azure 儲存體資源。
  * 附加既有的 Azure 儲存體帳戶。 此選項僅適用于從 Azure Resource Manager [範本](/azure/templates/microsoft.timeseriesinsights/allversions)進行部署，且不會顯示在 Azure 入口網站中。
* 暖資料存放區：
  * 暖存放區是選擇性的，可以在布建期間或之後啟用或停用。 如果您決定稍後啟用暖存放區，而且您的冷存放區中已經有資料， [請參閱下面](concepts-storage.md#warm-store-behavior) 的小節以瞭解預期的行為。 暖存放區資料保留時間可以設定為7到31天，也可以視需要調整。

當事件內嵌時，如果已啟用) 和冷存放區，則會在暖存放區 (中建立索引。

[![儲存體總覽](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> 身為冷存放區資料所在 Azure Blob 儲存體帳戶的擁有者，您可以完整存取帳戶中的所有資料。 此存取權包括寫入和刪除權限。 請勿編輯或刪除 Azure 時間序列深入解析 Gen2 寫入的資料，因為這可能會導致資料遺失。

## <a name="data-availability"></a>資料可用性

Azure 時間序列深入解析 Gen2 資料分割和索引資料，以獲得最佳查詢效能。 如果在索引之後啟用) 和冷存放區，就可以從暖 (中查詢資料。 正在內嵌的資料量和每個分割區的輸送量速率可能會影響可用性。 請參閱事件來源 [輸送量限制](./concepts-streaming-ingress-throughput-limits.md) 和 [最佳作法](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) ，以獲得最佳效能。 您也可以設定延遲 [警示](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) ，以在您的環境遇到處理資料的問題時收到通知。

> [!IMPORTANT]
> 在資料變成可用之前，您可能會遇到最多60秒的時間。 如果您遭遇超過 60 秒的嚴重延遲，請透過 Azure 入口網站提交支援票證。

## <a name="warm-store"></a>暖存放區

您可以透過 [時間序列查詢 api](./concepts-query-overview.md)、 [Azure 時間序列深入解析 TSI Explorer](./concepts-ux-panels.md)或 [Power BI 連接器](./how-to-connect-power-bi.md)，取得暖存放區中的資料。 暖存放區查詢是免費的，而且沒有配額，但有 [30 個](/rest/api/time-series-insights/reference-api-limits#query-apis---limits) 並行要求的限制。

### <a name="warm-store-behavior"></a>暖存放區行為

* 啟用時，串流至您環境的所有資料都會路由傳送至您的暖存放區，而不論事件時間戳記為何。 請注意，串流內嵌管線是針對近乎即時的串流處理所建立，且 [不支援](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion)擷取歷程記錄事件。
* 保留期限是根據事件在暖存放區中編制索引的時間來計算，而不是事件時間戳記。 這表示在保留期限過了之後，暖存放區中的資料將無法再使用，即使事件時間戳記適用于未來。
  * 範例：具有10天天氣預報的事件會在設定7天保留期間的暖儲存體容器中內嵌並編制索引。 在7天后，將無法再于暖存放區中存取預測，但可以從冷查詢進行查詢。
* 如果您在現有的環境中啟用暖存放區，而該環境已經在冷儲存體中建立最新的資料，請注意，您的暖存放區將不會再填滿此資料。
* 如果您剛剛啟用暖存放區，並在瀏覽器中看到您最近的資料時遇到問題，您可以暫時切換暖存放區查詢：

   [![停用暖查詢](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>冷存放區

本節說明與 Azure 時間序列深入解析 Gen2 相關的 Azure 儲存體詳細資料。

如需 Azure Blob 儲存體的完整說明，請參閱[儲存體 Blob 簡介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-cold-storage-account"></a>您的冷儲存體帳戶

Azure 時間序列深入解析 Gen2 會在您的 Azure 儲存體帳戶中最多保留每個事件的兩個複本。 一個複本儲存依擷取時間排序的事件，一律允許以時間排序的順序存取事件。 經過一段時間後，Azure 時間序列深入解析 Gen2 也會建立資料的重新分割複本，以針對效能查詢進行優化。

您的所有資料都會無限期地儲存在您的 Azure 儲存體帳戶中。

#### <a name="writing-and-editing-blobs"></a>寫入和編輯 blob

為確保查詢效能和資料可用性，請勿編輯或刪除 Azure 時間序列深入解析 Gen2 建立的任何 blob。

#### <a name="accessing-cold-store-data"></a>存取冷存放區資料

除了從 [Azure 時間序列深入解析 Explorer](./concepts-ux-panels.md) 和 [時間序列查詢 api](./concepts-query-overview.md)存取您的資料之外，您也可能想要直接從儲存在冷存放區中的 Parquet 檔案存取您的資料。 例如，您可以讀取、轉換和清除 Jupyter 筆記本中的資料，然後將其用來定型相同 Spark 工作流程中的 Azure Machine Learning 模型。

若要直接從您的 Azure 儲存體帳戶存取資料，您需要用來儲存 Azure 時間序列深入解析 Gen2 資料之帳戶的讀取存取權。 接著，您可以根據 Parquet 檔案的建立時間來讀取選取的資料，而此檔案位於底下 [Parquet 檔案格式](#parquet-file-format-and-folder-structure)一節所述的 `PT=Time` 資料夾中。  如需對儲存體帳戶啟用讀取存取權的詳細資訊，請參閱[管理對儲存體帳戶資源的存取](../storage/blobs/anonymous-read-access-configure.md)。

#### <a name="data-deletion"></a>刪除資料

請勿刪除您的 Azure 時間序列深入解析 Gen2 檔。 僅從 Azure 時間序列深入解析 Gen2 中管理相關資料。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 檔案格式和資料夾結構

Parquet 是開放原始碼的單欄式檔案格式，專為有效率的儲存和效能所設計。 Azure 時間序列深入解析 Gen2 會使用 Parquet，以大規模啟用以時間序列識別碼為基礎的查詢效能。  

如需有關 Parquet 檔案類型的詳細資訊，請參閱 [Parquet 文件](https://parquet.apache.org/documentation/latest/)。

Azure 時間序列深入解析 Gen2 會儲存資料的複本，如下所示：

* 第一個初始複本是透過擷取時間來分割，並大略地以抵達的順序儲存資料。 此資料位於 `PT=Time` 資料夾中：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 第二個重新分割的複本是依時間序列識別碼來分組，並位於 `PT=TsId` 資料夾中：

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

資料夾中 blob 名稱的時間戳記 `PT=Time` 會對應到資料的抵達時間 Azure 時間序列深入解析 Gen2，而不是事件的時間戳記。

`PT=TsId` 資料夾中的資料將會在一段時間後針對查詢最佳化，而且不是靜態資料。 在重新分割期間，某些事件可能會出現在多個 blob 中。 此資料夾中的 blob 命名不保證保持不變。

一般情況下，如果您需要直接透過 Parquet 檔存取資料，請使用 `PT=Time` 資料夾。  未來的功能將可讓您有效率地存取 `PT=TsId` 資料夾。

> [!NOTE]
>
> * `<YYYY>` 對應至四位數年份表示法。
> * `<MM>` 對應至兩位數月份表示法。
> * `<YYYYMMDDHHMMSSfff>` 對應至四位數年份 (`YYYY`)、兩位數月份 (`MM`)、兩位數日 (`DD`)、兩位數小時 (`HH`)、兩位數分鐘 (`MM`)、兩位數秒 (`SS`) 和三位數毫秒 (`fff`) 的時間戳記表示法。

Azure 時間序列深入解析 Gen2 事件會對應到 Parquet 檔案內容，如下所示：

* 每個事件都會對應至單一資料列。
* 每個資料列都包含具有事件時間戳記的 **時間戳記** 資料行。 時間戳記屬性絕不會是 Null。 如果未在事件來源中指定時間戳記屬性，它會預設為加入 **佇列的時間** 。 儲存的時間戳記一律採用 UTC 格式。
* 每個資料列都包含 (TSID) 資料行 (s 的時間序列識別碼，) 如建立 Azure 時間序列深入解析 Gen2 環境時所定義。 TSID 屬性名稱包含 `_string` 尾碼。
* 所有其他以遙測資料傳送的屬性都會對應到以 (布林值結尾的資料行名稱 `_bool`) 、 `_datetime` (時間戳記) 、 `_long` (long) 、 `_double` (double) 、 `_string` (string) 或 `dynamic` (dynamic) ，視屬性類型而定。  如需詳細資訊，請參閱 [支援的資料類型](./concepts-supported-data-types.md)。
* 這個對應結構描述會套用至檔案格式的第一個版本 (稱之為 **V=1**)，並儲存在相同名稱的基底資料夾中。 隨著這項功能的演進，此對應結構描述可能會有所變更，且參考名稱會遞增。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [資料模型](./concepts-model-overview.md)化。

* 規劃您的 [Azure 時間序列深入解析 Gen2 環境](./how-to-plan-your-environment.md)。
