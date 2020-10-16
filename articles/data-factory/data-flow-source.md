---
title: 對應資料流程中的來源轉換
description: 瞭解如何在對應的資料流程中設定來源轉換。
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2020
ms.openlocfilehash: 0efcc82617c75089ff2bf396133f9fbc7ec701ea
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107686"
---
# <a name="source-transformation-in-mapping-data-flow"></a>對應資料流程中的來源轉換 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

來源轉換會設定資料流程的資料來源。 設計資料流程時，您的第一個步驟一律是設定來源轉換。 若要加入來源，請按一下 [資料流程] 畫布中的 [ **加入來源** ] 方塊。

每個資料流程都需要至少一個來源轉換，但您可以視需要新增任意數量的來源，以完成資料轉換。 您可以使用聯結、查閱或等位轉換，將這些來源聯結在一起。

每個來源轉換都只會與一個資料集或連結服務相關聯。 資料集會定義您想要寫入或讀取之資料的形狀和位置。 如果使用以檔案為基礎的資料集，您可以在來源中使用萬用字元和檔案清單，一次處理一個以上的檔案。

## <a name="inline-datasets"></a>內嵌資料集

建立來源轉換時的第一個決策是您的來源資訊是在資料集物件內或在來源轉換內定義。 大部分的格式僅適用于其中一種。 請參考適當的連接器檔，以瞭解如何使用特定的連接器。

當內嵌和資料集物件都支援格式時，兩者都有其優點。 Dataset 物件是可重複使用的實體，可在其他資料流程和活動（例如複製）中運用。 這些在使用強化的架構時特別有用。 資料集並非以 Spark 為基礎，而且有時候您可能需要覆寫來源轉換中的某些設定或架構投射。

使用彈性架構、一次性來源實例或參數化來源時，建議使用內嵌資料集。 如果您的來源經過大量參數化，則內嵌資料集可讓您無法建立「虛擬」物件。 內嵌資料集是以 spark 為基礎，而其屬性則是資料流程的原生。

若要使用內嵌資料集，請在 **來源類型** 選取器中選取所要的格式。 您可以選取想要連接的連結服務，而不是選取源資料集。

![內嵌資料集](media/data-flow/inline-selector.png "內嵌資料集")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> 支援的來源類型

對應資料流程會遵循 (ELT) 方法的解壓縮、載入、轉換，而且適用于所有 Azure 中的 *暫存* 資料集。 下列資料集目前可用於來源轉換：

