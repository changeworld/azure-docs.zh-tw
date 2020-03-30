---
title: 映射資料流程的接收器轉換
description: 瞭解如何在映射資料流程時配置接收器轉換。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: a0b9d424c1995fba075c05ffe5058e297d764775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531255"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>映射資料流程的接收器轉換

轉換資料後，可以將資料沉入目標資料集。 每個資料流程至少需要一個接收器轉換，但您可以根據需要寫入盡可能多的接收器來完成轉換流。 要寫入其他接收器，請通過新分支和條件拆分創建新流。

每個接收器轉換都與一個資料工廠資料集關聯。 資料集定義要寫入的資料的形狀和位置。

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>映射資料流程中支援的接收器連接器

目前，以下資料集可用於接收器轉換：
    
* [Azure Blob 存儲](connector-azure-blob-storage.md#mapping-data-flow-properties)（JSON、Avro、文本、鑲木地板）
* [Azure 資料存儲湖存儲第 1 代](connector-azure-data-lake-store.md#mapping-data-flow-properties)（JSON、Avro、文本、鑲木地板）
* [Azure 資料存儲第 2 代](connector-azure-data-lake-storage.md#mapping-data-flow-properties)（JSON、Avro、文本、鑲木地板）
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure 宇宙DB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

特定于這些連接器的**設置位於"設置"** 選項卡中。有關這些設置的資訊位於連接器文檔中。 

Azure 資料工廠可以訪問[90 多個本機連接器](connector-overview.md)。 要將資料從資料流程寫入這些其他源，請使用複製活動在資料流程完成後從受支援的過渡區域之一載入該資料。

## <a name="sink-settings"></a>接收器設置

添加接收器後，通過 **"接收器"** 選項卡進行配置。在這裡，您可以選擇或創建接收器寫入的資料集 

![接收器設置](media/data-flow/sink-settings.png "水槽設置")

**架構漂移：**[架構漂移](concepts-data-flow-schema-drift.md)是資料工廠在無需顯式定義列更改的情況下，在資料流程中本機處理靈活架構的能力。 啟用**允許架構漂移**在接收器資料架構中定義的內容之上寫入其他列。

**驗證架構：** 如果選擇了驗證架構，則如果在源投影中找不到傳入源架構的任何列，或者資料類型不匹配，則資料流程將失敗。 使用此設置強制來源資料滿足已定義的投影的協定。 在資料庫源方案中，發出列名稱或類型已更改的信號非常有用。

## <a name="field-mapping"></a>欄位對應

與"選擇"轉換類似，在接收器的 **"映射"** 選項卡中，可以決定將寫入哪些傳入列。 預設情況下，將映射所有輸入列（包括漂移列）。 這稱為**自動映射**。

關閉自動映射時，您可以選擇添加基於列的固定映射或基於規則的映射。 基於規則的映射允許您編寫具有模式匹配的運算式，而固定映射將映射邏輯列和物理列名稱。 有關基於規則的映射的詳細資訊，請參閱[映射資料流程的列模式](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)。

## <a name="custom-sink-ordering"></a>自訂接收器訂購

預設情況下，資料以非確定性順序寫入多個接收器。 執行引擎將在轉換邏輯完成時並行寫入資料，並且接收器順序可能因每次運行而異。 要指定和精確接收器排序，請在資料流程的一般選項卡中啟用**自訂接收器排序**。 啟用後，將按增加順序寫入接收器。

![自訂接收器訂購](media/data-flow/custom-sink-ordering.png "自訂接收器訂購")

## <a name="data-preview-in-sink"></a>接收器中的資料預覽

在調試群集上獲取資料預覽時，不會將資料寫入接收器。 將返回資料外觀的快照，但不會將任何內容寫入您的目標。 要測試將資料寫入接收器，請從管道畫布運行管道調試。

## <a name="next-steps"></a>後續步驟
現在，您已經創建了資料流程，請向管道添加[資料流程活動](concepts-data-flow-overview.md)。
