---
title: Azure SQL Edge 中的資料串流
description: 瞭解 Azure SQL Edge 中的資料串流。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f63ab040e750c0c642c9656a5482529b926e9295
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392107"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Azure SQL Edge 中的資料串流

Azure SQL Edge 提供稱為 T-sql 串流的資料串流功能原生執行。 它提供即時資料串流、分析和事件處理，可同時分析和處理來自多個來源的大量快速串流資料。 T-sql 串流的建立方式是使用相同的高效能串流引擎來 Microsoft Azure 中的 [Azure 串流分析](../stream-analytics/stream-analytics-introduction.md) 。 此功能支援在邊緣上執行的 Azure 串流分析所提供的一組類似功能。

如同串流分析，T-sql 串流能辨識從數個 IoT 輸入來源（包括裝置、感應器和應用程式）解壓縮的資訊中的模式和關聯性。 您可以使用這些模式來觸發動作和起始工作流程。 例如，您可以建立警示、將資訊饋送至報告或視覺效果解決方案，或儲存資料以供稍後使用。 

T-sql 串流可協助您：

* 分析來自 IoT 裝置的即時遙測串流。
* 使用自主和無人駕駛車輛所產生的即時資料分析。
* 使用高價值產業或製造業資產的遠端監視和預測性維護。
* 在農業或能源陣列中使用 IoT 感應器讀數的異常偵測和模式識別。

## <a name="how-does-t-sql-streaming-work"></a>T-sql 串流如何運作？

T-sql 串流的運作方式完全與 [Azure 串流分析](../stream-analytics/stream-analytics-introduction.md#how-does-stream-analytics-work)相同。 例如，它會使用串流 *工作* 的概念來處理即時資料串流。 

串流分析作業是由下列各項所組成：

- **資料流程輸入** ：這會定義要從中讀取資料流程的資料來源連接。 Azure SQL Edge 目前支援下列串流輸入類型：
    - Edge 中樞
    - Kafka (Kafka 輸入的支援目前僅適用于 Intel/AMD64 版本的 Azure SQL Edge。 ) 

- **資料流程輸出** ：這會定義資料來源的連接，以將資料流程寫入至其中。 Azure SQL Edge 目前支援下列串流輸出類型
    - Edge 中樞
    - SQL (SQL 輸出可以是 Azure SQL Edge 實例內的本機資料庫，或遠端 SQL Server 或 Azure SQL Database。 )  

- **資料流程查詢** ：這會定義要套用至輸入資料流程的轉換、匯總、篩選、排序和聯結，然後再寫入資料流程輸出。 資料流程查詢是以與串流分析所使用的相同查詢語言為基礎。 如需詳細資訊，請參閱 [串流分析查詢語言](/stream-analytics-query/stream-analytics-query-language-reference)。

> [!IMPORTANT]
> 不同于串流分析的 t-sql 串流，目前不支援 [使用參考資料進行查閱](../stream-analytics/stream-analytics-use-reference-data.md) ，或 [使用 UDF 的和 UDA 在串流作業中](../stream-analytics/streaming-technologies.md#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)。

> [!NOTE]
> T-sql 串流僅支援串流分析所支援的語言介面區子集。 如需詳細資訊，請參閱 [串流分析查詢語言](/stream-analytics-query/stream-analytics-query-language-reference)。

## <a name="limitations-and-restrictions"></a>限制事項

下列限制和限制適用于 T-sql 串流。 

- 在任何特定時間，都只能有一個作用中的串流作業。 開始另一個作業之前，必須先停止已在執行中的作業。
- 每個串流作業執行都是單一執行緒。 如果串流作業包含多個查詢，則每個查詢都會以序列順序進行評估。
- 當您在 Azure SQL Edge 中停止串流作業時，可能會有一些延遲時間，才能開始下一個串流作業。 因為基礎串流處理常式必須停止以回應停止工作要求，然後重新開機以回應啟動作業要求，所以引進此延遲。 
- 適用于 kafka 資料流程的 t-sql 串流最多32個數據分割。 嘗試設定較高的資料分割計數將會導致錯誤。 

## <a name="next-steps"></a>後續步驟

- [在 Azure SQL Edge 中建立串流分析作業 ](create-stream-analytics-job.md)
- [在 Azure SQL Edge 中查看與串流作業相關聯的中繼資料 ](streaming-catalog-views.md)
- [建立外部資料流](create-external-stream-transact-sql.md)