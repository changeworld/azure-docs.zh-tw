---
title: 對應資料流程中的來源轉換
description: 瞭解如何在對應的資料流程中設定來源轉換。
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/16/2020
ms.openlocfilehash: adbe3ceb72d88c2a6441c04d876d92ac2ab85930
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86522766"
---
# <a name="source-transformation-in-mapping-data-flow"></a>對應資料流程中的來源轉換 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

「來源」轉換會為數據流設定您的資料來源。 在設計資料流程時，您的第一個步驟一律會設定來源轉換。 若要新增來源，請按一下 [資料流程] 畫布中的 [**新增來源**] 方塊。

每個資料流程都需要至少一個來源轉換，但您可以視需要新增多個來源來完成資料轉換。 您可以將這些來源與聯結、查閱或聯集轉換聯結在一起。

每個來源轉換只會與一個資料集或連結服務相關聯。 資料集會定義您想要寫入或讀取之資料的形狀和位置。 如果使用以檔案為基礎的資料集，您可以在來源中使用萬用字元和檔案清單，一次處理一個以上的檔案。

## <a name="inline-datasets"></a>內嵌資料集

您在建立來源轉換時所做的第一個決定，是要在資料集物件內或在來源轉換內定義您的來源資訊。 大部分的格式僅適用于其中一種。 請參考適當的連接器檔，以瞭解如何使用特定的連接器。

當內嵌和 dataset 物件都支援格式時，兩者都有其優點。 Dataset 物件是可重複使用的實體，可以在其他資料流程和活動（例如複製）中運用。 這些在使用強化的架構時特別有用。 資料集並非以 Spark 為基礎，有時候您可能需要覆寫來源轉換中的特定設定或架構投射。

使用彈性架構、一次性來源實例或參數化來源時，建議您使用內嵌資料集。 如果您的來源是高度參數化的，內嵌資料集可讓您不建立「虛擬」物件。 內嵌資料集是以 spark 為基礎，而其屬性是資料流程的原生。

若要使用內嵌資料集，請在 [**來源類型**選取器] 中選取所需的格式。 您不需要選取來源資料集，而是選取您想要連接的連結服務。

![內嵌資料集](media/data-flow/inline-selector.png "內嵌資料集")

##  <a name="supported-source-types"></a><a name="supported-sources"></a>支援的來源類型

對應資料流程遵循「解壓縮」、「載入」、「轉換」（ELT）方法，並適用于所有 Azure 中的*臨時*資料集。 目前，下列資料集可以用於來源轉換：

