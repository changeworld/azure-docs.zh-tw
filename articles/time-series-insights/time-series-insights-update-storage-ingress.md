---
title: 預覽中的資料儲存體和輸入 - Azure 時間序列深入解析 | Microsoft Docs
description: 了解 Azure 時間序列深入解析預覽中的資料儲存體和輸入。
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: ca5ba8d7b2d78440401e29344361538c3650ba48
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779171"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure 時間序列深入解析預覽中的資料儲存體和輸入

本文說明如何更新 Azure 時間序列深入解析預覽中的資料儲存體和輸入。 也說明基礎儲存體結構、檔案格式和時間序列識別碼屬性。 同時還會說明基礎輸入程序、最佳做法和目前的預覽限制。

## <a name="data-ingress"></a>資料輸入

您的 Azure 時間序列深入解析環境包含「擷取引擎」 來收集、處理和儲存時間序列資料。

有一些要留意的考量，是為了確保所有傳入的資料都經過處理，以達到高輸入規模，並在[規劃您的環境](time-series-insights-update-plan.md)時，將「擷取延遲」(時間序列深入解析從事件來源讀取及處理資料所需的時間) 降至最低。

時間序列深入解析預覽資料輸入原則會決定資料的來源，以及資料應該具有的格式。

### <a name="ingress-policies"></a>輸入原則

「資料輸入」涉及將資料傳送至 Azure 時間序列深入解析預覽環境的方式。

以下摘要說明金鑰組態、格式化和最佳做法。

#### <a name="event-sources"></a>事件來源

Azure 時間序列深入解析預覽支援下列事件來源：

- [Azure IoT 中心](../iot-hub/about-iot-hub.md)
- [Azure 事件中樞](../event-hubs/event-hubs-about.md)

Azure 時間序列深入解析預覽支援每個實例最多兩個事件來源。 當您連線事件來源時，TSI 環境會讀取目前儲存在 Iot 或事件中樞的所有事件，從最舊的事件開始。

> [!IMPORTANT]
>
> * 將事件來源附加至預覽環境時，您可能會遭遇高初始延遲的情況。
> 事件來源延遲取決於目前在 IoT 中樞或事件中樞內的事件數目。
> * 在第一次擷取事件來源資料之後，將會減少高延遲。 如果您遭遇持續高延遲的情況，請透過 Azure 入口網站提交支援票證。

#### <a name="supported-data-format-and-types"></a>支援的資料格式和類型

Azure 時間序列深入解析支援從 Azure IoT 中樞或 Azure 事件中樞傳送的 UTF-8 編碼 JSON。 

支援的資料類型如下：

