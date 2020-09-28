---
title: 對應資料流程中的接收轉換
description: 瞭解如何在對應的資料流程中設定接收轉換。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/27/2020
ms.openlocfilehash: b92b6930193b77d0fb50ad707001a6fca0401970
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91404753"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>對應資料流程中的接收轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您完成資料轉換之後，請使用接收轉換將資料寫入目的地存放區。 每個資料流程都需要至少一個接收轉換，但您可以視需要寫入至任意數量的接收，以完成轉換流程。 若要寫入至其他接收，請透過新的分支和條件式分割來建立新的資料流程。

每個接收轉換都只會與一個 Azure Data Factory 資料集物件或連結服務相關聯。 接收轉換會決定您要寫入之資料的形狀和位置。

## <a name="inline-datasets"></a>內嵌資料集

建立接收轉換時，請選擇是否要在 dataset 物件內或在接收轉換中定義接收資訊。 大部分的格式僅適用于其中一種。 請參考適當的連接器檔，以瞭解如何使用特定的連接器。

當內嵌和資料集物件都支援格式時，兩者都有其優點。 Dataset 物件是可重複使用的實體，可在其他資料流程和活動（例如複製）中運用。 這些在使用強化的架構時特別有用。 資料集並非以 Spark 為基礎，而且有時候您可能需要覆寫接收轉換中的某些設定或架構投射。

使用彈性架構、一次性接收實例或參數化接收時，建議使用內嵌資料集。 如果您的接收經過大量參數化，則內嵌資料集可讓您不建立「虛擬」物件。 內嵌資料集是以 spark 為基礎，而其屬性則是資料流程的原生。

若要使用內嵌資料集，請在 **接收器類型** 選取器中選取所要的格式。 您可以選取想要連接的連結服務，而不是選取接收資料集。

![內嵌資料集](media/data-flow/inline-selector.png "內嵌資料集")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> 支援的接收類型

對應資料流程會遵循 (ELT) 方法的解壓縮、載入、轉換，而且適用于所有 Azure 中的 *暫存* 資料集。 下列資料集目前可用於來源轉換：

| 連接器 | 格式 | 資料集/內嵌 |
| --------- | ------ | -------------- |
| [Azure Blob 儲存體](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [分隔符號文字](format-delimited-text.md#mapping-data-flow-properties) <br> [差異 (預覽) ](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [分隔符號文字](format-delimited-text.md#mapping-data-flow-properties) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) \(部分機器翻譯\) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [分隔符號文字](format-delimited-text.md#mapping-data-flow-properties) <br> [差異 (預覽) ](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Common Data Model (preview) ](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure CosmosDB (SQL API) ](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

這些連接器特定的設定位於 [ **設定** ] 索引標籤中。這些設定的資訊和資料流程腳本範例位於連接器檔中。 

Azure Data Factory 可以存取超過 [90 種原生連接器](connector-overview.md)。 若要從您的資料流程將資料寫入其他來源，請使用複製活動從支援的接收載入該資料。

## <a name="sink-settings"></a>接收設定

新增接收之後，請透過 [ **接收** ] 索引標籤設定。您可以在這裡挑選或建立接收所寫入的資料集。 以下是一段影片，解說文字分隔檔案類型的數個不同接收選項：

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![接收設定](media/data-flow/sink-settings.png "接收設定")

**架構漂移：** [架構漂移](concepts-data-flow-schema-drift.md) 是 data factory 能夠以原生方式處理資料流程中的彈性架構，而不需要明確定義資料行變更。 啟用 [ **允許架構漂移** ]，以在接收資料架構中定義的最上層寫入額外的資料行。

**驗證架構：** 如果選取 [驗證架構]，如果在來源投射中找不到傳入來源架構的任何資料行，或資料類型不相符，資料流程將會失敗。 您可以使用此設定來強制來源資料符合您所定義投射的合約。 這在資料庫來源案例中非常實用，可表示資料行名稱或類型已變更。

## <a name="field-mapping"></a>欄位對應

類似于 Select 轉換，在接收器的 [ **對應** ] 索引標籤中，您可以決定要寫入的傳入資料行。 預設會對應所有輸入資料行，包括漂移資料行。 這就是所謂的 **自動對應**。

當您關閉自動對應時，可以加入宣告固定資料行型對應或以規則為基礎的對應。 以規則為基礎的對應可讓您撰寫具有模式比對的運算式，而固定對應會對應邏輯和實體資料行名稱。 如需以規則為基礎的對應詳細資訊，請參閱 [對應資料流程中的資料行模式](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)。

## <a name="custom-sink-ordering"></a>自訂接收順序

依預設，資料會以不具決定性的順序寫入至多個接收器。 執行引擎會以平行方式寫入資料，因為轉換邏輯已完成，而且每次執行時的接收順序可能會不同。 若要指定和確切的接收順序，請在資料流程的 [一般] 索引標籤中啟用 **自訂接收順序** 。 啟用時，接收會依順序以遞增順序寫入。

![自訂接收順序](media/data-flow/custom-sink-ordering.png "自訂接收順序")

## <a name="data-preview-in-sink"></a>接收中的資料預覽

當您在偵錯工具叢集上提取資料預覽時，不會將任何資料寫入至您的接收。 將會傳回資料外觀的快照集，但不會將任何內容寫入目的地。 若要測試將資料寫入至您的接收，請從管線畫布執行管線偵錯工具。

## <a name="next-steps"></a>後續步驟
現在您已建立資料流程，請將 [資料流程活動新增至您的管線](concepts-data-flow-overview.md)。