| 連接器 | 格式 | 資料集/內嵌 |
| --------- | ------ | -------------- |
| [Azure Blob 儲存體](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[分隔符號文字](format-delimited-text.md#mapping-data-flow-properties)<br>[差異（預覽）](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>-/✓<br>✓/✓<br/>✓/-<br>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[分隔符號文字](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties)  | ✓/-<br>✓/-<br>✓/✓<br/>✓/-<br>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) \(部分機器翻譯\) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Common Data Model （預覽）](format-common-data-model.md#source-properties)<br>[分隔符號文字](format-delimited-text.md#mapping-data-flow-properties)<br>[差異（預覽）](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br/>-/✓<br>✓/-<br>-/✓<br>✓/✓<br>✓/-<br/>✓/-<br>✓/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |

這些連接器的特定設定位於 [**來源選項**] 索引標籤中。這些設定的資訊和資料流程腳本範例位於連接器檔中。 

Azure Data Factory 可以存取超過 [90 種原生連接器](connector-overview.md)。 若要在資料流程中包含來自其他來源的資料，請使用複製活動，將該資料載入其中一個支援的臨時區域。

## <a name="source-settings"></a>來源設定

新增來源之後，請透過 [**來源設定**] 索引標籤進行設定。您可以在這裡挑選或建立來源點的資料集。 您也可以選取資料的架構和取樣選項。

![[來源設定] 索引標籤](media/data-flow/source1.png "[來源設定] 索引標籤")

**輸出資料流程名稱：** 來源轉換的名稱。

**來源類型：** 選擇要使用內嵌資料集或現有的資料集物件。

**測試連接：** 測試資料流程的 spark 服務是否可以成功連接到您的源資料集所使用的連結服務。 必須開啟 Debug 模式，才能啟用這項功能。

**架構漂移：** [架構漂移](concepts-data-flow-schema-drift.md)是 data factory 能夠以原生方式處理您資料流程中的彈性架構，而不需要明確地定義資料行變更。

* 如果來源資料行經常變更，請選取 [**允許架構漂移**] 方塊。 此設定可讓所有傳入的來源欄位流經接收的轉換。

* 選擇 [**推斷漂移資料行類型**] 將會指示 data factory 偵測並定義每個探索到的新資料行的資料類型。 關閉這項功能之後，所有漂移的資料行都是字串類型。

**驗證架構：** 如果選取了 [驗證架構]，當傳入的來源資料不符合資料集的定義架構時，資料流程將無法執行。

**略過行計數：**[略過行計數] 欄位會指定資料集開頭要忽略的行數。

**取樣：** 啟用取樣以限制來源的資料列數目。 當您從來源測試或取樣資料以進行調試時，請使用此設定。

若要驗證您的來源設定是否正確，請開啟 [偵測模式]，並提取資料預覽。 如需詳細資訊，請參閱[Debug mode](concepts-data-flow-debug-mode.md)。

> [!NOTE]
> 開啟 [偵錯模式] 時，[調試設定] 中的 [資料列限制] 設定將會在資料預覽期間覆寫來源中的取樣設定。

## <a name="source-options"></a>來源選項

[來源選項] 索引標籤包含所選連接器和格式的特定設定。 如需詳細資訊和範例，請參考相關的[連接器檔](#supported-sources)。

## <a name="projection"></a>Projection

如同資料集內的架構，來源中的投射會定義來源資料的資料行、類型和格式。 對於大部分的資料集類型（例如 SQL 和 Parquet），會修正來源中的投射，以反映資料集中所定義的架構。 當您的來源檔案不是強型別（例如，一般 csv 檔案，而不是 Parquet 檔案）時，您可以在來源轉換中定義每個欄位的資料類型。

![[投射] 索引標籤上的設定](media/data-flow/source3.png "Projection")

如果您的文字檔沒有已定義的架構，請選取 [偵測**資料類型**]，讓 Data Factory 將會取樣並推斷資料類型。 選取 [**定義預設格式**] 以自動偵測預設資料格式。

**重設架構**會將投影重設為參考的資料集中所定義的投射。

您可以在「向下串流衍生資料行」轉換中修改資料行資料類型。 使用 [選取] 轉換來修改資料行名稱。

### <a name="import-schema"></a>匯入架構

[**預測**] 索引標籤上的 [匯**入架構**] 按鈕可讓您使用主動的 debug 叢集來建立架構投射。 適用于每個來源類型，在此匯入架構將會覆寫資料集中定義的投影。 Dataset 物件將不會變更。

這在支援複雜資料結構的資料集（例如 Avro 和 Azure Cosmos DB）中很有用，因為它不需要架構定義存在於資料集內。 對於內嵌資料集，這是參考資料行中繼資料的唯一方式，而不需要架構漂移。

## <a name="optimize-the-source-transformation"></a>優化來源轉換

[**優化**] 索引標籤可讓您在每個轉換步驟中編輯分割區資訊。 在大部分情況下，**使用目前**的資料分割將會針對來源的理想資料分割結構進行優化。

如果您是從 Azure SQL Database 來源讀取，自訂的**來源**分割區可能會以最快的速度讀取資料。 ADF 會以平行方式連接到您的資料庫，以讀取大型查詢。 此來源資料分割可以在資料行上進行或使用查詢來完成。

![來源分割區設定](media/data-flow/sourcepart3.png "分割")

如需有關對應資料流程內優化的詳細資訊，請參閱 [[優化]](concepts-data-flow-overview.md#optimize)索引標籤。

## <a name="next-steps"></a>後續步驟

使用[衍生的資料行轉換](data-flow-derived-column.md)和[選取轉換](data-flow-select.md)，開始建立您的資料流程。
