---
title: Azure SQL Edge (預覽) 中的資料串流
description: 了解 Azure SQL Edge (預覽) 中的資料串流
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 15b6b80f9924b050b388d74adc1d67db6a386134
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594507"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Azure SQL Edge (預覽) 中的資料串流

Azure SQL Edge (預覽) 提供兩個不同的選項供您實作資料串流。 

1. 部署在 Azure 中所建立的 Azure 串流分析 Edge 作業。 如需如何在 Azure SQL Edge 中部署 Azure 串流分析 Edge 作業的詳細資訊，請參閱[部署 Azure 串流分析作業](deploy-dacpac.md)。
2. 使用新的 **T-SQL 串流**功能在 SQL Edge 中建立串流作業，而不需要在 Azure 中設定串流作業。 

雖然可以同時使用這兩個選項在 SQL Edge 中實作資料串流，但強烈建議您只使用其中一個選項。 同時使用這兩種選項可能會造成競爭情形，而影響到資料串流作業的運作。

本文件其餘部分所參考的是 **T-SQL 串流**這個新功能，其可提供即時的資料串流、分析和事件處理，以同時分析和處理來自多個來源的大量、快速串流資料。 *T-SQL 串流*是使用能在 Microsoft Azure 中支援 [Azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction)的同一個高效能串流引擎所建置的，並可支援在邊緣上執行的 Azure 串流分析所提供的一組類似功能。

如同 Azure 串流分析，T-SQL 串流也能夠辨識從眾多 IoT 輸入來源 (包括裝置、感應器和應用程式) 擷取而來的資訊有何模式和關聯性。 這些模式可以用來觸發動作並起始工作流程，例如建立警示、將資訊提供給報告或視覺化解決方案，或是儲存資料以供之後使用。 

下列案例是您何時可以使用 T-SQL 串流的範例：

* 分析來自 IoT 裝置的即時遙測串流。
* 即時分析從自駕車和無人車產生的資料。
* 遠端監視和預測性維護高價值產業或製造資產。
* 農田或能源農場中 IoT 感應器讀數的異常偵測和 (或) 模式辨識。

## <a name="how-does-t-sql-streaming-work"></a>T SQL 串流如何運作？

T-SQL 串流的運作方式與 [Azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work)完全相同，例如，其使用了串流作業的概念來處理即時資料串流。 

串流分析作業包含下列各項：

- 串流輸入 - 串流輸入會定義要從中讀取串流的資料來源連線。 Azure SQL Edge 目前支援下列串流輸入類型：
    - Edge 中樞
    - Kafka - Kafka 輸入支援目前僅適用於 Intel/AMD64 版本的 Azure SQL Edge。

- 串流輸出 - 串流輸出會定義要作為資料串流寫入目的地的資料來源連線。 Azure SQL Edge 目前支援下列串流輸出類型
    - Edge 中樞
    - SQL - SQL 輸出可以是 SQL Edge 執行個體內的本機資料庫，也可以是遠端 SQL Server 或 Azure SQL Database。 
    - Azure Blob 儲存體

- 串流查詢 - 串流查詢會定義必須先套用至輸入串流的轉換、彙總、篩選、排序和聯結作業，然後才寫入到串流輸出。 串流查詢會以 Azure 串流分析所使用的相同查詢語言為基礎。 如需 Azure 串流分析查詢語言的詳細資訊，請參閱[串流分析查詢語言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)。

> [!IMPORTANT]
> 不同於 Azure 串流分析，T-SQL 串流目前不支援[使用參考資料進行查閱](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data)或[在串流作業中使用 UDF 和 UDA](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)。

> [!NOTE]
> T-SQL 串流只支援 Azure 串流分析所支援的語言介面區子集。 如需 Azure 串流分析查詢語言的詳細資訊，請參閱[串流分析查詢語言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)。

## <a name="limitations-and-restrictions"></a>限制事項

下列限制適用於 T-SQL 串流。 

- 任何時間都只能有一個作用中的串流作業。 必須先停止已經在執行的作業，然後才能啟動另一項作業。
- 每個串流作業都會在單一執行緒中執行。 如果串流作業包含多個查詢，則每個查詢會以序列順序進行評估。

## <a name="next-steps"></a>後續步驟

- [在 Azure SQL Edge (預覽) 中建立串流分析作業](create-stream-analytics-job.md)
- [在 Azure SQL Edge (預覽) 中檢視與串流作業相關聯的中繼資料](streaming-catalog-views.md)
- [建立外部串流](create-external-stream-transact-sql.md)