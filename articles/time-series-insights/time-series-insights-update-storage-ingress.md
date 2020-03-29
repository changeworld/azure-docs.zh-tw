---
title: 預覽中的資料存儲和入口 - Azure 時間序列見解 |微軟文檔
description: 在 Azure 時間序列見解預覽中瞭解資料存儲和入口。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246193"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure 時間序列深入解析預覽中的資料儲存體和輸入

本文介紹了 Azure 時間序列見解預覽的資料存儲和入口的更新。 它描述了基礎存儲結構、檔案格式和時間序列 ID 屬性。 還介紹了潛在的入口過程、最佳做法和當前預覽限制。

## <a name="data-ingress"></a>資料入口

Azure 時間序列見解環境包含用於收集、處理和存儲時間序列資料的*引入引擎*。 

[在規劃環境](time-series-insights-update-plan.md)時，需要注意一些注意事項，以確保處理所有傳入資料，實現高入口擴展，並最大限度地減少*引入延遲*（時間序列見解從事件源讀取和處理資料所花時間）。

時間序列見解 預覽資料入口策略確定資料的來源以及資料應具有的格式。

### <a name="ingress-policies"></a>入口策略

*資料入口*涉及如何將資料發送到 Azure 時間序列見解預覽環境。 

關鍵配置、格式和最佳實踐概述如下。

#### <a name="event-sources"></a>事件來源

Azure 時間序列見解預覽支援以下事件源：

- [Azure IoT 中心](../iot-hub/about-iot-hub.md)
- [Azure 事件中心](../event-hubs/event-hubs-about.md)

Azure 時間序列見解預覽版每個實例最多支援兩個事件源。

> [!IMPORTANT] 
> * 將事件源附加到預覽環境時，可能會遇到較高的初始延遲。 
> 事件源延遲取決於 IoT 中心或事件中心當前的事件數。
> * 首次引入事件來源資料後，高延遲將降低。 如果遇到持續的高延遲，則通過 Azure 門戶提交支援票證。

#### <a name="supported-data-format-and-types"></a>支援的資料格式和類型

Azure 時間序列見解支援從 Azure IoT 中心或 Azure 事件中心發送的 UTF-8 編碼 JSON。 

支援的資料類型包括：

