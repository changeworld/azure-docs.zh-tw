---
title: 對應資料串流的接收器轉換
description: 瞭解如何在映射數據流時配置接收器轉換。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 4b10a4c98abd6bec4074bf35764a9cbb85d5b157
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605973"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>對應資料串流的接收器轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

轉換數據后,可以將數據沉入目標數據集。 每個資料流至少需要一個接收器轉換,但您可以根據需要寫入盡可能多的接收器來完成轉換流。 要寫入其他接收器,請通過新分支和條件拆分創建新流。

每個接收器轉換都與一個數據工廠數據集關聯。 數據集定義要寫入的數據的形狀和位置。

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>映射資料串流支援的接收器連接器

目前,以下資料集可用於接收器轉換:
    
* [Azure Blob 儲存](connector-azure-blob-storage.md#mapping-data-flow-properties)(JSON、Avro、文本、鑲木地板)
* [Azure 資料儲存湖儲存第 1 代](connector-azure-data-lake-store.md#mapping-data-flow-properties)(JSON、Avro、文本、鑲木地板)
* [Azure 資料儲存第 2 代](connector-azure-data-lake-storage.md#mapping-data-flow-properties)(JSON、Avro、文本、鑲木地板)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure 宇宙DB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

特定於這些連接器的**設置位於"設定"** 選項卡中。有關這些設置的資訊位於連接器文檔中。 

Azure 資料工廠可以存[取 90 多個本機連接器](connector-overview.md)。 要將數據從資料流寫入這些其他源,請使用複製活動在資料流完成後從受支援的過渡區域之一載入該資料。

## <a name="sink-settings"></a>接收器設定

添加接收器後,通過 **「接收器」** 選項卡進行配置。在這裡,您可以選取或創建接收器寫入的數據集。 下面是一個影片,解釋文本分隔檔案類型的許多不同的 Sink 選項:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

![接收器設定](media/data-flow/sink-settings.png "水槽設置")

**架構漂移:**[架構漂移](concepts-data-flow-schema-drift.md)是數據工廠在無需顯式定義列更改的情況下,在資料流中本機處理靈活架構的能力。 啟用**允許架構漂移**在接收器資料架構中定義的內容之上寫入其他列。

**驗證架構:** 如果選擇了驗證架構,則如果在源投影中找不到傳入源架構的任何列,或者數據類型不匹配,則數據流將失敗。 使用此設置強制源數據滿足已定義的投影的協定。 在資料庫源方案中,發出列名稱或類型已更改的信號非常有用。

## <a name="field-mapping"></a>欄位對應

與「選擇」轉換類似,在接收器的 **「映射」** 選項卡中,可以決定將寫入哪些傳入列。 默認情況下,將映射所有輸入列(包括漂移列)。 這稱為**自動映射**。

關閉自動映射時,您可以選擇添加基於列的固定映射或基於規則的映射。 基於規則的映射允許您編寫具有模式匹配的運算式,而固定映射將映射邏輯列和物理列名稱。 有關基於規則的映射的詳細資訊,請參閱[映射資料串流的欄模式](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)。

## <a name="custom-sink-ordering"></a>自訂接收器訂購

默認情況下,數據以非確定性順序寫入多個接收器。 執行引擎將在轉換邏輯完成時並行寫入數據,並且接收器順序可能因每次運行而異。 要指定與精確接收器排序,請在資料串序的一般選項卡中啟用**自訂接收器排序**。 啟用後,將按增加順序寫入接收器。

![自訂接收器訂購](media/data-flow/custom-sink-ordering.png "自訂接收器訂購")

## <a name="data-preview-in-sink"></a>接收器中的資料預覽

在調試群集上獲取數據預覽時,不會將數據寫入接收器。 將返回數據外觀的快照,但不會將任何內容寫入您的目標。 要測試將數據寫入接收器,請從管道畫布運行管道調試。

## <a name="next-steps"></a>後續步驟
現在,您已經建立了資料串流,請向導管添加[資料串流活動](concepts-data-flow-overview.md)。