| 連接子 | 格式 | 資料集/內嵌 |
| --------- | ------ | -------------- |
| [Azure Blob 儲存體](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[分隔符號文字](format-delimited-text.md#mapping-data-flow-properties)<br>[差異 (預覽) ](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>-/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[分隔符號文字](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) \(部分機器翻譯\) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Common Data Model (preview) ](format-common-data-model.md#source-properties)<br>[分隔符號文字](format-delimited-text.md#mapping-data-flow-properties)<br>[差異 (預覽) ](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br/>-/✓<br>✓/-<br>-/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL 受控執行個體 (preview) ](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

這些連接器特定的設定位於 [ **來源選項** ] 索引標籤中。這些設定的資訊和資料流程腳本範例位於連接器檔中。 

Azure Data Factory 可以存取超過 [90 種原生連接器](connector-overview.md)。 若要在資料流程中包含其他來源的資料，請使用複製活動將該資料載入其中一個支援的暫存區域。

## <a name="source-settings"></a>來源設定

新增來源之後，請透過 [ **來源設定** ] 索引標籤設定。您可以在這裡挑選或建立您的來源所指向的資料集。 您也可以選取資料的架構和取樣選項。 

您可以在 [偵錯工具設定](concepts-data-flow-debug-mode.md) 中設定資料集參數的開發值 (需要在) 上開啟 Debug 模式。

![[來源設定] 索引標籤](media/data-flow/source1.png "[來源設定] 索引標籤")

**輸出資料流程名稱：** 來源轉換的名稱。

**來源類型：** 選擇您要使用內嵌資料集或現有的資料集物件。

**測試連接：** 測試資料流程的 spark 服務是否可以成功連接到您的源資料集所使用的連結服務。 必須開啟 Debug 模式，才能啟用這項功能。

**架構漂移：** [架構漂移](concepts-data-flow-schema-drift.md) 是 data factory 能夠以原生方式處理資料流程中的彈性架構，而不需要明確定義資料行變更。

* 如果來源資料行經常變更，請選取 [ **允許架構漂移** ] 方塊。 此設定可讓所有傳入的來源欄位流經轉換到接收器。

* 選擇 [ **推斷漂移資料行類型** ]，將會指示 data factory 偵測並定義每個探索到的新資料行的資料類型。 當此功能關閉時，所有漂移資料行的類型都是字串。

**驗證架構：** 如果選取 [驗證架構]，則如果連入的來源資料不符合定義的資料集架構，資料流程將無法執行。

**略過行計數：** [略過行數] 欄位會指定在資料集的開頭要忽略的行數。

**取樣：** 啟用取樣以限制來源的資料列數目。 當您從來源測試或取樣資料以進行調試時，請使用此設定。

若要驗證您的來源是否已正確設定，請開啟 [偵測模式]，並提取資料預覽。 如需詳細資訊，請參閱 [偵錯工具模式](concepts-data-flow-debug-mode.md)。

> [!NOTE]
> 開啟 debug 模式時，[偵錯工具] 設定中的 [資料列限制] 設定會在資料預覽期間覆寫來源中的取樣設定。

## <a name="source-options"></a>來源選項

[來源選項] 索引標籤包含連接器和所選格式的特定設定。 如需詳細資訊和範例，請參閱相關的 [連接器檔](#supported-sources)。

## <a name="projection"></a>Projection

如同資料集中的架構，來源中的投射會定義來源資料的資料行、類型和格式。 針對大部分的資料集類型（例如 SQL 和 Parquet），來源中的投射是固定的，以反映資料集中所定義的架構。 當您的來源檔案不是強型別時 (例如，一般 csv 檔案（而不是 Parquet 檔）) ，您可以在來源轉換中定義每個欄位的資料類型。

![[投射] 索引標籤上的設定](media/data-flow/source3.png "Projection")

如果您的文字檔沒有定義的架構，請選取 [偵測 **資料類型** ]，讓 Data Factory 將會取樣並推斷資料類型。 選取 [ **定義預設格式** ]，以自動偵測預設資料格式。

**重設架構** 會將投影重設為參考資料集中定義的結果。

您可以修改下資料流程衍生資料行轉換中的資料行資料類型。 使用「選取」轉換來修改資料行名稱。

### <a name="import-schema"></a>匯入架構

[**預測**] 索引標籤上的 [匯**入架構**] 按鈕，可讓您使用主動式的 debug 叢集來建立架構投射。 在每個來源類型中都有提供，在這裡匯入架構將會覆寫資料集中定義的投射。 Dataset 物件不會變更。

在支援複雜資料結構的資料集（例如 Avro 和 Azure Cosmos DB）中，這項功能很有用，不需要在資料集中存在架構定義。 如果是內嵌資料集，這是在沒有架構漂移的情況下參考資料行中繼資料的唯一方法。

## <a name="optimize-the-source-transformation"></a>優化來源轉換

[ **優化** ] 索引標籤可讓您在每個轉換步驟中編輯分割區資訊。 在大部分的情況下， **使用目前** 的資料分割會針對來源的理想資料分割結構進行優化。

如果您要從 Azure SQL Database 來源讀取，自訂 **來源** 資料分割可能會以最快的速度讀取資料。 ADF 會以平行方式連接到您的資料庫，以讀取大型查詢。 您可以在資料行上或使用查詢來進行此來來源資料分割。

![來源分割區設定](media/data-flow/sourcepart3.png "分割")

如需有關在對應資料流程內優化的詳細資訊，請參閱 [ [優化]](concepts-data-flow-overview.md#optimize)索引標籤。

## <a name="next-steps"></a>後續步驟

使用 [衍生的資料行轉換](data-flow-derived-column.md) 和 [select 轉換](data-flow-select.md)開始建立您的資料流程。