| 資料類型 | 描述 |
|---|---|
| **bool** | 具有下列兩種狀態之一的資料類型：`true` 或 `false`。 |
| **dateTime** | 表示時間的瞬間，通常以一天的日期和時間表示。 以 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 格式表示。 |
| **double** | 雙精確度 64 位 [IEEE 754](https://ieeexplore.ieee.org/document/8766229) 浮點數。 |
| **string** | Unicode 字元所組成的文字值。          |

#### <a name="objects-and-arrays"></a>物件和陣列

您可以傳送複雜類型 (例如物件和陣列) 做為事件裝載的一部分，但是您的資料會在儲存執進行壓平合併程序。

如需說明如何塑造 JSON 事件、傳送複雜類型和巢狀套物件壓平合併的詳細資訊，請參閱[如何塑造 JSON 進行輸入和查詢](./time-series-insights-update-how-to-shape-events.md)，以協助規劃和最佳化。

### <a name="ingress-best-practices"></a>輸入最佳做法

建議您採用下列最佳做法：

* 在相同區域中設定 Azure 時間序列深入解析和任何 IoT 中樞或事件中樞，以降低潛在的延遲。

* 藉由計算預期的擷取速率，並驗證其是否落在下列支援的速率中，[為您的規模需求進行規劃](time-series-insights-update-plan.md)。

* 閱讀[如何塑造 JSON 進行輸入和查詢](./time-series-insights-update-how-to-shape-events.md)，以了解如何最佳化和塑造您的 JSON 資料，以及目前的預覽限制。

* 僅針對近乎即時和最近的資料使用串流擷取，不支援串流歷程記錄資料。

#### <a name="historical-data-ingestion"></a>歷程記錄資料擷取

Azure 時間序列深入解析預覽中目前不支援使用串流管道來匯入歷程記錄資料。 如果您需要將過去的資料匯入至您的環境，請遵循下列指導方針：

* 請不要以平行方式串流即時和歷程記錄資料。 擷取不按順序的資料會導致查詢效能降低。
* 依時間排序的方式擷取歷程記錄資料，以獲得最佳效能。
* 保持在下列的擷取輸送量速率限制內。
* 如果資料比您的溫存放區保留期間還舊，請停用溫存放區。

### <a name="ingress-scale-and-preview-limitations"></a>輸入規模和預覽限制

Azure 時間序列深入解析預覽的輸入限制如下所述。

> [!TIP]
> 如需所有預覽限制的完整清單，請參閱[規劃您的預覽環境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits)。

#### <a name="per-environment-limitations"></a>每個環境限制

一般來說，輸入速率會被視為您組織中的裝置數目、事件發出頻率，以及每個事件大小的因素：

*  **裝置數目** × **事件發出頻率** × **每個事件的大小**。

根據預設，時間序列深入解析預覽可以在每個時間序列深入解析環境中，以**每秒最多 1 MBps** 的速率來擷取傳入的資料。 [每個中樞分割區](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits)都有額外的限制。

> [!TIP]
>
> * 要求可以提供擷取速度最高可達 16 MBps 的環境支援。
> * 如果您需要更高的輸送量，請透過 Azure 入口網站提交支援票證來連絡我們。
 
* **範例 1：**

    Contoso Shipping 有 100,000 部裝置每分鐘發出事件三次。 事件的大小為 200 個位元組。 其會使用一個 IoT 中樞搭配四個分割區，做為時間序列深入解析事件來源。

    * 其時間序列深入解析環境的擷取速率如下：**100,000 部裝置 * 200 個位元組/事件 * (3/60 個事件/秒) = 1 MBps**。
    * 每個分割區的擷取速率將為 0.25 MBps。
    * Contoso Shipping 的擷取速率會在預覽規模限制範圍內。

* **範例 2：**

    Contoso Fleet Analytics 有 60,000 部裝置每秒發出一個事件。 其使用一個事件中樞搭配計數為 4 的分割區，做為時間序列深入解析事件來源。 事件的大小為 200 個位元組。

    * 環境擷取速率將為：**60,000 部裝置 * 200 個位元組/事件 * 1 個事件/秒 = 12 MBps**。
    * 每個分割區速率將為 3 MBps。
    * Contoso Fleet Analytics 的擷取速率超過環境和分割區限制。 其可以透過 Azure 入口網站提交時間序列深入解析的要求，以增加其環境的擷取速率，並建立具有更多分割區但不超過預覽限制的事件中樞。

#### <a name="hub-partitions-and-per-partition-limits"></a>中樞分割區和每個分割區限制

在規劃您的時間序列深入解析環境時，請務必考慮要連線到時間序列深入解析的事件來源組態。 Azure IoT 中樞和事件中樞都會利用分割區來啟用事件處理的水平調整。 

*「分割區」* 是經過排序且保存在中樞內的事件序列。 分割區計數是在中樞建立階段期間設定的，而且無法變更。

如需事件中樞分割最佳做法，請參閱[我需要多少個分割區？](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> 大部分與 Azure 時間序列深入解析搭配使用的 IoT 中樞只需要四個分割區。

無論您是為時間序列深入解析環境建立新的中樞，還是使用現有的中樞，都需要計算每個分割區的擷取速率，以判斷其是否在預覽限制內。 

Azure 時間序列深入解析預覽目前具有一般的**每個分割區限制為 0.5 MBps**。

#### <a name="iot-hub-specific-considerations"></a>IoT 中樞特定考量

在 IoT 中樞中建立裝置時，其會永久指派給分割區。 這樣做之後，IoT 中樞就能夠保證事件排序 (因為指派永遠不會變更)。

固定分割區指派也會影響時間序列深入解析執行個體，而這些執行個體會擷取從 IoT 中樞下游傳送的資料。 當使用相同的閘道裝置識別碼將來自多個裝置的訊息轉送到中樞時，其可能會同時抵達相同的分割區，因而可能會超過每個分割區的規模限制。

**影響**：

* 如果單一分割區遭遇擷取速率持續超過預覽限制，則在超過 IoT 中樞資料保留期間之前，時間序列深入解析可能不會同步所有裝置遙測。 因此，如果一致地超過擷取限制，傳送的資料可能會遺失。

為了減緩這種情況，建議您採用下列最佳做法：

* 在部署您的解決方案之前，請先計算每個環境和每個分割區的擷取速率。
* 確定您的 IoT 中樞裝置已負載平衡到最大的可能程度。

> [!IMPORTANT]
> 針對使用 IoT 中樞做為事件來源的環境，使用正在使用中的中樞裝置數目來計算擷取速率，以確保速率低於預覽中每個分割區的 0.5 MBps 限制。
>
> * 即使有數個事件同時抵達，也不會超過預覽限制。

  ![IoT 中樞分割區圖表](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

若要深入瞭解如何最佳化中樞輸送量和分割區，請參閱下列資源：

* [IoT 中樞規模](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [事件中樞規模](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [事件中樞分割區](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>資料儲存體

當您建立時間序列深入解析預覽「隨用隨付」(PAYG) SKU 環境時，您會建立兩個 Azure 資源：

* 一個可以針對暖資料儲存體進行設定的 Azure 時間序列深入解析預覽環境。
* 一個適用於冷資料儲存體的 Azure 儲存體一般用途 V1 Blob 帳戶。

暖存放區中的資料只能透過[時間序列查詢](./time-series-insights-update-tsq.md)和 [Azure 時間序列深入解析預覽總管](./time-series-insights-update-explorer.md)來使用。 您的暖存放區將包含[保留期間](./time-series-insights-update-plan.md#the-preview-environment)內的最近資料，而這個保留期間是在建立時間序列深入解析環境時所選取的。

時間序列深入解析預覽會以 [Parquet 檔案格式](#parquet-file-format-and-folder-structure)，將您的冷存放區資料儲存至 Azure Blob 儲存體。 時間序列深入解析預覽會以獨佔方式管理此冷存放區資料，但可供您直接讀取為標準 Parquet 檔案。

> [!WARNING]
> 身為冷存放區資料所在 Azure Blob 儲存體帳戶的擁有者，您可以完整存取帳戶中的所有資料。 此存取權包括寫入和刪除權限。 請不要編輯或刪除時間序列深入解析預覽寫入的資料，因為這可能會導致資料遺失。

### <a name="data-availability"></a>資料可用性

Azure 時間序列深入解析預覽會分割資料並為其編製索引，以獲得最佳查詢效能。 資料會在編制索引之後，皆可從暖存放區 (若啟用) 和冷存放區進行查詢。 正在擷取的資料量可能會影響此可用性。

> [!IMPORTANT]
> 在預覽期間，您可能會遭遇最多 60秒的期間，資料才會變成可用。 如果您遭遇超過 60 秒的嚴重延遲，請透過 Azure 入口網站提交支援票證。

## <a name="azure-storage"></a>Azure 儲存體

本節說明與 Azure 時間序列深入解析預覽相關的 Azure 儲存體詳細資料。

如需 Azure Blob 儲存體的完整說明，請參閱[儲存體 Blob 簡介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-storage-account"></a>您的儲存體帳戶

當您建立 Azure 時間序列深入解析預覽 PAYG 環境時，系統會將 Azure 儲存體一般用途 V1 Blob 帳戶建立為長期冷存放區。  

Azure 時間序列深入解析預覽會在您的 Azure 儲存體帳戶中，每個事件最多保留兩個複本。 一個複本儲存依擷取時間排序的事件，一律允許以時間排序的順序存取事件。 經過一段時間後，時間序列深入解析預覽也會建立資料的重新分割複本，以針對效能時間序列深入解析查詢進行最佳化。

在公開預覽期間，資料會無限期地儲存在您的 Azure 儲存體帳戶中。

#### <a name="writing-and-editing-time-series-insights-blobs"></a>寫入和編輯時間序列深入解析 Blob

為了確保查詢效能和資料可用性，請不要編輯或刪除時間序列深入解析預覽建立的任何 Blob。

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>存取時間序列深入解析預覽冷存放區資料

除了從[時間序列深入解析預覽總管](./time-series-insights-update-explorer.md)和[時間序列查詢](./time-series-insights-update-tsq.md)存取您的資料，您可能也想要直接從儲存在冷存放區中的 Parquet 檔案存取您的資料。 例如，您可以讀取、轉換和清除 Jupyter 筆記本中的資料，然後將其用來定型相同 Spark 工作流程中的 Azure Machine Learning 模型。

若要直接從您的 Azure 儲存體帳戶存取資料，您需要對用來儲存時間序列深入解析預覽資料的帳戶具有讀取存取權。 接著，您可以根據 Parquet 檔案的建立時間來讀取選取的資料，而此檔案位於底下 [Parquet 檔案格式](#parquet-file-format-and-folder-structure)一節所述的 `PT=Time` 資料夾中。  如需對儲存體帳戶啟用讀取存取權的詳細資訊，請參閱[管理對儲存體帳戶資源的存取](../storage/blobs/storage-manage-access-to-resources.md)。

#### <a name="data-deletion"></a>刪除資料

請不要刪除您的時間序列深入解析預覽檔案。 僅管理來自時間序列深入解析預覽內的相關資料。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 檔案格式和資料夾結構

Parquet 是開放原始碼的單欄式檔案格式，專為有效率的儲存和效能所設計。 時間序列深入解析預覽會使用 Parquet 來大規模啟用時間序列識別碼型查詢效能。  

如需有關 Parquet 檔案類型的詳細資訊，請參閱 [Parquet 文件](https://parquet.apache.org/documentation/latest/)。

時間序列深入解析預覽會儲存資料的複本，如下所示：

* 第一個初始複本是透過擷取時間來分割，並大略地以抵達的順序儲存資料。 此資料位於 `PT=Time` 資料夾中：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 第二個重新分割的複本是依時間序列識別碼來分組，並位於 `PT=TsId` 資料夾中：

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

在這兩種情況下，Parquet 檔案的時間屬性都會對應到 Blob 建立時間。 `PT=Time` 資料夾中的資料一旦寫入檔案後，就會保留且不會有任何變更。 `PT=TsId` 資料夾中的資料將會在一段時間後針對查詢最佳化，而且不是靜態資料。

> [!NOTE]
>
> * `<YYYY>` 對應至四位數年份表示法。
> * `<MM>` 對應至兩位數月份表示法。
> * `<YYYYMMDDHHMMSSfff>` 對應至四位數年份 (`YYYY`)、兩位數月份 (`MM`)、兩位數日 (`DD`)、兩位數小時 (`HH`)、兩位數分鐘 (`MM`)、兩位數秒 (`SS`) 和三位數毫秒 (`fff`) 的時間戳記表示法。

時間序列深入解析預覽事件會對應至 Parquet 檔案內容，如下所示：

* 每個事件都會對應至單一資料列。
* 每個資料列都包含具有事件時間戳記的**時間戳記**資料行。 時間戳記屬性絕不會是 Null。 如果事件來源中未指定時間戳記屬性，其會預設為**事件加入佇列的時間**。 儲存的時間戳記一律採用 UTC 格式。
* 每個資料列都包含建立時間序列深入解析環境時所定義的時間序列識別碼 (TSID) 資料行。 TSID 屬性名稱包含 `_string` 尾碼。
* 所有當作遙測資料傳送的其他屬性，都會對應至以 `_string` (字串)、`_bool` (布林值)、`_datetime` (日期時間) 或 `_double` (雙精度浮點數) 結尾的資料行名稱，取決於屬性類型。
* 這個對應結構描述會套用至檔案格式的第一個版本 (稱之為 **V=1**)，並儲存在相同名稱的基底資料夾中。 隨著這項功能的演進，此對應結構描述可能會有所變更，且參考名稱會遞增。

## <a name="next-steps"></a>後續步驟

- 閱讀[如何塑造 JSON 進行輸入和查詢](./time-series-insights-update-how-to-shape-events.md)。

- 閱讀新的[資料模型](./time-series-insights-update-tsm.md)。
