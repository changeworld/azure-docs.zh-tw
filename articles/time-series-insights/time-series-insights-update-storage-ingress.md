---
title: 預覽中的資料儲存體和輸入-Azure 時間序列深入解析 |Microsoft Docs
description: 深入瞭解 Azure 時間序列深入解析預覽中的資料儲存體和輸入。
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/31/2019
ms.custom: seodec18
ms.openlocfilehash: f00529d00312fd6acb045de698590047f991bec7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714307"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure 時間序列深入解析預覽中的資料儲存體和輸入

本文說明 Azure 時間序列深入解析 Preview 的資料儲存體和輸入的更新。 內容涵蓋基礎儲存體結構、檔案格式和時間序列識別碼屬性。 它也會討論基礎輸入程式、最佳作法，以及目前的預覽限制。

## <a name="data-ingress"></a>資料輸入

您的 Azure 時間序列深入解析環境包含可收集、處理和儲存時間序列資料的內嵌引擎。 在規劃您的環境時，有一些考慮需要考慮，以確保所有傳入的資料都已處理，並可達到高輸入規模並最小化內建延遲（TSI 用來讀取和處理事件資料的時間）來源）。 

在時間序列深入解析 Preview 中，資料輸入原則會決定資料的來源，以及資料應該有的格式。

### <a name="ingress-policies"></a>輸入原則

#### <a name="event-sources"></a>事件來源

時間序列深入解析 Preview 支援下列事件來源：

- [Azure IoT 中心](../iot-hub/about-iot-hub.md)
- [Azure 事件中樞](../event-hubs/event-hubs-about.md)

時間序列深入解析 Preview 針對每個實例最多支援兩個事件來源。

> [!WARNING] 
> * 將事件來源附加至預覽環境時，您可能會遇到高初始延遲的情況。 
> 事件來源延遲取決於目前在 IoT 中樞或事件中樞內的事件數目。
> * 事件來源資料第一次內嵌之後，將會減少高延遲。 如果您遇到持續高延遲的情況，請透過 Azure 入口網站提交支援票證來洽詢我們。

#### <a name="supported-data-format-and-types"></a>支援的資料格式和類型

Azure 時間序列深入解析支援透過 Azure IoT 中樞或 Azure 事件中樞提交的 UTF8 編碼 JSON。 

以下是支援的資料類型清單。

| 資料類型 | 描述 |
|-----------|------------------|-------------|
| bool      |   具有下列兩種狀態之一的資料類型： true 或 false。       |
| dateTime    |   表示時間的瞬間，通常以一天的日期和時程表示。 日期時間應採用 ISO 8601 格式。      |
| double    |   雙精確度64位 IEEE 754 浮點
| 字串    |   Unicode 字元所組成的文字值。          |

#### <a name="objects-and-arrays"></a>物件和陣列

您可以傳送複雜的類型（例如物件和陣列）做為事件裝載的一部分，但是您的資料會在儲存時經歷簡維處理常式。 如需如何塑造 JSON 事件的詳細資訊，以及複雜型別和嵌套物件簡維的詳細資訊，請參閱[如何為輸入和查詢塑造 json](./time-series-insights-update-how-to-shape-events.md)一頁。


### <a name="ingress-best-practices"></a>輸入最佳作法

我們建議您採用下列最佳作法：

* 在相同區域中設定時間序列深入解析和您的 IoT 中樞或事件中樞，以減少網路產生的內嵌延遲。
* 藉由計算預期的內傳速率，並確認其是否落在下列支援的費率中，為您的調整需求進行規劃
* 閱讀[如何針對輸入和查詢塑造 json](./time-series-insights-update-how-to-shape-events.md)，以瞭解如何優化和塑造您的 json 資料，以及目前的預覽限制。

### <a name="ingress-scale-and-limitations-in-preview"></a>預覽中的輸入規模和限制

#### <a name="per-environment-limitations"></a>每個環境的限制

一般來說，輸入速率會視為您組織中的裝置數目、事件排放頻率，以及每個事件的大小因素：

*  **裝置數**×**事件發射頻率**×**每個事件的大小**。

根據預設，時間序列深入解析 preview 可以在**每個 TSI 環境**中，以最多每秒 1 mb 的速率內嵌傳入的資料。 請洽詢我們如果這不符合您的需求，我們可以在 Azure 入口網站中提交支援票證，以支援最多 16 MBps 的環境。
 
範例1： Contoso 運送有100000個裝置，每分鐘發出事件三次。 事件的大小為200個位元組。 它們使用具有4個分割區的事件中樞作為 TSI 事件來源。
其 TSI 環境的內嵌速率為：100000裝置 * 200 位元組/事件 * （3/60 事件/秒） = 1 MBps。
每個分割區的內嵌速率為 0.25 MBps。
Contoso 運送的內送費率會在預覽規模限制範圍內。
 
