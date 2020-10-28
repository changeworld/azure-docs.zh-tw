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
ms.date: 10/27/2020
ms.openlocfilehash: 6354b0a1df9d8c331de0731b230d628ac4e435df
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891344"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>對應資料流程中的接收轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您完成資料轉換之後，請使用接收轉換將資料寫入目的地存放區。 每個資料流程都需要至少一個接收轉換，但您可以視需要寫入至任意數量的接收，以完成轉換流程。 若要寫入至其他接收，請透過新的分支和條件式分割來建立新的資料流程。

每個接收轉換都只會與一個 Azure Data Factory 資料集物件或連結服務相關聯。 接收轉換會決定您要寫入之資料的形狀和位置。

## <a name="inline-datasets"></a>內嵌資料集

當您建立接收轉換時，請選擇是否在資料集物件內或在接收轉換中定義接收資訊。 大部分的格式僅適用于其中一種。 若要瞭解如何使用特定連接器，請參閱適當的連接器檔。

當內嵌和資料集物件都支援格式時，兩者都有其優點。 Dataset 物件是可重複使用的實體，可用於其他資料流程和活動，例如複製。 當您使用強化的架構時，這些可重複使用的實體特別有用。 資料集不是以 Spark 為基礎。 有時候，您可能需要覆寫接收轉換中的某些設定或架構投射。

當您使用彈性架構、一次性接收實例或參數化接收時，建議使用內嵌資料集。 如果您的接收經過大量參數化，內嵌資料集可讓您無法建立「虛擬」物件。 內嵌資料集是以 Spark 為基礎，而其屬性是資料流程的原生屬性。

若要使用內嵌資料集，請在 [ **接收器類型** 選取器] 中選取您想要的格式。 您可以選取想要連接的連結服務，而不是選取接收資料集。

![顯示內嵌選取的螢幕擷取畫面。](media/data-flow/inline-selector.png "顯示內嵌選取的螢幕擷取畫面。")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> 支援的接收類型

對應資料流程會遵循 (ELT) 方法的解壓縮、載入和轉換，且適用于所有 Azure 中的 *暫存* 資料集。 目前，您可以在來源轉換中使用下列資料集。

| 連接子 | 格式 | 資料集/內嵌 |
| --------- | ------ | -------------- |
| [Azure Blob 儲存體](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [分隔符號文字](format-delimited-text.md#mapping-data-flow-properties) <br> [差異 (預覽) ](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [分隔符號文字](format-delimited-text.md#mapping-data-flow-properties) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) \(部分機器翻譯\) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [分隔符號文字](format-delimited-text.md#mapping-data-flow-properties) <br> [差異 (預覽) ](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Common Data Model (preview) ](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL 受控執行個體 (preview) ](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

這些連接器特定的設定位於 [ **設定** ] 索引標籤上。這些設定的資訊和資料流程腳本範例位於連接器檔中。

Azure Data Factory 可以存取90以上的 [原生連接器](connector-overview.md)。 若要從您的資料流程將資料寫入其他來源，請使用複製活動從支援的接收載入該資料。

## <a name="sink-settings"></a>接收設定

新增接收之後，請透過 [ **接收** ] 索引標籤設定。您可以在這裡挑選或建立接收所寫入的資料集。 您可以在 [偵錯工具設定](concepts-data-flow-debug-mode.md)中設定資料集參數的開發值。 必須開啟 (的 Debug 模式。 ) 

下列影片說明一些不同的接收選項，用於以文字分隔的檔案類型。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![顯示接收設定的螢幕擷取畫面。](media/data-flow/sink-settings.png "顯示接收設定的螢幕擷取畫面。")

**架構漂移** ： [架構漂移](concepts-data-flow-schema-drift.md) 是 Data Factory 能夠以原生方式處理資料流程中的彈性架構，而不需要明確定義資料行變更。 啟用 [ **允許架構漂移** ]，以在接收資料架構中定義的內容上寫入額外的資料行。

**驗證架構** ：如果選取了 [驗證架構]，如果在來源投射中找不到傳入來源架構的任何資料行，或資料類型不相符，資料流程將會失敗。 您可以使用此設定來強制來源資料符合您所定義投射的合約。 這在資料庫來源案例中很有用，可告知資料行名稱或類型已變更。

**使用 TempDB：** 根據預設，Data Factory 會使用全域臨時表，在載入過程中儲存資料。 您也可以取消核取 [使用 TempDB] 選項，並改為要求 Data Factory 將臨時保存資料表儲存在使用者資料庫中，該資料庫位於要用於此接收的資料庫中。

![Tempdb](media/data-flow/tempdb.png "Tempdb")

## <a name="field-mapping"></a>欄位對應

類似于「選取」轉換，您可以在接收的 [ **對應** ] 索引標籤上，決定要寫入的內送資料行。 預設會對應所有輸入資料行，包括漂移資料行。 這種行為稱為 *automapping* 。

當您關閉 automapping 時，您可以加入固定資料行型對應或以規則為基礎的對應。 使用以規則為基礎的對應，您可以撰寫具有模式比對的運算式。 固定對應會對應邏輯和實體資料行名稱。 如需以規則為基礎的對應詳細資訊，請參閱 [對應資料流程中的資料行模式](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)。

## <a name="custom-sink-ordering"></a>自訂接收順序

依預設，資料會以不具決定性的順序寫入至多個接收器。 當轉換邏輯已完成時，執行引擎會以平行方式寫入資料，而接收順序可能會因每次執行而有所不同。 若要指定確切的接收順序，請在資料流程的 [ **一般** ] 索引標籤上啟用 **自訂接收順序** 。 啟用時，會依順序以遞增順序寫入接收。

![顯示自訂接收順序的螢幕擷取畫面。](media/data-flow/custom-sink-ordering.png "顯示自訂接收順序的螢幕擷取畫面。")

## <a name="data-preview-in-sink"></a>接收中的資料預覽

當您在偵錯工具叢集上提取資料預覽時，不會將任何資料寫入至您的接收。 將會傳回資料外觀的快照集，但不會將任何內容寫入目的地。 若要測試將資料寫入至您的接收，請從管線畫布執行管線偵錯工具。

## <a name="next-steps"></a>後續步驟
現在您已建立資料流程，請將 [資料流程活動新增至您的管線](concepts-data-flow-overview.md)。
