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
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 2f12cf303c58f0fa614c59ffe643c6c2ee5d2415
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246193"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure 時間序列深入解析預覽中的資料儲存體和輸入

本文說明 Azure 時間序列深入解析 Preview 的資料儲存體和輸入的更新。 它會描述基礎儲存結構、檔案格式和時間序列識別碼屬性。 同時也會說明基礎輸入程式、最佳作法和目前的預覽限制。

## <a name="data-ingress"></a>資料輸入

您的 Azure 時間序列深入解析環境包含可收集、處理和儲存時間序列資料的內嵌*引擎*。 

在[規劃您的環境](time-series-insights-update-plan.md)時，必須留意一些考慮，以確保所有傳入的資料都已處理、達到高輸入規模，並將內嵌*延遲*降到最低（時間序列深入解析從事件來源讀取及處理資料所花費的時間）。

時間序列深入解析預覽資料輸入原則會決定資料的來源，以及資料應該有的格式。

### <a name="ingress-policies"></a>輸入原則

*資料*輸入牽涉到如何將資料傳送至 Azure 時間序列深入解析預覽環境。 

以下摘要說明金鑰設定、格式和最佳作法。

#### <a name="event-sources"></a>事件來源

Azure 時間序列深入解析 Preview 支援下列事件來源：

- [Azure IoT 中心](../iot-hub/about-iot-hub.md)
- [Azure 事件中樞](../event-hubs/event-hubs-about.md)

Azure 時間序列深入解析 Preview 針對每個實例最多支援兩個事件來源。

> [!IMPORTANT] 
> * 將事件來源附加至預覽環境時，您可能會遇到高初始延遲的情況。 
> 事件來源延遲取決於目前在 IoT 中樞或事件中樞內的事件數目。
> * 事件來源資料第一次內嵌之後，將會減少高延遲。 如果您遇到持續高延遲的情況，請透過 Azure 入口網站提交支援票證。

#### <a name="supported-data-format-and-types"></a>支援的資料格式和類型

Azure 時間序列深入解析支援從 Azure IoT 中樞或 Azure 事件中樞傳送的 UTF-8 編碼 JSON。 

支援的資料類型如下：