| 資料類型 | 描述 |
|---|---|
| **bool** | 具有兩種狀態之一的資料類型：`true`或`false`。 |
| **Datetime** | 表示時間的瞬間，通常以一天的日期和時間表示。 以[ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html)格式表示。 |
| **雙** | 雙精度 64 位[IEEE 754](https://ieeexplore.ieee.org/document/8766229)浮點。 |
| **字串** | Unicode 字元所組成的文字值。          |

#### <a name="objects-and-arrays"></a>物件和陣列

您可以將物件和陣列等複雜類型作為事件裝載的一部分發送，但資料存儲時資料將經歷拼合過程。 

有關如何塑造 JSON 事件、發送複雜類型和嵌套物件拼合的詳細資訊，請參閱[如何為入口和查詢塑造 JSON](./time-series-insights-update-how-to-shape-events.md)以説明規劃和優化。

### <a name="ingress-best-practices"></a>入口最佳實踐

我們建議您採用以下最佳實踐：

* 配置 Azure 時間序列見解以及同一區域中的任何 IoT 中心或事件中心，以減少潛在延遲。

* 通過計算預期攝入率並驗證其是否屬於下面列出的支援率，[規劃您的規模需求](time-series-insights-update-plan.md)。

* 瞭解如何通過閱讀[如何為入口和查詢塑造 JSON](./time-series-insights-update-how-to-shape-events.md)資料，以及預覽中的當前限制，瞭解如何優化和塑造 JSON 資料。

### <a name="ingress-scale-and-preview-limitations"></a>入口比例和預覽限制 

Azure 時間序列見解 預覽入口限制如下所述。

> [!TIP]
> 閱讀["預覽環境規劃"，](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits)瞭解所有預覽限制的完整清單。

#### <a name="per-environment-limitations"></a>每個環境限制

通常，入口速率被視為組織中的設備數量、事件發射頻率和每個事件大小的因素：

*  **設備數量**=**事件發射頻率**=**每個事件的大小**。

預設情況下，時序見解預覽可以**以高達每秒 1 百萬位元組 （MBps） 的速度接收傳入資料，每個時間序列見解環境**。

> [!TIP] 
> * 可按要求提供高達 16 MBps 的攝取速度的環境支援。
> * 如果您需要通過 Azure 門戶提交支援票證，請與我們聯繫。
 
* **範例 1：**

    Contoso 運輸有 100，000 台設備，每分鐘發出三次事件。 事件的大小為 200 位元組。 他們使用具有四個分區的事件中心作為時間序列見解事件源。

    * 其時序見解環境的引入速率為 **：100，000 台設備 = 200 位元組/事件 * （3/60 事件/秒） = 1 MBps**。
    * 每個分區的引入速率將為 0.25 MBps。
    * Contoso 航運的吞食率將在預覽量級限制範圍內。

* **示例 2：**

    Contoso 車隊分析有 60，000 台設備每秒發出一個事件。 他們使用 IoT 中心 24 分區計數 4 作為時間序列見解事件源。 事件的大小為 200 位元組。

    * 環境引入率是 **：20，000台設備 = 200 位元組/事件 = 1 事件/秒 = 4 MBps。**
    * 每個分區速率為 1 MBps。
    * Contoso 佇列分析可以通過 Azure 門戶向時序見解提交請求，以提高其環境的引入率。

#### <a name="hub-partitions-and-per-partition-limits"></a>中心分區和每個分區限制

在規劃時序見解環境時，請務必考慮要連接到時序見解的事件源的配置。 Azure IoT 中心中心和事件中心都利用分區為事件處理啟用水準縮放。 

*分區*是集線器中持有的事件的有序序列。 分區計數在集線器創建階段設置，無法更改。 

對於事件中心分區最佳實踐，請查看[我需要多少個分區？](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> 與 Azure 時間序列見解一起使用的大多數 IoT 中心只需要四個分區。

無論您是為時序見解環境創建新中心，還是使用現有中心，都需要計算每個分區引入速率，以確定其是否在預覽限制範圍內。 

Azure 時間序列見解預覽版當前**具有每個分區的一般限制為 0.5 MBps。**

#### <a name="iot-hub-specific-considerations"></a>IoT 中心特定的注意事項

在 IoT 中心中創建設備時，它將永久分配給分區。 這樣，IoT 中心能夠保證事件排序（因為分配永遠不會更改）。

固定分區分配還會影響正在引入從 IoT 中心下游發送的資料的時間序列見解實例。 當來自多個設備的消息使用相同的閘道裝置識別碼 轉發到集線器時，它們可能同時到達同一分區，可能超出每個分區規模限制。 

**影響**：

* 如果單個分區在預覽限制上遇到持續吞取率，則在超過 IoT 中心資料保留期之前，時間序列見解可能不會同步所有設備遙測。 因此，如果持續超過攝入限制，則發送的資料可能會丟失。

為了緩解這種情況，我們建議採用以下最佳做法：

* 在部署解決方案之前，計算每個環境和每個分區的引入速率。
* 確保您的 IoT 中心設備盡可能在盡可能遠的範圍內實現負載平衡。

> [!IMPORTANT]
> 對於使用 IoT 中心作為事件源的環境，請使用使用的集線器設備數量計算引入速率，以確保速率低於預覽版中每個分區限制 0.5 MBps。
> * 即使多個事件同時到達，也不會超過預覽限制。

  ![IoT 中心區域圖](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

請參閱以下資源，瞭解有關優化中心輸送量和分區的詳細資訊：

* [IoT 中心規模](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [事件中心規模](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [事件中心分區](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>資料儲存體

創建時序見解預覽即*用即付*（PAYG） SKU 環境時，將創建兩個 Azure 資源：

* 可配置為暖資料存儲的 Azure 時間序列見解預覽環境。
* Azure 存儲通用 V1 blob 帳戶用於冷資料存儲。

暖存儲中的資料只能通過[時間序列查詢](./time-series-insights-update-tsq.md)和[Azure 時間序列預覽資源管理器](./time-series-insights-update-explorer.md)提供。 暖存儲將在創建時序見解環境時選擇的[保留期內](./time-series-insights-update-plan.md#the-preview-environment)包含最近的資料。

時間序列見解預覽將冷存儲資料以[Parquet 檔案格式](#parquet-file-format-and-folder-structure)保存到 Azure Blob 存儲。 時間序列見解預覽專門管理此冷存儲資料，但可供您直接讀取為標準 Parquet 檔。

> [!WARNING]
> 作為冷存儲資料所在的 Azure Blob 存儲帳戶的擁有者，您可以完全存取帳戶中的所有資料。 此存取權限包括寫入和刪除許可權。 不要編輯或刪除時間序列見解預覽版寫入的資料，因為這可能導致資料丟失。

### <a name="data-availability"></a>資料可用性

Azure 時間序列見解 預覽分區和索引資料，以實現最佳的查詢性能。 資料在編制索引後可從暖存儲（如果啟用）和冷存儲進行查詢。 正在引入的資料量可能會影響此可用性。

> [!IMPORTANT]
> 在預覽期間，在資料可用之前，您可能會遇到長達 60 秒的時間。 如果遇到超過 60 秒的重大延遲，請通過 Azure 門戶提交支援票證。

## <a name="azure-storage"></a>Azure 儲存體

本節介紹與 Azure 時間序列見解預覽相關的 Azure 存儲詳細資訊。

有關 Azure Blob 存儲的詳盡說明，請閱讀[存儲 Blob 簡介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-storage-account"></a>您的存儲帳戶

創建 Azure 時間序列見解預覽 PAYG 環境時，將創建 Azure 存儲通用 V1 Blob 帳戶作為長期冷庫創建。  

Azure 時間序列預覽版最多保留 Azure 存儲帳戶中每個事件的兩個副本。 一個副本存儲按引入時間排序的事件，始終允許按時間循序存取事件。 隨著時間的推移，時序見解預覽還會創建資料的重新分區副本，以優化執行時序見解查詢。 

在公共預覽期間，資料將無限期地存儲在 Azure 存儲帳戶中。

#### <a name="writing-and-editing-time-series-insights-blobs"></a>寫入和編輯時間序列深入解析 Blob

為確保查詢性能和資料可用性，請不要編輯或刪除時間序列見解預覽創建的任何 Blob。

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>存取時間序列見解 預覽冷存儲資料 

除了從[時序見解預覽資源管理器](./time-series-insights-update-explorer.md)和[時間序列查詢](./time-series-insights-update-tsq.md)訪問您的資料外，您可能還希望直接從存儲在冷存儲中的 Parquet 檔中訪問您的資料。 例如，您可以在 Jupyter 筆記本中讀取、轉換和清理資料，然後使用它在同一 Spark 工作流中訓練 Azure 機器學習模型。

要直接從 Azure 存儲帳戶訪問資料，您需要讀取對用於存儲時間序列見解預覽資料的帳戶的讀取存取許可權。 然後，您可以根據位於`PT=Time`[Parquet 檔案格式](#parquet-file-format-and-folder-structure)部分中所述資料夾中的 Parquet 檔的創建時間讀取所選資料。  有關啟用對存儲帳戶的讀取存取的詳細資訊，請參閱[管理對存儲帳戶資源的存取權限](../storage/blobs/storage-manage-access-to-resources.md)。

#### <a name="data-deletion"></a>刪除資料

不要刪除時間序列見解預覽檔。 僅在時間序列見解預覽中管理相關資料。

### <a name="parquet-file-format-and-folder-structure"></a>鑲木地板檔案格式和資料夾結構

Parquet 是一種開源列式檔案格式，旨在實現高效的存儲和性能。 時序見解預覽版使用 Parquet 大規模啟用基於時間序列 ID 的查詢性能。  

有關 Parquet 檔案類型的詳細資訊，請閱讀[Parquet 文檔](https://parquet.apache.org/documentation/latest/)。

時間序列見解 預覽存儲資料副本，如下所示：

* 第一個初始副本通過引入時間進行分區，並大致按到達順序存儲資料。 此資料駐留在`PT=Time`資料夾中：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 第二個重新分區的副本按時間序列 ID 分組，並駐留在`PT=TsId`資料夾中：

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

在這兩種情況下，Parquet 檔的時間屬性對應于 blob 創建時間。 `PT=Time`資料夾中的資料在寫入檔後不會更改。 `PT=TsId`資料夾中的資料將針對隨時間的查詢進行優化，並且不是靜態的。

> [!NOTE]
> * `<YYYY>`映射到四位數的年份表示形式。
> * `<MM>`映射到兩位數的月表示形式。
> * `<YYYYMMDDHHMMSSfff>`映射到`YYYY`四位數位年 （）、兩位數位月 （）、`MM`兩位數位日 （）、`DD`兩位數小時 （）、`HH`兩位數分鐘 （）、`MM`兩位數秒 （）`SS`和三位數毫秒 （）`fff`的時間戳記表示。

時間序列見解 預覽事件映射到 Parquet 檔內容，如下所示：

* 每個事件都會對應至單一資料列。
* 每行都包含帶有事件時間戳記**的時間戳記**列。 時間戳記屬性永遠不會為空。 如果未在事件源中指定時間戳記屬性，則預設為**事件排隊時間**。 存儲的時間戳記始終以 UTC 表示。
* 每行都包括創建時序見解環境時定義的時序 ID （TSID） 列。 TSID 屬性名稱包括`_string`尾碼。
* 作為遙測`_string`資料發送的所有其他屬性都映射到以（字串）、（`_bool`布林`_datetime`）、（日期時間）或`_double`（雙）結尾的列名稱，具體取決於屬性類型。
* 此映射架構適用于檔案格式的第一個版本，引用為**V=1，** 並存儲在同名的基本資料夾中。 隨著此功能的發展，此映射架構可能會更改，引用名稱也會增加。

## <a name="next-steps"></a>後續步驟

- 閱讀[如何為入口和查詢塑造 JSON。](./time-series-insights-update-how-to-shape-events.md)

- 閱讀有關新[資料建模](./time-series-insights-update-tsm.md)。