範例2： Contoso 車隊分析有60000個裝置，每秒發出事件一次。 它們使用 IoT 中樞24分割區計數4作為 TSI 事件來源。 事件的大小為200個位元組。
環境的內嵌速度會是：20000裝置 * 200 位元組/事件 * 1 事件/秒 = 4 MBps。
每個分割區的速率會是 1 MBps。
Contoso 車隊分析必須透過適用于專用環境的 Azure 入口網站將要求提交至 TSI，以達成此規模。

#### <a name="hub-partitions-and-per-partition-limits"></a>中樞磁碟分割和每個分割區限制

在規劃 TSI 環境時，請務必考慮您將連接到 TSI 的事件來源設定。 Azure IoT 中樞和事件中樞都利用分割區來啟用事件處理的水準調整。  分割區是保留在中樞中的已排序事件順序。 在 IoT 或事件中樞的建立階段期間，會設定分割區計數，而且無法變更。 如需有關判斷分割區計數的詳細資訊，請參閱事件中樞的常見問題：我需要多少個磁碟分割？ 針對使用 IoT 中樞的 TSI 環境，通常大部分的 IoT 中樞只需要4個磁碟分割。 無論您是要為 TSI 環境建立新的中樞，還是使用現有的集線器，都需要計算每個分割區的內嵌速率，以判斷它是否在預覽限制內。 TSI preview 目前有**每個分割**區限制為 0.5 MB/秒。 使用下列範例做為參考，如果您是 IoT 中樞使用者，請注意下列 IoT 中樞特定的考慮。

#### <a name="iot-hub-specific-considerations"></a>IoT 中樞特定的考慮

在中建立裝置時 IoT 中樞它會指派給磁碟分割，而分割區指派則不會變更。 如此一來，IoT 中樞就能夠保證事件的排序。 不過，在某些情況下，這會影響 TSI 作為下游讀取器。 當使用相同的閘道裝置識別碼將來自多個裝置的訊息轉送到中樞時，它們會抵達相同的磁碟分割，因此可能會超過每個分割區的規模限制。 

**影響**：如果單一分割區的快補速率持續超過預覽限制，則在超過 IoT 中樞資料保留期間之前，TSI 讀取器可能不會趕上。 這會導致資料遺失。

我們的建議如下： 

* 在部署您的解決方案之前，計算每個環境和每個分割區的內嵌速率
* 確定您的 IoT 中樞裝置（也就是磁碟分割）已負載平衡，以達到最大的擴充可能性

