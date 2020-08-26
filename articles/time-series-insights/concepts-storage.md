---
title: 儲存體總覽-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: 瞭解 Azure 時間序列深入解析 Gen2 中的資料儲存體。
author: esung22
ms.author: elsung
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: a0f1e7789c0cebdd1cb5b22f21151020a0be09c9
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855121"
---
# <a name="data-storage"></a>資料儲存體

當您建立 Azure 時間序列深入解析 Gen2 環境時，您會建立兩個 Azure 資源：

* 可以針對暖資料儲存進行設定的 Azure 時間序列深入解析 Gen2 環境。
* 用於冷資料儲存的 Azure 儲存體帳戶。

您暖存放區中的資料只能透過 [時間序列查詢 api](./time-series-insights-update-tsq.md) 和 [Azure 時間序列深入解析 TSI Explorer](./time-series-insights-update-explorer.md)來使用。 您的暖存放區將會在建立 Azure 時間序列深入解析 Gen2 環境時，于選取的 [保留期間](./time-series-insights-update-plan.md#the-preview-environment) 內包含最近的資料。

Azure 時間序列深入解析 Gen2 會以 [Parquet 檔案格式](#parquet-file-format-and-folder-structure)，將您的冷儲存資料儲存至 Azure Blob 儲存體。 Azure 時間序列深入解析 Gen2 會以獨佔方式管理此冷存放區資料，但可供您直接讀取為標準 Parquet 檔案。

> [!WARNING]
> 身為冷存放區資料所在 Azure Blob 儲存體帳戶的擁有者，您可以完整存取帳戶中的所有資料。 此存取權包括寫入和刪除權限。 請勿編輯或刪除 Azure 時間序列深入解析 Gen2 寫入的資料，因為這可能會導致資料遺失。

## <a name="data-availability"></a>資料可用性

Azure 時間序列深入解析 Gen2 資料分割和索引資料，以獲得最佳查詢效能。 如果在索引之後啟用) 和冷存放區，就可以從暖 (中查詢資料。 正在擷取的資料量可能會影響此可用性。

> [!IMPORTANT]
> 在資料變成可用之前，您可能會遇到最多60秒的時間。 如果您遭遇超過 60 秒的嚴重延遲，請透過 Azure 入口網站提交支援票證。

## <a name="azure-storage"></a>Azure 儲存體

本節說明與 Azure 時間序列深入解析 Gen2 相關的 Azure 儲存體詳細資料。

如需 Azure Blob 儲存體的完整說明，請參閱[儲存體 Blob 簡介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-storage-account"></a>您的儲存體帳戶

當您建立 Azure 時間序列深入解析 Gen2 環境時，會建立一個 Azure 儲存體帳戶做為您的長期冷存放區。  

Azure 時間序列深入解析 Gen2 會在您的 Azure 儲存體帳戶中最多保留每個事件的兩個複本。 一個複本儲存依擷取時間排序的事件，一律允許以時間排序的順序存取事件。 經過一段時間後，Azure 時間序列深入解析 Gen2 也會建立資料的重新分割複本，以針對效能查詢進行優化。

您的所有資料都會無限期地儲存在您的 Azure 儲存體帳戶中。

#### <a name="writing-and-editing-blobs"></a>寫入和編輯 blob

為確保查詢效能和資料可用性，請勿編輯或刪除 Azure 時間序列深入解析 Gen2 建立的任何 blob。

#### <a name="accessing-cold-store-data"></a>存取冷存放區資料

除了從 [AZURE 時間序列深入解析 TSI Explorer](./time-series-insights-update-explorer.md) 和 [時間序列查詢 api](./time-series-insights-update-tsq.md)存取您的資料之外，您也可能想要直接從儲存在冷存放區中的 Parquet 檔案存取您的資料。 例如，您可以讀取、轉換和清除 Jupyter 筆記本中的資料，然後將其用來定型相同 Spark 工作流程中的 Azure Machine Learning 模型。

若要直接從您的 Azure 儲存體帳戶存取資料，您需要用來儲存 Azure 時間序列深入解析 Gen2 資料之帳戶的讀取存取權。 接著，您可以根據 Parquet 檔案的建立時間來讀取選取的資料，而此檔案位於底下 [Parquet 檔案格式](#parquet-file-format-and-folder-structure)一節所述的 `PT=Time` 資料夾中。  如需對儲存體帳戶啟用讀取存取權的詳細資訊，請參閱[管理對儲存體帳戶資源的存取](../storage/blobs/storage-manage-access-to-resources.md)。

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
* 每個資料列都包含具有事件時間戳記的**時間戳記**資料行。 時間戳記屬性絕不會是 Null。 如果未在事件來源中指定時間戳記屬性，它會預設為加入 **佇列的時間** 。 儲存的時間戳記一律採用 UTC 格式。
* 每個資料列都包含 (TSID) 資料行 (s 的時間序列識別碼，) 如建立 Azure 時間序列深入解析 Gen2 環境時所定義。 TSID 屬性名稱包含 `_string` 尾碼。
* 所有其他以遙測資料傳送的屬性都會對應到以 (布林值結尾的資料行名稱 `_bool`) 、 `_datetime` (時間戳記) 、 `_long` (long) 、 `_double` (double) 、 `_string` (string) 或 `dynamic` (dynamic) ，視屬性類型而定。  如需詳細資訊，請參閱 [支援的資料類型](./concepts-supported-data-types.md)。
* 這個對應結構描述會套用至檔案格式的第一個版本 (稱之為 **V=1**)，並儲存在相同名稱的基底資料夾中。 隨著這項功能的演進，此對應結構描述可能會有所變更，且參考名稱會遞增。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [資料模型](./time-series-insights-update-tsm.md)化。

* 規劃您的 [Azure 時間序列深入解析 Gen2 環境](./time-series-insights-update-plan.md)。
