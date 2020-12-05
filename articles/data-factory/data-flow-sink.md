---
title: 對應資料流程中的接收轉換
description: 瞭解如何在對應的資料流程中設定接收轉換。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/17/2020
ms.openlocfilehash: da89d4fbc3f9e03e76d901c2215e4f16c5273013
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621105"
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

**架構漂移**： [架構漂移](concepts-data-flow-schema-drift.md) 是 Data Factory 能夠以原生方式處理資料流程中的彈性架構，而不需要明確定義資料行變更。 啟用 [ **允許架構漂移** ]，以在接收資料架構中定義的內容上寫入額外的資料行。

**驗證架構**：如果選取了 [驗證架構]，如果在來源投射中找不到傳入來源架構的任何資料行，或資料類型不相符，資料流程將會失敗。 您可以使用此設定來強制來源資料符合您所定義投射的合約。 這在資料庫來源案例中很有用，可告知資料行名稱或類型已變更。

## <a name="cache-sink"></a>快取接收

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]
 
當資料流程將資料寫入 Spark 快取，而不是資料存放區時，就會有快取 *接收器* 。 在對應資料流程中，您可以使用快取 *查閱*，多次參考相同流程中的這項資料。 當您想要在運算式中參考資料，但是不想要明確地將資料行聯結至資料行時，這會很有用。 快取接收可協助查閱資料存放區的最大值，以及比對錯誤代碼與錯誤訊息資料庫的常見範例。 

若要寫入至快取接收，請新增接收轉換， **然後選取 [** 快取] 作為接收類型。 與其他接收類型不同的是，您不需要選取資料集或連結服務，因為您不會寫入至外部存放區。 

![選取快取接收](media/data-flow/select-cache-sink.png "選取快取接收")

在 [接收設定] 中，您可以選擇性地指定快取接收的索引鍵資料行。 在快取查閱中使用函數時，這些會用來做為相符的條件 `lookup()` 。 如果您指定索引鍵資料行，就無法 `outputs()` 在快取查閱中使用函數。 若要深入瞭解快取查閱語法，請[參閱快取查閱。](concepts-data-flow-expression-builder.md#cached-lookup)

![快取接收索引鍵資料行](media/data-flow/cache-sink-key-columns.png "快取接收索引鍵資料行")

例如，如果我在呼叫的快取接收中指定單一索引鍵資料 `column1` `cacheExample` 行，則呼叫 `cacheExample#lookup()` 會有一個參數，以指定要比對快取接收中的哪一個資料列。 此函數會針對每個對應的資料行，輸出具有個子的單一複雜資料行。

> [!NOTE]
> 快取接收必須與透過快取查閱參考它的任何轉換在完全獨立的資料流程中。 快取接收也必須寫入第一個接收。 

## <a name="field-mapping"></a>欄位對應

類似于「選取」轉換，您可以在接收的 [ **對應** ] 索引標籤上，決定要寫入的內送資料行。 預設會對應所有輸入資料行，包括漂移資料行。 這種行為稱為 *automapping*。

當您關閉 automapping 時，您可以加入固定資料行型對應或以規則為基礎的對應。 使用以規則為基礎的對應，您可以撰寫具有模式比對的運算式。 固定對應會對應邏輯和實體資料行名稱。 如需以規則為基礎的對應詳細資訊，請參閱 [對應資料流程中的資料行模式](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)。

## <a name="custom-sink-ordering"></a>自訂接收順序

依預設，資料會以不具決定性的順序寫入至多個接收器。 當轉換邏輯已完成時，執行引擎會以平行方式寫入資料，而接收順序可能會因每次執行而有所不同。 若要指定確切的接收順序，請在資料流程的 [**一般**] 索引標籤上啟用 **自訂接收順序**。 啟用時，會依順序以遞增順序寫入接收。

![顯示自訂接收順序的螢幕擷取畫面。](media/data-flow/custom-sink-ordering.png "顯示自訂接收順序的螢幕擷取畫面。")

> [!NOTE]
> 使用快取 [查閱](./concepts-data-flow-expression-builder.md#cached-lookup)時，請確定您的接收順序將快取的接收設定為1、最低的 (或依序排序的第一個) 。

![自訂接收順序](media/data-flow/cache-2.png "自訂接收順序")

### <a name="sink-groups"></a>接收群組

您可以將相同的訂單編號套用至一系列接收器，以將接收器群組在一起。 ADF 會將這些接收器視為可以平行執行的群組。 平行執行的選項會顯示在管線資料流程活動中。

## <a name="error-row-handling"></a>處理資料列時發生錯誤

寫入資料庫時，某些資料列可能會因為目的地所設定的限制而失敗。 根據預設，資料流程執行會在其取得的第一個錯誤時失敗。 在某些連接器中，您可以選擇 **在發生錯誤時繼續** 進行，即使個別的資料列發生錯誤，也會允許您的資料流程完成。 目前，這項功能僅適用于 Azure SQL Database。 如需詳細資訊，請參閱 [AZURE SQL DB 中的錯誤資料列處理](connector-azure-sql-database.md#error-row-handling)。

以下是如何在接收轉換中自動使用資料庫錯誤資料列處理的影片教學課程。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4IWne]

## <a name="data-preview-in-sink"></a>接收中的資料預覽

當您在偵錯工具叢集上提取資料預覽時，不會將任何資料寫入至您的接收。 將會傳回資料外觀的快照集，但不會將任何內容寫入目的地。 若要測試將資料寫入至您的接收，請從管線畫布執行管線偵錯工具。

## <a name="next-steps"></a>後續步驟

現在您已建立資料流程，請將 [資料流程活動新增至您的管線](concepts-data-flow-overview.md)。