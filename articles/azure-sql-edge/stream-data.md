---
title: Azure SQL Edge (預覽) 中的資料串流
description: 瞭解 Azure SQL Edge （預覽）中的資料串流。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 866c74fbdfcfcef7cbb7d6cddb360c4265a2f776
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669608"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Azure SQL Edge (預覽) 中的資料串流

Azure SQL Edge （預覽）提供下列選項來執行資料串流： 

- 部署在 Azure 中建立的 Azure 串流分析 edge 作業。 如需詳細資訊，請參閱[部署 Azure 串流分析作業](deploy-dacpac.md)。
- 在 Azure SQL Edge 中使用 T-sql 串流來建立串流作業，而不需要在 Azure 中設定串流作業。 

雖然您可以使用這兩個選項來執行 Azure SQL Edge 中的資料串流，但您應該只使用其中一個。 當您同時使用這兩種情況時，可能會有影響資料串流作業運作的競爭條件。

T-sql 串流是本文的重點。 它提供即時的資料串流、分析和事件處理，可同時分析和處理多個來源的大量快速串流資料。 T-sql 串流的建立方式，是使用與[Azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction)在 Microsoft Azure 中的相同高效能串流引擎。 此功能支援在邊緣上執行的 Azure 串流分析所提供的一組類似功能。

如同串流分析，T-sql 串流能夠辨識從許多 IoT 輸入來源（包括裝置、感應器和應用程式）解壓縮的資訊模式和關聯性。 您可以使用這些模式來觸發動作並起始工作流程。 例如，您可以建立警示、將資訊饋送至報表或視覺效果解決方案，或儲存資料以供稍後使用。 

T-sql 串流可協助您：

* 分析來自 IoT 裝置的即時遙測串流。
* 針對從自發和無人駕駛車輛產生的資料，使用即時分析。
* 使用高價值產業或製造資產的遠端監視和預測性維護。
* 在農業或能源伺服器陣列中，使用 IoT 感應器讀數的異常偵測和模式辨識。

## <a name="how-does-t-sql-streaming-work"></a>T SQL 串流如何運作？

T-sql 串流的運作方式與[Azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work)完全相同。 例如，它會使用串流*作業*的概念來處理即時資料串流。 

串流分析作業包含下列各項：

- **資料流程輸入**：這會定義要從中讀取資料流程的資料來源連接。 Azure SQL Edge 目前支援下列串流輸入類型：
    - Edge 中樞
    - Kafka （支援 Kafka 輸入目前僅適用于 Intel/AMD64 版本的 Azure SQL Edge）。

- **資料流程輸出**：這會定義要將資料流程寫入其中的資料來源連接。 Azure SQL Edge 目前支援下列串流輸出類型
    - Edge 中樞
    - SQL （SQL 輸出可以是 Azure SQL Edge 實例內的本機資料庫，或是遠端 SQL Server 或 Azure SQL Database）。 
    - Azure Blob 儲存體

- **資料流程查詢**：這會定義要套用至輸入資料流程的轉換、匯總、篩選、排序和聯結，然後再寫入資料流程輸出。 資料流程查詢是以串流分析所使用的相同查詢語言為基礎。 如需詳細資訊，請參閱[串流分析查詢語言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)。

> [!IMPORTANT]
> 與串流分析不同的是，t-sql 資料流程目前不支援[使用參考資料進行查閱](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data)，或[在資料流程工作中使用 UDF 和 UDA](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)。

> [!NOTE]
> T-sql 資料流程僅支援串流分析支援的語言介面區子集。 如需詳細資訊，請參閱[串流分析查詢語言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)。

## <a name="limitations-and-restrictions"></a>限制事項

下列限制適用于 T-sql 串流。 

- 任何特定時間都只能有一個作用中的串流作業。 啟動另一個作業之前，必須先停止已在執行中的作業。
- 每個串流作業執行都是單一執行緒。 如果串流作業包含多個查詢，則會依序列順序評估每個查詢。

## <a name="next-steps"></a>後續步驟

- [在 Azure SQL Edge (預覽) 中建立串流分析作業](create-stream-analytics-job.md)
- [在 Azure SQL Edge (預覽) 中檢視與串流作業相關聯的中繼資料](streaming-catalog-views.md)
- [建立外部資料流](create-external-stream-transact-sql.md)