> [!WARNING]
> 針對使用 IoT 中樞作為事件來源的環境，使用使用中的中樞裝置數目來計算內建速率，以確保速率低於預覽中每個分割區的 0.5 MBps 限制。

  ![IoT 中樞分割區圖表](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

如需輸送量單位和資料分割的詳細資訊，請參閱下列連結：

* [IoT 中樞規模](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [事件中樞規模](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [事件中樞磁碟分割](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>資料儲存體

當您建立時間序列深入解析預覽版的隨用隨付 SKU 環境時，您會建立兩個 Azure 資源：

* 時間序列深入解析的預覽環境，可以選擇性地包含暖存放區功能。
* 適用于冷資料儲存的 Azure 儲存體一般用途 V1 blob 帳戶。

您的暖存放區中的資料只能透過[時間序列查詢](./time-series-insights-update-tsq.md)和[Azure 時間序列深入解析預覽瀏覽器](./time-series-insights-update-explorer.md)來使用。 

時間序列深入解析 Preview 會以[Parquet 檔案格式](#parquet-file-format-and-folder-structure)將您的冷存放區資料儲存至 Azure Blob 儲存體。 時間序列深入解析 Preview 會以獨佔方式管理此冷存放區資料，但可供您直接讀取為標準 Parquet 檔案。

> [!WARNING]
> 身為 Azure Blob 儲存體帳戶的擁有者，如果有冷存放區資料所在，您就可以完整存取帳戶中的所有資料。 此存取權包括 [寫入] 和 [刪除] 許可權。 請勿編輯或刪除時間序列深入解析預覽寫入的資料，因為這可能會導致資料遺失。

### <a name="data-availability"></a>資料可用性

時間序列深入解析預覽分割和索引資料，以獲得最佳查詢效能。 資料會在索引後變成可供查詢。 正在內嵌的資料量可能會影響此可用性。

> [!IMPORTANT]
> 在預覽期間，您可能會遇到最多60秒的時間，資料才會變成可用。 如果您遇到超過60秒的嚴重延遲，請透過 Azure 入口網站提交支援票證。

## <a name="azure-storage"></a>Azure 儲存體

本節說明 Azure 時間序列深入解析 Preview 的相關 Azure 儲存體詳細資料。

如需 Azure Blob 儲存體的完整說明，請參閱[儲存體 blob 簡介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-storage-account"></a>您的儲存體帳戶

當您建立時間序列深入解析預覽版的隨用隨付環境時，系統會將 Azure 儲存體一般用途 V1 blob 帳戶建立為長期冷存放區。  

時間序列深入解析 Preview 會在您的 Azure 儲存體帳戶中最多發佈兩個事件的複本。 初始複本具有依內嵌時間排序的事件，而且一律會保留，因此您可以使用其他服務來存取它。 您可以使用 Spark、Hadoop 和其他熟悉的工具來處理原始的 Parquet 檔案。 

時間序列深入解析 Preview 會重新分割 Parquet 檔案，以針對時間序列深入解析查詢進行優化。 此資料的重新分割複本也會一併儲存。

在公開預覽期間，資料會無限期地儲存在您的 Azure 儲存體帳戶中。

#### <a name="writing-and-editing-time-series-insights-blobs"></a>寫入和編輯時間序列深入解析 Blob

若要確保查詢效能和資料可用性，請勿編輯或刪除時間序列深入解析 Preview 所建立的任何 blob。

#### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>從時間序列深入解析預覽存取和匯出資料

您可能想要存取在時間序列深入解析預覽瀏覽器中查看的資料，以便與其他服務搭配使用。 例如，您可以使用您的資料在 Power BI 中建立報表，或使用 Azure Machine Learning Studio 將機器學習模型定型。 或者，您可以使用您的資料在 Jupyter 筆記本中轉換、視覺化和模型化。

您可以透過三種一般方式存取資料：

* 透過時間序列深入解析預覽總管。 您可以從 [explorer] 將資料匯出成 CSV 檔案。 如需詳細資訊，請參閱[時間序列深入解析 Preview explorer](./time-series-insights-update-explorer.md)。
* 使用取得事件查詢從時間序列深入解析預覽 API。 若要深入瞭解此 API，請參閱[時間序列查詢](./time-series-insights-update-tsq.md)。
* 直接從 Azure 儲存體帳戶。 您需要您用來存取時間序列深入解析預覽資料的任何帳戶的讀取存取權。 如需詳細資訊，請參閱[管理儲存體帳戶資源的存取權](../storage/blobs/storage-manage-access-to-resources.md)。

#### <a name="data-deletion"></a>刪除資料

請勿刪除您的時間序列深入解析預覽檔案。 僅在時間序列深入解析 Preview 內管理相關資料。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 檔案格式和資料夾結構

Parquet 是開放原始碼的單欄式檔案格式，專為有效率的儲存和效能所設計。 基於這些理由，時間序列深入解析 Preview 會使用 Parquet。 它會依時間序列識別碼分割資料，以獲得大規模的查詢效能。  

如需 Parquet 檔案類型的詳細資訊，請參閱[Parquet](https://parquet.apache.org/documentation/latest/)檔。

時間序列深入解析預覽會儲存資料的複本，如下所示：

* 第一個，初始複本是透過內嵌時間來分割，並大致地以抵達的順序儲存資料。 資料位於 `PT=Time` 資料夾中：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 第二個是以時間序列識別碼群組分割複本，並位於 `PT=TsId` 資料夾中：

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

在這兩種情況下，時間值都會對應到 blob 建立時間。 [`PT=Time`] 資料夾中的資料會保留下來。 [`PT=TsId`] 資料夾中的資料將會針對一段時間的查詢優化，而且不會保持靜態。

> [!NOTE]
> * `<YYYY>` 對應到四位數年份標記法。
> * `<MM>` 對應到兩位數的月份標記法。
> * `<YYYYMMDDHHMMSSfff>` 對應到具有四位數年份（`YYYY`）、兩位數月份（`MM`）、兩位數的日（`DD`）、兩位數的小時（`HH`）、兩位數的分鐘（`MM`）和三位數毫秒（`SS`）的時間戳記標記法。`fff`

時間序列深入解析預覽事件會對應至 Parquet 檔案內容，如下所示：

* 每個事件都會對應至單一資料列。
* 每個資料列都包含**時間**戳資料行與事件時間戳記。 時間戳記屬性絕不會是 null。 如果事件來源中未指定時間戳記屬性，它會預設為**事件排入佇列的時間**。 時間戳記一律為 UTC 格式。
* 每個資料列都包含建立時間序列深入解析環境時所定義的時間序列識別碼資料行。 屬性名稱包含 `_string` 尾碼。
* 以遙測資料傳送的所有其他屬性都會對應到以 `_string` （string）、`_bool` （布林）、`_datetime` （datetime）或 `_double` （double）為結尾的資料行名稱，視屬性類型而定。
* 這個對應配置適用于檔案格式的第一版，其參考為**V = 1**。 隨著這項功能的演進，名稱可能會遞增。

## <a name="next-steps"></a>後續步驟

- 閱讀[如何塑造 JSON 以進行輸入和查詢](./time-series-insights-update-how-to-shape-events.md)。

- 深入瞭解新的[資料模型](./time-series-insights-update-tsm.md)。
