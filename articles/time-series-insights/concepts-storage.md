---
title: 儲存體總覽-Azure 時間序列深入解析 |Microsoft Docs
description: 深入瞭解 Azure 時間序列深入解析中的資料存放區。
author: esung22
ms.author: elsung
manager: diegoviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: fc6b6b42293b4f2028f1a12af950e96e28febc87
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085560"
---
# <a name="data-storage"></a>資料儲存體

當您建立時間序列深入解析的*隨用隨付*（PAYG） SKU 環境時，您會建立兩個 Azure 資源：

* 可以針對暖資料儲存設定的 Azure 時間序列深入解析環境。
* 用於冷資料儲存的 Azure 儲存體帳戶。

您的暖存放區中的資料只能透過[時間序列查詢](./time-series-insights-update-tsq.md)和[Azure 時間序列深入解析 explorer](./time-series-insights-update-explorer.md)來使用。 您的暖存放區將包含[保留期間](./time-series-insights-update-plan.md#the-preview-environment)內的最近資料，而這個保留期間是在建立時間序列深入解析環境時所選取的。

Azure 時間序列深入解析以[Parquet 檔案格式](#parquet-file-format-and-folder-structure)將您的冷存放區資料儲存至 Azure Blob 儲存體。 時間序列深入解析以獨佔方式管理此冷存放區資料，但可供您直接讀取為標準 Parquet 檔案。

> [!WARNING]
> 身為冷存放區資料所在 Azure Blob 儲存體帳戶的擁有者，您可以完整存取帳戶中的所有資料。 此存取權包括寫入和刪除權限。 請勿編輯或刪除 Azure 時間序列深入解析寫入的資料，因為這可能會導致資料遺失。

## <a name="data-availability"></a>資料可用性

Azure 時間序列深入解析分割和索引資料，以獲得最佳查詢效能。 資料會從暖（啟用時）和冷存放區編制索引之後，都可供查詢。 正在擷取的資料量可能會影響此可用性。

> [!IMPORTANT]
> 在資料變成可用之前，您可能會遇到最多60秒的時間。 如果您遭遇超過 60 秒的嚴重延遲，請透過 Azure 入口網站提交支援票證。

## <a name="azure-storage"></a>Azure 儲存體

本節說明與 Azure 時間序列深入解析相關的 Azure 儲存體詳細資料。

如需 Azure Blob 儲存體的完整說明，請參閱[儲存體 Blob 簡介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-storage-account"></a>您的儲存體帳戶

當您建立 Azure 時間序列深入解析 PAYG 環境時，會建立 Azure 儲存體帳戶作為長期冷存放區。  

Azure 時間序列深入解析在您的 Azure 儲存體帳戶中最多保留兩個事件的複本。 一個複本儲存依擷取時間排序的事件，一律允許以時間排序的順序存取事件。 經過一段時間後，時間序列深入解析也會建立資料的重新分割複本，以針對高效能時間序列深入解析查詢進行優化。

您的所有資料會無限期地儲存在您的 Azure 儲存體帳戶中。

#### <a name="writing-and-editing-time-series-insights-blobs"></a>寫入和編輯時間序列深入解析 Blob

若要確保查詢效能和資料可用性，請勿編輯或刪除時間序列深入解析建立的任何 blob。

#### <a name="accessing-time-series-insights-cold-store-data"></a>存取時間序列深入解析冷存放區資料

除了從[時間序列深入解析 explorer](./time-series-insights-update-explorer.md)和[時間序列查詢](./time-series-insights-update-tsq.md)存取資料以外，您可能也會想要從儲存在冷存放區中的 Parquet 檔案直接存取您的資料。 例如，您可以讀取、轉換和清除 Jupyter 筆記本中的資料，然後將其用來定型相同 Spark 工作流程中的 Azure Machine Learning 模型。

若要直接從您的 Azure 儲存體帳戶存取資料，您需要對用來儲存時間序列深入解析預覽資料的帳戶具有讀取存取權。 接著，您可以根據 Parquet 檔案的建立時間來讀取選取的資料，而此檔案位於底下 [Parquet 檔案格式](#parquet-file-format-and-folder-structure)一節所述的 `PT=Time` 資料夾中。  如需對儲存體帳戶啟用讀取存取權的詳細資訊，請參閱[管理對儲存體帳戶資源的存取](../storage/blobs/storage-manage-access-to-resources.md)。

#### <a name="data-deletion"></a>刪除資料

請勿刪除您的 Azure 時間序列深入解析檔案。 僅在 Azure 時間序列深入解析內管理相關資料。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 檔案格式和資料夾結構

Parquet 是開放原始碼的單欄式檔案格式，專為有效率的儲存和效能所設計。 Azure 時間序列深入解析使用 Parquet 來大規模啟用以時間序列識別碼為基礎的查詢效能。  

如需有關 Parquet 檔案類型的詳細資訊，請參閱 [Parquet 文件](https://parquet.apache.org/documentation/latest/)。

Azure 時間序列深入解析會儲存資料的複本，如下所示：

* 第一個初始複本是透過擷取時間來分割，並大略地以抵達的順序儲存資料。 此資料位於 `PT=Time` 資料夾中：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 第二個重新分割的複本是依時間序列識別碼來分組，並位於 `PT=TsId` 資料夾中：

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

資料夾中 blob 名稱的時間戳記 `PT=Time` 會對應到要 Azure 時間序列深入解析之資料的抵達時間，而不是事件的時間戳記。

`PT=TsId` 資料夾中的資料將會在一段時間後針對查詢最佳化，而且不是靜態資料。 在重新分割期間，某些事件可能會出現在多個 blob 中。 此資料夾中的 blob 命名不保證會維持不變。 

一般而言，如果您需要直接透過 Parquet 檔存取資料，請使用 `PT=Time` 資料夾。  未來的功能可以有效率地存取 `PT=TsId` 資料夾。 

> [!NOTE]
>
> * `<YYYY>` 對應至四位數年份表示法。
> * `<MM>` 對應至兩位數月份表示法。
> * `<YYYYMMDDHHMMSSfff>` 對應至四位數年份 (`YYYY`)、兩位數月份 (`MM`)、兩位數日 (`DD`)、兩位數小時 (`HH`)、兩位數分鐘 (`MM`)、兩位數秒 (`SS`) 和三位數毫秒 (`fff`) 的時間戳記表示法。

時間序列深入解析預覽事件會對應至 Parquet 檔案內容，如下所示：

* 每個事件都會對應至單一資料列。
* 每個資料列都包含具有事件時間戳記的**時間戳記**資料行。 時間戳記屬性絕不會是 Null。 如果事件來源中未指定時間戳記屬性，它會預設為加入**佇列的時間**。 儲存的時間戳記一律採用 UTC 格式。
* 每個資料列都包含建立時間序列深入解析環境時所定義的時間序列識別碼 (TSID) 資料行。 TSID 屬性名稱包含 `_string` 尾碼。
* 以遙測資料傳送的所有其他屬性都會對應到結尾為 `_bool` （布林值）、 `_datetime` （時間戳記）、 `_long` （長）、 `_double` （雙精確度）、 `_string` （字串）或（動態）的資料行名稱 `dynamic` ，視屬性類型而定。  如需詳細資訊，請參閱[支援的資料類型](./concepts-supported-data-types.md)。
* 這個對應結構描述會套用至檔案格式的第一個版本 (稱之為 **V=1**)，並儲存在相同名稱的基底資料夾中。 隨著這項功能的演進，此對應結構描述可能會有所變更，且參考名稱會遞增。

## <a name="next-steps"></a>後續步驟

* 閱讀有關[資料模型](./time-series-insights-update-tsm.md)化的資訊。

* 規劃您的[Azure 時間序列深入解析預覽環境](./time-series-insights-update-plan.md)。