| 資料類型 | 描述 |
|---|---|
| **bool** | 具有下列其中一種狀態的資料類型： `true` 或 `false`。 |
| **dateTime** | 表示時間的瞬間，通常以一天的日期和時程表示。 以[ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html)格式表示。 |
| **double** | 雙精確度64位[IEEE 754](https://ieeexplore.ieee.org/document/8766229)浮點。 |
| **string** | Unicode 字元所組成的文字值。          |

#### <a name="objects-and-arrays"></a>物件和陣列

您可以傳送複雜的類型（例如物件和陣列）做為事件裝載的一部分，但是您的資料會在儲存時經歷簡維處理常式。 

說明如何塑造 JSON 事件、傳送複雜型別和嵌套物件簡維的詳細資訊，可在[如何塑造 json 以進行輸入和查詢](./time-series-insights-update-how-to-shape-events.md)，以協助規劃與優化。

### <a name="ingress-best-practices"></a>輸入最佳作法

我們建議您採用下列最佳作法：

* 在相同區域中設定 Azure 時間序列深入解析和任何 IoT 中樞或事件中樞，以降低潛在的延遲。

* 藉由計算預期的內傳速率，並確認其是否落在下列支援的費率中，[為您的調整需求進行規劃](time-series-insights-update-plan.md)。

* 閱讀[如何針對輸入和查詢塑造 json](./time-series-insights-update-how-to-shape-events.md)，以瞭解如何優化和塑造您的 json 資料，以及目前的預覽限制。

### <a name="ingress-scale-and-preview-limitations"></a>輸入規模和預覽限制 

Azure 時間序列深入解析的預覽輸入限制如下所述。

> [!TIP]
> 如需所有預覽限制的完整清單，請參閱[規劃您的預覽環境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits)。

#### <a name="per-environment-limitations"></a>每個環境的限制

一般來說，輸入速率會視為您組織中的裝置數目、事件排放頻率，以及每個事件的大小因素：

*  **裝置數**×**事件發射頻率**×**每個事件的大小**。

根據預設，時間序列深入解析 preview 可以在每個時間序列深入解析環境中，以**最多每秒 1 mb （MBps）** 的速率內嵌傳入的資料。

> [!TIP] 
> * 要求可以提供內嵌速度最多 16 MBps 的環境支援。
> * 如果您透過 Azure 入口網站提交支援票證來要求更高的輸送量，請洽詢我們。
 
* **範例 1：**

    Contoso 運送有100000個裝置每分鐘發出事件三次。 事件的大小為200個位元組。 它們使用具有四個分割區的事件中樞做為時間序列深入解析事件來源。

    * 其時間序列深入解析環境的內嵌速率為： **100000 裝置 * 200 位元組/事件 * （3/60 事件/秒） = 1 MBps**。
    * 每個分割區的內嵌速率為 0.25 MBps。
    * Contoso 運送的內送費率會在預覽規模限制範圍內。

* **範例2：**

    Contoso 車隊分析有60000個會每秒發出事件的裝置。 它們使用 IoT 中樞24分割區計數4作為時間序列深入解析事件來源。 事件的大小為200個位元組。

    * 環境的內嵌速度會是： **20000 裝置 * 200 位元組/事件 * 1 事件/秒 = 4 MBps**。
    * 每個分割區的速率會是 1 MBps。
    * Contoso 車隊分析可以透過 Azure 入口網站提交要求時間序列深入解析，以提高其環境的內嵌速度。

#### <a name="hub-partitions-and-per-partition-limits"></a>中樞磁碟分割和每個分割區限制

在規劃您的時間序列深入解析環境時，請務必考慮要連接到時間序列深入解析的事件來源設定。 Azure IoT 中樞和事件中樞都利用分割區來啟用事件處理的水準調整。 

「*分割*區」（partition）是保留在中樞中的事件順序。 分割區計數是在中樞建立階段設定的，而且無法變更。 

如需事件中樞資料分割最佳做法，請參閱[我需要多少個磁碟分割？](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> 大部分與 Azure 時間序列深入解析搭配使用的 IoT 中樞只需要四個磁碟分割。

無論您是為時間序列深入解析環境建立新的中樞，還是要使用現有的集線器，都需要計算每個分割區的內嵌速率，以判斷它是否在預覽限制內。 

Azure 時間序列深入解析 Preview 目前有**每個分割區限制為 0.5 MBps 的**一般。

#### <a name="iot-hub-specific-considerations"></a>IoT 中樞特定的考慮

在 IoT 中樞中建立裝置時，它會永久指派給磁碟分割。 在這種情況下，IoT 中樞能夠保證事件排序（因為指派永遠不會變更）。

固定的分割區指派也會影響內嵌從下游 IoT 中樞傳送之資料的時間序列深入解析實例。 當使用相同的閘道裝置識別碼將來自多個裝置的訊息轉送到中樞時，它們可能會同時抵達相同的分割區，可能會超過每個分割區的規模限制。 

**影響**：

* 如果單一分割區的處理速度持續超過預覽限制，則在超過 IoT 中樞資料保留期間之前，時間序列深入解析可能不會同步所有裝置遙測。 因此，如果一致地超過內嵌限制，傳送的資料可能會遺失。

為了減輕這種情況，我們建議您採用下列最佳作法：

* 在部署您的解決方案之前，請先計算每個環境和每個分割區的內嵌速率。
* 請確定您的 IoT 中樞裝置已負載平衡到最大的可能程度。

> [!IMPORTANT]
> 針對使用 IoT 中樞作為事件來源的環境，使用使用中的中樞裝置數目來計算內建速率，以確保速率低於預覽中每個分割區的 0.5 MBps 限制。
> * 即使有數個事件同時抵達，也不會超過預覽限制。

  ![IoT 中樞分割區圖表](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

若要深入瞭解如何優化中樞輸送量和資料分割，請參閱下列資源：

* [IoT 中樞規模](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [事件中樞規模](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [事件中樞磁碟分割](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>資料儲存

當您建立時間序列深入解析預覽的*隨用隨付*（PAYG） SKU 環境時，您會建立兩個 Azure 資源：

* Azure 時間序列深入解析的預覽環境，可針對暖資料儲存進行設定。
* 適用于冷資料儲存的 Azure 儲存體一般用途 V1 blob 帳戶。

您的暖存放區中的資料只能透過[時間序列查詢](./time-series-insights-update-tsq.md)和[Azure 時間序列深入解析預覽瀏覽器](./time-series-insights-update-explorer.md)來使用。 您的暖存放區將會包含建立時間序列深入解析環境時，所選[保留期間](./time-series-insights-update-plan.md#the-preview-environment)內的最新資料。

時間序列深入解析 Preview 會以[Parquet 檔案格式](#parquet-file-format-and-folder-structure)將您的冷存放區資料儲存至 Azure Blob 儲存體。 時間序列深入解析 Preview 會以獨佔方式管理此冷存放區資料，但可供您直接讀取為標準 Parquet 檔案。

> [!WARNING]
> 身為 Azure Blob 儲存體帳戶的擁有者，如果有冷存放區資料所在，您就可以完整存取帳戶中的所有資料。 此存取權包括 [寫入] 和 [刪除] 許可權。 請勿編輯或刪除時間序列深入解析預覽寫入的資料，因為這可能會導致資料遺失。

### <a name="data-availability"></a>資料可用性

Azure 時間序列深入解析預覽分割和索引資料，以獲得最佳查詢效能。 資料會從暖（啟用時）和冷存放區編制索引之後，都可供查詢。 正在內嵌的資料量可能會影響此可用性。

> [!IMPORTANT]
> 在預覽期間，您可能會遇到最多60秒的時間，資料才會變成可用。 如果您遇到超過60秒的嚴重延遲，請透過 Azure 入口網站提交支援票證。

## <a name="azure-storage"></a>Azure 儲存體

本節說明 Azure 時間序列深入解析 Preview 的相關 Azure 儲存體詳細資料。

如需 Azure Blob 儲存體的完整說明，請參閱[儲存體 blob 簡介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-storage-account"></a>您的儲存體帳戶

當您建立 Azure 時間序列深入解析 Preview PAYG 環境時，系統會將 Azure 儲存體一般用途 V1 blob 帳戶建立為長期冷存放區。  

Azure 時間序列深入解析 Preview 會在您的 Azure 儲存體帳戶中最多保留兩個事件的複本。 一個複本會儲存依內嵌時間排序的事件，並一律允許以時間排序的順序存取事件。 經過一段時間後，時間序列深入解析 Preview 也會建立資料的重新分割複本，以針對高效能時間序列深入解析查詢進行優化。 

在公開預覽期間，資料會無限期地儲存在您的 Azure 儲存體帳戶中。

#### <a name="writing-and-editing-time-series-insights-blobs"></a>寫入和編輯時間序列深入解析 Blob

若要確保查詢效能和資料可用性，請勿編輯或刪除時間序列深入解析 Preview 所建立的任何 blob。

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>存取時間序列深入解析預覽冷存放區資料 

除了從[時間序列深入解析 Preview explorer](./time-series-insights-update-explorer.md)和[時間序列查詢](./time-series-insights-update-tsq.md)存取您的資料，您可能也會想要直接從儲存在冷存放區中的 Parquet 檔案存取您的資料。 例如，您可以讀取、轉換和清理 Jupyter 筆記本中的資料，然後使用它來定型相同 Spark 工作流程中的 Azure Machine Learning 模型。

若要直接從您的 Azure 儲存體帳戶存取資料，您需要用來儲存時間序列深入解析預覽資料之帳戶的 [讀取] 存取權。 接著，您可以根據[Parquet 檔案格式](#parquet-file-format-and-folder-structure)一節中所述的 [`PT=Time`] 資料夾中的 Parquet 檔案建立時間，讀取選取的資料。  如需啟用儲存體帳戶之讀取權限的詳細資訊，請參閱[管理儲存體帳戶資源的存取權](../storage/blobs/storage-manage-access-to-resources.md)。

#### <a name="data-deletion"></a>刪除資料

請勿刪除您的時間序列深入解析預覽檔案。 僅在時間序列深入解析 Preview 內管理相關資料。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 檔案格式和資料夾結構

Parquet 是開放原始碼的單欄式檔案格式，專為有效率的儲存和效能所設計。 時間序列深入解析 Preview 會使用 Parquet 來大規模啟用以時間序列識別碼為基礎的查詢效能。  

如需 Parquet 檔案類型的詳細資訊，請參閱[Parquet](https://parquet.apache.org/documentation/latest/)檔。

時間序列深入解析預覽會儲存資料的複本，如下所示：

* 第一個，初始複本是透過內嵌時間來分割，並大致地以抵達的順序儲存資料。 此資料位於 `PT=Time` 資料夾中：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 第二個是以時間序列識別碼來分組重新分割複本，並位於 `PT=TsId` 資料夾中：

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

在這兩種情況下，Parquet 檔案的 time 屬性都會對應到 blob 建立時間。 在 [`PT=Time`] 資料夾中的資料在寫入檔案後不會有任何變更。 [`PT=TsId`] 資料夾中的資料將會針對一段時間的查詢優化，而且不是靜態的。

> [!NOTE]
> * `<YYYY>` 對應到四位數年份標記法。
> * `<MM>` 對應到兩位數的月份標記法。
> * `<YYYYMMDDHHMMSSfff>` 對應到具有四位數年份（`YYYY`）、兩位數月份（`MM`）、兩位數的日（`DD`）、兩位數的小時（`HH`）、兩位數的分鐘（`MM`）和三位數毫秒（`SS`）的時間戳記標記法。`fff`

時間序列深入解析預覽事件會對應至 Parquet 檔案內容，如下所示：

* 每個事件都會對應至單一資料列。
* 每個資料列都包含**時間**戳資料行與事件時間戳記。 時間戳記屬性絕不會是 null。 如果事件來源中未指定時間戳記屬性，它會預設為已加入**佇列的事件時間**。 儲存的時間戳記一律是 UTC 格式。
* 每個資料列都包含建立時間序列深入解析環境時所定義的時間序列識別碼（TSID）資料行。 TSID 屬性名稱包含 `_string` 尾碼。
* 以遙測資料傳送的所有其他屬性都會對應到以 `_string` （string）、`_bool` （布林）、`_datetime` （datetime）或 `_double` （double）為結尾的資料行名稱，視屬性類型而定。
* 這個對應架構會套用至檔案格式的第一個版本，其參考為**V = 1** ，並儲存在相同名稱的基底資料夾中。 隨著這項功能的演進，此對應架構可能會變更，且參考名稱會遞增。

## <a name="next-steps"></a>後續步驟

- 閱讀[如何塑造 JSON 以進行輸入和查詢](./time-series-insights-update-how-to-shape-events.md)。

- 深入瞭解新的[資料模型](./time-series-insights-update-tsm.md)。
